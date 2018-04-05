---
title: Azure Mobile Engagement 문제 해결 가이드 - API
description: Azure Mobile Engagement에 대한 문제 해결 가이드 - API
services: mobile-engagement
documentationcenter: ''
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 3efc8a52-2b74-4917-b887-815ae8277474
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 10/04/2016
ms.author: piyushjo
ms.openlocfilehash: cda11c8d2f1d147a807083c5479d2b2cda65462c
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2018
---
# <a name="troubleshooting-guide-for-api-issues"></a>API 문제에 대한 문제 해결 가이드
> [!IMPORTANT]
> Azure Mobile Engagement는 2018/3/31에 사용이 중지됩니다. 이 페이지는 이후에 삭제됩니다.
> 

다음은 관리자가 API를 통해 Azure Mobile Engagement와 상호 작용하는 방법과 관련해서 발생할 수 있는 문제입니다.

## <a name="syntax-issues"></a>구문 문제
### <a name="issue"></a>문제
* API 사용 시에 구문 오류가 발생하거나 예기치 않은 동작이 수행됩니다.

### <a name="causes"></a>원인
* 구문 문제
  * 사용 중인 특정 API의 구문을 점검하여 옵션이 사용 가능한지 확인합니다.
  * API 사용 시 일반적으로 발생하는 문제 중 하나는 도달률 API와 푸시 API를 혼동하는 것입니다. 대부분의 작업은 푸시 API가 아닌 도달률 API를 사용하여 수행해야 합니다. 
  * SDK 통합 및 API 사용과 관련하여 흔히 발생하는 또 다른 문제는 SDK 키와 API 키를 혼동하는 것입니다.
  * API에 연결하는 스크립트는 최소 10분마다 데이터를 보내야 하며, 그렇지 않으면 연결 시간이 초과됩니다. 이러한 현상은 특히 데이터를 수신 대기하는 모니터 API 스크립트에서 일반적으로 발생합니다. 시간 초과를 방지하려면 서버에 대한 세션 연결을 유지하기 위해 스크립트가 10분마다 XMPP ping을 전송하도록 설정합니다.

### <a name="see-also"></a>참고 항목
* [API 설명서][Link 4]
* [XMPP 프로토콜 정보](http://xmpp.org/extensions/xep-0199.html)

## <a name="unable-to-use-the-api-to-perform-the-same-action-available-in-the-azure-mobile-engagement-ui"></a>Azure Mobile Engagement UI에서 수행 가능한 것과 같은 작업을 API를 사용하여 수행할 수 없음
### <a name="issue"></a>문제
* Azure Mobile Engagement UI에서 수행할 수 있는 작업을 관련 Azure Mobile Engagement API에서는 수행할 수 없습니다.

### <a name="causes"></a>원인
* Azure Mobile Engagement UI에서 같은 작업을 수행할 수 있으면 Azure Mobile Engagement의 이 기능이 SDK와 올바르게 통합된 것입니다.

### <a name="see-also"></a>참고 항목
* [UI 설명서][Link 1]

## <a name="error-messages"></a>오류 메시지
### <a name="issue"></a>문제
* API 사용 시 런타임에 또는 로그에 오류 코드가 표시됩니다.

### <a name="causes"></a>원인
* 아래에는 참조 및 임시 문제 해결에 사용할 수 있도록 일반적인 API 상태 코드 번호의 통합 목록이 나와 있습니다.
  
        200        Success.
        200        Account updated: device registered, associated, updated, or removed from the current account.
        200        Returns a list of projects as a JSON object or an authentication token generated and returned in the response’s body.
        201        Account created.
        400        Invalid parameter or validation exception (check payload for details). The parameters provided to the API or service are invalid. In this case, the HTTP response will embed more details. Make sure to test for the MIME type of the response as the payload can either be plain text or a JSON object.
        401        Authentication error. No user is currently authenticated or connected (check the AppID and SDK key).
        402        Billing lock. The application is either off its quotas or is currently in a bad billing state.
        403        The application is not enabled or the specific API is disabled for this application.
        403        Unauthorized access to the project or application, invalid access key (the key must match the one provided when created).
        403        Campaign specific errors: campaign must be finished (or has already been activated), the suspend action can only be performed on an scheduled campaign, cannot finish a campaign that is not currently “in progress”, campaign must be “in progress” and the campaign’s property named, manual Push must be set to true.
        403        The email address is already associated to another account (a super user for instance). No authentication token will be generated.
        404        Application, device, campaign, or project identifier not found.
        404        Query parameter is invalid JSON or has a field with an unexpected value.
        404        The email address is not associated with an account. Please create or update the account first.
        405        Invalid HTTP method (GET, POST, etc.) or trying to edit a read only segment (i.e. add or update or delete a criterion). A segment becomes read only after it has been computed for the first time.
        409        Name already associated to a different device ID or campaign.
        413        Too many device identifiers (current limit is 1,000), POST URL encoded entity is over 2MB, or the period is too large to be displayed (the server didn’t retrieve the analytics because the user request is for a period that is too large).
        503        Analytics not available yet (the requested information is not computed yet for an application).
        504        The server was not able to handle your request in a reasonable time (if you make multiple calls to an API very quickly, try to make one call at a time and spread the calls out over time).

### <a name="see-also"></a>참고 항목
* [API 설명서 - 각 특정 API에 대한 상세 오류 정보][Link 4]

## <a name="silent-failures"></a>자동 오류
### <a name="issue"></a>문제
* API 작업이 실패하고 런타임이나 로그에 오류 메시지가 표시되지 않습니다.

### <a name="causes"></a>원인
* 대부분의 항목은 올바르게 통합되지 않은 경우 Azure Mobile Engagement UI에서 사용되지 않도록 설정되지만 API에서는 자동으로 오류가 발생하므로 UI의 같은 기능을 테스트하여 작동하는지 확인해야 합니다.
* Azure Mobile Engagement 및 Azure Mobile Engagement에서 사용하려는 대다수의 고급 기능은 별도의 단계를 통해 SDK와 함께 앱에 개별적으로 통합해야 사용 가능합니다.

### <a name="see-also"></a>참고 항목
* [문제 해결 가이드 - SDK][Link 25]

<!--Link references-->
[Link 1]: mobile-engagement-user-interface-home.md
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

