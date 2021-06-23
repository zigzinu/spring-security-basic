# Async 웹 MVC를 지원하는 필터 WebAsyncManagerIntegrationFilter

- 필터 중 가장 위에있는 필터
- 원래는 ThreadLocal 이라는 컨텍스트를 사용하여 동일한 쓰레드에서만 데이터를 공유한다.
- 다른 쓰레드에서도 동일한 Security Context를 사용할 수 있게 해주는 필터.
- 스프링 MVC의 Async 기능(핸들러에서 Callable을 리턴할 수 있는 기능)을 사용할 때에도 SecurityContext를 공유하도록 도와주는 필터.

## 사용 방법

- Request 메소드에서 리턴 값을 `Callable<String>`
- `Callable<String>() {}` 함수 안에 있는 로직을 처리하기 전에 리퀘스트를 처리하는 스레드를 반환(release)한다.
- 안에서 하는 일이 완료될 때 응답을 내보낸다. 

```
    @GetMapping("/async-handler")
    @ResponseBody
    public Callable<String> asyncHandler() {
        // Preprocess
        System.out.println("MVC");
        Thread thread = Thread.currentThread();
        System.out.println("Thread: " + thread.getName());
        Object principal = SecurityContextHolder.getContext().getAuthentication().getPrincipal();
        System.out.println("Principal: " + principal);

        return new Callable<String>() -> {
            // Callable
            System.out.println("Callable");
            Thread thread = Thread.currentThread();
            System.out.println("Thread: " + thread.getName());
            Object principal = SecurityContextHolder.getContext().getAuthentication().getPrincipal();
            System.out.println("Principal: " + principal);

            return "Async Handler";
        };
    }

```
- 컨텍스트, 스레드, 현재 위치 `MVC`
- `"MVC"` 관련 까지 처리해주는 스레드는 톰캣이 할당해주는 nio 스레드일 것.
- `Callable` 함수 안에는 별도의 스레드일 것.
- 스레드는 다르지만 컨텍스트 정보는 동일한 것을 확인하는 것.

![image](https://user-images.githubusercontent.com/83999058/122946683-89899000-d3b4-11eb-9088-00075020a358.png)

- `Preprocess`: SecurityContext를 새로 만들 스레드에 integration 해준다.
- `Callable`: 비록 다른 쓰레드이지만 그 안에서 동일한 SecurityContext 참조할 수 있다.
- `Postprocess`: 매 요청이 처리가 끝나면 SecurityContext 는 정리(clean up)되어야 한다 .