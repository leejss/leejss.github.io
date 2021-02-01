---
layout: post 
title: Mongodb CRUD methods
categories: Post 
tags: 
- TIL
- Mongodb
---

## Mongodb data

### document

mongodb의 기본적인 데이터 단위는 document다. RBMS의 row라고 보면 된다.

{"key": value}의 형식을 가지고 있다. 자바스크립트 객체와 유사하다.

document의 key의 타입은 string이기 때문에 " "로 감싸줘야 한다.

`{"apple": "1"}`과 `{"apple": 1}`은 서로 다른 document이며(Type-sensitive)

`{"Apple": 1}`과 `{"apple": 1}`은 서로 다르다.(Case-sensitive)

### collection

document을 모은 것이 collection이다. RDBMS의 table이라고 보면 된다.

***

## Mongodb CRUD

몽고디비에서 사용하는 CRUD methods를 먼저 간략히 정리한다.

### Create

    - db.collection.insert()
    - db.collection.batchInsert()

### Read(query)

    - db.collection.find()

### Update

    - db.collection.update()

### Delete

    - db.collection.remove()
    - db.collection.drop()

***

## CREATE method

### db.collection.insert(document)

    db.fruit.insert({"apple": 2, "price": 4000})

insert의 매개변수로 document를 넘겨준다. mongodb의 collection은 dynamic schemas를 가지고 있기 때문에, 즉 key의 집합이 고정되어 있는 것이 아니기 때문에 document의 삽입이 자유롭다. nodejs 프로그래밍을 할 경우 mongoose를 통해 이를 보완할 수 있다.

### db.collection.batchInsert(array)

여러 개의 document를 삽입할 경우 batchInsert(배열)를 사용한다.

    db.fruit.batchInsert([{"banana":5}, {"mango":2}, {"orange":3}])

document의 배열을 매개변수로 넘겨주면 된다.

### _id

mongodb는 document를 삽입할 때 자동으로 `_id` key(or field)를 추가해서 삽입한다.

***

## Read method

### db.collection.find(query)

    db.fruit.find() // return all fruit's document
    db.fruit.find({"apple": 2}) // return {"apple": 2, "price": 4000}

db.collection.find()를 통해 document를 읽을 수 있다. find의 매개변수에 아무 것도 넘기지 않으면 collection의 모든 document를 리턴한다.

매개변수로는 query의 기준이 되는 query document를 받는다.

    db.collection.find({query1, query2, ..}) // query1 AND query2 AND ...

하나의 query document에 여러 개의 query를 받을 수 있는데, 이때 query는 AND로 묶여 검색을 수행한다.

    db.collection.find({}, {"key1": 1, "key2": 1}) 
    // return {_"id": ObjectId(""), "key1": value1, "key2": value2}

특정 키 값만 보고 싶은 경우 find()의 두번째 매개변수로 어떤 키를 보고 싶은지 정할 수 있다.
여기서 1은 true를 의미한다.

    db.collection.find({}, {"key3": 0})

특정 키를 제외한 document를 보고 싶은 경우 0을 부여하면 된다. **1은 포함, 0은 제외**

### Query conditional

복잡한 쿼리를 도와줄 operator들이 존재한다.

    "$lt": less than
    "$lte": less than or equal
    "$gt": greater than
    "$gte": greater than or equal
    "$ne": not equal

위 연산자를 이용하여 조건 검색을 수행할 수 있다.

    db.collection.find({"age": {"$gte": 18, "$lte": 30}})
    // "age"가 18보다 크거나 같으며 30보다 작거나 같은 document를 리턴한다.
    db.collection.find({"name" : {"$ne": "joe"})
    // name 값이 joe가 아닌 document를 리턴한다.

### OR query

    "$in": 주어진 여러 값 중 하나라도 일치하면 리턴한다.
    "$or": 주어진 조건 중 하나라도 일치하면 리턴한다. 

두 operator는 배열과 함께 쓰인다.

    db.collection.find({"ticket_no", {"$in": [4, 2, 7, 10, 43, 28]}})
    // ticket_no가 주어진 값 중 하나라도 일치하는 경우 그 document를 리턴
    db.collection.fin({"$or": [{"name": "joe"}, {"age": {"$gt": 19}}]})
    // name이 joe이거나, age가 19보다 큰 document를 리턴

***

## Update method

### db.collection.update()

    db.collection.update(query document, replacing document)

query document로 일치하는 document를 찾은 뒤 replacing document로 바꿔버린다. 주의해야할 것은 특정 필드만 바꾸는 것이 아니라 document자체를 바꿔버린다는 것이다.(document replacement)

### update modifiers

특정 필드만 바꾸고 싶은 경우 modifier를 사용해야 한다.(여기서 필드는 key를 말한다.)

    "$inc": 특정 필드의 값을 증가시킨다. (number type만 가능)
    "$set": 특정 필드의 값을 다른 값으로 바꾼다.
    "$unset": 특정 필드를 document에서 지운다.

    db.collection.update({"domain": "naver"},{"page_views": {"$inc": 1}})
    // domain이 naver인 document를 찾아서 그 document의 
    // page_views 필드의 값을 1 증가시킨다.
    
    db.collection.update({"name": "james"}, {"$set: {"married": 1}})
    // name이 james인 document를 찾아서 그 document의 married값을 1로 바꾼다.

    db.collection.update({"name": "joe"},{"$unset": {"book": 1}})
    // name이 joe인 document를 찾아서 그 document의 book 필드를 지운다.

### upsert

upsert는 update와 insert를 수행하는 것을 말한다.

update의 첫번째 매개변수인 query document가 일치하는 document를 찾지 못하면 query document와 동일한 document를 insert하고 update를 수행하는 update의 특수한 형태다.

update의 세번째 매개변수에 true를 주면 upsert를 수행할 수 있다.

    db.collection.update({}, {}, true)

save()도 upsert를 수행한다.

    db.collection.save()

***

## Delete method

### db.collection.remove()

    db.collection.remove()
    // query document를 주지 않으면 collection의 모든 document를 delete한다.

    db.collection.remove(query document)
    // query document와 일치하는 document를 삭제한다.

### db.collection.drop()

    db.collection.drop()
    // collection을 clear한다. find()보다 더 높은 성능을 보인다.
