---
title: Azure Mobile Engagement 문제 해결 가이드 - SDK
description: Azure Mobile Engagement에서 SDK 통합 문제 해결
services: mobile-engagement
documentationcenter: ''
author: piyushjo
manager: erikre
editor: ''
ms.assetid: de265cf1-2f88-43ef-8616-156ada5be7b6
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 1f617b3c3b221a38b63be6f61f7553b390cf2f7c
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2018
---
# <a name="troubleshooting-guide-for-sdk-integration-issues"></a>SDK 통합 문제에 대한 문제 해결 가이드
> [!IMPORTANT]
> Azure Mobile Engagement는 2018/3/31에 사용이 중지됩니다. 이 페이지는 이후에 삭제됩니다.
> 

다음은 Azure Mobile Engagement가 응용 프로그램에 통합되는 방법과 관련해서 발생할 수 있는 문제입니다.

## <a name="sdk-issues-discovered-by-a-failure-in-another-area-of-your-application"></a>응용 프로그램의 다른 영역에서 발생한 오류를 통해 검색된 SDK 문제
### <a name="issue"></a>문제
* 분석, 모니터링, 구분 또는 대시보드의 UI 데이터 수집 오류
* 푸시 오류(푸시가 앱 내부 또는 외부나 두 위치에서 모두 작동하지 않음)
* 고급 기능 오류(추적, 지리적 위치 또는 플랫폼별 푸시 미작동)
* API 오류(API에서 자동 오류가 자주 발생하며 오류 메시지가 표시되지 않음)
* 서비스 오류(Azure Mobile Engagement 기능이 응용 프로그램에서 작동하지 않음)

### <a name="causes"></a>원인
* Azure Mobile Engagement SDK를 사용하여 해결해야 하는 대부분의 문제는 UI 데이터 수집 오류, 푸시 오류, 고급 기능 오류, API 오류, 응용 프로그램 작동 중단, 명백한 서비스 중단과 같은 응용 프로그램의 오류를 통해 검색됩니다.  
* Azure Mobile Engagement의 특정 기능이 앱에서 한 번도 정상적으로 작동한 적이 없다면 통합을 완료해야 합니다. 
* Azure Mobile Engagement의 특정 기능이 작동하다가 중지되는 경우에는 Azure Mobile Engagement SDK를 사용하여 최신 버전으로 업그레이드해야 할 수 있습니다. Azure Mobile Engagement에서 지원하는 각 플랫폼(Android, iOS, Windows, Windows Phone)에 대해 다른 버전의 Azure Mobile Engagement SDK가 제공됩니다.

#### <a name="sdk-integration"></a>SDK 통합
* Azure Mobile Engagement가 SDK에 올바르게 통합되지 않았습니다(분석).
* 도달률이 SDK에 올바르게 통합되지 않았습니다(앱 내 푸시/앱 외부 푸시).
* 인증서가 만료되었거나 프로덕션/개발 버전이 잘못되었습니다(iOS에만 해당).
* GCM 또는 ADM이 SDK에 올바르게 통합되지 않았습니다(Android에만 해당 - 서비스별 푸시).
* 추적이 SDK에 올바르게 통합되지 않았습니다(설치 스토어 추적).
* 지연 위치 또는 GPS 위치가 SDK에 올바르게 통합되지 않았습니다(지리적 위치 기준 대상 지정).

**참고 항목:**

* [SDK 설명서 - 통합 가이드][Link 5] 
* [문제 해결 가이드 - 푸시][Link 23]

#### <a name="sdk-upgrade"></a>SDK 업그레이드
* SDK를 업그레이드하여 이전 버전 SDK의 문제를 해결해야 합니다(대개 최신 버전 장치 OS와 관련이 있음).
* 모든 이전 버전 앱을 장치에서 제거한 다음 최신 버전 앱을 다시 설치하고 Azure Mobile Engagement UI에서 장치 ID를 다시 등록하여 장치에서 최신 버전의 앱을 사용 중임을 확인합니다.

**참고 항목:**

* [SDK 설명서 - 릴리스 정보](http://go.microsoft.com/fwlink/?LinkId= 525554) 
* [SDK 설명서 - 업그레이드 가이드](http://go.microsoft.com/fwlink/?LinkId= 525554)

#### <a name="sdk-other"></a>기타 SDK 관련 문제
* 응용 프로그램 매니페스트 "AndroidManifest.xml"의 오류로 인해 Azure Mobile Engagement가 작동하지 않습니다(Android에만 해당).
* SDK 통합과 API 사용에 관한 일반적인 문제는 SDK 키와 API 키를 혼동한다는 점입니다.

**참고 항목:**

* [개념 - 용어집][Link 6]

## <a name="advanced-coding-issues"></a>고급 코딩 문제
### <a name="issue"></a>문제
* Azure Mobile Engagement와 직접 관련되지 않은 플랫폼별 코드로 인해 iOS, Android, Windows Phone에서 문제가 발생할 수 있습니다.

### <a name="causes"></a>원인
* Azure Mobile Engagement의 고급 코딩 문제는 대부분 Azure Mobile Engagement와 직접적인 관련이 없는 플랫폼별 코드를 잘못 작성하는 경우 발생합니다. 이 경우에는 Android, iOS, 웹, Windows, Windows Phone 등 개발을 진행 중인 플랫폼과 관련된 설명서 및 Azure Mobile Engagement 설명서를 모두 확인해야 합니다.
* "범주"를 잘못 구성하면 알림에서 앱 내부나 외부의 다른 위치로 연결하지 못하게 됩니다(Android에만 해당). 
* iOS 코드에서 "UIKit.framework"를 "optional"로 설정하지 않으면 "기호를 찾을 수 없음" 오류가 표시되거나 이전 버전의 iOS 장치 작동이 중단됩니다(iOS에만 해당).
* 인증서가 잘못되었거나 인증서의 개발 버전 또는 프로덕션 버전을 잘못 사용하면 푸시 문제가 발생합니다(iOS에만 해당).
* Android 및 iOS에서 시스템 센터가 앱 외부 푸시에 대해 작동하는 방식 등 Azure Mobile Engagement에서 제어할 수 없는 플랫폼의 기본적인 제한이 있습니다.
* Azure Mobile Engagement는 iOS 및 Android에 대해 참조용으로 Azure Mobile Engagement에서 사용하는 내부 패키지의 전체 목록을 게시합니다. Azure Mobile Engagement의 일부 기능은 Android, iOS, 웹, Windows, Windows Phone 등의 특정 플랫폼에만 제공됩니다.

### <a name="see-also"></a>참고 항목
* [문제 해결 가이드 - 푸시][Link 23] 
* [SDK 설명서 - 릴리스 정보][Link 5]
* [SDK 설명서 - 업그레이드 가이드][Link 5]

## <a name="application-crashes"></a>응용 프로그램 작동 중단
### <a name="issue"></a>문제
* 최종 사용자 장치에서 응용 프로그램 작동이 중단됩니다.

### <a name="causes"></a>원인
* *분석 UI* 또는 *분석 API*에서 작동 중단 정보를 확인할 수 있습니다.
* 테스트 장치의 장치 ID를 찾은 다음 최종 사용자가 작동 중단 문제를 파악할 수 있도록 응용 프로그램 작동을 중단시킨 동일 작업을 수행할 수 있습니다.
* 최신 버전의 SDK로 업그레이드하면 응용 프로그램 작동 중단의 원인이 되는 Azure Mobile Engagement SDK의 알려진 문제가 해결되는 경우도 있습니다. 따라서 작동 중단 문제를 조사할 때는 사용 중인 플랫폼의 릴리스 정보를 확인하세요.

### <a name="see-also"></a>참고 항목
* [SDK 설명서 - 릴리스 정보][Link 5]
* [SDK 설명서 - 업그레이드 가이드][Link 5]

## <a name="app-store-upload-failures"></a>앱 스토어 업로드 오류
### <a name="issue"></a>문제
* 최신 버전의 앱을 Apple, Google 또는 Windows 앱 스토어에 업로드할 때 발생하는 오류에 대해 설명합니다.

### <a name="causes"></a>원인
* 앱 스토어에서는 특정 기능이 사용하도록 설정된 앱을 차단할 수 있습니다. 예를 들어 Apple 스토어에서는 앱에서 IDFV 사용을 금지하며, GooglePlay 스토어에서는 앱 간의 응용 프로그램 정보 공유를 금지합니다. 
* 스토어에 앱을 업로드하는 데 문제가 있으면 최신 버전 SDK 및 사용 중인 플랫폼의 릴리스 정보를 확인하세요.

<!--Link references-->
[Link 1]: mobile-engagement-user-interface.md
[Link 2]: mobile-engagement-troubleshooting-guide.md
[Link 3]: mobile-engagement-how-tos.md
[Link 4]: http://go.microsoft.com/fwlink/?LinkID=525553
[Link 5]: http://go.microsoft.com/fwlink/?LinkID=525554
[Link 6]: http://go.microsoft.com/fwlink/?LinkId=525555
[Link 7]: https://account.windowsazure.com/PreviewFeatures
[Link 8]: https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=azuremobileengagement
[Link 9]: http://azure.microsoft.com/en-us/services/mobile-engagement/
[Link 10]: http://azure.microsoft.com/en-us/documentation/services/mobile-engagement/
[Link 11]: http://azure.microsoft.com/en-us/pricing/details/mobile-engagement/
[Link 12]: mobile-engagement-user-interface-navigation.md
[Link 13]: mobile-engagement-user-interface-home.md
[Link 14]: mobile-engagement-user-interface-my-account.md
[Link 15]: mobile-engagement-user-interface-analytics.md
[Link 16]: mobile-engagement-user-interface-monitor.md
[Link 17]: mobile-engagement-user-interface-reach.md
[Link 18]: mobile-engagement-user-interface-segments.md
[Link 19]: mobile-engagement-user-interface-dashboard.md
[Link 20]: mobile-engagement-user-interface-settings.md
[Link 21]: mobile-engagement-troubleshooting-guide-analytics.md
[Link 22]: mobile-engagement-troubleshooting-guide-apis.md
[Link 23]: mobile-engagement-troubleshooting-guide-push-reach.md
[Link 24]: mobile-engagement-troubleshooting-guide-service.md
[Link 25]: mobile-engagement-troubleshooting-guide-sdk.md
[Link 26]: mobile-engagement-troubleshooting-guide-sr-info.md
[Link 27]: mobile-engagement-user-interface-reach-campaign.md
[Link 28]: mobile-engagement-user-interface-reach-criterion.md
[Link 29]: mobile-engagement-user-interface-reach-content.md

