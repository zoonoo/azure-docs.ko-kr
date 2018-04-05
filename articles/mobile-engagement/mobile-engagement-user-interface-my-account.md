---
title: Azure Mobile Engagement 사용자 인터페이스 - 내 계정
description: Azure Mobile Engagement를 사용하여 계정 프로필 및 테스트 장치를 관리하는 방법을 알아봅니다
services: mobile-engagement
documentationcenter: ''
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 22832678-3959-4b8c-9fb2-f2ff5974e5d1
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 89ef4f97a03ab0334f5474fb5b0ad2fd44f23518
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2018
---
# <a name="how-to-manage-your-account-profile-and-test-devices"></a>계정 프로필 및 테스트 장치를 관리하는 방법
> [!IMPORTANT]
> Azure Mobile Engagement는 2018/3/31에 사용이 중지됩니다. 이 페이지는 이후에 삭제됩니다.
> 

이 문서에서는 **Mobile Engagement** 포털의 **홈** 페이지를 설명합니다. **Mobile Engagement** 포털을 사용하여 모바일 앱을 모니터링하고 관리합니다. 

**내 계정** 페이지를 보려면 페이지 위쪽의 계정을 클릭합니다.

UI의 내 계정 섹션에서는 프로필 설정, 테스트 장치 ID 등 계정과 연결된 설정을 확인하고 변경할 수 있습니다. 이러한 설정에는 장치 API를 통해서도 액세스할 수 있는 항목이 포함됩니다.

![MyAccount1][7]  

## <a name="profile"></a>프로필
계정 설정을 확인하거나 변경할 수 있습니다. [홈](mobile-engagement-user-interface-home.md)의 전자 메일 주소를 기준으로 다른 사용자에게 응용 프로그램 사용 권한을 부여할 수도 있습니다.

![MyAccount2][8]  

## <a name="devices"></a>장치
**도달률** 또는 **푸시** 캠페인을 테스트하는 데 사용할 수 있는 테스트 장치의 ID를 확인, 추가 또는 제거할 수 있습니다. "새 장치"를 클릭하면 iOS, Android, Windows Phone 등의 각 플랫폼에 대해 장치의 ID를 찾는 방법에 대해 상황에 맞는 지침이 표시됩니다. 

![MyAccount3][9]  

푸시 API 또는 장치 API를 사용하려면 사용자의 고유한 장치 식별자(deviceid 매개 변수)를 확인해야 합니다. 이 식별자는 다음과 같은 여러 가지 방법으로 검색할 수 있습니다.

1. 백 엔드에서 장치 API의 "Get" 기능을 사용하여 장치 식별자의 전체 목록을 확인할 수 있습니다.
2. 앱에서 SDK를 사용하여 식별자를 확인할 수 있습니다. Android에서는 Agent 클래스의 getDeviceID() 함수를 호출하고 iOS에서는 Agent 클래스의 deviceid 속성을 확인합니다.
3. 도달률 알림에서 알림과 연결된 작업 URL에 {deviceid} 패턴이 포함되어 있으면 작업을 트리거한 장치의 식별자로 자동 교체됩니다.
   http://<example>.com/registeruser?deviceid={deviceid}&otherparam=myparamdata는 http://<example>.com/registeruser?deviceid=XXXXXXXXXXXXXXXX&otherparam=myparamdata로 바꿉니다. 
4. 도달률 웹 알림에서 알림의 HTML 코드에 {deviceid} 패턴이 포함되어 있으면 웹 알림을 표시하는 장치의 식별자로 자동 교체됩니다.
   예를 들어 {deviceid} 장치 식별자는 다음과 같이 바뀝니다. XXXXXXXXXXXXXXXX
5. 장치에서 응용 프로그램을 열고 태그가 지정된 앱에서 이벤트를 수행합니다.
   "UI - 앱 - 모니터 - 이벤트 - 세부 정보"의 목록에서 수행한 이벤트를 찾습니다.
   모니터에서 이 이벤트를 클릭합니다.
   그러면 해당 이벤트를 수행한 장치 목록에서 장치 ID를 확인할 수 있습니다.
   이 장치 ID를 복사한 다음 "UI - 내 계정 - 장치 - 새 장치 - 장치 플랫폼 선택"에서 등록할 수 있습니다.
   >iOS에 대해 IDFA를 사용하지 않도록 설정된 경우 앱을 제거했다가 다시 설치하면 시간이 지남에 따라 장치 ID가 변경될 수 있습니다.

## <a name="troubleshooting-guide"></a>문제 해결 가이드
* [문제 해결 가이드 - 서비스][Link 24]

## <a name="see-also"></a>참고 항목
* [UI 설명서 - 홈][Link 13]

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




