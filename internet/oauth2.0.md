# Oauth2.0
- 인증을 위한 개방형 표준 프로토콜
- 인터넷 사용자들이 비밀번호를 제공하지 않고, 다른 웹사이트 상의 자신들의 정보에 대해
웹 사이트나 애플리케이션의 접근 권한을 부여할 수 있는 공통적인 수단으로서 사용되는, 접근 위임을 위한 개방형 표준이다.

### 용어
- Resource Owner : 애플리케이션 서비스를 사용하는 유저를 말한다.
- Client : 애플리케이션 서비스를 말한다.
- Resource Server : Goole, Facebook, Twitter등 Oauth를 통해 접근을 지원하는 제공자를 말한다.

### 과정

우선 client는 resource serve에 등록하는 과정을 거친다. 이 과정을 통해 client id와 client secret을 얻을 수 있다.

`Resource Owner`와 `Client`
`Client`는 `Resource Owner`에게 `Oauth Serivce`의 로그인 화면으로 이동하게 합니다.
`Resource Owner`는 해당 `Oauth Serivce`의 ID, PW로 로그인 합니다.
`Resource Owner` 해당 정보를 허용하는 화면을 보여준다.
`Resource Server`는 `인가 코드`를 등록한 Redirect Url에게 `Client`에게 전달 해 준다.


내가 진행했던 프로젝트에서는 프론트엔드에서 인가코드까지 받은 후 이것을 우리 서버로 전달한다.

우리 서버에서는 해당 `인가코드`를 통해 `access token`을 얻는다. 
`access token`을 통해 Resource Owner정보(ex. email)에 접근이 가능하다.

---

로그인은,
`access token`을 통해 얻어 온 email이 우리서비스의 DB에 등록되어 있는지 확인하고,
있다면 JWT 토큰을 발행한다.



