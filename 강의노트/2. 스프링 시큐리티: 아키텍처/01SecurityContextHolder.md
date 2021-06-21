# SecurityContextHolder 와 Authentication 관계

- Principal 이라는 정보를 Authentication 이라는 객체에 담아서 관리한다.
- Authtication > SecurityContext > SecurityContextHolder 로 감싸서 관린한다.

## SecurityContextHolder

- SecurityContext 를 제공한다.
- ThreadLocal 을 사용한다. 하나의 쓰레드에서 사용하는 저장소.
  - 메소드의 파라미터 처럼 객체를 넘겨주지 않아도 접근할 수 있게 된다.

```
    Authentication authentication = SecurityContextHolder.getContext().getAuthentication();
    UserDetails userDetails = (UserDetails) authentication.getPrincipal();
    Collection<? extends GrantedAuthority> authorities = authentication.getAuthorities();
    Object credentials = authentication.getCredentials();
    boolean authenticated = authentication.isAuthenticated();
```
- `authentication`: 
  - `UsernamePasswordAuthenticationToken` 타입
    - Form 인증같은 경우에 리턴하는 Authentication 타입
  - 디테일 정보
    - `credential = null`
    - `principal = User`
    - `authorities = Collections`
    - `details = WebAuthenticationDetails`
    - `authenticated = true`
- `getPrincipal()`: 임의(Object) 타입이긴 한데, `UserDetails` 타입을 반환할 것 이다. 
  - `userDetails`(Principal): 인증한 사용자를 나타내는 정보
  - `User` 타입: 서비스 객체의 `loadUserByUsername` 메소드에서 반환하는 `UserDetails` 타입
  - 디테일 정보   
    - `password = null`
    - `username = "user"`
    - `authorities = Collections`
    - `accountNonExpired = true`
    - `accountNonLocked = true`
    - `credentialsNonExpired = true`
    - `enabled = true`
- `getAuthorities()`: 사용자의 권한을 나타내는 정보 (복수일 수 있다)
  - `role = "ROLE_USER"`
    - `User.builder().roles()`로 설정 roles() 메소드가 추가한 prefix `"ROLE"`
- `getCredentials()`: 인증한 다음에는 크리텐셜을 가지고 있을 필요가 없이 때문에 빈 값일 것.
  - `=null`
- `isAuthenticated()`: 인증된 사용자인지
  - 로그아웃 하기 전까지는 `true`
  - Token 이 `Oauth` 이고 만료가 되면, `false` 로 표시될 것

### ThreadLocal

- 어떤 변수의 Scope를 스레드로 지정해준다. 한 스레드 내에서 변수를 공유한다.

- SecurityContextHolder 개체에서 `static final` 로 다른 모드 확인할 수 있다.
  - `MODE_THREADLOCAL`
    - Servlet 기반에서는 동일한 스레드가 요청을 처리하게 된다. Async 한 구현을 하지 않는 이상.
    - 컨트롤러에서 받은 principal 을 전달하지 않더라도, SecurityContextHolder를 통해 정보를 접근할 수 잇다.
  - `MODE_INHERITABLETHREADLOCAL`
  - `MODE_GLOBAL`
  - `spring.security.strategy`
    - `System.getProperty(SYSTEM_PROPERTY)`

- `AccountContext`를 만들어서 ThreadLocal 안에다가 넣어놓을 수 잇다.
  - 변수 및 함수를 `static`으로 선언하여, 다른 서비스에서 클래스를 불러서 쓰면 된다.
- `SpringSecurityContextHolder` 처럼 접근하여 쓸 수 있다.

**- 2개의 브라우져에서 접근/로그인 했을 때, 해당 계정 정보로 계속 Thread 기반으로 정보를 저장하고 있는다.**

## Authentication 객체

- ContextHolder를 통해 `authentication` 객체를 어플리케이션 어디에서나 접근할 수 있다.