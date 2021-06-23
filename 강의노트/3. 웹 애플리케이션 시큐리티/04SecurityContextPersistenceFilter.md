# SecurityContext 영속화 필터: SecurityContextPersistenceFilter

- 여러 요청 간에 SecurityContext 공유.
  - 원래 요청했던 페이지로 로그인 했다면, 다른 요청으로 넘어가도 Security 정보를 열람하여 접근하게 해줌.
- SecurityContextRepository 를 이용해서 기존의 SecurityContext를 읽어오거나 초기화한다.

```
public class HttpSessionSecurityContextRepository implements SecurityContextRepository {
}
```
- `SecurityContextRepository` 인터페이스의 기본 구현체는 Http 세션 기반이다.
- Http 세션에서 읽어오는 것.
- Spring-session 과 연동해서 세션 클러스터를 구현할 수 있다.

## SecurityContextPersistenceFilter

- 모든 Filter 보다 위에 있어야 한다. 또 로그인 요청을 하지 않아도 되겠금.
- 커스텀한 인증 필터를 만들고싶다면 이 필터 뒤에 위치해야 한다.