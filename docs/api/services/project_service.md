# Overview: Project Service

The Project Service handles the business logic for managing projects. It consists of an interface (`ProjectService`) that defines the service contract and its implementation (`ProjectServiceImpl`) that provides the actual logic. This service interacts with the repository layer for data persistence.

---

## ProjectService (Interface)

The `ProjectService` interface defines the methods required for managing projects. It serves as a contract for any implementation of the service.

### Methods

#### 1. `save(Project project): Project`

Saves a project in the system.

- **Parameters:** 
    - `project`: The `Project` object to be saved.
- **Returns:**
    - The saved `Project` with a generated ID.

---

#### 2. `findAll(): List<Project>`

Retrieves all projects from the system.

- **Returns:**
    - A list of all `Project` objects.

---

#### 3. `findById(Long id): Optional<Project>`

Finds a project by its ID.

- **Parameters:**
    - `id`: The ID of the project to retrieve.
- **Returns:**
    - An `Optional<Project>` containing the project if found, or empty if not.

---

#### 4. `isExists(Long id): boolean`

Checks if a project exists by its ID.

- **Parameters:**
    - `id`: The ID of the project to check.
- **Returns:**
    - `true` if the project exists, otherwise `false`.

---

#### 5. `partialUpdate(Long id, Project project): Project`

Applies partial updates to an existing project.

- **Parameters:**
    - `id`: The ID of the project to update.
    - `project`: The `Project` object with fields to update.
- **Returns:**
    - The updated `Project` object.

---

#### 6. `delete(Long id): void`

Deletes a project by its ID.

- **Parameters:**
    - `id`: The ID of the project to delete.

---

## ProjectServiceImpl (Implementation)

The `ProjectServiceImpl` class implements the `ProjectService` interface, providing the business logic for managing projects. It uses the `ProjectRepository` to interact with the database.

### Methods

#### 1. `save(Project project): Project`

Saves a project in the database.

- **Implementation:**
    - Delegates to the `save` method of `ProjectRepository`.

```java
@Override
public Project save(Project project) {
    return projectRepository.save(project);
}
```

---

#### 2. `findAll(): List<Project>`

Fetches all projects from the database.

- **Implementation:**
    - Converts the `Iterable` returned by `ProjectRepository.findAll()` into a `List` using Java streams.
- **Behavior:**
    - Returns a list of all projects.

```java
@Override
public List<Project> findAll() {
    return StreamSupport.stream(projectRepository
            .findAll()
            .spliterator(),
            false)
            .collect(Collectors.toList());
}
```

---

#### 3. `findById(Long id): Optional<Project>`

Finds a project by its ID.

- **Implementation:**
    - Delegates to `ProjectRepository.findById(id)`.
- **Behavior:**
    - Returns an `Optional` containing the project if it exists.

```java
@Override
public Optional<Project> findById(Long id) {
    return projectRepository.findById(id);
}
```

---

#### 4. `isExists(Long id): boolean`

Checks if a project exists by ID.

- **Implementation:**
    - Uses the `existsById` method of `ProjectRepository`.
- **Behavior:**
    - Returns `true` if the project exists, `false` otherwise.

```java
@Override
public boolean isExists(Long id) {
    return projectRepository.existsById(id);
}
```

---

#### 5. `partialUpdate(Long id, Project project): Project`

Applies partial updates to a project.

- **Implementation:**
    - Fetches the project by ID.
    - Updates only the non-null fields provided in the `project` parameter.
    - Saves the updated project back to the database.
- **Behavior:**
    - Throws a `RuntimeException` if the project does not exist.

```java
@Override
public Project partialUpdate(Long id, Project project) {
    project.setId(id);

    return projectRepository.findById(id).map(existingProject -> {
        Optional.ofNullable(project.getName()).ifPresent(existingProject::setName);
        Optional.ofNullable(project.getDescription()).ifPresent(existingProject::setDescription);
        // TODO: Calculations

        return projectRepository.save(existingProject);
    }).orElseThrow(() -> new RuntimeException("Project does not exists"));
}
```

---

#### 6. `delete(Long id): void`

Deletes a project by ID.

- **Implementation:**
    - Uses the `deleteById` method of `ProjectRepository`.

```java
@Override
public void delete(Long id) {
    projectRepository.deleteById(id);
}
```

---

## Notes

- The `ProjectServiceImpl` class is annotated with `@Service`, marking it as a Spring-managed service component.

---

## Dependencies

The `ProjectServiceImpl` class depends on the `ProjectRepository` for all database interactions.

---