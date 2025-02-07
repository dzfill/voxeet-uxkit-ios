# Voxeet UXKit iOS

The Swift UXKit is a framework based on [iOS SDK](https://github.com/voxeet/voxeet-sdk-ios). If you wish to see how UXKit looks like and how it works, check a sample application available in the [Sample](Sample) folder.

## Installing the UXKit

Follow this instruction to install the Swift UXKit.

### Requirements

Before installing the Swift UXKit, ensure that you meet the minimum hardware and software requirements.

- **Operating systems**: iOS 11.0 and later
- **Integrated development environment**: [Xcode 12.5+](https://developer.apple.com/xcode/)
- **Language**: Swift 5.4.2+
- **Supported architectures**: armv7, arm64, i386, x86_64

### Before you start

Before the installation, obtain your `Consumer Key` and `Consumer Secret`.

1. Select the `SIGN IN` link located in the upper right corner of the [Dolby.io](https://dolby.io) page. Log in using your email and password.
2. Click the `DASHBOARD` link visible in the upper right corner of the website.
3. Select your application from the `APPLICATIONS` category located on the left side menu.
4. Select the `API Keys` category from the drop-down menu visible under your application.
5. In the `Interactivity APIs` section, you can access your `Consumer Key` and `Consumer Secret`.

### Procedure

1. Open Xcode and go to `Target settings` ▸ `Capabilities` ▸ `Background Modes` to set up your project and enable the proper background modes.
2. Enable the `Audio, AirPlay, and Picture in Picture` and `Voice over IP` options.

> Note: To enable the VoIP push notifications with CallKit, enable push notifications and upload your [VoIP push certificate](https://developer.apple.com/account/ios/certificate/) to the developer portal.
> Read this [Manage push notifications](https://github.com/voxeet/voxeet-sdk-ios/wiki/Manage-push-notification) wiki page for more information.

3. Add the following keys to the `Info.plist` file to enable the required permissions:

    - Privacy: Microphone Usage Description
    - Privacy: Camera Usage Description

4. Install the SDK using **Carthage**, **CocoaPods**, or install the SDK **manually**.

#### Install the SDK using Carthage

- Use the following Homebrew commands to install Carthage:

```bash
brew update
brew install carthage
```

- Specify the UXKit version in `Cartfile` to integrate UXKit into the Xcode project:

```ogdl
github "voxeet/voxeet-uxkit-ios" ~> 1.0
```

- Run `carthage update` to build the frameworks and drag and drop `VoxeetUXKit.framework`, `VoxeetSDK.framework`, and `WebRTC.framework` to the `Embedded Binaries` category in your Xcode project.

> Note: For more information about building platform-specific bundles using Carthage, see the [Carthage documentation](https://github.com/Carthage/Carthage#if-youre-building-for-ios-tvos-or-watchos).

> If you are using Xcode 12, please see the workaround from [carthage](https://github.com/Carthage/Carthage/blob/master/Documentation/Xcode12Workaround.md)

#### Install the SDK using CocoaPods

- Install CocoaPods using the following command:

```bash
sudo gem install cocoapods
```

- Specify UXKit version in `Podfile` to integrate UXKit into the Xcode project:

```ogdl
pod 'VoxeetUXKit', '~> 1.0'
```

- Run `pod install` to build dependencies.

#### Install the SDK manually

- Download the latest UXKit and SDK zip files using the following links:

  - [UXKit zip file](https://github.com/voxeet/voxeet-uxkit-ios/releases)
  - [SDK zip file](https://github.com/voxeet/voxeet-sdk-ios/blob/master/VoxeetSDK.json)

- Unzip the downloaded files and drag and drop the frameworks to your Xcode project.

- Select the `Copy items if needed` option using the proper target. In the `general` tab of your target, add the `VoxeetUXKit.framework`, `VoxeetSDK.framework`, and `WebRTC.framework` to the `Embedded Binaries` category.

- Download the SDWebImage library from [GitHub](https://github.com/SDWebImage/SDWebImage) and install the framework manually or using Carthage or CocoaPods.

After the installation, check if the SDWebImage library is included on the the list of libraries. If you installed the library manually or you used Carthage, check if the `SDWebImage.framework` library is mentioned in the `Frameworks, Libraries, and Embedded Content` category in your Xcode project. If you installed the library using CocoaPods, check if the SDWebImage library is included inside the `Pods` project in the client xcworkspace.

## Configuring UXKit

The Swift UXKit provides a few additional parameters that offer an additional configuration.

- Use the `appearMaximized` parameter to maximize and minimize a conference. By default, the conference is maximized. Change the value to false if you wish to minimize a conference.

```swift
VoxeetUXKit.shared.appearMaximized = true
```

- Use the `telecom` parameter to change the application behavior. If the parameter value is set to true, the conference behaves similarly to a phone call where a participant who leaves a call, triggers the end of the call. If the parameter value is set to false, participants can leave a call without ending the call.

```swift
VoxeetUXKit.shared.telecom = false
```

- If you want to use CallKit, you can configure the default CallKit settings. CallKit offers the default ringtone and image icon that you can change. By default, the SDK uses the `CallKitSound.mp3` ringtone for CallKit notifications. If you wish to use a different ringtone, replace the `CallKitSound.mp3` file with another mp3 file. If you wish to use a different image icon, replace the default `IconMask.png` image with another image (40px x 40px).

## Using UXKit

The following procedure explains how to initialize the iOS SDK, open a session, and create and join a conference. If you wish to see more information about using the iOS SDK, see the documents available in the [Guides](doc:guides-dolby-voice) section or check the [Reference](doc:overview-) documentation.

1. Use the [initialize](https://docs.dolby.io/interactivity/docs/ios-client-sdk-voxeetsdk#initialize) method to initialize the Voxeet SDK.

```swift
import VoxeetSDK
import VoxeetUXKit

@UIApplicationMain
class AppDelegate: UIResponder, UIApplicationDelegate {
    func application(application: UIApplication, didFinishLaunchingWithOptions launchOptions: [NSObject: AnyObject]?) -> Bool {

        // Voxeet SDK initialization.
        VoxeetSDK.shared.initialize(consumerKey: "YOUR_CONSUMER_KEY", consumerSecret: "YOUR_CONSUMER_SECRET")
        VoxeetUXKit.shared.initialize()

        // Example of public variables to change the conference behavior.
        VoxeetSDK.shared.pushNotification.push.type = .none
        VoxeetSDK.shared.conference.defaultBuiltInSpeaker = false
        VoxeetSDK.shared.conference.defaultVideo = false
        VoxeetUXKit.shared.appearMaximized = true
        VoxeetUXKit.shared.telecom = false

        return true
    }
}
```

> Note: For more information about integrating the Dolby Interactivity APIs Client SDK into your application, see the reference documentation or the [Initializing]() document.

2. [Open](https://docs.dolby.io/interactivity/docs/ios-client-sdk-sessionservice#open) a new session.

```swift
let info = VTParticipantInfo(externalID: "1234", name: "Username", avatarURL: "https://www.gravatar.com/avatar/1234")
VoxeetSDK.shared.session.open(info: info) { error in }
```

> Note: The SDK invite process requires invitees to use the [open](https://docs.dolby.io/interactivity/docs/ios-client-sdk-sessionservice#open) method to be able to receive the conference invitations. If you use CallKit, you can receive conference invitations even when you do not have an open session.

3. Call the [create](https://docs.dolby.io/interactivity/docs/ios-client-sdk-conferenceservice#create) and [join](https://docs.dolby.io/interactivity/docs/ios-client-sdk-conferenceservice#join) methods to create and join a conference.

```swift
// Create a conference (with a custom conference alias).
let options = VTConferenceOptions()
options.alias = conferenceAlias

VoxeetSDK.shared.conference.create(options: options, success: { conference in
    // Join the created conference.
    let joinOptions = VTJoinOptions()
    joinOptions.constraints.video = false

    VoxeetSDK.shared.conference.join(conference: conference, options: joinOptions, success: { conference in
        // Success
    }, fail: { error in
        // Error
    })
}, fail: { error in
    // Error
})
```

> Note: For more information about joining conferences, see the [Conferencing](https://docs.dolby.io/interactivity/docs/ios-client-sdk-conferenceservice) document.

4. Call the [leave](https://docs.dolby.io/interactivity/docs/ios-client-sdk-conferenceservice#leave) method to leave the conference.

```swift
VoxeetSDK.shared.conference.leave { error in }
```

5. Call the [close](https://docs.dolby.io/interactivity/docs/ios-client-sdk-sessionservice#close) method to close the current session. The method closes the web socket and does not allow sending the VoIP push notifications.

```swift
VoxeetSDK.shared.session.close { error in }
```

## Open Source Projects

The Voxeet iOS SDK and UXKit rely on these open source projects:

* [SDWebImage](https://github.com/SDWebImage/SDWebImage), provides an async image downloader with cache support.
* [Starscream](https://github.com/daltoniam/Starscream), a conforming WebSocket (RFC 6455) client library.
* [Alamofire](https://github.com/Alamofire/Alamofire), an HTTP networking library.
* [SwiftyJSON](https://github.com/SwiftyJSON/SwiftyJSON), a tool for handling JSON data.
