# Test

### @RunWith(SpringRunner.class)

- Juint 4 기반의 테스트

### @Test 메소드

```
    @Test
    public void index_user() throws Exception() {
        mockMvc.perform(get("/").with(user("user").roles("USER")))
                .andDo(print())
                .andExpect(status().isOk());
    }
```
- `user` 라는 유저가 이미 있다고 가정하고 (모킹) 테스트를 진행한다.
  - 데이터베이스에 이런 유저가 있는 것이 아니다.
- `with(anonymous())` 도 가능

## 어노테이션

- `@WithAnonymousUser`, `@WithMockUser` 으로 유저를 코딩하지 않아도 된다.

## 커스텀 어노테이션 생성
```
@Retention(RetentionPolicy.RUNTIME)
@WithMockUser(username = "keesun", roles = "USER")
public @interface WithUser {
}
```
  - 새로운 ` @interface` 생성
  - `Retention(RetentionPolicy.RUNTIME)`: 어노테이션을 런타임에도 참고할 것
  - 중복적인 코드 정리 

## 함수명

- `index_user()`: index 페이지에 user 가 접근

## 응답

- `isForbidden()`: 인증 권한이 틀렸을 때. (`admin_user()`)

## Form 로그인 테스트

```
    mockMvc.perform(formLogin().user(user.getUsername()).password(password))
            .andExpect(authenticated());
```
- `authenticated()`, `unauthenticated()`
- `SecurityContext`에서 꺼내서 확인하는 함수.
- `@Transactional`
  - Javax 기본과 Springboot에서 제공하는 것 둘다 사용해도 되나, 스프링부트에서 더 많은 기능이 있어서 선택
  - 각각의 테스트가 끝나고 롤백하는 어노테이션