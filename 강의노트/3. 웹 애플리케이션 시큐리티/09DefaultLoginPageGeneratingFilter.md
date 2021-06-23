# DefaultLogin/LogoutPageGeneratingFilter

- 간단한 설정 가능

```
    http.formLogin()
            .usernameParameter("my-username")
            .passwordParameter("my-password")
            .failureForwardUrl() 
            .successForwardUrl() // 이건 잘 쓰지 않는다. 원래 페이지로 가려고 
``` 
- `paramter` 값을 바꾸는이유: 너무 알려진 값을 사용하지 않기 위함
  - 커스텀 로그인 페이지에서도 똑같이 맞춰줘야함.
- `ForwardUrl`: 이동하는 url 설정

## 커스텀 로그인 뷰

```
    http.formLogin()
            .loginPage("/login")
            .permitAll()
```
- 이 Url 로 들어오는 `GET`: view 만 보여주게 된다.
- 이 Url 에 대한 `POST` 요청은 `UsernamePasswordAuthenticationFilter`가 처리해주기 때문에 구현할 필요 없다.
- 이렇게 하면 기본 필터(두 가지 `GeneratingFilter`가 등록이 안된다.
- `permitAll`: 없으면 요청 거부
- csrf 도 들어와있다.


### 필터가 뜨는지 확인 ?

**FilterChainProxy.java**
```
    List<Filter> filters = getFilters(fwRequest)
```
- `UsernamePasswordAuthenticationFilter` 다음에 `DefaultPageGenerating` 필터들이 없어진다.

### 로그인 실패했을 떄 에러를 보여준다.

```
    <div th:if="${param.error}">
        Invalid username or password.
    </div>
```
- 에러가 있었다면 페이지를 에너난 파라미터와 함께 다시 보여준다.

## 커스텀 로그아웃 뷰```
```
    http.formLogout()
            .logoutSuccessUrl("/")
```
- 버튼만 만들면 된다.