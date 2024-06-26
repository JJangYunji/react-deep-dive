# 11장에 앞서: 10장 中 이벤트 핸들링
## 이벤트 핸들링
### 이벤트 타겟
웹 페이지에서 사용자가 클릭하는 지점에 있는 요소는 "이벤트 타깃" 또는 "리프 노드"다. 이는 해당 클릭 지점에서 가장 구체적인, 즉 가장 하위에 위치한 요소이다. 이 타겟에 대한 이벤트가 발생하는 과정에서 이벤트가 전파되는 경우 각 노드는 "원본 이벤트 객체"를 공유한다. 캡쳐링-타겟도달-버블링을 거치는 이유는 다음과 같다.

**캡쳐링**
특정 상황에서 이벤트를 미리 처리하거나 중간에 이벤트를 중단해야 할 때 유용하다. 캡처링 단계의 존재는 개발자가 이벤트 처리의 흐름을 더욱 세밀하게 제어할 수 있게 해준다. 

- 이벤트 핸들링의 우선 순위 제공: 특정 이벤트가 발생했을 때, 상위 요소에서 먼저 이벤트를 감지하고 처리할 기회를 제공. 이는 하위 요소보다 먼저 이벤트에 반응해야 하는 경우 유용하다. 예를 들어, 특정 조건에서 이벤트를 차단하거나, 특정 동작을 먼저 수행한 후 하위 요소로 이벤트를 전달해야 할 때 사용된다.

- 보안 및 사전 처리: 캡처링 단계에서 이벤트를 처리함으로써, 보안 관련 로직이나 데이터 검증과 같은 작업을 수행할 수 있다. 이를 통해 이벤트가 실제 타깃에 도달하기 전에 필요한 조치를 취할 수 있다.

## 리액트 이벤트 핸들링
리액트는 자체적인 합성 이벤트(synthetic events) 시스템을 통해 DOM 이벤트를 처리한다. 이 합성 이벤트는 브라우저 간 호환성을 보장하고, 리액트 이벤트 핸들러에서 자체적으로 이벤트를 관리하므로 더 빠른 성능을 제공한다. 합성 이벤트는 실제 DOM 이벤트를 감싸는 래퍼로, 리액트 이벤트가 실제 DOM으로 전파되기 전에 리액트가 중간에서 처리한다.

브라우저에서 이벤트 발생 -> 이벤트 처리 시스템이 document level 또는 root level에서 이벤트 리스닝 -> 내부  타입과 타겟 식별 -> 합성 이벤트 생성 -> 핸들러 실행 -> 이벤트 버블링 에뮬레이션 -> (선택)버블링 또는 타겟 단계에서stop propagation 적용

대충 갓난아기가 쿠키를 로켓배송시켰다고 생각하면 모든게 이해가 된다.


## 리액트 19
Next 13부분을 살펴본 뒤 보면 이해가 잘 된다. 아래에서 다루도록 하겠다.

---
# 11장 Next.js 13과 리액트 18

Nextjs 13의 주요한 변경사항
- 서버사이드 렌더링과 관련된 변화가 많이 있는 React 18을 채용하였다.
- 레이아웃 지원을 시작했다.
- 트랜스파일러로 Rust를 사용한 SWC를 출시하였다.
- 웹팩을 대체할 Turbopack을 출시하였다.

---
# Next.js 돌아보기
서버 사이드 렌더링(SSR), 정적 사이트 생성(SSG), 클라이언트 사이드 렌더링(CSR), 그리고 Incremental Static Regeneration(ISR)을 지원하는 React 기반의 프레임워크이다. 이 프레임워크는 웹 개발에 있어서 빠른 성능, SEO 최적화, 그리고 개발자 친화적인 환경을 제공하기 위해 설계되었다.

Next.js는 Nest.js와 같은 별도의 서버 프레임워크 없이도 웹 애플리케이션의 전반적인 기능을 수행할 수 있으며, API 라우트를 통해 서버 사이드 로직 처리 역시 가능하다. 따라서 Next.js 하나로 클라이언트와 서버의 모든 동작을 처리할 수 있다. 

pages 디렉토리 내의 각 파일은 라우트와 직접 매핑되므로, 파일 이름과 구조를 명확히 관리해야 한다.

클라이언트 사이드 코드와 서버 사이드 코드를 명확히 분리하며, 보안을 유지해야 한다.

## 서버 사이드
**렌더링 방식:** Next.js의 서버 사이드 렌더링은 초기 페이지 로드 시 사용자의 요청에 따라 즉시 HTML을 생성하고, 이를 클라이언트로 전송한다. 이 방식은 사용자에게 빠른 초기 로딩을 제공하고, 검색 엔진 최적화(SEO)에 유리하다.

**API 라우트:** /pages/api 디렉토리 내부에 파일을 배치함으로써 API 라우트를 설정할 수 있다. 이 라우트들은 서버에서만 실행되며, 데이터베이스 쿼리, 사용자 인증, 외부 API 호출 등 서버 사이드 로직을 처리한다.

**데이터 페칭:** 서버 사이드에서는 getServerSideProps, getStaticProps 등의 데이터 페칭 함수를 사용하여 페이지 렌더링 전에 필요한 데이터를 미리 가져올 수 있다.

## 클라이언트 사이드
**하이드레이션:** 서버로부터 받은 HTML은 클라이언트에서 React가 관리하는 동적인 웹 페이지로 "하이드레이션"되어 사용자와의 상호작용을 처리한다. 이 과정에서 클라이언트 사이드에서만 필요한 자바스크립트가 로드되고 실행된다.

**동적 라우팅:** 클라이언트 사이드 라우팅은 사용자가 페이지 간에 이동할 때 페이지를 새로 로드하지 않고, 필요한 데이터만을 비동기적으로 불러와 페이지를 업데이트한다. 이는 사용자 경험을 향상시킨다.

**클라이언트 사이드 데이터 페칭:** 페이지의 동적 부분에 대한 데이터는 클라이언트 사이드에서 fetch API나 Axios 같은 라이브러리를 사용해 비동기적으로 불러올 수 있다.

## 서버와 클라이언트 간의 상호작용
Next.js에서 서버와 클라이언트 사이의 상호작용은 주로 데이터와 상태를 공유하는 방식으로 이루어진다. 예를 들어, 서버에서 생성된 HTML 페이지는 클라이언트로 전송된 후, 사용자의 상호작용에 따라 필요한 데이터만을 클라이언트에서 불러와 업데이트한다. 또한, API 라우트를 통해 클라이언트는 서버에 데이터 요청을 보낼 수 있고, 서버는 이에 대한 응답을 처리하여 다시 클라이언트로 전송한다.

---



## 11.1 app 디렉터리의 등장

```javascript
export default function App() {
  return (
    <div>
    	Common Layout
    </div>
    <Routes>
    	<Route ... >
    	<Route ... >
    </Routes>
```

리액트에선 위와 같은 구조에서 Common Layout을 유지할 수 있으나, Next.js는 각각의 파일이 하나의 페이지가 되므로 공통 영역을 가질 수 없다. Next.js 13까지 공통된 것은 다음과 같았다.

**_document.js**
_document.js 파일은 서버에서만 작동하는 파일이다. 이 파일은 웹 애플리케이션의 HTML 문서 구조를 정의하는 데 사용된다. 클라이언트 사이드 로직을 추가하는 것은 금지되어 있다. 그 이유는 _document.js가 초기 서버 사이드 렌더링 시에만 실행되며, 페이지를 네비게이션 할 때 재실행되지 않기 때문이다. 따라서 클라이언트 사이드에서 변화를 일으킬 수 있는 스크립트나 로직은 이 파일에 포함되어서는 안 된다.

**_app.js**
_app.js 파일은 Next.js 애플리케이션의 루트 컴포넌트를 정의한다. 이 파일은 페이지 간의 상태 유지를 가능하게 하며, 애플리케이션 전반에 걸쳐 공통적으로 사용되는 레이아웃이나 페이지 전환, 타이틀 업데이트 등을 관리한다. 모든 페이지 요청에 대해 실행되기 때문에, _app.js는 전역 상태 관리나 라우팅 관리에 적합하다. _document.js와 달리, _app.js는 클라이언트 사이드에서도 실행되어 페이지 간에 일관된 상태를 유지할 수 있도록 한다.

그러나 위와 같은 요소를 사용하여서는 페이지별로 다른 레이아웃을 생성할 수 없기 때문에 제한적이었다. 이러한 사항을 레이아웃을 통해 극복하였다.

아래에서 구조를 살펴본다.

### 11.1.1 라우팅
기존에 Next.js는 프레임워크로서 파일시스템을 기반으로 페이지를 생성하였다. 정해진 디렉터리 구조 등을 따라야 했으며 이에 따라 생성되는 내용은 다음과 같다.
> /pages/a/b.tsx(파일시스템) -> /a/b(URL)

이는 Next.js 13에 이르러 다음과 같이 변경되었다.
>app/a/b -> /a/b

디렉터리 b 내에는 예약된 파일명만이 next.js에서 사용되게 된다. 예약된 파일명은 다음과 같다.



- **layout.js**
- **page.js**
- **error.js**


#### 11.1.1.1 layout.js
```typescript
export default function AppLayout({ children }: { children: ReactNode}) {
  return (
    <PageLayout>
    	{children}
    </PageLayout>
  )
}
```
이와 같은 파일을 서브루트로 할당되는 서브디렉터리 내에 할당할 수가 있는데 이렇게 할당된 layout은 해당 위치부터 하위에 이르기까지 영향을 미친다.

루트의 경우에는 웹페이지에 필요한 기본 정보를 담아둘 수도 있다.

이를 통해 공통된 UI 뿐만 아리나 웹페이지를 시작하는데에 필요한 공통 코드를 삽입할 수도 있다. 다만 다음과 같은 주의사항을 준수하여야 한다.
- layout은 app 디렉터레 내부에서는 예약어이다. layout.{js|jsx|ts|tsx}는 공통 레이아웃 관리 목적으로만 사용한다.
- layout은 children을 props로 받아서 렌더링해야 한다.
- layout에서는 반드시 export default로 내보내는 컴포넌트가 있어야 한다.
- 비동기 작업을 수행할 수 있다는 점을 인지한다.

**클라이언트 사이드:** layout은 페이지 전환 시에도 유지되는 구조를 제공하며, 사용자 인터페이스의 일관성을 유지한다.

**서버 사이드:** 서버에서 페이지 요청 시에도 layout은 사용되어 일관된 HTML 구조를 생성한다.

#### 11.1.1.2 page.js
기존에 Next.js에서 렌더링 되는 페이지의 역할을 맡았던 파일에 대한 예약어이다.

**클라이언트 사이드:** 각 페이지는 클라이언트 사이드에서 동적으로 렌더링된다.

**서버 사이드:** 첫 요청이나 페이지 새로고침 시에는 서버에서 페이지가 렌더링되고, 결과적으로 생성된 HTML이 클라이언트로 전송된다.
다음과 같은 props를 받는다.

params
params 객체는 동적 라우팅을 사용할 때 URL의 경로 부분에서 정의된 변수들을 포함한다. 예를 들어, /posts/[id]라는 파일 경로로 정의된 페이지가 있고 사용자가 /posts/123 URL로 접속했다면, params 객체는 { id: '123' }와 같이 매핑된다. 이 객체는 페이지 경로의 각 세그먼트를 변수로 캡처하여 접근할 수 있게 해준다.

searchParams
searchParams 객체는 URL의 쿼리 문자열 부분을 처리한다. 이 객체는 URLSearchParams 인터페이스를 기반으로 하며, URL의 쿼리스트링에서 키-값 쌍을 쉽게 읽거나 조작할 수 있다. 예를 들어, 사용자가 /search?query=nextjs URL로 접속했다면, searchParams.get('query')는 nextjs를 반환한다.

#### 11.1.1.3 error.js
해당 라우팅 영역에서 사용되는 공통 에러 컴포넌트이다. 라우팅별로 서로 다른 에러 UI를 렌더링할 수 있다.

**클라이언트 사이드:** loading은 사용자가 새 페이지로 이동하는 동안 보여지는 상태이다. 페이지 로딩 중 사용자 경험을 개선하기 위해 사용된다.

**서버 사이드:** 서버 사이드에서는 loading 상태가 존재하지 않는다. 페이지 로딩 상태는 클라이언트에서 관리된다.

```typescript
'use client' // Error components must be Client Components
 
export default function Error({error,reset}: {error: Error, reset: () => void}) {
  useEffect(() => {
    // Log the error to an error reporting service
    console.error(error)
  }, [error])
 
  return (
    <div>
      <button
        onClick={() => reset()}
	  >
      </button>
    </div>
  )
}
```
error : 에러 정보를 담고 있음

reset : 에러 바운더리를 초기화 할 수 있는 함수


#### 11.1.1.4 not-found.js
```typescript
export default function NotFound() {
  return '404 Not Found'
}
```
찾을 수 없는 페이지를 렌더링 할 때 사용된다.

**클라이언트 사이드:** 사용자가 없는 페이지에 접근하려 할 때 not-found 페이지가 표시된다.

**서버 사이드:** 서버에서도 요청받은 URL이 유효하지 않을 경우 not-found 페이지를 렌더링하여 응답한다.


#### 11.1.1.5 loading.js
```typescript
export default function Loading() {
  return 'Loading...'
}
```
리액트 Suspense를 기반으로 해당 컴포넌트가 불러오는 중임을 나타낸다.

**클라이언트 사이드:** loading은 사용자가 새 페이지로 이동하는 동안 보여지는 상태이다. 페이지 로딩 중 사용자 경험을 개선하기 위해 사용된다.

**서버 사이드:** 서버 사이드에서는 loading 상태가 존재하지 않는다. 페이지 로딩 상태는 클라이언트에서 관리된다.

#### 11.1.1.6 route.js
이것에 앞서 잠시 /app/api에 대해 살펴본다.

### /app/api 디렉토리
/app/api 내부에 파일을 배치함으로써, 개발자는 서버 사이드 로직을 구현할 수 있다. 예를 들어, /app/api/user.js 파일을 생성하면, 이 파일은 http://myDomain/api/user 주소로 접근할 수 있는 API 엔드포인트가 된다. 이 파일 내에서는 데이터베이스 조회, 데이터 처리, 외부 API 호출 등 서버 사이드에서 필요한 작업을 수행할 수 있다.

**API 라우트 파일**
API 라우트 파일에서는 Next.js의 API 라우트 기능을 활용하여 HTTP 요청을 처리한다. 각 API 라우트 파일은 기본적으로 다음과 같은 구조를 가질 수 있다:
```javascript
  export default function handler(req, res) {
  if (req.method === 'GET') {
    // GET 요청 처리 로직
    res.status(200).json({ message: 'This is a GET request' });
  } else if (req.method === 'POST') {
    // POST 요청 처리 로직
    res.status(201).json({ message: 'This is a POST request' });
  } else {
    // 지원하지 않는 HTTP 메소드에 대한 응답
    res.status(405).end();
  }
}
```
  
**다시 돌아와서,** 파일명 라우팅이 없어지고 위와 같은 내용은 디렉터리 라우팅으로 변경되었다고 한다. 즉 /pages/api를 기준으로 디렉터리를 추가하여 api 엔드포인트의 주소를 구성해야 하는 것이다. 다만 여기서 api 디렉터리를 사용하는 것은 컨벤션으로, 사실은 어떤 디렉터리를 사용하여도 route.ts가 있는 경우 해당 디렉터리는 라우팅 포인트로 작동한다.

> /apps/internal-api/hello/route.ts
>
> "curl -X GET "http://localhost:3000/internal-api/hello"

  
다만 page.tsx와 공존할 수 없다.



---

## 11.2 리액트 서버 컴포넌트
서버사이드 렌더링과 다른 개념이다.

### 11.2.1 기존 리액트 컴포넌트와 서버사이드 렌더링의 한계
리액트의 모든 컴포넌트는 클라이언트 사이드에서 작동하였다. 이는 즉 모든 코드가 클라이언트로 다운로드 되고, 클라이언트 사이드에서 리액트 돔을 구성한 뒤 작동하는 것을 의미한다.

서버사이드 렌더링에서는 서버에서 필요한 데이터를 모아 HTML을 생성하고, 클라이언트에서는 전송된 HTML을 렌더링 한 후 만들어진 DOM을 기준으로 하이드레이션을 진행한다. 이러한 구조에는 다음과 같은 한계점들이 존재한다.

- **자바스크립트 번들 크기가 0인 컴포넌트를 만들 수 없다.** 즉, 컴포넌트가 클라이언트 사이드에서 실행되므로 실행에 필요한 자바스크립트 번들 파일을 포함하게 된다. 이것을 서버측에서 수행하길 원할 수 있다.

- **백엔드 리소스에 대한 직접적인 접근이 불가능하다.** API를 이용하여야 한다. 클라이언트에서 백엔드 리소스에 직접 접근할 수 있을 수도 있다?

- **자동 코드 분할(code split)이 불가능하다.** 코드가 필요하게 되는 시점에 동적으로 게으른 로딩을 하는것을 의미한다. 개발자가 일일히 설정해주고, 분기 등 처리가 일어난 후에야 로딩을 시작하기 때문에 분할의 이점이 없어지게 된다.

- **연쇄적인 로딩** 중첩된 구조의 컴포넌트 로딩이 있는 경우, 그리고 각 컴포넌트들이 서버에 요청을 해야 하는 경우, 그 수만큼의 지연과 요청이 발생한다. 이러한 모든 과정이 서버에서 일어난다면 효율적일 것이다.

이러한 문제점들이 모두 리액트가 클라이언트를 중심으로 돌아가기 때문에 발생한다고 이야기 할 수 있다고 한다. 



### 11.2.2 서버 컴포넌트란?
서버 컴포넌트(Server Component)란 하나의 언어, 하나의 프레임워크, 하나의 API와 개념을 사용하면서 서버와 클라이언트 모두에서 컴포넌트를 렌더링 할 수 있는 기법.

다음과 같이 세 종류로 이루어진다.

**서버 컴포넌트**
서버 수준에서 실행되어 그 결과값으로 정적인 결과물을 반환하는 컴포넌트로 다음과 같은 특성을 갖는다.
- 상태를 가질 수 없다.
- 렌더링 생명주기를 사용할 수 없다.
- DOM API에 접근할 수 없다.
- 서버에 있는 데이터에 접근할 수 있다.
- 다른 서버 컴포넌트를 렌더링하거나, (클라이언트 컴포넌트를 렌더링 할 수 있다)<-?

**클라이언트 컴포넌트**
클라이언트에서 실행되어 렌더링되는 컴포넌트
- 서버 컴포넌트를 불러오거나, 서버 전용 훅/유틸리티를 사용할 수 없다.
- 서버 컴포넌트를 자식으로 가질 수 있다. 서버 컴포넌트는 결과값 트리로 반환되기 때문이다.
- 상태나 생명주기 등을 가질 수 있다.

**공용 컴포넌트**
서버와 클라이언트 모두에서 사용할 수 있으며, 두 제약을 동시에 받는다.

리액트는 모든 컴포넌트를 공용컴포넌트로 판단한다(안전).

다만 파일의 첫 줄에 'use client'를 작성하는 경우 클라이언트 컴포넌트로 간주한다.

### 11.2.3 서버 사이드 렌더링과 서버 컴포넌트의 차이
서버 사이드 렌더링의 장점을 사용할 수 있는 컴포넌트를 의미하는 것이 서버 컴포넌트이다.

기본적으로 작동이 클라이언트일 것으로 예상되기 때문에, 서버 컴포넌트라는 이름이 붙었다.

서버 작동이 기본일 것이라고 생각하는 경우, 양쪽의 장점을 취하기 위해 클라이언트 컴포넌트가 되었을 것이다.

### 11.2.4 서버 컴포넌트는 어떻게 작동하는가?
- 서버가 렌더링 요청을 받는다.
- 서버는 받은 요청에 따라 컴포넌트를 JSON으로 직렬화한다.
- 빈 부분은 플레이스 홀더 형식으로 남겨둔 채 결과물을 보낸다.
- 클라이언트 사이드에서 결과물을 역직렬화 한 후 렌더링을 수행한다.
- 브라우저가 리액트 컴포넌트 트리를 구성한다.
- 트리를 렌더링한다.
- DOM에 커밋한다.

여기서 특별한 점
- 클라이언트로 정보를 스트리밍 형태로 보내 빠르게 반응할 수 있다.
- 컴포넌트별로 번들링이 별개로 되어 있다.
- HTML이 아니라 JSON 방식으로 보내진다. 이는 복잡하게 구성된 형태를 조화롭게 구성하기 위해서이다.

## 11.3 Next.js에서의 리액트 서버 컴포넌트
위에서 살펴본 것과 같이

서버 컴포넌트는 클라이언트 컴포넌트를 불러올 수 없다.
클라이언트 컴포넌트는 서버 컴포넌트를 내용으로 포함할 수 있다.


### 11.3.1 새로운 fetch 도입과 getServerSideProps, getStaticProps, getInitial Props의 삭제
fetch가 데이터 요청의 표준으로서 사용된다.
비동기적으로 요청하며 다음과 같이 사용한다.
```typescript
async function getData() {
  const result = await fetch('https://api.imagoworks.com')
  
  return result.json()
}

export default function Page() {
  const data = await getData()
  
  return(
    <main>
    	<Children data={data} />
    </main>
}
```
내부적으로 캐싱을 하기 때문에 동일한 요청이 서버 컴포넌트 트리 내에 있을 경우, 중복 요청을 방지한다.
![](https://velog.velcdn.com/images/velog_sp/post/20a76cb5-62a3-4701-b923-285f089575e5/image.png)


### 11.3.2 정적 렌더링과 동적 렌더링
정적 렌더링은 미리 해놓는것 - 캐싱 활용

동적 렌더링은 서버에서 요청이 올때마다 렌더링 하는 것

이것은 모두 결국 fetch가 동작시에 캐싱을 하는지 아닌지에 따라 결정되는데, 그 명세는 다음과 같다.

- fetch(URL, { cache: 'force-cache' }) : 캐싱한다.
- fetch(URL, { cache: 'no-store}) : 매번 새로운 데이터를 불러온다.
- fetch(URL, { next: { revalidate: 10 } }) : 일정 기간동안만 캐싱을 유지한다.

### 11.3.3 캐시와 mutating 그리고 revalidating
> export const revalidate = 60

위와 같이 루트에 설정해 놓는 경우, 위의 기간에 따라 캐시를 갱신한다. 갱신 과정에서 공백이 생기지 않도록 다음과 같이 한다.

기간 내엔 캐시된 데이터 보여준다 - 해당시간이 지나도 데이터를 불러오기 전까진 캐싱된 데이터를 보여준다 - 데이터가 불러와지면 데이터를 캐싱하고, 실패했을 경우 이전에 캐시된 데이터를 보여준다.

### 11.3.4 스트리밍을 활용한 점진적인 페이지 불러오기
TTFB(Time To First Byte)의 시간을 줄여주고, 최초 콘텐츠풀 페인팅(FCP; First Contentful Paint)을 개선하는데 큰 도움을 준다.

## 11.4 웹팩의 대항마, 터보팩의 등장(beta)
기존 자바스크립트로 만들어지고 제공되는 기능을 Rust나 Go등 다른 언어를 사용해 제공
많은 프로젝트에서 바벨을 대신해 사용하고 있다. 웹팩 대비 700배 Vite보다 10배 빠르다고 소개하고 있다고 한다.

## 11.5 서버 액션(alpha)
API를 생성하지 않고 함수 수준에서 직접 서버에 접근해 데이터 요청 등을 수행할 수 있는 기능이다. 책 기준으로는 서버 액션을 활성화하기 위해 `next.config.js`에서 실험 기능을 활성화해야 한다고 하고 있으며, "use server"키워드를 사용해야 한다고 한다.

아래와 같은 자세한 내용은 생략한다.




### 11.5.1 form의 액션
### 11.5.2 input의 submit과 image의 formAction
### 11.5.3 startTransition과의 연동
### 11.5.4 server mutation이 없는 작업
### 11.5.5 서버 액션 사용 시 주의할 점

## 11.6 그 밖의 변화
이 부분은 책에서도 알아서 찾아보라고 하면서 생략한다. 나도 생략한다.

서버 컴포넌트 등은 단순하게 프로젝트를 구성해서는 사용하기 어렵기 때문에, 프레임워크를 통한 적용이 거의 필수적이며, 리액트 팀도 이러한 부분을 구현하기 위해서는 프레임워크와의 협업이 필요하다고 판단, Vercel등과 활발이 협업하고 있다고 한다. 고려하자.

## 11.7 맛보기는 생략한다.

## 근황 알아보기
### 트랜스파일러 / 컴파일러
**SWC(Speedy Web Compiler)**
JavaScript 및 TypeScript 컴파일러로, 특히 Babel과 같은 기존 도구들에 비해 그 성능과 속도에서 두드러진 차이를 보인다. Rust로 작성되어 있어 싱글 스레드에서는 Babel보다 최대 20배, 멀티 코어 환경에서는 최대 70배 빠른 속도를 제공한다 
https://blog.logrocket.com/why-you-should-use-swc/

**주요 특성 및 비교 :**
성능: SWC는 빌드 시간이 중요한 프로젝트에 적합할 만큼 빠른 속도로 컴파일을 수행한다. Babel, Sucrase, 심지어 esbuild보다도 빠를 수 있는 성능을 자랑한다

**호환성: **
SWC는 최신 JavaScript 기능을 포함한 JSX 및 TypeScript를 지원한다. 주로 새로운 브라우저에 최적화되어 있으며, 구형 브라우저와의 호환성을 위해서는 추가 설정이 필요할 수 있다

**인테그레이션:**
SWC는 개발 환경에 통합하기가 비교적 간단하다. Webpack과 같은 인기 있는 번들러와 빌드 시스템에 swc-loader를 사용하거나 Jest의 성능을 향상시키기 위한 플러그인을 제공한다. 설정은 주로 .swcrc 파일을 통해 코드의 변환 방법을 지정함으로써 이루어진다

**커뮤니티 및 지원:**
SWC는 Babel에 비해 상대적으로 신생이지만, 주요 프로젝트와 회사에서의 사용 증가로 인해 커뮤니티가 성장하고 있다. 

아래 벤치마크 영상을 확인한 결과 바벨보다는 확연히 뛰어난 성능을 보이나 경합은 있는것으로 보인다. 추후 커뮤니티 활성도 등을 고려하여 선택하면 될 것으로 생각된다.
https://www.youtube.com/watch?v=1p-Vjle0vZ0
![](https://velog.velcdn.com/images/velog_sp/post/bddcc6e9-2090-4ee1-989e-d70f13ba3815/image.png)
![](https://velog.velcdn.com/images/velog_sp/post/6aa54cfc-cf48-4cba-9530-24a2ab26d1ae/image.png)
![](https://velog.velcdn.com/images/velog_sp/post/7b2c3721-366e-4d82-bcac-ed1629623f5b/image.png)
![](https://velog.velcdn.com/images/velog_sp/post/558bb0bf-d05d-40e6-acea-32e444592680/image.png)


### NextJS 버전
https://nextjs.org/blog
위 블로그에서 아주 간략하게 축약된 주요 변경사항들을 확인할 수 있다.
2022.10.25 : 13
2022.12.22 : 13.1
2023.02.23 : 13.2
2023.04.06 : 13.3
2023.05.04 : 13.4
2023.10.19 : 13.5

2023.10.26 : 14
2024.01.18 : 14.1
2024.04.11 : 14.2

현재에는 TurboPack이 정식 적용되었고, ServerAction이 안정화되었으며 여러가지 기능들이 추가되었다.

### 서버컴포넌트
Next.js 13의 서버컴포넌트 - https://blog.logrocket.com/react-server-components-next-js-13/
2024.02 리액트 트렌드 - https://www.robinwieruch.de/react-trends/
2024.02 리액트팀 무슨일하고 있나 - https://chatgpt.com/c/3d223760-aa69-4356-bd72-abf44306ead3

리액트 서버 컴포넌트(React Server Components, RSC)는 성능 향상과 네트워크 요청 감소에 기여한다.

Next.js 13에서는 RSC가 기본적으로 활성화되어 있으며, 데이터를 서버에서 가져오고 이를 스트리밍하는 방식으로 구현되어 있다. 이 방식은 전통적인 SSR과 SSG의 의 장점을 취하고 단점을 상쇄하여 새로운 방향성으로 제안되고 있다.

RSC의 도입은 데이터를 서버에서 컴포넌트 수준에서 가져오는 새로운 아키텍처를 가능하게 한다. 이는 클라이언트와 서버 간의 '워터폴' 요청 방식을 개선하며, 서버에서 데이터 처리가 진행되고 클라이언트는 최종적으로 컴포넌트를 받아 렌더링만 수행함으로써 응답 시간과 성능이 크게 향상된다.

위 링크를 참조하여서 보면, 리액트 팀은 RSC를 더욱 발전시키기 위해 지속적으로 연구하고 개선할 예정이라고 한다. 이에 따라 서버와 클라이언트 간에 일관된 프로그래밍 모델을 사용할 수 있게 하며, 다양한 환경에서 애플리케이션을 운영할 수 있도록 지원할 것으로 예상된다.


## 리액트 19
**동시 렌더링 지원:** 
React 19는 동시 렌더링을 기본 모드로 지원한다. 이는 UI 업데이트를 더 작은 비동기 작업으로 세분화하여, 우선순위가 높은 업데이트를 먼저 처리함으로써 사용자 경험을 개선한다.

**Suspense 개선:** 
React 19는 Suspense 기능을 개선하여, 로딩 상태와 데이터 종속성을 선언적으로 처리할 수 있다. 이를 통해 데이터 기반 애플리케이션 개발이 간소화된다.

**서버 측 렌더링 개선:**
React 19는 서버 측 렌더링을 통해 TTFB(Time to First Byte)와 SEO 성능을 향상한다. 서버에서의 렌더링 개선은 클라이언트로의 응답 속도를 빠르게 하여 사용자 경험을 향상시킨다.

**점진적 도입:** 
React 19는 기존 React 프로젝트와의 호환성을 유지하면서 점진적으로 업그레이드할 수 있는 환경을 제공한다. 이는 개발자가 새로운 기능을 손쉽게 도입할 수 있게 한다.

**성능 최적화 기법:** 
React 19는 런타임 효율을 높이고 번들 사이즈를 줄이기 위한 다양한 성능 최적화 기술을 사용한다. 이는 애플리케이션의 렌더링 속도와 메모리 관리를 향상시킨다.

**디버깅 개발자 도구:** 
React 19는 개발자가 컴포넌트의 수명 주기와 성능 문제를 보다 정밀하게 진단할 수 있도록 고급 디버깅 및 프로파일링 도구를 제공한다.
https://velog.io/@velog_sp/11%EC%9E%A5