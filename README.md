### Installation
- Download the  `bms-android-sdk-release.aar` from Github.
- In Android Studio, go to ```File > New > New Module > Import .JAR/.AAR Package```, choose the AAR file, then click `Finish
- Make sure the library is listed at the top of your settings.gradle file, as shown here for library named `bms-android-sdk-realease`:
`include ':app', ':bms-android-sdk-realease'`
- Open the app module's `build.gradle` file and add new lines to the dependencies block as shown in the following snippet:

```gradle
    implementation project(':bms-android-sdk-release')
    implementation "com.android.support:design:27.1.1"
    implementation "org.altbeacon:android-beacon-library:2.8.1"
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
    implementation "org.altbeacon:android-beacon-library:2.8.1"
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
        
        // configure bms sdk settings at first
        // to enable alert
        // to enable minisite feature and type of view (AUTO or LIST)
        // to enable customer tracking feature
        // to enable customer attendance feature
        ViaBmsCtrl.settings(true, true,
                true,  ViaBmsUtil.MinisiteViewType.LIST, null,
                true, true,
                true, 5, 5);

        // method to attach delegate
        // 4 callbacks
        // sdkInited
        // customerInited
        // if attendance is enable
        // checkin and checkout
        ViaBmsCtrl.setDelegate(this);
        
        // this method must be called at first to do handshake with bms
        // sdkInited callback will be called after initialization
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
            ViaBmsCtrl.initCustomer("PASTE IDENTIFIER OF CUSTOMER HERE", "000000000", "example@email.com", zones);
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

    // override this method
    @Override
    public void onRequestPermissionsResult(int requestCode, String[] permissions, int[] grantResults) {
        super.onRequestPermissionsResult(requestCode, permissions, grantResults);

        // must put this line of code in after super.onRequestPermissionsResult
        ViaBmsCtrl.onRequestPermissionsResult(this, requestCode, permissions, grantResults);
    }

    // override this method
    @Override
    public void onResume() {
        super.onResume();

        // must put this line of code in after super.onResume()
        ViaBmsCtrl.onResume();
    }

    // start sdk service
    public void startSDK(View view) {
        // these methods are to check sdk initation and bms is running or not
        boolean sdkInited = ViaBmsCtrl.isSdkInited();
        boolean bmsRunning = ViaBmsCtrl.isBmsRunning();

        if (!bmsRunning && sdkInited) {
            Log.d(TAG, "Bms Starting");
            
            // this method is to start bms service if it is not running
            // you can call this method to restart without calling initSdk again
            ViaBmsCtrl.startBmsService();
        }

    }
    
    // stop sdk service
    public void stopSDK(View view) {
     // this method is to stop the bms service
        ViaBmsCtrl.stopBmsService();
    }
}
```
