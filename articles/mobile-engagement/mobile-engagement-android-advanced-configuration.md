---
title: Azure Mobile Engagement Android SDK용 고급 구성
description: Azure Mobile Engagement Android SDK를 사용하는 Android 매니페스트를 포함하여 고급 구성 옵션에 대해 설명합니다
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 37d2c09a-86fa-473d-8987-c7e35a0eb3e8
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: Java
ms.topic: article
ms.date: 10/04/2016
ms.author: piyushjo;ricksal
ms.openlocfilehash: 29a7bb7daae59e2034504ce27c9ba66755b11e4b
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2018
---
# <a name="advanced-configuration-for-azure-mobile-engagement-android-sdk"></a>Azure Mobile Engagement Android SDK용 고급 구성
> [!IMPORTANT]
> Azure Mobile Engagement는 2018/3/31에 사용이 중지됩니다. 이 페이지는 이후에 삭제됩니다.
> 

> [!div class="op_single_selector"]
> * [유니버설 Windows](mobile-engagement-windows-store-advanced-configuration.md)
> * [Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md)
> * [iOS](mobile-engagement-ios-integrate-engagement.md)
> * [Android](mobile-engagement-android-advanced-configuration.md)
>
>

이 절차에서는 Azure Mobile Engagement Android 앱에 대한 다양한 고급 구성 옵션을 구성하는 방법을 설명합니다.

## <a name="prerequisites"></a>필수 조건
[!INCLUDE [Prereqs](../../includes/mobile-engagement-android-prereqs.md)]

## <a name="permission-requirements"></a>사용 권한 요구 사항
일부 옵션에 특정 권한이 필요하며 참조할 수 있도록 모든 권한이 여기에 나열되어 있으며 특정 기능에 인라인으로 포함해야 합니다. 프로젝트의 AndroidManifest.xml에서 `<application>` 태그 바로 앞 또는 뒤에 다음 권한을 추가합니다.

사용 권한 코드는 다음과 같아야 하며 여기에 아래 테이블에서 해당하는 권한을 입력합니다.

    <uses-permission android:name="android.permission.[specific permission]"/>


| 사용 권한 | 사용할 경우 |
| --- | --- |
| 인터넷 |필수 사항입니다. 기본 보고용 |
| ACCESS_NETWORK_STATE |필수 사항입니다. 기본 보고용 |
| RECEIVE_BOOT_COMPLETED |필수 사항입니다. 장치 재부팅 후 알림 센터를 표시합니다. |
| WAKE_LOCK |권장됩니다. WiFi를 사용하거나 화면이 꺼져 있을 때 데이터를 수집합니다. |
| VIBRATE |선택 사항입니다. 알림이 수신될 때 진동을 울립니다. |
| DOWNLOAD_WITHOUT_NOTIFICATION |선택 사항입니다. Android 큰 그림 알림을 사용합니다. |
| WRITE_EXTERNAL_STORAGE |선택 사항입니다. Android 큰 그림 알림을 사용합니다. |
| ACCESS_COARSE_LOCATION |선택 사항입니다. 실시간 위치 보고를 활성화합니다. |
| ACCESS_FINE_LOCATION |선택 사항입니다. GPS 기반 위치 보고를 활성화합니다. |

Android M부터는 [일부 권한이 런타임 시 관리됩니다](mobile-engagement-android-location-reporting.md#android-m-permissions).

``ACCESS_FINE_LOCATION``을 이미 사용 중인 경우 ``ACCESS_COARSE_LOCATION``을 사용할 필요가 없습니다.

## <a name="android-manifest-configuration-options"></a>Android 매니페스트 파일 구성 옵션
### <a name="crash-report"></a>충돌 보고서
크래시 보고를 사용하지 않도록 설정하려면 `<application>` 및 `</application>` 태그 사이에 다음 코드를 추가합니다.

    <meta-data android:name="engagement:reportCrash" android:value="false"/>

### <a name="burst-threshold"></a>버스트 임계값
기본적으로 Engagement 서비스는 로그를 실시간으로 보고합니다. 응용 프로그램이 로그를 매우 자주 보고하는 경우 로그를 버퍼링한 후 정기적으로 한 번에 모두 보고하는 것이 좋습니다. 이를 "버스트 모드"라고 합니다. 이렇게 하려면 `<application>` 및 `</application>` 태그 사이에 이 코드를 추가합니다.

    <meta-data android:name="engagement:burstThreshold" android:value="{interval between too bursts (in milliseconds)}"/>

버스트 모드를 사용하는 경우 배터리 수명은 약간 길어지지만 Engagement 모니터에 영향을 주게 됩니다. 모든 세션 및 작업 기간이 버스트 임계값으로 반올림되므로 버스트 임계값보다 짧은 세션과 작업은 표시되지 않을 수도 있습니다. 버스트 임계값은 30000(30초) 이하여야 합니다.

### <a name="session-timeout"></a>세션 시간 제한
 **홈** 또는 **뒤로** 키를 누르거나, 휴대폰을 유휴 상태로 설정하거나, 다른 응용 프로그램으로 이동하여 활동을 끝낼 수 있습니다. 기본적으로 세션은 마지막 활동이 끝나고 10초 후에 종료됩니다. 이를 통해 사용자가 응용 프로그램을 종료하고 빠르게 응용 프로그램으로 돌아갈 때(이미지를 선택하거나 알림을 확인하는 등의 작업을 통해)마다 세션 분할을 피할 수 있습니다. 이 매개 변수를 수정할 수도 있습니다. 이렇게 하려면 `<application>` 및 `</application>` 태그 사이에 이 코드를 추가합니다.

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
* `engagement:agent:settings:mode` 키는 공유 기본 설정 파일의 모드를 정의하는 데 사용됩니다. `PreferenceActivity`와 동일한 모드를 사용합니다. 모드는 숫자로 전달해야 합니다. 코드에서 상수 플래그의 조합을 사용하는 경우 전체 값을 확인합니다.

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
