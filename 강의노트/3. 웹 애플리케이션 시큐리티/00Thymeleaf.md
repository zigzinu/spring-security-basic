# Thymeleaf 문법

```
    <form action="/signup" th:action="@{/signup}" th:object="${account}" method="post">
        <p>Username: <input type="text" th:field="*{username}" /></p>
```
- `action` 값을 `th:action` 값으로 바꾸어줌
- `@{/signup}`: Url 전용 키워드. Context Root Path 에 따라서 자동으로 제대로 만들기 위함. Context Root Path 가 바뀌어도 쉽게 적용.
- `th:object`: 컨트롤러에서 넘겨준 object를 폼에서 참조
- `*{username}`: object 기준으로 field 명시