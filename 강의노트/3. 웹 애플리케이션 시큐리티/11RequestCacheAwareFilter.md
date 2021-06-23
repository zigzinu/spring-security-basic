# RequestCacheAwareFilter

- 현재 요청에 매칭이 되는 다른 요청을 찾아와서 관련이 있다면 그 요청을 처리하고 없다면 현재 요청을 처리한다.

1. `/dashboard` 로 간다.
2. AccessDecisionManager 가 `/login`으로 보낸다.
3. `/login`처리를 한다. 
4. 원래를 가려던 요청을 캐시에서 가져와서 처리한다.

- 크게 설정할 필요가 없다.