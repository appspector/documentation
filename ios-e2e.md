## Installation
Each app you want to use with AppSpector SDK you have to register on the web (https://app.appspector.com).
After adding the application navigate to app settings and copy API key.

#### CocoaPods
<!-- integration-pods-start -->
To use CocoaPods add this line to your podfile and run `pod install`:

```
pod 'AppSpectorSDKE2E'
```
<!-- integration-pods-end -->

#### Carthage
<!-- integration-carthage-start -->
- Install [Carthage](https://github.com/Carthage/Carthage#installing-carthage)
- Add `binary "https://github.com/appspector/ios-sdk/raw/1.2.4/AppSpectorE2E.json"` to your Cartfile
- Run `carthage update`
- Drag [AppSpectorSDKE2E.framework](https://github.com/appspector/ios-sdk/blob/master/AppSpectorSDKE2E.framework.zip?raw=true) from the appropriate platform directory in Carthage/Build/ to the “Linked Frameworks and Libraries” section of your Xcode project’s “General” settings
<!-- integration-carthage-end -->

#### Manually
<!-- integration-manual-start -->
To manually link just download [AppSpectorSDKE2E.framework](https://github.com/appspector/ios-sdk/blob/master/AppSpectorSDKE2E.framework.zip?raw=true) and drop AppSpectorSDKE2E.framework to your XCode project.
Then navigate to your project settings and under 'General' tab add AppSpectorSDKE2E framework to 'Embedded Binaries' section.

You also need to install Themis library with OpenSSL support. Refer this tutorial for instructions: [https://github.com/cossacklabs/themis/wiki/Installing-Themis](https://github.com/cossacklabs/themis/wiki/Installing-Themis)

If you plan either to submit builds with AppSpector SDK to the Apple TestFlight for testing or archive them for AdHoc distribution you'll have to perform one more step: create a new “Run Script Phase” in your app’s target’s “Build Phases” and paste the following script:

```
code_sign() {
    echo "Code Signing $1 with Identity ${EXPANDED_CODE_SIGN_IDENTITY_NAME}"
    echo "/usr/bin/codesign --force --sign ${EXPANDED_CODE_SIGN_IDENTITY} --preserve-metadata=identifier,entitlements $1"
    /usr/bin/codesign --force --sign ${EXPANDED_CODE_SIGN_IDENTITY} --preserve-metadata=identifier,entitlements "$1"
}

cd "${BUILT_PRODUCTS_DIR}/${FRAMEWORKS_FOLDER_PATH}"

for file in $(find . -type f -perm +111); do
    if ! [[ "$(file "$file")" == *"dynamically linked shared library"* ]]; then
        continue
    fi

    archs="$(lipo -info "${file}" | rev | cut -d ':' -f1 | rev)"
    stripped=""
    for arch in $archs; do
        if ! [[ "${VALID_ARCHS}" == *"$arch"* ]]; then
            lipo -remove "$arch" -output "$file" "$file" || exit 1
            stripped="$stripped $arch"
        fi
    done

    if [[ "$stripped" != "" ]]; then
        echo "Stripped $file of architectures:$stripped"
        if [ "${CODE_SIGNING_REQUIRED}" == "YES" ]; then
            code_sign "${file}"
        fi
    fi
done
```

This script is required as a workaround for this [Apple AppStore bug](http://www.openradar.me/radar?id=6409498411401216)


<!-- integration-manual-end -->

#### Apple TV
End-to-end encryption is not supported for tvOS for now. Please let us know if you need it.


## Integration

#### Swift

<!-- integration-swift-example-start -->
First import the framework:
```
import AppSpectorSDKE2E
```

Start selected monitors only
```swift
let config = AppSpectorConfig(apiKey: "API_KEY", publicKey: "PUBLIC_KEY", monitorIDs: [Monitor.http, Monitor.logs])
AppSpector.run(with: config)
```
or start all monitors
```
let config = AppSpectorConfig(apiKey: "API_KEY", publicKey: "PUBLIC_KEY")
AppSpector.run(with: config)
```
<!-- integration-swift-example-end -->

#### Objective-C
<!-- integration-objc-example-start -->
First import the framework:
```
@import AppSpectorSDKE2E;
```

Start selected monitors only
```objective-c
NSSet *monitorIDs = [NSSet setWithObjects:AS_HTTP_MONITOR, AS_LOG_MONITOR, nil];
AppSpectorConfig *config = [AppSpectorConfig configWithAPIKey:@"API_KEY" publicKey:@"PUBLIC_KEY" monitorIDs:monitorIDs];
[AppSpector runWithConfig:config];
```

or start all monitors
```
AppSpectorConfig *config = [AppSpectorConfig configWithAPIKey:@"API_KEY" publicKey:@"PUBLIC_KEY"];
[AppSpector runWithConfig:config];
```
<!-- integration-objc-example-end -->


[Join our slack to discuss setup process and features](https://slack.appspector.com)
