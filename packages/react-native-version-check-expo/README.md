# react-native-version-check

[![npm version][npm-image]][npm-url]
[![npm downloads][downloads-image]][downloads-url]
[![Build Status][travis-image]][travis-url]
[![DevDependencies Status][dev-dependencies-image]][dev-dependencies-url]
[![Known Vulnerabilities][vulnerabilities-image]][vulnerabilities-url]

A version checker for react-native applications.
This library gets the latest app version by parsing google play store, apple app store's app information or custom url.
Parsing code is referenced from [here](http://itmir.tistory.com/524)

### expo
react-native-version-check supports [expo](https://expo.io)! with [react-native-version-check-expo](https://npmjs.org/packages/react-native-version-check-expo)
- usage
```js
// import
import VersionCheck from 'react-native-version-check-expo'

VersionCheck.getCountry() // this will return promise!!

VersionCheck.getCountryAsync().then(country => console.log(country)) // or use this!
```

## Usage
```javascript
import { Linking } from 'react-native';
import VersionCheck from 'react-native-version-check-expo';

// START: iOS Only

VersionCheck.setAppID(APP_ID);                    // Your App ID for App Store URL
VersionCheck.setAppName(APP_NAME);                // Your App Name for App Store URL

// Your app's id, name and country info will be use for App Store URL like
// https://itunes.apple.com/{COUNTRY}/app/{APP_NAME}/id{APP_ID}

// END: iOS Only

VersionCheck.getCountryAsync()
  .then(country => console.log(country));          // KR
console.log(VersionCheck.getPackageName());        // com.reactnative.app
console.log(VersionCheck.getCurrentBuildNumber()); // 10
console.log(VersionCheck.getCurrentVersion());     // 0.1.1

VersionCheck.getLatestVersion()
  .then(latestVersion => {
    console.log(latestVersion);    // 0.1.2
  });

VersionCheck.getLatestVersion({
    provider: 'store'
  })
  .then(latestVersion => {
    console.log(latestVersion);    // 0.1.2
  });

VersionCheck.getLatestVersion({
  forceUpdate: true,
  provider: () => fetch('http://your.own/api')
    .then(r => r.json())
    .then(({version}) => version),   // You can get latest version from your own api.
  fetchOptions: {
    method: "GET"
  }
}).then(latestVersion =>{
  console.log(latestVersion);
});

VersionCheck.needUpdate()
  .then(async res => {
    console.log(res.isNeeded);    // true
    if (res.isNeeded) {
      Linking.openURL(await VersionCheck.getStoreUrl());  // open store if update is needed.
    }
  });

VersionCheck.needUpdate({
  depth: 2
}).then(res => {
  console.log(res.isNeeded);
  // false; because first two fields of current and the latest versions are the same as "0.1".
});

VersionCheck.needUpdate({
  currentVersion: "1.0",
  latestVersion: "2.0"
}).then(res => {
  console.log(res.isNeeded);  // true
});

VersionCheck.needUpdate({
  currentVersion: "1.1",
  latestVersion: "2.0",
  semantic: true
}).then(res => {
  console.log(res.isNeeded);  // false
});

```

## Methods

- <a name="setAppID" href="#setAppID">#</a>**`setAppID(appId: Number)`** _()_ - Sets app id of application for App Store Url. **[Required only for iOS Apps]**
- <a name="setAppName" href="#setAppName">#</a>**`setAppName(appName: String)`** _()_ - Sets app name of application for App Store Url. **[Required only for iOS Apps]**
- <a name="getCountry" href="#getCountry">#</a>**`getCountry()`** _(country: String)_ - Returns device's country code of 2 characters.
- <a name="getCountryAsync" href="#getCountryAsync">#</a>**`getCountryAsync()`** _(Promise<country: String>)_ - Returns device's country code of 2 characters.
- <a name="getPackageName" href="#getPackageName">#</a>**`getPackageName()`** _(packageName: String)_ - Returns package name of app.
- <a name="getCurrentBuildNumber" href="#getCurrentBuildNumber">#</a>**`getCurrentBuildNumber()`** _(buildNumber: Number)_ - Returns current app build number.
- <a name="getStoreUrl" href="#getStoreUrl">#</a>**`getStoreUrl([option: Object])`** _(storeUrl: String)_ - Returns url of Play Market or App Store of app.
  - Option

    Field | Type | Default
    --- | --- | ---
    appID | _string_ | App ID which was set by [setAppID()](#setAppID)
    appName | _string_ | App Name which was set by [setAppName()](#setAppName)

- <a name="getStoreUrlAsync" href="#getStoreUrlAsync">#</a>**`getStoreUrlAsync([option: Object])`** _(Promise<storeUrl: String>)_ - Returns url of Play Market or App Store of app.
  - Option

    Field | Type | Default
    --- | --- | ---
    appID | _string_ | App ID which was set by [setAppID()](#setAppID)
    appName | _string_ | App Name which was set by [setAppName()](#setAppName)

- <a name="getCurrentVersion" href="#getCurrentVersion">#</a>**`getCurrentVersion()`** _(currentVersion: String)_ - Returns current app version.
- <a name="getLatestVersion" href="#getLatestVersion">#</a>**`getLatestVersion([option: Object])`** _(Promise<latestVersion: String>)_ - Returns the latest app version parsed from url. Returns `null` when parsing error occurs.
  - Option

    Field | Type | Default
    --- | --- | ---
    forceUpdate | _boolean_ | ```false```
    provider | _string_ or _function_ | provider name or function that returns promise or value of the latest version
    fetchOptions | _object_ | isomorphic-fetch options (https://github.github.io/fetch/)

- <a name="needUpdate" href="#needUpdate">#</a>**`needUpdate([option: Object])`** _(Promise<result: Object>)_ - Returns an object contains with boolean value whether update needed, current version and latest version. Current and the latest app versions are first split by delimiter, and check each split numbers into depth.
  - Option

    Field | Type | Default
    --- | --- | ---
    currentVersion | _string_ | app's current version from [getCurrentVersion()](#getCurrentVersion)
    latestVersion | _string_ | app's latest version from [getLatestVersion()](#getLatestVersion)
    depth | _number_ | ```Infinity```
    delimiter | _string_ | ```"."```
    semantic | _boolean_ | ```false```
    forceUpdate | _boolean_ | ```false```
    provider | _string_ or _function_ | provider name or function that returns promise or value of the latest version
    fetchOptions | _object_ | isomorphic-fetch options (https://github.github.io/fetch/)

  - Result

    Field | Type
    --- | ---
    isNeeded | _boolean_
    currentVersion | _string_
    latestVersion | _string_



## License
MIT


[npm-image]: https://img.shields.io/npm/v/react-native-version-check-expo.svg
[npm-url]: https://npmjs.org/package/react-native-version-check-expo
[downloads-image]: https://img.shields.io/npm/dm/react-native-version-check-expo.svg
[downloads-url]: https://npmjs.org/package/react-native-version-check-expo
[travis-image]: https://travis-ci.org/kimxogus/react-native-version-check.svg?branch=develop
[travis-url]: https://travis-ci.org/kimxogus/react-native-version-check
[dev-dependencies-image]: https://david-dm.org/kimxogus/react-native-version-check/dev-status.svg
[dev-dependencies-url]: https://david-dm.org/kimxogus/react-native-version-check?type=dev
[vulnerabilities-image]: https://snyk.io/test/github/kimxogus/react-native-version-check/badge.svg
[vulnerabilities-url]: https://snyk.io/test/github/kimxogus/react-native-version-check

