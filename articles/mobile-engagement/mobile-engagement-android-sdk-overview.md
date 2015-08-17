<properties 
	pageTitle="Azure Mobile Engagement Android SDK 통합" 
	description="Azure Mobile Engagement용 Android SDK의 최신 업데이트 및 절차"
	services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="kpiteira" 
	manager="dwrede" 
	editor="" />

<tags 
	ms.service="mobile-engagement" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-android" 
	ms.devlang="Java" 
	ms.topic="article" 
	ms.date="05/04/2015" 
	ms.author="kapiteir" />


#Azure Mobile Engagement용 Android SDK

Android 앱에서 Azure Mobile Engagement를 통합하는 방법에 대한 모든 자세한 내용을 보려면 여기에서 시작하세요. 먼저 통합을 연습해 보려면 [15분 자습서](mobile-engagement-android-get-started.md)의 단계를 진행하세요

[SDK 콘텐츠](mobile-engagement-android-sdk-content.md)를 보려면 클릭하세요.

##통합 절차
1. 시작: [Android 앱에서 Mobile Engagement를 통합하는 방법](mobile-engagement-android-integrate-engagement.md)

2. 알림: [Android 앱에서 도달률(알림)을 통합하는 방법](mobile-engagement-android-integrate-engagement-reach.md)
	1. Google Cloud Messaging (GCM): [GCM과 Mobile Engagement를 통합하는 방법](mobile-engagement-android-gcm-integrate.md)
	2. Amazon Device Messaging (ADM): [ADM과 Mobile Engagement를 통합하는 방법](mobile-engagement-android-adm-integrate.md)

3. 태그 계획 구현: [Android 앱에서 고급 Mobile Engagement API 태깅을 사용하는 방법](mobile-engagement-android-use-engagement-api.md)


##릴리스 정보

###4\.0.0(07/06/2015)

-   내부 프로토콜이 분석을 수행하고 더 안정적으로 푸시하도록 변경됩니다.
-   네이티브 푸시(GCM/ADM)가 이제 앱 알림에도 사용되므로 모든 푸시 캠페인 유형에 대한 네이티브 푸시 자격 증명을 구성해야 합니다.
-   큰 그림 알림을 수정합니다. 푸시된 후 10초만 표시되었습니다.
-   기본 작업 URL이 있는 웹 공지 내에서 링크를 클릭하는 동작을 수정합니다.
-   로컬 저장소 관리와 관련된 드문 충돌을 수정합니다.
-   동적 구성 문자열 관리를 수정합니다.
-   EULA를 업데이트합니다.

이전 버전에 대한 내용은 [전체 릴리스 정보](mobile-engagement-android-release-notes.md)를 참조하세요.

##업그레이드 절차

이전 버전의 SDK를 응용 프로그램에 이미 통합한 경우에는 SDK를 업그레이드할 때 다음 사항을 고려해야 합니다.

여러 SDK 버전을 건너뛴 경우에는 여러 절차를 수행해야 할 수 있습니다. 관련 정보는 전체 [업그레이드 절차](mobile-engagement-android-upgrade-procedure.md)를 참조하세요 예를 들어 1.4.0에서 1.6.0으로 마이그레이션하는 경우 먼저 "1.4.0에서 1.5.0으로" 절차를 따른 다음 "1.5.0에서 1.6.0으로" 절차를 따라야 합니다.

업그레이드를 수행하려는 소스 버전이 무엇이든, `mobile-engagement-VERSION.jar`을 새로운 파일로 바꾸세요.

###3\.0.0에서 4.0.0으로

#### 네이티브 푸시

네이티브 푸시(GCM/ADM)가 이제 앱 알림에도 사용되므로 모든 푸시 캠페인 유형에 대한 네이티브 푸시 자격 증명을 구성해야 합니다.

아직 하지 않았다면 [이 절차](mobile-engagement-android-integrate-engagement-reach.md#native-push)를 따르세요.

#### AndroidManifest.xml

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

이제 공지(텍스트/웹 콘텐츠 포함) 또는 설문 조사를 클릭할 때 로딩 화면이 있을 수 있습니다. 해당 캠페인이 4.0.0에서 작동하려면 이를 추가해야 합니다.

    <activity
      android:name="com.microsoft.azure.engagement.reach.activity.EngagementLoadingActivity"
      android:theme="@android:style/Theme.Dialog">
      <intent-filter>
        <action android:name="com.microsoft.azure.engagement.reach.intent.action.LOADING"/>
        <category android:name="android.intent.category.DEFAULT"/>
      </intent-filter>
    </activity>

#### 리소스

새 `res/layout/engagement_loading.xml` 파일을 프로젝트에 포함합니다.

<!---HONumber=August15_HO6-->