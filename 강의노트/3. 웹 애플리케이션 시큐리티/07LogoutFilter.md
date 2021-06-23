# 로그아웃 처리 필터 : LogoutFilter

- `LogoutHandler`
  - 다른 여러 LogoutHandler 를 감싸고 있는 Composite 객체이다.
  - `SimpleUrlLogoutSuccessHandler`: 기본 핸들러 명시된 위치로 Redirect 해주는 핸들러
- `LogoutSuccessHandler` 
  - 로그아웃 처리가 되고난 뒤에 어떻게 할 것인지? Redirect 할 것인지 결정하는 핸들러
- 로그아웃 페이지는 `DefaultLogoutPageGeneratingFilter`가 제공하는 것. 여기가 아니다.

**LogoutFilter.java**
```
    if(requiresLogout(request, respose)) 
```
- 여기에 걸려야 Logout 처리한다.

## 로그아웃 처리 중

- `CompositeLogoutHandler`: 데이터 핸들러 중 하나.
- `logout()` 메소드에는 `LogoutHandler`들이 있다.
  - `CsrfLogoutHandler`: 
  - `SecurityContextLogoutHandler`

## 로그아웃 했을 때 가는 페이지 설정
```
    http.logout()
        .logoutUrl("/logout")
        .logoutSuccessUrl("/")
        .addLogoutHandler()
        .logoutSuccessHandler()
        .invalidateHttpSession(true)
        .deleteCookies("")
```
- `.logoutUrl`: 로그아웃 처리를 담당하는 Url 설정. Post 요청이다. 로그아웃 페이지에서 요청해야하는 것.
  - 이것을 일단 임의로 수정하면 기본 제공되는 로그아웃 페이지로 로그아웃 요청이 안된다. `/my/logout`
- ` .logoutSuccessUrl`: 로그아웃 성고하면 기본적으로 가는 페이지
- `.addLogoutHandler()`: 추가적인 핸들러
- `.invalidateHttpSession(true)`: 로그아웃 하고 http 세션을 invalidate 할 것인지? 기본값 true
- `.deleteCookies()`: 쿠기 기반의 인증 방식을 사용한다면 해당 쿠키 이름을 설정해준다.