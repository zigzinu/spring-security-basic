# 스프링 시큐리티 필터와 FilterChainProxy

1. 체인의 목록을 가져옴
- `FilterChainProxy` > `doFilterInternal` > `getFilters`
  - Filter 목록을 가져온다
  - `SecurityFilterChain`에서 url 패턴이 매치가 되면 거기에 매치하는 Filter 목록을 가져옴
  - 기본으로 15개가 선택이 된다.
    - 목록이 만들어지는 지점이 `SecurityConfig`
    - 하나의 거대한 Filter Chain 이다.
    - 여러개를 만들 수 있다.
      - 충돌이 나기 때문에 클래스에 `@Order(Ordered.LOWEST_PRECEDENCE -15)` 와 같은 순서를 입력한다.
    - 체인이 여러개 검색이 되는데, 먼저 온 것에 매칭이 된다.
- `http.antMatcher`와 `Order` 설정에 따라 필터 목록이 생성된다.

2. `public static class VirtualFilterChain` 에서 `doFilter`를 실행
  - 명령을 실행하고, `Filter nextFilter = additionalFilters.get()`과 `nextFilter.doFilter(request, response, this)` 에 디버그로 찍으면 필터 목록을 확인할 수 있다.