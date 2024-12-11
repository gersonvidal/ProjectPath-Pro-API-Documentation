# StringValidator 

The `StringValidator` is a utility class that provides methods for validating and comparing string labels. It ensures that labels adhere to specific formats and logical relationships, such as avoiding recursive dependencies or maintaining label precedence over predecessors.

---

## Purpose

This utility is designed to:

1. Validate string labels against specific patterns and rules.
2. Ensure logical relationships between activity labels and their predecessors.
3. Facilitate label-based comparisons using a custom comparator.

---

## Methods

#### 1. `isValidLabelString(String label): boolean`

Validates whether a string label consists of uppercase alphabetic characters (`A-Z`) and ensures that all characters in the string are identical (e.g., "A", "AA", "AAA").

- **Parameters:**
    - `label`: The string label to validate.
- **Returns:**
    - `true` if the label is valid.
    - `false` otherwise.

- **Implementation:**
    - Matches the label against a regular expression (`^[A-Z]+$`).
    - Ensures that all characters in the label are identical by checking the count of distinct characters.

```java
public static boolean isValidLabelString(String label) {
    String regex = "^[A-Z]+$";
    return label.matches(regex) && label.chars().distinct().count() == 1;
}
```

---

#### 2. `predecessorsIsNotRecursive(String predecessors, String label): boolean`

Checks if a label is not recursively referenced in its list of predecessors.

- **Parameters:**
    - `predecessors`: A comma-separated string of predecessor labels.
    - `label`: The current activity label.
- **Returns:**
    - `true` if the label is not recursively referenced.
    - `false` if the label is found in its predecessors.

- **Implementation:**
    - Splits the `predecessors` string into an array.
    - Iterates through the array to check if any predecessor equals the given label.

```java
public static boolean predecessorsIsNotRecursive(String predecessors, String label) {
    if (predecessors == null) {
        return true; // No predecessors, not recursive
    }
    String[] predecessorsArray = predecessors.split(",");
    for (String predecessor : predecessorsArray) {
        if (predecessor.equals(label)) {
            return false; // Recursive
        }
    }
    return true; // Not recursive
}
```

---

#### 3. `isLabelGreaterThanPredecessors(String predecessors, String label): boolean`

Verifies if a label is lexicographically greater than or equal to all its predecessors, based on the `CustomStringComparator`.

- **Parameters:**
    - `predecessors`: A comma-separated string of predecessor labels.
    - `label`: The current activity label.
- **Returns:**
    - `true` if the label is greater than or equal to all predecessors.
    - `false` otherwise.

- **Implementation:**
    - Splits the `predecessors` string into an array.
    - Uses the `CustomStringComparator` to compare the label with each predecessor.

```java
public static boolean isLabelGreaterThanPredecessors(String predecessors, String label) {
    if (predecessors == null) {
        return true; // No predecessors, condition applies
    }
    String[] predecessorsArray = predecessors.split(",");
    CustomStringComparator comparator = new CustomStringComparator();
    for (String predecessor : predecessorsArray) {
        if (comparator.compare(predecessor, label) > 0) {
            return false; // Predecessor is greater than label
        }
    }
    return true; // All predecessors are lower or equal
}
```

---

### Notes

- The utility is crucial as it ensures logical and valid relationships between activities and their predecessors.