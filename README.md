Overview
========
This Gradle plugin allows you to automatically *resolve and add* your native dependencies to `jniLibs` directory.

![screen shot 2014-04-10 at 15 29 40](https://cloud.githubusercontent.com/assets/1793238/2668930/6969020e-c0bd-11e3-9116-ae879991e356.png)

Usage
=====
Apply the plugin in your `build.gradle` after the regular `android` plugin, then declare the list of your native dependencies.
```groovy
buildscript {
  repositories {
    mavenCentral()
  }
  dependencies {
    classpath 'com.android.tools.build:gradle:1.0.0'
    classpath 'com.nabilhachicha:android-native-dependencies:0.1.2'
  }
}

apply plugin: 'android'
apply plugin: 'android-native-dependencies'

native_dependencies {
    artifact 'com.snappydb:snappydb-native:0.2.+:armeabi'
    artifact 'com.snappydb:snappydb-native:0.2.+:x86'
}

dependencies {
    //regular Jar dependencies ...
}

```

Convention
==========

> The artifact DSL follows the naming convention for Maven artifacts.
> thus, you can use one of the following syntax:

- abbreviated *group:name:version[:classifier]*

```groovy
//adding x86 classifier will resolve only intel's (.so) lib
native_dependencies {
    artifact 'com.snappydb:snappydb-native:0.2+:x86'
}

//omit the classifier will resolve all supported architectures
native_dependencies {
    artifact 'com.snappydb:snappydb-native:0.2+'
}
```

- map-style notation


```groovy
//adding x86 classifier will resolve only intel's (.so) lib
native_dependencies {
    artifact group: 'com.snappydb', name: 'snappydb-native', version: '0.2+', classifier: 'x86'
}

//omit the classifier will resolve all supported architectures
native_dependencies {
    artifact group: 'com.snappydb', name: 'snappydb-native', version: '0.2+'
}
```
    
In both notations, *classifier* is optional. this means that when omitted, the plugin try to resolve the artifact for *all* architectures: `armeabi`, `armeabi-v7a`, `x86` and `mips`.

- Disable `lib` prefix:

You may want to disable the plugin from prefixing the resolved artifact with `lib`  (when copying into **jniLibs** directory) 
If there is a `lib` in the name, `lib` won't be added again.
```groovy
native_dependencies {
    artifact ('com.snappydb:snappydb-native:0.2.+:armeabi') {
           shouldPrefixWithLib=false
    }
}
```
in this example the ARM dependency will have the name `snappydb-native.so` rather than `libsnappydb-native.so`

Or you may want to disable all.
```groovy
native_dependencies {
    addLibPrefixToArtifact=false

    artifact ('com.snappydb:snappydb-native:0.2.+:armeabi')
    artifact ('com.snappydb:snappydb-native:0.2.+:x86')
}
```

You are able to use both together as well.
```groovy
native_dependencies {
    addLibPrefixToArtifact=false

    artifact ('com.snappydb:snappydb-native:0.2.+:armeabi') {
           shouldPrefixWithLib=true
    }
    artifact ('com.snappydb:snappydb-native:0.2.+:x86')
}
```
in this example, all other dependencies will be disabled with `lib` except of `'com.snappydb:snappydb-native:0.2.+:armeabi'`.

- Sets `cache` time:

You may want to set the length of time that changing modules will be cached, by default it's 24 hours.
```groovy
native_dependencies {
    artifact ('com.snappydb:snappydb-native:0.2.+:armeabi') {
           cachePeriodTimeValue=0
           cachePeriodTimeUnits="seconds"
    }
}
```
in this example the dependency will update in each run.

Or you may want to set all dependecies with same changing frequences.
```groovy
native_dependencies {
    cacheChangingModulesFor 10, "minutes"

    artifact ('com.snappydb:snappydb-native:0.2.+:armeabi')
    artifact ('com.snappydb:snappydb-native:0.2.+:x86')
}
```

You are able to use both together as well.
```groovy
native_dependencies {
    cacheChangingModulesFor 10, "minutes"

    artifact ('com.snappydb:snappydb-native:0.2.+:armeabi') {
           cachePeriodTimeValue=0
           cachePeriodTimeUnits="seconds"
    }

    artifact ('com.snappydb:snappydb-native:0.2.+:x86')
}
```
in this example, all other dependencies will be set with `10 minutes` except of `'com.snappydb:snappydb-native:0.2.+:armeabi'`.

Tasks
=====

The plugin adds the `resolveNativeDependencies` task to your project, this task runs automatically whenever you build your project. 
##### Note: #####
The plugin uses [Gradle incremental task](http://www.gradle.org/docs/current/dsl/org.gradle.api.tasks.incremental.IncrementalTaskInputs.html) system to run faster if the configuration didn't change since the last build.

License
=======

    Copyright 2014 Nabil Hachicha

    Licensed under the Apache License, Version 2.0 (the "License");
    you may not use this file except in compliance with the License.
    You may obtain a copy of the License at

       http://www.apache.org/licenses/LICENSE-2.0

    Unless required by applicable law or agreed to in writing, software
    distributed under the License is distributed on an "AS IS" BASIS,
    WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
    See the License for the specific language governing permissions and
    limitations under the License.

[![Build Status](https://travis-ci.org/nhachicha/android-native-dependencies.svg?branch=master)](https://travis-ci.org/nhachicha/android-native-dependencies)


