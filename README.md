## App Tracking Transparency for Unity

### Introduction

This plugin allows your app/game to easily show the iOS 14 native App Tracking Transparency popup to your users, meeting Apple's requirement since iOS 14.

<p align="center">
<img src="https://github.com/Balaso-Software/app-tracking-transparency-for-unity/raw/master/img/screenshot.png" alt="drawing" width="320"/>
</p>

### Installation
#### Unity Asset Store
Get it on [Unity Asset Store](https://assetstore.unity.com/packages/slug/174256)

### Settings

Customize the app-tracking popup message and specify any SkAdNetwork ID to be added to your **Info.plist** file.

<p align="center">
<img src="https://github.com/Balaso-Software/app-tracking-transparency-for-unity/raw/master/img/settings.png" alt="drawing" width="320"/>
</p>

### Usage
#### Obtain current app-tracking authorization status

You can obtain, at any time, the current app-tracking authorization status by accessing `AppTrackingTransparency.TrackingAuthorizationStatus` property

```csharp
AppTrackingTransparency.AuthorizationStatus currentStatus = AppTrackingTransparency.TrackingAuthorizationStatus;
Debug.Log(string.Format("Current authorization status: {0}", currentStatus.ToString()));

```

#### Present the app-tracking authorization request to the end user

Register your callback to get notified with the authorization result and request tracking authorization

```csharp
AppTrackingTransparency.OnAuthorizationRequestDone += OnAuthorizationRequestDone;
AppTrackingTransparency.RequestTrackingAuthorization();
```

#### SkAdNetwork

Call `RegisterAppForAdNetworkAttribution` or `UpdateConversionValue` at anytime to support **SkAdNetwork** attribution system

```csharp
AppTrackingTransparency.RegisterAppForAdNetworkAttribution();
AppTrackingTransparency.UpdateConversionValue(3);

```
### Example

```csharp
using Balaso;
using UnityEngine;

/// <summary>
/// Example MonoBehaviour class requesting iOS Tracking Authorization
/// </summary>
public class AppTrackingTransparencyExample : MonoBehaviour
{
    private void Awake()
    {
#if UNITY_IOS
        AppTrackingTransparency.RegisterAppForAdNetworkAttribution();
        AppTrackingTransparency.UpdateConversionValue(3);
#endif
    }

    void Start()
    {
#if UNITY_IOS
        AppTrackingTransparency.OnAuthorizationRequestDone += OnAuthorizationRequestDone;

        AppTrackingTransparency.AuthorizationStatus currentStatus = AppTrackingTransparency.TrackingAuthorizationStatus;
        Debug.Log(string.Format("Current authorization status: {0}", currentStatus.ToString()));
        if (currentStatus != AppTrackingTransparency.AuthorizationStatus.AUTHORIZED)
        {
            Debug.Log("Requesting authorization...");
            AppTrackingTransparency.RequestTrackingAuthorization();
        }
#endif
    }

#if UNITY_IOS

    /// <summary>
    /// Callback invoked with the user's decision
    /// </summary>
    /// <param name="status"></param>
    private void OnAuthorizationRequestDone(AppTrackingTransparency.AuthorizationStatus status)
    {
        switch(status)
        {
            case AppTrackingTransparency.AuthorizationStatus.NOT_DETERMINED:
                Debug.Log("AuthorizationStatus: NOT_DETERMINED");
                break;
            case AppTrackingTransparency.AuthorizationStatus.RESTRICTED:
                Debug.Log("AuthorizationStatus: RESTRICTED");
                break;
            case AppTrackingTransparency.AuthorizationStatus.DENIED:
                Debug.Log("AuthorizationStatus: DENIED");
                break;
            case AppTrackingTransparency.AuthorizationStatus.AUTHORIZED:
                Debug.Log("AuthorizationStatus: AUTHORIZED");
                break;
        }

        // Obtain IDFA
        Debug.Log($"IDFA: {AppTrackingTransparency.IdentifierForAdvertising()}");
    }
#endif
}   

```
