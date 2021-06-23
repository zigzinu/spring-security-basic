# AnonymouseAuthenticationFilter

- 아무도 인증하지 않는 요청일 경우 Security Context Holder 에 anonymousAuthentication 객체를 만들어서 넣어줌.


```
    http.anonymouse().principal("anonymousUser")
```
- Principal 설정 가능
- 기본값 `principal="anonymousUser"`, `authorities=AuthorityUtils.createAuthorityList("ROLE_ANONYMOUS")`