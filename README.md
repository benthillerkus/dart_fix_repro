# dart_fix_repro

Repro for https://github.com/dart-lang/sdk/issues/59707

This is based on the default Flutter app created by running `flutter create dart_fix_repro`.
I then did the following changes:

- Deleted all platform folders (android, ios, linux, macos, web, windows)
- Deleted the test folder
- Deleted IntelliJ bloat
- Deleted .metadata
- Added analysis_options.yaml / added `prefer_const_constructors` rule

After that I introduced the following issues to the code:

- Removed `@override` for `MyHomePage.createState` and `MyApp.build`
- Removed `const` on `MyApp()` constructor call in `main`

If you then format the code with `dart fix --apply`, those issues should get cleared up correctly.
So far so good.

----

Now to the issue:

1. Undo the fixes.
2. Split the main.dart library into two files and connect them with a part directive.
3. Run `dart fix --apply` again.

The issues are being fixed multiple times!

```diff
diff --git a/lib/main.dart b/lib/main.dart
index 3aa91cd..5f08c49 100644
--- a/lib/main.dart
+++ b/lib/main.dart
@@ -3,13 +3,15 @@ import 'package:flutter/material.dart';
 part 'my_home_page.dart';

 void main() {
-  runApp( MyApp());
+  runApp( const const const MyApp());
 }

 class MyApp extends StatelessWidget {
   const MyApp({super.key});

   // This widget is the root of your application.
+  @override
+  @override
   Widget build(BuildContext context) {
     return MaterialApp(
       title: 'Flutter Demo',
```

```diff
diff --git a/lib/my_home_page.dart b/lib/my_home_page.dart
index 1085484..74f5c56 100644
--- a/lib/my_home_page.dart
+++ b/lib/my_home_page.dart
@@ -15,6 +15,8 @@ class MyHomePage extends StatefulWidget {

   final String title;

+  @override
+  @override
   State<MyHomePage> createState() => _MyHomePageState();
 }
```

oh well.