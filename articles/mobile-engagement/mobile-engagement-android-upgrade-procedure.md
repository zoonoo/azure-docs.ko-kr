---
title: Azure Mobile Engagement Android SDK 통합
description: Azure Mobile Engagement용 Android SDK의 최신 업데이트 및 절차
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 11618586-c709-49ca-bcd8-745323ff1af6
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: Java
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 77047cb1dc39fa3c05f58550ceea74e78396157f
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2018
---
# <a name="upgrade-procedures"></a>업그레이드 절차
> [!IMPORTANT]
> Azure Mobile Engagement는 2018/3/31에 사용이 중지됩니다. 이 페이지는 이후에 삭제됩니다.
> 

이전 버전의 SDK를 응용 프로그램에 이미 통합한 경우에는 SDK를 업그레이드할 때 다음 사항을 고려해야 합니다.

여러 SDK 버전을 건너뛴 경우에는 여러 절차를 수행해야 할 수 있습니다. 예를 들어 1.4.0에서 1.6.0으로 마이그레이션하는 경우 먼저 "1.4.0에서 1.5.0으로" 절차를 따른 다음 "1.5.0에서 1.6.0으로" 절차를 따라야 합니다.

업그레이드를 수행하려는 소스 버전이 무엇이든, `mobile-engagement-VERSION.jar` 을 새로운 파일로 바꾸세요.

## <a name="from-420-to-421"></a>4.2.0 ~ 4.2.1
이 단계는 실제로 모든 버전의 SDK에서 실행할 수 있으며 도달률 활동을 통합할 때 보안을 강화합니다.

이제 모든 도달률 활동에 `exported="false"` 을(를) 추가해야 합니다.

도달률 활동은 이제 사용자의 `AndroidManifest.xml`에서 다음과 같이 나타납니다.

            <activity android:name="com.microsoft.azure.engagement.reach.activity.EngagementTextAnnouncementActivity" android:theme="@android:style/Theme.Light" android:exported="false">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT"/>
                <category android:name="android.intent.category.DEFAULT" />
                <data android:mimeType="text/plain" />
              </intent-filter>
            </activity>
            <activity android:name="com.microsoft.azure.engagement.reach.activity.EngagementWebAnnouncementActivity" android:theme="@android:style/Theme.Light" android:exported="false">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT"/>
                <category android:name="android.intent.category.DEFAULT" />
                <data android:mimeType="text/html" />
              </intent-filter>
            </activity>
            <activity android:name="com.microsoft.azure.engagement.reach.activity.EngagementPollActivity" android:theme="@android:style/Theme.Light" android:exported="false">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.POLL"/>
                <category android:name="android.intent.category.DEFAULT" />
              </intent-filter>
            </activity>
            <activity android:name="com.microsoft.azure.engagement.reach.activity.EngagementLoadingActivity" android:theme="@android:style/Theme.Dialog" android:exported="false">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.LOADING"/>
                <category android:name="android.intent.category.DEFAULT"/>
              </intent-filter>
            </activity>

## <a name="from-400-to-410"></a>4.0.0에서 4.1.0으로
이제 SDK가 Android M에서 새 권한 모델을 다룹니다.

위치 기능 또는 큰 그림 알림을 사용하는 경우 [이 섹션](mobile-engagement-android-integrate-engagement.md#android-m-permissions)을 읽으세요.

새 권한 모델 외에도 이제 런타임 시 위치 기능 구성을 지원합니다.
위치에 대한 매니페스트 매개 변수와 계속 호환 가능하지만 더 이상 사용할 수 없습니다. 런타임 구성을 사용하려면 ``AndroidManifest.xml``에서 다음 섹션을 제거합니다.

    <meta-data
      android:name="engagement:locationReport:lazyArea"
      android:value="true"/>
    <meta-data
      android:name="engagement:locationReport:realTime"
      android:value="true"/>
    <meta-data
      android:name="engagement:locationReport:realTime:background"
      android:value="true"/>
    <meta-data
      android:name="engagement:locationReport:realTime:fine"
      android:value="true"/>

대신 런타임 구성을 사용하려면 [이 업데이트된 절차](mobile-engagement-android-integrate-engagement.md#location-reporting) 를 확인합니다.

## <a name="from-300-to-400"></a>3.0.0에서 4.0.0으로
### <a name="native-push"></a>네이티브 푸시
네이티브 푸시(GCM/ADM)가 이제 앱 알림에도 사용되므로 모든 푸시 캠페인 유형에 대한 네이티브 푸시 자격 증명을 구성해야 합니다.

아직 하지 않았다면 [이 절차](mobile-engagement-android-integrate-engagement-reach.md#native-push)를 따르세요.

### <a name="androidmanifestxml"></a>AndroidManifest.xml
도달률 통합이 ``AndroidManifest.xml``에서 수정되었습니다.

다음을 바꿉니다.

    <receiver
      android:name="com.microsoft.azure.engagement.reach.EngagementReachReceiver"
      android:exported="false">
      <intent-filter>
        <action android:name="android.intent.action.BOOT_COMPLETED"/>
        <action android:name="com.microsoft.azure.engagement.intent.action.AGENT_CREATED"/>
        <action android:name="com.microsoft.azure.engagement.intent.action.MESSAGE"/>
        <action android:name="com.microsoft.azure.engagement.reach.intent.action.ACTION_NOTIFICATION"/>
        <action android:name="com.microsoft.azure.engagement.reach.intent.action.EXIT_NOTIFICATION"/>
        <action android:name="android.intent.action.DOWNLOAD_COMPLETE"/>
        <action android:name="com.microsoft.azure.engagement.reach.intent.action.DOWNLOAD_TIMEOUT"/>
      </intent-filter>
    </receiver>

기준

    <receiver
      android:name="com.microsoft.azure.engagement.reach.EngagementReachReceiver"
      android:exported="false">
      <intent-filter>
        <action android:name="android.intent.action.BOOT_COMPLETED"/>
        <action android:name="com.microsoft.azure.engagement.intent.action.AGENT_CREATED"/>
        <action android:name="com.microsoft.azure.engagement.intent.action.MESSAGE"/>
        <action android:name="com.microsoft.azure.engagement.reach.intent.action.ACTION_NOTIFICATION"/>
        <action android:name="com.microsoft.azure.engagement.reach.intent.action.EXIT_NOTIFICATION"/>
        <action android:name="com.microsoft.azure.engagement.reach.intent.action.DOWNLOAD_TIMEOUT"/>
      </intent-filter>
    </receiver>
    <receiver android:name="com.microsoft.azure.engagement.reach.EngagementReachDownloadReceiver">
      <intent-filter>
        <action android:name="android.intent.action.DOWNLOAD_COMPLETE"/>
      </intent-filter>
    </receiver>

이제 공지(텍스트/웹 콘텐츠 포함) 또는 설문 조사를 클릭할 때 로딩 화면이 있을 수 있습니다.
해당 캠페인이 4.0.0에서 작동하려면 이를 추가해야 합니다.

    <activity
      android:name="com.microsoft.azure.engagement.reach.activity.EngagementLoadingActivity"
      android:theme="@android:style/Theme.Dialog">
      <intent-filter>
        <action android:name="com.microsoft.azure.engagement.reach.intent.action.LOADING"/>
        <category android:name="android.intent.category.DEFAULT"/>
      </intent-filter>
    </activity>

### <a name="resources"></a>리소스
새 `res/layout/engagement_loading.xml` 파일을 프로젝트에 포함합니다.

## <a name="from-240-to-300"></a>2.4.0에서 3.0.0으로 마이그레이션
아래에서는 SDK 통합을 Capptain SAS 제공 Capptain 서비스에서 Azure Mobile Engagement 구동 앱으로 마이그레이션하는 방법을 설명합니다. 이전 버전에서 마이그레이션하는 경우 Capptain 웹 사이트를 참조하여 먼저 2.4.0으로 마이그레이션한 후 다음 절차를 적용하세요.

> [!IMPORTANT]
> Capptain과 Mobile Engagement는 같은 서비스가 아니며, 아래에서 제공하는 절차에서는 클라이언트 앱을 마이그레이션하는 방법만 중점적으로 설명합니다. 앱에서 SDK를 마이그레이션해도 데이터가 Capptain 서버에서 Mobile Engagement 서버로 마이그레이션되지는 않습니다.
> 
> 

### <a name="jar-file"></a>JAR 파일
`libs` 폴더에서 `capptain.jar`을(를) `mobile-engagement-VERSION.jar`로 바꿉니다.

### <a name="resource-files"></a>리소스 파일
제공하는 모든 리소스 파일(`capptain_`이(가) 접두사로 지정됨)을 새 파일(`engagement_`이(가) 접두사로 지정됨)로 바꿔야 합니다.

해당 파일을 사용자 지정한 경우 새 파일에 대해 모든 사용자 지정 내용을 다시 적용해야 하며, **리소스 파일의 모든 식별자 이름이 바뀝니다**.

### <a name="application-id"></a>응용 프로그램 UI
이제 Engagement에서 연결 문자열을 사용하여 응용 프로그램 식별자와 같은 SDK 식별자를 구성합니다.

다음과 같은 시작 관리자 작업에서 `EngagementAgent.init` 메서드를 사용해야 합니다.

            EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
            engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
            EngagementAgent.getInstance(this).init(engagementConfiguration);

응용 프로그램의 연결 문자열은 Azure Portal에 표시됩니다.

`EngagementAgent.init`이(가) 해당 메서드를 대체하므로, `CapptainAgent.configure`에 대한 모든 호출을 제거하세요.

`appId`은(는) 더 이상 `AndroidManifest.xml`을(를) 사용하여 구성할 수 없습니다.

다음 섹션이 있는 경우 `AndroidManifest.xml`에서 이 섹션을 제거하세요.

            <meta-data android:name="capptain:appId" android:value="<YOUR_APPID>"/>

### <a name="java-api"></a>Java API
SDK의 모든 Java 클래스를 호출할 때마다 이름을 바꿔야 합니다. 예를 들어 `CapptainAgent.getInstance(this)`의 이름은 `EngagementAgent.getInstance(this)`(으)로 바꿔야 하고, `extends CapptainActivity`의 이름은 `extends EngagementActivity`(으)로 바꿔야 하는 식입니다.

기본 에이전트 기본 설정 파일과 통합된 경우 기본 파일 이름은 이제 `engagement.agent`이고 키는 `engagement:agent`입니다.

웹 공지를 만들 때 Javascript 바인더는 이제 `engagementReachContent`입니다.

### <a name="androidmanifestxml"></a>AndroidManifest.xml
많은 변경이 발생했으며, 서비스가 더 이상 공유되지 않고 많은 수신기를 더 이상 탐색할 수 없습니다.

서비스 선언은 더욱 간단합니다. 의도 필터 및 그 안의 모든 메타데이터를 제거한 다음 `exportable=false`을(를) 추가합니다.

또한 모든 항목이 Engagement를 사용하도록 이름이 바뀝니다.

이제 다음과 같습니다.

            <service
              android:name="com.microsoft.azure.engagement.service.EngagementService"
              android:exported="false"
              android:label="<Your application name>Service"
              android:process=":Engagement"/>

테스트 로그를 활성화하려는 경우 이제 meta-data가 응용 프로그램 태그로 이동되었고 다음과 같이 이름이 바뀌었습니다.

            <application>

              <meta-data android:name="engagement:log:test" android:value="true" />

              <service/>

            </application>

모든 다른 meta-data 이름이 바뀌었으며 전체 목록은 다음과 같습니다(이름 바꾸기에서는 사용자가 사용하는 이름만 사용함).

            <meta-data
              android:name="engagement:reportCrash"
              android:value="true"/>
            <meta-data
              android:name="engagement:sessionTimeout"
              android:value="10000"/>
            <meta-data
              android:name="engagement:burstThreshold"
              android:value="0"/>
            <meta-data
              android:name="engagement:connection:delay"
              android:value="0"/>
            <meta-data
              android:name="engagement:locationReport:lazyArea"
              android:value="false"/>
            <meta-data
              android:name="engagement:locationReport:realTime"
              android:value="false"/>
            <meta-data
              android:name="engagement:locationReport:realTime:background"
              android:value="false"/>
            <meta-data
              android:name="engagement:locationReport:realTime:fine"
              android:value="false"/>
            <meta-data
              android:name="engagement:agent:settings:name"
              android:value="engagement.agent"/>
            <meta-data
              android:name="engagement:agent:settings:mode"
              android:value="0"/>
            <meta-data
              android:name="engagement:gcm:sender"
              android:value="<YOUR_PROJECT_NUMBER>\n"/>
            <meta-data
              android:name="engagement:adm:register"
              android:value="true"/>
            <meta-data
              android:name="engagement:reach:notification:icon"
              android:value="<DRAWABLE_NAME_WITHOUT_EXTENSION>"/>

            <activity android:name="SomeActivityWithoutReachOverlay">
              <meta-data
                android:name="engagement:notification:overlay"
                android:value="false"/>
            </activity>

Google Play 및 SmartAd 추적이 SDK에서 제거되었습니다. 대체하지 않고 이 부분을 제거하기만 하면 됩니다.

            <meta-data 
                android:name="capptain:track:installReferrerForwardList"
                android:value="com.class1,com.class2"/>
            <meta-data
                android:name="capptain:track:adservers"
                android:value="smartad" />

도달률 활동은 이제 다음과 같이 선언됩니다.

            <activity
              android:name="com.microsoft.azure.engagement.reach.activity.EngagementTextAnnouncementActivity"
              android:theme="@android:style/Theme.Light">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT"/>
                <category android:name="android.intent.category.DEFAULT"/>
                <data android:mimeType="text/plain"/>
              </intent-filter>
            </activity>
            <activity
              android:name="com.microsoft.azure.engagement.reach.activity.EngagementWebAnnouncementActivity"
              android:theme="@android:style/Theme.Light">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT"/>
                <category android:name="android.intent.category.DEFAULT"/>
                <data android:mimeType="text/html"/>
              </intent-filter>
            </activity>
            <activity
              android:name="com.microsoft.azure.engagement.reach.activity.EngagementPollActivity"
              android:theme="@android:style/Theme.Light">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.POLL"/>
                <category android:name="android.intent.category.DEFAULT"/>
              </intent-filter>
            </activity>

사용자 지정 도달률 활동이 있는 경우 `com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT` 또는 `com.microsoft.azure.engagement.reach.intent.action.POLL`와(과) 일치하도록 의도 작업을 변경하기만 하면 됩니다.

브로드캐스트 수신기 이름이 바뀌었으며, 이제 `exported=false`을(를) 추가합니다. 새 사양이 포함된 수신기의 전체 목록은 다음과 같습니다(이름 바꾸기에서는 사용자가 사용하는 이름만 사용함).

            <receiver android:name="com.microsoft.azure.engagement.reach.EngagementReachReceiver"
              android:exported="false">
              <intent-filter>
                <action android:name="android.intent.action.BOOT_COMPLETED"/>
                <action android:name="com.microsoft.azure.engagement.intent.action.AGENT_CREATED"/>
                <action android:name="com.microsoft.azure.engagement.intent.action.MESSAGE"/>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.ACTION_NOTIFICATION"/>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.EXIT_NOTIFICATION"/>
                <action android:name="android.intent.action.DOWNLOAD_COMPLETE"/>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.DOWNLOAD_TIMEOUT"/>
              </intent-filter>
            </receiver>

            <receiver android:name="com.microsoft.azure.engagement.gcm.EngagementGCMEnabler"
              android:exported="false">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.intent.action.APPID_GOT" />
              </intent-filter>
            </receiver>

            <receiver
              android:name="com.microsoft.azure.engagement.gcm.EngagementGCMReceiver"
              android:permission="com.google.android.c2dm.permission.SEND">
              <intent-filter>
                <action android:name="com.google.android.c2dm.intent.REGISTRATION"/>
                <action android:name="com.google.android.c2dm.intent.RECEIVE"/>
                <category android:name="<your_package_name>"/>
              </intent-filter>
            </receiver>

            <receiver android:name="com.microsoft.azure.engagement.adm.EngagementADMEnabler"
              android:exported="false">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.intent.action.APPID_GOT"/>
              </intent-filter>
            </receiver>

            <receiver
              android:name="com.microsoft.azure.engagement.adm.EngagementADMReceiver"
              android:permission="com.amazon.device.messaging.permission.SEND">
              <intent-filter>
                <action android:name="com.amazon.device.messaging.intent.REGISTRATION"/>
                <action android:name="com.amazon.device.messaging.intent.RECEIVE"/>
                <category android:name="<your_package_name>"/>
              </intent-filter>
            </receiver>

            <receiver android:name="<your_sub_class_of_com.microsoft.azure.engagement.reach.EngagementReachDataPushReceiver>"
              android:exported="false">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.DATA_PUSH" />
              </intent-filter>
            </receiver>

            <receiver android:name="com.microsoft.azure.engagement.EngagementLocationBootReceiver"
               android:exported="false">
               <intent-filter>
                  <action android:name="android.intent.action.BOOT_COMPLETED" />
               </intent-filter>
            </receiver>

            <receiver android:name="<your_sub_class_of_com.microsoft.azure.engagement.EngagementConnectionReceiver.java>"
              android:exported="false">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.intent.action.CONNECTED"/>
                <action android:name="com.microsoft.azure.engagement.intent.action.DISCONNECTED"/>
              </intent-filter>
            </receiver>

            <receiver
              android:name="<your_sub_class_of_com.microsoft.azure.engagement.EngagementMessageReceiver.java>"
              android:exported="false">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.MESSAGE"/>
              </intent-filter>
            </receiver>

추적 수신기가 제거되었으므로, 이 섹션을 제거해야 합니다.

          <receiver android:name="com.ubikod.capptain.android.sdk.track.CapptainTrackReceiver">
            <intent-filter>
              <action android:name="com.ubikod.capptain.intent.action.APPID_GOT" />
              <!-- possibly <action android:name="com.android.vending.INSTALL_REFERRER" /> -->
            </intent-filter>
          </receiver>

브로드캐스트 수신기의 구현 선언 **EngagementMessageReceiver**가 `AndroidManifest.xml`에서 변경되었습니다. 즉, XMPP 엔터티 및 API에서 임의의 XMPP 보내고 제거하는 API와 장치 간의 메시지를 주고받는 API가 제거되었기 때문입니다. 따라서 **EngagementMessageReceiver** 구현에서 다음 콜백을 삭제해야 합니다.

            protected void onDeviceMessageReceived(android.content.Context context, java.lang.String deviceId, java.lang.String payload)

and

            protected void onXMPPMessageReceived(android.content.Context context, android.os.Bundle message)

그런 다음 아래의 **EngagementAgent** 에 대한 모든 호출을 삭제합니다.

            sendMessageToDevice(java.lang.String deviceId, java.lang.String payload, java.lang.String packageName)

and

            sendXMPPMessage(android.os.Bundle msg)

### <a name="proguard"></a>Proguard
Proguard 구성은 브랜드 재지정의 영향을 받을 수 있으며, 규칙은 이제 다음과 같습니다.

            -dontwarn android.**
            -keep class android.support.v4.** { *; }

            -keep public class * extends android.os.IInterface
            -keep class com.microsoft.azure.engagement.reach.activity.EngagementWebAnnouncementActivity$EngagementReachContentJS {
              <methods>;
            }

