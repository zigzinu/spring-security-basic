# Basic 인증 처리 필터: BasicAuthenticationFilter

- 헤더에 `Authorization: Basic BASE64(username:password)`를 모든 요청에 실어 보내는 것.
- Sniffing 이 된다면 인증 정보가 누출되기 때문에 https 를 사용하도록 한다.

```
    http.httpBasic()
```

```
$ curl -u user:123 http://localhost:8080
```
- `curl` 툴이 `-u` 옵션으로 적으면 BasicAuthentication 헤더를 만들어서 요청을 보낸다.
  - 인증이 필요 없는 루트 `/` 에다가 user/123 이라는 정보 전달 가능
- 잘못된 인증 정보를 주면 응답을 받을 수 없다.
- 인증을 필요로하는 url 은 `401` 에러를 준다.
  - **인증이 필요한 url 도 정보 전달로 인증 가능!**
- authentication 을 똑같이 만든다.
- Form (`UsernamePasswordAuthenticationFilter`) 과 읽어오는 방식의 차이다.

## Stateless 
- Form 과는 다르게 읽어온 후 SecurityContextRepository 에 저장하는 절차가 없다. 
  - 한번 인증해도 다음번 요청에 정보를 보내야 한다.
- `rememberme` 옵션으로 저장하게 할수도있다.