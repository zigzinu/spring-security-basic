# RememberMeAuthenticationFilter

- **크롬**에 **EditThisCookie** 설치하면 쿠키 정보를 확인할 수 있다.
  - JSESSIONID: 세션을 담고잇는 쿠키. 쿠키값을 보내면 서버가 매칭이 되는 세션을 찾아서 사용한다.


- 추가적인 필터
- 로그인할 때, 로그인 기억하기 체크박스.
  - 탭을 닫거나 몇일이 지나도, 세션보다 수명이 긴 쿠키나 토큰 값이 브라우져에 남아있거나, 서버에 저장되어있거나 하는 기능.

```
        http.rememberMe()
                .rememberMeParameter("remember")
                .userDetailsService(accountService)
                .key("remember-me-sample")
                .tokenValiditySeconds()
                .useSecureCookie(true)
                .alwaysRemeber()
```
- `html`에서도 기본 명칭은 `remember-me`
- `tokenValiditySeconds`: 기본값 2주
- `.useSecureCookie(true)`: https만 쿠키에 접근 가능하도록. https 사용할 땐 true 로 해준다.
- `alwaysRemember()`: 기본값 false. 폼에서 Remember 설정 안해도 항상 사용하도록하는것.
## RememberMe 설정 안했을 때

- 쿠키를 지우면 다시 로그인해야 한다.


## RememberMe 설정 안했을 때

- 로그인하면 쿠키가 2개다
  - 1. JSESSIONID
  - 2. remember-me: username, 언제까지 유효한지 정보가 들어있다.
- `JSESSIONID` 쿠키를 지워도 다시 로그인 요청을 안 한다.
  - `FilterChainProxy`잡아서 보면 `RememberMeAuthenticationFilter`가 들어와있다.

## RememberMeAuthenticationFilter

- `SecurityContextHolder`에서 인증된 정보가 있는지 꺼내서 본다. 
  - JSESSIONID 를 삭제했으니깐 없다.
- `rememberMeAuth` 토큰을 확인. 토큰 가지고 `authenticationManger.authenticate`로 인증
  - 이 때 발급되는 토큰은 `UsernameAUthenticationToken`이 아니라 `RememberMeAuthenticationToken`이다.