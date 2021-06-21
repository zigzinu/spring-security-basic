# UserDetailsService

- 스프링 시큐리티에서 authentication 을 관리할 때, 인메모리가 아닌 DAO (데이터베이스)를 통해 사용자 정보를 가져올 때 사용
- 인증을 하는 객체가 아니다
- 어떤 Database 타입에 구현될 것인지는 자유다.
  - 임의의 DAO 구현체: `@Autowired AccountRepository accountRepository`

### public UserDetails loadUserByUsername(String username) throws UsernameNotFoundException
- 구현은 자유이나, `UserDetails` 객체를 반환해야 한다.
  - `User` 클래스를 활용하여 `Account` 에서 `UserDetails` 로 변환한다.
  - `User` 클래스가 없었던 시절에는 어댑터 클래스를 하나 만들어줬었다.
- 없을 때는 `UsernameNotFoundException`

## 명시적으로 AccountService 를 쓰게 하는 방법

- 이렇게 하지 않아도 `UserDetailsService` 타입의 Bean이 Bean으로 등록만 되어있어도 가져다가 쓰게 된다.
- 패스워드 인코더도 마찬가지의 방법이다.

**SecurityConfig.java**
```
    @Autowired AccountService accountService;

    @Override 
    protected void configure(AuthenticationManagerBuilder auth) throws Exception {
        auth.userDetailsService(accountService);
    }
```
- `AuthenticationManagerBuilder`에게 우리가 사용할 `UserDetailsService`를 지정해 주는 것.