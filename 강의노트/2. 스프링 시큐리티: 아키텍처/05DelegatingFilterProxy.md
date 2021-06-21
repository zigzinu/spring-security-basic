# DelegatingFilterProxy 와 FilterChainProxy

- 우리는 서블릿 기반의 아키텍쳐 사용한다.
- 서블릿 컨테이너 = Tomcat
- 서블릿 컨테이너는 서블릿 스펙을 지원하고, 서블릿 스펙에는 필터(Servlet Filter)라는 개념이 있다.
- 요청을 앞뒤로 일을 하는 인터셉터 개념

# DelegatingFilterProxy
- `FilterChainProxy`는 이 클래스를 통해 들어온다.
- 누구한테 위임하는 필터
- 자신이 필터의 역할을 하는게 아니라 스프링 IoC 컨테이너에 있는 특정한 빈에 위임한다.
- 어떤 빈에 위임할지 명시해야 한다. 이름 지정.
  - Springboot 없이 Spring Security 설정할 때는 AbstractSecurityWebApplicationInitializer 사용해서 등록.
  - Springboot 사용할 때는 아무런 설정 없어도 자동으로 등록 (SecurityFilterAutoConfiguration 빈 설정에 따름)
- 모든 필터는 ServletFilter 이다.
- `DelegatingFilterProxy`는 서블릿 설정에 등록되는 것이고, 나머지 FilterChainProxy 들은 스프링 내부적으로 사용되는 필터들.
- 작업을 위임하려면 빈 이름을 알아야 하는데 보통 `springSecurityFilterChain`이다.
  - `DelegatingFilterProxyRegiratrionBean` 이라고 등록하는 빈에서 참고하는 이름이다.