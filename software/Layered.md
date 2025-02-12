# 계층형 아키텍처

## 1. 계층형 아키텍처란?
Layered Architecture는 소프트웨어 시스템을 기능별로 분리하여 여러 계층으로 나누는 설계 방식이다.  
각 계층은 특정한 역할을 수행하며, 상위 계층은 하위 계층에 의존하는 구조를 가진다.   

일반적으로 사용되는 계층 구조는 다음과 같다:

1. **Presentation Layer**: 사용자 인터페이스 및 요청을 처리하는 계층.
2. **Application Layer**: 비즈니스 로직과 데이터 처리 간 조정 역할을 수행하는 계층.
3. **Business Layer**: 애플리케이션의 핵심 로직을 담당하는 계층.
4. **Persistence Layer**: 데이터베이스와의 인터페이스를 담당하는 계층.
5. **Database Layer**: 실제 데이터를 저장하고 관리하는 계층.

이러한 계층적 구조를 통해 시스템을 모듈화하고 유지보수를 용이하게 할 수 있다.

---

## 2. 계층형 아키텍처의 장점과 단점

### 2.1 장점
- **모듈화**: 각 계층이 독립적으로 동작하므로 유지보수 및 확장이 용이하다.
- **재사용성**: 특정 계층의 기능을 여러 곳에서 재사용할 수 있다.
- **유지보수성**: 특정 계층의 변경이 다른 계층에 최소한의 영향을 미치도록 설계 가능하다.
- **테스트 용이성**: 각 계층별로 테스트가 가능하여 단위 테스트 및 통합 테스트를 쉽게 수행할 수 있다.

### 2.2 단점
- **성능 저하**: 계층 간 호출이 많아질수록 성능 저하가 발생할 수 있다.
- **복잡성 증가**: 계층이 많아질수록 관리해야 할 코드와 의존성이 증가한다.
- **유연성 부족**: 계층 간 의존성이 강하면 특정 계층을 변경할 때 다른 계층에도 영향을 미칠 수 있다.

---

## 3. Spring 환경에서의 계층형 아키텍처 적용
Spring 프레임워크에서는 계층형 아키텍처를 적용하는 것이 일반적이며, 다음과 같은 구조를 따른다.

### 3.1 기본적인 Spring 계층 구조
1. **Controller Layer**: `@RestController` 또는 `@Controller`
2. **Service Layer**: `@Service`
3. **Repository Layer**: `@Repository`

```java
@RestController
@RequestMapping("/users")
public class UserController {
    private final UserService userService;

    public UserController(UserService userService) {
        this.userService = userService;
    }

    @GetMapping("/{id}")
    public ResponseEntity<UserDto> getUser(@PathVariable Long id) {
        return ResponseEntity.ok(userService.getUserById(id));
    }
}

@Service
public class UserService {
    private final UserRepository userRepository;

    public UserService(UserRepository userRepository) {
        this.userRepository = userRepository;
    }

    public UserDto getUserById(Long id) {
        User user = userRepository.findById(id)
                .orElseThrow(() -> new UserNotFoundException("User not found"));
        return new UserDto(user);
    }
}

@Repository
public interface UserRepository extends JpaRepository<User, Long> {}
```
### 3.2 계층형 아키텍처에서 발생하는 문제 해결
1. DTO 활용  
    - Controller와 Service 간에 엔터티가 직접 노출되지 않도록 DTO를 사용한다.
2. 서비스 간 분리
    - 복잡한 비즈니스 로직이 많아질 경우, 도메인 별로 서비스 클래스를 분리하여 관리한다.
3.  트랜잭션 관리
    - @Transactional을 Service Layer에서 적용하여 트랜잭션의 일관성을 유지한다.
```java
@Service
@Transactional
public class UserService {
    // 비즈니스 로직 처리
}
```
## 4. 개인생각
계층형 아키텍처는 지금도 많이 사용하는 만큼 효율적인 아키텍처라 생각합니다.  
특히 각 계층이 직관적으로 분리되어 관리되는 만큼 유지보수하기 좋은 아키텍처라 생각하지만, 무분별한 계층 분리는 싱크홀 안티패턴 등 다양한 문제점을 야기할 수 있으니 신중한 고민과 커뮤니케이션을 통해 분리하는 것이 합리적이라고 생각합니다.