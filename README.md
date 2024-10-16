# Google mobile ads build error repro

## Summary

`google_mobile_ads` 5.2.0 causes Flutter iOS apps that use an Objective-C
entrypoint to crash immediately at startup.

The build errors appear to be caused by its dependency on `Google-Mobile-Ads-SDK`.
Downgrading `Google-Mobile-Ads-SDK` to version 11.5.0 fixes the build errors.

## Repro steps

Switch to the latest Flutter master channel:

1. `cd path/to/flutter`
1. `git checkout master`
1. `git pull`

Switch to Xcode 16.0:

1. `sudo xcode-select -s /Applications/Xcode_16_0.app`

Create a new Flutter app that uses Objective-C, add google_mobile_ads, and run it on an iOS device:

1. `flutter create ads_link_error --ios-language objc`
1. `cd ads_link_error`
1. `flutter pub add google_mobile_ads`
1. Edit `ios/Runner/Info.plist` and add:

   ```
   	<key>GADApplicationIdentifier</key>
   	<string>ca-app-pub-3940256099942544~1458002511</string>
   ```

1. `flutter run`

App crashes:

```
dyld[3705]: Library not loaded: @rpath/libswiftCore.dylib
  Referenced from: <D8C59B6C-5CEA-3F67-B8EE-C816C1BAE3DE> /private/var/containers/Bundle/Application/5173510E-D833-477C-A56D-33A2362CC9CB/Runner.app/Runner
  Reason: tried: '/usr/lib/system/introspection/libswiftCore.dylib' (no such file, not in dyld cache), '/private/var/containers/Bundle/Application/5173510E-D833-477C-A56D-33A2362CC9CB/Runner.app/Frameworks/libswiftCore.dylib' (no such file), '/private/var/containers/Bundle/Application/5173510E-D833-477C-A56D-33A2362CC9CB/Runner.app/Frameworks/libswiftCore.dylib' (no such file)
```