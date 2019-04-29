
**_compileSdkVersion_** - version of the API the app is compiled against

**_targetSdkVersion_** - not related to how your app is compiled or what APIs you can utilize but to indicate (upto & including) the version you have tested against

**_minSdkVersion_** - earliest version of Android that the app can run on

**ideally:** `minSdkVersion (lowest possible) <= targetSdkVersion == compileSdkVersion (latest SDK)`


`apply plugin: kotlin-kapt` to add annotation processors (see JSR 269) support in Kotlin then libraries such as Dagger can be used however the dependency has to be added using `kapt` configuration

`apply plugin: kotlin-android-extensions` to add support for view binding with Kotlin Android Extensions
