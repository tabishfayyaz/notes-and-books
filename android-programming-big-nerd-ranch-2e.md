## Android Programming: The Big Nerd Ranch Guide 2e

Please [purchase the book](https://www.amazon.com/Android-Programming-Nerd-Ranch-Guide/dp/0134171454/) and support the authors.

**minSdkVersion**: The value below which the OS should refuse to install the app.

**targetSdkVersion:** The value tells Android which API level your app was designed to run on. Most often this will be the latest Android release. When would you lower the target SDK? New SDK releases can change how your app appears on a device or even how the OS behaves behind the scenes. If you have already designed ap app, you should confirm that it works as expected on new releases. Not increasing the target SDK when a new version of Android is released ensures that your app will still run with the appearance and behavior of the targeted version on which it worked well. Check the documentation to see where problems might arise. Then you can modify your app to work with the new behavior or lower the target SDK.

**compileSdkVersion:** The compile SDK version is private information between you and the compiler (this setting is not used to update the AndroidManifest.xml where as minimum and target SDK are placed in the manifest in order to advertise those value to the OS). The compile SDK version, or build target, specifies which version to use when building your own code. When Android Studio is looking to find the classes and methods you refer to in your imports, the build target determines which SDK version it checks against. The best choice for a build target is the latest API level. However, you can change the build target of an existing application if you need to. For instance, you might want to update the build target when yet another version of Android is released so that you can make sure of the new methods and classes introduced in that version of Android.

The difference between minimum SDK and build SDK version leaves you with a compatibility gap to self-manage.

A **resource** is a piece of your application that is not code - things like image files, audio files, and XML files.

You can also have multiple strings files in a project. As long as the file is located in res/values/, has a resources root element, and contains child string elements, your strings will be found and used appropriately.

**AppCompactActivity** is a subclass of Android’s Activity class that provides compatibility support for older versions of Android.

Separating code into classes helps you design and understand the application as a whole; you can think in terms of classes instead of individual variables and methods.

Similarly, separating classes into model, view and controller (**MVC**) layers helps you design and understand an application; you can think in terms of layers instead of individual classes.

The **device configuration** is a set of characteristics that describe the current state of an individual device. The characteristics that make up the configuration include screen orientation, screen density, screen size, keyboard type, dock mode, language, and more.

Screen density is a fixed component of the device configuration; it cannot change at runtime. On the other hand, some components, like screen orientation, can change at runtime.

The -land suffix is another example of a configuration qualifier. Configuration qualifiers on res subdirectories are how Android identifies which resources best match the current device configuration. Android destroys the current Activity on rotation and stars fresh to ensure that it has the resources that best match the new configuration. Note that Android destroys the current activity and creates a new one whenever any runtime configuration change occurs. A change in keyboard availability or language could also occur at runtime, but a change in screen orientation is the runtime change that occurs most frequently.

Note that the types that you can save to and restore from a **Bundle** are primitive types and classes that implement the Serializable or Parcelable interfaces. It is usually a bad practice to put objects of custom types into a Bundle because the data might be stale when you get it back out. It is a better choice to use some other kind of storage for the data and put a primitive identifier into the Bundle instead.

**Fragments:** introduced in API level 11 along with the first Android tablets and the sudden need for UI flexibility

If you add the fragment to the activity’s layout, you hardwire the fragment and its view to the activity’s view and cannot swap out that fragment during the activity’s lifetime.

The second approach, adding the fragment to the activity’s code, is more complex. But it is the only way to have control at runtime over your fragments. Thus to achieve real UI flexibility you must add your fragment in code.

The **onAttach(Activity), onCreate(Bundle), and onCreateView(...)** methods are called when you add the fragment to the FragmentManager. 

The **onActivityCreated(...)** method is called after the hosting activity’s **onCreate(...)** method has executed. 
If a fragment is added to an activity that is already running, that fragment gets calls to **onAttach(Activity), onCreate(Bundle), onCreateView(...), onActivityCreated(Bundle), onStart()**, and then **onResume().**

If you have a significant number of fragments on screen at once, your code will be littered with fragment transactions and unclear responsibility. A good rule of thumb is to have no more than two or three fragments on the screen at a time.

A **style** is an XML resource that contains attributes that describe how a widget should look and behave.

A **theme** is a collection of styles. Structurally, a theme is itself a style resource whose attributes point to other style resources.

**Screen density** means how many pixels appear within a constant area of the display, dots per inch = dpi

**Screen size** means amount of physical space available for displaying an interface, screen's diagonal, inch

**Screen resolution** means number of pixels available in the display

**density-independent pixel = dp or dip**, virtual pixel that is independent of the screen density. When your display is a higher density, density independent pixels will expand to fill a larger number of screen pixels. One dp is always 1/160th of an inch on a device’s screen.

**scale-independent pixel = sp**, scale-independent pixels are density-independent pixels that also take into account the user’s font size preference. You will almost always use sp to set display text size.

When an attribute name begins with layout_, that attribute is a direction to that widget’s parent. They tell the parent layout how to arrange the child element within the parent.

**LinearLayout** makes two passes to set the width (or height) of a view. In the first pass, it looks at layout_width. In the next pass, it distributes any left over extra space based on the values for layout_weight. What if you want the LinearLayout to allocate exactly 50% of its width to each view? You simply skip the first pass by setting the layout_width of each widget to 0dp instead of wrap_content. This leaves layout_weight the sole component in the LinearLayout’s decision making.

**ViewHolder** does one thing: it holds on to a View.

**Why use Fragment Arguments?** Why not just set an instance variable on the Fragment when it is created? Because it would not always works. When the OS re-creates your fragment, either across a configuration change or when user has switched out of your app and the OS reclaims memory, all of your instance variables will be lost. 

One option is to use the saved instance state mechanism. You can store the ID for example as a normal instance variable, save the ID in onSaveInstanceState(Bundle) and snag it from the Bundle in onCreate(Bundle). However, this solution is hard to maintain. In future if you add another argument, you may not remember to save the argument in onSaveInstanceState(Bundle).

**Back button**: takes you to where you were last (temporal navigation)

**Up button**: take you up the app hierarchy, achieved by setting a parentActivityName (ancestral navigation)
Even though they accomplish the same result, behind the scenes they are doing very different things. Depending on the application, navigating up may pop the user back multiple activities in the back stack. When the user navigates up from a child activity, an intent like the following is created:

Intent intent = new Intent(this, ParentActivity.class);
intent.addFlags(Intent.FLAG_ACTIVITY_CLEAR_TOP);
startActivity(intent);
finish();

FLAG_ACTIVITY_CLEAR_TOP tells Android to look for an existing instance of the activity in the stack, and if there is one, pop every other activity off the stack so that the activity being started will be top most.

An unfortunate side effect of the way ancestral navigation is implemented in Android is that the activity that you navigate up to will be completed re-created from scratch. This means that any instance variables will be lost and it also means that any saved instance state will be lost as well. This parent activity is seen as a completely new activity.

**Explicit intent:** you specify the class of the activity to start, and the OS will start it

**Implicit Intent:** you describe to the OS the job that you want done. The OS then starts the activity that has advertised itself as capable of doing that job.

Instances of **ContentProvider** wrap databases and make it available to other applications. You can access a ContentProvider through a ContentResolver.

With methods like **Context.getFileStreamPath(String)** and **Context.getFilesDir()**, you can do the same thing with regular files, too (which will live in a subfolder adjacent to the databases subfolder your SQLite database lives in).

If you are storing files to share with other apps or receiving files from other apps (files like stored pictures), you need to store them on external storage instead. There are two kinds of external storage: primary, and everything else. All android devices have at least one location for external storage: the primary location, which is located in the folder returned by **Environment.getExternalStorageDirectory()**. This may be an SD card, but nowadays it is more commonly integrated into the device itself. Some devices may have additional external storage. That would fall under “everything else”. 

Screen size qualifiers (small, normal, large, xlarge) were deprecated in Android 3.2 in favor of qualifiers that allow you to test for the dimensions of the device. With a -sw600dp qualifier, you are saying, “Use this resource on any device whose smallest dimension is 600dp or greater”. This is a good rule of thumb for specifying a tablet-sized screen.

wXXXdp: width is greater than or equal to XXX dp

hXXXdp: height is greater than or equal to XXX dp

swXXXdp: width or height (whichever is smaller) greater than or equal to XXX dp. The height or width may swap depending on the orientation of the device. sw stands for smallest width

Let’s say that you wanted to specify a layout that would only be used if the display were at least 300dp wide. In that case, you could use an available width qualifier and put your layout file in res/layout-w300dp. You can also do the same thing for height by using an “h”

**HandlerThread:** A thread that has a looper.

**Resources** can store sounds. With sounds stored as resources, you can do all the usual resource things, like having a different sounds for different orientations, languages, versions of Android, and so on. However if you have lots of sounds dealing with them all one by one in the resources system would be cumbersome. It would be nice to just ship them all out in one big folders, but resources do not let you do this, nor do they allow you to give your resources anything other than a totally flat structure. This is exactly what the assets system is great for. **Assets** are like a little filesystem that ships with your packaged application. With assets, you can use whatever folder structure you want. Since they give you this kind of organizational ability, assets are commonly used for loading graphics and sound in applications that have a lot of those things, like games. 

Asset file paths will not work if you try to open them with a File; you must use them with an **AssetManager**.

Remember that earlier we said that Android has two systems, assets and resources? Well, the resources system has a nice big lookup system. But some big resources are too big to fit inside that system. So these big resources - images and raw sound files, usually - are actually stored in the assets system. Under the hood, Android opens these things itself using the AssetManager.**openNonAsset** methods, not all of which are publicly available. 

Android has a variety of different audio streams, each of which has its own independent volume settings. This is why turning down the music does not also turn down your alarms. Check out the documentation for the AUDIO_* constants in **AudioManager** to see the other options. STREAM_MUSIC will put you on the same volume setting as music and games on the device.

The main benefit of using a **SoundPool** over some other method of playing audio is that SoundPool is responsive: when you tell it to play a sound, it will play the sound immediately, with no lag.

**Parcelable**, like **Serializable**, is an API for saving an object out to a stream of bytes. Objects are stashed in Java by putting them in a **Bundle**, or by marking them **Serializable** so that they can be serialized, or by implementing the **Parcelable** interface. 

Fragments have a feature that you can use to keep its instances alive across configuration change: retainInstances
The retained state is only entered into when two conditions are met:
- setRetainInstance(true) has been called on the fragments
- The hosting activity is being destroyed for a configuration change (typically rotation)

A fragment is only in the retained state for an extremely brief interval - the time between being detached from the old activity & being reattached to the new activity that is immediately created.

You may wonder why you would not retain every fragment?
- The first reasons is simply that retained fragments are more complicated than unretained fragments. When something goes wrong with them, it takes longer to get to the bottom of what went wrong.
- The other reasons is that fragments that handle rotation using saved instance state handle all lifecycle situations, but retained fragments only handle the case when an activity is destroyed for a configuration change. If your activity is destroyed because the OS needs to reclaim memory, then all your retained fragments are destroyed, too, which may mean you lose some data.

If your app does not have any problems with rotation, it is because the default behavior of **onSaveInstanceState(..)** is working. This is what **onSaveInstanceState(...)** was designed to do - save out and restore the UI state of your app.

The major difference between overriding **Fragment.onSaveInstanceState(...)** and retaining the fragment is how long the preserved data lasts. If it only needs to last long enough to survive configurations changes, then retaining the fragment is much less work. If an activity is destroyed to reclaim memory after the user has been away for a while, then any retained fragments are destroyed just like their unretained brethren. Therefore, if you have something in your activity or fragment that should last a long time, then you should tie it to the activity record’s lifetime by overriding **onSaveInstanceState(Bundle)** to save its state so that you can restore it later.

**Styles** allow you to define a set of attributes in one place & then apply them to as many widgets as you want. The downside of style is that you have to apply them to each & every widget, one at a time. In an app with lots of buttons that could be a huge task. That is where themes come in. In a theme you can say e.g. “I want all buttons to use this style.” **Theme** attributes can store a reference to concrete resources, such as colors, and they can also store a reference to styles. 

The AppCompat library comes with three main themes:
- Theme.AppCompat - a dark theme
- Theme.AppCompat.Light - a light theme
- Theme.AppCompat.Light.DarkActionBar - a light theme with a dark toolbar

To discover different style attributes you often have to Cmd-Click theme names and go up the inheritance & look up the style attributes to use. There is not much documentation for styling attributes.

android:background=”?attr/colorAccent” - When referencing a concrete value in XML, such as a color, you use the @ notation. The ? notation says to use the resource that the **colorAccent** attribute on your theme points to.

Android calls anything that is intended to be drawn to the screen a drawable, whether it is an abstract shape, a clever bit of code that subclasses the Drawable class, or a bitmap image. State list drawables, shape drawables and layer list drawables are few types.

Layer list drawables allow you to combine two XML drawables into one.

Launchable main activities are simple activities with intent filters that include a MAIN action and a LAUNCHER category.

Each application opened from Android’s default launcher app gets its own **task**. A task is a stack of activities. When you press the Back button, you are popping the top activity off of this stack. By default, new activities are started in the current task. Sometimes, when you start an activity, you want the activity added to the current task. Other times, you want it started in a new task.

Android app starts life with a main thread which sits in an infinite loop and waits for events initiated by the user or the system. Then it executes code in response to those vents as they occur.

If you use **AsyncTasks** to load data, you are responsible for managing its lifecycle during configuration changes, such as rotation, and stashing its data somewhere that lives through them. Often, this is simplified by using **setRetainInstance(true)** on a Fragment but there are still situations where you have to intervene and code you have to write in order to ensure that everything happens correctly. Such situations include the user pressing the Back button while the AsyncTask is running, or the fragment that launched the AsyncTask getting destroyed during execution by the OS due to a low-memory situation. Using a **Loader** is an alternative solution that takes some (but not all) of this responsibility off your hands. A loader is designed to load some kind of data (an object) from some source. This source could be a disk, a database, a ContentProvider, the network, or another process. **AsyncTaskLoader** is an abstract Loader that uses an AsyncTask to move the work of loading data to another thread. 

Why would you use a loader instead of, say, as AsyncTask directly? Well the most compelling reason is that the **LoaderManager** will keep your component’s loaders alive, along with their data, between configuration changes like rotation. **LoaderManager** is responsible for starting, stopping, and maintaining the lifecycle of any **Loaders** associated with your component. If, after any configuration change, you initialize a loader that has already finished loading its data, it can deliver that data immediately rather than trying to fetch it again. This works whether your fragment is retained or not, which can make your life easier because you do not have to consider the lifecycle complications that retained fragments can introduce.

**Message:**

*what* - a user-defined int that describes the message

*obj* - a user-specified object to be sent with the message

*target* - the Handler that will handle the message

It is better to build the message by calling Handler.obtainmessage(..). You pass the other message fields into this method, and it automatically sets the target to the Handler object the method was called on for you. Usually, you do not set a message’s target Handler by hand. 

By default, the **Handler** will attach itself to the Looper for the thread it is initialized in.

A service’s intents are called commands.

**AlarmManager** is a system service that can send Intents for you.

There are two methods available for setting repeating alarms: AlarmManager.setRepeating() and AlarmManager.setInexactRepeating(). If your interval requirements are flexible you should give the system flexibility to group your alarm with others. This is called “inexact repeating”. This allows the system to batch your alarm with others and minimize the amount of wake time needed.

**JobScheduler** allows you to define services to run particular jobs, and then schedule them to run only when particular conditions apply.

Custom views have two broad categories:

*simple* - it has no child views, will almost always perform custom rendering. **View** is a blank canvas so pick it as its superclass. 

*composite* - typically manage child views but do not perform custom rendering. Instead rendering is delegated to each child view. Choose an appropriate superclass such as **FrameLayout**.

**Canvas** - The methods you call on Canvas determine where and what you draw - a line, a circle, a word, or a rectangle

**Paint** - The class determines how the above operations are done - whether shapes are filled, which font text is drawn in, and what color lines are
