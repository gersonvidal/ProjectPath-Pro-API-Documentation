# CustomStringComparator 

The `CustomStringComparator` is a utility class that implements the `Comparator<String>` interface to provide a custom sorting logic for strings. It orders strings primarily by their length and, in the case of equal lengths, lexicographically.

The main goal is to ensure the order `A,B,C....Z,AA,BB,CC...ZZ,AAA,BBB,CCC...ZZZ`.

---

## Purpose

This utility is designed to handle cases where string sorting needs to prioritize string length before alphabetic order, making it ideal for scenarios like ordering labels or identifiers in project activities.

---

## Methods

#### 1. `compare(String s1, String s2): int`

Compares two strings based on the following criteria:

1. **String Length**: Shorter strings are ranked before longer strings.
2. **Lexicographical Order**: If the strings have the same length, they are compared alphabetically.

- **Parameters:**
    - `s1`: The first string to compare.
    - `s2`: The second string to compare.
- **Returns:**
    - A negative integer if `s1` is "less than" `s2`.
    - Zero if `s1` is "equal to" `s2`.
    - A positive integer if `s1` is "greater than" `s2`.

---

## Implementation

The `compare` method follows a two-step comparison process: 

1. **Length-Based Comparison**:
    - If the lengths of the two strings are different, the method returns the result of `Integer.compare(s1.length(), s2.length())`.

2. **Lexicographical Comparison**:
    - If the lengths are the same, the method returns the result of `s1.compareTo(s2)`.

```java
@Override
public int compare(String s1, String s2) {
    // Order by length first
    if (s1.length() != s2.length()) {
        return Integer.compare(s1.length(), s2.length());
    }
    // If they are the same length, compare lexicographically
    return s1.compareTo(s2);
}
```

---