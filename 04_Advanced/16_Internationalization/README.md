# Java Internationalization (i18n) and Localization (l10n)

Internationalization (i18n) is the process of designing and developing software that can be adapted to different languages and regions without engineering changes. Localization (l10n) is the process of adapting internationalized software for a specific region or language by translating text and adding locale-specific components.

Java provides comprehensive support for internationalization through various classes and interfaces in the `java.util` and `java.text` packages.

## Locales

A `Locale` object represents a specific geographical, political, or cultural region. It is the foundation of all internationalization in Java.

### Creating Locales

```java
import java.util.Locale;

public class LocaleExample {
    public static void main(String[] args) {
        // Get the default locale
        Locale defaultLocale = Locale.getDefault();
        System.out.println("Default Locale: " + defaultLocale);
        
        // Create locales using constructors
        Locale usLocale = new Locale("en", "US");
        Locale frLocale = new Locale("fr", "FR");
        Locale jpLocale = new Locale("ja", "JP");
        
        // Create locales using constants
        Locale usLocaleConstant = Locale.US;
        Locale frLocaleConstant = Locale.FRANCE;
        Locale jpLocaleConstant = Locale.JAPAN;
        
        // Create a locale with language, country, and variant
        Locale customLocale = new Locale("en", "US", "WIN");
        
        // Display locale information
        displayLocaleInfo(usLocale);
        displayLocaleInfo(frLocale);
        displayLocaleInfo(jpLocale);
        displayLocaleInfo(customLocale);
        
        // Get available locales
        Locale[] availableLocales = Locale.getAvailableLocales();
        System.out.println("\nNumber of available locales: " + availableLocales.length);
        
        // Print first 5 available locales
        System.out.println("\nSample of available locales:");
        for (int i = 0; i < 5 && i < availableLocales.length; i++) {
            System.out.println(availableLocales[i]);
        }
    }
    
    private static void displayLocaleInfo(Locale locale) {
        System.out.println("\nLocale: " + locale);
        System.out.println("Language: " + locale.getLanguage() + " - " + locale.getDisplayLanguage());
        System.out.println("Country: " + locale.getCountry() + " - " + locale.getDisplayCountry());
        System.out.println("Variant: " + locale.getVariant());
        System.out.println("Display Name: " + locale.getDisplayName());
    }
}
```

### Setting the Default Locale

```java
import java.util.Locale;

public class DefaultLocaleExample {
    public static void main(String[] args) {
        // Get the current default locale
        Locale originalDefault = Locale.getDefault();
        System.out.println("Original default locale: " + originalDefault);
        
        // Set a new default locale
        Locale.setDefault(Locale.FRANCE);
        System.out.println("New default locale: " + Locale.getDefault());
        
        // Restore the original default locale
        Locale.setDefault(originalDefault);
        System.out.println("Restored default locale: " + Locale.getDefault());
    }
}
```

## Resource Bundles

Resource bundles contain locale-specific objects, such as translated text, images, and other locale-specific resources. Java provides the `ResourceBundle` class to handle resource bundles.

### Creating Resource Bundles

Resource bundles are typically stored in properties files. For example:

**messages_en_US.properties**:
```
greeting=Hello
farewell=Goodbye
question=How are you?
```

**messages_fr_FR.properties**:
```
greeting=Bonjour
farewell=Au revoir
question=Comment allez-vous?
```

**messages_ja_JP.properties**:
```
greeting=こんにちは
farewell=さようなら
question=お元気ですか?
```

### Using Resource Bundles

```java
import java.util.Locale;
import java.util.ResourceBundle;

public class ResourceBundleExample {
    public static void main(String[] args) {
        // Define locales
        Locale usLocale = new Locale("en", "US");
        Locale frLocale = new Locale("fr", "FR");
        Locale jpLocale = new Locale("ja", "JP");
        
        // Load resource bundles for different locales
        ResourceBundle usBundle = ResourceBundle.getBundle("messages", usLocale);
        ResourceBundle frBundle = ResourceBundle.getBundle("messages", frLocale);
        ResourceBundle jpBundle = ResourceBundle.getBundle("messages", jpLocale);
        
        // Display messages in different languages
        System.out.println("US English:");
        System.out.println("  " + usBundle.getString("greeting"));
        System.out.println("  " + usBundle.getString("question"));
        System.out.println("  " + usBundle.getString("farewell"));
        
        System.out.println("\nFrench:");
        System.out.println("  " + frBundle.getString("greeting"));
        System.out.println("  " + frBundle.getString("question"));
        System.out.println("  " + frBundle.getString("farewell"));
        
        System.out.println("\nJapanese:");
        System.out.println("  " + jpBundle.getString("greeting"));
        System.out.println("  " + jpBundle.getString("question"));
        System.out.println("  " + jpBundle.getString("farewell"));
        
        // Resource bundle fallback
        System.out.println("\nResource bundle fallback:");
        try {
            // Try to get a bundle for a locale that doesn't have a specific resource bundle
            Locale deLocale = new Locale("de", "DE");
            ResourceBundle deBundle = ResourceBundle.getBundle("messages", deLocale);
            
            // This will use the default locale or the base bundle if available
            System.out.println("  Fallback locale: " + deBundle.getLocale());
            System.out.println("  " + deBundle.getString("greeting"));
        } catch (Exception e) {
            System.out.println("  Error: " + e.getMessage());
        }
    }
}
```

### List Keys in a Resource Bundle

```java
import java.util.Enumeration;
import java.util.Locale;
import java.util.ResourceBundle;

public class ResourceBundleKeysExample {
    public static void main(String[] args) {
        // Load a resource bundle
        ResourceBundle bundle = ResourceBundle.getBundle("messages", Locale.US);
        
        // Get all keys in the bundle
        Enumeration<String> keys = bundle.getKeys();
        
        System.out.println("Keys in the resource bundle:");
        while (keys.hasMoreElements()) {
            String key = keys.nextElement();
            System.out.println("  " + key + " = " + bundle.getString(key));
        }
    }
}
```

### Creating a Custom Resource Bundle

You can also create custom resource bundles by extending the `ResourceBundle` class:

```java
import java.util.Enumeration;
import java.util.Hashtable;
import java.util.ResourceBundle;

public class CustomResourceBundleExample {
    public static void main(String[] args) {
        // Use our custom resource bundle
        ResourceBundle bundle = new MyResourceBundle();
        
        System.out.println("Custom resource bundle:");
        System.out.println("  " + bundle.getString("greeting"));
        System.out.println("  " + bundle.getString("farewell"));
    }
    
    // Custom resource bundle class
    static class MyResourceBundle extends ResourceBundle {
        private Hashtable<String, String> resources;
        
        public MyResourceBundle() {
            resources = new Hashtable<>();
            resources.put("greeting", "Hello from custom bundle");
            resources.put("farewell", "Goodbye from custom bundle");
        }
        
        @Override
        protected Object handleGetObject(String key) {
            return resources.get(key);
        }
        
        @Override
        public Enumeration<String> getKeys() {
            return resources.keys();
        }
    }
}
```

## Formatting

Java provides several classes for formatting locale-sensitive data such as numbers, dates, and messages.

### Number Formatting

```java
import java.text.NumberFormat;
import java.util.Locale;

public class NumberFormattingExample {
    public static void main(String[] args) {
        // Sample number
        double number = 1234567.89;
        
        // Define locales
        Locale usLocale = Locale.US;
        Locale frLocale = Locale.FRANCE;
        Locale deLocale = Locale.GERMANY;
        
        // Format as general number
        NumberFormat usNumberFormat = NumberFormat.getNumberInstance(usLocale);
        NumberFormat frNumberFormat = NumberFormat.getNumberInstance(frLocale);
        NumberFormat deNumberFormat = NumberFormat.getNumberInstance(deLocale);
        
        System.out.println("Number formatting:");
        System.out.println("  US: " + usNumberFormat.format(number));
        System.out.println("  France: " + frNumberFormat.format(number));
        System.out.println("  Germany: " + deNumberFormat.format(number));
        
        // Format as currency
        NumberFormat usCurrencyFormat = NumberFormat.getCurrencyInstance(usLocale);
        NumberFormat frCurrencyFormat = NumberFormat.getCurrencyInstance(frLocale);
        NumberFormat deCurrencyFormat = NumberFormat.getCurrencyInstance(deLocale);
        
        System.out.println("\nCurrency formatting:");
        System.out.println("  US: " + usCurrencyFormat.format(number));
        System.out.println("  France: " + frCurrencyFormat.format(number));
        System.out.println("  Germany: " + deCurrencyFormat.format(number));
        
        // Format as percentage
        NumberFormat usPercentFormat = NumberFormat.getPercentInstance(usLocale);
        NumberFormat frPercentFormat = NumberFormat.getPercentInstance(frLocale);
        NumberFormat dePercentFormat = NumberFormat.getPercentInstance(deLocale);
        
        System.out.println("\nPercentage formatting:");
        System.out.println("  US: " + usPercentFormat.format(0.75));
        System.out.println("  France: " + frPercentFormat.format(0.75));
        System.out.println("  Germany: " + dePercentFormat.format(0.75));
    }
}
```

### Date and Time Formatting

```java
import java.text.DateFormat;
import java.util.Date;
import java.util.Locale;

public class DateFormattingExample {
    public static void main(String[] args) {
        // Current date and time
        Date now = new Date();
        
        // Define locales
        Locale usLocale = Locale.US;
        Locale frLocale = Locale.FRANCE;
        Locale jpLocale = Locale.JAPAN;
        
        // Format date in different styles
        System.out.println("Date formatting:");
        
        // Short style
        DateFormat usShortDate = DateFormat.getDateInstance(DateFormat.SHORT, usLocale);
        DateFormat frShortDate = DateFormat.getDateInstance(DateFormat.SHORT, frLocale);
        DateFormat jpShortDate = DateFormat.getDateInstance(DateFormat.SHORT, jpLocale);
        
        System.out.println("\nShort date style:");
        System.out.println("  US: " + usShortDate.format(now));
        System.out.println("  France: " + frShortDate.format(now));
        System.out.println("  Japan: " + jpShortDate.format(now));
        
        // Medium style
        DateFormat usMediumDate = DateFormat.getDateInstance(DateFormat.MEDIUM, usLocale);
        DateFormat frMediumDate = DateFormat.getDateInstance(DateFormat.MEDIUM, frLocale);
        DateFormat jpMediumDate = DateFormat.getDateInstance(DateFormat.MEDIUM, jpLocale);
        
        System.out.println("\nMedium date style:");
        System.out.println("  US: " + usMediumDate.format(now));
        System.out.println("  France: " + frMediumDate.format(now));
        System.out.println("  Japan: " + jpMediumDate.format(now));
        
        // Long style
        DateFormat usLongDate = DateFormat.getDateInstance(DateFormat.LONG, usLocale);
        DateFormat frLongDate = DateFormat.getDateInstance(DateFormat.LONG, frLocale);
        DateFormat jpLongDate = DateFormat.getDateInstance(DateFormat.LONG, jpLocale);
        
        System.out.println("\nLong date style:");
        System.out.println("  US: " + usLongDate.format(now));
        System.out.println("  France: " + frLongDate.format(now));
        System.out.println("  Japan: " + jpLongDate.format(now));
        
        // Full style
        DateFormat usFullDate = DateFormat.getDateInstance(DateFormat.FULL, usLocale);
        DateFormat frFullDate = DateFormat.getDateInstance(DateFormat.FULL, frLocale);
        DateFormat jpFullDate = DateFormat.getDateInstance(DateFormat.FULL, jpLocale);
        
        System.out.println("\nFull date style:");
        System.out.println("  US: " + usFullDate.format(now));
        System.out.println("  France: " + frFullDate.format(now));
        System.out.println("  Japan: " + jpFullDate.format(now));
        
        // Time formatting
        DateFormat usTime = DateFormat.getTimeInstance(DateFormat.MEDIUM, usLocale);
        DateFormat frTime = DateFormat.getTimeInstance(DateFormat.MEDIUM, frLocale);
        DateFormat jpTime = DateFormat.getTimeInstance(DateFormat.MEDIUM, jpLocale);
        
        System.out.println("\nTime formatting:");
        System.out.println("  US: " + usTime.format(now));
        System.out.println("  France: " + frTime.format(now));
        System.out.println("  Japan: " + jpTime.format(now));
        
        // Date and time formatting
        DateFormat usDateTime = DateFormat.getDateTimeInstance(
                DateFormat.MEDIUM, DateFormat.MEDIUM, usLocale);
        DateFormat frDateTime = DateFormat.getDateTimeInstance(
                DateFormat.MEDIUM, DateFormat.MEDIUM, frLocale);
        DateFormat jpDateTime = DateFormat.getDateTimeInstance(
                DateFormat.MEDIUM, DateFormat.MEDIUM, jpLocale);
        
        System.out.println("\nDate and time formatting:");
        System.out.println("  US: " + usDateTime.format(now));
        System.out.println("  France: " + frDateTime.format(now));
        System.out.println("  Japan: " + jpDateTime.format(now));
    }
}
```

### Using SimpleDateFormat

```java
import java.text.SimpleDateFormat;
import java.util.Date;
import java.util.Locale;

public class SimpleDateFormatExample {
    public static void main(String[] args) {
        // Current date and time
        Date now = new Date();
        
        // Define locales
        Locale usLocale = Locale.US;
        Locale frLocale = Locale.FRANCE;
        
        // Custom date formats
        System.out.println("Custom date formatting:");
        
        // Format: Month Day, Year
        SimpleDateFormat usFormat = new SimpleDateFormat("MMMM d, yyyy", usLocale);
        System.out.println("  US format: " + usFormat.format(now));
        
        // Format: Day Month Year
        SimpleDateFormat frFormat = new SimpleDateFormat("d MMMM yyyy", frLocale);
        System.out.println("  French format: " + frFormat.format(now));
        
        // Format with time
        SimpleDateFormat timeFormat = new SimpleDateFormat("EEEE, MMMM d, yyyy 'at' h:mm a", usLocale);
        System.out.println("  With time: " + timeFormat.format(now));
        
        // Format with time zone
        SimpleDateFormat tzFormat = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss z", usLocale);
        System.out.println("  With timezone: " + tzFormat.format(now));
    }
}
```

### Java 8 Date and Time API with Localization

```java
import java.time.LocalDate;
import java.time.LocalDateTime;
import java.time.LocalTime;
import java.time.format.DateTimeFormatter;
import java.time.format.FormatStyle;
import java.util.Locale;

public class Java8DateTimeFormattingExample {
    public static void main(String[] args) {
        // Current date and time
        LocalDate today = LocalDate.now();
        LocalTime now = LocalTime.now();
        LocalDateTime dateTime = LocalDateTime.now();
        
        // Define locales
        Locale usLocale = Locale.US;
        Locale frLocale = Locale.FRANCE;
        Locale jpLocale = Locale.JAPAN;
        
        // Formatting dates with predefined formatters
        System.out.println("Java 8 Date formatting:");
        
        // LocalDate formatting
        System.out.println("\nLocalDate formatting:");
        System.out.println("  US (SHORT): " + 
                today.format(DateTimeFormatter.ofLocalizedDate(FormatStyle.SHORT).withLocale(usLocale)));
        System.out.println("  France (MEDIUM): " + 
                today.format(DateTimeFormatter.ofLocalizedDate(FormatStyle.MEDIUM).withLocale(frLocale)));
        System.out.println("  Japan (LONG): " + 
                today.format(DateTimeFormatter.ofLocalizedDate(FormatStyle.LONG).withLocale(jpLocale)));
        
        // LocalTime formatting
        System.out.println("\nLocalTime formatting:");
        System.out.println("  US (SHORT): " + 
                now.format(DateTimeFormatter.ofLocalizedTime(FormatStyle.SHORT).withLocale(usLocale)));
        System.out.println("  France (MEDIUM): " + 
                now.format(DateTimeFormatter.ofLocalizedTime(FormatStyle.MEDIUM).withLocale(frLocale)));
        
        // LocalDateTime formatting
        System.out.println("\nLocalDateTime formatting:");
        System.out.println("  US (SHORT): " + 
                dateTime.format(DateTimeFormatter.ofLocalizedDateTime(FormatStyle.SHORT).withLocale(usLocale)));
        System.out.println("  France (MEDIUM): " + 
                dateTime.format(DateTimeFormatter.ofLocalizedDateTime(FormatStyle.MEDIUM).withLocale(frLocale)));
        
        // Custom patterns
        System.out.println("\nCustom pattern formatting:");
        DateTimeFormatter customFormatter = DateTimeFormatter.ofPattern("EEEE, MMMM d, yyyy 'at' h:mm a", usLocale);
        System.out.println("  Custom US format: " + dateTime.format(customFormatter));
        
        DateTimeFormatter frCustomFormatter = DateTimeFormatter.ofPattern("EEEE d MMMM yyyy 'à' HH'h'mm", frLocale);
        System.out.println("  Custom French format: " + dateTime.format(frCustomFormatter));
    }
}
```

### Message Formatting

```java
import java.text.MessageFormat;
import java.util.Date;
import java.util.Locale;

public class MessageFormattingExample {
    public static void main(String[] args) {
        // Define locales
        Locale usLocale = Locale.US;
        Locale frLocale = Locale.FRANCE;
        
        // Set up parameters for the message
        Object[] messageArgs = {
            "John",
            new Date(),
            7,
            1234.56
        };
        
        // Format messages with different patterns
        String pattern = "On {1,date,long}, {0} sent {2,number,integer} messages and spent {3,number,currency}.";
        
        // Format with US locale
        MessageFormat usFormatter = new MessageFormat(pattern, usLocale);
        String usMessage = usFormatter.format(messageArgs);
        System.out.println("US message: " + usMessage);
        
        // Format with French locale
        MessageFormat frFormatter = new MessageFormat(pattern, frLocale);
        String frMessage = frFormatter.format(messageArgs);
        System.out.println("French message: " + frMessage);
        
        // Format with choice format
        String choicePattern = "{0} {1,choice,0#sent no messages|1#sent one message|1<sent {1,number,integer} messages}.";
        MessageFormat choiceFormatter = new MessageFormat(choicePattern, usLocale);
        
        Object[][] choiceExamples = {
            {"John", 0},
            {"Mary", 1},
            {"Bob", 5}
        };
        
        System.out.println("\nChoice format examples:");
        for (Object[] example : choiceExamples) {
            System.out.println("  " + choiceFormatter.format(example));
        }
    }
}
```

## Collation (String Comparison)

Collation is the process of determining the sorting order of strings according to the rules of a specific locale.

```java
import java.text.Collator;
import java.util.Arrays;
import java.util.Locale;

public class CollationExample {
    public static void main(String[] args) {
        // Sample strings to sort
        String[] words = {
            "cafe", "café", "apple", "zebra", "banana", "Ångström", "Aegean"
        };
        
        // Define locales
        Locale usLocale = Locale.US;
        Locale frLocale = Locale.FRANCE;
        
        // Sort using default string comparison (binary comparison)
        String[] defaultSorted = words.clone();
        Arrays.sort(defaultSorted);
        
        System.out.println("Default sorting (binary comparison):");
        for (String word : defaultSorted) {
            System.out.println("  " + word);
        }
        
        // Sort using US English collation
        Collator usCollator = Collator.getInstance(usLocale);
        String[] usSorted = words.clone();
        Arrays.sort(usSorted, usCollator);
        
        System.out.println("\nUS English collation:");
        for (String word : usSorted) {
            System.out.println("  " + word);
        }
        
        // Sort using French collation
        Collator frCollator = Collator.getInstance(frLocale);
        String[] frSorted = words.clone();
        Arrays.sort(frSorted, frCollator);
        
        System.out.println("\nFrench collation:");
        for (String word : frSorted) {
            System.out.println("  " + word);
        }
        
        // Collation strength
        System.out.println("\nCollation strength examples:");
        
        // Primary strength (base letters only)
        Collator primaryCollator = Collator.getInstance(usLocale);
        primaryCollator.setStrength(Collator.PRIMARY);
        System.out.println("  Primary strength (cafe == café): " + 
                primaryCollator.equals("cafe", "café"));
        
        // Secondary strength (base letters and accents)
        Collator secondaryCollator = Collator.getInstance(usLocale);
        secondaryCollator.setStrength(Collator.SECONDARY);
        System.out.println("  Secondary strength (cafe == café): " + 
                secondaryCollator.equals("cafe", "café"));
        
        // Tertiary strength (base letters, accents, and case)
        Collator tertiaryCollator = Collator.getInstance(usLocale);
        tertiaryCollator.setStrength(Collator.TERTIARY);
        System.out.println("  Tertiary strength (cafe == Cafe): " + 
                tertiaryCollator.equals("cafe", "Cafe"));
    }
}
```

## Character Encoding and Unicode

```java
import java.nio.charset.Charset;
import java.nio.charset.StandardCharsets;
import java.util.SortedMap;

public class CharsetExample {
    public static void main(String[] args) {
        // Get the default charset
        Charset defaultCharset = Charset.defaultCharset();
        System.out.println("Default charset: " + defaultCharset);
        
        // List available charsets
        SortedMap<String, Charset> availableCharsets = Charset.availableCharsets();
        System.out.println("\nNumber of available charsets: " + availableCharsets.size());
        
        // Print some common charsets
        System.out.println("\nCommon charsets:");
        System.out.println("  UTF-8: " + StandardCharsets.UTF_8);
        System.out.println("  UTF-16: " + StandardCharsets.UTF_16);
        System.out.println("  ISO-8859-1: " + StandardCharsets.ISO_8859_1);
        System.out.println("  US-ASCII: " + StandardCharsets.US_ASCII);
        
        // Encoding and decoding example
        String original = "Hello, 世界! Привет! こんにちは!";
        System.out.println("\nOriginal string: " + original);
        
        // Encode to different charsets
        byte[] utf8Bytes = original.getBytes(StandardCharsets.UTF_8);
        byte[] utf16Bytes = original.getBytes(StandardCharsets.UTF_16);
        byte[] iso8859Bytes = original.getBytes(StandardCharsets.ISO_8859_1);
        
        System.out.println("\nEncoded sizes:");
        System.out.println("  UTF-8: " + utf8Bytes.length + " bytes");
        System.out.println("  UTF-16: " + utf16Bytes.length + " bytes");
        System.out.println("  ISO-8859-1: " + iso8859Bytes.length + " bytes");
        
        // Decode back to strings
        String utf8Decoded = new String(utf8Bytes, StandardCharsets.UTF_8);
        String utf16Decoded = new String(utf16Bytes, StandardCharsets.UTF_16);
        String iso8859Decoded = new String(iso8859Bytes, StandardCharsets.ISO_8859_1);
        
        System.out.println("\nDecoded strings:");
        System.out.println("  UTF-8: " + utf8Decoded);
        System.out.println("  UTF-16: " + utf16Decoded);
        System.out.println("  ISO-8859-1: " + iso8859Decoded + " (notice the lost characters)");
    }
}
```

## Time Zones

```java
import java.text.SimpleDateFormat;
import java.util.Date;
import java.util.TimeZone;

public class TimeZoneExample {
    public static void main(String[] args) {
        // Get the current date and time
        Date now = new Date();
        
        // Create a formatter that includes the time zone
        SimpleDateFormat formatter = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss z");
        
        // Get the default time zone
        TimeZone defaultTimeZone = TimeZone.getDefault();
        System.out.println("Default time zone: " + defaultTimeZone.getID());
        
        // Display the current time in different time zones
        String[] timeZoneIds = {
            "America/New_York",
            "Europe/London",
            "Europe/Paris",
            "Asia/Tokyo",
            "Australia/Sydney"
        };
        
        System.out.println("\nCurrent time in different time zones:");
        for (String timeZoneId : timeZoneIds) {
            TimeZone timeZone = TimeZone.getTimeZone(timeZoneId);
            formatter.setTimeZone(timeZone);
            System.out.println("  " + timeZoneId + ": " + formatter.format(now));
        }
        
        // Get all available time zone IDs
        String[] availableIDs = TimeZone.getAvailableIDs();
        System.out.println("\nNumber of available time zones: " + availableIDs.length);
        
        // Display time zones for a specific offset (UTC+1)
        System.out.println("\nTime zones for UTC+1:");
        for (String id : TimeZone.getAvailableIDs(3600000)) { // 3600000 ms = 1 hour
            System.out.println("  " + id);
        }
    }
}
```

## Java 8 Time Zone Support

```java
import java.time.LocalDateTime;
import java.time.ZoneId;
import java.time.ZonedDateTime;
import java.time.format.DateTimeFormatter;

public class Java8TimeZoneExample {
    public static void main(String[] args) {
        // Current date and time
        LocalDateTime now = LocalDateTime.now();
        
        // Create a formatter
        DateTimeFormatter formatter = DateTimeFormatter.ofPattern("yyyy-MM-dd HH:mm:ss z");
        
        // Display the current time in different time zones
        String[] zoneIds = {
            "America/New_York",
            "Europe/London",
            "Europe/Paris",
            "Asia/Tokyo",
            "Australia/Sydney"
        };
        
        System.out.println("Current time in different time zones:");
        for (String zoneId : zoneIds) {
            ZonedDateTime zonedDateTime = now.atZone(ZoneId.of(zoneId));
            System.out.println("  " + zoneId + ": " + formatter.format(zonedDateTime));
        }
        
        // Get all available zone IDs
        System.out.println("\nNumber of available zone IDs: " + ZoneId.getAvailableZoneIds().size());
        
        // Display some zone IDs for Europe
        System.out.println("\nSome European zone IDs:");
        ZoneId.getAvailableZoneIds().stream()
            .filter(id -> id.startsWith("Europe/"))
            .limit(5)
            .forEach(id -> System.out.println("  " + id));
    }
}
```

## Currency

```java
import java.util.Currency;
import java.util.Locale;
import java.util.Set;

public class CurrencyExample {
    public static void main(String[] args) {
        // Get the currency for a specific locale
        Currency usCurrency = Currency.getInstance(Locale.US);
        Currency ukCurrency = Currency.getInstance(Locale.UK);
        Currency jpCurrency = Currency.getInstance(Locale.JAPAN);
        Currency frCurrency = Currency.getInstance(Locale.FRANCE);
        
        // Display currency information
        System.out.println("Currency information:");
        displayCurrencyInfo("US", usCurrency);
        displayCurrencyInfo("UK", ukCurrency);
        displayCurrencyInfo("Japan", jpCurrency);
        displayCurrencyInfo("France", frCurrency);
        
        // Get a currency by its code
        Currency btcCurrency = Currency.getInstance("BTC"); // Bitcoin
        displayCurrencyInfo("Bitcoin", btcCurrency);
        
        // Get all available currencies
        Set<Currency> availableCurrencies = Currency.getAvailableCurrencies();
        System.out.println("\nNumber of available currencies: " + availableCurrencies.size());
    }
    
    private static void displayCurrencyInfo(String country, Currency currency) {
        System.out.println("\n" + country + " currency:");
        System.out.println("  Code: " + currency.getCurrencyCode());
        System.out.println("  Symbol: " + currency.getSymbol());
        System.out.println("  Default fraction digits: " + currency.getDefaultFractionDigits());
        System.out.println("  Display name: " + currency.getDisplayName());
    }
}
```

## Internationalization Best Practices

1. **Design with Internationalization in Mind**: Plan for internationalization from the beginning of your project.

2. **Externalize Strings**: Keep all user-visible strings in resource bundles, not hardcoded in your code.

3. **Use Proper Encoding**: Always use UTF-8 or UTF-16 for text storage and processing.

4. **Format Dates, Numbers, and Currencies Correctly**: Use the appropriate formatting classes for locale-sensitive data.

5. **Handle Text Direction**: Support both left-to-right (LTR) and right-to-left (RTL) text for languages like Arabic and Hebrew.

6. **Consider Cultural Differences**: Be aware of cultural differences in colors, symbols, and imagery.

7. **Test with Different Locales**: Test your application with various locales to ensure proper functionality.

8. **Allow for Text Expansion**: Some languages require more space than others when translated.

9. **Use Unicode for Character Handling**: Unicode supports characters from virtually all writing systems.

10. **Separate Logic from Presentation**: Keep business logic separate from presentation to make localization easier.

11. **Use Standard Libraries**: Leverage Java's built-in internationalization support rather than creating custom solutions.

12. **Document Locale-Specific Code**: Clearly document any code that is specific to certain locales.

## Conclusion

Java provides comprehensive support for internationalization and localization through its various classes and interfaces. By properly using these features, you can create applications that can be easily adapted to different languages and regions, providing a better user experience for a global audience.

Remember that internationalization is not just about translating text; it also involves handling different formats for dates, numbers, currencies, and other locale-sensitive data, as well as considering cultural differences and preferences.