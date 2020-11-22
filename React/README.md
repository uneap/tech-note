# React
## React왜 쓰는가?
### 재사용성이 좋다.
컴포넌트는 UI를 이루고 있는 아주 작은 요소들이다.
작은 컴포넌트는 재 사용성을 가지고 있다.
똑같은 코드를 반복적으로 입력할 필요가 없으므로 효율적이다.
컴포넌트는 재사용할 수 있을 정도로 필요에 의해서 쪼개는 것이 좋다.

### 성능이 좋다

#### 브라우저가 Dom을 이용하여 화면에 렌더링하는 과정
1. HTML 마크업을 처리하고 DOM 트리를 빌드한다.
2. CSS 마크업을 처리하고 CSSOM 트리를 빌드한다.
3. DOM 및 CSSOM 을 결합하여 렌더링 트리를 형성한다.
4. 렌더링 트리에서 레이아웃을 실행하여 각 노드의 기하학적 형태를 계산한다. 노드들은 스크린의 좌표가 주어지고, 정확히 어디에 나타나야 할 지 위치가 주어진다.
5. 개별 노드를 화면에 페인트한다.(or 래스터화)

#### 리액트는 Virtual DOM 방식을 사용하여 뷰를 업데이트할 때는 "재조정(Reconciliation) 과정"이라고 한다.
> Dom 이란?
> 웹문서를 객체로 표현하는 모델, DOM 트리에서 각 HTML 요소를 Node(노드)라고 한다.

## virtual Dom이란?
- 가상 DOM은 DOM의 상태를 메모리 위에 계속 올려두고 DOM에 변경이 있을 때만 해당 변경을 반영합니다.
- DOM 관리를 자동화하고 추상화하여 직접 할 필요가 없게 해준다. DOm관리가 어렵기 때문에, 개발자가 작업을 쉽게 하도록 도와준다.
- HTML tag마다 가질 수 있는 자식tag가 한정적이다. 그래서 react는 부모노드 타입이 다르면 자식 노드를 비교하지 않고 전면 교체한다.

- 노드에 key를 부여하면 일일이 탐색하지 않고도 접근할 수 있다.
- react 에서 key는 dom 변경을 인지하고 재조정(reconciliation)하는 비교대상에 사용됩니다.
- 따라서 key 값을 array의 index로 설정해둔다면 array에 내용이 삭제할 때마다 key 값이 달라져서 재조정 대상이 된다. 그리하여 성능이 느려질 수 있으므로 피해야한다.

1. 데이터를 업데이트하면 전체 UI를 Virtual DOM에 리렌더링한다.
2. 이전 Virtual DOM에 있던 내용과 현재 내용을 비교한다. (Diff 알고리즘)
3. 바뀐 부분만 실제 DOM에 적용한다.

## React life cycle은?
### 컴포넌트가 처음 실행되는 mount 때
#### constructor
- 컴포넌트가 처음 실행될 때 호출된다.
-  이 메소드에서만 state를 설정할 수 있다.
#### componentWillMount
- React element를 실제 dom node에 추가하기 직전에 호출된다.
- 이곳에서 state를 변경하더라도 render가 호출되지 않는다.
#### render
- 컴포넌트 렌더링을 담당한다.

#### componentDidMount
- component를 dom node에 추가했으므로 Dom에 접근할 수 있다.
- setTimeout같은 작업을 수행한다.
### state가 변경됐을 때
componentWillReceiveProps -> getDerivedStateFrom
#### componentWillReceiveProps
- 마운트시에는 호출되지 않는다.
- 업데이트 되기 전에 업데이트가 발생했음을 감지 후 호출된다.
- 내부에서 setState를 해도 추가적인 렌더링이 발생하지 않는다.
#### getDerivedStateFromProps
- 최초 마운트와 갱신시에 render 호출 직전에 호출된다.
- state를 갱신하는 객체를 반환할 수 있고, state를 갱신하지 않고자 할 땐 null 값을 반환한다.
#### ShouldComponentDidUpdate
- componentWillUpdate가 호출될지 말지를 결정한다.
- 최적화 작업을 수행한다.
    - 예를 들면 props만 변경되었을 경우도 있을 수 있으므로, 변경 state가 현재 state랑 다른지를 확인할 수 있다.
 (componentWillUpdate) -> getSnapshotBeforeUpdate
#### componentWillUpdate
- state를 변경할경우 shouldComponentDidUpdate를 호출하여 무한루프가 돈다.
#### getSnapshotBeforeUpdate
- Dom 노드에 react element의 변화가 추가된 직후에 호출된다.
- 이 곳에서는 이전 props와 이전 state를 볼 수 있고 이곳에서 리턴하는 값은 componentDidUpdate의 3번째 파라미터이다.
#### render
- 가상 dom 반영
- diff 뜬다
- diff를 물리 dom에 반영
#### componentDidUpdate
- 컴포넌트 업데이트 직후 호출되는 값이다. 이전 props와 이전 state를 접근할 수 있다.
### props가 변경됐을 때
#### ShouldComponentDidUpdate
#### (ComponentWillUpdate)
#### render
- 가상 dom 반영
- diff 뜬다
- diff를 물리 dom에 반영
#### componentDidUpdate

### 컴포넌트가 제거될 때 Unmount

#### componentWillMount
- 연결했던 이벤트 리스너를 제거한다.

### 컴포넌트에 에러 발생했을 때
#### componentDidCatch
- 자신의 component가 아닌 자식 component의 에러가 발생했을 때 호출된다.

## hook이란?
- hook을 이용하여 class 없이 state를 사용할 수 있으며,
- 함수 컴포넌트에서 react state와 생명주기 기능을 연동할 수 있게 해주는 함수이다.

### hook이 생겨나게 된 이유?
- 각 생명주기를 관련있는 로직끼리 묶어 컴포넌트 재사용성을 높이고자 했다.

### useState?
- 인자로 초기값을 받고 현재 상태와 현재 상태를 업데이트 할 수 있는 함수를 반환해준다.

### useEffect?
- class 컴포넌트의 componentDidMount와 ComponentDidUpdate, ComponentWillUnmount가 합쳐진 것과 동일하다.

### hook 규칙
- 최상위에서만 hook을 호출해야한다.
- 오직 react 함수 내에서 hook을 호출해야한다.

## React Context란?
**장점**
- context api를 사용하지 않는다면 최상위 컴포넌트에서 자식 컴포넌트까지 props를 전달해야한다. 그러나 context를 사용하게 되면 provider를 이용해 context 변경사항을 자손들에게 알릴 수 있다.
- useContext를 사용하면 consumer wrapper를 굳이 쓰지 않고도 값에 접근할 수 있어 편리하다.

## redux 썼던 이유?
메인 페이지의 데이터를 캐싱하여 디테일 페이지로 전달하기 위해 사용했다.
물론 다시 새로고침을 하는 순간 데이터는 날라가고 api 호출을 필요로 하겠지만 새로고침하지 않는 경우에는 이것이 좀 더 효율적이라고 판단했다.
redux 구조는 아이유 책에서 보자.
## reflow란?
하나의 노드 추가나 삭제를 하여 웹 페이지 전체의 레이아웃에 영향을 받는 경우, 웹페이지의 일부 또는 전체가 다시 렌더링 될 수 있다. 이런 경우를 Reflow라고 한다.

## babel이란?
babel은 브라우저가 이해할 수 있는 문법으로 변환해준다. ES6,7 등 최신 문법을 사용해서 코딩을 하면 babel을 통해 이해할 수 있도록 해준다.
## webpack이란?
의존성 있는 파일들끼리 묶어주는 모듈 번들러이다. 컴파일 속도를 높여 네트워크 속도를 높이는 효과를 갖고 있다. 웹팩의 로더 덕분에 모든 파일을 모듈로 볼 수 있다.

## webpack loader란?
타입스크립트 같은 다른 언어를 자바스크립트 문법으로 변환해 준다. 그리고 이미지를 data URL 형식의 문자열로 반환한다. 또한 css 파일을 자바스크립트에서 직접 로딩할 수 있도록 해준다.

## webpack plugin이란?
묶어진 자바스크립트를 난독화 한다. 또한 특정 텍스트를 추출하는 용도로 사용한다.

