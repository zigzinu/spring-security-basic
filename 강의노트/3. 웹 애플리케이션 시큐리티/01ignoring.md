# ignoring

- 웹 시큐리티 특정 필터를 적용하고 싶지 않을 때.

```
    @Override
    public void configure(WebSecurity web) throws Exception {
        web.ignoring().mvcMatchers("/favicon.ico");
    }
```

