# Fujifilm SPA iOS SDK Tutorial

## Introduction
This project is a sample application to demonstrate how to integrate with the Fujifilm SPA iOS SDK.  

This document provides a tutorial to use the Fujifilm SPA iOS SDK library in your iOS application.

### What is it?
The Fujifilm Smart Publishing API SDK is a native library that you can include in your existing iOS application to enable photo product output through Fujifilm.  This provides you with a new revenue stream while providing a new valuable service to your application users.

The Fujifilm SPA SDK gives you access to over 50 popular photo gift products and allows you to control the availability and pricing of each product through our web portal. 

Please visit the Fujifilm Developer Network portal to sign-up and obtain an API key, set product pricing, and configure your application.  The portal is available at http://www.fujifilmapi.com/.

### What’s new?
There are many new items in this release:
+ Completely re-designed GUI
+ Product categorization to help organize the available product set
+ Over 30 new products including items within popular categories such as Home Décor and mobile device cases
+ A native Android SDK in addition to our iOS SDK so that you can provide your users with a common experience in either operating system (Android versions 4.0 and above)
+ Updated support for iOS versions 7 and above

## Requirements
+ iOS version 7.0 or later
+ Developers using the Fujifilm SPA iOS SDK need to sign up for an account on Fujifilm Developer Network (http://fujifilmapi.com), create an application, obtain an Api Key, and setup catalog products and pricing.

## Integration Instructions
To add Fujifilm SPA SDK to your Xcode project, you may add it manually, or you may install via Cocoapods. 

### Using CocoaPods

For more information on Cocoapods, including instructions on adding a pod to your Xcode project, visit https://cocoapods.org/ and navigate to the Guides section. 

This section assumes you have CocoaPods installed on your system.

In your Podfile, include the SPA SDK pod like so:
```ruby 
pod ‘Fujifilm-SPA-SDK’
```
If it is not already added, make sure your Podfile contains the use_frameworks! instruction. Install the pod by running $ pod install inside your project directory. If you have already installed the SDK and would like to update to the latest version, run $ pod update instead. Upon successful installation, close your Xcode project if it is open, and open the workspace that was generated by Cocoapods. The workspace should contain your project, as well as a Pods project that contains several files including libFujifilm_SPA_SDK_iOS.a and Fujifilm.SPA.SDK.h, which are the SDK library and header file, respectively.

### Manual Installation
1. Download files 
    * Download the [libFujifilm_SPA_SDK_iOS.a](https://github.com/fujifilmssd/iOS-Fujifilm-SPA-SDK-SampleApp/blob/master/Fujifilm_SPA_SDK_iOS_DemoApp/libFujifilm_SPA_SDK_iOS.a) and [Fujifilm.SPA.SDK.h](https://github.com/fujifilmssd/iOS-Fujifilm-SPA-SDK-SampleApp/blob/master/Fujifilm_SPA_SDK_iOS_DemoApp/Fujifilm.SPA.SDK.h), which are the located [here](https://github.com/fujifilmssd/iOS-Fujifilm-SPA-SDK-SampleApp/blob/master/Fujifilm_SPA_SDK_iOS_DemoApp/libFujifilm_SPA_SDK_iOS.a) and [here](https://github.com/fujifilmssd/iOS-Fujifilm-SPA-SDK-SampleApp/blob/master/Fujifilm_SPA_SDK_iOS_DemoApp/Fujifilm.SPA.SDK.h). 
2. Add files to project
    * Open your project in Xcode. Select File > Add Files To “MyApp” and select the files you just downloaded. Check “Copy items if needed” under Destination and select “Create groups” under Added Folders. Make sure your target is checked in the “Add to targets” section. Click Add.
3. Link with frameworks
    * Add the following frameworks to your project:  
        - AddressBook
        - AddressBookUI
        - MobileCoreServices
        - SystemConfiguration
        - AssetsLibrary
        - ImageIO
        - Photos
    * To add frameworks,, select your project in the Xcode file explorer. In the main window, the top left corner has a dropdown menu with a list of your projects and targets. Make sure your target is selected (not your project) and switch to the Build Phases tab. Expand the Link Binary With Libraries section and add the frameworks listed above.

## Implementation Instructions

### iOS 9+ Security - Updating info.plist
Xcode 7 includes a new security feature called App Transport Security. In order to use the SDK you will need to add exceptions to your project's info plist file.

You can enter this manually using the Xcode UI, or you can open the Info.plist file in a text editor and copy/paste the following in the inside the <plist> <dict> tag:
```
    <key>NSAppTransportSecurity</key>
    <dict>
        <key>NSAllowsArbitraryLoads</key>
        <true/>
        <key>NSExceptionDomains</key>
        <dict>
            <key>paypal.com</key>
            <dict>
                <key>NSExceptionRequiresForwardSecrecy</key>
                <false/>
                <key>NSIncludesSubdomains</key>
                <true/>
            </dict>
                <key>fujifilmesys.com</key>
            <dict>
                <key>NSExceptionRequiresForwardSecrecy</key>
                <false/>
                <key>NSIncludesSubdomains</key>
                <true/>
            </dict>
        </dict>
    </dict>
```
### Creating the View Controller
To begin, create a view controller that will extend UIViewController. For example, you may name your view controller MyFujifilmSPASDKController. In your view controller header (MyFujifilmSPASDKController.h) file, add the following line:
```objective-c
#import "Fujifilm.SPA.SDK.h"
```

In your view controller header file, ensure it implements the FujifilmSPASDKDelegate protocol, like so:
```objective-c
@interface MyFujifilmSPASDKController : UIViewController <FujifilmSPASDKDelegate>{}
```

### Fujifilm SPA SDK Usage
In your view controller, create a Fujifilm_SPA_SDK_iOS object. Initialize it using the initWithOptions method like so:
```objective-c
Fujifilm_SPA_SDK_iOS *fujifilmOrderController = [[Fujifilm_SPA_SDK_iOS alloc] initWithOptions:[YOUR_API_KEY] environment:["test"_or_"live"] images:[ARRAY_of_IMAGES] userID:@""];
```

#### Parameters
*  **apiKey(NSString)**: Fujifilm SPA apiKey you receive when you create your app at http://fujifilmapi.com  
*  **environment(NSString)**: A string indicating which environment your app runs in. Must match your app’s environment set on http://fujifilmapi.com. Possible values are “test” or “live”.  
*  **images(id)**: An NSArray of PHAsset, ALAsset, or NSString (public image urls https://). (Array can contain combination of types). Images must be JPG format and smaller than 20MB. A maximum of 50 images can be sent in a given Checkout process. If more than 50 images are sent, only the first 50 will be processed.  
*  **userid(NSString)**: Optional param, send in @"" if you don't use it. This can be used to link a user with an order. MaxLength = 50 alphanumeric characters.  

Next, set the Fujifilm_SPA_SDK_iOS object’s delegate to the view controller:
```objective-c
fujifilmOrderController.delegate = self;
```
Finally, present the Fujifilm_SPA_SDK_iOS object:
```objective-c
[self presentViewController:fujifilmOrderController animated:YES completion:nil];
```
The FujifilmSPASDKDelegate requires the view controller to implement the method fujifilmSPASDKFinishedWithStatus:(int) statusCode andMessage(NSString*) message. See **Finish Fujifilm SPA SDK** below for more information on what to put in this method.

#### Finish Fujifilm SPA SDK

When the Fujifilm SPA SDK is finished, it will return to the parent app, calling 
```objective-c
#pragma mark -
#pragma mark Fujifilm SPA SDK delegate

-(void) fujifilmSPASDKFinishedWithStatus: (int) statusCode andMessage: (NSString*) message{
    switch (statusCode){
        case 4:
            // "User Cancelled";
            break;
        case 7:
            // "Order Complete";
            // orderId is stored in message
        break;
        //…
    }
}
```
The status code will be one of the following values:

*  Fatal Error         = 0  
*  No Images Uploaded  = 1  
*  No Internet         = 2  
*  Invalid API Key     = 3  
*  User Cancelled      = 4  
*  No Valid Images     = 5  
*  Time Out            = 6  
*  Order Complete      = 7  
*  Upload Failed       = 8  

It is up to the view controller to handle any/all of these cases in fujifilmSPASDKFinishedWithStatus as seen above. The status codes and messages are for internal use only, please do not present these to the user.

#### Example Code
```objective-c
- (IBAction)launchFujifilmSDK:(id)sender {
      NSArray *images = [[NSArray alloc] initWithObjects:@"https://pixabay.com/static/uploads/photo/2015/09/05/21/08/fujifilm-925350_960_720.jpg",@"https://pixabay.com/static/uploads/photo/2016/02/07/12/02/mustang-1184505_960_720.jpg", nil];
   
      /*
      -------------------------------------------------------------------------------
      Create a Fujifilm_SPA_SDK_iOS instance and present the Fujifilm SDK controller.
      -------------------------------------------------------------------------------
      
      - Go to http://www.fujifilmapi.com to register for an apiKey.
      - Ensure you have the right apiKey for the right environment.
      
      @param apiKey: Fujifilm SPA apiKey you receive when you create your app at http://fujifilmapi.com
      @param environment: A string indicating which environment your app runs in. Must match your app’s environment set on http://fujifilmapi.com. Possible values are “test” or “live”.
      @param images: An NSArray of PHAsset, ALAsset, or NSString (public image urls http://). (Array can contain combination of types). Images must be JPG format and smaller than 20MB.
      @param userid: Optional param, send in @"" if you don't use it. This can be used to link a user with an order. MaxLength = 50 alphanumeric characters
      *---------------------------------------------------------------------------------------
     */
     
     //MAKE SURE TO CHANGE YOUR_API_KEY TO YOUR APIKEY!
      Fujifilm_SPA_SDK_iOS *fujifilmOrderController = [[Fujifilm_SPA_SDK_iOS alloc] initWithOptions:@"YOUR_API_KEY" environment:@"test" images:images userID:@""];
      
      fujifilmOrderController.delegate = self;
      
      [self presentViewController:fujifilmOrderController animated:YES completion:nil];
}
-(void) fujifilmSPASDKFinishedWithStatus: (int) statusCode andMessage: (NSString*) message{
}
```

## Additional notes and debugging help

The following are some notes to help with integrating with **Fujifilm SPA iOS SDK**. 

### Use Requirements:
+ The phone must have internet access to begin the SDK, and must retain access throughout the checkout process. If the connection is lost during the checkout process, an alert will be shown notifying the user that an internet connection is required
+ A maximum of 50 images can be sent to the SDK in a given checkout process. If more than 50 images are sent, only the first 50 will be processed
+ Only .jpg files are supported
+ The maximum size of a single file is 20MB

### Errors that prevent the SDK from Starting
+ 0 Images to be uploaded
+ No internet access
+ Invalid APIKey. Ensure the APIKey you are using matches the environment string you are passing in (“test’, “live”)
+ Missing required frameworks: AddressBook, AddressBookUI, MobileCoreServices, SystemConfiguration, AssetsLibrary, ImageIO, and Photos

### Errors that will cancel the SDK
+ Loss of network or internet access before all images have finished uploading
+ All images fail to upload / no remaining images to checkout with

These errors will return control back to the parent app and call fujifilmSPASDKFinishedWithStatus  with status code and message corresponding to the cause of the error. In addition, depending on the nature of the error there may be a Log message in the iOS console with more information on the cause for the error.

### Errors that will prevent a specific picture from uploading or being processed
+ An image is over 20MB
+ An image is of an unsupported file format
Image file is corrupt or is uploaded unsuccessfully, making it corrupt

These Errors will not cancel the SDK, and as such, they will not directly return an error code to fujifilmSPASDKFinishedWithStatus. However, if enough images are removed such that 0 images are remaining then the SDK will be terminated.

### Feedback
We’re very interested in your feedback!  If you run into any trouble, have a suggestion, or want to let us know what worked well send us an email to contact@fujifilmapi.com or use the web form at https://www.fujifilmapi.com/contact-us. 

### License
IMPORTANT - PLEASE READ THE FOLLOWING TERMS AND CONDITIONS CAREFULLY BEFORE USING THE FOLLOWING COMPUTER CODE (THE “CODE”). USE OF THE CODE IS AT YOUR OWN RISK. THE CODE IS PROVIDED “AS IS”, WITH ANY AND ALL FAULTS, DEFECTS AND ERRORS, AND WITHOUT ANY WARRANTY OF ANY KIND. FUJIFILM DISCLAIMS ALL WARRANTIES, WHETHER EXPRESS, IMPLIED OR STATUTORY, INCLUDING, WITHOUT LIMITATION, ALL IMPLIED WARRANTIES OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE, AND NON-INFRINGEMENT, WITH RESPECT TO THE CODE OR DEFECTS IN OPERATION OR ANY PARTICULAR APPLICATION OR USE OF THE CODE. FUJIFILM DOES NOT WARRANT THAT THE CODE WILL MEET YOUR REQUIREMENTS OR EXPECTATIONS, THAT THE CODE WILL WORK ON ANY HARDWARE, OPERATING SYSTEM OR WITH ANY SOFTWARE, THAT THE OPERATION OF THE CODE WILL BE UNINTERRUPTED, FREE OF HARMFUL COMPONENTS OR ERROR-FREE, OR THAT ANY KNOWN OR DISCOVERED ERRORS WILL BE CORRECTED.
FUJIFILM SHALL NOT BE LIABLE TO YOU OR ANY THIRD PARTY FOR ANY LOSS OF PROFIT, LOSS OF DATA, COMPUTER FAILURE OR MALFUNCTION, INTERRUPTION OF BUSINESS, OR OTHER DAMAGE ARISING OUT OF OR RELATING TO THE CODE, INCLUDING, WITHOUT LIMITATION, EXEMPLARY, PUNITIVE, SPECIAL, STATUTORY, DIRECT, INDIRECT, INCIDENTAL, CONSEQUENTIAL, TORT OR COVER DAMAGES, WHETHER IN CONTRACT, TORT OR OTHERWISE, INCLUDING, WITHOUT LIMITATION, DAMAGES RESULTING FROM THE USE OR INABILITY TO USE THE CODE, EVEN IF FUJIFILM HAS BEEN ADVISED OR AWARE OF THE POSSIBILITY OF SUCH DAMAGES.
