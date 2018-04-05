---
title: Azure Mobile Engagement 사용자 인터페이스 - 설정
description: Azure Mobile Engagement를 사용하여 응용 프로그램의 전역 설정을 관리하는 방법 알아보기
services: mobile-engagement
documentationcenter: ''
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 858f4cb4-14de-4bb5-826f-28cadbfc928b
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 31df9f151febcb7e5f0f2cd4a83053b3aac416a3
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2018
---
# <a name="how-to-manage-the-global-settings-of-your-application"></a>응용 프로그램의 전역 설정을 관리하는 방법
> [!IMPORTANT]
> Azure Mobile Engagement는 2018/3/31에 사용이 중지됩니다. 이 페이지는 이후에 삭제됩니다.
> 

응용 프로그램에서 제공하는 **설정** 메뉴 옵션은 응용 프로그램의 플랫폼과 응용 프로그램에 대해 부여한 권한에 따라 달라집니다. 설정에는 세부 정보, 프로젝트, 네이티브 푸시, 푸시 속도, 태그(앱 정보) 및 상업적 압력 등이 있습니다. 설정 섹션의 태그(앱 정보) 메뉴 옵션은 응용 프로그램(SDK 사용) 또는 백 엔드(장치 API 사용)에서 관리할 수 있습니다. 

> [!NOTE]
> **Mobile Engagement** 포털 UI의 여러 섹션에는 **도움말 표시** 단추가 포함되어 있습니다. 섹션에 대해 더 자세한 문맥 정보를 보려면 이 단추를 누릅니다.
> 
> 

## <a name="details"></a>세부 정보
응용 프로그램의 이름과 설명을 변경하고 응용 프로그램 소유자와 역할 권한을 확인할 수 있습니다. 

분석 구성에서는 주의 시작 요일과 보존 기간을 확인하거나 변경할 수 있습니다.

  ![settings1][46]

## <a name="projects"></a>프로젝트
응용 프로그램을 표시할 모든 프로젝트를 선택할 수 있습니다. 

또한 프로젝트를 검색하고 응용 프로그램이 속한 프로젝트의 이름, 설명, 소유자, 역할 권한을 확인할 수 있습니다.

자세한 내용은 [UI 설명서 - 홈][Link 13]을 참조하세요.

  ![settings3][48]

## <a name="native-push"></a>네이티브 푸시
네이티브 푸시에 사용할 새 인증서를 등록하거나 기존 인증서를 삭제할 수 있습니다. Azure Mobile Engagement에서는 네이티브 푸시를 통해 응용 프로그램이 실행 중이지 않을 때라도 언제든지 응용 프로그램에 푸시를 보낼 수 있습니다. 

하나 이상의 네이티브 푸시 서비스에 대해 자격 증명이나 인증서를 제공한 후에는 도달률 캠페인을 만들 때 "항상"을 선택할 수 있으며 푸시 API에서 "알림 구성 요소" 매개 변수도 사용할 수 있습니다.

### <a name="apple-push-notification-service-apns"></a>APNS(Apple 푸시 알림 서비스)
Apple 푸시 알림 서비스를 사용하여 네이티브 푸시를 사용하도록 설정하려면 인증서를 등록해야 합니다. 인증서 유형은 개발(DEV) 또는 프로덕션(PROD)으로 지정해야 합니다. 그런 후에 인증서와 암호를 업로드해야 합니다.

자세한 내용은 [SDK 설명서 - iOS - Apple 푸시 알림을 받도록 응용 프로그램을 준비하는 방법][Link 5]을 참조하세요.

![settings4][49]

### <a name="windows-push-notification-service-wpns"></a>WPNS(Windows 푸시 알림 서비스)
Windows 알림 서비스를 사용하여 네이티브 푸시를 사용하도록 설정하려면 응용 프로그램의 자격 증명을 제공해야 합니다. 이렇게 하려면 패키지 SID(보안 식별자)와 비밀 키가 필요합니다.

![settings5][50]

### <a name="google-cloud-messaging-for-android-gcm"></a>Google Cloud Messaging for Android(GCM)
GCM을 사용하여 네이티브 푸시를 사용하도록 설정하려면 Google의 지침을 따라야 합니다. 그런 후에 IP 제한 없이 구성된 서버의 단순 API 키를 붙여 넣어야 합니다. 이렇게 하려면 Android v1.12.0 이상 버전용 SDK를 통합해야 합니다.

자세한 내용은 다음을 참조하세요. 

* [SDK 설명서 Android GCM을 통합하는 방법][Link 5]
* [Google 개발자 GCM 가이드](http://developer.android.com/guide/google/gcm/gs.html)

### <a name="amazon-device-messaging-for-android-adm"></a>Amazon Device Messaging for Android(ADM)
ADM을 사용하여 네이티브 푸시를 사용하도록 설정하려면 클라이언트 ID와 클라이언트 암호로 구성된 Amazon <OAuth credentials>을(를) 제공해야 합니다. 이렇게 하려면 Android v2.1.0 이상 버전용 SDK를 통합해야 합니다.

자세한 내용은 다음을 참조하세요. 

* [SDK 설명서 Android ADM을 통합하는 방법][Link 5]
* [Amazon 개발자 ADM 설명서](https://developer.amazon.com/sdk/adm/credentials.html#Getting)

![settings6][51]

## <a name="push-speed"></a>푸시 속도
응용 프로그램의 현재 푸시 속도가 표시되며 원하는 푸시 속도를 정의할 수 있습니다.

  ![settings7][52]

## <a name="tag-app-info"></a>태그(앱 정보)
![settings11][56]

## <a name="commercial-pressure"></a>상업적 압력
![settings12][57]

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

