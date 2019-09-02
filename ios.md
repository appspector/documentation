## Installation
Each app you want to use with AppSpector SDK you have to register on the web (https://app.appspector.com).
After adding the application navigate to app settings and copy API key.
To use AppSpector on tvOS just follow installation steps below but use AppSpectorTVSDK instead.

#### CocoaPods
<!-- integration-pods-start -->
To use CocoaPods add this line to your podfile and run `pod install`:

```
pod 'AppSpectorSDK'
```
<!-- integration-pods-end -->

#### Carthage
<!-- integration-carthage-start -->
- Install [Carthage](https://github.com/Carthage/Carthage#installing-carthage)
- Add `binary "https://github.com/appspector/ios-sdk/raw/1.2.4/AppSpector.json"` to your Cartfile
- Run `carthage update`
- Drag [AppSpectorSDK.framework](https://github.com/appspector/ios-sdk/blob/master/AppSpectorSDK.framework.zip?raw=true) from the appropriate platform directory in Carthage/Build/ to the “Linked Frameworks and Libraries” section of your Xcode project’s “General” settings
<!-- integration-carthage-end -->

#### Manually
<!-- integration-manual-start -->
To manually link just download [AppSpectorSDK.framework](https://github.com/appspector/ios-sdk/blob/master/AppSpectorSDK.framework.zip?raw=true) and drop AppSpectorSDK.framework to your XCode project.
Then navigate to your project settings and under 'General' tab add AppSpectorSDK framework to 'Embedded Binaries' section.

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
AppSpector is also available for tvOS, you can use any of described above methods to install it, all you need is just use `AppSpectorTVSDK` pod instead of `AppSpectorSDK` and include `AppSpectorTVSDK.framework` instead of `AppSpectorSDK.framework`.

[Join our slack to discuss setup process and features](https://slack.appspector.com)
