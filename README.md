[Cocoapods]: https://cocoapods.org "Cocoapods Home Page"
[plist]: http://help.webtrends.com/en/ios/docs/webtrends.plist "webtrends.plist"
[AnaConfig]: Analytics---Advanced-Configuration-Guide.html "Analytics Advanced Configuration Guide"
[OptConfig]: Optimize---Advanced-Configuration-Guide.html "Optimize Advanced Configuration Guide"
[Swift]: https://developer.apple.com/library/prerelease/ios/documentation/Swift/Conceptual/BuildingCocoaApps/MixandMatch.html "Using Swift with Cocoa"
[WatchGuide]: Analytics---Webtrends-WatchKit-SDK-Guide.html "Webtrends WatchKit Guide"
[AutoEvents]: Analytics---Automatic-Events-Guide.html "Automatic Events Guide"
[ManualEvents]: Analytics---Manual-Events-Guide.html "Manual Events Guide"
[SampleAppRepo]: https://github.com/Webtrends/mobile-sdk-sample-apps "Sample App Github Repo"
[SampleAppGuide]: Sample-Applications-Guide.html "Sample App Guide"
[Optimize]: http://www.webtrends.com/testing-targeting-optimization/ "Webtrends Optimize"
[help]: http://help.webtrends.com/en/ios/ "Webtrends iOS Library Help"

The Webtrends SDK is a analytics library for iOS. This guide will walk you through the initial setup process, from integrating the SDK with your project, to sending events to Webtrends.

###Release Notes
The Webtrends iOS SDK 3.2.8 has been updated to not use location service if wt_dc_report_location_enabled wt config is set to false (default is true).  Additional fixes have been implemented to fix a crash that was previously reported.  This also incorporates all the fixes for the iOS SDK 3.2.4, which supports the ability to deliver a test or target to the first screen an app user sees after launching the app. In addition, the ability to add a load progress indicator was added to the SDK. You can now configure the timeout value for the progress indicator as well. For users wanting more manual control of the Optimize test data cached in the SDK, the ability to manually flush the data cache and set cache refresh values has been provided. Lastly, partial test content rendering has been added to the SDK. You can now track which parts of a test were viewed and use that data to generate Infinity reports. More detail documentation is available in the [help] pages on what parameters are sent and what values they will contain.

For more details, see [the Webtrends Optimize page][Optimize] for more information. For details on implementing Optimize in your iOS application, please see [our help documentation][help].


#Installation
[CocoaPods][Cocoapods] is the dependency manager for Swift and Objective-C Cocoa projects. It automates and streamlines the process of integrating libraries such as the Webtrends SDK into your project.

Webtrends SDK uses CocoaPods to manage project integration. It's broken into two sub pods, one for iOS devices, and one for WatchKit extensions. You can install the iOS library by itself, or both libraries, depending on your needs.

##Step 1 – Install CocoaPods
If you've never used CocoaPods, you’ll need to install it first. It's distributed as a ruby gem, and can be installed from the Terminal as follows:

	$ sudo gem install cocoapods

##Step 2 – Create a Podfile
Cocoapods uses a file called `Podfile` to manage third party code associated with a project. In your project's base directory, run the following commands in Terminal:

	$ touch Podfile
	$ open -a Xcode Podfile

Now that the `Podfile` has been created and opened, we'll add the information we need to install. First, tell the Podfile where to get the SDK. This step is not strictly necessary, but will ensure that you are receiving the validated production code from Webtrends. Add the following lines to your `Podfile`:

	source 'https://github.com/CocoaPods/Specs.git'
	source 'https://github.com/Webtrends/ios-sdk.git'

Next, select the components you need for your application by including the appropriate snippet below:

###I just need the Analytics SDK, no Optimize or Apple Watch support
	
	target 'MyApp' do
        pod 'Webtrends-SDK/Core', '~> 3.0'
	end
	

###I need Analytics, including Apple Watch support
Assuming your main iOS application target is called `MyApp` and your Apple Watch application target is called `MyApp Watchkit Extension`: 

	...
	
	# You can find the exact names of your targets by running:
	#
	# xcodebuild -list
	#
	# from your project's root directory
	
	target 'MyApp' do
	  pod 'Webtrends-SDK/Core', '~> 3.0'
	end
	
	target 'MyApp WatchKit Extension' do
	  pod 'Webtrends-SDK/Watch', '~> 3.0'
	end	

###I just need Optimize, no Apple Watch support
The Analytics (Core) is a dependency for Optimize. You must install both the Core and the Optimize pods.

	pod 'Webtrends-SDK/Core', '~>3.0'
	pod 'Webtrends-SDK/Optimize', '~>3.0'

###I need Analytics and Optimize, including Apple Watch support
This configuration will install the entire Webtrends SDK.  Assuming your main iOS application target is called `MyApp` and your Apple Watch application target is called `MyApp Watchkit Extension`: 

	...
	
	# You can find the exact names of your targets by running:
	#
	# xcodebuild -list
	#
	# from your project's root directory
	target 'MyApp' do
	  pod 'Webtrends-SDK/Core', '~> 3.0'
	  pod 'Webtrends-SDK/Optimize', '~>3.0'	  
	end
	
	target 'MyApp WatchKit Extension' do
	  pod 'Webtrends-SDK/Watch', '~> 3.0'
	end	

##Step 3 – Install
Once your `Podfile` is saved, you're ready to actually install Pods using the following command, still from your project's root directory:

	$ pod install

This process will create a new file in your root directory called `[Project Name].xcworkspace`. Open your project from that file from now on, rather than the `.xcodeproj` that was used before.

##Step 4 – Set up webtrends.plist

You can download an example copy of `webtrends.plist` [here][plist]. Add the file to your project, open it, and edit it according to your project needs.

####Required Entries
**For Analytics(tm):**

* **wt_dc_dcsid:** Your Webtrends Analytics(tm) DCSID for this application

**For Optimize(tm):**

* **wt_opt_key_token:** Your Webtrends Optimize(tm) key token
* **wt_opt_domain_id:** Your Optimnize(tm) domain ID, also known as an account number
* **wt_opt_account_guid:** Your Webtrends Account GUID. This is a 10-character alphanumeric string. Do not include the ".acct.webtrends.com" suffix.
* **wt_opt_project_locations:** A comma-separated list of project locations. For this example, it will contain the project location that you define above in the Optimize(tm) application.
* **wt_opt_staging_mode:** Set this to *true* if you have created Optimize(tm) tests in staging mode. This is commonly used while developing applications and Optimize(tm) tests. Once the application is ready for launch, remove this setting or set it to *false* and change the tests in Optimize(tm) to *Active*.


There are many configuration options available in `webtrends.plist`. You can read more in the [Analytics Advanced Configuration Guide][AnaConfig] or the [Optimize Advanced Configuration Guide][OptConfig].

##Additional Swift Setup

Once the Webtrends Pod has been set up, you'll have to import the SDK to integrate the library into your Swift-based application's bridging header file. If you've never worked with bridging headers before, follow the directions for manually creating one from Apple's [Using Swift with Cocoa and Objective-C][Swift] guide.

In a nutshell, you will need to create the bridging header in your application project, tell the compiler where it is, and update the search path to find the header files in the SDK.

In your bridging header, simply add the following line, which will make the entire core Webtrends SDK available to your swift app:

<div class="objective_c-sample-code"><pre><code>@import Foundation;
#import "WebtrendsSDK.h"</code></pre></div>

To actually use this header we need to tell the Swift compiler where it is, so open up your project’s build settings and find the **Swift Compiler — Code Generation** section. Change the project-level setting for Objective-C Bridging Header and change the value to point to our new header (e.g. YourApp/Bridging-Header.h for a project called 'YourApp’).

Finally, tell the compiler where to find the header files you will be using in the bridging header. Find the section called **Search Paths** and change the project-level setting for User Header Search Paths, adding a recursive entry for the **Pods** directory. The entry will look like **Pods/\*\***.

The [Webtrends WatchKit SDK Guide][WatchGuide] has more information on setting up bridging headers for WatchKit apps.

##Step 5 – Start logging events
Once your `webtrends.plist` file is in order, you're ready to start logging events. In its default configuration, Webtrends SDK will log a lot of useful events for you automatically (See the [Automatic Events Guide][AutoEvents] for more information). To manually log events of your own, use any of the `triggerEvent` methods documented in <WTDataCollector>. See the [Manual Event Logging Guide][ManualEvents] for more information.

##Sample Applications
We have developed a set of sample applications that illustrate and document how to configure an application to use the Webtrends iOS SDK. These applications may be cloned or downloaded directly from our public [Sample Applications Github repo][SampleAppRepo]. This repo contains both Objective-C and Swift sample applications and demonstrate all major aspects of implementation. To get started, see our [Sample Apps Guide][SampleAppGuide].


##All Documentation
The complete documentation for the Webtrends iOS SDK can be found at 
[http://help.webtrends.com/en/ios/](http://help.webtrends.com/en/ios/).


