# Bramble Unity Module Package

Download and import the package into your Unity Project.

Sample Scenes of Authorize App Button and Wallet Scenes are in the Bramble folder.

The Plugins folder contains the Bramble JAR Plugin which is required for the Oauth Functionality to work.

Placeholder Email: rahul.soshte47@gmail.com
Password: 123456

# How to create Bramble Oauth JAR plugin
**1.** Open Android Studio

**2.** Create New Empty Project. Create New Module. Select Android Library.
![Android Library Module](images/1401.png)

**3.** Ensure package name is com.dan.chunkbreaker and Library Name as Chunkbreaker. Ensure Language is Java.
![Android Library Module](images/1402.png)

**4.** Now copy the file classes.jar at this Address Path in your Unity Editor
```
C:\Program Files\Unity\Hub\Editor\2018.4.18f1\Editor\Data\PlaybackEngines\AndroidPlayer\Variations\mono\Release\Classes\
```
into Android Studio 
```
Chunkbreaker/libs/
```

**5.** Open Files -> Project Structure -> Dependencies. Click on Chunkbreaker. Add JAR Dependency libs/classes.jar
![Add Dependency](images/1404.png)


**6.** Create a MainActivity.java file in Chunkbreaker/src/main/java/com.dan.chunkbreaker/

```
package com.dan.chunkbreaker;

import com.unity3d.player.UnityPlayer;
import com.unity3d.player.UnityPlayerActivity;
import android.content.Intent;
import android.net.Uri;
import android.os.Bundle;
import android.util.Log;

public class MainActivity extends UnityPlayerActivity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);

        Intent gameIntent = new Intent(this,com.unity3d.player.UnityPlayerActivity.class);
//        setContentView(R.layout.activity_main);

        if(UnityPlayer.currentActivity == null){
            startActivity(gameIntent);
        }
    }

    @Override
    protected void onNewIntent(Intent intent)
    {
        super.onNewIntent(intent);
        handleAccessToken(intent);
    }

    private void handleAccessToken(Intent intent){
        Uri uri = intent.getData();
        if(uri != null && uri.toString().startsWith("chunkbreaker")){
            Log.d("Unity", "About to message unity with return code:"+uri.toString());
            UnityPlayer.UnitySendMessage("CustomAndroidPlugin","PassReturnCode",uri.toString());
        }
    }
}
```

**7.** Your Chunkbreaker/build.gradle file will look something like this 
```
apply plugin: 'com.android.library'

android {
    compileSdkVersion 28
    buildToolsVersion "29.0.3"


    defaultConfig {
        minSdkVersion 15
        targetSdkVersion 28
        versionCode 1
        versionName "1.0"

        testInstrumentationRunner "androidx.test.runner.AndroidJUnitRunner"
        consumerProguardFiles 'consumer-rules.pro'
    }

    buildTypes {
        release {
            minifyEnabled false
            proguardFiles getDefaultProguardFile('proguard-android-optimize.txt'), 'proguard-rules.pro'
        }
    }

}

dependencies {
    implementation fileTree(dir: 'libs', include: ['*.jar'])

    implementation 'androidx.appcompat:appcompat:1.1.0'
    implementation 'androidx.constraintlayout:constraintlayout:1.1.3'
    testImplementation 'junit:junit:4.12'
    androidTestImplementation 'androidx.test.ext:junit:1.1.1'
    androidTestImplementation 'androidx.test.espresso:espresso-core:3.2.0'
    implementation files('libs\\classes.jar')
    implementation fileTree(dir: 'libs', include: ['*.aar', '*.jar'], exclude: [])


}

afterEvaluate {
    generateReleaseBuildConfig.enabled = false
}
```

Just ensure to add this line is present at the end of gradle file
```
afterEvaluate {
    generateReleaseBuildConfig.enabled = false
}
```


**8.** After that click on Gradle at the right hand side of Android Studio.

![Android Library Module](images/1405.png)

Then click on Chunkbreaker -> createFullJarRelease

![Android Library Module](images/1406.png)

In 'other' dropdown you will find this

![Android Library Module](images/1407.png)

Then copy the Chunkbreaker/build/full_jar/release/createFullJarRelease/full.jar in Unity's Android/Plugins/

Android Studio is also sometimes quite buggy. Delete any previous files present in this folder Chunkbreaker/build/full_jar/release/ before starting the 7 step or do a Complete Clean Project and redo the 7 step.

References:

http://technicalartistry.blogspot.com/2016/01/fitbit-unity-oauth-2-and-native.html

https://www.youtube.com/watch?v=v5s7BMPtK9E

http://oferei.com/2013/06/serverless-instagram-authentication/

# How to send Achievement Data to Bramble API

**Simple Achievement Data POST Request**
----
Request which will send Achievements data of the user to Bramble API. So if a user has completed a particular the game can send data to Bramble API with this request.

* **URL**

    /achievement/:achievement_id

* **Method:**

  `POST`
  
*  **URL Params**

   **Required:**

      `achivement_id=[hexadecimal]` 

* **Header Params**
  
  **Required:**

      `
      Authorization='Bearer ' + access_token_2(Received in authorization_grant Request in CustomAndroidPlugin.cs)
      `

* **Data Params**

  None

* **Success Response:**

  * **Code:** 200 <br />
    **Content:** `Added Achievement Successfully`

* **Sample Call:**

  ```csharp
        var achievementURL = "http://3.19.60.28:3000/achievement/5e871be30b3d43640a15e01b";
        UnityWebRequest uwr = UnityWebRequest.Post(authURL,"POST");
        uwr.SetRequestHeader("Authorization", "Basic Y2h1bmticmVha2VyOmNodW5rYnJlYWtlcnNlY3JldA==");
        yield return uwr.Send();
        var N = JSON.Parse(uwr.downloadHandler.text);

        if (N ["code"].Value == "400" || N ["code"].Value == "503" || N ["code"].Value == "500") {
            txt.text = "Error!"+ code;
            Debug.Log("Error!" + code);
        } else {
            Debug.Log(N);
        }
  ```