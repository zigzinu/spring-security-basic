# sec 네임스페이스

- Thymeleaf 의 좀 더 편리한 뷰에서 시큐리티 정보를 확인하는 방법

```
<html lang="en" xmlns:th="http://www.thymeleaf.org" xmlns:sec="http://www.thymeleaf.prg/extras/spring-security">
```
- 네임스페이스를 등록하는 과정만 하면 된다.


**기존 코드**
```
    <div th:if="${#authorization.expr('isAuthenticated()')}">
        <h2 th:text="${#authentication.name}">Name</h2>
        <a href="/logout" th:href="@{/logout}">Logout</a>
    </div>
    <div th:unless="${#authorization.expr('isAuthenticated()')}">
        <a href="/login" th:href="@{/login}">Login</a>
    </div>
```

**새로운 코드**
```
    <div sec:authorize-expr="isAuthenticated()">
        <h2 sec:authentication="name">Name</h2>
        <a href="/logout" th:href="@{/logout}">Logout</a>
    </div>
    <div sec:authorize-expr="!isAuthenticated()">
        <a href="/login" th:href="@{/login}">Login</a>
    </div>
```
- `sec:` 이후에 자동완성도 되고
- `authorize-expr`: 안에도 자동 완성이 되는데, intelliJ ultimate 버젼이라 가능할 수도있음.
- `isAuthenticated()`이 외에 `hasAnyRole(roles)`, `hasRole()`, `isAnonymous()`, `isRememberMe()` 등등 사용할 수 있다.
- `sec:authentication="name"`이 외에 `"authorities"` 등 참조할 수 있다.
  - 기존의 `#authentication.`뒤에 자동 완성에 대해 힌트를 얻을 수 있다.