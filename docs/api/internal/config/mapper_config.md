# Overview: `MapperConfig`

The `MapperConfig` class provides configuration for the `ModelMapper` library, which is used for mapping objects in the application. This configuration ensures that the mapping process between objects is flexible and efficient.

---

## Purpose

The `MapperConfig` class:

1. Defines a `ModelMapper` bean for object mapping.
2. Configures the `ModelMapper` to use the `LOOSE` matching strategy for greater flexibility during the mapping process and to ensure nested objects mapping.

---

## Key Components

### 1. `ModelMapper` Bean

This bean provides the mapping functionality used across the application.

**Definition:**

```java
@Bean
public ModelMapper modelMapper()
```

**Behavior:**

- Creates and configures a new `ModelMapper` instance.
- Sets the `MatchingStrategy` to `LOOSE`, allowing mappings even when object properties have slight differences in naming conventions or structure and ensures mapping for nested objects.

**Example:**

If the source object has a property named `firstName` and the destination object has a property named `first_name`, the `LOOSE` strategy maps them automatically.

---

## Dependencies

The `MapperConfig` class relies on the following:

- **`ModelMapper` Library**:
    - Used for object-to-object mapping.
 
---

## Notes

- The class is annotated with `@Configuration`, indicating that it defines a Spring-managed bean.

---

## Related Documentation

- [ModelMapper Official Documentation](http://modelmapper.org/)