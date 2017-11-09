# Font in a React Native TV app

Recently,
[create-react-native-app](https://github.com/react-community/create-react-native-app)
got [support for tvOS
apps](https://facebook.github.io/react-native/docs/building-for-apple-tv.html).
Technically, in one Xcode project you now have two (times two)
build targets. For an app "myapp", you get "myapp" and
"myapp-tvOS".

If you want to add custom fonts in your React Native app, you
usually add them via Xcode: put them into Resources folder and
refer to them in Info.plist, `UIApiFonts` element.

The problem with Xcode is that, by default, original app
("myapp") is selected, so when you add fonts and refer to them
from Info.plist, you actually only refer to them from "myapp"'s
settings, not "myapp-tvOS"'s. But be careful! As you can see,
there are several settings files:

```bash
$ find ios/* | grep plist
ios/myapp/Info.plist
ios/myapp-tvOS/Info.plist
ios/myapp-tvOSTests/Info.plist
ios/myapp.xcodeproj/xcuserdata/Rishat.xcuserdatad/xcschemes/xcschememanagement.plist
ios/myappTests/Info.plist
```

To make your tvOS app support custom fonts, you need to refer to
them from ios/myapp-tvOS/Info.plist.

Technically, after getting the fonts in Resources folder, what
you need to do is to add a new section to
ios/myapp-tvOS/Info.plist:

```xml
<key>UIAppFonts</key>
<array>
        <string>proximanova-regular.otf</string>
</array>
```

This way, you'll get the fonts in the tvOS app, too.

Remember that TTF and OTF fonts are supported by Xcode, and WOFF2
are not.
