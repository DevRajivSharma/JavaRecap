# Java Regular Expressions

Regular expressions (regex) are powerful tools for pattern matching and text manipulation. Java provides comprehensive support for regular expressions through the `java.util.regex` package, which includes classes like `Pattern`, `Matcher`, and `PatternSyntaxException`.

## Introduction to Regular Expressions

A regular expression is a sequence of characters that defines a search pattern. These patterns can be used for text searching, text replacing, input validation, and parsing.

Regular expressions consist of:
- Literal characters that match themselves
- Meta-characters with special meanings
- Quantifiers that specify how many times a pattern should match
- Character classes that match sets of characters
- Grouping constructs that treat multiple characters as a single unit

## The `java.util.regex` Package

Java's regex support is provided through the following main classes:

1. **Pattern**: Compiled representation of a regular expression
2. **Matcher**: Engine that matches patterns against text
3. **PatternSyntaxException**: Exception thrown for syntax errors in regex patterns

## Basic Pattern Matching

```java
import java.util.regex.Matcher;
import java.util.regex.Pattern;

public class BasicRegexExample {
    public static void main(String[] args) {
        // The text to search
        String text = "The quick brown fox jumps over the lazy dog";
        
        // Create a pattern to search for
        Pattern pattern = Pattern.compile("fox");
        
        // Create a matcher for the input text
        Matcher matcher = pattern.matcher(text);
        
        // Check if the pattern is found
        if (matcher.find()) {
            System.out.println("Match found at position: " + matcher.start());
            System.out.println("Matched text: " + matcher.group());
        } else {
            System.out.println("No match found");
        }
    }
}
```

## Pattern Flags

The `Pattern.compile()` method accepts flags that modify how the pattern is interpreted:

```java
import java.util.regex.Matcher;
import java.util.regex.Pattern;

public class PatternFlagsExample {
    public static void main(String[] args) {
        String text = "The Quick Brown Fox Jumps Over The Lazy Dog";
        
        // Case-insensitive matching
        Pattern pattern = Pattern.compile("quick", Pattern.CASE_INSENSITIVE);
        Matcher matcher = pattern.matcher(text);
        
        if (matcher.find()) {
            System.out.println("Case-insensitive match found: " + matcher.group());
        }
        
        // Multiple flags using bitwise OR
        Pattern multiPattern = Pattern.compile("^the", 
                                Pattern.CASE_INSENSITIVE | Pattern.MULTILINE);
        Matcher multiMatcher = multiPattern.matcher(text);
        
        if (multiMatcher.find()) {
            System.out.println("Multi-flag match found: " + multiMatcher.group());
        }
    }
}
```

Common pattern flags include:

- `Pattern.CASE_INSENSITIVE` - Enables case-insensitive matching
- `Pattern.MULTILINE` - Changes behavior of `^` and `$` to match at line boundaries
- `Pattern.DOTALL` - Makes `.` match any character including line terminators
- `Pattern.UNICODE_CASE` - Use Unicode case folding for case-insensitive matching
- `Pattern.COMMENTS` - Allows whitespace and comments in pattern

## Character Classes

Character classes match a single character from a set of characters:

```java
import java.util.regex.Matcher;
import java.util.regex.Pattern;

public class CharacterClassExample {
    public static void main(String[] args) {
        String text = "The year 2023 had 365 days, and 12 months.";
        
        // Match any digit
        Pattern digitPattern = Pattern.compile("\\d+");
        Matcher digitMatcher = digitPattern.matcher(text);
        
        System.out.println("Digits in the text:");
        while (digitMatcher.find()) {
            System.out.println(digitMatcher.group());
        }
        
        // Match any word character (letter, digit, or underscore)
        Pattern wordPattern = Pattern.compile("\\w+");
        Matcher wordMatcher = wordPattern.matcher(text);
        
        System.out.println("\nWords in the text:");
        while (wordMatcher.find()) {
            System.out.println(wordMatcher.group());
        }
        
        // Custom character class
        Pattern vowelPattern = Pattern.compile("[aeiou]");
        Matcher vowelMatcher = vowelPattern.matcher(text.toLowerCase());
        
        int vowelCount = 0;
        while (vowelMatcher.find()) {
            vowelCount++;
        }
        
        System.out.println("\nNumber of vowels: " + vowelCount);
    }
}
```

Common predefined character classes:

- `\d` - A digit: `[0-9]`
- `\D` - A non-digit: `[^0-9]`
- `\s` - A whitespace character: `[ \t\n\x0B\f\r]`
- `\S` - A non-whitespace character: `[^\s]`
- `\w` - A word character: `[a-zA-Z_0-9]`
- `\W` - A non-word character: `[^\w]`

## Quantifiers

Quantifiers specify how many times a character or group should be matched:

```java
import java.util.regex.Matcher;
import java.util.regex.Pattern;

public class QuantifierExample {
    public static void main(String[] args) {
        String text = "The price of the item is $1234.56";
        
        // Match a price (dollar sign followed by digits and optional decimal part)
        Pattern pricePattern = Pattern.compile("\\$\\d+(\\.\\d{2})?");
        Matcher priceMatcher = pricePattern.matcher(text);
        
        if (priceMatcher.find()) {
            System.out.println("Price found: " + priceMatcher.group());
        }
        
        // Match words with 3 or more characters
        Pattern wordPattern = Pattern.compile("\\b\\w{3,}\\b");
        Matcher wordMatcher = wordPattern.matcher(text);
        
        System.out.println("\nWords with 3 or more characters:");
        while (wordMatcher.find()) {
            System.out.println(wordMatcher.group());
        }
    }
}
```

Common quantifiers:

- `*` - Match 0 or more times
- `+` - Match 1 or more times
- `?` - Match 0 or 1 time
- `{n}` - Match exactly n times
- `{n,}` - Match at least n times
- `{n,m}` - Match between n and m times

## Greedy vs. Reluctant Quantifiers

By default, quantifiers are greedy (match as much as possible). Adding a `?` after a quantifier makes it reluctant (match as little as possible):

```java
import java.util.regex.Matcher;
import java.util.regex.Pattern;

public class GreedyVsReluctantExample {
    public static void main(String[] args) {
        String text = "<h1>Title</h1><p>Paragraph</p>";
        
        // Greedy quantifier
        Pattern greedyPattern = Pattern.compile("<.*>");
        Matcher greedyMatcher = greedyPattern.matcher(text);
        
        if (greedyMatcher.find()) {
            System.out.println("Greedy match: " + greedyMatcher.group());
        }
        
        // Reluctant quantifier
        Pattern reluctantPattern = Pattern.compile("<.*?>");
        Matcher reluctantMatcher = reluctantPattern.matcher(text);
        
        System.out.println("\nReluctant matches:");
        while (reluctantMatcher.find()) {
            System.out.println(reluctantMatcher.group());
        }
    }
}
```

## Capturing Groups

Capturing groups allow you to treat multiple characters as a single unit and extract matched substrings:

```java
import java.util.regex.Matcher;
import java.util.regex.Pattern;

public class GroupExample {
    public static void main(String[] args) {
        String text = "John Smith (john.smith@example.com)";
        
        // Pattern to match name and email
        Pattern pattern = Pattern.compile("(\\w+\\s\\w+)\\s\\(([\\w.]+@[\\w.]+)\\)");
        Matcher matcher = pattern.matcher(text);
        
        if (matcher.find()) {
            System.out.println("Full match: " + matcher.group(0));
            System.out.println("Name: " + matcher.group(1));
            System.out.println("Email: " + matcher.group(2));
        }
        
        // Parse date in different formats
        String dates = "Dates: 2023-12-31, 12/31/2023, 31-Dec-2023";
        
        // ISO format: YYYY-MM-DD
        Pattern isoPattern = Pattern.compile("(\\d{4})-(\\d{2})-(\\d{2})");
        Matcher isoMatcher = isoPattern.matcher(dates);
        
        if (isoMatcher.find()) {
            System.out.println("\nISO Date: " + isoMatcher.group(0));
            System.out.println("Year: " + isoMatcher.group(1));
            System.out.println("Month: " + isoMatcher.group(2));
            System.out.println("Day: " + isoMatcher.group(3));
        }
    }
}
```

## Named Capturing Groups

Java supports named capturing groups, which make your regex more readable and maintainable:

```java
import java.util.regex.Matcher;
import java.util.regex.Pattern;

public class NamedGroupExample {
    public static void main(String[] args) {
        String text = "John Smith (john.smith@example.com)";
        
        // Pattern with named groups
        Pattern pattern = Pattern.compile("(?<name>\\w+\\s\\w+)\\s\\((?<email>[\\w.]+@[\\w.]+)\\)");
        Matcher matcher = pattern.matcher(text);
        
        if (matcher.find()) {
            System.out.println("Full match: " + matcher.group(0));
            System.out.println("Name: " + matcher.group("name"));
            System.out.println("Email: " + matcher.group("email"));
        }
        
        // Parse date with named groups
        String date = "2023-12-31";
        
        Pattern datePattern = Pattern.compile("(?<year>\\d{4})-(?<month>\\d{2})-(?<day>\\d{2})");
        Matcher dateMatcher = datePattern.matcher(date);
        
        if (dateMatcher.find()) {
            System.out.println("\nDate: " + dateMatcher.group(0));
            System.out.println("Year: " + dateMatcher.group("year"));
            System.out.println("Month: " + dateMatcher.group("month"));
            System.out.println("Day: " + dateMatcher.group("day"));
        }
    }
}
```

## Backreferences

Backreferences allow you to match the same text that was matched by a capturing group earlier in the regex:

```java
import java.util.regex.Matcher;
import java.util.regex.Pattern;

public class BackreferenceExample {
    public static void main(String[] args) {
        String text = "<h1>Title</h1> <p>Paragraph</p> <div>Content</div> <h1>Invalid Title</h2>";
        
        // Match HTML tags with the same opening and closing tag
        Pattern pattern = Pattern.compile("<([a-z][a-z0-9]*)>.*?</\\1>");
        Matcher matcher = pattern.matcher(text);
        
        System.out.println("Valid HTML tags:");
        while (matcher.find()) {
            System.out.println(matcher.group());
        }
        
        // Find repeated words
        String sentence = "The the quick brown fox jumps over the lazy lazy dog";
        
        Pattern wordPattern = Pattern.compile("\\b(\\w+)\\s+\\1\\b", Pattern.CASE_INSENSITIVE);
        Matcher wordMatcher = wordPattern.matcher(sentence);
        
        System.out.println("\nRepeated words:");
        while (wordMatcher.find()) {
            System.out.println(wordMatcher.group());
        }
    }
}
```

## Boundary Matchers

Boundary matchers help you match positions rather than characters:

```java
import java.util.regex.Matcher;
import java.util.regex.Pattern;

public class BoundaryExample {
    public static void main(String[] args) {
        String text = "The quick brown fox jumps over the lazy dog";
        
        // Match words starting with 'q'
        Pattern startPattern = Pattern.compile("\\bq\\w*\\b");
        Matcher startMatcher = startPattern.matcher(text);
        
        if (startMatcher.find()) {
            System.out.println("Word starting with 'q': " + startMatcher.group());
        }
        
        // Match words ending with 'x'
        Pattern endPattern = Pattern.compile("\\b\\w*x\\b");
        Matcher endMatcher = endPattern.matcher(text);
        
        if (endMatcher.find()) {
            System.out.println("Word ending with 'x': " + endMatcher.group());
        }
        
        // Match complete lines
        String multiline = "First line\nSecond line\nThird line";
        
        Pattern linePattern = Pattern.compile("^.*$", Pattern.MULTILINE);
        Matcher lineMatcher = linePattern.matcher(multiline);
        
        System.out.println("\nLines:");
        while (lineMatcher.find()) {
            System.out.println("Line: " + lineMatcher.group());
        }
    }
}
```

Common boundary matchers:

- `^` - The beginning of a line
- `$` - The end of a line
- `\b` - A word boundary
- `\B` - A non-word boundary
- `\A` - The beginning of the input
- `\Z` - The end of the input but for the final terminator, if any
- `\z` - The end of the input

## Lookahead and Lookbehind Assertions

Lookahead and lookbehind assertions allow you to match a pattern only if it's followed by or preceded by another pattern, without including the second pattern in the match:

```java
import java.util.regex.Matcher;
import java.util.regex.Pattern;

public class LookAroundExample {
    public static void main(String[] args) {
        // Positive lookahead
        String passwords = "password123, abc123, strongPass42, weak, P@ssw0rd";
        
        // Match passwords that contain at least one digit
        Pattern digitPattern = Pattern.compile("\\b\\w+(?=.*\\d)\\b");
        Matcher digitMatcher = digitPattern.matcher(passwords);
        
        System.out.println("Passwords with at least one digit:");
        while (digitMatcher.find()) {
            System.out.println(digitMatcher.group());
        }
        
        // Negative lookahead
        // Match passwords that don't contain any digits
        Pattern noDigitPattern = Pattern.compile("\\b\\w+(?!.*\\d)\\b");
        Matcher noDigitMatcher = noDigitPattern.matcher(passwords);
        
        System.out.println("\nPasswords without digits:");
        while (noDigitMatcher.find()) {
            System.out.println(noDigitMatcher.group());
        }
        
        // Positive lookbehind
        String prices = "$10, €20, $30, £40, $50";
        
        // Match prices in dollars
        Pattern dollarPattern = Pattern.compile("(?<=\\$)\\d+");
        Matcher dollarMatcher = dollarPattern.matcher(prices);
        
        System.out.println("\nPrices in dollars:");
        while (dollarMatcher.find()) {
            System.out.println(dollarMatcher.group());
        }
        
        // Negative lookbehind
        // Match prices not in dollars
        Pattern notDollarPattern = Pattern.compile("(?<!\\$)\\d+");
        Matcher notDollarMatcher = notDollarPattern.matcher(prices);
        
        System.out.println("\nPrices not in dollars:");
        while (notDollarMatcher.find()) {
            // Filter out matches that aren't actually prices
            if (prices.charAt(notDollarMatcher.start() - 1) == '€' || 
                prices.charAt(notDollarMatcher.start() - 1) == '£') {
                System.out.println(notDollarMatcher.group());
            }
        }
    }
}
```

Types of lookaround assertions:

- `(?=pattern)` - Positive lookahead
- `(?!pattern)` - Negative lookahead
- `(?<=pattern)` - Positive lookbehind
- `(?<!pattern)` - Negative lookbehind

## String Methods with Regex

Java's `String` class provides several methods that use regular expressions:

```java
public class StringRegexExample {
    public static void main(String[] args) {
        String text = "The quick brown fox jumps over the lazy dog";
        
        // Split string by whitespace
        String[] words = text.split("\\s+");
        System.out.println("Words in the text:");
        for (String word : words) {
            System.out.println(word);
        }
        
        // Replace all vowels with '*'
        String noVowels = text.replaceAll("[aeiou]", "*");
        System.out.println("\nText with vowels replaced: " + noVowels);
        
        // Replace first occurrence only
        String replaceFirst = text.replaceFirst("the", "a");
        System.out.println("\nReplace first 'the': " + replaceFirst);
        
        // Check if the text matches a pattern
        boolean matches = text.matches(".*fox.*");
        System.out.println("\nText contains 'fox': " + matches);
    }
}
```

## Finding All Matches

To find all matches of a pattern in a text:

```java
import java.util.regex.Matcher;
import java.util.regex.Pattern;

public class FindAllExample {
    public static void main(String[] args) {
        String text = "Contact us at info@example.com or support@company.org for assistance.";
        
        // Pattern to match email addresses
        Pattern pattern = Pattern.compile("[\\w.]+@[\\w.]+\\.[a-z]{2,}");
        Matcher matcher = pattern.matcher(text);
        
        System.out.println("Email addresses found:");
        while (matcher.find()) {
            System.out.println(matcher.group());
            System.out.println("  Position: " + matcher.start() + "-" + matcher.end());
        }
    }
}
```

## Replacing with Matcher

The `Matcher` class provides methods for replacing text:

```java
import java.util.regex.Matcher;
import java.util.regex.Pattern;

public class MatcherReplaceExample {
    public static void main(String[] args) {
        String text = "The date is 2023-12-31 and another date is 2024-01-15.";
        
        // Pattern to match dates in ISO format (YYYY-MM-DD)
        Pattern pattern = Pattern.compile("(\\d{4})-(\\d{2})-(\\d{2})");
        Matcher matcher = pattern.matcher(text);
        
        // Replace with MM/DD/YYYY format using group references
        String result = matcher.replaceAll("$2/$3/$1");
        System.out.println("Dates reformatted: " + result);
        
        // More complex replacement using appendReplacement and appendTail
        String html = "<p>First paragraph</p><p>Second paragraph</p>";
        Pattern tagPattern = Pattern.compile("<p>(.*?)</p>");
        Matcher tagMatcher = tagPattern.matcher(html);
        
        StringBuffer sb = new StringBuffer();
        int count = 1;
        
        while (tagMatcher.find()) {
            String replacement = "<h" + count + ">" + tagMatcher.group(1) + "</h" + count + ">";
            tagMatcher.appendReplacement(sb, replacement);
            count++;
        }
        tagMatcher.appendTail(sb);
        
        System.out.println("\nTransformed HTML: " + sb.toString());
    }
}
```

## Pattern Compilation and Reuse

Compiling patterns is expensive, so it's best to compile once and reuse:

```java
import java.util.regex.Matcher;
import java.util.regex.Pattern;

public class PatternReuseExample {
    // Compile patterns once as static constants
    private static final Pattern EMAIL_PATTERN = 
            Pattern.compile("[\\w.]+@[\\w.]+\\.[a-z]{2,}");
    
    private static final Pattern DATE_PATTERN = 
            Pattern.compile("\\d{4}-\\d{2}-\\d{2}");
    
    public static void main(String[] args) {
        String[] texts = {
            "Contact: john@example.com",
            "Meeting scheduled for 2023-12-15",
            "Send report to jane@company.org by 2023-12-20"
        };
        
        for (String text : texts) {
            System.out.println("\nAnalyzing: " + text);
            
            // Find emails
            Matcher emailMatcher = EMAIL_PATTERN.matcher(text);
            while (emailMatcher.find()) {
                System.out.println("Email found: " + emailMatcher.group());
            }
            
            // Find dates
            Matcher dateMatcher = DATE_PATTERN.matcher(text);
            while (dateMatcher.find()) {
                System.out.println("Date found: " + dateMatcher.group());
            }
        }
    }
}
```

## Handling Pattern Syntax Exceptions

Invalid regex patterns throw `PatternSyntaxException`:

```java
import java.util.regex.Pattern;
import java.util.regex.PatternSyntaxException;

public class PatternExceptionExample {
    public static void main(String[] args) {
        String[] patterns = {
            "[a-z]+",       // Valid pattern
            "[a-z",         // Missing closing bracket
            "*abc",         // Quantifier without preceding character
            "\\"            // Incomplete escape sequence
        };
        
        for (String patternStr : patterns) {
            try {
                Pattern pattern = Pattern.compile(patternStr);
                System.out.println("Pattern compiled successfully: " + patternStr);
            } catch (PatternSyntaxException e) {
                System.out.println("Invalid pattern: " + patternStr);
                System.out.println("Error: " + e.getMessage());
                System.out.println("Error index: " + e.getIndex());
                System.out.println();
            }
        }
    }
}
```

## Common Regex Patterns

Here are some commonly used regex patterns in Java:

```java
import java.util.regex.Matcher;
import java.util.regex.Pattern;

public class CommonPatternsExample {
    public static void main(String[] args) {
        // Test data
        String[] testData = {
            "john@example.com",           // Email
            "123-456-7890",              // US Phone
            "https://www.example.com",    // URL
            "192.168.1.1",               // IPv4
            "2001:0db8:85a3:0000:0000:8a2e:0370:7334", // IPv6
            "#FF5733",                   // Hex color
            "2023-12-31",                // ISO date
            "12/31/2023",                // US date
            "John123",                   // Alphanumeric
            "P@ssw0rd!",                 // Strong password
            "   Whitespace   "           // Whitespace
        };
        
        // Common regex patterns
        String[][] patterns = {
            {"Email", "^[\\w.%+-]+@[\\w.-]+\\.[a-zA-Z]{2,}$"},
            {"US Phone", "^\\d{3}-\\d{3}-\\d{4}$"},
            {"URL", "^(https?|ftp)://[^\\s/$.?#].[^\\s]*$"},
            {"IPv4", "^((25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)\\.){3}(25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)$"},
            {"IPv6", "^([0-9a-fA-F]{1,4}:){7}[0-9a-fA-F]{1,4}$"},
            {"Hex Color", "^#([A-Fa-f0-9]{6}|[A-Fa-f0-9]{3})$"},
            {"ISO Date", "^\\d{4}-\\d{2}-\\d{2}$"},
            {"US Date", "^\\d{1,2}/\\d{1,2}/\\d{4}$"},
            {"Alphanumeric", "^[a-zA-Z0-9]+$"},
            {"Strong Password", "^(?=.*[0-9])(?=.*[a-z])(?=.*[A-Z])(?=.*[@#$%^&+=!])(?=\\S+$).{8,}$"},
            {"Whitespace", "^\\s+.*\\s+$"}
        };
        
        // Test each pattern against all test data
        for (String data : testData) {
            System.out.println("\nTesting: " + data);
            
            for (String[] patternInfo : patterns) {
                String patternName = patternInfo[0];
                String patternStr = patternInfo[1];
                
                Pattern pattern = Pattern.compile(patternStr);
                Matcher matcher = pattern.matcher(data);
                
                if (matcher.matches()) {
                    System.out.println("  Matches " + patternName + " pattern");
                }
            }
        }
    }
}
```

## Performance Considerations

Regular expressions are powerful but can have performance implications:

```java
import java.util.regex.Matcher;
import java.util.regex.Pattern;

public class RegexPerformanceExample {
    public static void main(String[] args) {
        // Generate a large text
        StringBuilder sb = new StringBuilder();
        for (int i = 0; i < 100000; i++) {
            sb.append("line ").append(i).append(": some text here\n");
        }
        String largeText = sb.toString();
        
        // Measure time for compiled pattern
        long startTime = System.currentTimeMillis();
        
        Pattern pattern = Pattern.compile("line (\\d+):");
        Matcher matcher = pattern.matcher(largeText);
        
        int count = 0;
        while (matcher.find()) {
            count++;
        }
        
        long endTime = System.currentTimeMillis();
        System.out.println("Compiled pattern found " + count + " matches in " + 
                          (endTime - startTime) + " ms");
        
        // Measure time for String.matches() (compiles pattern each time)
        startTime = System.currentTimeMillis();
        
        count = 0;
        String[] lines = largeText.split("\n");
        for (String line : lines) {
            if (line.matches("line \\d+:.*")) {
                count++;
            }
        }
        
        endTime = System.currentTimeMillis();
        System.out.println("String.matches() found " + count + " matches in " + 
                          (endTime - startTime) + " ms");
    }
}
```

Performance tips:

1. **Compile patterns once**: Reuse compiled `Pattern` objects instead of using `String.matches()` repeatedly
2. **Be specific**: More specific patterns are usually faster
3. **Avoid catastrophic backtracking**: Be careful with nested quantifiers
4. **Use non-capturing groups** `(?:pattern)` when you don't need to extract the matched text
5. **Use possessive quantifiers** (`++`, `*+`, `?+`, `{n,m}+`) or atomic groups `(?>pattern)` for better performance in some cases

## Best Practices

1. **Compile patterns once**: Store compiled patterns as static final variables when used repeatedly
2. **Use appropriate flags**: Choose the right flags for your use case
3. **Test thoroughly**: Regex can be tricky; test with various inputs
4. **Document complex patterns**: Add comments to explain complex regex
5. **Handle exceptions**: Always handle `PatternSyntaxException`
6. **Consider alternatives**: For simple string operations, methods like `contains()` or `startsWith()` might be faster
7. **Use named groups**: For better readability in complex patterns
8. **Be careful with user input**: Don't compile regex patterns from untrusted user input
9. **Limit backtracking**: Use possessive quantifiers or atomic groups for complex patterns
10. **Consider performance**: Be aware of the computational cost of complex patterns

## Conclusion

Java's regular expression support through the `java.util.regex` package provides powerful tools for pattern matching and text manipulation. By understanding the syntax, classes, and best practices, you can effectively use regex to solve a wide range of text processing problems in your Java applications.

Regular expressions are particularly useful for input validation, data extraction, text parsing, and search/replace operations. While they can be complex, mastering regex will significantly enhance your text processing capabilities in Java.