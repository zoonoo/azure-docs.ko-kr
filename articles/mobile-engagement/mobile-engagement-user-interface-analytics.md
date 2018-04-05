---
title: Azure Mobile Engagement 사용자 인터페이스 - 분석
description: Azure Mobile Engagement를 사용하여 응용 프로그램에 대한 기록 데이터를 분석하는 방법 알아보기
services: mobile-engagement
documentationcenter: ''
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 6b2533ac-b8ec-4e35-872c-d563895bdc0c
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: fceae1ffff40fc525170121181e21726fe2bd3f7
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2018
---
# <a name="how-to-analyze-historical-data-about-your-application"></a>응용 프로그램에 대한 기록 데이터를 분석하는 방법
> [!IMPORTANT]
> Azure Mobile Engagement는 2018/3/31에 사용이 중지됩니다. 이 페이지는 이후에 삭제됩니다.
> 

이 문서에서는 **Mobile Engagement** 포털의 **분석** 탭을 설명합니다. **Mobile Engagement** 포털을 사용하여 모바일 앱을 모니터링하고 관리합니다. 포털 사용을 시작하려면 먼저 **Azure Mobile Engagement** 계정을 만들어야 합니다.

UI의 분석 섹션에서는 24시간마다 업데이트되는 기록 데이터에 따라 응용 프로그램에 대한 집계 정보를 제공합니다. 정보는 꺾은선형/막대형/원형 차트, 표, 맵으로 구성된 여러 대시보드에 표시됩니다. 데이터를 .csv 파일로 다운로드할 수도 있습니다. 이와 동일한 대부분의 정보는 UI의 모니터 섹션에서도 실시간으로 제공되며 분석 API에서 액세스할 수도 있습니다.

> [!NOTE]
> **Mobile Engagement** 포털 UI의 여러 섹션에는 **도움말 표시** 단추가 포함되어 있습니다. 섹션에 대해 더 자세한 문맥 정보를 보려면 이 단추를 누릅니다.

## <a name="standard-and-custom-analytics"></a>표준 분석 및 사용자 지정 분석
Azure Mobile Engagement에서는 앱을 SDK와 통합하는 즉시 그래프로 작성할 수 있는 응용 프로그램에 대한 기본적인 표준 분석 정보 집합을 제공합니다. 또한 최종 사용자 동작에 대해 원하는 추가 사용자 지정 분석 정보를 수집하는 기능도 제공합니다. 이러한 정보를 수집하려면 Azure Mobile Engagement에서 해당 추가 데이터를 자동으로 수집할 수 있도록 사용자 지정 "태그(앱 정보)"를 사용하는 태그 계획을 **설정** 에서 작성하면 됩니다.

## <a name="analytics"></a>분석
* 대시보드: 새 사용자와 활성 사용자 및 해당 추세에 대한 일반 정보가 표시됩니다.
* 사용자: 장치 식별자를 통해 사용자를 식별합니다. 이 식별자는 각 장치에서 고유합니다. 각각의 새 사용자는 새 장치 하나에 해당합니다. 사용자는 지정된 시간 간격 동안 첫 번째 세션을 수행하는 경우 새 사용자로 간주됩니다. 그리고 지난 7일 동안 세션을 하나 이상 수행한 경우 재방문 사용자로 간주됩니다. 활성 사용자는 지정된 기간 동안 세션을 하나 이상 만든 사용자입니다. 기간(월/주/일/시간)을 기준으로 정렬할 수 있습니다. 모든 차트는 비슷하게 표시되지만 응용 프로그램 버전 등 서로 다른 기능별로 필터링한 다음 기간별로 정렬할 수 있도록 합니다. SDK를 통합하여 수집할 수 있는 표준 정보는 활성 사용자, 새 사용자, 세션 수, 각 세션의 길이, 국가/지역/위치 관련 기술 정보, 고객이 사용하는 해당 언어의 통신 회사, 장치, 펌웨어, 네트워크(Wi-Fi), 앱/SDK 버전 등이 있습니다. 모니터 섹션에서 이 정보를 실시간으로 볼 수 있습니다.

> [!NOTE]
> 기간은 사용자 장치 설정의 날짜를 기준으로 하므로 휴대폰에 날짜가 잘못 설정된 사용자의 경우 잘못된 기간의 데이터가 표시됩니다.

* 재방문 주기: 지정된 시간 간격 동안 첫 번째 세션을 수행한 사용자는 재방문 사용자로 간주됩니다. 재방문 사용자와 새 사용자를 계산하는 시간 간격을 시간/일/주/월 단위로 변경할 수 있습니다. 사용자 재방문 주기 분석 기능은 집단을 기반으로 작성되었습니다. 집단이란 지정된 기간 동안 검색된 모든 새 사용자 집합, 즉 이 기간 동안 첫 번째 세션을 수행하는 사용자 집합입니다. Azure Mobile Engagement에서는 1일/2일/4일/7일/1개월에 해당하는 집단을 사용합니다. 특정 집단에서 Azure Mobile Engagement는 1일/2일/4일/7일/1개월마다 해당 집단에 속하면서 아직 활성 상태인 모든 사용자 집합, 즉 지정된 기간 동안 세션을 하나 이상 수행한 사용자 집합을 계산합니다. 이 사용자 집합을 집단 버전이라고 합니다. Azure Mobile Engagement에서는 앱을 계속 사용 중인 사용자의 수를 표시할 수 있지만 앱을 제거한 사용자 수는 GooglePlay, iTunes, Windows 스토어 등의 플랫폼별 스토어를 통해서만 확인할 수 있습니다.
* 세션: 사용자가 응용 프로그램을 한 번 사용하는 것입니다. 세션은 사용자가 수행하는 일련의 활동에서 생성됩니다. 활동은 대개 응용 프로그램의 단일 화면 사용과 연결되지만, SDK가 응용 프로그램에 통합된 방식에 따라 달라질 수 있습니다. 사용자는 한 번에 하나의 활동만 수행할 수 있습니다. 세션은 사용자가 첫 번째 활동을 시작하는 즉시 시작되며 마지막 활동을 마치면 중지됩니다. 사용자가 아무런 활동을 수행하지 않고 몇 초가 지나면 활동 시퀀스가 고유 세션 두 개로 분할됩니다.
* 활동: 응용 프로그램의 각 화면 이름과 사용자가 각 화면에서 머무는 시간입니다. 활동은 앱에 대해 설정한 "앱 정보" 태그에 해당하는 사용자 지정 분석 옵션입니다.
* 사용자 경로: 사용자가 응용 프로그램 작업(화면)을 탐색하는 방식이 표시됩니다. 슬라이더를 이동하여 세부 정보 수준을 조정할 수 있습니다. 파란색 노드는 응용 프로그램의 활동을 나타냅니다. 이 노드의 크기는 사용자가 해당 활동에 사용한 시간과 비례합니다. 흰색 노드는 세션 시작 및 중지를 나타냅니다. 빨간색 노드는 작동 중단을 나타냅니다. 링크는 응용 프로그램 활동 간/활동과 작동 중단 간의 전환을 나타냅니다. 노드나 링크를 클릭하면 데이터에 대한 추가 정보가 포함된 도구 설명이 표시됩니다. 이러한 추가 정보로는 특정 화면에서 머문 시간, 전환 횟수, 원본 활동에서 대상 활동으로 전환한 비율 등이 있습니다. 예를 들어 A ---60% ---> B는 활동 A를 수행하는 사용자가 활동 B로 전환한 비율이 60%라는 의미입니다. 그래프의 정보를 명확하게 파악하기 위해 그래프를 다시 구성할 수 있습니다. 그래프를 변경할 때마다 해당 위치가 저장됩니다. 작동 중단 정보는 표시할 수도 있고 그래프를 간소화하기 위해 숨길 수도 있습니다.
* 이벤트: 응용 프로그램에서 사용자가 수행한 특정 작업입니다. 이벤트의 분포는 세션당 사용자별 이벤트 수로 표시 됩니다. 이벤트는 단추 클릭, 알림 수신 등의 즉각적인 작업을 나타냅니다. 이벤트의 의미는 SDK가 응용 프로그램에 통합된 방식에 따라 달라집니다. 이벤트는 세션이나 작업 중에 발생할 수도 있고 독립 실행형으로 발생할 수도 있습니다.
* 작업: 이벤트와 비슷하지만 동작의 지속 시간에 보다 집중합니다. 예를 들어 작업을 보면 콘텐츠를 로드하거나 웹 서비스를 호출하는 데 걸리는 시간에 대한 기술 정보를 확인할 수 있습니다. 또한 사용자가 양식을 작성하거나 계정을 만들거나 구매를 진행하는 데 소요되는 시간도 파악할 수 있습니다. 작업은 다운로드 태스크에 소요되는 시간, 배너가 화면에 표시되는 시간 등 특정 태스크를 수행하는 시간입니다. 작업의 의미는 SDK가 응용 프로그램에 통합된 방식에 따라 달라집니다. 작업은 대개 세션 범위 외부에서 수행되므로 사용자 활동은 없는 백그라운드 태스크와 연결됩니다.
* 기술 정보: 추적 가능한 앱 사용자의 장치에 대한 기술 정보입니다. 사용자 장치의 로캘, 통신 회사, 네트워크, 장치, 펌웨어, 화면 크기, 앱 버전, 앱에서 사용되는 SDK 버전 등이 있습니다.
* 오류: 응용 프로그램 작동을 중단시키지는 않는 응용 프로그램 내의 기술적 오류에 대한 정보입니다. 오류는 네트워크 문제, 잘못된 조작 등의 즉각적인 문제를 나타냅니다. 이벤트의 의미는 SDK가 응용 프로그램에 통합된 방식에 따라 달라집니다. 오류는 세션이나 작업 중에 발생할 수도 있고 독립 실행형으로 발생할 수도 있습니다.
* 작동 중단: 응용 프로그램 작동을 중단시킨 오류에 대한 정보입니다. 작동 중단은 응용 프로그램이 정상적인 기능을 수행하지 못해 사용을 중지해야 하는 예기치 않은 상황이며, 대개 응용 프로그램의 버그로 인해 발생합니다.

![Analytics2][11]

## <a name="accessing-the-retention-overview"></a>재방문 주기 개요 액세스
![Analytics3][12]

재방문 주기 개요는 가운데 화면에서 7개의 카드로 구분되며 각 카드에는 특정 재방문 기간에 대한 개요가 표시됩니다. 예제에는 재방문 기간 2일이 표시되어 있습니다. 다른 카드에는 4일 및 7일의 재방문 기간이 나와 있습니다.

## <a name="understanding-the-retention-overview-cards"></a>재방문 주기 개요 카드 이해
![Analytics4][13]

### <a name="each-card-is-composed-of-3-main-parts"></a>각 카드는 3개의 주요 부분으로 구성됩니다.
1. 1: 고려 대상 집단 및 기간
2. 2-4: 현재 기간의 재방문 주기
3. 5: 기록의 스파크라인

### <a name="here-is-detailed-information-about-each-element"></a>아래에서 각 요소에 대해 자세히 설명합니다.
1. 집단 및 기간: 집단 유형을 설명하는 헤드라인입니다. 여기서 "2일 기간"은 2일 동안의 사용자 동작, 2일 동안 유입된 사용자 및 다음 2일의 블록 기간에 해당 사용자의 앱 연결 여부를 확인한다는 의미입니다. 위의 예제에서는 11월 21~22일 동안의 사용자 활동을 고려합니다.
2. 이 카드는 11월 19~20일에 유입된 사용자에 대한 11월 21~22일의 재방문 비율을 보여 줍니다. 위에 나와 있는 것처럼 19~20일의 새 사용자 3명 중에 21~22일의 활성 사용자는 1명입니다.
3. 시각적 표시기에는 위에 나와 있는 것과 같은 정보가 그래픽으로 표시됩니다. 사용자의 비율이 33%이므로 원의 1/3이 색으로 표시됩니다. 색도 추가 정보를 제공합니다. 녹색은 이전 계산보다 값이 증가했음을 나타냅니다. 노란색은 값이 이전과 동일하며 빨간색은 값이 감소했다는 의미입니다.
4. 계산에 사용된 값을 나타냅니다.
5. 재방문 값 기록의 스파크라인입니다. 이 스파크라인을 통해 이전의 값을 확인하여 값의 변화를 전체적으로 파악할 수 있습니다.

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
