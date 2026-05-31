---
title: FirstNonRepeatingChar
parent: Strings
nav_order: 9
---

# First non-repeating character

To find the first non-repeating character in a string, you can use a LinkedHashMap to maintain the insertion order while counting character frequencies.

```java
package exercise.strings;

import java.util.LinkedHashMap;
import java.util.Map;

public class FirstNonRepeatingChar {

    public static Character findFirstNonRepeating(String str) {

        Map<Character, Integer> freqMap = new LinkedHashMap<>();

        for (char ch : str.toCharArray()) {
            freqMap.put(ch, freqMap.getOrDefault(ch, 0) + 1);
        }

        // Find first character with frequency 1
        for (Map.Entry<Character, Integer> entry : freqMap.entrySet()) {
            if (entry.getValue() == 1) {
                return entry.getKey();
            }
        }

        return null; // No non-repeating character found
    }

    public static void main(String[] args) {
        String str = "swiss";

        Character result = findFirstNonRepeating(str);

        if (result != null) {
            System.out.println("First non-repeating character: " + result);
        } else {
            System.out.println("No non-repeating character found.");
        }
    }
}
```

