# ExceptionTranslationFilter

- `FilterSecurityInterceptor`의 상위 클래스인 `AbstractSecurityInterceptor`에서 발생하는 2가지 예외를 처리하는 필터
  1. `AccessDeniedException`
  2. `AuthenticationException`

1. `AccessDeniedException`
  - 발생 시 `AuthenticationEntryPoint` 실행
  - 인증이 될 때까지 인증을 시도

2. `AuthenticationException`
  - 인증은 됐는데 이 예외가 발생하면
  - 익명 사용자라면 `AuthenticationEntryPoint`실행 (로그인 페이지 요청)
  - 익명 사용자가 아니라면 `AccessDeniedHandler`에게 위임

## ExceptionTranslationFilter

- `/dashbard`요청을 했음.
- `exception instanceof AuthenticationException`에 버그 걸어놨음.
- Authentication이 아니라 `exception instanceof AccessDeniedException`에서 처리할 것으로 예상.
  - `isAnonymouse` 라면 로그인 페이지로 이동
  - 아니라면 적당한 메시지 출력 `"Forbidden"` Whitelabel Error Page


## 로그인 정보에서 발생하는 인증 에러는?

- `ExceptionTranslationFilter` 에서 처리하는 것이 아니다.
- `UsernamePasswordAuthenticationFilter` 에서 처리한다.
  - 상위 클래스 `AbstractAuthenticationProcessingFilter`
  - `unsuccessfulAuthentication()`
    - 세션에 에러를 담아두는 일을 한다. `SimpleUrlAuthenticationFailureHandler` `saveException()`\
  - `loginpageGeneratorFilter` 가 로그인 페이지를 다시 보여준다. 에러와 함께