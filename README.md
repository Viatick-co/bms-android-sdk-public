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
public class MainActivity extends AppCompatActivity implements ViaBmsCtrl.ViaBmsCtrlDelegate {
    ViaBmsCtrl viaBmsCtrl;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        // Some codes...

        viaBmsCtrl = new ViaBmsCtrl();

        // optional delegate
        viaBmsCtrl.viaBmsCtrlDelegate = this;

        // you can specify your customer information in order to enable attendance and tracking feature (optional)
        viaBmsCtrl.initCustomer("qe7sua", "88268722", "khoa.zany@gmail.com");

        // bms sdk setting (setting can change later and default values are false)
        viaBmsCtrl.settings(true, true,true,true,true);

        // initiate viatick bms sdk with your bms application sdk key (this function will not start the sdk service)
        viaBmsCtrl.initSdk(this, "pcd9i08ok58199qu0jq6e8bbvbo9ve2rpuud7dgnpo26b4vc0re");
    }

    // Some codes...

    // start sdk service
    public void startSDK(View view) {
        viaBmsCtrl.startBmsService();
    }

    // end sdk service
    public void stopSDK(View view) {
        viaBmsCtrl.stopBmsService();
    }

    // override this method
    @Override
    public void onRequestPermissionsResult(int requestCode, @NonNull String[] permissions, @NonNull int[] grantResults) {
        super.onRequestPermissionsResult(requestCode, permissions, grantResults);

        // need to put this line of code in after super.onRequestPermissionsResult
        viaBmsCtrl.onRequestPermissionsResult(this, requestCode, permissions, grantResults);
    }

    // override this method
    @Override
    public void onResume() {
        super.onResume();

        // need to put this line of code in after super.onResume()
        viaBmsCtrl.onResume();
    }

    public void inited (boolean status) {
      // do something after the SDK is init
      Log.i("ViaBmsSdk", String.valueOf(status));
    }
}
```
