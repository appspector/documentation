# Installation

Each app you want to use with AppSpector SDK you have to register on the web ([https://app.appspector.com](https://app.appspector.com?utm_source=android_readme)).
After adding the application navigate to app settings and copy API key.

## Add AppSpector SDK to your project
<!-- integration-manual-start -->
[![GitHub release](https://img.shields.io/github/release/appspector/android-sdk.svg)](https://github.com/appspector/android-sdk/releases)

#### Add AppSpector dependency to buildscript in your project-level build.gradle
```groovy
buildscript {
  repositories {
      jcenter()
      google()
      maven {
          url "https://maven.appspector.com/artifactory/android-sdk"
      }
  }
  
  dependencies {
      classpath "com.appspector:android-sdk-plugin:1.+"
  }
}
```

#### Modify your app-level build.gradle
```groovy
apply plugin: 'com.android.application'
// Put AppSpector plugin after Android plugin
apply plugin: 'com.appspector.sdk'

// Add AppSpector maven repository
repositories {
    maven {
        url "https://maven.appspector.com/artifactory/android-sdk"
    }
}

dependencies {
    implementation "com.appspector:android-sdk:1.+"
}
```

In case when you don't want to have AppSpector SDK in your release APK use AppSpector NO-OP artifact
```groovy
dependencies {
    debugImplementation "com.appspector:android-sdk:1.+"
    releaseImplementation "com.appspector:android-sdk-noop:1.+"
}
```
<!-- integration-manual-end -->

## Initialize AppSpector SDK
<!-- initialization-manual-start -->
```java
import android.app.Application;

import com.appspector.sdk.AppSpector;

public class AmazingApp extends Application {

   @Override
   public void onCreate() {
      super.onCreate();
      
      // We recommend to start AppSpector from Application#onCreate method
      
      // You can start all monitors
      AppSpector
            .build(this)
            .withDefaultMonitors()            
            .run("API_KEY");
            
      // Or you can select monitors that you want to use
      AppSpector
            .build(this)
            .addPerformanceMonitor()
            .addHttpMonitor()
            // If specific monitor is not added then this kind of data won't be tracked and available on the web
            .addLogMonitor()
            .addScreenshotMonitor()
            .addSQLMonitor()
            .run("API_KEY");
   }

}
```
<!-- initialization-manual-end -->

## Build and Run

Build your project and see everything work! When your app is up and running you can go to [https://app.appspector.com](https://app.appspector.com?utm_source=android_readme) and connect to your application session.
