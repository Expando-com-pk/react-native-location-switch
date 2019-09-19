
# React Native Location Switch

A react Native module to enable location based services on Android and IOS.

<img src="preview/previewAndroid.gif" width="200" /> <img src="preview/previewIOS.gif" width="193" />

## This was tested with
- react-native >= 0.57.5
- android buildToolsVersion 27.0.3
- gradle build tools 3.1.4


## Installation Android

1. npm install react-native-location-switch-pk

2. add the following 2 lines to your <project>/android/settings.gradle file
   ```
   include ':react-native-location-switch'
   project(':react-native-location-switch').projectDir = new File(settingsDir, '../node_modules/react-native-location-switch-pk/android')
   ```

3. add the following line to your <project>/android/app/build.gradle file
   ```
   implementation project(':react-native-location-switch')
   ```

4. add the "LocationSwitchPackage" import into your MainApplication.java file:
   ```java
   import org.pweitz.reactnative.locationswitch.LocationSwitchPackage;
   ```

5. add the "LocationSwitchPackage" into your MainApplication.java file (getPackages method):
   ```java
    @Override
    protected List<ReactPackage> getPackages() {
      return Arrays.<ReactPackage>asList(
          new MainReactPackage(),
          ... // your other react native packages
          new LocationSwitchPackage()
      );
    }

   ```
6. add the "LocationSwitch" import into your MainActivity.java file:
   ```java
   import org.pweitz.reactnative.locationswitch.LocationSwitch;
   ```

7. add the following code into your MainActivity.java file:
    ```java   
    @Override
    public void onActivityResult(int requestCode, int resultCode, Intent data) {
        super.onActivityResult(requestCode, resultCode, data);
        LocationSwitch.getInstance().onActivityResult(requestCode, resultCode);
    }
    ```

## Post Installation Android

After you have installed on android and you do the first build **you are going to see the following error**:
```
FAILURE: Build failed with an exception.

* What went wrong:
Execution failed for task ':app:preDebugBuild'.
> Android dependency 'com.google.android.gms:play-services-base' has different version for the compile (11.0.0) and runtime (16.1.0) classpath. You should manually set the same version via DependencyResolution
```

In the error above, **it tells us that the runtime version for 'com.google.android.gms:play-services-base' is '16.1.0'**, so in order to solve this **we must add 16.1.0 as a forced choice in <project>/android/app/build.gradle** like this:
```
configurations.all {
    resolutionStrategy {
        force "com.google.android.gms:play-services-base:16.1.0"
    }
}
```

When you clean and build after this, it should just work.

## Installation IOS

1. Open the project in xCode, left click on the Libraries folder -> Add files to ... and select 
   ```
   ./node_modules/react-native-location-switch-pk/ios/RNReactNativeLocationSwitch.xcodeproj
   ```

2. Open the project -> Build Phases -> Link Binary With Libraries and select libRNReactNativeLocationSwitch.a


## React Native Interface

```javascript
LocationSwitch.enableLocationService(
    interval,
    requestHighAccuracy,
    successCallback,
    errorCallback
);
```
```javascript
LocationSwitch.isLocationEnabled(
    successCallback,
    errorCallback
);
```

Option | Default | Info
------ | ------- | ----
interval | 1000 | Update interval in ms (ignored on IOS)
requestHighAccuracy | false | If true, highest accuracy is requested. If false, "block" level accuracy is requested (ignored on IOS)
successCallback | null | Is called when the user allows access to the location services or when the location services are already enabled
errorCallback | null | Is called when the user denies access to the location services

## Usage

```javascript
import React, { Component } from 'react';
import { AppRegistry, Text, View, TouchableOpacity, StyleSheet, Alert } from 'react-native';
import LocationSwitch from 'react-native-location-switch-pk';

const style = StyleSheet.create({
  container: {
    flex: 1,
    alignItems: 'center',
    justifyContent: 'center',
  },
  button: {
    padding: 20,
  },
  text: {
    fontSize: 20,
  },
  textSuccess: {
    fontSize: 20,
    color: 'green',
  },
});

export default class LocationSwitchApp extends Component {

  constructor(props) {
    super(props);

    this.state = { locationEnabled: false };
    this.onEnableLocationPress = this.onEnableLocationPress.bind(this);
  }

  componentDidMount() {
    LocationSwitch.isLocationEnabled(
      () => {
        Alert.alert('Location is enabled');
        this.setState({ locationEnabled: true });
      },
      () => { Alert.alert('Location is disabled'); },
    );
  }

  onEnableLocationPress() {
    LocationSwitch.enableLocationService(1000, true,
      () => { this.setState({ locationEnabled: true }); },
      () => { this.setState({ locationEnabled: false }); },
    );
  }

  renderLocationStatus() {
    if (this.state.locationEnabled) {
      return <Text style={style.textSuccess} >Location enabled</Text>;
    }
    return <Text style={style.text}>Location disabled</Text>;
  }

  render() {
    return (
      <View style={style.container}>
        <TouchableOpacity style={style.button} onPress={this.onEnableLocationPress}>
          <Text style={style.text}>Enable location service</Text>
        </TouchableOpacity>
        {this.renderLocationStatus()}
      </View>
    );
  }
}

AppRegistry.registerComponent('reactClientSandbox', () => LocationSwitchApp);
```
