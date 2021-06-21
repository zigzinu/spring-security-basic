# Authentication 과 SecurityContextHolder

- 필터가 authentication을 관리한다.

## Authentication 객체

- 이전에 로그인한 유저가 같은 요청을 보내면 유저 정보를 기억하고 있다.
  - `UsernamePasswordAuthenticationToken@f84c4e49` 동일

## SecurityContextPersistenceFilter

- 어플리케이션을 새로 띄우고 Account를 만들었다.
  - `contextBeforeChainExecution = Null authentication`
- `/dashboard`에 접근
  - `Null authentication`
  - 아직 로그인 하기 전
- 계속 읽어오려는 시도를 하고 contextChain 이 끝나면 Context 를 비워준다.
- 로그인을 시도한다
  - `UsernamePasswordAuthenticationFilter`에 걸린다.

## UsernamePasswordAuthenticationFilter

- 폼 인증을 처리하는 시큐리티 필터
- 로그인을 하면 `AuthenticationManager`를 사용한다.
- 인증을 마치면 parent인 `AbstractAuthenticationProcessingFilter`로 간다.
  - 리턴한 객체 `authResult`
  - `successfulAuthentication()` 함수를 호출한다.
    - ContextHolder의 `setAuthentication(authResult)`를 호출한다.

## 다시 SecurityContextPersistenceFilter

- `repo.saveContext()`을 통해 세션에 저장한다.
  **- SecurityContext를 HTTP 세션에 캐시(기본 전략)하여 여러 요청에서 Authentication을 공유할 수 있는 필터**
    - HTTP session이 아닌 다른 곳에서도 저장 가능하다.

- 원래 들어왔던 `/dashboard` 위치로 redirect 한다.
  - `SecurityContextPersistenceFilter`의 `doFilter`함수에서 `req` 변수를 보면 `coyoteRequest="R{/dashboard}"`
- 다시 SecurityContextPersistenceFilter 잡는다.
  - `HttpRequestResponseHolder`를 통해 repository 에서 context 를 가져온다.
    - `HttpSessionSecurityContextRepository`
    - Http 세션에 저장되어있던 authentication 을 가져온다.
- 그리고 다시 `SecurityContextHolder.setContext(contextBeforeChainExecution)` 에 넣어준다.
  **- 매번 비우고 넣어주는 것을 확인**

### 결론 
- HTTP 세션 정보가 바뀌면 인증 정보가 날아간다