# FilterSecurityInterceptor

- `AccessDecisionManager`를 어디서 사용하고 있는지?

## FilterSecurityInterceptor
- `AccessDecisionManager`를 사용하여 Access Control 또는 예외 처리를 하는 필터
- Security Filter 중 하나로, 대부분의 경우 `FilterChainProxy`의 마지막 필터로 자리하고 있다.

### AbstractSecurityInterceptor
- 디버그로 `AbstractSecurityInterceptor`에 잡아놨다.

- `FilterSecurityInterceptor`의 부모 클래스
- `this.accessDecisionManager.decide(authenticated, object, attributes)` 에서 access 컨트롤을 하고 있다.
  - 서버의 루트 `"/"`에 익명으로 들어갔을 때
    - `authencitated = AnonymousAuthenticationToken`
    - `object = FilterInvocation`: FilterChain, request, response
    - `attributes = ArrayList`: `"permitAll"`
    - 예외를 던지지 않고 넘어간다
  - 로그인 전 `"/dashboard"`
    - `authencitated = AnonymousAuthenticationToken`: `principal = "anonymousUser"` 
    - `attributes`: `"authenticated"`
    - 실패한다. 예외를 던지게 되어있다.
    - Exception Handling 하는 필터에서 처리하게 된다.
  - 로그인 후 `"/dashboard"`
    - `authencitated = UsernamePasswordAuthenticationToken`: `principal = "User"`, `authorities: "ROLE_USER"`
    - `attributes`: `"authenticated"`
    - 허용한다.