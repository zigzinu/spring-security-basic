# AuthenticationManger 와 authentication 관계

- Authencation 을 만들고 인증을 처리함
- `authenticate()` 메소드 하나 밖에 없음
  - 인증 정보가 유효하다면 `Principal` 객체를 반환한다

## ProviderManager

- 구현체로 가장 많이 사용함
- 여기에도 `authenticate` 함수에서 디버그 찍어서 살펴본다
  - `authentication`: `UsernamePasswordAuthenticationToken`
    - `principal = "user"`
    - `credentials = "123"`
    - `authorities = Collections`
    - `details = WebAuthenticationDetails`
    - `authenticated = false`
- 인증을 `provider` 여러개를 사용해서 한다. ProviderManager가 직접하는 것이 아니라 위임한다.
  - `this.providers = AnonymousAuthenticationProvider`
    - 이 provider 객체는 `UsernamePasswordAuthenticationToken` 객체를 처리할 수 없다.
    - 이 경우에는 parent 의 ProviderManager 한테 가서 요청을 한다.
      - 여기에는 `DaoAuthenticationProvider`가 들어가있다.
      - `UsernamePasswordAuthenticationToken`를 처리할 수 있다.
      - 여기서 또 들어가는 Provider 는 `AbstractUserDetailsAuthenticationProvider`이다.
      - 여기서 호출하는 `retrieveUser` 메소드는 `DaoAuthenticationProvider` 이다.
      - **여기서 `getUserDetailsService()`가 반환하는 객체가 우리가 구현한 `AccountService`이다.**
  - `provider.authenticate(authentication)`이 반환하는 객체는 `UsernamePasswordAuthenticationToken`인데
    - `principal = User`
      - 원래 스트링이었는데 User 객체로 바뀌었다.
- 인증이 완료되면 **ContextHolder**에 `authentication` 객체를 넣어준다.