---
layout: post
title:  "Mini Notes"
date:   2020-02-01 14:13:56 -0500
categories: android
---
## NOTE 1 - Fragment's view vs instance lifecycles

Use Fragment's [view lifecycle](https://developer.android.com/reference/androidx/fragment/app/Fragment#getViewLifecycleOwner()) when observing lifedata in Fragments. This allows releasing resources when the view is gone but Fragment's instance is still alive.

When the user navigates away from a fragment but the fragment is still in backstack, Android will keep the instance of the fragment but will kill the view to save resources. When the user goes back to the fragment `onCreateView` is called and we create a new view for the same fragment object we had before.

- LiveData observers - if this is done in `onCreateView`

- RV adapter keeps references to ViewHolder instances and those keep references to the inflated views. When we have a reference to the adapter all ViewHolder instances and View instances remain in memory when the user navigates away from the fragment. This gives a benefit of not having to inflate views again when the user comes back but that comes with a cost of keeping them in memory. So, just be aware of this and use it to your advantage.


## NOTE 2 - Minimize `requestLayout()` calls on RecyclerView

- Measure it by extending RV and counting `requestLayout()` calls
- ImageView
    - use fixed width, height if you can and put a placeholder with the same width height. See https://android.googlesource.com/platform/frameworks/base/+/master/core/java/android/widget/ImageView.java#585
    - use AspectRatioImageView
- TextView: TBD
- Use `recyclerView.setHasFixedSize(true)` to prevent remeasuring when item views change.

## NOTE 3 - LiveData `postValue()` vs `value=`

- `postValue()` is for posting LiveData value from a different thread
- `postValue()` is much slower (more than 100ms vs less than 10ms) therefore use `value=` if possible

## NOTE 4 - Reading from Room database

- Observing values is slower than getting them. Use get, render the view than observe the value for changes. 

## NOTE 5 - Image loading with Glide
- Use `placeholder()` to prevent a `setImageDrawable()` call with a `null` value, which causes remeasurement
- Make sure placeholder size is equal to the loaded image. Resize the loaded image to keep the sizes the same

## NOTE 6 - Using `android.text.format.DateUtils`
- The first call to `DateUtils.getRelativeDateTimeString` takes about 400ms in average, subsequent calls take 2-8ms
- Make a dummy call `getRelativeDateTimeString` on a background thread to avoid spending that time when loading a screen

## NOTE 7 - Use SparseArray or ArrayMap for performance
- Boxing: int -> Integer
- Unboxing: Integer -> int
- [Source](https://android.jlelse.eu/autoboxing-a-little-thing-can-cause-big-problems-for-android-app-performance-1fb6cb1e48dd)

## NOTE 8 - Gradle build time
- `org.gradle.daemon=true`
- `org.gradle.parallel=true`
- [source](https://android.jlelse.eu/speeding-up-gradle-builds-619c442113cb#.thj9h2pqd) & [source](https://www.jrebel.com/blog/making-gradle-builds-faster)