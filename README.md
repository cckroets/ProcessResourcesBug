# Incremental Merge Resources Bug Demo

Context: https://issuetracker.google.com/issues/206674992

## Info
AGP: 7.1.1
Gradle: 7.2
OS: macOS
Java: 11

## Steps to Reproduce 
1. Clone the project

2. Build release APK from CLI with `./gradlew :app:assembleRelease`

3. Change the name of an attribute (`myColorTextBrand` -> `myColorTextBrand1`) in colors.xml and attrs.xml
in the `myLibrary` module.
   
E.g.
```
diff --git a/mylibrary/src/main/res/values/attrs.xml b/mylibrary/src/main/res/values/attrs.xml
index 8badf92..11ed5b1 100644
--- a/mylibrary/src/main/res/values/attrs.xml
+++ b/mylibrary/src/main/res/values/attrs.xml
@@ -1,5 +1,5 @@
 <?xml version="1.0" encoding="utf-8"?>
 <resources>
-    <attr name="myColorTextBrand" format="reference"/>
+    <attr name="myColorTextBrand1" format="reference"/>
     <attr name="myColorTextPrimary" format="reference"/>
 </resources>
diff --git a/mylibrary/src/main/res/values/themes.xml b/mylibrary/src/main/res/values/themes.xml
index 307f0ee..efb2f98 100644
--- a/mylibrary/src/main/res/values/themes.xml
+++ b/mylibrary/src/main/res/values/themes.xml
@@ -12,7 +12,7 @@
         <!-- Status bar color. -->
         <item name="android:statusBarColor" tools:targetApi="l">?attr/colorPrimaryVariant</item>
         <!-- Customize your theme here. -->
-        <item name="myColorTextBrand">@color/black</item>
+        <item name="myColorTextBrand1">@color/black</item>
         <item name="myColorTextPrimary">@color/black</item>
     </style>
```
4. Build release APK from CLI again with `./gradlew :app:assembleRelease`

5. Observe logged Exception during Build

```
> Task :app:mergeReleaseResources
java.lang.IllegalArgumentException: Unable to locate resourceFile (/.../ProcessResourcesBug/app/build/intermediates/merged-not-compiled-resources/release/layout/notification_action.xml) in source-sets.
        at com.android.ide.common.resources.RelativeResourceUtils.getRelativeSourceSetPath(RelativeResourceUtils.kt:46)
        at com.android.ide.common.resources.MergedResourceWriter.getSourceFilePath(MergedResourceWriter.java:333)
        at com.android.ide.common.resources.MergedResourceWriter.removeOutFile(MergedResourceWriter.java:755)
        at com.android.ide.common.resources.MergedResourceWriter.removeFileFromNotCompiledOutputDir(MergedResourceWriter.java:723)
        at com.android.ide.common.resources.MergedResourceWriter.removeOutFile(MergedResourceWriter.java:741)
        at com.android.ide.common.resources.MergedResourceWriter.removeItem(MergedResourceWriter.java:448)
        at com.android.ide.common.resources.MergedResourceWriter.removeItem(MergedResourceWriter.java:71)
        at com.android.ide.common.resources.DataMerger.mergeData(DataMerger.java:276)
        at com.android.ide.common.resources.ResourceMerger.mergeData(ResourceMerger.java:385)
        at com.android.build.gradle.tasks.MergeResources.doTaskAction(MergeResources.java:501)
        at com.android.build.gradle.internal.tasks.NewIncrementalTask$taskAction$$inlined$recordTaskAction$1.invoke(BaseTask.kt:69)
        at com.android.build.gradle.internal.tasks.Blocks.recordSpan(Blocks.java:51)
        at com.android.build.gradle.internal.tasks.NewIncrementalTask.taskAction(NewIncrementalTask.kt:47)
        at java.base/jdk.internal.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
        at java.base/jdk.internal.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:62)
        at java.base/jdk.internal.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
        at java.base/java.lang.reflect.Method.invoke(Method.java:566)
        ...
```

