---
title: Azure Mobile Engagement Android SDK 통합
description: Azure Mobile Engagement용 Android SDK의 최신 업데이트 및 절차
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: a5487793-1a12-4f6c-a1cf-587c5a671e6b
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: Java
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: fffff6de996b8295639b3d595c5f778de8a0f74f
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2018
---
# <a name="how-to-integrate-engagement-on-android"></a>Android에서 Engagement를 통합하는 방법
> [!IMPORTANT]
> Azure Mobile Engagement는 2018/3/31에 사용이 중지됩니다. 이 페이지는 이후에 삭제됩니다.
> 

> [!div class="op_single_selector"]
> * [Windows 범용](mobile-engagement-windows-store-integrate-engagement.md)
> * [Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md)
> * [iOS](mobile-engagement-ios-integrate-engagement.md)
> * [Android](mobile-engagement-android-integrate-engagement.md)
> 
> 

이 절차는 Android 응용 프로그램에서 Engagement의 분석 및 모니터링 기능을 활성화하는 가장 간단한 방법을 설명합니다.

> [!IMPORTANT]
> Android SDK API의 최소 수준은 10 이상(Android 2.3.3 이상)이어야 합니다.
> 
> 

다음 단계를 통해 사용자, 세션, 작업, 크래시 및 기술과 관련된 모든 통계를 계산하는 데 필요한 로그의 보고서를 활성화할 수 있습니다. 이벤트, 오류, 작업 등의 기타 통계는 응용 프로그램별로 다르므로, 해당 통계를 계산하는 데 필요한 로그 보고는 Engagement API를 사용하여 수동으로 수행해야 합니다. 관련 설명은 [Android에서 고급 Mobile Engagement 태깅 API를 사용하는 방법](mobile-engagement-android-use-engagement-api.md)을 참조하세요.

## <a name="embed-the-engagement-sdk-and-service-into-your-android-project"></a>Android 프로젝트에 Engagement SDK 및 서비스 포함
[여기](https://aka.ms/vq9mfn)에서 Android SDK를 다운로드합니다. `mobile-engagement-VERSION.jar`을 가져와서 Android 프로젝트의 폴더에 넣습니다(`libs` 폴더가 아직 존재하지 않는 경우 생성).

> [!IMPORTANT]
> ProGuard로 응용 프로그램 패키지를 빌드하는 경우 일부 클래스를 유지해야 합니다. 다음 구성 코드 조각을 사용할 수 있습니다.
> 
> -keep public class * extends android.os.IInterface -keep class com.microsoft.azure.engagement.reach.activity.EngagementWebAnnouncementActivity$EngagementReachContentJS {
> 
> <methods>; }
> 
> 

시작 관리자 작업에서 다음 메서드를 호출하여 Engagement 연결 문자열을 지정합니다.

            EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
            engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
            EngagementAgent.getInstance(this).init(engagementConfiguration);

응용 프로그램의 연결 문자열은 Azure Portal에 표시됩니다.

* 없는 경우 다음 Android 권한을 `<application>` 태그 앞에 추가합니다.
  
          <uses-permission android:name="android.permission.INTERNET"/>
          <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE"/>
* `<application>` 태그와 `</application>` 태그 사이에 다음 섹션을 추가합니다.
  
          <service
            android:name="com.microsoft.azure.engagement.service.EngagementService"
            android:exported="false"
            android:label="<Your application name>Service"
            android:process=":Engagement"/>
* `<Your application name>`은(는) 응용 프로그램의 이름으로 바꿉니다.

> [!TIP]
> `android:label` 특성을 통해 휴대폰의 "서비스 실행 중" 화면에서 최종 사용자에게 표시될 참여 서비스의 이름을 선택할 수 있습니다. 이 특성을 `"<Your application name>Service"`(예: `"AcmeFunGameService"`)(으)로 설정하는 것이 좋습니다.
> 
> 

또한 `android:process` 특성을 지정하면 Engagement 서비스가 자체 프로세스에서 실행됩니다. 그리고 응용 프로그램과 동일한 프로세스에서 Engagement를 실행하면 주/UI 스레드의 응답성이 떨어질 수 있습니다.

> [!NOTE]
> `Application.onCreate()` 및 기타 응용 프로그램 콜백에 배치한 코드는 Engagement 서비스를 비롯하여 모든 응용 프로그램 프로세스에서 실행됩니다. 이로 인해 Engagement 프로세스, 중복 브로드캐스트 수신기 또는 서비스에서의 불필요한 메모리 할당 및 스레드와 같은 원치 않는 부작용이 발생할 수 있습니다.
> 
> 

`Application.onCreate()`을(를) 재정의하는 경우 `Application.onCreate()` 함수의 시작 부분에 다음 코드 조각을 추가하는 것이 좋습니다.

             public void onCreate()
             {
               if (EngagementAgentUtils.isInDedicatedEngagementProcess(this))
                 return;

               ... Your code...
             }

그리고 `Application.onTerminate()`, `Application.onLowMemory()` 및 `Application.onConfigurationChanged(...)`에 대해 동일한 작업을 수행할 수 있습니다.

또한 `Application`을(를) 확장하는 대신 `EngagementApplication`을(를) 확장할 수도 있습니다. 콜백 `Application.onCreate()`은(는) 프로세스 검사를 수행하고 현재 프로세스가 Engagement 서비스를 호스트하는 프로세스가 아닌 경우에만 `Application.onApplicationProcessCreate()`을(를) 호출합니다. 그리고 다른 콜백에 대해서도 동일한 규칙이 적용됩니다.

## <a name="basic-reporting"></a>기본 보고
### <a name="recommended-method-overload-your-activity-classes"></a>권장 방법: `Activity` 클래스 오버로드
Engagement에서 사용자, 세션, 작업, 충돌 및 기술 통계를 계산하는 데 필요한 모든 로그의 보고서를 활성화하려면 모든 `*Activity` 하위 클래스가 해당 `Engagement*Activity` 클래스에서 상속하도록 설정해야 합니다. 예를 들어 레거시 작업이 `ListActivity`을(를) 확장하는 경우 `EngagementListActivity`을(를) 확장하도록 합니다.

**Engagement 사용 안 함:**

            package com.company.myapp;

            import android.app.Activity;
            import android.os.Bundle;

            public class MyApp extends Activity
            {
              @Override
              public void onCreate(Bundle savedInstanceState)
              {
                super.onCreate(savedInstanceState);
                setContentView(R.layout.main);
              }
            }

**Engagement 사용:**

            package com.company.myapp;

            import com.microsoft.azure.engagement.activity.EngagementActivity;
            import android.os.Bundle;

            public class MyApp extends EngagementActivity
            {
              @Override
              public void onCreate(Bundle savedInstanceState)
              {
                super.onCreate(savedInstanceState);
                setContentView(R.layout.main);
              }
            }

> [!IMPORTANT]
> `EngagementListActivity` 또는 `EngagementExpandableListActivity`을(를) 사용할 경우 `super.onCreate(...);`을(를) 호출하기 전에 `requestWindowFeature(...);`을(를) 호출해야 합니다. 그렇지 않으면 충돌이 발생합니다.
> 
> 

이러한 클래스는 `src` 폴더에서 찾을 수 있으며 프로젝트에 복사할 수 있습니다. 또한 클래스는 **JavaDoc**에도 있습니다.

### <a name="alternate-method-call-startactivity-and-endactivity-manually"></a>대체 방법: `startActivity()` 및 `endActivity()` 수동 호출
`Activity` 클래스를 오버로드할 수 없거나 오버로드하지 않으려는 경우 `EngagementAgent`의 메서드를 직접 호출하여 작업을 시작하고 종료할 수 있습니다.

> [!IMPORTANT]
> Android SDK는 응용 프로그램이 닫힐 때에도 `endActivity()` 메서드를 호출하지 않습니다(Android에서 응용 프로그램은 실제로 닫히지 않음). 따라서 *모든* 작업의 `onResume` 콜백에서는 `startActivity()` 메서드를, *모든* 작업의 `onPause()` 콜백에서는 `endActivity()` 메서드를 호출하는 것이 *상당히* 좋습니다. 이것이 세션이 손실되지 않도록 하는 유일한 방법입니다. 세션이 손실되는 경우 Engagement 서비스의 Engagement 백 엔드에 대한 연결이 끊기지 않습니다(세션이 보류 중인 한 서비스가 연결된 상태를 유지하므로).
> 
> 

다음은 예제입니다.

            public class MyActivity extends Some3rdPartyActivity
            {
              @Override
              protected void onResume()
              {
                super.onResume();
                String activityNameOnEngagement = EngagementAgentUtils.buildEngagementActivityName(getClass()); // Uses short class name and removes "Activity" at the end.
                EngagementAgent.getInstance(this).startActivity(this, activityNameOnEngagement, null);
              }

              @Override
              protected void onPause()
              {
                super.onPause();
                EngagementAgent.getInstance(this).endActivity();
              }
            }

이 예제는 `EngagementActivity` 클래스 및 해당 변형과 매우 비슷합니다. 해당 소스 코드는 `src` 폴더에 제공되어 있습니다.

## <a name="test"></a>테스트
이제 에뮬레이터와 장치에서 모바일 앱을 실행하고 모니터 탭에서 세션을 등록하는지 확인하여 통합을 확인하세요.

다음 섹션은 선택 사항입니다.

## <a name="location-reporting"></a>위치 보고
위치가 보고되도록 하려는 경우 몇 줄의 구성을 `<application>` 태그와 `</application>` 태그 사이에 추가해야 합니다.

### <a name="lazy-area-location-reporting"></a>지연 영역 위치 보고
지연 영역 위치 보고를 통해 국가, 지역 및 장치와 연결된 위치를 보고할 수 있습니다. 이러한 유형의 위치 보고에서는 네트워크 위치(셀 ID 또는 WIFI 기반)만 사용합니다. 장치 영역은 세션당 한번 이하로 보고됩니다. GPS는 전혀 사용되지 않으므로 이러한 위치 보고는 배터리에 거의 영향을 미치지 않습니다.

보고된 영역은 사용자, 세션, 이벤트 및 오류에 대한 지리적 통계를 계산 하는 데 사용됩니다. 이 영역은 도달률 캠페인의 기준으로도 사용할 수 있습니다.

지연 영역 위치 보고를 사용하도록 설정하려면 이 절차의 앞에서 설명한 구성을 사용하여 수행할 수 있습니다.

    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
    engagementConfiguration.setLazyAreaLocationReport(true);
    EngagementAgent.getInstance(this).init(engagementConfiguration);

또한 아직 없는 경우 다음 권한도 추가해야 합니다.

            <uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION"/>

또는 응용 프로그램에서 이미 사용하고 있는 경우 ``ACCESS_FINE_LOCATION`` 을 계속 사용할 수 있습니다.

### <a name="real-time-location-reporting"></a>실시간 위치 보고
실시간 위치 보고를 통해 장치와 연결된 위도와 경도를 보고할 수 있습니다. 기본적으로 이 유형의 위치 보고에서는 네트워크 위치(셀 ID 또는 WIFI 기반)만 사용하고, 보고는 응용 프로그램이 포그라운드로 실행될 때(즉, 세션 중)만 활성화됩니다.

실시간 위치는 통계를 계산하는 데 사용되지 *않습니다* . 유일한 용도는 도달률 캠페인에서 실시간 지리적 펜스 \<Reach-Audience-geofencing\> 사용을 허용하는 것입니다.

실시간 위치 보고를 사용하도록 설정하려면 이 절차의 앞에서 설명한 구성을 사용하여 수행할 수 있습니다.

    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
    engagementConfiguration.setRealtimeLocationReport(true);
    EngagementAgent.getInstance(this).init(engagementConfiguration);

또한 아직 없는 경우 다음 권한도 추가해야 합니다.

            <uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION"/>

또는 응용 프로그램에서 이미 사용하고 있는 경우 ``ACCESS_FINE_LOCATION`` 을 계속 사용할 수 있습니다.

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
기본적으로 실시간 위치 보고는 응용 프로그램이 포그라운드로 실행되는 경우(즉, 세션 중)에만 활성 상태입니다. 백그라운드에서도 보고를 활성화하려면 구성 개체를 사용합니다.

    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
    engagementConfiguration.setRealtimeLocationReport(true);
    engagementConfiguration.setBackgroundRealtimeLocationReport(true);
    EngagementAgent.getInstance(this).init(engagementConfiguration);

> [!NOTE]
> 응용 프로그램이 백그라운드에서 실행될 때 GPS를 활성화한 경우에도 네트워크 기반 위치만 보고됩니다.
> 
> 

백그라운드 위치 보고는 사용자가 해당 장치를 재부팅하는 경우 중지됩니다. 다음을 추가하면 부팅 시 자동으로 다시 시작하도록 설정할 수 있습니다.

            <receiver android:name="com.microsoft.azure.engagement.EngagementLocationBootReceiver"
               android:exported="false">
               <intent-filter>
                  <action android:name="android.intent.action.BOOT_COMPLETED" />
               </intent-filter>
            </receiver>

또한 아직 없는 경우 다음 권한도 추가해야 합니다.

            <uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />

### <a name="android-m-permissions"></a>Android M 권한
Android M부터는 일부 권한이 런타임 시 관리되며 사용자 승인이 필요합니다.

Android API Level 23을 대상으로 하는 경우 새 앱 설치에 대해서는 기본적으로 런타임 권한이 해제됩니다. 그렇지 않으면 기본적으로 활성화됩니다.

사용자는 장치 설정 메뉴에서 이러한 권한을 설정/해제할 수 있습니다. 시스템 메뉴에서 권한을 해제하면 응용 프로그램의 백그라운드 프로세스가 중단되며 이것은 시스템 동작으로 백그라운드로 푸시 알림을 받는 기능에는 영향을 주지 않습니다.

Mobile Engagement의 컨텍스트에서 런타임 시 승인이 필요한 권한은 다음과 같습니다.

* `ACCESS_COARSE_LOCATION`
* `ACCESS_FINE_LOCATION`
* `WRITE_EXTERNAL_STORAGE` (이에 대한 Android API Level 23을 대상으로 할 때만 해당)

외부 저장소는 도달률 큰 그림 기능에만 사용됩니다. 사용자에게 이 권한을 요청하는 것이 번거롭다고 생각되는 경우, 큰 그림 기능을 사용하지 않는 비용으로 Mobile Engagement에 대해서만 사용하는 경우 권한을 제거할 수 있습니다.

위치 기능의 경우 표준 시스템 대화 상자를 사용하여 사용자에 대한 권한을 요청해야 합니다. 사용자가 승인하면 ``EngagementAgent``에 알려 실시간으로 변경 내용을 고려하도록 해야 합니다. 그렇지 않으면 사용자가 다음에 응용 프로그램을 시작할 때 변경 내용이 처리됩니다.

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
         * Request location permission, but this won't explain why it is needed to the user.
         * The standard Android documentation explains with more details how to display a rationale activity to explain the user why the permission is needed in your application.
         * Putting COARSE vs FINE has no impact here, they are part of the same group for runtime permission management.
         */
        if (checkSelfPermission(android.Manifest.permission.ACCESS_FINE_LOCATION) != PackageManager.PERMISSION_GRANTED)
          requestPermissions(new String[] { android.Manifest.permission.ACCESS_FINE_LOCATION }, 0);

        /* Only if you want to keep features using external storage */
        if (checkSelfPermission(android.Manifest.permission.WRITE_EXTERNAL_STORAGE) != PackageManager.PERMISSION_GRANTED)
          requestPermissions(new String[] { android.Manifest.permission.WRITE_EXTERNAL_STORAGE }, 1);
      }
    }

    @Override
    public void onRequestPermissionsResult(int requestCode, String[] permissions, int[] grantResults)
    {
      /* Only a positive location permission update requires engagement agent refresh, hence the request code matching from above function */
      if (requestCode == 0 && grantResults[0] == PackageManager.PERMISSION_GRANTED)
        getEngagementAgent().refreshPermissions();
    }

## <a name="advanced-reporting"></a>고급 보고
필요에 따라 응용 프로그램 관련 이벤트, 오류 및 작업을 보고하려는 경우 `EngagementAgent` 클래스의 메서드를 통해 Engagement API를 사용해야 합니다. `EngagementAgent.getInstance()` 정적 메서드를 호출하여 이 클래스의 개체를 검색할 수 있습니다.

Engagement API는 모든 Engagement의 고급 기능 사용을 허용하며 Android의 Engagement API 사용 방법(및 `EngagementAgent` 클래스의 기술 문서)에 자세히 설명되어 있습니다.

## <a name="advanced-configuration-in-androidmanifestxml"></a>고급 구성(AndroidManifest.xml의)
### <a name="wake-locks"></a>절전 모드 해제 잠금
Wifi를 사용하는 경우 또는 화면이 꺼져 있을 때 실시간으로 통계를 보내려면 다음과 같은 선택적 권한을 추가합니다.

            <uses-permission android:name="android.permission.WAKE_LOCK"/>

### <a name="crash-report"></a>충돌 보고서
크래시 보고를 비활성화하려는 경우 `<application>` 태그와 `</application>` 태그 사이에 다음을 추가합니다.

            <meta-data android:name="engagement:reportCrash" android:value="false"/>

### <a name="burst-threshold"></a>버스트 임계값
기본적으로 Engagement 서비스는 로그를 실시간으로 보고합니다. 응용 프로그램이 로그를 매우 자주 보고하는 경우 로그를 버퍼링한 후 정기적으로 한 번에 모두 보고하는 것이 좋습니다. 이를 "버스트 모드"라고 합니다. 그렇게 하려면 `<application>` 태그와 `</application>` 태그 사이에 다음을 추가합니다.

            <meta-data android:name="engagement:burstThreshold" android:value="{interval between too bursts (in milliseconds)}"/>

버스트 모드를 사용하는 경우 배터리 수명은 약간 길어지지만 Engagement 모니터에 영향을 주게 됩니다. 모든 세션 및 작업 기간이 버스트 임계값으로 반올림되므로 버스트 임계값보다 짧은 세션과 작업은 표시되지 않을 수도 있습니다. 30000(30초) 이하의 버스트 임계값을 사용하는 것이 좋습니다.

### <a name="session-timeout"></a>세션 시간 제한
기본적으로 세션은 마지막 작업(일반적으로 홈 또는 뒤로 키를 누르거나, 휴대폰을 유휴로 설정하거나, 다른 응용 프로그램으로 이동함으로써 발생)의 종료 10초 후에 종료됩니다. 그러면 사용자가 응용 프로그램을 종료하고 빠르게 응용 프로그램으로 돌아갈 때(이미지를 선택하거나 알림을 확인하는 등의 작업을 통해)마다 세션 분할을 피할 수 있습니다. 이 매개 변수를 수정할 수도 있습니다. 그렇게 하려면 `<application>` 태그와 `</application>` 태그 사이에 다음을 추가합니다.

            <meta-data android:name="engagement:sessionTimeout" android:value="{session timeout (in milliseconds)}"/>

## <a name="disable-log-reporting"></a>로그 보고 사용 안 함
### <a name="using-a-method-call"></a>메서드 호출 사용
Engagement에서 로그 전송을 중지하려면 다음을 호출할 수 있습니다.

            EngagementAgent.getInstance(context).setEnabled(false);

이 호출은 영구적이며, 공유 기본 설정 파일을 사용합니다.

이 함수를 호출할 때 Engagement가 활성 상태인 경우 서비스가 중지되는 데 1분이 걸릴 수 있습니다. 그러나 다음에 응용 프로그램을 시작할 때는 서비스가 시작되지 않습니다.

또한 `true`(으)로 동일한 함수를 호출하여 로그 보고를 다시 활성화할 수 있습니다.

### <a name="integration-in-your-own-preferenceactivity"></a>고유한 `PreferenceActivity`
이 함수를 호출하지 않고 기존 `PreferenceActivity`에서 직접 이 설정을 통합할 수 있습니다.

다음과 같이 `AndroidManifest.xml` 파일의 기본 설정 파일을(원하는 모드에서) `application meta-data`와(과) 함께 사용하도록 Engagement를 구성할 수 있습니다.

* `engagement:agent:settings:name` 키는 공유 기본 설정 파일의 이름을 정의하는 데 사용됩니다.
* `engagement:agent:settings:mode` 키는 공유 기본 설정 파일의 모드를 정의하는 데 사용되며, `PreferenceActivity`에서와 동일한 모드를 사용해야 합니다. 모드는 숫자로 전달해야 합니다. 코드에서 상수 플래그의 조합을 사용하는 경우 전체 값을 확인합니다.

Engagement는 이 설정을 관리하기 위한 기본 설정 파일 내에서 항상 `engagement:key` 부울 키를 사용합니다.

다음 `AndroidManifest.xml` 예제는 기본값을 보여 줍니다.

            <application>
                [...]
                <meta-data
                  android:name="engagement:agent:settings:name"
                  android:value="engagement.agent" />
                <meta-data
                  android:name="engagement:agent:settings:mode"
                  android:value="0" />

이제 다음과 같은 기본 설정 레이아웃에서 `CheckBoxPreference` 을(를) 추가할 수 있습니다.

            <CheckBoxPreference
              android:key="engagement:enabled"
              android:defaultValue="true"
              android:title="Use Engagement"
              android:summaryOn="Engagement is enabled."
              android:summaryOff="Engagement is disabled." />

<!-- URLs. -->
[Device API]: http://go.microsoft.com/?linkid=9876094
