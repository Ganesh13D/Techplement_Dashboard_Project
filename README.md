# Techplement_Dashboard_Project

spring.datasource.url=jdbc:mysql://localhost:3306/techplement_db
spring.datasource.username=root
spring.datasource.password=yourpassword
spring.jpa.hibernate.ddl-auto=update
spring.jpa.show-sql=true

package com.techplement.registration.entity;

import javax.persistence.*;

@Entity
@Table(name = "users")
public class User {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @Column(nullable = false, unique = true)
    private String email;

    @Column(nullable = false)
    private String username;

    @Column(nullable = false)
    private String password;

    // getters and setters
}

package com.techplement.registration.dto;

import javax.validation.constraints.Email;
import javax.validation.constraints.NotBlank;
import javax.validation.constraints.Size;

public class UserRegistrationDto {
    @Email
    @NotBlank
    private String email;

    @NotBlank
    @Size(min = 4, max = 20)
    private String username;

    @NotBlank
    @Size(min = 6)
    private String password;

    // getters and setters
}

public class UserLoginDto {
    @Email
    @NotBlank
    private String email;

    @NotBlank
    private String password;

    // getters and setters
}
package com.techplement.registration.repository;

import java.util.Optional;
import org.springframework.data.jpa.repository.JpaRepository;
import com.techplement.registration.entity.User;

public interface UserRepository extends JpaRepository<User, Long> {
    Optional<User> findByEmail(String email);
}
package com.techplement.registration.exception;

public class UserAlreadyExistsException extends RuntimeException {
    public UserAlreadyExistsException(String msg) {
        super(msg);
    }
}

package com.techplement.registration.exception;

import org.springframework.http.HttpStatus;
import org.springframework.web.bind.annotation.*;

@RestControllerAdvice
public class RestExceptionHandler {
    @ExceptionHandler(UserAlreadyExistsException.class)
    @ResponseStatus(HttpStatus.BAD_REQUEST)
    public String handleUserExists(UserAlreadyExistsException ex) {
        return ex.getMessage();
    }
}

git init
git remote add origin https://github.com/<your-username>/Techplement.git
mkdir week1-tasks
mv * week1-tasks
git add .
git commit -m "Week1: User Registration System"
git push -u origin master


