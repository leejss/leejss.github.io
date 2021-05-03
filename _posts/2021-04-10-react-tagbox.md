---
layout: post
title: React - useState를 이용해서 Tagbox 만들어보기
date: 2021-04-10 23:18 +0900
tags: React
category: React
---

## 생각하기

1. input과 submit button으로 이루어진 form이 있다.
2. input에 태그를 입력하고 enter를 누르거나 button을 클릭하면 태그가 추가되어 렌더링한다.
3. 입력한 태그를 받을 state 가 필요하다.
4. 태그 리스트 state가 필요하다.
5. 태그를 추가하는 로직이 필요하다.
6. 태그를 삭제하는 로직이 필요하다.

## 구현하기

### 컴포넌트 구성

1. 우선, 만들고자 하는 것이 간단한 컴포넌트이기 때문에 하나의 파일에 관련 코드를 모두 작성하는 것으로 한다.
2. state를 정의하고 추가, 삭제 로직과 form태그가 있는 TagContainer 컴포넌트를 만든다.
3. 태그 리스트를 받아 렌더링을 하는 TagList 컴포넌트를 만든다.
4. 각각의 tag를 렌더링할 TagItem 컴포넌트를 만든다.

```jsx
const TagItem = ({tag}) => <div>{tag}</div>

const TagList = ({tags}) => <div>
    {
        tags.map(tag => <TagItem key={tag} tag={tag} />)
    }
    </div>


const TagContainer = () => {
    // state 정의
    // 함수 정의

    return (<div>
        <form>
            <input placeholder='태그를 추가하세요.' />
            <button type="submit">태그추가</button>
        </form>
        <TagList tags={/*아직 정의하지 않은 태그 리스트*/} />
    </div>)
```

### state와 함수 구성

1. useState를 이용하여 state를 구성한다.
2. input text와 taglist를 정의해야 한다.
3. taglist에 input text를 추가하는 함수를 작성한다.
4. tag를 클릭하면 해당 태그를 없애는 함수를 작성한다.
5. 작성한 state와 함수를 알맞은 컴포넌트에 전달한다.

```jsx

const TagContainer = () => {
    // -- state 정의
    const [inputText, setInputText] = useState('')
    const [tagList, setTagList] = useState([])

    // -- 함수 정의
    // 태그를 추가하는 함수
    const insertTag = (tag) => {
        if (!tag) return
        if (tagList.include(tag)) return
        setTagList([...tagList, tag])
    }
    // inputText를 받는 함수
    const onChange = (e) => {
        setInputText(e.target.value)
    }
    // tag를 지우는 함수
    const onRemove = tag => {
        setTagList(tagList.filter(t => t !== tag))
    }
    // submit시, inputText를 tagList에 추가하는 함수
    const onSubmit = (e) => {
        e.preventDefault()
        insertTag(inputText)
        setInputText('')
    }

    return (/**/)
}

```

```jsx

const TagContainer = () => {
    /* state와 함수 */

    return (
        <form onSubmit={onSubmit}>
            <input placeholder='태그를 추가하세요.' value={inputText} onChange={onChange} />
            <button type="submit">태그추가</button>
        </form>
        <TagList tags={tagList} onRemove={onRemove} />        
    )

}

```

```jsx
const TagList = ({tags, onRemove}) => (
    <div>
    {
        tags.map(tag => <TagItem key={tag} tag={tag} onRemove={onRemove} />)
    }
    </div>
)
```

```jsx
const TagItem = ({tag, onRemove}) => (
    <div onClick={() => onRemove(tag)}>
        {tag}
    </div>
)
```

