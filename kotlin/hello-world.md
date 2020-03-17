# HelloWorld

project level build.gradle

```java
buildscript {
  ext.kotlin_version = '1.1.2-4'
  repositories {
    maven { url 'https://maven.google.com'}
    jcenter()
  }

  dependencies {
    ...
    classpath 'org.jetbrains.kotlin:kotlin-gradle-plugin:$kotlin_version'
  }
}

allprojects {
  repositories {
    jcenter()
    maven { url 'https://maven.google.com' }
    mavenCentral()
  }
}
```

module level build.gradle

```java
apply plugin: "kotlin-android"
apply plugin: "kotlin-android-extensions"

dependecies {
  compile "org.jetbrains.kotlin:kotlin-stdlib:$kotlin_version"
}
```

hello world

```java
fun main(args: Array<String>) {
    println("Hello, world!")
}
```