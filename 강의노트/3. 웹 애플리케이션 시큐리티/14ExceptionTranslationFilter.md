# ExceptionTranslationFilter

- `FilterSecurityInterceptor` 와 밀접한 관계.
- `ExceptionTranslationFilter`가 `FilterSecurityInterceptor`를 감싸고 실행되어야 한다.
- `ExceptionTranslationFilter`가 try catch 문으로 감싸고 `FilterSecurityInterceptor` 가 그다음 처리를 한다.
- `FilterSecurityInterceptor` 가 `AccessDecisionManager` (그 기본 구현체 `AffirmativeBased`)를 사용해서 인가를 처리한다.
  - 이 떄 2가지 에러 발생 가능.
  - 1. `AuthenticationException`: 인증 자체가 안되어있다.
  - 2. `AccessDeniedException`: 인증은 되어있는데 권한이 충분하지가 않다.
- `ExceptionTranslationFilter`는 예외에 따라서 각기 다른 처리를 한다.
  - 1. `AuthenticationException`: `AuthenticationEntryPoint`를 사용해서 인증이 가능한 페이지로 보낸다.
  - 2. `AccessDeniedException`: `AccessDeniedHandler`로 처리. 기본 처리는 `403 Forbidden`에러 메시지. 스프링부트의 기본 에러 페이지로 보여짐.

## AccessDenied 페이지 커스터마이징

```
    http.exceptionHandling()
            .accessDeniedPage("/access-denied");
```
- 연결 페이지만 설정

```
    http.exceptionHandling()
            .accessDeniedHandler(new AccessDeniedHandler() {
                @Override
                public void handle(HttpServletRequest request, HttpServletReponse response, AccessDeniedExcpetion accessDeniedException) {
                    UserDetails principal = (UserDetails) SecurityContextHolder.getContext().getAuthentication().getPrincipal();
                    String username = principal.getUsername();
                    System.out.println(username + " is denied to access " + request.getRequestURI()); // 로거를 쓰는게 나음
                    response.sendRedirect("/access-denied");
                }
            });
```
- `accessDeniedHandler()`: 서버단에 로그도 만들고 좀 더 많은 일을 하고싶을 때.
- 별도의 클래스로 빼고 단위 테스트도 만드는게 좋다.

```
    @GetMapping("/access-denied")
    public String accessDenied(Principal principal, Model model) {
        model.addAttribute("name", principal.getName());
        return "access-denied";
    }
```
- `principal`: 현재 누구인지