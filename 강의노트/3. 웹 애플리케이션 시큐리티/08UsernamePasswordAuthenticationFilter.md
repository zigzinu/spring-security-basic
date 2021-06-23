# UsernamePasswordAuthenticationFilter

- `/login` 페이지에서 sign in 버튼을 눌렀을 때 처리하는 필터.
  - Form 을 보여줄때가 아니다. 
- 인증 정보를 보내면 Authentication Manager 한테 보내서 인증을 시도한다.

```
    return this.getAuthenticationManager().authenticate(authRequest)
```
- `authenticate()`: `ProviderManager.java`에서 여러개의 provider 를 순회하면서 
  - **provider**: 자식이 처리 못하면 부모에게 위임해주는 방식
  - `AnonymouseAuthenticationProvider`: 검사 못하고
  - 부모 쪽에서 인증 성공
- 인증에 성공하면 Security Context Holder 에 넣어준다.