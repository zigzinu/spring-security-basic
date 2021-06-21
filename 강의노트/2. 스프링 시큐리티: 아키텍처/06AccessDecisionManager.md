# AccessDecisionManager

## AccessDecisionManager

- **인가**: 사용자가 서버에 요청을 할 때 접근을 허용할 것인치 판단하는 인터페이스.
- Access Control 결정을 내리는 인터페이스로, 구현체 3가지를 기본으로 제공한다.
  - `AffirmativeBased`: 기본값, voter 중 한 명이라도 허용하면 허용
  - `ConsensusBased`: 다수결
  - `UnanimousBased`: 만장일치

## AccessDecisionVoter
- Access Control 안에 들어있는 여러개의 Voter들이 각각 유효성을 확인한다.
- 해당 Authentication이 특정한 Object에 접근할 때 필요한 ConfigAttributes를 만족한는지 확인.
- ConfigAttribute
  - `.permitAll()`
  - `.hasRole()`
- 디버그에서 `voter.vote()` 값이 1 이면은 허용하는 것

1. `WebExpressionVoter` 
  - 웹 시큐리티에서 사용하는 기본 구현체. `ROLE_XXX`가 매치하는지 확인.

2. `RoleHierarchyVoter`
  - 계층형 ROLE 지원. ADMIN > MANAGER > USER

### AffirmativeBased.java

- `decide()`
  - 각 요청을 시도해보면서 이 함수가 리턴하는 `result` 값을 통해 확인할 수 있다.
- `supports(ConfigAttribute attribute)`
  - 해당 ConfigAttribute 를 지원하는지. 
  - `hasRole` 같은 것.
- `supports(Class<?> clazz)`

### ADMIN 권한에게 USER 권한도 부여하는 방법
### AccessDecisionVoter 를 커스터마이징

1. 롤을 여러개 부여한다
  - `.roles()` 설정할 때, `"ADMIN"`인 경우 `"USER"`도 부여한다. 

2. AccessDecisionManager 가 Role 들의 hierarchy 를 이해하게 한다.

SecurityConfig.java
```
    public AccessDecisionManager accessDecisionManager() {
        // hierarchy 구현 ADMIN 은 USER 의 상위버젼
        RoleHierarchyImpl roleHierarchy = new RoleHierarchyImpl();
        roleHierarchy.setHierarchy("ROLE_ADMIN > ROLE_USER");

        // handler 생성
        DefaultWebSecurityExpressionHandler handler = new DefaultWebSecurityExpressionHandler();
        handler.setRoleHierarchy(roleHierarchy);

        // hierarchy 를 설정할 수 있는 handler 를 사용할 voter 생성
        WebExpressionVoter webExpressionVoter = new WebExpressionVoter();
        webExpressionVoter.setExpressionHandler(handler);

        // voter 목록을 생성
        List<AccessDecisionVoter<? extends Object>> voters = Arrays.asList(webExpressionVoter);
        return new AffirmativeBased(voters);
    }

    @Override void configure(HttpSecurity http) throws Exception {
        http.authorizeRequests()
                .mvcMatchers("/").hasRole("ADMIN")
                .anyRequest().authenticated()
                .accessDecisionManager(accessDecisionManager())
    }
```
  - `accessDecisionManager` 를 설정하지 않으면 기본으로 `AffirmativeBased`가 된다.
  - 기본 스타일에 role hierarchy 를 알 수 잇는 handler 만 추가한 코드이다.

3. ExpressionHandler 만 커스터마이징 한다.

```
    public SecurityExpressionHandler expressionHandler() {
        RoleHierarchyImpl roleHierarchy = new RoleHierarchyImpl();
        roleHierarchy.setHierarchy("ROLE_ADMIN > ROLE_USER");

        DefaultWebSecurityExpressionHandler handler = new DefaultWebSecurityExpressionHandler();
        handler.setRoleHierarchy(roleHierarchy);

        return handler;
    }

    @Override void configure(HttpSecurity http) throws Exception {
        http.authorizeRequests()
                .mvcMatchers("/").hasRole("ADMIN")
                .anyRequest().authenticated()
                .expressionHandler(expressionHandler())
    }
```



