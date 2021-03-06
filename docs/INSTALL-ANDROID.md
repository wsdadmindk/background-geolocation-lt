# Android Installation

You will have to install the plugin by manually downloading [a Release](https://github.com/transistorsoft/background-geolocation-lt/releases) from this repository.  The plugin is not currently submitted to a package manager (eg: jCenter)

Create a folder in the root of your application project, eg: `/Libraries` and place the extracted **`background-geolocation`** folder into it:

eg: :open_file_folder: **`Libraries/background-geolocation-lt`**

## Gradle Configuration

### :open_file_folder: **`settings.gradle`**

```diff
+include ':background-geolocation'
+project(':background-geolocation').projectDir = new File(rootProject.projectDir, './Libraries/background-geolocation-lt/android/background-geolocation')
```

### :open_file_folder: **`android/build.gradle`**

```diff
buildscript {
    ext {
        buildToolsVersion = "29.0.6"  // <-- set as desired
        compileSdkVersion = 29        // <-- set as desired
        targetSdkVersion = 29         // <-- set as desired
        minSdkVersion = 19            // <-- set as desired
+       appCompatVersion = "1.1.0"    // <-- AndroidX compatibility.  set as desired.  For pre-androidX, specify `com.android.support` version.
+       googlePlayServicesLocationVersion = "17.0.0"
    }
    ...
}

allprojects {
    repositories {
        google()
        jcenter()
+       maven {
+           // REQUIRED background-geolocation
+           url("${project(':background-geolocation').projectDir}/libs")
+       }
    }
}
```

### :open_file_folder: **`app/build.gradle`**

```diff
android {
+    compileSdkVersion rootProject.ext.compileSdkVersion
     defaultConfig {
+        targetSdkVersion rootProject.ext.targetSdkVersion
         .
         .
         .
     }
     .
     .
     .
}

// Get a reference to background-geolocation project.
+Project background_geolocation = project(':background-geolocation')

// Apply background-geolocation's custom app.gradle file
+apply from: "${background_geolocation.projectDir}/app.gradle"

dependencies {
     .
     .
     .
+    implementation background_geolocation
}

```

You can now import and use the SDK in your application.  See the [Example](../README.md#android).

## AndroidManifest.xml

### :open_file_folder: **`android/app/src/main/AndroidManifest.xml`**

```diff
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="com.transistorsoft.backgroundgeolocation.react">

  <application
    android:name=".MainApplication"
    android:allowBackup="true"
    android:label="@string/app_name"
    android:icon="@mipmap/ic_launcher"
    android:theme="@style/AppTheme">

    <!-- react-native-background-geolocation licence -->
+   <meta-data android:name="com.transistorsoft.locationmanager.license" android:value="YOUR_LICENCE_KEY_HERE" />
    .
    .
    .
  </application>
</manifest>

```

:information_source: [Purchase a License](http://www.transistorsoft.com/shop/products/native-background-geolocation)

## Proguard Config

If you've set **`minifyEnabled`** in your `android/app/build.gradle`, be sure to apply the SDK's **Proguard Rules**:

### :open_file_folder: `android/app/build.gradle`)

```diff
/**
 * Run Proguard to shrink the Java bytecode in release builds.
 */
def enableProguardInReleaseBuilds = true
.
.
.
android {
    .
    .
    .
    buildTypes {
        release {
            minifyEnabled true
            proguardFiles getDefaultProguardFile("proguard-android.txt"), "proguard-rules.pro"
            // Add following proguardFiles (leave existing one above untouched)
+           proguardFiles "${background_geolocation.projectDir}/proguard-rules.pro"
            signingConfig signingConfigs.release
        }
    }
}
```


