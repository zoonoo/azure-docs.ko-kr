<properties
	pageTitle="Android Mobile Engagement SDK를 사용한 고급 구성"
	description="Azure Mobile Engagement SDK를 사용한 Android용 고급 구성 옵션"
	services="mobile-engagement"
	documentationCenter="mobile"
	authors="piyushjo"
	manager="erikre"
	editor="" />

<tags
	ms.service="mobile-engagement"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-android"
	ms.devlang="Java"
	ms.topic="article"
	ms.date="05/10/2016"
	ms.author="piyushjo;ricksal" />

# Android Mobile Engagement SDK를 사용한 고급 구성

> [AZURE.SELECTOR]
- [Android](mobile-engagement-android-logging.md)

이 절차에서는 Engagement Android 앱에 대한 다양한 고급 구성 옵션을 구성하는 방법을 설명합니다.

## 필수 조건

[AZURE.INCLUDE [선행 조건](../../includes/mobile-engagement-android-prereqs.md)]

## 사용 권한 요구 사항
다양한 옵션에 특정 권한이 필요하며 참조할 수 있도록 모든 권한이 여기에 나열되어 있으며 특정 기능에 인라인으로 포함해야 합니다. 프로젝트의 AndroidManifest.xml에서 `<application>` 태그 바로 앞 또는 뒤에 다음 권한을 추가합니다.

사용 권한 코드는 다음과 같아야 하며 여기에 아래 표에서 해당하는 권한을 입력합니다.

		<uses-permission android:name="android.permission.[specific permission]"/>



| 사용 권한 | 사용할 경우 |
| ---------- | --------- |
| 인터넷 | 기본 보고 |
| ACCESS\_NETWORK\_STATE" | 기본 보고 |
| WRITE\_EXTERNAL\_STORAGE | 기본 보고 |
| RECEIVE\_BOOT\_COMPLETED | 기본 보고 |
| VIBRATE | 기본 보고 |
| DOWNLOAD\_WITHOUT\_NOTIFICATION | 큰 그림 알림 |
| WAKE\_LOCK | wifi를 사용하거나 화면이 꺼진 경우 통계 수집 |
| RECEIVE\_BOOT\_COMPLETED | 백그라운드 보고 사용 |
| ACCESS\_COARSE\_LOCATION | 실시간 위치 보고 |
| ACCESS\_FINE\_LOCATION | GPS 기반 보고 |



Android M부터는 [일부 권한이 런타임 시 관리](mobile-engagement-android-location-reporting.md#Android-M-Permissions)됩니다.

``ACCESS_FINE_LOCATION``을 이미 사용 중인 경우 ``ACCESS_COARSE_LOCATION``을 사용할 필요가 없습니다.

## 매니페스트 파일 구성 옵션

### 충돌 보고서

크래시 보고를 비활성화하려는 경우 `<application>` 태그와 `</application>` 태그 사이에 다음을 추가합니다.

		<meta-data android:name="engagement:reportCrash" android:value="false"/>

### 버스트 임계값

기본적으로 Engagement 서비스는 로그를 실시간으로 보고합니다. 응용 프로그램이 로그를 매우 자주 보고하는 경우 로그를 버퍼링한 후 정기적으로 한 번에 모두 보고하는 것이 좋습니다. 이를 "버스트 모드"라고 합니다. 그렇게 하려면 `<application>` 및 `</application>` 태그 사이에 이 코드를 추가합니다.

		<meta-data android:name="engagement:burstThreshold" android:value="{interval between too bursts (in milliseconds)}"/>

버스트 모드를 사용하는 경우 배터리 수명은 약간 길어지지만 Engagement 모니터에 영향을 주게 됩니다. 모든 세션 및 작업 기간이 버스트 임계값으로 반올림되므로 버스트 임계값보다 짧은 세션과 작업은 표시되지 않을 수도 있습니다. 30000(30초) 이하의 버스트 임계값을 사용하는 것이 좋습니다.

### 세션 시간 제한

기본적으로 세션은 마지막 작업(일반적으로 홈 또는 뒤로 키를 누르거나, 휴대폰을 유휴로 설정하거나, 다른 응용 프로그램으로 이동함으로써 발생)의 종료 10초 후에 종료됩니다. 그러면 사용자가 응용 프로그램을 종료하고 빠르게 응용 프로그램으로 돌아갈 때(이미지를 선택하거나 알림을 확인하는 등의 작업을 통해)마다 세션 분할을 피할 수 있습니다. 이 매개 변수를 수정할 수도 있습니다. 그렇게 하려면 `<application>` 태그와 `</application>` 태그 사이에 다음을 추가합니다.

		<meta-data android:name="engagement:sessionTimeout" android:value="{session timeout (in milliseconds)}"/>

## 로그 보고 사용 안 함

### 메서드 호출 사용

Engagement에서 로그 전송을 중지하려면 다음을 호출할 수 있습니다.

			EngagementAgent.getInstance(context).setEnabled(false);

이 호출은 영구적이며, 공유 기본 설정 파일을 사용합니다.

이 함수를 호출할 때 Engagement가 활성 상태인 경우 서비스가 중지되는 데 1분이 걸릴 수 있습니다. 그러나 다음에 응용 프로그램을 시작할 때는 서비스가 시작되지 않습니다.

또한 `true`(으)로 동일한 함수를 호출하여 로그 보고를 다시 활성화할 수 있습니다.

### 고유한 `PreferenceActivity`에서 통합

이 함수를 호출하지 않고 기존 `PreferenceActivity`에서 직접 이 설정을 통합할 수 있습니다.


다음과 같이 `AndroidManifest.xml` 파일의 기본 설정 파일을(원하는 모드에서) `application meta-data`와(과) 함께 사용하도록 Engagement를 구성할 수 있습니다.

-   `engagement:agent:settings:name` 키는 공유 기본 설정 파일의 이름을 정의하는 데 사용됩니다.
-   `engagement:agent:settings:mode` 키는 공유 기본 설정 파일의 모드를 정의하는 데 사용되며, `PreferenceActivity`에서와 동일한 모드를 사용해야 합니다. 모드는 숫자로 전달해야 합니다. 코드에서 상수 플래그의 조합을 사용하는 경우 전체 값을 확인합니다.

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

이제 다음과 같은 기본 설정 레이아웃에서 `CheckBoxPreference`을(를) 추가할 수 있습니다.

			<CheckBoxPreference
			  android:key="engagement:enabled"
			  android:defaultValue="true"
			  android:title="Use Engagement"
			  android:summaryOn="Engagement is enabled."
			  android:summaryOff="Engagement is disabled." />

<!---HONumber=AcomDC_0511_2016-->