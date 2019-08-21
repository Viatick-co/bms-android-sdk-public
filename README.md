### Installation
- Download the [source.zip](https://github.com/Viatick-co/bms-android-sdk-public/archive/master.zip) code from Github and extract the zip file.
- In Android Studio, go to ```File > New > Import Module```, choose the Source directory as the folder you have just extracted.
- In New Module selection, only tick "Import" on the module with source location "bms-android-sdk" , untick all the rest (if any) then click "Finish".
- Under your project ```Gradle Scripts```, go to the ```build.gradle (Module: app)``` file (be careful as there are 3 different ```build.gradle``` files). Under the ```dependencies``` block, add this line at the end of it:

```gradle
compile project(":bms-android-sdk")
```

##### The ```dependencies``` block now might look like:
```gradle
dependencies {
    compile fileTree(dir: 'libs', include: ['*.jar'])
    testCompile 'junit:junit:4.12'
    compile 'com.android.support:appcompat-v7:23.1.1'
    compile project(":bms-android-sdk")
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
