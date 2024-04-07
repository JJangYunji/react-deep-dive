# 05장: 리액트와 상태 관리 라이브러리

## 5.1 상태 관리는 왜 필요한가?

### 웹 애플리케이션에서 상태로 분류될 수 있는 것

- UI: 상호 작용이 가능한 모든 요소의 현재 값
- URL: 브라우저에서 관리되고 있는 상태값
- Form(폼): loading, submit, disabled, validation 등
- 서버에서 가져온 값: 클라이언트에서 서버로 요청을 통해 가져온 값

### Flux

#### Flux 패턴의 등장

- 양방향 데이터 바인딩
    - 어디서 어떤 일이 일어나서 이 상태가 변했는지 등을 추적하고 이해하기가 매우 어려운 상황
    - 뷰(HTML)가 모델(자바스크립트)을 변경할 수 있으며, 반대의 경우 모델도 뷰를 변경할 수 있음
- 양방향이 아닌 단방향으로 데이터 흐름을 변경하는 Flux 패턴이 등장
    - 데이터의 흐름이 단방향으로만 흐르기 때문에 데이터의 흐름을 추적하고 이해하기가 쉬워짐

#### Flux 패턴

- action(액션): 어떠한 작업을 처리할 액션과 그 액션 발생 시 함께 포함시킬 데이터
- dispatcher(디스패처): 액션을 전달받아 스토어에 보내는 역할
- store(스토어): 실제 상태에 따른 값을 저장하고 상태가 변경되면 뷰에게 알려주는 역할
- view(뷰): 사용자에게 보여지는 화면, 스토어에서 만들어진 데이터를 가져와 화면 렌더링

#### 단방향 데이터 흐름의 장단점

- 장점
    - 데이터의 흐름이 모두 액션이라는 한방향으로 줄어들기 때문에 데이터의 흐름을 추적하기 쉽고 코드를 이해하기가 한결 수월해집니다.
- 단점
    - 사용자의 입력에 따라 데이터를 갱신하고 화면을 어떻게 업데이트해야 하는지도 코드로 작성해야 하므로 코드량이 많아지고 복잡해집니다.

### Redux

- 리덕스는 Flux 패턴을 기반으로 만들어진 라이브러리 + Elm 아키텍처를 도입

#### Elm

- Elm: 웹페이지를 선언적으로 작성하기 위한 언어
- Elm 아키텍처 요소
    - 모델(Model): 애플리케이션의 상태를 나타내는 데이터
    - 뷰(View): 모델을 시각적인 요소로 변환(HTML)
    - 업데이트(Update): 사용자의 입력에 따라 모델을 업데이트하는 함수
- 데이터 흐름을 세 가지로 분류하고, 이를 단방향으로 강제해 웹 애플리케이션의 상태를 안정적으로 관리

#### Redux의 특징

- 하나의 상태 객체를 스토어에 저장해 두고, 이 객체를 업데이트하는 작업을 디스패치해 업데이트를 수행합니다.
- 웹 애플리케이션 상태에 대한 완전히 새로운 복사본을 반환한 다음, 애플리케이션에 새롭게 만들어진 상태를 전파합니다.
- 리덕스는 하고자 하는 일에 비해 구현을 위한 보일러플레이트가 많이 필요하다는 단점이 있습니다.

### Context API

- Context API: 리액트에서 상태를 전역적으로 관리할 수 있는 기능
- Context API는 상태 관리가 아닌 주입을 도와주는 기능이며, 랜더링을 막아주는 기능 또한 존재하지 않으니 사용할 때 주의가 필요합니다.

### React Query, SWR

- 훅과 state의 등장으로 이전에는 볼 수 없었던 방식의 상태 관리 등장
- React Query: 서버로부터 데이터를 가져오는 작업을 쉽게 할 수 있도록 도와주는 라이브러리
- SWR: React Query와 비슷한 기능을 제공하는 라이브러리
- 두 라이브러리 모두 HTTP 요청에 특화된 상태 관리 라이브러리입니다.

## 5.2 리액트 훅으로 시작하는 상태 관리

### useState와 useReducer

- useState의 등장으로 리액트에서는 여러 컴포넌트에 걸쳐 손쉽게 동일한 인터페이스의 상태를 생성하고 관리할 수 있게 되었습니다.
- useState와 useReducer로 컴포넌트 내부의 상태를 관리할 수 있지만, 이 둘을 기반으로 하는 사용자 지정 훅의 한계는 명확합니다.
- 훅을 사용할 때마다 컴포넌트별로 초기화되므로 컴포넌트에 따라 서로 다른 상태를 가질 수 밖에 없고, 결과적으로 컴포넌트별로 상태의 파편화를 만들어 버립니다.
- 기본적인 useState를 기반으로 한 상태를 지역 상태(Local State)라고 하며, 이 지역 상태는 해당 컴포넌트 내에서만 유효하다는 한계가 있습니다.
- 여러 컨포넌트가 동시에 사용할 수 있는 전역 상태(Global State)로 만들어 컴포넌트가 사용하는 모든 훅이 동일한 값을 참고할 수 있어야 합니다.

### useState와 Context

- 훅을 사용하는 서로 다른 스코프에서 스토어의 구조는 동일하되, 여러 개의 서로 다른 데이터를 공유해 사용할 수 있습니다.

### 상태 관리 라이브러리의 작동 방식

- useState, useReducer가 가지고 있는 한계인 컴포넌트 내부에서만 사용할 수 있는 지역 상태라는 점을 극복하기 위해 외부 어딘가에 상태를 둡니다.
- 이 외부의 상태 변경을 각자의 방식으로 감지해 컴포넌트의 렌더링을 일으킵니다.

### Recoil

- Recoil: 페이스북에서 만든 상태 관리 라이브러리
- Recoil을 사용하기 위해서는 `RocoilRoot`를 애플리케이션의 최상단에 선언해두어야 합니다.

#### RecoilRoot

- Recoil의 상태값은 `RecoilRoot`로 생성된 Context의 스토어에 저장됩니다.
- 스토어의 상태값에 접근할 수 있는 함수들이 있으며, 이 함수를 활용해 상태값에 접근하거나 상태값을 변경할 수 있습니다.
- 값의 변경이 발생하면 이를 참조하고 있는 하위 컴포넌트에 모두 알립니다.

#### atom

- atom: 상태를 나타내는 Recoil의 최소 상태 단위
- `key` 값을 필수로 가지며, 이 키는 다른 atom과 구별하는 식별자가 됩니다.
- `default` 값을 가질 수 있으며, 이 값은 초기 상태값으로 사용됩니다.
- atom은 상태값을 읽고 쓰는 함수를 반환합니다.
- `useRecoilValue`: atom의 값을 읽어오는 훅
- `useRecoilState`: atom의 값을 가져오고 변경하는 훅

### Jotai

- Jotai: Recoil의 atom 모델에 영감을 받아 만들어진 상태 관리 라이브러리
- 상향식(bottom-up) 접근법 - 작은 단위의 상태를 위로 전파하는 구조로 되어 있습니다.
- 리액트 Context의 문제점인 불필요한 리렌더링이 일어난다는 문제를 해결하도록 설계되어 있습니다.

#### atom

- Recoil과 같은 최소한의 상태 단위라는 것까지는 동일하지만 atom 하나로 파생된 상태까지 만들 수 있습니다.
- Jotai는 atom을 생성할 때 별도의 `key`를 넘겨주지 않아도 됩니다.
- `selector`가 없이도 atom만으로 atom 값에서 또 다른 파생된 상태를 만들 수 있습니다.

### Zustand

- Zustand: 리덕스에 영감을 받아 만들어진 상태 관리 라이브러리
- 하나의 스토어를 중앙 집중형으로 활용해 이 스토어 내부에서 상태를 관리하고 있습니다.
- Zustand는 특별히 많은 코드를 작성하지 않아도 빠르게 스토어를 만들고 사용할 수 있습니다.
- 간단하고 빠르게 상태를 정의할 수 있어 상태를 관리하는 입장에서 한결 가볍고 편리합니다.
- Zustand는 리덕스와 마찬가지로 미들웨어를 지원합니다.