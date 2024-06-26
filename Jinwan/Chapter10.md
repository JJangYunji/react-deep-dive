## 리액트 17과 18의 변경사항 살펴보기

## 리액트 17

### 점진적 업데이트

리액트 17은 점진적 업데이트를 지원하기 위한 변경이 주요 내용이었다.

리액트를 내부에서 2개의 버전을 사용할 수 있도록하는 트리키한 방법을 사용하게할 수 있음

### 이벤트 핸들러 부착 방식 변경

리액트는 이밴트 타입당 하나의 핸들러를 root에서 관리하게해 위임하는 방식으로 사용.

- 캡처 → 타겟 → 버블링의 단계로 진행
- 하위요소에 모두 이벤트를 부착하고 관리하기 보단 공통의 부모에서 처리하는 것이 효율적
  - 작업의 반복, 추가 수정, 등이 있다.

리액트는 17버전에서 **이러한 위임을 document → 리액트 최상단 트리(root element)로 변경**

- JQuery, js 코드와의 혼재를 막기위함
- 점진적 업그래이드 지원

아래의 구조에서 `react-root-16-8` 에서 e.stopPropagation()을 실행해도 이미 document에 이벤트가 등록되어 있어 `react-root-16-14` 의 같은 타입의 이벤트의 전파를 막을 수 없엇다.

```jsx
// 이전 16에서
<div id="react-root-16-14">
	<div id="react-root-16-8" />
<div/>
```

이를 리액트의 트리에서 관리하게되면 각 루트의 최상단에서 실행됨으로 막을 수 있음.
그래서 각 루트는 격리되어 이벤트의 전파를 막을 수 있도록할 수 있음.

### import React from ‘react’ 필요 없음

바벨 트랜스레이트 시점에서 `react/jsx-runtime`을 import해서 해결함.

> ‘react-codemod’ 를 사용해서 레거시 플잭이있다면 다날려보는것도 좋을듯
> `Removing Unused React Imports` 를 수행한다고함

### evnet pooling 제거

`SyntheicEvnet`를 두고 합성 이벤트를 보다 효율적으로 사용하기 위해서 사용되는 패턴이었음.

하지만 `SyntheicEvnet`는 수행이후 재사용하는 사이 null변경되 params로 받은 envet에 접근할때 특정 에러 발생함. 그래서 비동기시 null 참조하는 경우가 생겨 사용 불편해고, 이를 위해 추가 메모리할당을해야함으로 삭제됨.

### useEffect 클린업 비동기 실행

16까지는 동기로 실행됨.

17에서는 커밋단계가 완료될 때까지 지연된 다음 실행됨.

(커밋 이후는 변경사항이 dom에 모두 반영된 이후를 의미함.)

```jsx
useEffect(() => {
  fetch().then((data) => {
    setData(data);
  });
  return () => {
    console.log("LoL");
  };
}, []);
```

16: 커밋 → ‘LoL’ → 커밋

17: 커밋 → 커밋 → ‘LoL’

의 순서로 진행되 커밋의 시간을 단축시킬 수 있음. (커밋 단축 = 화면을 보다 빨리 그림)

## 리액트 18

### useId

서버 사이드 렌더링과 클라이언트 사이드 랜더링시 useId를 사용하면 불일치 에러를 피할 수 있음.

### useTransition

UI의 변경을 가로 막지 않고 상태를 업데이트함.

이를 통해 무거운 렌더링을 막을 수 있음.

아래의 코드로 tab를 바꾸는데 오래걸리는 tab 랜더링을 제어해 텝을 여러번 누를 경우에 랜더링을 효율적으로할 수 있게한다.

```jsx
const [isPending, startTransition] = useTransition();
const [tab, setTab] = useState("about");
const selectTab = (tab) => {
  startTranstition(() => {
    // 반듯이 상태 업데이트만 포홤할 것. 동기일 것.
    setTab(tab);
  });
};
```

### useDeferredValue

리렌더링이 급하지 않은 부분을 지연함.

`useDeferredValue` 는 렌더링 완료이후 지연해놨던 렌더링을 수행하게함. (디바운스랑 비슷)

state값 자체를 감싸서사용한다.

- props, state 값만을 사용해야된다면 `useDeferredValue`
- 상태를 직접 업데이트할 수 있다면 `useTransition`

### useSyncExternalStore

useTransition 등 동시성을 사용할 경우, 외부 스토어에 대해tearing이 발생할 수 있음.

> tearing(테어링) : 찢어지다 말그대로 하나의 store(데이터 소스)를 봄에도 다른 값으로 보여지는 형태이다.
> ex) 빨강을 렌더링 도중 파랑으로 바뀐다. 이때 이미 바뀐 빨강은 빨강이 보여지고 이후의 렌더되는에들은 파랑이 보여짐.

외부 스토어(window.innerWidth, localstorage..및 useState, useReducer 가 아닌 것들)일때, `useSyncExternalStore`를 통해서 동시성을 사용하면서도 문제가 없을 수 있도록 해야된다.

```jsx
const [w, setW] = useState(0);
useEffect(() => {
	window.addEventListener('resize' () => {setW(winodw.innerWidth);} )
}, []);
// 의 경우, 동시성 hooks로 지연되면 변경 이후에도 지연된다면 0으로 보여질 수 있음.
```

그래서 라이브러리 사용할때 잘 살펴야된다. 근데 진짜 window 참조하는 일아니면 일어나기 힘들듯…

### useInsdertionEffect

css-in-js를 사용할 때, 클라이언트 렌더링시 stlye테그를 삽입하는 작업이 발생하지 않도록 도와주는 hooks임

(스타일을 모두 모아서 서버 사이드 렌더링 이전에 style에 삽입함.)

브라우저가 레이아웃 단계를 계산하기 전에 집어 넣는다. (DOM이 반영되기전 동기적으로 실행됨)

`useInsdertionEffect` → `useLayoutEffect` → `useEffect` 순으로 실행됨.

> 라이브러리 만들때나 쓰고 실제에서는 쓰지않길 권고

### renderToPipeableStream

서버사이드에서 Suspense를 사용해 필요한 부분을 보다 빠른 렌더링함.

next는 옜~~~날에 적용됨.

### 자동 배치 개선

기존 react event handler를 기반으로는 최적화가 되어있었지만, promise(setTimeout, sleep)은 지원이 안되었는데 배치를 통해 여러 업데이트가 있어도 한번만 렌더링되도록 개선.

### 엄격 모드 개선

- side-effects 검사 (useState, useMemo, 함수형 컴포넌트 내부)
  - 함수형 프로그래밍에서 함수는 항상 같은 값을 반환야함. = 함수의 순수성을 확인
- useEffect 두번 실행
  - 사용자가 화면에 돌아왔을 경우 이전 화면을 표시하고 싶음
  - 이를 위해 최초 마운트시 자동으로 마운트해제 다시 마운트 실행해 화면을 보여준다.
    - 그래서 2번되는것 처럼보이는것임.

### Suspense

지연된 컴포넌트 로딩 전에는 fallback으로 보여줬다가 로딩후 보여줌.

18이전에는 문제가 있었음.

- Suspense로 지연된 컴포넌트에서 useEffect 동작
- 서버에서 사용할 수 없음

18에서는

- Suspense로 지연 및 노출 상태에대해서 useLayoutEffect, useLayoutEffect-cleanup이 정상적으로 실행됨
- 서버에서도 실행이 가능함
- 그럼에도 next 및 react.lazy 경우 등 재한적으로 사용이 가능함.
