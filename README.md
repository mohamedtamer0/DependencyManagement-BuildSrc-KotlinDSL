<h1 align="center">
Better Dependency Management Using buildSrc + Kotlin DSL
</h1>

- Dependency management for better reusability and easy maintenance

---

<p align="center">
<img src="https://user-images.githubusercontent.com/51374446/152661025-8ae0ba54-9760-4686-9fd1-f5bdf5aa8321.png"/>
</p>

---

# Introduction
- Since the start of using Android Studio, Gradle has become a central place for managing many things like dependencies, config data, flavours, etc.
And maintaining the dependencies across multi-module projects has become a challenge over time.
Let’s see in detail how the evolution of dependencies management across multi-module projects happened and the problems faced.

---

# What is buildSrc?

- buildSrc is a directory at the project root level which contains build info.
We can use this directory to enable kotlin-dsl and write logic related to custom configuration and share them across the project.
It was one of the most used approaches in recent days because of its testability,

> The directory buildSrc is treated as an included build. Upon discovery of the directory,
Gradle automatically compiles and tests this code and puts it in the classpath of your build script.
For multi-project builds there can be only one buildSrc directory, which has to sit in the root project directory.
buildSrc should be preferred over script plugins as it is easier to maintain, refactor and test the code — Gradle Docs -> https://docs.gradle.org/current/userguide/organizing_gradle_projects.html#sec:build_sources

---

- Let’s see how to create a buildSrc directory
# Right Click on Root Project → New → Directory → buildSrc

<p align="center">
<img src="https://user-images.githubusercontent.com/51374446/152661241-d977c965-546f-4d33-91de-80d4fa3c0243.gif"/>
</p>

---

# Enable Kotlin DSL in `buildSrc`
- As Kotlin DSL is adopted from the parent Kotlin language most of its syntax would be similar.
For this in `buildSrc` first, create an empty file build.gradle.kts and then enable the option of ‘kotlin-dsl’ in `buildSrc`

```gradle
import org.gradle.kotlin.dsl.`kotlin-dsl`

plugins {
    `kotlin-dsl`
}

repositories {
    mavenCentral()
}
```
Click on sync now to finish the set-up.
Once it’s done now we can create our Kotlin DSL scripts for custom build logic. Here let’s do configuration build data and dependency management set-up.

---

## Step 1 :

- Create a `src` directory structure in `buildSrc` directory

<p align="center">
<img src="https://user-images.githubusercontent.com/51374446/152661391-6d5d6ea3-5e03-4bac-bf0c-864214aa5e9f.gif"/>
</p>

---

## Step 2:

- In this directory, we can create our Kotlin files.
Let’s create an object class called `Versions.kt` where we define all the versions related to plugins, dependencies, etc

```kotlin
object Versions {
    const val coreKts = "1.7.0"
    const val appCompat = "1.4.1"
    const val material = "1.5.0"
    const val constraintLayout = "2.1.3"
    const val jUnit = "4.13.2"
    const val jUnitExt = "1.1.3"
    const val espresso = "3.4.0"
}
```

---

## Step 3:

- Let’s create a new file `Dependencies.kt` to define all our plugins, dependencies, etc
The file will be looking as below

```kotlin
/**
 * To define dependencies
 */

object Deps {
    val coreKts = "androidx.core:core-ktx:${Versions.coreKts}"
    val appCompat = "androidx.appcompat:appcompat:${Versions.appCompat}"
    val material = "com.google.android.material:material:${Versions.material}"
    val constraintLayout = "androidx.constraintlayout:constraintlayout:${Versions.constraintLayout}"
    val jUnit = "junit:junit:${Versions.jUnit}"
    val jUnitExt = "androidx.test.ext:junit:${Versions.jUnitExt}"
    val espresso = "androidx.test.espresso:espresso-core:${Versions.espresso}"
}
```

- While creating a file we can have auto-suggestions poping from `Versions.kt as shown below. So we can easily navigate between file to check the specific values

<p align="center">
<img src="https://user-images.githubusercontent.com/51374446/152661458-911e2fc7-0a78-4e31-8383-ec4b2f256871.gif"/>
</p>

---

## Step 4:
- We can also create a `ConfigData.kt` for any build configuration related info

```kotlin
object ConfigData {
    const val compileSdkVersion = 32
    //const val buildToolsVersion = "30.0.3"
    const val minSdkVersion = 21
    const val targetSdkVersion = 32
    const val versionCode = 1
    const val versionName = "1.0"
}
```

---

## Step 5:

- Finally using the data defined in the above classes in the module-level build.gradle.kts file

```gradle
plugins {
    id 'com.android.application'
    id 'org.jetbrains.kotlin.android'
}

android {
    compileSdk(ConfigData.compileSdkVersion)

    defaultConfig {
        applicationId "com.example.dependencymanagement_buildsrc_kotlindsl"
        minSdk(ConfigData.minSdkVersion)
        targetSdk(ConfigData.targetSdkVersion)
        versionCode = ConfigData.versionCode
        versionName = ConfigData.versionName

        testInstrumentationRunner "androidx.test.runner.AndroidJUnitRunner"
    }

    buildTypes {
        release {
            minifyEnabled false
            proguardFiles getDefaultProguardFile('proguard-android-optimize.txt'), 'proguard-rules.pro'
        }
    }
    compileOptions {
        sourceCompatibility JavaVersion.VERSION_1_8
        targetCompatibility JavaVersion.VERSION_1_8
    }
    kotlinOptions {
        jvmTarget = '1.8'
    }
}

dependencies {

    implementation(Deps.coreKts)
    implementation(Deps.appCompat)
    implementation(Deps.material)
    implementation(Deps.constraintLayout)
    testImplementation(Deps.jUnit)
    androidTestImplementation(Deps.jUnitExt)
    androidTestImplementation(Deps.espresso)
}
```

- The final directory structure would be something like below

`buildSrc`
```gradle
├── src
│ └── main
│ ─└── kotlin
│ ──└── Dependencies.kt
├── build.gradle.kts
```
> Note: Though we can do this declaration of versions, dependencies, etc in a single class it would be better to have segregation of classes so that the code will be clean

---

## Summary:
- `buildSrc + Kotlin DSL` is the best option for dependency management. As it’s a class-level declaration it can be easily tested. The auto-suggestion support and code navigation would help in saving time. Maintain separate classes for each purpose. This approach could be easy for better reusability and easy maintenance.





