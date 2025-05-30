## [할 일 관리] 앱 만들기

---

## UI 구현하기

UI 구현을 흔히 ‘퍼블리싱’ 또는 ‘UI 개발’이라고 한다.

## 기능 구현 준비하기

컴포넌트별로 어떤 기능을 구현해야 하는지 정리

- App : 할 일 데이터 관리하기
- Header : 오늘의 날짜 표시
- ToodEditor : 새로운 할 일 아이템 생성
- TodoList : 검색에 따라 필터링된 할 일 아이템 렌더링
- TodoItem : 할 일 아이템의 수정 및 삭제

데이터를 다루는 4개의 기능, 즉 추가(Create), 조회(Read), 수정(Update), 삭제(Delete) 기능을 앞 글자만 따서 CRUD라고 한다. CRUD는 데이터 처리의 기본 기능으로, 웹 서비스라면 기본적으로 갖추고 있어야 한다.

- Create : 할 일 아이템 생성
- Read : 할 일 아이템 렌더링
- Update : 할 일 아이템 수정
- Delete : 할 일 아이템 삭제

**데이터 모델링하기**

자바스크립트에서는 보통 현실의 사물이나 개념을 표현할 때 객체를 사용한다. 현실의 사물은 일반적으로 여러 속성을 동시에 가지고 있기 때문이다. 이렇게 현실의 사물이나 개념을 프로그래밍 언어의 객체와 같은 자료구조로 표현하는 행위를 ‘데이터 모델링’이라 한다.

데이터를 모델링하는 이유는 데이터를 어떻게 관리할지 생각하기 위함이다. 모델링 과정이 잘못되면 작업 과정에서 큰 문제가 생길 수 있다.

**목 데이터 설정하기**

목(Mock) 데이터란 모조품 데이터라는 뜻이다. 기능을 완벽히 구현하지 않은 상태에서 테스트를 목적으로사용하는 데이터다. 임시 데이터라 표현하기도 한다.

기능을 아직 개발하지 않아 데이터가 없는 상황일 때 목 데이터를 사용한다. 임시 데이터 역할을 하는 목 데이터가 있으면, 데이터 관리 기능 개발이 한결 수월해진다.

## Create: 할 일 추가하기

**아이템 추가 함수 만들기**

TodoEditor 컴포넌트에서 추가 버튼을 클릭하면 App에 사용자가 입력한 할 일 데이터를 전달하고 추가 이벤트가 발생했음을 알려야 한다.

```jsx
const mockTodo = [
  {
    id: 0,
    isDone: false,
    content: "Learn React",
    createdDate: new Date().getTime(),
  },
  {
    id: 1,
    isDone: false,
    content: "Learn React Router",
    createdDate: new Date().getTime(),
  },
  {
    id: 2,
    isDone: false,
    content: "Learn React Query",
    createdDate: new Date().getTime(),
  },
];

function App() {
  const idRef = useRef(3);
  const [todo, setTodo] = useState(mockTodo);

  const onCreate = (content) => {
    const newItem = {
      id: idRef.current,
      content,
      isDone: false,
      createdDate: new Date().getTime(),
    };
    setTodo([newItem, ...todo]);
    idRef.current += 1;
  };
  return (
    <div className="App">
      <Header />
      <TodoEditor onCreate={onCreate} />
      <TodoList />
    </div>
  );
}
```

**아이템 추가 함수 호출하기**

사용자가 할 일 입력 폼에서 아이템을 입력하고 추가 버튼을 클릭한다. 그러면 TodoEditor 컴포넌트는 새 할 일을 생성하기 위해 App에서 Props로 받은 함수 onCraete를 호출하고 현재 사용자가 작성한 할 일을 인수로 전달한다.

```
export default function TodoEditor({ onCreate }) {
  const [content, setContent] = useState("");

  const onChangeContent = (e) => {
    setContent(e.target.value);
  };

  const onSubmit = () => {
    onCreate(content);
  };
  return (
    <div className="TodoEditor">
      <h4>새로운 Todo 작성하기 ✏️</h4>
      <div className="editor_wrapper">
        <input
          value={content}
          onChange={onChangeContent}
          placeholder="새로운 Todo..."
        />
        <button onClick={onSubmit}>추가</button>
      </div>
    </div>
  );
}
```

**Create 완성도 높이기**

빈 입력 방지하기. 아무것도 입력하지 않은 상태로 아이템을 추가하는 것을 방지하기 위한 방법은 여럿 있다. 웹 서비스들이 일반적으로 채택하고 있는 빈 입력란에 포커스를 주는 기능을 구현하자.

특정 페이지 요소에 포커스를 주는 방법은 할 일 입력 폼을 관리할 Ref 객체를 하나 만들고, 함수 onSubmit 에서 content 값이 비어 있으면 입력 폼에 포커스를 구현하는 방식이다.

```jsx
export default function TodoEditor({ onCreate }) {
  const [content, setContent] = useState("");
  const inputRef = useRef();

  const onChangeContent = (e) => {
    setContent(e.target.value);
  };

  const onSubmit = () => {
    if (!content) {
      inputRef.current.focus();
      return;
    }
    onCreate(content);
  };
  return (
    <div className="TodoEditor">
      <h4>새로운 Todo 작성하기 ✏️</h4>
      <div className="editor_wrapper">
        <input
          ref={inputRef}
          value={content}
          onChange={onChangeContent}
          placeholder="새로운 Todo..."
        />
        <button onClick={onSubmit}>추가</button>
      </div>
    </div>
  );
}
```

**아이템 추가 후 입력 폼 초기화하기**

프로그램의 완성도를 높이기 위한 두 번째 조치로 아이템을 추가하면 자동으로 할 일 입력 폼을 초기화하는 기능을 구현. 입력 폼을 초기화하는 기능이 없다면 의도치 않게 추가 버튼을 클릭해 중복 아이템을 생성할 수 있다.

```jsx
const onSubmit = () => {
  if (!content) {
    inputRef.current.focus();
    return;
  }
  onCreate(content);
  setContent("");
};
```

**Enter 키를 눌러 아이템 추가하기**

키보드의 Enter 키를 누르면, 추가 버튼을 클릭한 것과 동일한 동작을 수행하는 키 입력 이벤트 만들기.

```jsx
const onKeyDown = (e) => {
  if (e.keyCode === 13) {
    onSubmit();
  }
};
return (
  <div className="TodoEditor">
    <h4>새로운 Todo 작성하기 ✏️</h4>
    <div className="editor_wrapper">
      <input
        ref={inputRef}
        value={content}
        onChange={onChangeContent}
        onKeyDown={onKeyDown}
        placeholder="새로운 Todo..."
      />
      <button onClick={onSubmit}>추가</button>
    </div>
  </div>
);
```

## Read: 할 일 리스트 렌더링하기

**배열을 리스트로 렌더링하기**

리액트에서 배열 데이터를 렌더링할 때는 배열 메서드 map을 주로 이용한다. map을 이용하면 HTML 또는 컴포넌트를 순회하면서 매 요소를 반복하여 렌더링한다.

```jsx
<div className="TodoList">
  <h4>Todo List 🌷</h4>
  <input className="searchbar" placeholder="검색어를 입력하세요" />
  <div className="list_wrapper">
    {todo.map((it) => (
      <TodoItem {...it} />
    ))}
  </div>
</div>
```

**Key 설정하기**

Key는 리스트에서 각각의 컴포넌트를 구분하기 위해 사용하는 값이다. 리액트 리스트에서 특정 컴포넌트를 수정, 추가, 삭제하는 경우, 이 key로 어떤 컴포넌트를 업데이트할지 결정한다. 따라서 리트스의 각 컴포넌트 key로 구분하지 않으면 생성, 수정, 삭제와 같은 연산을 수행할 수 없거나 비효율적으로 탐색하게 된다.

**검색어에 따라 필터링하기**

검색어를 입력하면, 해당 문자열을 포함하는 할일 아이템만 필터링해 보여주는 기능

```jsx
import TodoItem from "./TodoItem";
import "./TodoList.css";
import { useState } from "react";

export default function TodoList({ todo }) {
  const [search, setSearch] = useState("");
  const onChangeSearch = (e) => {
    setSearch(e.target.value);
  };

  const getSearchResult = () => {
    return search === ""
      ? todo
      : todo.filter((it) => it.content.includes(search));
  };
  return (
    <div className="TodoList">
      <h4>Todo List 🌷</h4>
      <input
        className="searchbar"
        placeholder="검색어를 입력하세요"
        value={search}
        onChange={onChangeSearch}
      />
      <div className="list_wrapper">
        {getSearchResult().map((it) => (
          <TodoItem key={it.id} {...it} />
        ))}
      </div>
    </div>
  );
}
```

## Update: 할 일 수정하기

**아이템 수정 함수 만들기**

```jsx
const onUpdate = (targetId) => {
  setTodo(
    todo.map((it) => (it.id === targetId ? { ...it, isDone: !it.isDone } : it))
  );
};
```

**TodoItem 컴포넌트에서 아이템 수정 함수 호출하기**

```jsx
import React from "react";
import "./TodoItem.css";

export default function TodoItem({
  id,
  content,
  isDone,
  createdDate,
  onUpdate,
}) {
  const onChangeCheckbox = () => {
    onUpdate(id);
  };

  return (
    <div className="TodoItem">
      <div className="checkbox_col">
        <input type="checkbox" checked={isDone} onChange={onChangeCheckbox} />
      </div>
      <div className="title_col">{content}</div>
      <div className="date_col">
        {new Date(createdDate).toLocaleDateString()}
      </div>
      <div className="btn_col">
        <button>삭제</button>
      </div>
    </div>
  );
}
```

## Delete: 할 일 삭제하기

**아이템 삭제 함수 만들기**

```jsx
const onDelete = (targetId) => {
  setTodo(todo.filter((it) => it.id !== targetId));
};
```
