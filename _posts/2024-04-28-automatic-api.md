---
title: "Swagger를 이용한 세상 간단한 API Mocking"
author:
  name: 박 찬영
  link: https://github.com/univdev
date: 2024-04-28 22:56:00 +0900
categories: [Tech, Talk]
tags: [Swagger, API, Mocking]
image:
  path: /assets/post_images/2024-04-28-automatic-api/2024-04-28-23-25-36.png
  alt: "Swagger를 이용한 세상 간단한 API Mocking"
---
![Swagger를 이용한 세상 간단한 API Mocking](/assets/post_images/2024-04-28-automatic-api/2024-04-28-23-25-36.png)

# API Mocking

프론트엔드와 백엔드의 관계에서 대부분의 경우 프론트엔드가 백엔드에 의존성을 띄고 있습니다.<br>
프로젝트를 수행함에 있어서 프론트엔드가 API를 연동하려면 백엔드의 개발이 모두 끝나야 진행할 수 있게 되기 때문입니다.

API의 개발이 모두 완료 되기 전 까지 대기할 필요가 없어지도록 가짜 API 서버를 개발하여 사용하는 방식이 만들어졌는데, 이를 API Mocking이라고 합니다.

# 기존 작업 순서

백엔드에 대한 프론트엔드의 의존성을 줄이기 위한 방법은 대부분 다음과 같습니다.

1. 백엔드는 프로젝트 투입 당시 예상 되는 API의 스펙을 결정합니다.
2. 이 과정에서 Payload, Response Interface를 확정합니다.
3. 이 사실을 프론트엔드에게 알립니다.
4. 프론트엔드는 임시 API 서버를 구축하여 백엔드가 결정한 Request, Response 스펙으로 가짜 데이터가 전달 되도록 간단하게 API를 구현합니다.
5. 프론트엔드는 가짜 API 서버에서 응답값을 받도록 화면을 구현합니다.
6. 백엔드의 API 개발이 끝났다면 프론트엔드는 가짜 API 서버 경로를 진짜 API 서버로 치환합니다.
7. Interface를 사전에 조율 하였으니 응답 데이터에 대한 문제가 발생하지 않고 자연스럽게 연결이 됩니다.

# 위 방법에 대한 문제점

이 과정에서의 문제는 다음과 같습니다.

- 백엔드의 Interface를 코드로 옮겨야 하는 불편함
- 임시 API라고는 하지만 API 서버를 프론트도 개발해야 한다는 비효율

심지어 계획 단계에서 모든 것을 예상할 수가 없기 때문에 백엔드의 API Interface는 주기적으로 변경 될 수 있으며, 계획이 수정 되면 백엔드는 프론트엔드에게 통보해야 하고 프론트엔드는 수정 된 내용으로 초기 작성했던 Interface와 서버 코드를 수정해야 하는 번거로움이 동반됩니다.

# 해결 방법

<aside>
⚠️ Swagger 문서를 작성하는 방법에 대해서는 기술하지 않습니다.

</aside>

## Swagger를 통한 자동화

API를 개발하는 과정에서 프론트엔드가 언제든지 API의 인터페이스를 이해하고 실행 해볼 수 있도록 하기 위해 Swagger 문서를 작성하게 됩니다.

Swagger 문서는 json 파일로 추출할 수 있는데, 이 json 파일을 이용하여 프론트엔드에서 API를 실행해볼 수 있는 클라이언트, 가짜 응답값을 전달할 수 있는 가짜 API 서버를 만들 수 있습니다.

## Swagger UI에서 json 추출하기

![Swagger UI](/assets/post_images/2024-04-28-automatic-api/2024-04-28-23-22-26.png)

만약 Swagger UI 문서가 구축 되었고, 내부 interface가 작성 된 상태라면 UI 페이지에 접근했을 때 위 사진과 같은 json 파일에 대한 링크가 표시 됩니다.

해당 링크에 접근하면 Swagger UI 문서를 이루고 있는 모든 데이터를 json 파일로 내려 받을 수 있습니다.

## JSON 파일로 HTTP Client 만들기

### HTTP Client

여기서 말하는 HTTP Client는 프론트엔드에서 백엔드에 요청을 보낼 때 사용하는 `fetch API`를 커스터마이징한 코드를 얘기합니다.

일반적인 경우 단순히 백엔드에 요청을 보내는 경우라면 `fetch('api_path', { params: ... })`  와 같은 방식으로 요청을 보내게 됩니다.

### HTTP Client의 장점

**타입 추론**

위와 같은 방법을 사용한다면 API 서버의 Path나 Payload, Response 데이터의 구조를 IDE 내에서 직관적으로 파악하기 어렵기 때문에 typescript를 사용하고 있다면 다소 아쉬운 부분이 있습니다.

하지만 Swagger를 이용하여 HTTP Client를 구축한다면 다음과 같이 사용할 수 있습니다.

{% raw %}
```tsx
api.auth.authControllerLogin({
  email: 'chanyoung@example.com',
  password: '12345678',
});
```
{% endraw %}

만약 API 서버 내부에 `/auth/login` 경로의 API가 존재하고 있다면 HTTP Client는 Path의 Segment를 기준으로 Depth가 있는 객체를 생성하게 됩니다.

그리고 그 객체 하위에는 실제 API를 요청할 수 있는 함수가 존재합니다.

해당 함수에는 Swagger에서 명시해 둔 요청, 응답 값에 대한 interface가 정의 되어 있기 때문에 typescript를 사용하고 있다면 요청, 응답 값에 대한 타입 추론이 가능합니다.

**fetch API에 대한 자동 확장**

Swagger로 인해 생성 된 HTTP Client는 사용자가 서비스에 녹여내기에 부족함이 없는 기능을 제공합니다.

생성 된 HTTPClient 구조의 일부를 보면 아래와 같습니다.
{% raw %}
```tsx
export class HttpClient<SecurityDataType = unknown> {
  public baseUrl: string = "http://localhost:3000/";
  private securityData: SecurityDataType | null = null;
  private securityWorker?: ApiConfig<SecurityDataType>["securityWorker"];
  private abortControllers = new Map<CancelToken, AbortController>();
  private customFetch = (...fetchParams: Parameters<typeof fetch>) => fetch(...fetchParams);
```
{% endraw %}

Javascript에서 사용하는 `fetch` API를 사용하는데, `fetch` 는 사용하다보면 부족한 기능이 있습니다.

- BaseURL 설정 기능 제공 안함
- 200 ~ 299 외 응답에 대해 Exception 처리가 되지 않음

이러한 기능을 구현하려면 개발자의 작업이 추가적으로 필요한데, HTTP Client는 이러한 기능에 대한 처리가 이미 되어 있어서 Mocking 뿐 아니라 실제 서버와 연동할 때 사용함에도 부족함이 없습니다.


### 생성 방법

<aside>
⚠️ swagger.json 파일이 이미 있다고 간주합니다.

</aside>

생성 방법은 매우 간단합니다.

{% raw %}
```bash
npx swagger-typescript-api -p path/to/your/swagger.json -o path/to/output/dir --name index.ts
```
{% endraw %}

- `path/to/your/swagger.json` 은 swagger.json 파일이 저장 된 경로로 수정해야 합니다.
- `path/to/output/dir`은 `index.ts` 파일이 생성 될 디렉토리 경로 입니다.
- `index.ts`는 HTTP Client 파일입니다. 파일명을 수정하고 싶으시면 해당 옵션을 수정해야 합니다.

위 코드를 실행하면 `swagger-typescript-api` 패키지가 `swagger.json` 을 읽고 지정해 둔 경로에 `index.ts` 파일을 생성하게 됩니다.

## Mock API 서버 구축 방법

<aside>
⚠️ swagger.json 파일이 이미 있다고 간주합니다.

</aside>

1. API 서버를 구축하기 위해 필요한 의존성 패키지를 다운 받아야 합니다.

    ```bash
    yarn add @stoplight/prism-cli
    ```

2. `package.json`에 script를 추가합니다.

    {% raw %}
    ```bash
    "scripts": {
      "run:server": "prism mock --host 0.0.0.0 -p 15002 -d swagger.json"
    }
    ```
    {% endraw %}

    - `host`는 서버의 IP입니다.
        - `0.0.0.0`을 기입하면 일반적인 루프백 아이피와 동일하게 작동합니다.
    - `-p 15002`는 포트 번호입니다.
    - `-d swagger.json`은 swagger.json의 위치입니다.
        - 만약 다른 폴더에 존재한다면 `swagger.json`값만 수정하여 `swagger.json` 파일의 경로를 정확히 지정해야 합니다.
3. `http://localhost:15002` 주소로 API 서버에 대한 구축이 완료 됩니다.

### Mock 서버와 HTTP Client 연동하기

이 방법 또한 매우 간단합니다.

HTTP Client 파일 내부의 `baseUrl` 프로퍼티의 경로를 `http://localhost:15002`로 수정하시면 별도의 추가적인 설정 없이 즉각적으로 요청을 보낼 수 있습니다.
