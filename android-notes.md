
**_compileSdkVersion_** - version of the API the app is compiled against

**_targetSdkVersion_** - not related to how your app is compiled or what APIs you can utilize but to indicate (upto & including) the version you have tested against

**_minSdkVersion_** - earliest version of Android that the app can run on

**ideally:** `minSdkVersion (lowest possible) <= targetSdkVersion == compileSdkVersion (latest SDK)`


`apply plugin: kotlin-kapt` to add annotation processors (see JSR 269) support in Kotlin then libraries such as Dagger can be used however the dependency has to be added using `kapt` configuration, (_kapt_: kotlin annotations processing tool)

`apply plugin: kotlin-android-extensions` to add support for view binding with Kotlin Android Extensions

`classpath 'com.android.tools.build:gradle:3.4.2'` Android Studio Gradle Plugin that provides integration of Gradle into Android Studio. The version will match the version of Android Studio being used

`distributionUrl=https\://services.gradle.org/distributions/gradle-5.1.1-all.zip` in _gradle-wrapper.properties_ which specifies the version of Gradle being used

## Resources
- Google’s Maven Repository: https://maven.google.com/web/index.html
- MVN Repository: https://mvnrepository.com/
