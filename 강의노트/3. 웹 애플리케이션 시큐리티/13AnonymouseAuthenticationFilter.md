# AnonymouseAuthenticationFilter

- 아무도 인증하지 않는 요청일 경우 Security Context Holder 에 anonymousAuthentication 객체를 만들어서 넣어줌.
- 커스터마이징할 다른 이유는 없다.

```
    http.anonymouse().principal("anonymousUser")
```
- Principal 설정 가능
- 기본값 `principal="anonymousUser"`, `authorities=AuthorityUtils.createAuthorityList("ROLE_ANONYMOUS")`

## Null Object Pattern

- 코드에서 Null 체크를 하는 것이 아니라 null 을 대변하는 객체를 만들어서 사용.