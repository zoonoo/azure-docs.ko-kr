---
title: Azure Mobile Engagement 사용자 인터페이스 - 세그먼트
description: Azure Mobile Engagement를 사용하여 사용자의 세그먼트를 만들고 관리하여 사용 패턴을 식별하는 방법 알아보기
services: mobile-engagement
documentationcenter: ''
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 6a4f2205-4a3c-406e-a04f-5e6f2a36653f
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 53c5b1b672a0d7212a3d298c3e411a9cc6ac0807
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2018
---
# <a name="how-to-create-and-manage-segments-of-users-to-identify-usage-patterns"></a>사용자의 세그먼트를 만들고 관리하여 사용 패턴을 식별하는 방법
> [!IMPORTANT]
> Azure Mobile Engagement는 2018/3/31에 사용이 중지됩니다. 이 페이지는 이후에 삭제됩니다.
> 

이 문서에서는 **Mobile Engagement** 포털의 **세그먼트** 탭을 설명합니다. **Mobile Engagement** 포털을 사용하여 모바일 앱을 모니터링하고 관리합니다.

UI의 세그먼트 섹션에서는 응용 프로그램에서 확인할 수 있으며 세그먼트 API를 통해서도 액세스할 수 있는 다양한 동작 및 분석 정보에 따라 사용자 구분 작업을 수행할 수 있습니다. 세그먼트는 작성된 지 24시간 후에 먼저 계산된 다음 최신 분석 정보를 기준으로 24시간마다 다시 계산됩니다. 계산된 세그먼트는 매일 "일 단위 기록" 차트에 표시됩니다.

> [!NOTE]
> **Mobile Engagement** 포털 UI의 여러 섹션에는 **도움말 표시** 단추가 포함되어 있습니다. 섹션에 대해 더 자세한 문맥 정보를 보려면 이 단추를 누릅니다.
> 
> 

## <a name="create-segments"></a>세그먼트 만들기
분석 섹션에서 지난 60일까지의 특정 기간에 대해 최대 10개의 기준에 따라 세그먼트를 만들 수 있습니다. 예를 들어 지난 10일 이내에 앱 내에서 특정 콘텐츠를 검색했거나 특정 페이지를 본 사용자를 기준으로 세그먼트를 만들 수 있습니다. 이 정보는 분석 섹션에서 제공됩니다. 따라서 이 정보를 사용해 세그먼트를 만든 다음 해당 특정 사용자 하위 집합을 대상으로 응용 프로그램을 다시 사용하도록 하는 푸시 알림을 설정할 수 있습니다. 

> [!NOTE]
> 계산된 세그먼트는 편집할 수 없으며 복제(복사) 또는 소멸(삭제)만 가능합니다. 세그먼트는 AppID가 같은 동일 응용 프로그램 내에서 복제할 수도 있고 AppID가 다른 타 응용 프로그램에 복제할 수도 있습니다. 

 ![segments1][35] 

## <a name="examples-segments"></a>예제 세그먼트
 ![segments2][36]

세그먼트를 사용하면 응용 프로그램의 최종 사용자를 구분할 수 있습니다.
사용자 구분은 중요한 마케팅 전략입니다. Azure Mobile Engagement에서는 기록 데이터를 가져와 사용자 지정 세그먼트를 만들 수 있습니다. 이 유용한 도구를 통해 응용 프로그램 내의 고객 작업 환경을 파악할 수 있습니다. 또한 세그먼트는 쉽게 분석이 가능하고 푸시 대상으로 사용할 수 있습니다.
일반적인 사용 사례로는 최종 사용자가 스토어에서 응용 프로그램을 평가하도록 푸시 알림을 보내는 경우가 있습니다. 이때 모든 최종 사용자에게 알림을 보내는 대신 지난 달에 매일 응용 프로그램을 사용했으며 사용자 환경에 만족했던 사용자만 지정하는 세그먼트를 만들 수 있습니다. 이처럼 대상이 자세하게 지정된 더 적은 수의 푸시 알림을 보내면 ROI가 높아집니다.

 ![segments3][37]

### <a name="segments-you-can-create-based-on-the-major-azure-mobile-engagement-elements"></a>주요 Azure Mobile Engagement 요소를 기준으로 만들 수 있는 세그먼트
* 이벤트: 매주 2회 넘게 발생하는 응용 프로그램의 특정 단일 이벤트를 대상으로 지정하는 세그먼트를 만듭니다. 
* 세션: 지난 주에 5회 넘게 응용 프로그램을 사용한 사용자의 세그먼트를 만듭니다.
* 활동: 지난 달에 특정 페이지나 콘텐츠를 사용한 횟수가 10회 정도인 사용자의 세그먼트를 만듭니다.
* 작업: 매일 2회 넘게 작업을 완료한 사용자의 세그먼트를 만듭니다.
* 작동 중단: 지난 주에 작동 중단이 10회를 초과하여 발생한 모든 사용자의 세그먼트를 만듭니다. 이 세그먼트에 대해 사과 메시지나 쿠폰을 푸시로 보낼 수도 있습니다.
* 오류: 지난 3일 동안 오류가 100회를 초과하여 발생한 모든 사용자의 세그먼트를 만듭니다.
* 앱 정보: 지난 25일 동안 생성된 사용자 지정 앱 정보를 대상으로 하는 세그먼트를 만듭니다.
  
  ![segments4][38]

세그먼트를 가장 적절하게 사용하려면 "앱 정보" 태그의 태깅 계획이 포함된 SDK를 응용 프로그램에 사용자 지정된 방식으로 통합해야 합니다.
그런 다음 인터페이스 홈 페이지에서 원하는 응용 프로그램을 선택하고 "세그먼트" 섹션을 클릭합니다.

1. "세그먼트" 섹션을 선택합니다.
2. "새 세그먼트" 단추를 클릭하여 새 세그먼트를 만듭니다.

## <a name="real-life-example-create-a-simple-segment-based-on-session-information"></a>실제 예: "세션" 정보를 기준으로 간단한 세그먼트 만들기
지난 주에 앱을 사용한 횟수가 최소 50회 이상인 모든 최종 사용자의 세그먼트를 만듭니다. 그런 다음 세션당 앱 사용 시간이 최소 30초 이상인 최종 사용자만 찾습니다. 그러면 앱 사용 환경이 긍정적이었던 모든 최종 사용자가 표시됩니다. 이렇게 만든 세그먼트를 사용하여 해당 최종 사용자에게 스토어에서 앱을 평가해 줄 것을 요청하는 푸시 알림을 보낼 수 있습니다.

 ![segments5][39]

1. 세그먼트를 "세그먼트" 목록에서 빠르게 찾을 수 있도록 세그먼트 이름을 지정합니다.
2. "만들기" 단추를 클릭합니다.
   
   ![segments6][40]

세션을 선택합니다.

 ![segments7][41]

1. 기간을 "지난 주"로 선택합니다.
2. 다음을 클릭합니다.
   
   ![segments8][42]
3. 목록에서 적절한 연산자(=; ≥, ≤)를 선택합니다.
4. 원하는 횟수를 입력합니다.
5. 원하는 발생 수를 선택합니다. 
6. 다음을 클릭합니다.
   그러면 지난 주에 세션을 최소 50개 이상 만든 사용자와 일치하는 예제가 설정됩니다.
   
   ![segments9][43]

세션 구분 시에는 세션당 길이를 기준으로 선택할 수 있습니다.

1. 목록에서 연산자를 선택합니다.
2. 세션당 길이를 입력합니다.
3. 다음을 클릭합니다.
   이 예제에서는 발생 섹션에서 구분한 모든 세션에 대해 세션당 사용 시간이 30초를 넘은 사용자만 선택하도록 지정합니다.
   
   ![segments10][44]

전체 깔때기에서 검색할 수 있도록 기준 이름을 지정하고 마침을 클릭합니다.

 ![segments11][45]

기준 설정을 완료하면 세그먼트 깔때기에 해당 기준이 표시됩니다.
세그먼트는 분석 데이터를 기준으로 하므로 매일 한 번씩 계산됩니다.
이 예제에서는 전체 최종 사용자 중 47.7%가 해당 기준에 일치합니다. 이러한 사용자는 응용 프로그램 환경에 만족했으므로 스토어에서 앱을 평가해 달라는 푸시 알림을 보내면 더 높은 등급을 선택할 가능성이 높습니다.

## <a name="see-also"></a>참고 항목
* [개념][Link 6]
* [문제 해결 가이드 서비스][Link 24]

<!--Image references-->
[1]: ./media/mobile-engagement-user-interface-navigation/navigation1.png
[2]: ./media/mobile-engagement-user-interface-home/home1.png
[3]: ./media/mobile-engagement-user-interface-home/home2.png
[4]: ./media/mobile-engagement-user-interface-home/home3.png
[5]: ./media/mobile-engagement-user-interface-home/home4.png
[6]: ./media/mobile-engagement-user-interface-home/home5.png
[7]: ./media/mobile-engagement-user-interface-my-account/myaccount1.png
[8]: ./media/mobile-engagement-user-interface-my-account/myaccount2.png
[9]: ./media/mobile-engagement-user-interface-my-account/myaccount3.png
[10]: ./media/mobile-engagement-user-interface-analytics/analytics1.png
[11]: ./media/mobile-engagement-user-interface-analytics/analytics2.png
[12]: ./media/mobile-engagement-user-interface-analytics/analytics3.png
[13]: ./media/mobile-engagement-user-interface-analytics/analytics4.png
[14]: ./media/mobile-engagement-user-interface-monitor/monitor1.png
[15]: ./media/mobile-engagement-user-interface-monitor/monitor2.png
[16]: ./media/mobile-engagement-user-interface-monitor/monitor3.png
[17]: ./media/mobile-engagement-user-interface-monitor/monitor4.png
[18]: ./media/mobile-engagement-user-interface-reach/reach1.png
[19]: ./media/mobile-engagement-user-interface-reach/reach2.png
[20]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign1.png
[21]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign2.png
[22]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign3.png
[23]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign4.png
[24]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign5.png
[25]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign6.png
[26]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign7.png
[27]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign8.png
[28]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign9.png
[29]: ./media/mobile-engagement-user-interface-reach-criterion/Reach-Criterion1.png
[30]: ./media/mobile-engagement-user-interface-reach-content/Reach-Content1.png
[31]: ./media/mobile-engagement-user-interface-reach-content/Reach-Content2.png
[32]: ./media/mobile-engagement-user-interface-reach-content/Reach-Content3.png
[33]: ./media/mobile-engagement-user-interface-reach-content/Reach-Content4.png
[34]: ./media/mobile-engagement-user-interface-dashboard/dashboard1.png
[35]: ./media/mobile-engagement-user-interface-segments/segments1.png
[36]: ./media/mobile-engagement-user-interface-segments/segments2.png
[37]: ./media/mobile-engagement-user-interface-segments/segments3.png
[38]: ./media/mobile-engagement-user-interface-segments/segments4.png
[39]: ./media/mobile-engagement-user-interface-segments/segments5.png
[40]: ./media/mobile-engagement-user-interface-segments/segments6.png
[41]: ./media/mobile-engagement-user-interface-segments/segments7.png
[42]: ./media/mobile-engagement-user-interface-segments/segments8.png
[43]: ./media/mobile-engagement-user-interface-segments/segments9.png
[44]: ./media/mobile-engagement-user-interface-segments/segments10.png
[45]: ./media/mobile-engagement-user-interface-segments/segments11.png
[46]: ./media/mobile-engagement-user-interface-settings/settings1.png
[47]: ./media/mobile-engagement-user-interface-settings/settings2.png
[48]: ./media/mobile-engagement-user-interface-settings/settings3.png
[49]: ./media/mobile-engagement-user-interface-settings/settings4.png
[50]: ./media/mobile-engagement-user-interface-settings/settings5.png
[51]: ./media/mobile-engagement-user-interface-settings/settings6.png
[52]: ./media/mobile-engagement-user-interface-settings/settings7.png
[53]: ./media/mobile-engagement-user-interface-settings/settings8.png
[54]: ./media/mobile-engagement-user-interface-settings/settings9.png
[55]: ./media/mobile-engagement-user-interface-settings/settings10.png
[56]: ./media/mobile-engagement-user-interface-settings/settings11.png
[57]: ./media/mobile-engagement-user-interface-settings/settings12.png
[58]: ./media/mobile-engagement-user-interface-settings/settings13.png

<!--Link references-->
[Link 1]: mobile-engagement-user-interface.md
[Link 2]: mobile-engagement-troubleshooting-guide.md
[Link 3]: mobile-engagement-how-tos.md
[Link 4]: http://go.microsoft.com/fwlink/?LinkID=525553
[Link 5]: http://go.microsoft.com/fwlink/?LinkID=525554
[Link 6]: http://go.microsoft.com/fwlink/?LinkId=525555
[Link 7]: https://account.windowsazure.com/PreviewFeatures
[Link 8]: https://social.msdn.microsoft.com/Forums/azure/home?forum=azuremobileengagement
[Link 9]: http://azure.microsoft.com/services/mobile-engagement/
[Link 10]: http://azure.microsoft.com/documentation/services/mobile-engagement/
[Link 11]: http://azure.microsoft.com/pricing/details/mobile-engagement/
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
[Link 27]: ../mobile-engagement-how-tos-first-push.md
[Link 28]: ../mobile-engagement-how-tos-test-campaign.md
[Link 29]: ../mobile-engagement-how-tos-personalize-push.md
[Link 30]: ../mobile-engagement-how-tos-differentiate-push.md
[Link 31]: ../mobile-engagement-how-tos-schedule-campaign.md
[Link 32]: ../mobile-engagement-how-tos-text-view.md
[Link 33]: ../mobile-engagement-how-tos-web-view.md

