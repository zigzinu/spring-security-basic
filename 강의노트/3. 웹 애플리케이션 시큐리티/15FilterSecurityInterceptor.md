# FilterSecurityInterceptor
- `http.hasRole()` 등으로 `AccessDecisionManager`에게 필요한 인가 정보를 알려줬다.
- `mvcMatcher`: 스프링 mvc 랑 매칭하는지를 위해 사용하고, `antMatcher`로 fallback 도 된다.
- `.hasAuthority("ROLE_USER")`: `hasRole`보다 좀 더 general 한 것. `ROLE_` 을 쓰지 안으려고 hasRole쓴다.
  - `User.builder().authority("ROLE_USER")` 도 마찬가지
- `anonymouse()`: 익명 사용자에게만 접근을 허용.
- `authenticated()`: 권한에 상관 없이 인증만 되면 됨.
- `rememberMe()`: 이 기능으로 인증을 한 사용자의 경우에만 접근을 허용
- `fullyAuthenticated()`: `rememberMe`로 로그인한 사용자에게 다시 로그인을 요구한다.
- `denyAll()`: 아무도 허용하지 않겠다.
- `not()`: 반대