---
title: Azure Mobile Engagement 사용자 인터페이스 - 홈
description: Azure Mobile Engagement를 사용하여 기존 응용 프로그램과 프로젝트를 관리하는 방법을 알아봅니다.
services: mobile-engagement
documentationcenter: ''
author: piyushjo
manager: erikre
editor: ''
ms.assetid: aff578d2-40f6-43e4-b0ea-7d2674cb28a1
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: c0379e51a30927af88367fc687d8f8ce66eb827f
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2018
---
# <a name="how-to-manage-your-existing-application-and-projects"></a>기존 응용 프로그램 및 프로젝트를 관리하는 방법
> [!IMPORTANT]
> Azure Mobile Engagement는 2018/3/31에 사용이 중지됩니다. 이 페이지는 이후에 삭제됩니다.
> 

이 문서에서는 **Mobile Engagement** 포털의 **홈** 페이지를 설명합니다. **Mobile Engagement** 포털을 사용하여 모바일 앱을 모니터링하고 관리합니다. 포털 사용을 시작하려면 먼저 **Azure Mobile Engagement** 계정을 만들어야 합니다. 

홈페이지로 이동하려면 페이지의 왼쪽 위에서 **홈** 을 클릭합니다. 선택한 컬렉션에 속하는 모든 응용 프로그램 목록이 있습니다. 이 페이지에서는 응용 프로그램의 빠른 개요만 참조합니다.

또한 홈페이지에는 계정의 모든 응용 프로그램을 포함할 수 있는 모든 프로젝트가 있습니다. 누구나 계정을 만들면 UI의 홈페이지에 액세스할 수 있지만 다른 사용자들이 **내 프로젝트**의 사용자 지정 응용 프로그램에 액세스하도록 하려면 해당 사용자에게 권한을 부여해야 합니다.

선택한 응용 프로그램에 대한 비교 차트를 볼 수도 있습니다. 또한 프로젝트에서 선택한 응용 프로그램에 대한 비교 차트를 보려면 선택합니다.

![Home1][0]

## <a name="my-applications"></a>내 응용 프로그램
응용 프로그램에 대한 간략한 개요에서 상세한 리본 옵션을 확인하기 위해 열 응용 프로그램을 선택할 수 있습니다. 응용 프로그램 이름을 클릭하면 응용 프로그램에서 가장 자주 방문하는 리본 위치가 반환되며, 기어 아이콘을 클릭하면 응용 프로그램의 "설정" 페이지로 직접 이동할 수 있습니다. 응용 프로그램 테이블에 표시되는 정보를 검색, 필터링 또는 정렬할 수 있습니다. 열 머리글을 끌어서 놓는 방법으로 순서를 변경할 수도 있습니다.

무엇보다도 응용 프로그램 개요에는 다음 항목이 포함됩니다.

* **새 사용자 추세**: 지난 2주 동안의 새 사용자 증가 수
* **활성 사용자**: 지난 30일 동안의 활성 사용자 수
* **활성 사용자 추세**: 지난 2주 동안의 활성 사용자 증가 수
* **세션**: 세션은 사용자가 응용 프로그램 사용을 시작한 시간부터 중지할 때까지 한 번 사용하는 것
* **세션 추세**: 지난 2주 동안의 세션 증가 수

응용 프로그램을 클릭하면 UI를 통해 앱 모니터링 및 관리를 시작할 수 있습니다. 예:     

* [응용 프로그램에 대한 실시간 데이터 모니터링](mobile-engagement-user-interface-monitor.md)
* [응용 프로그램에 대한 기록 데이터 분석](mobile-engagement-user-interface-analytics.md)
* [사용자의 세그먼트를 만들고 관리하여 사용 패턴 식별](mobile-engagement-user-interface-segments.md)
* [푸시 알림으로 응용 프로그램의 사용자에게 알림](mobile-engagement-user-interface-reach.md)

## <a name="my-projects"></a>내 프로젝트
프로젝트를 사용하여 응용 프로그램을 그룹화하고 다른 사용자에게 응용 프로그램에 액세스할 수 있는 권한을 부여할 수 있습니다. 메일 주소를 제공하여 다른 사용자에게 권한을 부여합니다. **새 프로젝트** 단추를 사용하면 새 프로젝트의 "이름"과 "설명"만 입력하여 새 프로젝트를 만들 수 있습니다. 프로젝트를 만든 후에는 프로젝트 이름을 클릭하여 제품 이름과 설명을 편집하고 이 프로젝트에 표시할 모든 응용 프로그램을 선택할 수 있습니다.

![Home6][60]

역할의 종류

* **보기 권한자**: 프로젝트에 연결된 응용 프로그램을 볼 수만 있는 사용자입니다. 보기 권한자는 분석 및 모니터 데이터에 액세스하고 도달률 결과를 확인할 수 있습니다. 그러나 정보를 변경하거나 응용 프로그램/사용자를 관리할 수는 없습니다. 또한 도달률 캠페인을 만들거나 해당 상태를 변경할 수도 없습니다.
* **개발자**: 보기 권한자가 수행할 수 있는 모든 작업을 수행할 수 있으며 응용 프로그램을 관리할 수도 있는 사용자입니다. 개발자는 응용 프로그램을 사용하거나 사용하지 않도록 설정하고, 패키지/서명 등의 응용 프로그램 정보를 변경하고, 도달률 캠페인을 만들 수 있습니다. 그러나 사용자를 관리할 수는 없습니다.
* **관리자**: 개발자가 수행할 수 있는 모든 작업을 수행할 수 있으며 사용자를 관리할 수도 있는 사용자입니다. 관리자는 사용자를 프로젝트에 참가하도록 초대하고 사용자 역할을 변경하며 프로젝트 정보를 변경할 수 있습니다. "설정"에서 응용 프로그램 수준 권한을 설정할 수도 있습니다.

이 프로젝트에 속하는 모든 응용 프로그램을 보려면 프로젝트를 클릭합니다. 다음 이미지에서는 선택한 응용 프로그램에 대한 비교 차트를 보여 줍니다.

![Home2][3]

## <a name="see-also"></a>참고 항목
* [개념][Link 6]
* [문제 해결 가이드 서비스][Link 24]

<!--Image references-->
[0]: ./media/mobile-engagement-user-interface-home/home0.png
[1]: ./media/mobile-engagement-user-interface-navigation/navigation1.png
[2]: ./media/mobile-engagement-user-interface-home/home1.png
[3]: ./media/mobile-engagement-user-interface-home/home2.png
[4]: ./media/mobile-engagement-user-interface-home/home3.png
[5]: ./media/mobile-engagement-user-interface-home/home4.png
[6]: ./media/mobile-engagement-user-interface-home/home5.png
[60]: ./media/mobile-engagement-user-interface-home/home6.png
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
