# SecurityConfig.java

## Override

- 설정 클래스는 Override 방식으로 코딩을 한다. 
  - 검색 후 메소드 선택

```
    @Override
    protected void configure(HttpSecurity http) throws Exception {
        super.configure(http);
    }
```

## 메서드 체이닝

- 꼭 하지 않아도 됨
- example

```
        http.authorizeRequests()
                .mvcMatchers("/", "/info", "/account/**", "/signup").permitAll()
                .mvcMatchers("/admin").hasRole("ADMIN")
                .anyRequest().authenticated() 
                .and()
            .formLogin() // 인증이 필요할 때 리다이렉팅하는 곳
```

### http.formLogin(), httpBasic()
- 2 가지 인증 방식
- 인증이 안된 요청은 `formLogin()` 쪽으로 리다이렉션
  - 스프링에서 기본으로 제공되는 로그인 페이지
  - `/logout`: 로그아웃도 동시에 지원됨
- httpBasic
  - 헤더에 특정한 값을 넣어서 요청을 보내면 서버에서는 헤더에 약속된 값을 설정
  - 요청으로부터 유저 네임/패스워드를 알아낼 수 있어서 위험하다. https 를 사용해야 한다.
    - Form 도 마찬가지
    - Https 를 권장한다

## 인메모리 유저 추가

- 기본 설정으로 구동했을 때, 자동 생성된 password는 `UserDetailsServiceAutoConfiguration` 클래스로부터 온 것 확인.
- 클래스를 열람해보면, `inMemoryUserDetailsManager` 함수에서 유저 정보는 `SecurityProperties` 에서 오는 것 확인.
- `SecurityProperties` 클래스에 name, password, role 설정 방법 확인.

1. 방법 1
- `application.properties` 에
```
spring.security.user.name=admin
spring.security.user.password=123
spring.security.user.roles=ADMIN
```
- ADMIN 권한으로 admin 페이지 접근 가능

2. 방법 2
- 오버라이드 
```
@Override
protected void configure(AuthenticationManagerBuilder auth) throws Exception {
    auth.inMemoryAuthentication()
            .withUser("user").password("{noop}user").roles("USER").and()
            .withUser("admin").password("{noop}123").roles("ADMIN");
}
```
- `{noop}`: 암호화하지 않겠다는 암호화 방식 설정
- 입력받을 때도 `{noop}`이 추가되어야 하기 때문에, `Account.encodePassword()` 메소드 구현

## PasswordEncoder
- `{noop}` 없애는 방법
- 스프링 5 이전에는 `NoOpPasswordEncoder`를 기본 인코더로 설정했었는데, 스프링 5 이후로부터는
`PasswordEncoderFactories.createDelegatingPasswordEncoder()`가 기본이다.
  - 해싱 알고리즘을 설정할 수 있다.
  - `{bcrypt}`를 사용한다.

1. `PasswordEncoder`를 빈으로 등록한다.
2. `AccountService`에서 주입받아서 `Account`객체가 사용할 수 있도록 메소드의 파라미터로 넘겨준다.