---
title: Azure Mobile Engagement 문제 해결 가이드
description: Azure Mobile Engagement용 문제 해결 가이드
services: mobile-engagement
documentationcenter: ''
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 31134a29-a513-4e5e-b626-f6cf6fe04769
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 1242b2b796821856561a82d4a585b26693aadadd
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2018
---
# <a name="azure-mobile-engagement---troubleshooting-guide"></a>Azure Mobile Engagement - 문제 해결 가이드
> [!IMPORTANT]
> Azure Mobile Engagement는 2018/3/31에 사용이 중지됩니다. 이 페이지는 이후에 삭제됩니다.
> 

## <a name="introduction"></a>소개
다음 문제 해결 가이드는 일반적으로 나타나는 문제의 근본 원인을 이해하는데 도움이 되고 스스로 문제를 해결할 수 있도록 합니다. 

## <a name="general"></a>일반
일반적으로 다음을 항상 확인해야 합니다.

1. [시작 자습서](mobile-engagement-windows-store-dotnet-get-started.md)
2. 최신 버전의 플랫폼 SDK를 사용합니다. 
3. 일부 문제는 에뮬레이터에만 관련이 있으므로 실제 장치와 에뮬레이터 모두에서 테스트합니다. 
4. [여기](../azure-subscription-service-limits.md)
5. 모바일 고객 관리 서비스 백 엔드에 연결할 수 없거나 데이터가 지속적으로 로드되지 않는 경우 [여기](https://azure.microsoft.com/status/)

## <a name="monitor-issues"></a>'모니터' 문제
### <a name="i-am-not-seeing-my-device-showing-up-on-the-monitor-tab"></a>모니터 탭에 장치가 표시되지 않습니다.
모니터 탭에서는 실시간으로 모바일 고객 관리 플랫폼에 연결된 장치를 보여 줍니다. 에뮬레이터 및 장치에서 디버깅하는 경우 여기에 하나 이상의 세션이 표시되어야 합니다. 응용 프로그램이 배포된 경우 실시간으로 플랫폼에 연결된 장치를 반영하는 활성 세션 계기가 표시됩니다. 

모니터 탭에서 장치에 나타나지 않는 경우 SDK 통합 문제일 가능성이 높습니다. 문제 해결을 위해 수행할 일부 일반적인 단계는 다음과 같습니다.

1. 모바일 앱에서 올바른 연결 문자열을 사용하고 있는지와 문자열이 API 키 섹션이 아닌 SDK 키 섹션에 있는지 확인합니다. 연결 문자열은 모니터 탭에서 장치를 확인하는 모바일 고객 관리 앱의 인스턴스에 모바일 앱을 연결합니다. 
2. Windows의 플램폼의 경우 - 페이지가 `OnNavigatedTo` 메서드를 재정의하는 경우에는 `base.OnNavigatedTo(e)`을(를) 호출해야 합니다.
3. 모바일 고객 관리를 기존 모바일 앱으로 통합하는 경우 [여기](mobile-engagement-windows-store-integrate-engagement.md)
4. [시작 자습서](mobile-engagement-windows-store-dotnet-get-started.md)에 설명한 대로 작업하는 플랫폼에 따라 EngagementActivity를 사용하여 페이지를 재정의하여 하나 이상의 화면/작업을 보내는지 확인합니다.

### <a name="i-am-seeing-the-monitor-tab-showing-a-session-even-when-i-have-disconnected-or-closed-my-app-emulator"></a>앱/에뮬레이터의 연결을 끊거나 닫은 경우에도 모니터 탭에서 세션을 표시합니다.
해당 시점에 플랫폼에 유일하게 연결되어 있고 앱을 열기 위해 에뮬레이터를 사용하는 경우 에뮬레이터 쿼크 때문일 가능성이 높습니다. 일반적으로 앱 세션을 성공적으로 연결 해제하려면 에뮬레이터의 홈 화면으로 돌아와야 합니다. 또한 Windows 플랫폼에서 Visual Studio를 사용하여 디버깅하는 동안 Visual Studio에 있는 것을 확인해야 하며 **수명 주기 이벤트** 메뉴 모음으로 이동하고 **일시 중단**을 클릭하여 실제로 세션을 닫아야 합니다. 자세한 내용은 [Windows 자습서](mobile-engagement-windows-store-dotnet-get-started.md) 를 참조하세요. 

## <a name="analytics-issues"></a>'분석' 문제
### <a name="i-am-not-seeing-any-data-refreshed-data-on-analytics-tab"></a>분석 탭에 데이터/새로 고친 데이터가 나타나지 않습니다.
분석 데이터는 정기적으로 다시 계산되고 이 새로 고침에 대해 최대 24시간까지 걸릴 수 있습니다. 이 데이터는 실시간이 아니며 24시간 기간 내에서 새로 고침이 표시됩니다.
`EngagementActivity`(으)로 하나 이상의 페이지를 재정의하거나 `SendActivity`을(를) 명시적으로 호출하여 하나 이상의 화면 또는 작업을 플랫폼 백 엔드에 전송하는지 확인하세요. 

### <a name="i-am-seeing-incorrectly-captured-datetime-for-a-device-on-the-analytics-tab"></a>분석 탭에서 장치에 대한 캡처된 날짜/시간이 올바르지 않습니다.
분석에 대한 기간은 사용자의 장치 설정의 날짜를 기준으로 합니다. 따라서 장치에 날짜가 올바르게 설정되었는지 확인합니다. 

## <a name="segment-issues"></a>'세그먼트' 문제
### <a name="i-created-a-segment-and-it-is-showing-up-as-greyed-out-or-not-showing-any-data"></a>세그먼트를 만들었는데 회색으로 표시되거나 데이터가 표시되지 않습니다.
세그먼트 만들기는 현재 실시간이 아닙니다. 분석 데이터가 집계되는 동시에 계산되므로 최대 24시간까지 걸릴 수 있습니다. 나중에 다시 확인해야 하는 한편 모바일 앱에서 세그먼트를 형성하는 기준으로 실제로 데이터를 보내고 있는지도 확인해야 합니다. 예: 이벤트에서 'foo'가 모바일 장치에서 전송되지 않는다고 전달한 경우 EventName = foo라는 조건으로 만들어진 세그먼트에 대한 세그먼트 데이터가 없습니다. 또한 SDK 통합을 확인하여 모바일 앱에서 데이터를 올바르게 보내고 있는지 확인해야 합니다. 

## <a name="reach-or-push-notifications-issues"></a>'도달률' 또는 푸시 알림 문제
### <a name="my-push-messages-are-not-being-delivered"></a>푸시 메시지 전달되지 않습니다.
1. 먼저 테스트 장치에 알림을 보내 모든 구성 요소 - 모바일 앱, SDK 및 서비스가 올바르게 연결되었고 푸시 알림을 전달할 수 있는지 확인합니다. 
2. 예약되지 않거나 지정된 대상 기준이 없는 캠페인을 통해 항상 가장 간단한 '앱 알림'을 먼저 보냅니다. 알림 연결이 올바르게 작동하는지 다시 증명하기 위한 것입니다. 
3. 앱 내 알림 전달에 문제가 발생한 경우에도 앱 알림을 먼저 보내는 것이 좋은 첫 번째 단계입니다. 
4. 모바일 앱에 대해 '네이티브 푸시'가 올바르게 구성되었는지 확인합니다. 플랫폼에 따라 키(Android, Windows) 또는 인증서(iOS)를 포함합니다. 자세한 내용은 [사용자 인터페이스 - 설정](mobile-engagement-user-interface-settings.md)
5. 앱 알림은 모바일 OS를 통해 사용자에 의해 차단될 수 있으므로 적용되지 않는지 확인합니다. 
6. 푸시 알림이 API를 통해서만 전송되는 것을 확인하므로 도달률 캠페인의 *캠페인* 섹션에서 **대상을 무시하고 API를 통해 사용자에게 푸시 전송** 옵션을 설정하지 않았는지 확인합니다. 
7. Wi-Fi와 휴대폰 운영자 네트워크를 통해 연결되는 장치를 사용하여 푸시 캠페인을 테스트하여 가능한 문제 원인에서 네트워크 연결을 제외할 수 있는지 확인합니다.
8. 동기화되지 않은 장치는 푸시 알림 서비스의 기능을 방해하여 알림을 전달하므로 장치/에뮬레이터의 시스템 날짜/시간이 올바른지 확인합니다. 

더 많은 플랫폼 관련 문제 해결 지침:

1. **iOS** 
   
   * 인증서가 유효하고 iOS 푸시 알림에 대해 만료되지 않았는지 확인합니다. 
   * 모바일 고객 관리 앱에서 *프로덕션* 인증서를 올바르게 구성되었는지 확인합니다. 
   * *실제, 물리적 장치*에서 테스트하고 있는지 확인합니다. iOS 시뮬레이터는 푸시 메시지를 처리할 수 없습니다.
   * 모바일 앱에서 번들 식별자가 올바르게 구성되었는지 확인합니다. [여기](https://developer.apple.com/library/prerelease/ios/documentation/IDEs/Conceptual/AppDistributionGuide/AddingCapabilities/AddingCapabilities.html#//apple_ref/doc/uid/TP40012582-CH26-SW6)
   * 테스트할 때 모바일 프로비저닝 프로필에서 "임시" 배포를 사용합니다. 앱이 "디버그"를 사용하여 컴파일된 경우 알림을 받을 수 없습니다.
2. **Android**
   
   * \n 문자로 이어지는 모바일 앱의 AndroidManifest.xml 파일에서 올바른 프로젝트 번호를 지정했는지 확인합니다. 
     
           <meta-data android:name="engagement:gcm:sender" android:value="************\n" />
   * Android 매니페스트 파일에 권한이 누락되거나 잘못 구성되었는지 확인합니다. 
   * 클라이언트에 추가할 프로젝트 번호가 GCM 서버 키를 가져온 동일한 계정에서 가져온 것인지 확인합니다. 계정이 일치하지 않으면 푸시를 내보낼 수 없습니다. 
   * 앱 내가 아닌 시스템 알림을 수신하는 경우 Android 매니페스트 파일에서 올바른 아이콘을 지정하지 않았을 수도 있으므로 [알림 섹션 아이콘 지정](mobile-engagement-android-get-started.md) 을 검토합니다. 
   * BigPicture 알림을 전송하는 경우 외부 이미지 서버가 있는지 확인한 다음 HTTP "GET" 및 "HEAD"를 지원할 수 있어야 합니다.
3. **Windows**
   
   * 유효한 Windows 스토어 앱으로 앱을 연결했는지 확인합니다. Visual Studio에서 마우스 오른쪽 단추로 프로젝트를 클릭하고 "저장소와 앱 연결" 옵션을 선택하고 Windows 스토어에서 만든 앱을 선택해야 합니다. 이 Windows 스토어 앱은 모바일 고객 지원 포털에서 구성하기 위해 네이티브 푸시 자격 증명에서 얻은 것과 동일해야 합니다.
   * `EngagementOverlay` 통합으로 앱 내 알림이 아닌 앱 푸시 알림을 수신하는 경우 페이지에 루트 grid 요소가 있는지 확인합니다. EngagementOverlay는 xaml 파일에서 발견되는 첫 번째 "Grid" 요소를 사용하여 페이지에 웹 보기 2개를 추가합니다. 웹 보기가 설정되는 곳을 찾으려는 경우 "EngagementGrid"라는 grid를 정의할 수 있지만 앱 내 알림으로 알림 및 다음 알림을 표시하는 두 개의 후속 웹 보기에 대한 충분한 높이 및 너비가 있는지 확인해야 합니다.
     
           <Grid x:Name="EngagementGrid"></Grid>

### <a name="i-created-a-push-notificationannouncement-campaign-and-even-after-it-sent-me-the-notification-it-is-showing-as-active-what-does-it-mean"></a>푸시 알림/알림/캠페인을 만들고 알림이 전송된 후에도 '활성'으로 표시됩니다. 무엇을 의미하나요?
모바일 참여 플랫폼에 연결된 새 장치를 의미하는 장기 실행 푸시 알림이며 캠페인에서 설정한 조건을 만족하는 한 여기에서 구성한 알림이 자동으로 전송되므로 Mobile Engagement에서 만든 **캠페인** 은 그렇게 불립니다. 일회성 단일 알림 설정이 아닙니다. **마침** 단추를 수동으로 클릭하여 추가 알림을 전송하지 않도록 캠페인을 종료해야 합니다. 

### <a name="i-created-a-push-campaign-and-i-am-receiving-notifications-successfully-however-whenever-i-open-up-the-app-i-get-the-same-notification-even-when-i-had-actioned-it-before"></a>푸시 캠페인을 만들고 알림을 성공적으로 수신하고 있지만 앱을 열 때마다 이전에 작업을 했을 때와 동일한 알림을 받습니다.
에뮬레이터 또는 TestFlight과 같은 일부 테스트 프레임워크를 사용하는 경우 테스트하는 동안 발생할 수 있습니다. 모든 앱 실행 인스턴스 시 새 DeviceID를 획득하고 모바일 고객 관리 플랫폼이 새 장치로 처리하게 하고 알림을 전송하는 백 엔드로 이를 전송합니다. 

## <a name="getting-support"></a>지원 받기
문제를 스스로 해결할 수 없는 경우 다음을 수행할 수 있습니다.

1. StackOverflow 포럼 및 [MSDN 포럼](https://social.msdn.microsoft.com/Forums/windows/en-US/home?forum=azuremobileengagement) 의 기존 스레드에서 문제를 검색하고 그렇지 않으면 질문을 합니다. 
2. 누락된 기능을 찾은 경우 [UserVoice 포럼](https://feedback.azure.com/forums/285737-mobile-engagement/)
3. Microsoft 지원이 있는 경우 다음 세부 정보를 제공하여 지원 인시던트를 엽니다. 
   * Azure 구독 ID
   * 플랫폼(예:: iOS, Android 등)
   * 앱 ID
   * 캠페인 ID(푸시 알림 문제의 경우)
   * 장치 ID
   * 모바일 고객 관리 SDK 버전(예: Android SDK v2.1.0)
   * 정확한 오류 메시지 및 시나리오가 있는 오류 세부 정보

