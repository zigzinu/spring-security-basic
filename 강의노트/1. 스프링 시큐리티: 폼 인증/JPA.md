# JPA

## Entity

- `@Entity` 태그 사용

## Repository

- `public interface AccountRepository extends JpaRepository<Account, Integer> {}`
  - Jpa 가 이미 빈으로 등록되어있는 것처럼 사용할 수 있음