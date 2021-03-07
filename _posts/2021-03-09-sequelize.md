---
layout: post 
title: Sequelize(시퀄라이즈) 정리
---

## 데이터베이스 연결하기

~~~javascript
const { Sequelize } = require('sequelize');

// Option 1: Passing a connection URI
const sequelize = new Sequelize('sqlite::memory:') // Example for sqlite
const sequelize = new Sequelize('postgres://user:pass@example.com:5432/dbname') // Example for postgres

// Option 2: Passing parameters separately (sqlite)
const sequelize = new Sequelize({
  dialect: 'sqlite',
  storage: 'path/to/database.sqlite'
});

// Option 2: Passing parameters separately (other dialects)
const sequelize = new Sequelize('database', 'username', 'password', {
  host: 'localhost',
  dialect: /* one of 'mysql' | 'mariadb' | 'postgres' | 'mssql' */
});
~~~

공식 문서에서 두 가지 방식을 제안한다.

첫번째는 Sequelize 생성자에 string타입의 URI를 한번에 넘겨주는 것,

두번째는 데이터베이스명, 유저네임, 비밀번호를 콤마로 구분하여 따로 넘겨주는 것

데이터베이스와 관련된 정보들을 .env파일에 따로 저장할 경우 다음과 같이 코드를 작성할 수 있다.

~~~javascript
// import 생략
dotenv.config()

const {DATABASE, DB_USERNAME, DB_PASSWORD, DB_HOST} = process.env

const sequelize = new Sequelize(DATABASE, DB_USERNAME, DB_PASSWORD, {
  host: DB_PASSWORD
  dialect: 'mysql' 
})

const db = {}

db.sequelize = sequelize
~~~

데이터베이스 인스턴스를 생성을 완료했다.

## 데이터베이스 연결 확인하기

sequelize 인스턴스의 authenticate()메소드를 호출하여 데이터베이스가 연결되었는지

확인 가능하다.

프로미스를 반환하므로, then() 또는 async, await을 이용해야 한다.

시퀄라이즈의 메소드들은 프로미스를 반환하므로 async await을 사용할 수 있다.

~~~javascript
// import 생략
const dbConnection = async () => {
  try {
    await db.sequelize.authenticate();
    console.log("Connection has been established successfully.");
  } catch (error) {
    console.error("Unable to connect to the database:", error);
  }
}
~~~

## 모델 정의하기

공식 문서에서 모델을 정의하는 두 가지 방법을 제시한다.

하나는 define() 메소드를 이용한 것과 나머지는 class 문법으로, Model을 extends하는 방법이 있다.

### define()을 이용한 방법

~~~javascript
const { DataTypes } = require("sequelize");
const moment = require("moment");

module.exports = (sequelize) => {
  // 첫번째 매개변수에 모델명을 넣고, 두번째 매개변수에 스키마를 넣어준다.
  const Product = sequelize.define("Product", {
    id: {
      type: DataTypes.INTEGER,
      primaryKey: true,
      autoIncrement: true,
    },
    name: DataTypes.STRING,
    price: DataTypes.INTEGER,
    description: DataTypes.TEXT,
  });

  // 아래와 같이 인스턴스 메소드를 정의할 수 있다.
  Product.prototype.dateFormat = (date) =>
    moment(date).format("YYYY년 MM월 YY일");

  return Product;
};

~~~

## 모델 인스턴스 생성하기

인스턴스를 생성하고 이를 데이터베이스에 저장하는 과정이 필요하다.

생성은 build(),

~~~javascript
const jane = User.build({ name: "Jane" });
~~~

저장은 save()를 통해 이루어진다.

~~~javascript
await jane.save();
~~~

위의 두 가지 스텝을 create()로 간단하게 대체 가능하다.

~~~javascript
// 인스턴스 생성과 동시에 데이터베이스에 반영이 되었다.
const jane = await User.create({ name: "Jane" });
~~~

### 실제코드

~~~javascript
// POST /admin/products/write
// <form> 안에서 상품과 관련된 정보를 작성하여 POST
exports.post_products_write = async (req, res) => {
  try {
    await Product.create(req.body);
    res.redirect("/admin/products");
  } catch (error) {
    console.error(error);
  }
};
~~~

## 데이터베이스에서 데이터 읽기

### 데이터 전부 읽기

~~~javascript
  const products = await Product.findAll(); // 배열로 반환한다.
  // product인스턴스가 products 배열에 담기게 된다.
~~~

### 데이터 하나 읽기

findOne({where: {}})을 쓰거나 모델에 primary key를 설정한 경우,

findByPk()를 이용하여 데이터를 가져올 수 있다.

~~~javascript
// GET /admin/products/detail/:id
exports.get_product_detail = async (req, res) => {
  try {
    // 파라미터로 인스턴스의 id를 받는다.
    const { id } = req.params;
    // findOne() 또는 findyPk()를 이용하여 데이터를 가져올 수 있다.
    const product = await Product.findOne({
      where: { id },
    });
    res.render("admin/detail.html", { product });
  } catch (error) {
    console.log(error);
  }
};
~~~

## 데이터 수정하기

데이터베이스의 데이터를 수정해야하는 경우, 어떤 데이터를 수정할 것인지 명시해야 한다.

update() 메소드를 이용하여 수정을 한다.

두번째 파라미터에 {where: {}}를 명시하여 어떤 데이터를 수정할 것인지 알려줘야 한다.

~~~javascript
// POST /admin/products/edit/:id
exports.post_product_edit = async (req, res) => {
  const { id } = req.params;
  await Product.update(req.body, {
    where: { id },
  });
  res.redirect(`/admin/products/detail/${id}`);
};
~~~

## 데이터 삭제하기

어떤 데이터를 삭제할 것인지 알려줘야 한다.

~~~javascript
// GET /admin/products/delete/:id
exports.get_product_delete = async (req, res) => {
  const { id } = req.params;
  await Product.destroy({
    where: { id },
  });
  res.redirect("/admin/products");
};
~~~

## synchronization

sync()에는 세 가지가 있다.

sync(): 테이블이 없으면 생성하고 있으면 아무일도 일어나지 않는다.

sync(force: true): 테이블이 없으면 생성하고 있으면 드랍후 다시 생성한다.

sync(alter: true): 현재 데이터베이스를 체크하고 모델과 바뀐 점이 있으면 반영한다.
