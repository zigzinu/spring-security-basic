# Thymeleaf Spring Security 확장팩

- 뷰에서 Security 관련된 정보(Authentication, Authorization)를 참조하고 싶을 때.
- `org.thymeleaf.extras:thymeleaf-extras-springsecurity5`
- 스프링부트를 쓰고있다면 해야하는 설정은 없다.
  - 스프링을 쓰고있다면 빈 설정을 좀 해줘야 한다.

## 메인 페이지에 로그인 버튼 추가

- 방법 1. Controller 핸들러에서 `principal`에 대한 정보를 따라서 `model`에 정보를 보내고 이걸 참조해서 뷰에 나타낼 수 있다.
- 방법 2.

```
    <div th:if="${#authorization.expr('isAuthenticated()')}">
        <h2 th:text="${#authentication.name}">Name</h2>
        <a href="/logout" th:href="@{/logout}">Logout</a>
    </div>
    <div th:unless="${#authorization.expr('isAuthenticated()')}">
        <a href="/login" th:href="@{/login}">Login</a>
    </div>
```
- `#authentication`, `#authorization` 제공
- `th:if="${#authorization.expr('isAuthenticated()')}"`: 인증이 된 사용자일 경우에
- `th:text="${#authentication.name}"`: 인증된 사용자 이름
- `@{/logout}`: 한번 감싸주면 Servlet Context Path 가 바뀌어도 따로 안바꿔도 된다.
- `th:unless`: 조건에 해당하지 않는 경우

- 문제: `#authentication`은 툴의 지원을 받아서 입력하는게 수월하다.
- 그러나 `#authorization`은 `expr()`을 사용해야 하는데 타입오류를 실행해봐야 알수가 있다.