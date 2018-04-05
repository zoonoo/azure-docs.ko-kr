---
title: Azure Mobile Engagement 사용자 인터페이스 - 도달률
description: 푸시 알림으로 응용 프로그램의 사용자에게 알리는 방법 알아보기
services: mobile-engagement
documentationcenter: ''
author: piyushjo
manager: erikre
editor: ''
ms.assetid: d96e2590-08dd-4481-a352-2c18f26a1643
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: d999b83df7d9d467f08ce8ec72468c738e8acfa5
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2018
---
# <a name="how-to-reach-out-to-the-users-of-your-application-with-push-notifications"></a>푸시 알림으로 응용 프로그램의 사용자에게 알리는 방법
> [!IMPORTANT]
> Azure Mobile Engagement는 2018/3/31에 사용이 중지됩니다. 이 페이지는 이후에 삭제됩니다.
> 

이 문서에서는 **Mobile Engagement** 포털의 **도달률** 탭을 설명합니다. **Mobile Engagement** 포털을 사용하여 모바일 앱을 모니터링하고 관리합니다. 포털 사용을 시작하려면 먼저 **Azure Mobile Engagement** 계정을 만들어야 합니다. 자세한 내용은 [Azure Mobile Engagement 계정 만들기](mobile-engagement-create.md)를 참조하세요.

UI의 도달률 섹션은 푸시 알림 캠페인과 기능을 작성/편집/활성화/완료/모니터링하고 관련 통계를 가져올 수 있는 푸시 캠페인 관리 도구입니다. 하위 수준 푸시 API의 일부 요소와 도달률 API를 통해서도 이러한 캠페인과 기능에 액세스할 수 있습니다. API나 UI 중 어느 쪽을 사용하든 Azure Mobile Engagement 및 도달률을 모두 SDK와 함께 각 플랫폼의 응용 프로그램에 통합해야 도달률 캠페인을 사용할 수 있습니다.

> [!NOTE]
> **Mobile Engagement** 포털 UI의 여러 섹션에는 **도움말 표시** 단추가 포함되어 있습니다. 섹션에 대해 더 자세한 문맥 정보를 보려면 이 단추를 누릅니다.
> 
> 

## <a name="four-types-of-push-notifications"></a>4가지 푸시 알림 유형
1. 알림 - 사용자에게 광고 메시지를 보낼 수 있습니다. 사용자는 해당 메시지를 앱 내의 다른 위치로 리디렉션하거나 웹 페이지로 보내거나 앱 외부에 저장할 수 있습니다. 
2. 설문 조사 - 최종 사용자에게 질문을 하여 정보를 수집할 수 있습니다.
3. 데이터 푸시 - 이진 또는 base64 데이터 파일을 보낼 수 있습니다. 데이터 푸시에 포함된 정보는 앱의 현재 사용자 환경을 수정하기 위해 응용 프로그램으로 전송됩니다. 응용 프로그램은 데이터 푸시의 데이터를 처리할 수 있어야 합니다.

## <a name="campaign-details"></a>캠페인 세부 정보
아직 활성화되지 않은 캠페인의 이름을 가리켜 해당 캠페인을 편집, 복제, 삭제 또는 활성화할 수 있으며 캠페인을 클릭하여 열 수도 있습니다. 이미 활성화된 캠페인의 이름을 가리켜 해당 캠페인을 복제할 수 있으며 캠페인을 클릭하여 열 수도 있습니다. 그러나 활성화된 캠페인은 변경할 수 없습니다.

![Reach1][18]

## <a name="reach-feedback"></a>도달률 피드백
**통계** 를 클릭하여 도달률 캠페인의 세부 정보를 표시합니다. **간단한** 보기는 캠페인이 활성화된 후에 변경된 내용에 대한 열 막대 그래프의 형태로 시각적 표현을 제공합니다. **고급** 보기는 푸시 캠페인에 대한 더 세부적인 정보를 제공합니다. 테스트 캠페인을 보내면 즉, 테스트 장치에 푸시를 전송하면 이러한 세부 정보를 사용할 수 없습니다. 이러한 세부 정보를 해석해야 하는 방법은 다음과 같습니다.

1. **푸시됨** - 장치에 푸시된 메시지 수를 지정합니다. 이 번호는 푸시 캠페인을 만들 동안 지정한 대상 사용자에 따라 달라집니다. 대상 사용자를 지정하지 않으면 이 푸시를 등록된 모든 장치에 전송합니다. 다른 모든 푸시 서비스와 마찬가지로 알림을 장치에 직접 푸시하지 않지만 대신 해당 플랫폼 특정 푸시 알림 서비스(PNS-WNS/GCM/APNS)에 알림을 푸시하므로 장치에 알림을 제공할 수 있습니다. 
2. **전달됨** - PNS에서 장치에 성공적으로 전달되고 Mobile Engagement SDK에서 받은 대로 승인된 메시지의 수를 지정합니다. 
   
   *전송됨 수가 푸시됨 수 보다 작은 이유:*
   
   1. 사용자가 장치에서 앱을 제거하지만 PNS에 푸시를 전송할 때 PNS가 알지 못한 경우 메시지는 삭제됩니다.
   2. 장치에 앱이 있지만 장치 자체가 오랜 시간 동안 오프라인 상태라면 PNS는 장치로 메시지를 전송하는 데 실패합니다. 
   3. 메시지가 장치에 전달되지만 앱의 Mobile Engagement SDK가 메시지의 콘텐츠를 인식하지 못하는 경우 해당 메시지를 삭제합니다. 앱의 알림을 사용자 지정하여 SDK에서 발견되는 예외를 생성하고 메시지를 삭제하는 경우 이 문제가 발생할 수 있습니다. 또한 플랫폼에서 전송된 푸시 메시지의 최신 버전을 이해할 수 없지만 알림이 플랫폼에서 발송되기 전에 앱이 업그레이드될 때만 해당하는 Mobile Engagement SDK의 버전을 장치의 앱이 사용하는 경우에 발생할 수 있습니다. **고급** 탭은 얼마나 많은 메시지가 삭제되었는지 알려줍니다. 
   4. iOS 장치에서 장치의 배터리가 부족하거나 앱이 원격 알림을 처리할 때 상당한 양의 전원을 사용하는 경우 메시지가 가끔 배달되지 않습니다. 이는 iOS 장치의 한계입니다.   
3. **표시됨** - 모바일 앱 내의 알림 센터 또는 앱 내 알림에서 시스템 푸시/앱 알림 형식의 장치에 있는 앱 사용자에게 성공적으로 표시되는 메시지의 수를 지정합니다.  **고급** 탭은 얼마나 많은 시스템 알림이 있었으며 얼마나 많은 앱 내 알림이 있었는지 알려줍니다. 
   
   *전송됨 수가 표시됨 수 보다 적은 이유(표시됨 대기 중)*
   
   1. 알림 캠페인이 종료 날짜에 있다면 알림이 전송되었을 가능성이 있습니다. 하지만 알림이 앱 사용자에게 열리고 표시되는 시간이 왔을 때 알림은 이미 만료되어 표시되지 않습니다.   
   2. 알림이 앱 내 알림인 경우 앱 사용자가 앱을 열 때에만 알림이 표시됩니다. 앱 사용자가 앱을 열지 않은 경우 SDK는 알림은 전송되었지만 앱이 열릴 때까지 표시되지 않은 것으로 보고합니다. 
   3. 알림이 앱 내 알림이고 특정 활동/화면에 표시되도록 구성한 경우 알림은 전송되었지만 사용자가 특정 화면에서 앱을 열 때까지 전송되지 않은 것으로 보고됩니다. 
4. **사용자 상호 작용** - 앱 사용자가 상호 작용하는 메시지의 수를 지정하고 작동하거나 종료되는 메시지를 포함합니다. 
   
   * *앱 사용자는 다음 방법 중 하나로 알림 작업을 수행할 수 있습니다.*
     
     1. 알림이 시스템/앱 알림이거나 앱 내 알림이 알림 전용으로 전송되면 앱 사용자는 알림을 클릭합니다.
     2. 알림이 텍스트 또는 웹 보기나 설문 조사가 있는 앱 내 알림인 경우 앱 사용자는 알림에서 실행 단추를 클릭합니다.
     3. 알림이 웹 보기가 있는 앱 내 알림인 경우 앱 사용자는 웹 보기 [Android 전용]에서 URL을 클릭합니다.
   * *앱 사용자는 다음 방법 중 하나로 알림을 끝낼 수 있습니다.*
     
     1. 알림에서 직접 닫기 단추를 클릭합니다. 
     2. 알림을 바로 넘기거나 삭제합니다. 
     3. 텍스트/웹 콘텐츠 및 설문 조사를 사용한 앱 내 알림은 일반적으로 두 단계 프로세스에서 앱 사용자에게 표시됩니다. 알림이 먼저 표시되고 클릭하면 후속 텍스트/웹/설문 조사 콘텐츠가 표시됩니다. 앱 사용자는 이러한 단계 중 하나를 종료할 수 있고 고급 보기에서 세부 정보가 이를 캡처합니다. 
5. **작업이 수행됨** - 앱 사용자가 명시적으로 수행했던 메시지 수를 지정합니다. 얼마나 많은 앱 사용자가 알림에서 푸시한 메시지에 흥미를 갖는지를 알려주는 흥미로운 숫자입니다. 

> [!NOTE]
> iOS 및 Windows 플랫폼에서 사용자가 앱을 열고 캠페인이 "시간 지정하지 않은" 캠페인이었다면 앱 알림 및 앱 내 알림은 모두 동시에 표시될 수 있습니다. 표시됨 수가 전송됨 수 보다 높아질 수 있습니다. 사용자가 알림과 상호 작용하거나 작업하는 경우 사용자 상호 작용/작업함 수는 전송됨 보다 커질 수 있습니다. 
> 
> 

![Reach2][19]

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
[Link 27]: mobile-engagement-user-interface-reach-campaign.md
[Link 28]: mobile-engagement-user-interface-reach-criterion.md
[Link 29]: mobile-engagement-user-interface-reach-content.md

