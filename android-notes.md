
**_compileSdkVersion_** - version of the API the app is compiled against

**_targetSdkVersion_** - not related to how your app is compiled or what APIs you can utilize but to indicate (upto & including) the version you have tested against

**_minSdkVersion_** - earliest version of Android that the app can run on

**ideally:** `minSdkVersion (lowest possible) <= targetSdkVersion == compileSdkVersion (latest SDK)`


`apply plugin: kotlin-kapt` to add annotation processors (see JSR 269) support in Kotlin then libraries such as Dagger can be used however the dependency has to be added using `kapt` configuration, (_kapt_: kotlin annotations processing tool)

`apply plugin: kotlin-android-extensions` to add support for view binding with Kotlin Android Extensions

`classpath 'com.android.tools.build:gradle:3.4.2'` Android Studio Gradle Plugin that provides integration of Gradle into Android Studio. The version will match the version of Android Studio being used

`distributionUrl=https\://services.gradle.org/distributions/gradle-5.1.1-all.zip` in _gradle-wrapper.properties_ which specifies the version of Gradle being used

- *Accelerometer*: for horizontal and lateral movements
- *Gyro*: for rotational speeds
- *Compass*: which way is north
- *Barometer*: for elevation

## QA
**Start Activity 1, Activity 2, Activity 3, Activity 4, Activity 5. Then startActivity(Activity 1) from within Activity 5 what will happen to stack?**
Activity 1 is brought to the screen and Activity 5,4,3,2 are not destroyed unless the intent sets the FLAG_ACTIVITY_CLEAR_TOP flag.

## Archive
**How to overcome problems with AsyncTask e.g. Activity destroyed after rotation and not available anymore?**
- A common solution to this is retained fragments (only retained in case of rotation not in case OS clears app for memory, see Ranch book for discussion on retained fragments)
- Foolproof solution is EventBus
- Loaders & AsyncTaskLoader
- Check for cancellation of AsyncTask

AsyncTaskLoaders, like all Loaders, are intended to solve the rotation problem, which is when an AsyncTask is created on an Activity, then the device is rotated before the task has completed that AsyncTask will be lost with the destruction of the Activity but instance of AsyncTaskLoader can be retrieved using the LoaderManager (in a similar manner to the way it was created). 

It's true that all Loaders currently don't support publishing progress, so if this is a requirement for your situation then you should consider an alternative.

**What is the relationship between the life cycle of an AsyncTask and an Activity? What problems can this result in? How can these problems be avoided?**

An AsyncTask is not tied to the life cycle of the Activity that contains it. So, for example, if you start an AsyncTask inside an Activity and the user rotates the device, the Activity will be destroyed (and a new Activity instance will be created) but the AsyncTask will not die but instead goes on living until it completes.

Then, when the AsyncTask does complete, rather than updating the UI of the new Activity, it updates the former instance of the Activity (i.e., the one in which it was created but that is not displayed anymore!). This can lead to an Exception (of the type java.lang.IllegalArgumentException: View not attached to window manager if you use, for instance, findViewById to retrieve a view inside the Activity).

There’s also the potential for this to result in a memory leak since the AsyncTask maintains a reference to the Activity, which prevents the Activity from being garbage collected as long as the AsyncTask remains alive.

For these reasons, using AsyncTasks for long-running background tasks is generally a bad idea . Rather, for long-running background tasks, a different mechanism (such as a service) should be employed.


## Links
- Google’s Maven Repository: https://maven.google.com/web/index.html
- MVN Repository: https://mvnrepository.com/
- RxJava: https://github.com/ReactiveX/Rxjava
- RxJava Docs: http://reactivex.io/RxJava/javadoc/io/reactivex/package-summary.html
- RxAndroid: https://github.com/ReactiveX/RxAndroid
- Android Tutorials by MindOrks https://mindorks.com/android-tutorial
- CodePath Android Guide https://guides.codepath.com/android
- Dagger: https://github.com/google/dagger

