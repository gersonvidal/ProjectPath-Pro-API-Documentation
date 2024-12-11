# UtilClass 

The `UtilClass` provides a utility method for performing a binary search on a sorted array of strings. It allows for custom comparison logic using a provided `Comparator<String>`. This flexibility makes it particularly useful in scenarios where standard lexicographical order is not sufficient.

---

## Purpose

This utility is designed to:

1. Perform efficient binary search operations on sorted arrays.
2. Allow custom comparison logic for specialized ordering requirements.

---

## Methods

#### 1. `binarySearch(String[] array, String target, Comparator<String> comparator, int endIndex): int`

Performs a binary search on a sorted array of strings using a custom comparator.

- **Parameters:**
    - `array`: The array of strings to search (must be sorted based on the comparator).
    - `target`: The string to search for.
    - `comparator`: A `Comparator<String>` to define the sorting order.
    - `endIndex`: The number of elements at the end of the array to exclude from the search.
- **Returns:**
    - The index of the target string if found.
    - `-1` if the target is not found.

- **Implementation:**
    - Initializes `low` and `high` to define the search range.
    - Iteratively adjusts `low` and `high` based on the comparator's result until the target is found or the range is exhausted.

```java
public static int binarySearch(String[] array, String target, Comparator<String> comparator, int endIndex) {
    int low = 0;
    int high = array.length - endIndex;

    while (low <= high) {
        int mid = low + (high - low) / 2;
        int cmp = comparator.compare(array[mid], target);

        if (cmp == 0) {
            return mid;
        } else if (cmp < 0) {
            low = mid + 1;
        } else {
            high = mid - 1;
        }
    }

    return -1;
}
```

---

### Notes

- **Efficiency**: The method leverages the logarithmic efficiency of binary search, making it suitable for large arrays.
- **Flexibility**: By accepting a custom comparator, it can handle specialized sorting and searching requirements.
- **Error Handling**: Returns `-1` if the target is not found, ensuring compatibility with common search operation patterns.