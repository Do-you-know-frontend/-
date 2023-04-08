# 라이프사이클 메서드

모든 리액트 컴포넌트는 라이프사이클(생명주기)이 존재. 

컴포넌트의 수명은 페이지가 렌더링 되기 전인 준비과정에서 시작하여 페이지가 사라질 때 끝난다.

리액트 프로젝트 도중 컴포넌트를 처음 렌더링할 때, 컴포넌트를 업데이트하기 전후로 어떤 작업을처리하거나 불필요한 업데이트를 방지할 때 라이프사이클 메서드를 사용한다.

참고) 라이프사이클 메서드는 클래스형 컴포넌트에서만 사용할 수 있다(함수형 컴포넌트 X)

    대신 Hooks 기능을 사용하여 비슷한 작업 처리 가능.

라이프사이클 메서드의 종류는 총 아홉 가지.

- **will** 접두사가 붙은 메서드는 어떤 작업을 작동하기 전에 실행되는 메서드입니다.
- **Did** 접두사가 붙은 메서드는 어떤 작업을 작동한 후에 실행되는 메서드입니다.

이 메서드들은 우리가 컴포넌트 클래스에서 덮어 써 선언함으로써 사용할 수 있습니다. 

라이프사이클은 총 세 가지, 즉 **마운트, 업데이트, 언마운트** 카테고리로 나눕니다.

![https://github.com/junh0328/TIL/raw/master/React/images/lifeCycle.png](https://github.com/junh0328/TIL/raw/master/React/images/lifeCycle.png)

## 1. 마운트

: DOM이 생성되고 웹 브라우저상에 나타나는 것을 마운트라고 한다.

  다음 네 가지 순서로 실행된다.

- **constructor**
    
    : 컴포넌트 생성자 메서드. 컴포넌트가 만들어지면 가장 먼저 실행.
    
      메서드를 바인딩하거나 state를 초기화하는 작업이 없다면, 필요없다.
    
      보통 두 가지 목적을 위해 사용한다.
    
    > 1. `this.state`에 객체를 할당하여 [지역 state](https://ko.reactjs.org/docs/state-and-lifecycle.html)를 초기화
    > 
    > 
    > 2. 인스턴스에 [이벤트 처리](https://ko.reactjs.org/docs/handling-events.html) 메서드를 바인딩
    > 
    
      
    
    ```jsx
      constructor(props) {
        //super(props)를 호출해야한다.
        //그렇지 않으면 this.props가 생성자 내에서 정의되지 않아 버그로 이어질 수 있다.
        super(props);
        this.state = { counter: 0 }; //setState()를 호출하면 절대 안됨!
    	  this.handleClick = this.handleClick.bind(this);
      }
    ```
    
- **getDerivedStateFromProps**
    
    : prop로 받아온 것을 state에 넣어주고 싶을 때
    
    ```jsx
    static getDerivedStateFromProps(nextProps, prevState) {
        console.log("getDerivedStateFromProps");
        if (nextProps.color !== prevState.color) {
          return { color: nextProps.color };
        }
        return null;
      }
    ```
    
    다른 생명주기 메서드와는 달리 앞에 `static`을 필요로 하고, 이 안에서는 `this`  조회 못함.
    
    여기서 특정 객체를 반환하게 되면 해당 객체 안에 있는 내용들이 컴포넌트의 `state` 로 설정이 되고, `null` 을 반환하게 되면 아무 일도 발생하지 않는다.
    컴포넌트가 처음 렌더링 되기 전에도 호출 되고, 그 이후 리렌더링 되기 전에도 매번 실행됨.
    
- **render**
    
    : 컴포넌트를 렌더링하는 메서드
    
- **componentDidMount**
    
    : 컴포넌트가 웹 브라우저상에 나타난 후(렌더링을 마치고) 호출하는 메서드.
    
    1. 여기선 주로 D3, masonry 처럼 DOM 을 사용해야하는 외부 라이브러리 연동을 하거나, 해당 컴포넌트에서 필요로하는 데이터를 요청하기 위해 axios, fetch 등을 통하여 ajax 요청을 하거나, DOM 의 속성을 읽거나 직접 변경하는 작업을 진행.
    2. 데이터 구독하기 좋은 위치. 데이터를 구독했다면 `componentWillUnmount()`에서 구독 해제 작업을 반드시 수행해야한다.
    3. 이때 즉시 setState()를 호출하는 경우에 추가적인 렌더링이 발생하지만, 브라우저가 화면을 갱신하기 전에 이루어진다. 즉, 사용자는 마지막 한 번의 render() 결과만을 볼 수 있다. 하지만 성능 문제로 이어지기 쉽기 때문에 주의가 필요하고, 대신 constructor() 메서드에서 초기 state를 할당 할 수 있다. 

## 2. 업데이트

**컴포넌트는 다음과 같은 총 네 가지 경우에 업데이트합니다.**

> - Props가 바뀔 때
- state가 바뀔 때
- 부모 컴포넌트가 리렌더링될 때
- this.forceUpdate로 강제로 렌더링을 트리거할 때
> 

다음 5가지 순서로 실행

- **getDerivedStateFromProps**
    
    : props변화에 따라 state값에도 변화를 주고 싶을 때 사용.
    
      마운트 과정에서도 호출되며, 업데이트가 시작되기 전에도 호출
    
- **shouldComponentUpdate**
    
    : 컴포넌트가 리렌더링 할지 말지를 결정하는 메서드
    
      true 반환 시 render호출, false 반환 시 여기서 작업 취소.
    
    ```jsx
    shouldComponentUpdate(nextProps, nextState) {
        console.log("shouldComponentUpdate", nextProps, nextState);
        // 숫자의 마지막 자리가 4면 리렌더링하지 않습니다
        return nextState.number % 10 !== 4;
      }
    ```
    
- **render**
    
    : 컴포넌트를 리렌더링함.
    
- **getSnapshotBeforeUpdate**
    
    : 컴포넌트 변화를 DOM에 반영하기 직적에 호출하는 메서드
    
    컴포넌트에 변화가 일어나기 직전의 DOM 상태를 가져와서 특정 값을 반환하면 그 다음 발생하게 되는 `componentDidUpdate`함수에서 받아와서 사용을 할 수 있다.
    
    ```jsx
    getSnapshotBeforeUpdate(prevProps, prevState) {
        console.log("getSnapshotBeforeUpdate");
        if (prevProps.color !== this.props.color) {
          return this.myRef.style.color;
        }
        return null;
      }
    ```
    

 **웹 브라우저 상 실제 DOM변화**

- **componentDidUpdate**
    
     : 컴포넌트의 업데이트(리렌더링) 작업이 끝난 후 호출하는 메서드
    
    1. 컴포넌트 갱신시 DOM조작할 때
    2. 이전과 현재 props비교해서 네트워크 요청을 할 때
    
    ```jsx
    componentDidUpdate(prevProps) {
      // 전형적인 사용 사례 (props 비교를 잊지 마세요)
      if (this.props.userID !== prevProps.userID) {
        this.fetchData(this.props.userID);
      }
    }
    ```
    
    1. 3번째 파라미터로 `getSnapshotBeforeUpdate`에서 반환한 값 조회 가능(없으면 undefined)
    
    ```jsx
    componentDidUpdate(prevProps, prevState, snapshot) {
        console.log("componentDidUpdate", prevProps, prevState);
        if (snapshot) {
          console.log("업데이트 되기 직전 색상: ", snapshot);
        }
      }
    ```
    

이 메서드는 사용되는 일이 별로 없지만, 함수형 컴포넌트 + Hooks를 사용할 때 이를 대체할 수 있는 기능이 아직 없다. DOM에 변화 반영 전, DOM의 속성을 확인하고 싶을 때 이 생명주기를 사용하면 된다.

- 활용 예시
    
    [getSnapshotBeforeUpdate 예제 - CodeSandbox](https://codesandbox.io/s/getsnapshotbeforeupdate-yeje-vpmle?fontsize=14)
    
    ```jsx
    getSnapshotBeforeUpdate(prevProps, prevState) {
        // DOM 업데이트가 일어나기 직전의 시점입니다.
        // 새 데이터가 상단에 추가되어도 스크롤바를 유지해보겠습니다.
        // scrollHeight 는 전 후를 비교해서 스크롤 위치를 설정하기 위함이고,
        // scrollTop 은, 이 기능이 크롬에 이미 구현이 되어있는데,
        // 이미 구현이 되어있다면 처리하지 않도록 하기 위함입니다.
        if (prevState.array !== this.state.array) {
          const { scrollTop, scrollHeight } = this.list;
    
       // 여기서 반환 하는 값은 componentDidMount 에서 snapshot 값으로 받아올 수 있습니다.
          return {
            scrollTop,
            scrollHeight
          };
        }
      }
      // 세로로 스크롤되는 픽셀 수를 가져오거나 설정
      // 요소 콘텐츠의 높이를 측정한 것
    
      componentDidUpdate(prevProps, prevState, snapshot) {
        if (snapshot) {
          const { scrollTop } = this.list;
    			// 기능이 이미 구현되어있다면 처리하지 않습니다.
          if (scrollTop !== snapshot.scrollTop) return;
    			
          const diff = this.list.scrollHeight - snapshot.scrollHeight;
          this.list.scrollTop += diff;
        }
      }
    ```
    
    Chrome 브라우저에서는 브라우저 자체적으로 이미 구현되어있는 기능중에 하나인데, 새로운 내용이 추가되었을 때 사용자의 스크롤 위치를 유지시키는 기능입니다. Safari 브라우저를 포함한 일부 브라우저는 이 기능이 구현되어있지 않아서 다음과 같이 작동하게 됩니다.
    
    ![https://i.imgur.com/1POUOrQ.gif](https://i.imgur.com/1POUOrQ.gif)
    
    새로운 항목이 위에서 추가가 되면 사용자가 보는 구간이 한칸씩 계속 밀리게 되지요? `getSnapshotBeforeUpdate` 를 활용하면 다음과 같이 유지를 할 수 있습니다.
    
    ![https://i.imgur.com/gS01iZ9.gif](https://i.imgur.com/gS01iZ9.gif)
    

## 3. 언마운트

: 마운트의 반대 과정, 즉 컴포넌트를 DOM에서 제거하는 것.

- componentWillUnmount
    
    : 컴포넌트가 웹 브라우저사에서 사라지기 전에 호출하는 메서드
    
      타이머 제거, 네트워크 요청취소, 구독해제 등 정리작업 수행.
    
      ※ 다시 렌더링 되지 않으므로 setState()호출하면 안됨.
    

![https://github.com/junh0328/TIL/raw/master/React/images/lifeCycleFlow.png](https://github.com/junh0328/TIL/raw/master/React/images/lifeCycleFlow.png)

# class컴포넌트 vs 함수형 컴포넌트

함수형 컴포넌트는 React Hook 이라고도 하며 **React 16.8**버전 이후부터 React 요소로 추가됨

기존의 클래스형 컴포넌트는 계속 지원될 예정이지만, 매뉴얼에서는 새로 작성하는 컴포넌트의 경우 함수형 컴포넌트와 Hooks를 사용할 것을 권장한다.

| 함수형 컴포넌트 | 클래스형 컴포넌트 |
| --- | --- |
| 함수형 컴포넌트는 props를 인수로 받아들이고 React 요소(JSX)를 반환하는 일반 JavaScript 순수 함수입니다. | 클래스 컴포넌트는 React에서 확장해야 합니다. 컴포넌트와 React 요소를 반환하는 렌더링 함수를 만듭니다. |
| 함수형 컴포넌트 기능적 구성 요소에서 사용되는 렌더링 방법이 없습니다. | JSX(HTML과 문법적으로 유사함)를 반환하는 render() 메서드가 있어야 합니다. |
| 위에서 아래로 실행되며 함수가 반환되면 언마운트된다. | 클래스 구성 요소가 인스턴스화되고 다양한 수명 주기 메서드가 활성 상태로 유지되며 클래스 구성 요소의 단계에 따라 실행 및 호출됩니다. |
| Stateless components | Stateful components |
| React 수명 주기 메서드(예: componentDidMount)는 사용할 수 없다 | React 수명 주기 메서드는 클래스 구성 요소(예: componentDidMount) 내에서 사용할 수 있습니다. |
| 훅을 사용하여 상태 저장을 할 수 있습니다.
예: const [name,SetName]= React.useState(' ') | 훅을 구현하려면 클래스 구성 요소 내부에 다른 구문이 필요합니다.
예: constructor(props) {
    super(props);
    this.state = { counter: 0 };
  } |
| 생성자는 사용되지 않습니다. | 생성자는 상태를 저장해야 하므로 사용됩니다. |

![[출처] https://wavez.github.io/react-hooks-lifecycle/](https://blog.kakaocdn.net/dn/bF6rTe/btrEvNPPvFs/kfuXlK3dGF4bJUpKXQcjH1/img.png)

[출처] https://wavez.github.io/react-hooks-lifecycle/

## 1. 마운트

| 분류 | 클래스형 컴포넌트 | 함수형 컴포넌트 |  |
| --- | --- | --- | --- |
| Mounting | constructor() | 함수형 컴포넌트 내부 | 컴포넌트가 호출이 되었을 때 가장 먼저 호출이 되는 것은 컴포넌트 내부 |
| Mounting | render() | return() | 미리 구현한 HTML를 화면상에 보여주는 메서드 |
| Mounting | ComponenDidMount() | useEffect() | 렌더링 이후에 실행이 되는 메서드 |
| Updating | componentDidUpdate() | useEffect() |  |
| UnMounting | componentWillUnmount() | useEffect() |  |

### useEffect **( function, deps? );**

- 마운트 될 때만 실행하고 싶을 때
    
    두 번째 파라미터로 [](빈배열) 넣어주기
    
- 특정 값이 없데이트될 때만 실행하고 싶을 때
    
    두 번째 파라미터로 검사하고싶은 값 넣어주기
    
- 뒷정리하기
    
    컴포넌트가 언마운트되기 전이나 업데이트되기 직전에 어떠한 작업을 수행하고 싶다면 useEffect에서 뒷정리(cleanup)함수 반환하기
    
    ```jsx
    useEffect(() => {
      const subscription = props.source.subscribe();
      **return () => {
        // Clean up the subscription
        subscription.unsubscribe();
      };**
    });
    ```
    

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/7d6f77de-f61a-410e-b2f4-edcb2490ea99/Untitled.png)

```jsx
useEffect(()=>{
		console.log('effect')
		console.log(name)
		return () => {
				console.log('cleanup')
				console.log(name);
		};
}, [name]);
```

렌더링될 때마다 뒷정리함수가 나타난다.

뒷정리함수가 호출될 때는 업데이트되기 직전의 값을 보여준다. 

오직 언마운트될 때만 뒷정리함수를 호출하고싶다면 두 번째 파라미터에 []

에러바운더리

[React.Component – React](https://ko.reactjs.org/docs/react-component.html)

[25. LifeCycle Method · GitBook](https://react.vlpt.us/basic/25-lifecycle.html)

리액트를 다루는 기술
