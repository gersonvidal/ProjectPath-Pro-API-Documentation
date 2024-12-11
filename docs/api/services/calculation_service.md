# Overview: Calculation Service

The Calculation Service handles the business logic for performing calculations related to the Critical Path Method (CPM) and generating network diagrams for projects. It consists of an interface (`CalculationService`) that defines the service contract and its implementation (`CalculationServiceImpl`) that provides the actual logic. This service interacts with the repository layer for data persistence and leverages related services for auxiliary operations like diagram generation.

---

## CalculationService (Interface)

The `CalculationService` interface defines the methods required for managing calculations. It serves as a contract for any implementation of the service.

### Methods

#### 1. `getCalculationByProjectId(Long projectId): Optional<Calculation>`

Retrieves calculation details for a given project.

- **Parameters:**
    - `projectId`: The ID of the project whose calculation is to be retrieved.
- **Returns:**
    - An `Optional<Calculation>` containing the calculation details, if found.

---

#### 2. `makeAllCalculationsWhenNew(Long projectId): void`

Performs the complete set of calculations for a new project.

- **Parameters:**
    - `projectId`: The ID of the project for which calculations are to be performed.

---

#### 3. `makeAllCalculationsWhenAlreadyExists(Long projectId): void`

Updates the calculations for an existing project.

- **Parameters:**
    - `projectId`: The ID of the project for which calculations are to be updated.

---

#### 4. `getNetworkAndCriticalPathDiagram(Long projectId): String`

Generates a network diagram for the specified project and returns it as a Base64-encoded image string.

- **Parameters:**
    - `projectId`: The ID of the project for which the diagram is to be generated.
- **Returns:**
    - A `String` containing the Base64-encoded representation of the network diagram.

---

## CalculationServiceImpl (Implementation)

The `CalculationServiceImpl` class implements the `CalculationService` interface, providing the business logic for calculations and diagram generation. It interacts with repositories, the `ProjectService`, and the `DiagramService` to perform its operations.

### Methods

#### 1. `getCalculationByProjectId(Long projectId): Optional<Calculation>`

Retrieves calculation details for a given project.

- **Implementation:**
    - Checks if the project exists.
    - Fetches the calculation from the repository.

```java
@Override
public Optional<Calculation> getCalculationByProjectId(Long projectId) {
    if (!projectService.isExists(projectId)) {
        throw new EntityNotFoundException("Project with id " + projectId + " does not exist");
    }
    return calculationRepository.findByProjectId(projectId);
}
```

---

#### 2. `makeAllCalculationsWhenNew(Long projectId): void`

Performs all necessary CPM calculations for a new project.

- **Implementation:**
    - Creates an empty calculation entry in the database.
    - Performs forward pass, backward pass, slack calculation, and critical path determination.

```java
@Override
@Transactional
public void makeAllCalculationsWhenNew(Long projectId) {
    createEmptyCalculation(projectId);
    List<Activity> activities = calculateForwardPass(projectId);
    int estimatedDuration = calculateEstimatedDuration(projectId, activities);
    activities = calculateBackwardPass(projectId, activities, estimatedDuration);
    activities = calculateSlack(projectId, activities);
    calculateAndSaveCriticalPath(projectId, activities);
}
```

---

#### 3. `makeAllCalculationsWhenAlreadyExists(Long projectId): void`

Updates calculations for an existing project.

- **Implementation:**
    - Repeats all calculation steps: forward pass, backward pass, slack calculation, and critical path determination.

```java
@Override
@Transactional
public void makeAllCalculationsWhenAlreadyExists(Long projectId) {
    List<Activity> activities = calculateForwardPass(projectId);
    int estimatedDuration = calculateEstimatedDuration(projectId, activities);
    activities = calculateBackwardPass(projectId, activities, estimatedDuration);
    activities = calculateSlack(projectId, activities);
    calculateAndSaveCriticalPath(projectId, activities);
}
```

---

#### 4. `getNetworkAndCriticalPathDiagram(Long projectId): String`

Generates the network diagram for the project and returns it as a Base64-encoded image string.

- **Implementation:**
    - Fetches the activities and critical path details.
    - Uses `DiagramService` to generate the PlantUML source and PNG diagram.
    - Encodes the diagram in Base64.

```java
@Override
public String getNetworkAndCriticalPathDiagram(Long projectId) {
    if (!projectService.isExists(projectId)) {
        throw new EntityNotFoundException("Project with id: " + projectId + " does not exist");
    }
    List<Activity> activities = activityRepository.findByProjectId(projectId);
    List<Activity> startActivities = getStartActivities(projectId, activities);
    List<Activity> endActivities = getEndActivities(projectId, activities);

    Calculation calculation = calculationRepository.findByProjectId(projectId)
            .orElseThrow(() -> new EntityNotFoundException("Calculation not found with project id: " + projectId));

    String criticalPath = calculation.getCriticalPath();
    Integer estimatedDuration = calculation.getEstimatedDuration();

    String source = diagramService.generatePlantUml(activities, startActivities, endActivities, criticalPath, estimatedDuration);
    byte[] pngBytes = diagramService.generateDiagram(source);

    return Base64.getEncoder().encodeToString(pngBytes);
}
```

### Additional Private Methods in `CalculationServiceImpl`

#### 1. `createEmptyCalculation(Long projectId): Calculation`

Creates an empty calculation entry for a given project.

- **Implementation:**
    - Retrieves the project by ID.
    - Creates a new `Calculation` object with default values.
    - Saves the object in the database.

```java
private Calculation createEmptyCalculation(Long projectId) {
    Project project = projectService.findById(projectId)
            .orElseThrow(() -> new EntityNotFoundException("Project not found with project id: " + projectId));

    Calculation calculation = Calculation.builder()
            .id(null)
            .criticalPath("A") // Default value so we can insert into db
            .estimatedDuration(0) // Default value so we can insert into db
            .project(project)
            .build();

    return calculationRepository.save(calculation);
}
```

---

#### 2. `calculateForwardPass(Long projectId): List<Activity>`

Performs the forward pass calculation to determine earliest start and finish times for all activities.

- **Implementation:**
    - Retrieves and orders activities by label.
    - Computes `closeStart` and `closeFinish` for each activity based on its predecessors.
    - Updates and saves activities in the database.

```java
private List<Activity> calculateForwardPass(Long projectId) {
    List<Activity> activities = activityRepository.findByProjectId(projectId);
    activities = orderActivitiesByLabel(activities);

    Map<String, Activity> activityMap = activities.stream()
            .collect(Collectors.toMap(Activity::getLabel, activity -> activity));

    for (Activity activity : activities) {
        int closeStart = 0;
        if (activity.getPredecessors() != null) {
            for (String predecessorLabel : activity.getPredecessors().split(",")) {
                Activity predecessor = activityMap.get(predecessorLabel.trim());
                closeStart = Math.max(closeStart, predecessor.getCloseFinish());
            }
        }
        activity.setCloseStart(closeStart);
        activity.setCloseFinish(closeStart + activity.getDaysDuration());
    }

    activityRepository.saveAll(activities);
    return activities;
}
```

---

#### 3. `calculateEstimatedDuration(Long projectId, List<Activity> activities): int`

Calculates the total estimated duration of the project by finding the maximum `closeFinish` value among end activities.

- **Implementation:**
    - Identifies end activities.
    - Updates the `estimatedDuration` in the calculation table.

```java
private int calculateEstimatedDuration(Long projectId, List<Activity> activities) {
    List<Activity> endActivities = getEndActivities(projectId, activities);

    int estimatedDuration = endActivities.stream()
            .mapToInt(Activity::getCloseFinish)
            .max()
            .orElseThrow(() -> new EntityNotFoundException("No end activities found for project id: " + projectId));

    Calculation calculation = calculationRepository.findByProjectId(projectId)
            .orElseThrow(() -> new EntityNotFoundException("No calculation found for project id: " + projectId));

    calculation.setEstimatedDuration(estimatedDuration);
    calculationRepository.save(calculation);
    return estimatedDuration;
}
```

---

#### 4. `calculateBackwardPass(Long projectId, List<Activity> activities, int estimatedDuration): List<Activity>`

Performs the backward pass calculation to determine latest start and finish times for all activities.

- **Implementation:**
    - Initializes `distantFinish` for all activities.
    - Iterates in reverse order, adjusting `distantStart` and predecessors' `distantFinish`.

```java
private List<Activity> calculateBackwardPass(Long projectId, List<Activity> activities, int estimatedDuration) {
    Map<String, Activity> activityMap = activities.stream()
            .collect(Collectors.toMap(Activity::getLabel, activity -> activity));

    activities.forEach(activity -> activity.setDistantFinish(estimatedDuration));

    for (int i = activities.size() - 1; i >= 0; i--) {
        Activity activity = activities.get(i);
        int distantStart = activity.getDistantFinish() - activity.getDaysDuration();

        if (activity.getPredecessors() != null) {
            for (String predecessorLabel : activity.getPredecessors().split(",")) {
                Activity predecessor = activityMap.get(predecessorLabel.trim());
                predecessor.setDistantFinish(Math.min(predecessor.getDistantFinish(), distantStart));
            }
        }

        activity.setDistantStart(distantStart);
    }

    activityRepository.saveAll(activities);
    return activities;
}
```

---

#### 5. `calculateSlack(Long projectId, List<Activity> activities): List<Activity>`

Calculates the slack for each activity based on the difference between its earliest and latest times.

- **Implementation:**
    - Computes slack for each activity as `distantStart - closeStart`.
    - Updates the database with slack values.

```java
private List<Activity> calculateSlack(Long projectId, List<Activity> activities) {
    for (Activity activity : activities) {
        int slack = activity.getDistantStart() - activity.getCloseStart();
        activity.setSlack(slack);
    }
    activityRepository.saveAll(activities);
    return activities;
}
```

---

#### 6. `calculateAndSaveCriticalPath(Long projectId, List<Activity> activities): void`

Determines the critical path by filtering activities with zero slack and saving the result in the database.

- **Implementation:**
    - Filters activities with `slack == 0`.
    - Builds a hyphen-separated string of activity labels representing the critical path.

```java
private void calculateAndSaveCriticalPath(Long projectId, List<Activity> activities) {
    List<Activity> criticalPathActivities = activities.stream()
            .filter(activity -> activity.getSlack() == 0)
            .sorted(Comparator.comparingInt(Activity::getCloseStart))
            .collect(Collectors.toList());

    String criticalPath = criticalPathActivities.stream()
            .map(Activity::getLabel)
            .collect(Collectors.joining("-"));

    Calculation calculation = calculationRepository.findByProjectId(projectId)
            .orElseThrow(() -> new EntityNotFoundException("No calculation found for project id: " + projectId));

    calculation.setCriticalPath(criticalPath);
    calculationRepository.save(calculation);
}
```

## Notes

- The `CalculationServiceImpl` is annotated with `@Service`, making it a Spring-managed component.
- The service uses `@Transactional` to ensure database consistency during calculation operations.

---

## Dependencies

The `CalculationServiceImpl` depends on:

- `CalculationRepository` for calculation persistence.
- `ActivityRepository` for activity data.
- `ProjectService` for project validation and retrieval.
- `DiagramService` for generating network diagrams.