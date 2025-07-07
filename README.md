# Techplement_Dashboard_Project

spring.datasource.url=jdbc:mysql://localhost:3306/techplement_db
spring.datasource.username=Ganesh
spring.datasource.password=Ganesh
spring.jpa.hibernate.ddl-auto=update
spring.jpa.show-sql=true

package com.techplement.registration.entity;

import javax.persistence.*;

@Entity
@Table(name = "username")
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

package com.techplement.registration.service;

import java.util.Optional;
import org.springframework.security.crypto.bcrypt.BCryptPasswordEncoder;
import org.springframework.stereotype.Service;
import com.techplement.registration.dto.UserRegistrationDto;
import com.techplement.registration.entity.User;
import com.techplement.registration.exception.UserAlreadyExistsException;
import com.techplement.registration.repository.UserRepository;

@Service
public class UserService {
    private final UserRepository userRepo;
    private final BCryptPasswordEncoder encoder = new BCryptPasswordEncoder();

    public UserService(UserRepository repo) {
        this.userRepo = repo;
    }

    public User register(UserRegistrationDto dto) {
        if (userRepo.findByEmail(dto.getEmail()).isPresent()) {
            throw new UserAlreadyExistsException("Email already in use");
        }
        User user = new User();
        user.setEmail(dto.getEmail());
        user.setUsername(dto.getUsername());
        user.setPassword(encoder.encode(dto.getPassword()));
        return userRepo.save(user);
    }

    public boolean login(String email, String rawPassword) {
        Optional<User> opt = userRepo.findByEmail(email);
        return opt.isPresent() && encoder.matches(rawPassword, opt.get().getPassword());
    }
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
package com.techplement.registration.controller;

import javax.validation.Valid;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.*;
import com.techplement.registration.dto.*;
import com.techplement.registration.entity.User;
import com.techplement.registration.service.UserService;

@RestController
@RequestMapping("/api/users")
public class UserController {
    private final UserService service;

    public UserController(UserService service) {
        this.service = service;
    }

    @PostMapping("/register")
    public ResponseEntity<User> register(@Valid @RequestBody UserRegistrationDto dto) {
        User user = service.register(dto);
        return ResponseEntity.ok(user);
    }

    @PostMapping("/login")
    public ResponseEntity<String> login(@Valid @RequestBody UserLoginDto dto) {
        boolean ok = service.login(dto.getEmail(), dto.getPassword());
        return ok
            ? ResponseEntity.ok("Login successful")
            : ResponseEntity.status(401).body("Invalid credentials");
    }
}

git init
git remote add origin https://github.com/<your-username>/Techplement.git
mkdir week1-tasks
mv * week1-tasks
git add .
git commit -m "Week1: User Registration System"
git push -u origin master
