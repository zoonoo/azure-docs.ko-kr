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
	ms.date="02/12/2015" 
	ms.author="kapiteir" />

#GCM과 Mobile Engagement를 통합하는 방법

> [AZURE.IMPORTANT]이 가이드를 수행하기 전에 Android 문서의 Engagement를 통합하는 방법에 설명된 통합 절차를 따라야 합니다.
>
> 이 문서는 상시 캠페인을 지원하기 위해 도달률 모듈을 통합하는 경우에만 유용합니다. 응용 프로그램에서 도달률 캠페인을 통합하려면 먼저 Android에서 Engagement 도달률을 통합하는 방법을 읽어보세요.

##소개

GCM을 통합하면 응용 프로그램이 실행되지 않을 때에도 응용 프로그램을 푸시할 수 있습니다.

캠페인 데이터는 GCM을 통해 실제로 전송되지 않으며, 단지 응용 프로그램에 Engagement 푸시를 가져오도록 지시하는 백그라운드 신호입니다. GCM 푸시를 수신하는 동안 응용 프로그램이 실행되지 않으면 Engagement 서버에 대한 연결이 트리거되어 푸시를 가져오며 사용자가 푸시에 응답하여 응용 프로그램을 시작할 경우를 위해 Engagement 연결은 1분 정도 활성 상태로 남아 있습니다.

참고로, Engagement에서는 `engagement.tickle` 축소 키와 함께 [Send-to-Sync] 메시지만 사용합니다.

> [AZURE.IMPORTANT]Android 2.2 이상을 실행하고, Google Play를 설치하고, Google 백그라운드 연결이 활성화된 장치만 GCM을 통해 응용 프로그램의 절전 모드가 해제될 수 있지만 Android SDK의 이전 버전 및 GCM을 지원할 수 없는 장치(단지 의도만 이용하는 장치)에서 이 코드를 안전하게 통합할 수 있습니다. GCM을 통해 응용 프로그램의 절전 모드가 해제되면 다음에 응용 프로그램이 시작될 때 Engagement 알림이 수신됩니다.


> [AZURE.WARNING]Engagement SDK가 GCM을 사용하도록 구성되어 있고 고유한 클라이언트 코드에서 C2DM 등록 식별자를 관리하는데 등록 식별자에 대한 충돌이 발생하는 경우 고유한 코드에서 C2DM을 사용하지 않는 경우에만 Engagement에서 GCM을 사용하세요.

##GCM에 등록 및 GCM 서비스 사용

이 작업을 아직 수행하지 않은 경우 Google 계정에서 GCM 서비스를 활성화해야 합니다.

이 문서를 작성하는 시점을 기준으로(2014년 2월 5일) 아래의 절차를 따르면 됩니다. [<http://developer.android.com/guide/google/gcm/gs.html>]

해당 절차를 따르면 계정에서 GCM을 활성화할 수 있습니다. **Obtaining an API Key** 섹션에 도달하면 해당 내용을 읽지 않고 Google 절차를 더 이상 진행하지 않은 상태에서 이 페이지로 돌아옵니다.

이 절차에서는 **프로젝트 번호**가 **GCM 보낸 사람 ID**(이 절차의 뒷부분에서 필요)로 사용된다는 것을 설명합니다.

> [AZURE.IMPORTANT]**프로젝트 번호**는 **프로젝트 ID**와 혼동하지 않아야 합니다. 이제 프로젝트 ID는 다를 수 있습니다(새 프로젝트의 이름). Engagement SDK에서 통합해야 하는 것은 **프로젝트 번호**이고, 이 번호는 [Google 개발자 콘솔]의 **개요** 메뉴에 표시됩니다.

##SDK 통합

### 장치 등록 관리

각 장치는 Google 서버에 등록 명령을 보내야 하며, 그렇지 않은 경우 해당 서버에 연결할 수 없습니다.

장치는 GCM 알림에서 등록 취소할 수 있습니다(응용 프로그램이 제거된 경우 장치가 자동으로 등록 취소됨).

[GCM 클라이언트 라이브러리]를 사용하는 경우 android-sdk-gcm-receive를 직접 읽을 수 있습니다.

등록 의도를 아직 직접 보내지 않은 경우 Engagement에서 자동으로 장치를 등록하도록 설정할 수 있습니다.

이 기능을 사용하려면 `AndroidManifest.xml` 파일의 `<application/>` 태그 내에 다음을 추가합니다.

			<!-- If only 1 sender, don't forget the \n, otherwise it will be parsed as a negative number... -->
			<meta-data android:name="engagement:gcm:sender" android:value="<Your Google Project Number>\n" />

### Engagement 푸시 서비스에 등록 ID를 전달하고 알림을 수신합니다.

Engagement 푸시 서비스에 장치의 등록 ID를 전달하고 해당 알림을 수신하려면 `AndroidManifest.xml` 파일의 `<application/>` 태그 내에 다음을 추가합니다(장치 등록을 직접 관리하는 경우에도).

			<receiver android:name="com.microsoft.azure.engagement.gcm.EngagementGCMEnabler"
			  android:exported="false">
			  <intent-filter>
			    <action android:name="com.microsoft.azure.engagement.intent.action.APPID_GOT" />
			  </intent-filter>
			</receiver>
			
			<receiver android:name="com.microsoft.azure.engagement.gcm.EngagementGCMReceiver" android:permission="com.google.android.c2dm.permission.SEND">
			  <intent-filter>
			    <action android:name="com.google.android.c2dm.intent.REGISTRATION" />
			    <action android:name="com.google.android.c2dm.intent.RECEIVE" />
			    <category android:name="<your_package_name>" />
			  </intent-filter>
			</receiver>

`AndroidManifest.xml`에서(`</application>` 태그 다음) 다음 권한이 있는지 확인합니다.

			<uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
			<uses-permission android:name="<your_package_name>.permission.C2D_MESSAGE" />
			<permission android:name="<your_package_name>.permission.C2D_MESSAGE" android:protectionLevel="signature" />

##서버 API 키에 Engagement 액세스 권한을 부여합니다.

아직 작업을 수행하지 않은 경우 [Google 개발자 콘솔]에서 **서버 API 키**를 만듭니다.

서버 키에는 **IP 제한이 없어야 합니다**.

이 문서를 작성하는 시점을 기준으로(2014 년 2월 5일) 절차는 다음과 같습니다.

-   이렇게 하려면 [Google 개발자 콘솔]을 엽니다.
-   절차의 이전 부분에서 사용한 것과 동일한 프로젝트(`AndroidManifest.xml`에서 통합한 **프로젝트 번호**를 사용하는 프로젝트)를 선택합니다.
-   "Public API access" 섹션에서 APIs & auth -> Credentials를 클릭한 다음 "CREATE NEW KEY"를 클릭합니다.
-   "Server key"를 선택합니다.
-   다음 화면에서 서버 키를 공란으로 비워 두고**(IP 제한 없음)** Create를 클릭합니다.
-   생성된 **API 키**를 복사합니다.
-   $/#application/YOUR_ENGAGEMENT_APPID/native-push로 이동합니다.
-   GCM 섹션에서 방금 생성하고 복사한 키로 API 키를 편집합니다.

이제 도달률 알림 및 설문 조사를 만들 때 "Any Time"을 선택할 수 있습니다.

> [AZURE.IMPORTANT]Engagement에서 실제로 **서버 키**를 요구하지만, Engagement 서버에서 Android 키를 사용할 수는 없습니다.

##테스트

이제 Android에서 Engagement 통합을 테스트하는 방법을 읽어보고 통합을 확인하세요.


[Send-to-Sync]: http://developer.android.com/google/gcm/adv.html#collapsible
[<http://developer.android.com/guide/google/gcm/gs.html>]: http://developer.android.com/guide/google/gcm/gs.html
[Google Developers Console]: https://cloud.google.com/console
[GCM 클라이언트 라이브러리]: http://developer.android.com/guide/google/gcm/gs.html#libs
[Google 개발자 콘솔]: https://cloud.google.com/console

 

<!---HONumber=July15_HO2-->