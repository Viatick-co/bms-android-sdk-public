### Installation
- Download the  `bms-android-sdk-release.aar` from Github.
- In Android Studio, go to ```File > New > New Module > Import .JAR/.AAR Package```, choose the AAR file, then click `Finish
- Make sure the library is listed at the top of your settings.gradle file, as shown here for library named `bms-android-sdk-realease`:
`include ':app', ':bms-android-sdk-realease'`
- Open the app module's `build.gradle` file and add new lines to the dependencies block as shown in the following snippet:

```gradle
    implementation project(':bms-android-sdk-release')
    implementation "com.android.support:design:27.1.1"
    implementation "com.squareup.picasso:picasso:2.7+"
```

##### The ```dependencies``` block now might look like:
```gradle
dependencies {
    implementation fileTree(dir: 'libs', include: ['*.jar'])
    implementation 'androidx.appcompat:appcompat:1.0.2'
    implementation 'androidx.constraintlayout:constraintlayout:1.1.3'
    testImplementation 'junit:junit:4.12'
    androidTestImplementation 'androidx.test:runner:1.2.0'
    androidTestImplementation 'androidx.test.espresso:espresso-core:3.2.0'

    // another dependencies

    // bms dependencies
    implementation project(':bms-android-sdk-release')
    implementation "com.android.support:design:27.1.1"
    implementation "com.squareup.picasso:picasso:2.7+"
}
```

A message might appear to prompt you to sync the project, click "Sync Now" to proceed.

### Setup
##### Sample setup codes in MainActivity
```java

// implements delegate of SDK
// ViaBmsCtrlDelegate
public class MainActivity extends AppCompatActivity implements ViaBmsCtrl.ViaBmsCtrlDelegate {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        // Some codes...
        List<IBeacon> requestDistanceBeacons = new ArrayList<>();

        // add beacon you want to track distance, only use this if you
        // specifically needs to use the onDistanceBeacons callback
        requestDistanceBeacons.add(new IBeacon("uuid", major, minor));

        // Configure BMS sdk settings at first
        ViaBmsCtrl.settings(
          false, // enableAlert: whether to show notification when a minisite is triggered
          false, // enableBackground: whether to enable beacon scanning in background mode
          false, // enableSite: whether to enable minisite feature
          ViaBmsUtil.MinisiteViewType.LIST, // minisitesView: choose either 'LIST' to display minisite list or "AUTO" to auto popup the latest minisite
          null, // autoSiteDuration: if minisite view mode is 'AUTO', this specifies number of seconds that the minisite will switch
          false, // tracking: whether to enable tracking feature and send tracking data to BMS
          false, // enableMQTT: whether to use MQTT or normal RESTful endpoint to send tracking data
          false, // attendance: whether to enable attendance feature
          5, // checkinDuration: duration of the device staying in the authorized zones to be considered "checked in"
          20, // checkoutDuration: duration of the device staying out of the authorized zones to be considered "checked out"
          requestDistanceBeacons, // requestDistanceBeacons: ibeacons that you want to return distance callback
          BmsEnvironment.DEV, //  bmsEnvironment: BMS environment, default is "PROD", other options are "DEV" and "CHINA"
          5, // beaconRegionRange: range of beacon region that you want to filter, set it to 0 if you don't want it
          false, // beaconRegionUUIDFilter: when true, it will filter only the preset UUID broadcasted by the mobile device of this application
          false, // isBroadcasting: set it to true to broadcast as beacon (UUID, major, minor is generated by the system)
          false, // proximityAlert: set it to true to enable alert when proximity period with a filtered device exceed proximityAlertThreshold
          120 // proximityAlertThreshold: minimum how long is the proximity period, in seconds,
          1 // scanMode: scan mode, 0 for low-battery scan mode, 1 for balanced scan mode, 2 for low-latency scan mode
          );

        // method to attach delegate
        // 4 callbacks
        // sdkInited
        // customerInited
        // if attendance is enable
        // checkin and checkout
        ViaBmsCtrl.setDelegate(this);

        // this method must be called at first to do handshake with bms
        // sdkInited callback will be called after initialization
        // only call this after calling settings
        ViaBmsCtrl.initSdk(this, "PASTE_YOUR_BMS_APP_SDK_KEY_HERE");
    }

    // Some codes...

    // this method will be called after sdk initilization done
    // list of zones in the sdk application is passed here
    @Override
    public void sdkInited(boolean inited, List<ViaZone> zones) {
        Log.d(TAG, "Sdk inited " + inited);
        if (inited) {
            // this method must be called in order to enable attendance and tracking feature
            // authorizedZones is optional field
	        // sdkInited callback will be called after initialization
            ViaBmsCtrl.initCustomer("PASTE UNIQUE IDENTIFIER OF CUSTOMER HERE", "000000000", "example@email.com", zones);
        }
    }

    // this method will be called after customer processing done
    @Override
    public void customerInited(boolean inited) {
        Log.d(TAG, "Customer Inited " + inited);
    }

    // this method will be called if customer checkin
    @Override
    public void checkin() {
        Log.d(TAG, "Checkin Callback");
    }

    // this method will be called if customer checkout
    @Override
    public void checkout() {
        Log.d(TAG, "Checkout Callback");
    }

    // it is callback of request tracking distance
    @Override
    public void onDistanceBeacons(List<IBeacon> list) {

    }

    // callback when device site is loaded succcessfully or failed, will
    // return error message code when it fails
    // Possible error code:
    // - INVALID_BMS_ENVIRONMENT: The BMS environment indicates in the device site
    // url doesn't match with the environment on the SDK settings
    // - INVALID_SERIAL_CODE: URL doesn't include serial code or serial code is of
    // invalid format
    // - SDK_NOT_INITIATED: openDeviceSite is called before the SDK is initiated
    // - NO_MINISITE_SCHEDULE: no minisite is attached with the device at the moment,
    // could be due to the serial code not existed or the device schedule isn't attached
    @Override
    public void deviceSiteLoaded(boolean loaded, String error) {

    }

    // override this method
    @Override
    public void onResume() {
        super.onResume();

	      // if background is disabled in setting
        // must put this line of code in after super.onResume() to start service again
	      boolean sdkInited = ViaBmsCtrl.isSdkInited();
        boolean bmsRunning = ViaBmsCtrl.isBmsRunning();

        if (!bmsRunning && sdkInited) {
            Log.d(TAG, "Bms Starting");

            // this method is to start BMS service if it is not running
            // you can call this method to restart without calling initSdk again
            // only call this after SDK has been initiated
            ViaBmsCtrl.startBmsService();
        }
    }

    // start SDK service
    public void startSDK(View view) {
        // these methods are to check SDK initation and BMS is running or not
        boolean sdkInited = ViaBmsCtrl.isSdkInited();
        boolean bmsRunning = ViaBmsCtrl.isBmsRunning();

        if (!bmsRunning && sdkInited) {
            Log.d(TAG, "Bms Starting");

            // this method is to start BMS service if it is not running
            // you can call this method to restart without calling initSdk again
            ViaBmsCtrl.startBmsService();
        }

    }

    // stop SDK service
    public void stopSDK(View view) {
     // this method is to stop the BMS service
        ViaBmsCtrl.stopBmsService();
    }

    // open a device site URL (for example the site URL received
    // upon scanning NFC Tag managed by BMS). Can only call after the SDK is initiated
    public void openDeviceSite(String url) {
        ViaBmsCtrl.openDeviceSite(url);
    }
}
```
