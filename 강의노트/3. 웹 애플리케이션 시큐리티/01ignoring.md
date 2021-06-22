# ignoring

- 웹 시큐리티 특정 필터를 적용하고 싶지 않을 때.

```
    @Override
    public void configure(WebSecurity web) throws Exception {
        web.ignoring().mvcMatchers("/favicon.ico");
    }
```

![image](https://user-images.githubusercontent.com/83999058/122849060-c23e5080-d345-11eb-9b54-78f4b31683ba.png)
