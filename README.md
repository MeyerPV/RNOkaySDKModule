### Installation

##### 1) Create folder "custom_modules" in your project root folder: 
---
* Project_Name/custom_modules
##### 2) Added folder with library to your "custom_modules" folder:
---
* Project_Name/custom_modules/OkaySDK
##### 3) Added to package.json "dependencies":
---
*  "react-native-okay-sdk": "file:custom_modules/OkaySDK"
##### 4) Install node_modules:
---
```sh
$ npm install
```
##### 5) Link library with react-native:
---
```sh
$ react-native link react-native-okay-sdk
```
##### 6) Link library with react-native:
---
* Open Project_Name/android/build.gradle
* Set minSdkVersion in build.gradle
```sh
buildscript {
    ext {
        buildToolsVersion = "28.0.3"
        minSdkVersion = 21 // Change this to 21
        compileSdkVersion = 28
        targetSdkVersion = 28
        supportLibVersion = "28.0.0"
    }
    .....
}
```
* Added maven repository to build.gradle
```sh
allprojects {
    repositories {
        mavenLocal()
        google()
        jcenter()
        maven {
            // All of React Native (JS, Obj-C sources, Android binaries) is installed from npm
            url "$rootDir/../node_modules/react-native/android"

        }
        // Begin: Added This
        maven {
            url 'https://dl.bintray.com/itrtestorg/maven'
        }
        // End:
    }
}
```
##### 7) Added permissions to AndroidManifest.xml:
---
* Open Project_Name/android/src/main/AndroidManifest.xml
* Added user-permissions to AndroidManifest.xml
````sh
    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="android.permission.READ_PHONE_STATE"/>
    <uses-permission android:name="android.permission.ACCESS_WIFI_STATE"/>
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE"/>
    <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>
    <uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE"/>
````
##### 8) Added databinding and multidex for android: 
---
* open Project_Name/android/app/build.gradle
```sh
android {
    compileSdkVersion rootProject.ext.compileSdkVersion

    compileOptions {
        sourceCompatibility JavaVersion.VERSION_1_8
        targetCompatibility JavaVersion.VERSION_1_8
    }
    // Begin Added DataBinding
    dataBinding {
        enabled = true
    }
    // End
    defaultConfig {
       ...
       multiDexEnabled true // Added this line
    }
    ...
}
```

##### 9) Install react-native Firebase:
* https://rnfirebase.io/docs/v5.x.x/installation/initial-setup
* https://rnfirebase.io/docs/v5.x.x/installation/android
* https://rnfirebase.io/docs/v5.x.x/messaging/android

### Usage

##### Allowed methods:
* permissionRequest()
```sh 
     RNOkaySdk.permissionRequest().then(response => console.log(response)); // Response: Array or required permissions
```
* init(endpoint) // PSSAddress for example 'http://protdemo.demohoster.com'
```sh 
    CompontentDidMount() {
        RNOkaySdk.init("http://protdemo.demohoster.com").then(response =>
            ...
        );
    }
```
* isEnrolled()
* isReadyForAuthorization()
* authorization(SpaAuthorizationData) // Called after receive message from firebase
```sh
    firebase.messaging().onMessage(message => {
      startAuthorization(message.data.sessionId);
    });
```
```sh
    startAuthorization = (sessionId) => {
        firebase.iid().get()
          .then(instanceID => {
            RNOkaySdk.authorization({
              SpaAuthorizationData: {
                sessionId: sessionId, // Received from firebase messaging
                appPNS: instanceID,
                pageTheme: { // Page Theme customization, if you don't want customization: pageTheme: null
                    actionBarTitle: "YOUR_ACTION_BAR_TITLE"
                    actionBarBackgroundColor: 5, 
                    actionBarTextColor: 10,
                    buttonTextColor: 15,
                }
              }
            }).then(response => console.log(response));
          })
          .catch(error => console.log(error));
    }
```
* enrollProcedure(SpaEnrollData)
```sh
    firebase.iid().get()
      .then(instanceID => {
        RNOkaySdk.enrollProcedure({
          SpaEnrollData: {
            appPns: instanceID,
            pubPss: pubPssBase64, // public Pss key https://github.com/Okaythis/okay-example/wiki/Mobile-Client-Settings
            installationId: "9990", // installationId https://github.com/Okaythis/okay-example/wiki/Mobile-Client-Settings
            pageTheme: { // Page Theme customization, if you don't want customization: pageTheme: null. All property with color is int.
               actionBarTitle: "YOUR_ACTION_BAR_TITLE"
               actionBarBackgroundColor: 5, 
               actionBarTextColor: 10,
               buttonTextColor: 15,
            }
          }
        }).then(response => console.log(response));
      })
      .catch(error => console.log(error));
```
