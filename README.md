# Comprehensive End-2-End Steps for Mobile Automation
## To successfully run automation on real devices (iOS / Android) using Appium
## Configuration
* macOS Sierra (10.2.1)
* Xcode 8.1
* iOS 10.1 (iPhone 6 Plus)
* Android 6.0 (Moto X (2nd Generation))
* Appium 1.6.0-beta3
* ScalaTest Framework

## Pre Requisites
### 1. Install the following
    Install xcode
   `Go to AppStore -> Search 'xcode' and install'

   `xcode-select --install` (accept the prompts,this is to install command line tools)

   `/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"`

   `brew install npm `  

   `npm install appium-doctor`

   `brew install carthage`

   `brew install ideviceinstaller`

   `npm install -g ios-deploy`
  
   `npm install -g deviceconsole`

   `npm install -g authorize-ios` (For iOS simulators only)

### 2. Installing [ios-webkit-debug-prox](https://github.com/google/ios-webkit-debug-proxy)
  
   `brew install —HEAD libimobiledevice`

   `brew install ios-webkit-debug-proxy`

### Making sure you device is connected to Mac

    2.1 First just run `ios_webkit_debug_proxy` 
 
    2.2 Output of the above command should be
      `Listing devices on :9221
             Connected :9222 to <Your Name> (40 digit UDID)`

    2.3 How to cross check your UDID?
     
     2.3.1 Connect your iPhone to Mac, Open iTunes
   
     2.3.2 Click the phone icon 

     2.3.3 Click "Serial Number" multiple time until you see UDID
 
    2.4. Exit the above command (CTRL + C or CTRL + Z) from step 1

    2.5. Now execute `ios_webkit_debug_proxy -c <device udid>:27753 -d`
  
     2.5.1 You should see something like this
         `ss.add_fd(3)
         ss.recv fd=3 len=294
         ss.recv fd=3 len=684
         ss.add_server_fd(4)
         ss.add_fd(6)
         wi.send_packet[245]:`
  
    2.6. Leave it running.You have to make sure this works, otherwise you CANNOT run automation on a real device

## Appium and Updates

  3.1  Installing Appium `git clone https://github.com/appium/appium.git`
       
  3.2 `cd appium`

  3.3 `npm install`

  3.4 `cd node_modules/appium-xcuitest-driver/WebDriverAgent` (iOS specific steps below)

  3.5 `carthage update` (This will update the depedencies from Carfile)
 
  3.6 `mkdir -p Resources/WebDriverAgent.bundle`

  3.7 `sh ./Scripts/bootstrap.sh -d`
 
### Deploying WebDriverAgent app to your device (iOS specific)

  3.8 Open appium/node_modules/appium-xcuitest-driver/WebDriverAgent/WebDriverAgent.xcodeproj in Xcode

  3.9 ADD team name (You need to create a team in http://developer.apple.com) Note: If anyone can add clear notes here please do)

  3.10 Choose the correct deployment target (your iPhone iOS version e.g.: 10.1)

  3.11 Repeat this for “WebDriverAgentLib” and “WebDriverAgentRunner” in Xcode

  3.11.1 ![WebDriverAgent XCode](https://cloud.githubusercontent.com/assets/12143988/18771980/2dc4f412-80f8-11e6-9ad6-c6883dbf6a03.png)

  3.12 Connect your iPhone to your Mac and choose it instead of a simulator

  3.13 Build and it should be successful

### Cross Checking the above setup is working
 
  3.14 `cd node_modules/appium-xcuitest-driver/WebDriverAgent`

  3.15 `xcodebuild -project WebDriverAgent.xcodeproj -scheme WebDriverAgentRunner -destination id=<device UDID> test` 

  3.16 You should see `Listening on USB`. Sometimes it takes ages for the test to run, then remove 'test' from above command line and run.

## iPhone Setting changes

 4.1 Open iPhone Settings -> Developer -> Enable “UI Automation"

## Add support for Instrument Without Delay

 5.1 `git clone https://github.com/appium/appium-instruments.git`
  
 5.2 `cd appium-instruments`

 5.3 `./bin/xcode-iwd.sh /Applications/Xcode.app /Users/<username>/appium-instruments/`

## Simulator
  
 6.1 If you dont give UDID, it will open up the simulator to run automation

 6.2 `sudo authorize-ios`

## Running Appium
 7.1 `cd <path/to/git/clone>/appium`
 
 7.2  `node .`
 
 7.3 refer step 2.5 - `ios_webkit_debug_proxy -c <device udid>:27753 -d` should be running as well

## To run more than one instance of Appium

 8.1 `node .` in one terminal
 
 8.2 `node . -p 4724` in another

 8.3 Pass `"http://0.0.0.0:4723/wd/hub` to Android and `"http://0.0.0.0:4724/wd/hub` to iOS during webDriver creation

## Run your automation

 9.1 iOS: Now you can run your automation pointing to the Appium url with correct capabilities and UDID     

 9.2 Android: Use the same url, and the capability below to run

## ScalaTest automation code with correct capabilities
`          var url = "http://0.0.0.0:4723/wd/hub"`
          `val remoteDriver = new RemoteWebDriver(new URL(url), appiumCapabilities("ios"))`
          `new Augmenter().augment(remoteDriver)`
        `}`


    `def appiumCapabilities(device: String = "iPhone 6 Plus", version: String = "10.1"): DesiredCapabilities = {`
    `var caps= new DesiredCapabilities()`
    `if (device.equals("ios")) {`
      `caps = DesiredCapabilities.iphone();`
      `caps.setCapability("appiumVersion", "1.6.0-beta3");`
      `caps.setCapability("deviceName", "iPhone 6 Plus");`
      `caps.setCapability("deviceOrientation", "portrait");`
      `caps.setCapability("platformVersion", "10.1");`
      `caps.setCapability("platformName", "iOS");`
      `caps.setCapability("browserName", "Safari");`
      `caps.setCapability("automationName", "XCUITest");`
      `caps.setCapability("newCommandTimeout", 5000);`
      `caps.setCapability("udid", "<your udid>")`
    `}else if (device.equals("android"))`
     `{`
      `caps = DesiredCapabilities.android();`
      `caps.setCapability("appiumVersion", "1.6.0-beta3");`
      `caps.setCapability("deviceName", "Moto X (2nd Generation)");`
      `caps.setCapability("deviceOrientation", "portrait");`
      `caps.setCapability("browserName", "chrome");`
      `caps.setCapability("platformVersion", "6.0");`
      `caps.setCapability("platformName", "Android");`
    `}`

      `return caps`

  `}`

  ### Running automation on a remote device

10.1 Ask your colleague to setup "Appium and Updates" and section 3,4,5 and 7.

10.2 Get his IP address and make sure its reachable `ping x.x.x.x` and you should get reply

10.3 Instead of `http://0.0.0.0:4723/wd/hub`, use `http://<your colleague\'s ip>:4723/wd/hub`

10.3. Make sure your capabilities are correct and now run your automation


  
   
