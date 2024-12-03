# Overview: `AppConfig`

The `AppConfig` class is part of the API's configuration layer. It defines beans and configurations necessary for user authentication and password encryption using Spring Security.

---

## Purpose

The `AppConfig` class:

1. Configures the user authentication process.
2. Provides a custom implementation of `UserDetailsService` for retrieving user information.
3. Configures the `AuthenticationProvider` with user details and password encoding.
4. Exposes an `AuthenticationManager` bean for managing authentication processes.
5. Defines a `PasswordEncoder` bean for hashing passwords securely.

---

## Key Components

### 1. `UserDetailsService`

This bean provides a mechanism to load user-specific data during authentication.

**Definition:**

```java
@Bean
public UserDetailsService userDetailsService()
```

**Behavior:**

- Retrieves a user by their email (username).
- Throws a `UsernameNotFoundException` if the user does not exist.
- Converts the retrieved `User` entity into a `UserDetails` object compatible with Spring Security.

**Example:**

If a user with the email `user@example.com` exists, the `UserDetailsService` will:

1. Query the `UserRepository`.
2. Return the user's details (email and password).

---

### 2. `AuthenticationProvider`

This bean provides the authentication logic by delegating to the `UserDetailsService` and `PasswordEncoder`.

**Definition:**

```java
@Bean
public AuthenticationProvider authenticationProvider()
```

**Behavior:**

- Configures a `DaoAuthenticationProvider` with:
  - The custom `UserDetailsService` for retrieving user data.
  - The `PasswordEncoder` for verifying credentials.
  
---

### 3. `AuthenticationManager`

This bean manages the authentication process and is used by the security framework.

**Definition:**

```java
@Bean
public AuthenticationManager authenticationManager(AuthenticationConfiguration config) throws Exception
```

**Behavior:**

- Leverages the `AuthenticationConfiguration` to provide the system's default `AuthenticationManager`.

---

### 4. `PasswordEncoder`

This bean provides password encryption and verification using BCrypt.

**Definition:**

```java
@Bean
public PasswordEncoder passwordEncoder()
```

**Behavior:**

- Encodes plain-text passwords using the BCrypt hashing algorithm.
- Verifies hashed passwords during authentication.

---

## Example Usage

### Password Encoding

When a user registers:

- The `PasswordEncoder` hashes the user's password.
- The hashed password is stored in the database.

**Example:**

```java
String rawPassword = "securePassword123";
String encodedPassword = passwordEncoder.encode(rawPassword);
// Encoded password is saved in the database
```

---

## Dependencies

The `AppConfig` class depends on the following:

1. **`UserRepository`**:
    - Used for querying user information by email.
    - Throws an exception if the user is not found.

2. **Spring Security Components**:
    - `UserDetailsService`
    - `AuthenticationProvider`
    - `AuthenticationManager`
    - `PasswordEncoder`

---

## Notes

- This class is annotated with `@Configuration`, which marks it as a configuration class for the Spring context.
- It uses `@RequiredArgsConstructor` to automatically inject dependencies (`UserRepository`).

---

## Potential Exceptions

- **`UsernameNotFoundException`**:
    - Thrown if a user is not found in the `UserRepository` during authentication.

---

## Related Documentation

- [Spring Security Authentication Documentation](https://docs.spring.io/spring-security/site/docs/current/reference/html5/#authentication)
- [BCryptPasswordEncoder Documentation](https://docs.spring.io/spring-security/site/docs/current/api/org/springframework/security/crypto/bcrypt/BCryptPasswordEncoder.html)
