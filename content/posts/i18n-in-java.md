---
title: "Internationalisation in Java"
date: "2024-07-28T18:19:24+01:00"
draft: false
tags: ["java", "programming", "internationalisation", "localisation"]
categories: ["java", "programming"]
---

## Overview

Internationalisation (i18n) refers to designing and preparing software to be easily adapted to various languages, regions, and cultures without requiring engineering changes to the code. This is usually followed by localisation (l10n), which involves adapting the internationalised software to a specific locale, including translating the text, adjusting for local conventions, and modifying other locale-specific elements.

The goal is to make the software flexible enough to support multiple locales by separating the core logic from locale-specific elements like language and cultural conventions.

## ResourceBundle

Localising a text message in a plain Java program is a bit straightforward using the ResourceBundle class provided by the programming language. The ResourceBundle class makes it easy to load, locale-specific, key-value attributes defined in properties files. These property files are known as **resource bundles**.

```java
ResourceBundle resources =
		ResourceBundle.getBundle("messages", Locale.FRANCE);
String greeting = resources.getString("greeting.hello");
assertEquals("Bonjour le monde", greeting);

String greetingUsername =
		MessageFormat.format(resources.getString("greeting.username"), "Ayo");
assertEquals("Bonjour Ayo", greetingUsername);
```

## Using i8n-resource-bundle

Another method of localising a text message in Java program is using third-party libraries. One of such library is i18n-resource-bundle. This library is an implementation over the ResourceBundle discussed earlier.

### Dependency

Add the dependency below in your pom.xml if you are using Maven

```xml
<dependency>
    <groupId>io.github.jelilio</groupId>
    <artifactId>i18n-resource-bundle</artifactId>
    <version>0.0.2</version>
</dependency>
```

If you prefer Gradle, use this instead;

```groovy
implementation 'io.github.jelilio:i18n-resource-bundle:0.0.2'
```

### MessageSource

i8n-resource-bundle provides a **MessageSource** interface that defines several methods for resolving messages. It has two implementations, **ResourceBundleMessageSource** and **ReloadableResourceBundleMessageSource**. Both implementations accesses the resource bundles using specified basenames similar to Java ResourceBundle. The ResourceBundleMessageSource resolve messages form resource bundles for different locales by relying on Java’s ResourceBundle implementation in combination with MessageFormat for message parsing.

```java
ResourceBundleMessageSource messageSource =
		new ResourceBundleMessageSource();
messageSource.setBasenames("messages");

String greeting = messageSource
		.getMessage("greeting.hello", null, Locale.FRANCE);
assertEquals("Bonjour le monde", greeting);

String greetingUsername = messageSource
		.getMessage("greeting.username", new String[]{"Ayo"}, Locale.FRANCE);
assertEquals("Bonjour Ayo", greetingUsername);
```

### ReloadableResourceBundleMessageSource

Unlike ResourceBundleMessageSource, the ReloadableResourceBundleMessageSource uses Java’s Properties instances as its custom data structure for messages loading them using a different strategies that allow the reloading of the properties files based on timestamp changes and a specific character encoding without the need of restarting the application.

```java
ReloadableResourceBundleMessageSource messageSource =
		new ReloadableResourceBundleMessageSource();
messageSource.setBasenames("messages");

String greeting = messageSource
		.getMessage("greeting.hello", null, Locale.FRANCE);
assertEquals("Bonjour le monde", greeting);

String greetingUsername = messageSource
		.getMessage("greeting.username", new String[]{"Ayo"}, Locale.US);
assertEquals("Bonjour Ayo", greetingUsername);
```

## Conclusion

In this brief guide, we learned to implement internationalisation (i18n) in Java applications using the *ResourceBundle* and i18n-resource-bundle. we learned how the resource bundles are resolved based on supplied locale names and saw an example in action.

## References

- [The Java™ Tutorials - Lesson: Isolating Locale-Specific Data](https://docs.oracle.com/javase/tutorial/i18n/resbundle/index.html)
- [i18n-resource-bundle](https://github.com/jelilio/i18n-resource-bundle)
- [Source code: i18n-in-java](https://github.com/jelilio/i18n-in-java)
