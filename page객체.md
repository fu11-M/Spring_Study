## Page 객체
Page 객체는 페이징 처리를 수행하기 위해 설계된 클래스이다.
단순히 데이터 목록을 반환하는 것에 그치지 않고, 페이징과 관련된 다양한 정보를 함께 포함하여 클라이언트와 서버 간 데이터 전달을 간소화한다.

### Page 객체 예시
아래와 같은 설정이 있다고 가정 했을때

```java
import jakarta.persistence.Entity;
import jakarta.persistence.GeneratedValue;
import jakarta.persistence.GenerationType;
import jakarta.persistence.Id;
import lombok.Getter;
import lombok.Setter;

@Entity
@Getter
@Setter
public class User {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String name;
    private String email;
}
```

```java
import org.springframework.data.jpa.repository.JpaRepository;

public interface UserRepository extends JpaRepository<User, Long> {
}
```

```java
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.data.domain.Page;
import org.springframework.data.domain.PageRequest;
import org.springframework.stereotype.Service;

@Service
public class UserService {

    @Autowired
    private UserRepository userRepository;

    public Page<User> getUsers(int page, int size) {
        // PageRequest.of(페이지 번호, 페이지 크기)
        return userRepository.findAll(PageRequest.of(page, size));
    }
}
```

```java
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.data.domain.Page;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestParam;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class UserController {

    @Autowired
    private UserService userService;

    @GetMapping("/users")
    public Page<User> listUsers(
            @RequestParam(defaultValue = "0") int page, // 기본 페이지 번호: 0
            @RequestParam(defaultValue = "10") int size // 기본 페이지 크기: 10
    ) {
        return userService.getUsers(page, size);
    }
}
```

```GET /users?page=1&size=5``` 와 같이 GET 요청을 받으면 컨트롤러는 page = 1, size = 5 값을 받아오게 되고 
이 범위에 대한 user 데이터를 받아온다.

