## Next.js로 리액트 개발 환경 구축

### 1. package.json 생성

```tsx
npm init
```

### 2. devDependencies에 필요한 패키지 설치

```tsx
npm i @types/node @types/react @types/react-dom eslint eslint-config-next typescri
pt --save-dev
```

### 3. tsconfig.json 생성

```tsx
{
  // $schema는 schema store에서 제공해 주는 정보로, 해당 json 파일이 무엇인지를 알려주는 도구다.
  "$schema": "http://json.schemastore.org/tsconfig",
  "compilerOptions": {
    "target": "es5",
    "lib": ["dom", "dom.iterable", "esnext"],
    "allowJs": true,
    "skipLibCheck": true,
    "forceConsistentCasingInFileNames": true,
    "noEmit": true,
    "strict": true,
    "esModuleInterop": true,
    "module": "commonjs",
    "moduleResolution": "node",
    "resolveJsonModule": true,
    "isolatedModules": true,
    "jsx": "preserve",
    "incremental": true,
    "baseUrl": "src",
    "paths": {
      "@/*": ["*"]
    },
    "include": ["next-env.d.ts", "/*.ts", "/*.tsx"],
    "exclude": ["node_modules"]
  }
}

```

### 사용된 tsconfig.json 옵션 목록

`target`

- 타입스크립트가 변환을 목표로 하는 언어의 버전을 의미한다.
- 폴리필까지는 지원하지 않기 때문에 `Promise`와 같이 별도의 폴리필이 필요한 경우까지 모두 도와주지는 않는다.

`lib`

- 타입스크립트가 Promise, Map, window.document 등의 존재를 확일할 수 있게 해준다.

`allowJs`

- 타입스크립트가 자바스크립트 파일 또한 컴파일할지를 결정한다.

`skipLibCheck`

- 라이브러리에서 제공하는 `d.ts`에 대한 검사 여부를 결정한다.
- 활성화 되어 있다면 d.ts에서 에러 발생 시 에러를 발생 시킨다.

`strict`

- 타입스크립트 컴파일러의 엄격 모드를 제어한다.
- 모든 자바스크립트 파일에 `use strict`를 추가한다.
- 엄격한 `null check`를 활성화한다.
- 함수에 대해 사용할 수 있는 call, bind, apply에 대한 정확한 인수를 요구한다.
- 함수의 타입에 대해 엄격함을 유지한다.
- 기타 등등 매우 엄격한 작업을 한다.

`forceConsistentCasingInFileNames`

- 파일 이름의 대소문자를 구분하도록 강제한다.

`noEmit`

- 컴파일을 하지 않고, 타입 체크만 한다.
- next.js 같은 경우는 swc가 타입스크립트 파일을 컴파일하므로 굳이 타입스크립트가 자바스크립트로 컴파일할 필요가 없어 활성화 시킨다.

`esModuleInterop`

- CommonJs 방식으로 보낸 모듈을 ES 모둘 방식의 import로 가져올 수 있게 해준다.

`module`

- 모듈 시스템을 결정한다. 대표적으로 commonjs와 esnext가 있다.
- commonjs는 `require`를 사용한다.
- esnext는 `import`를 사용한다.

`moduleResolution`

- 모듈을 해석하는 방식을 설정한다.
- `node`는 node_modules를 기준으로 모듈을 해석한다.
- node는 `CommonJS`일 때만 사용할 수 있다.
- `classic`은 tsconfig.json이 있는 디렉터리를 기준으로 모듈을 해석한다.

`resolveJsonModule`

- `JSON` 파일을 import 할 수 있게 한다.
- 이 옵션을 활성화하면 `allowJs` 옵션도 자동으로 활성화된다.

`isolateModules`

- import나 export가 없는 파일이 없다면 단순 스크립트 파일로 인식해 이러한 파일이 생성되지 않도록 막는 역할이다.

`jsx`

- .tsx 파일 내부에 있는 JSX를 어떻게 컴파일할지 설정한다.
- `react(default)`
  - `React.createElement` 로 변환한다.
- `react-jsx`
  - react/jsx-runtime을 사용하여 createElement를 사용하지 않는다.
  - `import React from ‘react’` 를 사용하지 않아도 된다.
- `react-jsxdev`
  - react-jsx에서 디버깅 정보가 추가된 형태다.
- `preserve`
  - 변환하지 않고 그대로 유지한다.
  - 이 옵션을 사용한 이유는 `swc`가 JSX 또한 변환해주기 때문이다.
- `react-native`
  - 리액트 네이티브에서 사용하는 방식이다.
  - 변환하지 않는다.

`incremental`

- 이 옵션을 활성화 시키면 타입스크립트는 마지막 컴파일 정보를 `.tsbuildinfo` 파일 형태로 만들어 디스크에 저장한다.
- 다시 컴파일러가 호출되었을 때 저장한 정보를 활용하여 컴파일을 수행하기 때문에 컴파일이 더 빨라진다.

`baseUrl`

- 모듈을 찾을 때 기준이 되는 디렉터리를 설정한다. 보통 `paths`와 함께 사용된다.

`paths`

- 절대경로 지정을 할 수 있다.
- `@의 사용은 자제하는 것이 좋다.` @는 보통 @angular나 @types와 같이 스코프 패키지에 사용되기 때문이다.

`include`

- 타입스크립트 컴파일 대상에서 포함시킬 파일 목록이다.

`exclude`

- 타입스크립트 컴파일 대상에서 제외시킬 목록이다.

### 4. next.config.js 생성

```tsx
/ @type {import('next').NextConfig} */;
const nextConfig = {
  reactStrictMode: true, // 리액트 엄격모드
  poweredByHeader: false, // 보안 취약점 X-Powered-By 헤더 제거
  eslint: {
    ignoreDuringBuilds: true, // 빌드시 eslint 무시
  },
};

module.exports = nextConfig;
```

### 5. ESLint 설정

1. 설정이 쉬운 `@titicaca/eslint-config` 를 설치

   ```tsx
   npm i @titicaca/eslint-config-triple --save-dev
   ```

1. `eslint-config-next`와 `eslint-config-triple`이 함께 작동하게 하기 위해 다음과 같은 설정을 한다.

   ```tsx
   // .eslintrc.js
   const path = require("path");

   const createConfig = require("@titicaca/eslint-config-triple/create-config");

   const { extends: extendConfigs, overrides } = createConfig({
     type: "frontend",
     project: path.resolve(__dirname, "./tsconfig.json"),
   });

   module.exports = {
     extends: [...extendConfigs, "next/core-web-vitals"],
     overrides,
   };
   ```

   `extends`에 `next/core-web-vitals`를 추가하면 두 가지 설정이 모두 적용된다.

   추가적으로 .eslintignore나 .prettierignore에 `.next`나 `node_modules`를 추가해 정적 분석 대상에서 제외시킨다.

<br/>

## 깃허브 액션

깃허브 액션은 깃허브를 둘러싼 다양한 이벤트를 기반으로 깃허브에서 제공하는 가상 환경에서 사용자가 원하는 작업을 수행할 수 있도록 도와주는 서비스다.

### 깃허브 액션의 기본 개념

- 러너 (runner)
  - 파일로 작성된 깃허브 액션이 실행되는 서버를 의미한다.
  - 특별히 지정하지 않으면 공용 깃허브 액션 서버를 이용하며, 별도의 러너를 구축해 자체적으로 운영할 수도 있다.
- 액션 (action)
  - 러너에서 실행되는 하나의 작업 단위다.
  - `yaml` 파일로 작성된 내용을 하나의 액션으로 볼 수 있다.
- 이벤트 (event)
  - 깃허브 액션의 실행을 일으키는 이벤트를 의미한다.
  - 한 개 이상의 이벤트를 지정할 수 있으며, 특정 브랜치를 지정하는 이벤트도 가능하다
  - 주로 사용되는 이벤트는 다음과 같다.
    - `pull_request` : PR과 관련된 이벤트
    - `issues` : 이슈와 관련된 이벤트
    - `push` : 커밋이나 태그가 푸시될 때마다 발생하는 이벤트
    - `schedule` : 특정 시간에 실행되는 이벤트
- 잡 (jobs)
  - 하나의 러너에서 실행되는 여러 스텝의 모음이다.
  - 하나의 액션에서 여러 잡을 생성할 수 있다.
  - 별도의 설정 없이는 병렬로 실행된다.
- 스텝 (steps)
  - 잡 내부에서 일어나는 하나하나의 작업을 의미한다.
  - 병렬로 실행되지 않는다.

<br/>

### 깃허브 액션 작성

저장소 루트에 `.github/workflows` 폴더를 생성하고 파일을 작성한다.

```tsx
// build.yaml

name: test build
run-name ${{ github.actor }} has been added new comit.

on:
	push:
			branches-ignore:
				- 'main'

jobs:
	build:
		runs-on: ubuntu-latest
		steps:
			- uses: actions/checkout@v3
			- uses: actions/setup-noe@v3
				width:
					node-version: 16
			- name: 'install dependencies'
				working-directory: ./test/app
				run: npm ci
			- name: 'build'
				working-directory: ./test/app
				run: npm run build
```

별도 브랜치에서 푸시하고 풀 리퀘스트를 만들어 `details`를 확인하면 해당 CI가 어떤 절차를 거쳐서 완료됐는지 확인할 수 있다.

### `yaml 파일에 사용된 값`

`name`

- 액션의 이름이다. 액션을 구별하는 데 도움을 준다.
- 필수 값은 아니다.

`run-name`

- 액션이 실행될 때 구별할 수 있는 타이틀명이다.
- 필수 값은 아니다.
- 예제에서 `github.actor`를 활용해 어떤 사람이 해당 액션을 트리거했는지 정도를 구별할 수 있다.

`on`

- 언제 이 액션을 실행할지를 정의한다.
- 필수 값이다.
- 예제에서는 main 브랜치를 제외한 브랜치에서 push가 발생했을 때 실행하도록 설정하였다.

`jobs`

- 해당 액션에서 수행할 잡을 의미한다. 여러 개를 지정 시에 병렬로 수행한다.
- 필수 값이다.
- `build`
  - build는 예약어가 아니다. 임의로 지정한 이름으로 name과 같은 역할을 한다.
  - jobs의 하위 항목이므로 반드시 들여쓰기해야 한다.
  - jobs에 1개 이상의 작업이 있는데, 그중 하나의 작업이 build라는 것을 의미한다
- `runs-on`
  - 어느 환경에서 해당 작업이 실행될지를 의미한다.
  - 깃허브에서 제공하는 서버를 사용하고 싶다면 ubuntu-latest를 선언한다.
  - 커스텀 러너는 Settings → Actions → Runners에서 추가할 수 있다.
- `steps`
  - 순차적으로 수행할 작업이다.
  - 예제에서는 `actions/checkount@v3`를 사용해서 작업하겠다는 의미이며, actions/checkount@v3는 깃허브에서 제공하는 기본 액션이다. 별도의 파라미터를 제공하지 않으면 해당 브랜치의 마지막 커밋을 기준으로 체크아웃한다. `최신 코드를 기준으로 작동해야 하는 CI 액션에서는 필수적이다.`
  - `actions/setup-noe@v3` 도 역시 기본 액션이며, 해당 러너에 Node.js를 설치한다. 예제에서는 16버전으로 설치하였다.
  - `install dependencies` 는 의존성 설치하느 작업을 수행한다. working-directory는 터미널의 cd 명령어와 비슷한 역할을하는데, 뒤이어 수행할 작업을 해당 디렉터리에서 수행하겠다는 의미다.
  - `name`은 마지막 작업으로 빌드를 수행한다.

<br/>

> 브랜치 보호 규칙

머지하기 전에 꼭 성공해야 하는 액션이 있다면 별도로 저장소에 브랜치 보호 규칙을 추가할 수 있다.

- 해당 저장소의 Settings → Code and automation → Branches로 이동한다.

- Add branch protection rule을 클릭하여 브랜치 보호 규칙을 추가한다.
- 다음과 같은 내용을 체크할 수 있다.

  ```tsx
  // 머지하기 전에 상태 체크를 필수로 한다.
  Require status checks to pass before merging

  // 머지하기 전에 브랜치가 최신 내용인지 확인한다.
  Require branches to be up to date before merging

  // 꼭 실행되어야할 액션 선택
  Status checks that are required
  ```

- 마지막으로 꼭 실행되어야 하는 액션의 파일명을 선택하고 저장하면, 해당 액션이 성공하기 전까지는 main branch에 대한 머지를 막을 수 있다.

<br/>

### 깃허브에서 제공하는 기본 액션

다음은 깃허브에서 제공하는 기본 액션으로 다른 액션을 만들 때 쓰이는 주요 액션이다.

`actions/checkout`

- 깃허브 저장소를 체크아웃 하는 액션이다.
- 저장소를 기반으로 작업을 해야 한다면 반드시 필요하다.
- 일반적으로는 아무런 옵션 없이 사용해 해당 액션을 트리거한 최신 커밋을 불러온다.
- ref를 지정하면 특정 브랜치나 커밋을 체크아웃할 수 있다.

`actions/setup-node`

- Node.js를 설치하는 액션이다.
- 설치할 노드 버전을 지정할 수 있다.

`actions/github-script`

- Github API가 제공하는 기능을 사용할 수 있도록 도와주는 액션이다.
- API를 이용하면 깃허브에서 할 수 있는 대부분의 작업을 수행할 수 있다.

`actions/stale`

- 오래된 이슈나 PR을 자동으로 닫거나 더 이상 커뮤니케이션하지 못하도록 한다.
- 정리할 때 도움된다.

`actions/dependency-review-action`

- 의존성 그래프에 대한 변경이 일어났을 때 실행된다.
- 의존성을 분석해 보안 또는 라이센스에 문제가 있다면 이를 알려준다.
- package.json / package-lock.json 등

`github/codeql-action`

- 깃허브의 코드 분석 솔루션인 code-ql을 활용해 저장소 내 코드의 취약점을 분석한다.
- languages에 javascript만 설정해 두면 타입, 자스 모두 검사한다.
- 특정 스케줄에 맞춰 실행사거나 CI로 활용할 수 있다.

<br/>

### 유용한 깃허브 앱

깃허브 Marketplaces에서는 여러 사용자가 만들어 놓은 액션을 손쉽게 가져다 쓸 수 있도록 운영하고 있다. 아래는 유용한 액션들이다.

`calibreapp/image-actions`

- 저장소에 포함돼 있는 이미지를 최적화하는 액션이다.
- CDN을 아직 구축하지 못한 경우 이미지를 저장소 내부에 두고 함께 관리할 때 사용된다.
- 이미지는 가장 작은 파일로 관리될 필요가 있어 이미지 압축에 도움을 준다.
- PR로 올라온 이미지를 sharp 패키지를 사용해 거의 무손실로 압축해서 다시 커밋해준다.

`lirantal/is-website-vulnerable`

- 특정 웹사이트를 방문해 해당 웹사이트에 라이브러리 취약점이 존재하는지 확인하는 액션이다.
- Synk라는 솔류션을 기반으로 작동한다.
- 개발자의 컴퓨터에서 설치 되고 실제 배포에 포함되지 않은 devDependencies나 번들링 과정에서 트리쉐이킹으로 인해 사라진 코드는 취약점으로 진단되지 않는다.

`Lighthouse CI`

- 라이트하우스를 CI 기반으로 실행할 수 있도록 도와주는 도구다.
- 현재 머지 예정인 웹사이트의 성능 지표를 측정할 수 있다.
- 설정을 통해 일정 점수 미만이 되면 에러를 발생시킬 수 있다.

<br/>

> package.json의 버전 이해

- 기존 버전과 호환되지 않게 API가 변경되면 주 버전을 올린다.
- 기존 버전과 호환되면서 새로운 기능을 추가할 때는 부 버전을 올린다.
- 기존 버전과 호환되면서 버그를 수정한 것이면 수 버전을 올린다.
- react@16.0.0
  - 버전 앞에 아무런 특수 기호가 없다면 정확히 해당 버전에 대해서만 의존하고 있다는 의미다.
- react@^16.0.0
  - 16.0.0과 호환된다는 버전을 의미한다.
  - 16.0.0부터 17.0.0 미만의 모든 버전은 호환된다고 가정한다.
- react@~16.0.0
  - 패치 버전에 대해서만 호환되는 버전을 의미한다.
  - 16.0.0부터 16.1.0 미만의 모든 버전은 호환된다고 가정한다.
- 위 내용 모두 개발자들 간의 약속일 뿐이다

<br/>

> 의존성 차이

- `dependencies`
  - package.json에서 npm install을 실행하면 설치되는 의존성이다.
  - 해당 프로젝트를 실행하는 데 꼭 필요한 패키지가 여기에 선언된다.
- `devDependencies`
  - package.json에서 npm install을 실행하면 설치되는 의존성이다.
  - npm i [packageName] —save-dev를 실행하면 추가된다.
  - 해당 프로젝트를 실행하는 데는 필요하지 않지만 개발 단계에서 필요한 패키지들을 여기에 선언된다.
- `peekDependencies`
  - 주로 서비스보다는 라이브러리와 패키지에서 자주 쓰이는 단위다.
  - 호환성으로 인해 필요한 경우를 의미한다.

<br/>

---

### 깃허브 Dependabot으로 보안 취약점 해결하기

깃허브에서 제공하는 Dependabot은 의존성에 문제가 있다면 이에 대해 문제를 알려주고 가능하다면 해결할 수 있는 풀 리퀘스트까지 열어준다.

깃허브의 Dependabot은 취약점을 `Critical`, `High`, `Moderate`, `Low` 총 4단계로 분류한다.

나머지는 알아서 찾아보자.

<br/>

---

### 도커란?

도커는 애플리케이션을 빠르게 배포할 수 있도록 애플리케이션은 `컨테이너`라는 단위로 패키징한다.이 컨테이너 내부에서 애플리케이션이 실행될 수 있도록 도와준다. 도커는 이 컨테이너를 바탕으로 독립된 환경에서 애플리케이션이 항상 일관되게 실행할 수 있도록 보장해 준다.

### 도커 용어

`이미지`

- 도커에서 이미지란 컨테이너를 만드는 데 사용되는 템플릿을 의미한다.
- 이 이미지를 만들기 위해서는 Dokerfile이 필요하며, 이 파일을 빌드하면 이미지를 만들 수 있다.

`컨테이너`

- 도커의 이미지를 실행한 상태를 컨테이너라고 한다.
- 이 컨테이너가 바로 독립된 공간이다.
- 이미지가 목표하는 운영체제, 파일 시스템, 각종 지원 및 네트워크 등이 할당되어 실행될 수 있는 독립된 공간이 생성된다.

`Dokerfile`

- 어떤 이미지 파일을 만들지 정의하는 파일이다.
- 이 파일을 빌드하면 이미지를 만들 수 있다.
- 흔히 도커 이미지화라고 할 때 가장 먼저 하는 것이 이 Dockerfile 생성이다.

`태그`

- 이미지를 식별할 수 있는 레이블 값을 의미한다.
- 일반적으로 이름 태그명 형태로 구성돼 있다.
- ubuntu:latest
  - ubuntu가 이미지 이름이다.
  - latest는 태그명이다.

`리포지터리`

- 이미지를 모아두는 저장소다.
- 이름에 다양한 태그로 지정된 이미지가 모여있는 저장소다.

`레지스트리`

- 리포지터리에 접근할 수 있게 해주는 서비스다.
- 대표적으로는 도커 허브가 있다.
- 여기에는 다양한 리포지터리가 있으며 사용자가 원하는 이미지를 내려받을 수 있다.

<br/>

### 자주 쓰는 도커 cli 명령어

`docker build`

- Dockerfile을 기준으로 이미지를 빌드하는 작업이다.
- 일반적으로 태그를 부여하는 옵션인 `-t`와 함께 많이 사용된다.

```tsx
// 현재 ./에 있는 Dockerfile을 기준으로 이미지를 빌드하고,
// 해당 이미지명에 foo:bar라는 태그를 붙이는 것을 의미한다.
docker build -t foo:bar ./
```

`docker push`

- 이미지나 리포지터리를 도커 레지스트리에 업로드하는 과정을 의미한다.
- 별도 설정이 돼 있지 않다면 기본적으로 도커 허브에 업로드한다.

```tsx
// yceffort라는 사용자 계정에 foo:bar 이미지를 푸시하는 것을 말한다.
docker push yceffort/foo:bar
```

`docker tag`

- 이미지에 태그를 생성하는 명령어다.
- 이름을 수정하는 게 아니라 기존 이미지에 새로운 태그를 붙인다.
- 동일한 이미지에 두 개의 태그가 생긴 것으로, 하나를 삭제하더라도 동일 이미지의 다른 태그에는 영향을 미치지 않는다.

```tsx
//  아래와 같은 형태로 실행한다.
docker tag [원본이미지:태그] [변경할_이미지:태그]
```

`docker inspect`

- 이미지나 컨테이너 세부 정보를 출력하는 명령어다.

```tsx
docker inspect [이미지명|컨테이너명]
```

`docker run`

- 이미지를 기반으로 새로운 컨테이너를 생성하는 명령어다.

`docker ps`

- 현재 가동 중인 컨테이너 목록을 확인할 수 있는 명령어다.
- `—-all`과 함께 실행한다면 현재 가동 중이지 않은 멈춘 컨테이너도 확인할 수 있다.

`docker rm`

- 컨테이너를 삭제할 수 있는 명령어다.

```tsx
// 해당 컨테이너가 실행중이라면 중지시키고 삭제해야한다.
docker stop [이미지명]

docker rm [이미지명]
```
