---
title: Azure Mobile Engagement Android SDK에 대한 위치 보고
description: Azure Mobile Engagement Android SDK에 대해 위치 보고를 구성하는 방법에 대해 설명합니다
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 6cab5ed1-b767-46ac-9f0b-48a4e249d88c
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: Java
ms.topic: article
ms.date: 08/12/2016
ms.author: piyushjo;ricksal
ms.openlocfilehash: 7978010bd92af18e3ab7c8ccab8dc682d7ef18df
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2018
---
# <a name="location-reporting-for-azure-mobile-engagement-android-sdk"></a>Azure Mobile Engagement Android SDK에 대한 위치 보고
> [!IMPORTANT]
> Azure Mobile Engagement는 2018/3/31에 사용이 중지됩니다. 이 페이지는 이후에 삭제됩니다.
> 

> [!div class="op_single_selector"]
> * [Android](mobile-engagement-android-integrate-engagement.md)
> 
> 

이 항목에서는 Android 응용 프로그램에서 위치 보고를 수행하는 방법을 설명합니다.

## <a name="prerequisites"></a>필수 조건
[!INCLUDE [Prereqs](../../includes/mobile-engagement-android-prereqs.md)]

## <a name="location-reporting"></a>위치 보고
위치가 보고되도록 하려는 경우 몇 줄의 구성을 `<application>` 태그와 `</application>` 태그 사이에 추가해야 합니다.

### <a name="lazy-area-location-reporting"></a>지연 영역 위치 보고
지연 영역 위치 보고를 통해 국가, 지역 및 장치와 연결된 위치를 보고할 수 있습니다. 이러한 유형의 위치 보고에서는 네트워크 위치(셀 ID 또는 WIFI 기반)만 사용합니다. 장치 영역은 세션당 한번 이하로 보고됩니다. GPS는 전혀 사용되지 않으므로 이러한 위치 보고 형식은 배터리에 미미한 영향을 미칩니다.

보고된 영역은 사용자, 세션, 이벤트 및 오류에 대한 지리적 통계를 계산하는 데 사용됩니다. 이 영역은 도달률 캠페인의 기준으로도 사용할 수 있습니다.

이 절차의 앞에서 설명한 구성을 사용하여 지연 영역 위치 보고를 사용하도록 설정할 수 있습니다.

    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
    engagementConfiguration.setLazyAreaLocationReport(true);
    EngagementAgent.getInstance(this).init(engagementConfiguration);

위치 권한도 지정해야 합니다. 다음 코드에서는 ``COARSE`` 권한을 사용합니다.

    <uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION"/>

앱에서 필요한 경우 대신 ``ACCESS_FINE_LOCATION`` 을 사용할 수 있습니다.

### <a name="real-time-location-reporting"></a>실시간 위치 보고
실시간 위치 보고를 통해 장치와 연결된 위도와 경도를 보고할 수 있습니다. 기본적으로 이러한 형식의 위치 보고에서는 셀 ID 또는 WIFI를 기반으로 하는 네트워크 위치만 사용합니다. 이 보고 기능은 응용 프로그램이 포그라운드로 실행되는 경우(예: 세션 중)에만 활성 상태입니다.

실시간 위치는 통계를 계산하는 데 사용되지 *않습니다* . 유일한 용도는 도달률 캠페인에서 실시간 지리적 펜스 \<Reach-Audience-geofencing\> 사용을 허용하는 것입니다.

실시간 위치 보고를 활성화하려면 시작 관리자 활동에서 Engagement 연결 문자열을 설정하는 위치에 코드 행을 추가합니다. 결과는 다음과 같이 표시됩니다.

    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
    engagementConfiguration.setRealtimeLocationReport(true);
    EngagementAgent.getInstance(this).init(engagementConfiguration);

        You also need to specify a location permission. This code uses ``COARSE`` permission:

            <uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION"/>

        If your app requires it, you can use ``ACCESS_FINE_LOCATION`` instead.

#### <a name="gps-based-reporting"></a>GPS 기반 보고
기본적으로 실시간 위치 보고에서는 네트워크 기반 위치만 사용합니다. 훨씬 더 정밀한 GPS 기반 위치의 사용을 설정하려면 구성 개체를 사용합니다.

    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
    engagementConfiguration.setRealtimeLocationReport(true);
    engagementConfiguration.setFineRealtimeLocationReport(true);
    EngagementAgent.getInstance(this).init(engagementConfiguration);

또한 아직 없는 경우 다음 권한도 추가해야 합니다.

    <uses-permission android:name="android.permission.ACCESS_FINE_LOCATION"/>

#### <a name="background-reporting"></a>백그라운드 보고
기본적으로 실시간 위치 보고는 응용 프로그램이 포그라운드로 실행되는 경우(예: 세션 중)에만 활성 상태입니다. 백그라운드에서도 보고를 활성화하려면 이 구성 개체를 사용합니다.

    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
    engagementConfiguration.setRealtimeLocationReport(true);
    engagementConfiguration.setBackgroundRealtimeLocationReport(true);
    EngagementAgent.getInstance(this).init(engagementConfiguration);

> [!NOTE]
> 응용 프로그램이 백그라운드에서 실행될 때 GPS를 활성화한 경우에도 네트워크 기반 위치만 보고됩니다.
> 
> 

사용자가 장치를 다시 부팅하는 경우 백그라운드 위치 보고가 중지됩니다. 부팅 시에 자동으로 다시 시작되도록 하려면 다음 코드를 추가합니다.

    <receiver android:name="com.microsoft.azure.engagement.EngagementLocationBootReceiver"
           android:exported="false">
        <intent-filter>
            <action android:name="android.intent.action.BOOT_COMPLETED" />
        </intent-filter>
    </receiver>

또한 아직 없는 경우 다음 권한도 추가해야 합니다.

    <uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />

## <a name="android-m-permissions"></a>Android M 권한
Android M부터는 일부 권한이 런타임 시 관리되며 사용자 승인이 필요합니다.

Android API Level 23을 대상으로 하는 경우 새 앱 설치에 대해서는 기본적으로 런타임 권한이 해제됩니다. 그렇지 않으면 기본적으로 활성화됩니다.

장치 설정 메뉴에서 이러한 권한을 설정/해제할 수 있습니다. 시스템 메뉴에서 권한을 해제하면 응용 프로그램의 백그라운드 프로세스가 중단되며 이것은 시스템 동작으로 백그라운드로 푸시 알림을 받는 기능에는 영향을 주지 않습니다.

Mobile Engagement 위치 보고의 컨텍스트에서 런타임 시 승인이 필요한 권한은 다음과 같습니다.

* `ACCESS_COARSE_LOCATION`
* `ACCESS_FINE_LOCATION`

표준 시스템 대화 상자를 사용하여 사용자로부터 권한을 요청합니다. 사용자가 승인하는 경우 ``EngagementAgent`` 에 실시간으로 변경을 고려할 것을 지시합니다. 그렇지 않으면 다음에 사용자가 응용 프로그램을 시작할 때 변경 내용이 처리됩니다.

다음은 사용 권한을 요청하고 ``EngagementAgent``에 긍정적인 경우 결과를 전달하는 응용 프로그램의 활동에 사용할 코드 샘플입니다.

    @Override
    public void onCreate(Bundle savedInstanceState)
    {
      /* Other code... */

      /* Request permissions */
      requestPermissions();
    }

    @TargetApi(Build.VERSION_CODES.M)
    private void requestPermissions()
    {
      /* Avoid crashing if not on Android M */
      if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.M)
      {
        /*
         * Request location permission, but this doesn't explain why it is needed to the user.
         * The standard Android documentation explains with more details how to display a rationale activity to explain the user why the permission is needed in your application.
         * Putting COARSE vs FINE has no impact here, they are part of the same group for runtime permission management.
         */
        if (checkSelfPermission(android.Manifest.permission.ACCESS_FINE_LOCATION) != PackageManager.PERMISSION_GRANTED)
          requestPermissions(new String[] { android.Manifest.permission.ACCESS_FINE_LOCATION }, 0);

      }
    }

    @Override
    public void onRequestPermissionsResult(int requestCode, String[] permissions, int[] grantResults)
    {
      /* Only a positive location permission update requires engagement agent refresh, hence the request code matching from above function */
      if (requestCode == 0 && grantResults[0] == PackageManager.PERMISSION_GRANTED)
        getEngagementAgent().refreshPermissions();
    }
