# Overview: Diagram Service

The `DiagramService` is responsible for generating network diagrams in PlantUML format and converting them into image files for visual representation of project schedules. It provides methods to create PlantUML source strings based on project activities and to produce diagram images.

---

## DiagramService (Interface)

The `DiagramService` interface defines the methods required for generating diagrams. It serves as a contract for any implementation.

### Methods

#### 1. `generatePlantUml(List<Activity> activities, List<Activity> startActivities, List<Activity> endActivities, String criticalPath, Integer estimatedDuration): String`

Generates a PlantUML diagram source string based on project activities and the critical path.

- **Parameters:**
    - `activities`: List of all project activities.
    - `startActivities`: List of starting activities (no predecessors).
    - `endActivities`: List of ending activities (no successors).
    - `criticalPath`: String representation of the critical path (e.g., "A-B-C").
    - `estimatedDuration`: Total project duration.
- **Returns:**
    - A `String` containing the PlantUML source code.

---

#### 2. `generateDiagram(String source): byte[]`

Generates a PNG image from the PlantUML source string.

- **Parameters:**
    - `source`: The PlantUML source string.
- **Returns:**
    - A `byte[]` containing the PNG image data.

---

## DiagramServiceImpl (Implementation)

The `DiagramServiceImpl` class implements the `DiagramService` interface, providing the logic for diagram generation.

### Methods

#### 1. `generatePlantUml(List<Activity> activities, List<Activity> startActivities, List<Activity> endActivities, String criticalPath, Integer estimatedDuration): String`

Creates a PlantUML source string representing the network diagram with critical path highlighted.

- **Implementation:**
    - Converts the critical path into a `Set` for quick verification.
    - Adds notes for start and end nodes, as well as for each activity, with details like earliest/latest start/finish times, duration, and slack.
    - Highlights critical path connections in red.

```java
@Override
public String generatePlantUml(List<Activity> activities, List<Activity> startActivities, List<Activity> endActivities, String criticalPath, Integer estimatedDuration) {
    Set<String> criticalPathLabels = Arrays.stream(criticalPath.split("-"))
            .collect(Collectors.toSet());
    Set<String> notedActivities = new HashSet<>();
    StringBuilder plantUml = new StringBuilder("@startuml\n");

    plantUml.append("note top\n")
            .append("Inicio\n")
            .append("IC: 0, TC: 0\n")
            .append("IL: 0, TL: 0\n")
            .append("Dur: 0, Holgura: 0\n")
            .append("end note\n");

    for (Activity activity : startActivities) {
        plantUml.append("(*top) --> ")
                .append("\"" + activity.getLabel() + "\"\n")
                .append("note bottom\n")
                .append(activity.getName() + "\n")
                .append("IC: ").append(activity.getCloseStart()).append(", TC: ").append(activity.getCloseFinish()).append("\n")
                .append("IL: ").append(activity.getDistantStart()).append(", TL: ").append(activity.getDistantFinish()).append("\n")
                .append("Dur: ").append(activity.getDaysDuration()).append(", Holgura: ").append(activity.getSlack()).append("\n")
                .append("end note\n");
        notedActivities.add(activity.getLabel());
    }

    for (Activity activity : activities) {
        if (activity.getPredecessors() != null) {
            for (String predecessor : activity.getPredecessors().split(",")) {
                boolean isCritical = criticalPathLabels.contains(activity.getLabel()) && criticalPathLabels.contains(predecessor);
                plantUml.append("\"" + predecessor + "\"")
                        .append(isCritical ? " -[#red]-> " : " --> ")
                        .append("\"" + activity.getLabel() + "\"\n");
                if (notedActivities.add(activity.getLabel())) {
                    plantUml.append("note bottom\n")
                            .append(activity.getName() + "\n")
                            .append("IC: ").append(activity.getCloseStart()).append(", TC: ").append(activity.getCloseFinish()).append("\n")
                            .append("IL: ").append(activity.getDistantStart()).append(", TL: ").append(activity.getDistantFinish()).append("\n")
                            .append("Dur: ").append(activity.getDaysDuration()).append(", Holgura: ").append(activity.getSlack()).append("\n")
                            .append("end note\n");
                }
            }
        }
    }

    for (Activity activity : endActivities) {
        boolean isCritical = criticalPathLabels.contains(activity.getLabel());
        plantUml.append("\"" + activity.getLabel() + "\"")
                .append(isCritical ? " -[#red]-> " : " --> ")
                .append("(*)\n");
    }

    plantUml.append("note bottom\n")
            .append("Final\n")
            .append("IC: ").append(estimatedDuration).append(", TC: ").append(estimatedDuration).append("\n")
            .append("IL: ").append(estimatedDuration).append(", TL: ").append(estimatedDuration).append("\n")
            .append("Dur: 0, Holgura: 0\n")
            .append("end note\n");

    plantUml.append("caption Ruta Crítica\n");
    plantUml.append("footer INICIO-").append(criticalPath).append("-FIN\n");
    plantUml.append("@enduml");

    return plantUml.toString();
}
```

---

#### 2. `generateDiagram(String source): byte[]`

Generates a PNG image from the provided PlantUML source string.

- **Implementation:**
    - Uses `SourceStringReader` from PlantUML to convert the source into an image.
    - Handles exceptions and ensures a valid byte array is returned.

```java
@Override
public byte[] generateDiagram(String source) {
    ByteArrayOutputStream outputStream = new ByteArrayOutputStream();
    SourceStringReader reader = new SourceStringReader(source);

    try {
        reader.outputImage(outputStream).getDescription();
    } catch (IOException e) {
        e.printStackTrace();
        return new byte[0];
    }

    return outputStream.toByteArray();
}
```

---

## Notes

- The diagrams highlight the critical path in red and include detailed notes for each activity.
- This service is crucial for visualizing project schedules and aiding project management.