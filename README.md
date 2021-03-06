# PowerAuth SDK for React Native Mobile Apps
<!-- begin remove -->
[![npm](https://img.shields.io/npm/v/react-native-powerauth-mobile-sdk)](https://www.npmjs.com/package/react-native-powerauth-mobile-sdk) ![license](https://img.shields.io/github/license/wultra/react-native-powerauth-mobile-sdk)
<!-- end -->

In order to connect to the [PowerAuth](https://www.wultra.com/mobile-security-suite) service, mobile applications need to perform the required network and cryptographic processes, as described in the PowerAuth documentation. To simplify the implementation of these processes, developers can use React Native library (for Android and iOS) from this repository.

## Support and Compatibility

|React Native SDK| Mobile SDK | Protocol | PowerAuth Server    | Support Status                    |
|----------------|------------|----------|---------------------|-----------------------------------|
|`1.5.x`         | `1.5.x`    | `V3.1`   | `0.24+`             | Fully supported                   |
|`1.4.x`         | `1.4.x`    | `V3.1`   | `0.24+`             | Security & Functionality bugfixes |

## How to install

### 1. Install package via `npm`
```sh
npm i react-native-powerauth-mobile-sdk --save
```

### 2. Link your native dependencies

```sh
npx react-native link
```

For iOS, don't forget to install the pods:

```sh
cd ios
pod install
```

or

```sh
npx pod-install
```
### 3. Configure the instance

Before you call any PowerAuth method, you need to configure it first. The `configure` method will need the following parameters:

- **instanceId** Identifier of the PowerAuthSDK instance. The aplication package name/identifier is recommended.  
- **appKey** APPLICATION_KEY as defined in PowerAuth specification - a key identifying an application version.
- **appSecret** APPLICATION_SECRET as defined in PowerAuth specification - a secret associated with an application version.  
- **masterServerPublicKey** KEY\_SERVER\_MASTER_PUBLIC as defined in PowerAuth specification - a master server public key.  
- **baseEndpointUrl** Base URL to the PowerAuth Standard RESTful API (the URL part before "/pa/...").  
- **enableUnsecureTraffic** If HTTP and invalid HTTPS communication should be enabled (do not set true in production).  

#### Configuration from JavaScript

You can configure the PowerAuth singleton directly in the Javascript. Simply import the module and use the following snippet.

```js
import PowerAuth from 'react-native-powerauth-mobile-sdk';

PowerAuth.configure("your-app-activation", "APPLICATION_KEY", "APPLICATION_SECRET", "KEY_SERVER_MASTER_PUBLIC", "https://your-powerauth-endpoint.com/", false)
  .catch(function(e) {
    console.log("Configuration failed");
  }).then(function(r) {
    console.log("PowerAuth configured");
});
```

#### Configuration from native code

In some cases (for example when you don't want to leave the configuration info in your `.js` files or when you need more advanced configuration) you might want to configure the PowerAuth directly from the platform native code.

__JAVA__

_The following code is an example based on `MainApplication.java` file that is generated by the React Native and can be found inside the `YOUR_APP/android/app/src/main/java/YOUR/PACKAGE` folder._  
_Your implementation might differ._

```java
import com.wultra.android.powerauth.reactnative.PowerAuthRNPackage;
import io.getlime.security.powerauth.networking.ssl.PA2ClientSslNoValidationStrategy;
import io.getlime.security.powerauth.sdk.PowerAuthClientConfiguration;
import io.getlime.security.powerauth.sdk.PowerAuthConfiguration;
import io.getlime.security.powerauth.sdk.PowerAuthSDK;

public class MainApplication extends Application implements ReactApplication {
		
  @Override
  public void onCreate() {
    super.onCreate();
    initializePowerAuth();
  }
  	
  private void initializePowerAuth() {
    for (ReactPackage pkg : this.getReactNativeHost().getReactInstanceManager().getPackages()) {
      if (pkg instanceof PowerAuthRNPackage) {
        try {
          PowerAuthSDK.Builder builder = new PowerAuthSDK.Builder(
                 new PowerAuthConfiguration.Builder("your-app-activation", "https://your-powerauth-endpoint.com/", "APPLICATION_KEY", "APPLICATION_SECRET", "KEY_SERVER_MPK").build()
         );
         ((PowerAuthRNPackage) pkg).configure(builder);
        } catch (Exception e) {
          e.printStackTrace();
        }
      }
    }
  }
}
```

For more information about the native configuration, you can visit [official documentation of the native SDK](https://github.com/wultra/powerauth-mobile-sdk/blob/develop/docs/PowerAuth-SDK-for-Android.md#configuration).

__OBJECTIVE-C__

_The following code is an example based on `AppDelegate.m` file that is generated by the React Native and can be found inside the `YOUR_APP/ios/PROJECT_NAME` folder._  
_Your implementation might differ._

```objc
#import "AppDelegate.h"
#import <PowerAuth.h>
#import <React/RCTRootView.h>

@interface AppDelegate ()
@property (nonatomic, strong) NSDictionary *launchOptions;
@end

@implementation AppDelegate

- (RCTBridge *)initializeReactNativeApp
{
  RCTBridge *bridge = [[RCTBridge alloc] initWithDelegate:self launchOptions:self.launchOptions];
  
  // POWERAUTH CONFIGURATION
  PowerAuth *pa = [bridge moduleForClass:PowerAuth.class];
  if (pa) {
    PowerAuthConfiguration *config = [[PowerAuthConfiguration alloc] init];
    config.instanceId = @"your-instance-id";
    config.appKey = @"APPLICATION_KEY";
    config.appSecret = @"APPLICATION_SECRET";
    config.masterServerPublicKey = @"KEY_SERVER_MASTER_PUBLIC";
    config.baseEndpointUrl = @"https://your-powerauth-endpoint.com/";
    if(![pa configureWithConfig:config keychainConfig:nil clientConfig:nil]) {
      NSLog(@"Failed to configure PowerAuth module");
    }
  } else {
    NSLog(@"PowerAuth module not found");
  }
  
  // CODE GENERATED BY THE REACT NATIVE TEMPLATE
  
  RCTRootView *rootView = [[RCTRootView alloc] initWithBridge:bridge moduleName:@"main" initialProperties:nil];
  rootView.backgroundColor = [[UIColor alloc] initWithRed:1.0f green:1.0f blue:1.0f alpha:1];

  UIViewController *rootViewController = [UIViewController new];
  rootViewController.view = rootView;
  self.window.rootViewController = rootViewController;
  [self.window makeKeyAndVisible];

  return bridge;
}

- (void)appController:(EXUpdatesAppController *)appController didStartWithSuccess:(BOOL)success
{
  appController.bridge = [self initializeReactNativeApp];
}

@end
```

For more information about the native configuration, you can visit [official documentation of the native SDK](https://github.com/wultra/powerauth-mobile-sdk/blob/develop/docs/PowerAuth-SDK-for-iOS.md#configuration).

## API reference

For API reference, visit [PowerAuth.d.ts definition file](https://github.com/wultra/react-native-powerauth-mobile-sdk/blob/master/PowerAuth.d.ts) where you can browse all documented available methods.

> More detailed documentation will be added later. If you need any information regarding the status of this library, don't hesitate to [contact us](#contact).  
> 
> For information, you can visit the [native PowerAuth SDK for Mobile Apps](https://github.com/wultra/powerauth-mobile-sdk) as this library acts as a bridge between the Javascript environment and the native module.*

## Demo application

Demo application with the integration of the PowerAuth React Native SDK can be found inside the `demoapp` folder.

## License

All sources are licensed using Apache 2.0 license, you can use them with no restriction. If you are using PowerAuth 2.0, please let us know. We will be happy to share and promote your project.

## Contact

If you need any assistance, do not hesitate to drop us a line at [hello@wultra.com](mailto:hello@wultra.com).

### Security Disclosure

If you believe you have identified a security vulnerability with PowerAuth, you should report it as soon as possible via email to [support@wultra.com](mailto:support@wultra.com). Please do not post it to a public issue tracker.
