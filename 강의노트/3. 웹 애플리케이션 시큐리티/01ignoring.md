# ignoring

- 웹 시큐리티 특정 필터를 적용하고 싶지 않을 때.

```
    @Override
    public void configure(WebSecurity web) throws Exception {
        web.ignoring().mvcMatchers("/favicon.ico");
    }
```

![image](https://user-images.githubusercontent.com/83999058/122849060-c23e5080-d345-11eb-9b54-78f4b31683ba.png)

- favicon 도 `200`응답에 필터를 덜 타고 로그인 폼에 대한 응답도 생략하기 때문에 응답시간이 줄어든다.

## 정적(static) 리소스에 대한 시큐리티 적용하지 않는 방법

```
    web.ignoring().requestMatchers(PathRequest.toStaticResources().atCommonLocations());
```

- `favicon.ico` 에 대한 필터 목록이 0 으로 된다.
- 제한을 두지 않았기 때문에 스프링에서 기본으로 제공하는 나뭇잎 모양이 나간다.

- `atCommonLocations().excluding()` 으로 몇 개를 제외할 수 있다.
- `at()`으로 직접 명시할 수 있다.

- `.antMatchers()`
- `.regexMatchers()`

## H2 콘솔에 대한 세팅

```
    web.ignoring().requestMatchers(PathRequest.toH2Conosle());
```

## ignore 대신에 permitAll?

```
protected void configure() {
    http.authorizeRequests()
            .requestMatchers(PathRequest.toStaticResources().atCommonLocations()).permitAll()
}
```

- 결과는 같지만 똑같지 않다.
- 무시하는 대신 검사하고 허용하는 플로우를 타게 된다.
- `ignore`방법을 추천한다.
  - `favicon`에 대한 요청이 더 오래걸렸다.
- **`favicon`에 대한 필더가 전부 타게 된다.**
  - `http.` 로 설정된 모든 필터 체인을 거쳐가야 한다.
  - `AnonymousAuthenticationFilter` 에서 `anonymousAuthentication` 생기고
  - `SessionManagementFilter` 에서 `session` 생기고
  - `FilterSecurityInterceptor`에서 허용한다.
    - 여기서 `AccessDecisionManager`가 검사를 하고 허용한다. `permitAll`

## 동적으로 처리하는 요청

- `/`, `/info` 등 로직에서 어떤 유저인지에 따라서 로직을 설정해야 한다면, 필터를 타야지만 정보를 알아올 수 있어서 ignore 로 허용할 수 없다.