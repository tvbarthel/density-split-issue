# Status: Fixed

This repository was aimed at reproducing a bug reported that is now fixed.

https://issuetracker.google.com/u/2/issues/37076144

The sample project has been updated with the version `3.0.1` of the `com.android.tools.build:gradle`
plugin and the version `27.0.3` of the build tools version. With these changes, the issue is no more replicable.
The fixed is confirmed :)

# Issue: density APK splits

When using the density APK split, the manifest merger seems to add three extra permissions even if they are not declared in the app manifest.

```xml
<android:uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
<android:uses-permission android:name="android.permission.READ_PHONE_STATE" />
<android:uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE" />
```

# Bug tracker
https://code.google.com/p/android/issues/detail?id=197928

# Step to reproduce the issue

* Execute `gradlew assembleRelease`
* In the `/app/build/outputs/logs/manifest-merger-release-report.txt`, observe the following lines:

```
android:uses-permission#android.permission.WRITE_EXTERNAL_STORAGE
IMPLIED from ...\split-density-bug\app\src\main\AndroidManifest.xml:2:1-20:12 reason:  has a targetSdkVersion < 4
android:uses-permission#android.permission.READ_PHONE_STATE
IMPLIED from ...\split-density-bug\app\src\main\AndroidManifest.xml:2:1-20:12 reason:  has a targetSdkVersion < 4
android:uses-permission#android.permission.READ_EXTERNAL_STORAGE
IMPLIED from ...\split-density-bug\app\src\main\AndroidManifest.xml:2:1-20:12 reason:  requested WRITE_EXTERNAL_STORAGE
```

* In the `/app/build/intermediates/manifests/full/hdpi/release/AndroidManifest.xml`, observe the three extra permissions:

```xml
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="fr.tvbarthel.experiment.splitdensitybug"
    android:versionCode="3000001"
    android:versionName="1.0" >

    <uses-sdk
        android:minSdkVersion="16"
        android:targetSdkVersion="25" />

    <compatible-screens>
        <screen
            android:screenDensity="hdpi"
            android:screenSize="small" />
        <screen
            android:screenDensity="hdpi"
            android:screenSize="normal" />
        <screen
            android:screenDensity="hdpi"
            android:screenSize="large" />
        <screen
            android:screenDensity="hdpi"
            android:screenSize="xlarge" />
    </compatible-screens>

    <android:uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
    <android:uses-permission android:name="android.permission.READ_PHONE_STATE" />
    <android:uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE" />

    <application
        android:allowBackup="true"
        android:icon="@mipmap/ic_launcher"
        android:label="@string/app_name"
        android:supportsRtl="true"
        android:theme="@style/AppTheme" >
        <activity android:name="fr.tvbarthel.experiment.splitdensitybug.MainActivity" >
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />

                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>
    </application>

</manifest>
```

* In the `/app/build/intermediates/manifests/full/release/AndroidManifest.xml`, observe the expected manifest:

```xml
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="fr.tvbarthel.experiment.splitdensitybug"
    android:versionCode="1000001"
    android:versionName="1.0" >

    <uses-sdk
        android:minSdkVersion="16"
        android:targetSdkVersion="25" />

    <application
        android:allowBackup="true"
        android:icon="@mipmap/ic_launcher"
        android:label="@string/app_name"
        android:supportsRtl="true"
        android:theme="@style/AppTheme" >
        <activity android:name="fr.tvbarthel.experiment.splitdensitybug.MainActivity" >
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />

                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>
    </application>

</manifest>
```

# Temporary solution
https://github.com/tvbarthel/density-split-issue/pull/1
