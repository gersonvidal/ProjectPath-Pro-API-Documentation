# Overview: Activity Service

The Activity Service manages business logic for handling activities in the system. It consists of an interface (`ActivityService`) defining the contract and an implementation (`ActivityServiceImpl`) providing the actual logic. This service interacts with the repository layer for data persistence and with the `ProjectService` for project-related validations.

---

## Consts

```java
private final String ONLY_VALUE = "ONLY";

private final String IN_BETWEEN = "BETWEEN";

private final String LAST_VALUE = "LAST";

private final String NOT_PRESENT = "NOT";
```

These const values help to give a default and unchangeable value that support the helper method `determineLabelStatus` and the `switch` inside the `delete` method operate correctly.

---

## ActivityService (Interface)

The `ActivityService` interface defines methods for managing activities in the application. It establishes a contract for saving, retrieving, updating, and deleting activities.

### Methods

#### 1. `save(Activity activity): Activity`

Saves an activity in the system.

- **Parameters:**
    - `activity`: The `Activity` object to be saved.
- **Returns:**
    - The saved `Activity` object with a generated ID.

---

#### 2. `findAll(): List<Activity>`

Retrieves all activities from the system.

- **Returns:**
    - A list of all `Activity` objects.

---

#### 3. `findById(Long id): Optional<Activity>`

Finds an activity by its ID.

- **Parameters:**
    - `id`: The ID of the activity to retrieve.
- **Returns:**
    - An `Optional<Activity>` containing the activity if found, or empty if not.

---

#### 4. `getActivitiesByProjectId(Long projectId): List<Activity>`

Retrieves all activities associated with a specific project.

- **Parameters:**
    - `projectId`: The ID of the project.
- **Returns:**
    - A list of activities belonging to the specified project.

---

#### 5. `isExists(Long id): boolean`

Checks if an activity exists by its ID.

- **Parameters:**
    - `id`: The ID of the activity to check.
- **Returns:**
    - `true` if the activity exists, otherwise `false`.

---

#### 6. `partialUpdate(Long id, Activity activity): Activity`

Applies partial updates to an existing activity.

- **Parameters:**
    - `id`: The ID of the activity to update.
    - `activity`: The `Activity` object with fields to update.
- **Returns:**
    - The updated `Activity` object.

---

#### 7. `delete(Long id): void`

Deletes an activity by its ID.

- **Parameters:**
    - `id`: The ID of the activity to delete.
- **Behavior:**
    - Deletes from the predecessors of other activities this activity before deletion.

---

## ActivityServiceImpl (Implementation)

The `ActivityServiceImpl` class implements the `ActivityService` interface. It provides business logic for managing activities, including complex logic for updating predecessors during deletion.

### Methods

#### 1. `save(Activity activity): Activity`

Saves an activity to the database.

- **Implementation:**
    - Validates if the associated project exists using `ProjectService`.
    - Throws an `IllegalArgumentException` if the project does not exist.
    - Saves the activity using the `ActivityRepository`.

```java
@Override
public Activity save(Activity activity) {
    Long projectId = activity.getProject().getId();

    if (!projectService.isExists(projectId)) {
        throw new IllegalArgumentException("Project with id " + projectId + " does not exist");
    }

    return activityRepository.save(activity);
}
```

---

#### 2. `findAll(): List<Activity>`

Retrieves all activities.

- **Implementation:**
    - Uses Java streams to convert the `Iterable` returned by `ActivityRepository.findAll()` into a `List`.

```java
@Override
public List<Activity> findAll() {
    return StreamSupport.stream(activityRepository
            .findAll()
            .spliterator(),
            false)
            .collect(Collectors.toList());
}
```

---

#### 3. `findById(Long id): Optional<Activity>`

Finds an activity by its ID.

- **Implementation:**
    - Delegates to `ActivityRepository.findById`.

```java
@Override
public Optional<Activity> findById(Long id) {
    return activityRepository.findById(id);
}
```

---

#### 4. `getActivitiesByProjectId(Long projectId): List<Activity>`

Retrieves activities for a specific project.

- **Implementation:**
    - Validates the project ID using `ProjectService`.
    - Fetches activities by project ID using `ActivityRepository`.

```java
@Override
public List<Activity> getActivitiesByProjectId(Long projectId) {
    if (!projectService.isExists(projectId)) {
        throw new IllegalArgumentException("Project with id " + projectId + " does not exist");
    }

    return activityRepository.findByProjectId(projectId);
}
```

---

#### 5. `isExists(Long id): boolean`

Checks if an activity exists by its ID.

- **Implementation:**
    - Delegates to `ActivityRepository.existsById`.

```java
@Override
public boolean isExists(Long id) {
    return activityRepository.existsById(id);
}
```

---

#### 6. `partialUpdate(Long id, Activity activity): Activity`

Applies partial updates to an activity.

- **Implementation:**
    - Fetches the existing activity.
    - Updates only the non-null fields provided in the `activity` object.
    - Updates the predecessors with either `null` or the `String` containig the `predecessors`. 
    - Saves the updated activity.
    - Throws a `RuntimeException` if the activity does not exist.

```java
@Override
public Activity partialUpdate(Long id, Activity activity) {
    activity.setId(id);

    return activityRepository.findById(id).map(existingActivity -> {
        Optional.ofNullable(activity.getName()).ifPresent(existingActivity::setName);
        // Optional.ofNullable(activity.getLabel()).ifPresent(existingActivity::setLabel);
        existingActivity.setPredecessors(activity.getPredecessors()); // Updates it with null or valid predecessors
        Optional.ofNullable(activity.getDaysDuration()).ifPresent(existingActivity::setDaysDuration);

        return activityRepository.save(existingActivity);
    }).orElseThrow(() -> new RuntimeException("Activity does not exists"));
}
```

---

#### 7. `delete(Long id): void`

Deletes an activity by ID and also deletes it from the predecessors of other activites.

- **Implementation:**
    - Fetches the activity and its associated project ID.
    - Updates the predecessors of other activities in the same project:
        - Removes references to the activity being deleted.
        - Handles scenarios where the activity label is the only predecessor, in between other predecessors, or the last predecessor.
    - Deletes the activity using `ActivityRepository`.

**Helper Methods:**

- `determineLabelStatus`: Identifies the position of a label in a list of predecessors.
- `removeLabel`: Removes a specific label from the predecessors string.
- `removeLastLabel`: Removes the last label from the predecessors string.

```java
@Override
public void delete(Long id) {
    // 1. Obtain the activity
    Activity activity = findById(id)
            .orElseThrow(() -> new EntityNotFoundException("Activity not found with id: " + id));

    // 2. Obtain its project's Id
    Long projectId = activity.getProject().getId();

    // 3. Obtain its label
    String label = activity.getLabel();

    // 4. Delete from all the predecessors that activity's label (only the
    // predecessors with the activity's projectId)
    // 4.1 Obtain the list of activities under the activity's projectId
    List<Activity> activities = getActivitiesByProjectId(projectId);

    // 4.2 For each activity check what is the case (1st: The only value, 2nd: It's
    // in between 0 and n - 2, 3rd: It's the last one, 4th: Is null, 5th:
    // Doesn't contain the label)
    for (Activity currentActivity : activities) {
        // 4.2.1 Split using the "," and turn the result into an String[]
        String predecessors = currentActivity.getPredecessors();

        // Case 4: Is null
        if (predecessors == null || predecessors.isBlank()) {
            continue;
        }

        String[] predecessorsValues = predecessors.split(",");

        // 4.2.2 Check the case and perform the algorithm
        String labelStatus = determineLabelStatus(predecessorsValues, label);

        switch (labelStatus) {
            case ONLY_VALUE:
                currentActivity.setPredecessors(null);

                activityRepository.save(currentActivity);
                break;

            case IN_BETWEEN:
                String updatedPredecessors = removeLabel(predecessorsValues, label);
                currentActivity.setPredecessors(updatedPredecessors);

                activityRepository.save(currentActivity);
                break;

            case LAST_VALUE:
                updatedPredecessors = removeLastLabel(predecessorsValues);
                currentActivity.setPredecessors(updatedPredecessors);

                activityRepository.save(currentActivity);
                break;

            case NOT_PRESENT:
                // Do nothing
                break;
        }

    }

    // 5. Delete the activity
    activityRepository.deleteById(id);
}

private String determineLabelStatus(String[] predecessorsValues, String label) {
    // Case 1: The only value
    if (predecessorsValues.length == 1 && predecessorsValues[0].equals(label)) {
        return ONLY_VALUE;
    }

    // Case 3: It's the last one
    if (predecessorsValues[predecessorsValues.length - 1].equals(label)) {
        return LAST_VALUE;
    }

    // Case 2: It's in between 0 and n - 2
    int found = UtilClass.binarySearch(predecessorsValues, label, new CustomStringComparator(), 2);

    if (found >= 0) {
        return IN_BETWEEN;
    }

    // Case 5: Doesn't contain the label
    if (found == -1) {
        return NOT_PRESENT;
    }

    throw new RuntimeException("Error when determining label position");

}

/**
 * Removes the specified label from the predecessors array and reconstructs the
 * String.
 * 
 * @param predecessorsValues The array of predecessors
 * @param label              The label to remove
 * @return The updated predecessors string
 */
private String removeLabel(String[] predecessorsValues, String label) {
    StringBuilder updatedPredecessors = new StringBuilder();

    for (int i = 0; i < predecessorsValues.length; i++) {
        // Skip the label to be removed
        if (predecessorsValues[i].equals(label)) {
            continue;
        }

        // Append the predecessor
        updatedPredecessors.append(predecessorsValues[i]);

        // Add a comma if it's not the last element
        if (i < predecessorsValues.length - 1) {
            updatedPredecessors.append(",");
        }
    }

    return updatedPredecessors.toString();
}

private String removeLastLabel(String[] predecessorsValues) {
    // Create a comma-separated string ("A,B,C,D")
    StringBuilder stringBuilder = new StringBuilder(String.join(",", predecessorsValues));

    stringBuilder.delete(stringBuilder.length() - 2, stringBuilder.length());

    return stringBuilder.toString();

}
```

---

## Notes

- The `ActivityServiceImpl` class is annotated with `@Service`, marking it as a Spring-managed service component.
- The service depends on:
    - **`ActivityRepository`**: For database operations.
    - **`ProjectService`**: To validate project existence.
- Custom helper methods like `determineLabelStatus` and `removeLabel` handle predecessor updates during deletions.

---

## Dependencies

**Repository:**

- `ActivityRepository`

**Related Services:**

- `ProjectService`

**Utilities:**

- `CustomStringComparator`
- `UtilClass`