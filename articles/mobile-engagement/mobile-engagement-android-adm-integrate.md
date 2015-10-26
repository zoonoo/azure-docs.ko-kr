<properties 
	pageTitle="Azure Mobile Engagement Android SDK 통합" 
	description="Azure Mobile Engagement용 Android SDK의 최신 업데이트 및 절차"
	services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="piyushjo" 
	manager="dwrede" 
	editor="" />

<tags 
	ms.service="mobile-engagement" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-android" 
	ms.devlang="Java" 
	ms.topic="article" 
	ms.date="08/10/2015" 
	ms.author="piyushjo" />


#ADM와 Engagement를 통합하는 방법

> [AZURE.IMPORTANT]이 가이드를 수행하기 전에 Android 문서의 Engagement를 통합하는 방법에 설명된 통합 절차를 따라야 합니다.
>
> 이 문서는 상시 캠페인을 지원하기 위해 도달률 모듈을 통합하는 경우에만 유용합니다. 응용 프로그램에서 도달률 캠페인을 통합하려면 먼저 Android에서 Engagement 도달률을 통합하는 방법을 읽어보세요.

##소개

ADM을 통합하면 응용 프로그램이 실행되지 않을 때에도 응용 프로그램을 푸시할 수 있습니다.

캠페인 데이터는 ADM을 통해 실제로 전송되지 않으며, 단지 응용 프로그램에 Engagement 푸시를 가져오도록 지시하는 백그라운드 신호입니다. ADM 푸시를 수신하는 동안 응용 프로그램이 실행되지 않으면 Engagement 서버에 대한 연결이 트리거되어 푸시를 가져오며 사용자가 푸시에 응답하여 응용 프로그램을 시작할 경우를 위해 Engagement 연결은 1분 정도 활성 상태로 남아 있습니다.

> [AZURE.IMPORTANT]Android 4.0.3 이상을 실행하는 Amazon Kindle 장치만 Amazon 장치 메시징을 통해 지원되지만 다른 장치에서 안전하게 이 코드를 통합할 수 있습니다. ADM을 통해 응용 프로그램의 절전 모드가 해제되면 다음에 응용 프로그램이 시작될 때 Engagement 알림이 수신됩니다.

##ADM에 등록

아직 등록하지 않은 경우 Amazon 계정에서 ADM를 활성화해야 합니다.

절차는 [<https://developer.amazon.com/sdk/adm/credentials.html>]에 자세히 설명되어 있습니다.

절차를 완료하면 다음을 얻게 됩니다.

-   Engagement에서 장치를 푸시하는 데 사용할 수 있는 OAuth 자격 증명(클라이언트 ID 및 클라이언트 암호)
-   응용 프로그램에 통합해야 하는 API 키

##SDK 통합

### 장치 등록 관리

각 장치에서 ADM 서버로 등록 명령을 보내야 하며, 그렇지 않은 경우 서버에 연결할 수 없습니다.

이미 [ADM 클라이언트 라이브러리]를 사용하며 이미 [ADM을 통합]한 경우 android-sdk-adm-receive로 직접 이동할 수 있습니다.

ADM을 아직 통합하지 않은 경우 Engagement를 통해 응용 프로그램에서 더 간단히 활성화할 수 있습니다.

`AndroidManifest.xml` 파일을 다음과 같이 편집합니다.

-   Amazon 네임스페이스를 추가하면 파일 시작 부분은 다음과 같습니다.

		<?xml version="1.0" encoding="utf-8"?>
		<manifest xmlns:android="http://schemas.android.com/apk/res/android"
		          xmlns:amazon="http://schemas.amazon.com/apk/res/android"

-   `<application/>` 태그 내에 다음 섹션을 추가합니다.

		<amazon:enable-feature
		   android:name="com.amazon.device.messaging"
		   android:required="false"/>
		
		<meta-data android:name="engagement:adm:register" android:value="true" />

-   Amazon 태그를 추가한 후 프로젝트 빌드 대상이 Android 2.1 이하인 경우 빌드 오류가 발생할 수 있습니다. **Android 2.1 이상**의 빌드 대상을 사용해야 합니다(여전히 `minSdkVersion`을(를) 4로 설정할 수 있음).
-   [이 절차]를 따라 ADM API 키를 자산으로 통합합니다.

그런 후 다음 섹션의 지침을 따릅니다.

### Engagement 푸시 서비스에 등록 ID를 전달하고 알림을 수신합니다.

Engagement 푸시 서비스에 장치의 등록 ID를 전달하고 해당 알림을 수신하려면 `AndroidManifest.xml` 파일의 `<application/>` 태그 내에 다음을 추가합니다(Engagement 없이 ADM을 사용하는 경우에도).

		<receiver android:name="com.microsoft.azure.engagement.adm.EngagementADMEnabler"
		  android:exported="false">
		  <intent-filter>
		    <action android:name="com.microsoft.azure.engagement.intent.action.APPID_GOT"/>
		  </intent-filter>
		</receiver>
		
		 <receiver android:name="com.microsoft.azure.engagement.adm.EngagementADMReceiver"
		   android:permission="com.amazon.device.messaging.permission.SEND">
		  <intent-filter>
		    <action android:name="com.amazon.device.messaging.intent.REGISTRATION"/>
		    <action android:name="com.amazon.device.messaging.intent.RECEIVE"/>
		    <category android:name="<your_package_name>"/>
		  </intent-filter>
		</receiver>   

`AndroidManifest.xml`에서(`</application>` 태그 앞에) 다음 권한이 있는지 확인합니다.

		<uses-permission android:name="android.permission.WAKE_LOCK"/>
		<uses-permission android:name="com.amazon.device.messaging.permission.RECEIVE"/>
		<uses-permission android:name="<your_package_name>.permission.RECEIVE_ADM_MESSAGE"/>
		<permission android:name="<your_package_name>.permission.RECEIVE_ADM_MESSAGE" android:protectionLevel="signature"/>

##Engagement OAuth 자격 증명 부여

$/#application/YOUR\_APPID/native-push에서 OAuth 자격 증명(클라이언트 ID 및 클라이언트 암호)을 제출합니다.

이제 도달률 알림 및 설문 조사를 만들 때 "Any Time"을 선택할 수 있습니다.


[<https://developer.amazon.com/sdk/adm/credentials.html>]: https://developer.amazon.com/sdk/adm/credentials.html
[ADM 클라이언트 라이브러리]: https://developer.amazon.com/sdk/adm/setup.html
[ADM을 통합]: https://developer.amazon.com/sdk/adm/integrating-app.html
[이 절차]: https://developer.amazon.com/sdk/adm/integrating-app.html#Asset
 

<!---HONumber=Oct15_HO3-->