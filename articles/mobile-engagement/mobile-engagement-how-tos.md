---
title: Azure Mobile Engagement 사용자 인터페이스 - 도달률 방법
description: Azure Mobile Engagement의 사용자 인터페이스 개요
services: mobile-engagement
documentationcenter: ''
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 30af87e6-c816-4cce-8609-6cbd3e83de14
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 0c536d378023ee4a9675bb2d67262fb485c8052b
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2018
---
# <a name="how-to-get-started-using-and-managing-pushes-to-reach-out-to-your-end-users"></a>사용 시작 및 최종 사용자에게 도달하는 푸시 관리 방법
> [!IMPORTANT]
> Azure Mobile Engagement는 2018/3/31에 사용이 중지됩니다. 이 페이지는 이후에 삭제됩니다.
> 

SDK가 응용 프로그램에 완전히 통합되면 UI의 도달률 섹션을 사용하기 시작하여 응용 프로그램의 사용자에게 푸시 알림을 할 수 있습니다.  

## <a name="do-your-first-push-notification-campaign"></a>첫 번째 푸시 알림 캠페인 수행
* Reach가 SDK와 함께 앱에 통합되었는지 확인합니다. 
* 응용 프로그램을 선택합니다.

![First1][1]

* "도달률" 섹션으로 이동하여 "새 알림"을 클릭합니다.

![First2][2]

* 새 캠페인을 만들고 이름을 지정합니다.
  
![First3][3]

* 알림을 배달하는 방식을 앱 내에서만으로 선택합니다.

![First4][4]

* 푸시하려는 메시지를 생성합니다.

![First5][5]

* 알림 제목을 작성할 수 있습니다(선택 사항).
* 푸시 메시지 내용을 작성합니다.
* 이미지를 업로드할 수 있습니다. 파일의 크기는 32,768바이트를 초과할 수 없습니다.
* 또한 추가 옵션을 선택할 수 있지만, 이 자습서의 주제에 집중하기 위해 나중에 살펴보겠습니다.
* 콘텐츠 형식을 알림만으로 선택합니다.

![First6][6]

* 푸시 캠페인을 만듭니다. 그러면 해당 캠페인이 캠페인 목록에 표시됩니다.

![First7][7]

## <a name="test-your-push-notification-campaign"></a>푸시 알림 캠페인 테스트
![Test1][8]

* 장치를 등록합니다.
* 푸시하려는 장치의 확인란을 클릭합니다.
* "테스트" 단추를 클릭하여 장치에 푸시를 보냅니다.

![Test2][9]

* 캠페인을 활성화합니다.

![Test3][10]

* 캠페인을 만들었으므로 사용자에게 알림을 푸시하려면 캠페인을 활성화하기만 하면 됩니다.

## <a name="send-personalized-pushes"></a>개인 설정 푸시 보내기
* 이 예제에서는 푸시 알림에 사용자 지정 리베이트 코드를 입력하는 푸시를 만듭니다.

![Personalize1][11]

개인 설정은 앱 정보 태그에서 마커를 바꿈으로써 작동되므로 먼저 사용자가 적절한 앱 정보를 정의하도록 해야 합니다. 이 예제에서 대상 사용자는 이름이 rebate_code인 앱 정보 태그를 정의해야 합니다.
위에서 알 수 있듯이 푸시 알림 내용에는 ${rebate_code} 마커가 포함되어 있는데 이 마커는 앱 정보 태그의 실제 내용으로 교체되어야 함을 나타냅니다.

> [!WARNING]
> 앱 정보 태그가 사용자에 대해 정의되지 않은 경우 사용자는 푸시를 받지 못합니다.

* 결과

![Personalize2][12]

### <a name="you-can-further-personalize-the-text-your-notification"></a>알림의 텍스트 추가 개인 설정 가능
![Personalize3][13]

* 알림의 제목 포함
* 메시지의 내용 지정
* 알림의 유형(텍스트 뷰 또는 웹 뷰) 선택

![Personalize4][14]

### <a name="the-body-of-an-announcement-may-also-be-personalized-with"></a>알림의 본문도 다음을 통해 개인 설정할 수 있습니다.
* 방문 페이지를 사용자 지정하려는 경우 작업 URL
* 제목
* 메시지의 본문

## <a name="differentiate-your-push-notification-in-or-out-of-app"></a>푸시 알림 차별화(앱 내부 또는 외부에서)
* 푸시할 알림 유형을 선택하고, 응용 프로그램을 선택하고, "도달률" 섹션으로 이동하여 푸시 캠페인을 선택하거나 생성하고, "알림" 섹션으로 이동합니다.
* 원하는 "배달 모드"를 클릭합니다.
* 특정 작업(화면)에서 알림의 발생을 원하는 경우 "작업 제한" 확인란을 클릭합니다.

![Differentiate1][15]

### <a name="out-of-app-only-delivery-mode"></a>"앱 외부에서만" 배달 모드
![Differentiate2][16]

"앱 외부에서만" 배달 모드는 응용 프로그램이 닫힌 경우 푸시 알림을 제공합니다. 이 모드가 표준 푸시 알림입니다.
"앱 외부에서만"을 선택한 경우 응용 프로그램이 빌드되고 있는 플랫폼(APNS 또는 GCM)의 인증서를 이미 제공했어야 합니다.

### <a name="see-also"></a>참고 항목
* [Apple 푸시 알림 서비스 - 인증서](http://developer.apple.com/library/mac/#documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/ApplePushService/ApplePushService.html#//apple_ref/doc/uid/TP40008194-CH100-SW9), [Google Cloud Messaging - 인증서](http://developer.android.com/google/gcm/index.html) 

### <a name="in-app-only-delivery-mode"></a>"앱 내에서만" 배달 모드
![Differentiate3][17]

"앱 내에서만" 배달 모드는 응용 프로그램이 실행 중인 경우 푸시 알림을 제공합니다.
이 알림의 경우 APNS 및 GCM 시스템을 통해 이동할 필요가 없습니다.
앱 내 배달 시스템을 사용하여 최종 사용자에게 도달할 수 있습니다.
알림을 완벽하게 사용자 지정할 수 있으며 알림이 표시되는 작업(화면)을 결정할 수 있습니다.

### <a name="anytime-delivery-mode"></a>"항상" 배달 모드
"항상" 배달 모드를 선택하면 응용 프로그램의 실행 여부와 상관없이 최종 사용자에게 도달할 수 있습니다.
"항상"을 선택한 경우 응용 프로그램이 빌드되고 있는 플랫폼(APNS 또는 GCM)의 인증서를 이미 제공했어야 합니다. 

## <a name="schedule-a-push-campaign"></a>푸시 캠페인 예약
### <a name="plan-to-start-a-campaign"></a>캠페인 시작 계획
![Shedule1][18]

3월 21일인데, 3월 22일 자정에 발표를 계획한 알림이 있습니다. 이 경우 푸시를 수행하기 위해 인터페이스 앞에 대기하고 있지 않아도 됩니다! 정확한 시간에 알림이 전송되도록 미리 계획할 수 있습니다.

* "없음" 확인란의 선택을 취소하고 시작 시간을 선택합니다. 
* 푸시 캠페인을 시작할 날짜 및 시간을 선택합니다.

### <a name="plan-to-end-a-campaign"></a>캠페인 종료 계획
![Shedule2][19]

3월 25일 오후 3시에 캠페인을 중지하고 싶지만, 그 시간에 해당 장소에 없다는 것을 알고 있습니다.
이 경우 푸시를 수행하려고 인터페이스 앞에 대기하고 있지 않아도 됩니다! 정확한 시간에 캠페인이 중지되도록 미리 계획할 수 있습니다.

* "없음" 확인란을 클릭하거나 종료 시간을 선택합니다.
* 푸시 캠페인을 완료할 날짜 및 시간을 선택합니다.

### <a name="end-a-campaign-manually"></a>캠페인 수동 종료
![Shedule3][20]

기본적으로 "없음" 확인란이 선택되어 있습니다.
이는 도달률 섹션에서 캠페인을 활성화하는 즉시 캠페인이 시작되며 도달률 섹션에서 캠페인을 중지하면 종료된다는 것을 의미합니다.

> [!NOTE]
> 종료 날짜 없이 작성된 캠페인은 푸시를 장치에 로컬로 저장하며, 캠페인을 수동으로 종료하더라도 다음 번에 앱을 열 때 해당 푸시를 표시합니다.

## <a name="enhance-a-push-notification-with-a-text-view"></a>텍스트 뷰로 푸시 알림 개선
### <a name="what-is-a-text-view"></a>텍스트 뷰란 무엇인가요?
![TextView1][21]

텍스트 뷰는 텍스트 내용이 있는 팝업입니다. 이 팝업은 최종 사용자가 푸시 알림을 클릭한 후에 나타납니다.
텍스트 뷰를 사용하면 최종 사용자에게 더 많은 콘텐츠를 제공할 수 있습니다. 또한 텍스트 뷰는 앱의 페이지로 이동하고 스토어로 리디렉션하고 웹 페이지를 열고 메일을 보내고 지역 검색을 시작하는 등의 작업에 대한 호출을 제공할 수 있는 기회도 됩니다.

### <a name="example-text-view"></a>예: 텍스트 뷰
* "도달률" 섹션에서 푸시 알림 캠페인을 만들고 캠페인에 이름을 지정합니다.

![TextView2][22]

* 알림에 표시할 메시지를 작성합니다.
* 알림 콘텐츠 유형으로 "텍스트"를 선택합니다.

![TextView3][23]

> [!NOTE]
> 텍스트 뷰를 푸시할 경우 항상 알림이 먼저 제공됩니다. 

* 텍스트를 정의합니다. 텍스트 알림 내용을 선택하면 하위 섹션이 표시됩니다. 이 섹션에서 표시할 텍스트를 정의할 수 있습니다.

![TextView4][24]

* 메시지의 상단에 표시할 제목을 작성합니다.
* 텍스트 뷰의 주요 내용을 작성합니다.
* 실행 단추에 표시할 내용을 작성합니다. 실행 단추를 사용하면 응용 프로그램의 페이지를 열고 앱 스토어 또는 제공할 수 있는 종류의 소스로 리디렉션하는 등 응용 프로그램에서 특정 작업을 수행할 수 있습니다.
* 끝내기 단추에 표시할 내용을 작성합니다. 끝내기 단추를 클릭하면 텍스트 뷰가 사라집니다.
* 푸시 알림 캠페인을 만듭니다. 그러면 해당 캠페인이 캠페인 목록에 표시됩니다.

![TextView5][25]

* 푸시 알림 캠페인을 활성화하여 텍스트 뷰를 최종 사용자에게 보냅니다.

![TextView6][26]

* 결과

![TextView7][27]

* 사용자가 알림을 받고 해당 알림을 클릭합니다.
* 텍스트 뷰는 사용자가 조작할 수 있는 팝업으로 표시됩니다.

## <a name="enhance-a-push-notification-with-a-web-view"></a>웹 뷰로 푸시 알림 개선
### <a name="what-is-a-web-view"></a>웹 뷰란 무엇인가요?
![WebView1][28]

웹 뷰는 웹 콘텐츠가 있는 팝업입니다. 이 팝업은 최종 사용자가 푸시 알림을 클릭한 경우에 나타납니다.
웹 뷰를 사용하면 최종 사용자와 더 많은 상호 작용을 할 수 있습니다.
또한 웹 뷰는 앱 스토어로 리디렉션하고 웹 페이지를 열고 메일을 보내고 지역 검색을 시작하는 등의 작업에 대한 호출을 제공할 수 있는 기회도 됩니다.

### <a name="example-web-view"></a>예: 웹 뷰
* "도달률" 섹션에서 푸시 캠페인을 만들고 캠페인의 이름을 지정합니다.

![WebView2][29]

* 알림에 표시할 메시지를 작성합니다.
* 알림 콘텐츠 유형으로 "웹"을 선택합니다.

![WebView3][30]

### <a name="about-announcement-types"></a>알림 유형 정보:
* 알림 전용: 단순한 표준 알림입니다. 즉, 사용자가 알림을 클릭해도 추가 뷰는 표시되지 않으며 해당 알림과 연결된 작업만 수행됩니다.
* 텍스트 알림: 사용자가 텍스트 뷰를 확인하도록 하는 알림입니다.
* 웹 알림: 사용자가 웹 뷰를 확인하도록 하는 알림입니다.
  "웹 알림" 콘텐츠를 선택합니다.

> [!NOTE]
> 웹 뷰를 푸시할 경우 항상 알림이 먼저 제공됩니다.

* 웹 콘텐츠를 정의합니다. 웹 알림 콘텐츠를 선택하면 하위 섹션이 표시됩니다. 이 섹션에서 표시할 웹 뷰 콘텐츠를 정의할 수 있습니다.

![WebView4][31]

* 메시지의 상단에 표시할 제목을 작성합니다(선택 사항).
* 여기에 HTML 코드를 작성합니다.
* 소스 편집 모드 단추를 클릭하여 편집으로 전환하고 어떤 모양으로 표시되는지 확인합니다.
* 실행 단추에 표시할 내용을 작성합니다. 실행 단추를 사용하면 응용 프로그램의 페이지를 열고 스토어 또는 제공할 수 있는 종류의 소스로 리디렉션하는 등 응용 프로그램에서 특정 작업을 수행할 수 있습니다.
* 끝내기 단추에 표시할 내용을 작성합니다. 끝내기 단추를 클릭하면 웹 뷰가 사라집니다.
* 결과

![WebView5][32]

* 사용자가 알림을 받고 해당 알림을 클릭합니다.
* 텍스트 뷰는 사용자가 조작할 수 있는 팝업으로 표시됩니다.

<!--Image references-->
[1]: ./media/mobile-engagement-how-tos/First1.png
[2]: ./media/mobile-engagement-how-tos/First2.png
[3]: ./media/mobile-engagement-how-tos/First3.png
[4]: ./media/mobile-engagement-how-tos/First4.png
[5]: ./media/mobile-engagement-how-tos/First5.png
[6]: ./media/mobile-engagement-how-tos/First6.png
[7]: ./media/mobile-engagement-how-tos/First7.png
[8]: ./media/mobile-engagement-how-tos/Test1.png
[9]: ./media/mobile-engagement-how-tos/Test2.png
[10]: ./media/mobile-engagement-how-tos/Test3.png
[11]: ./media/mobile-engagement-how-tos/Personalize1.png
[12]: ./media/mobile-engagement-how-tos/Personalize2.png
[13]: ./media/mobile-engagement-how-tos/Personalize3.png
[14]: ./media/mobile-engagement-how-tos/Personalize4.png
[15]: ./media/mobile-engagement-how-tos/Differentiate1.png
[16]: ./media/mobile-engagement-how-tos/Differentiate2.png
[17]: ./media/mobile-engagement-how-tos/Differentiate3.png
[18]: ./media/mobile-engagement-how-tos/Schedule1.png
[19]: ./media/mobile-engagement-how-tos/Schedule2.png
[20]: ./media/mobile-engagement-how-tos/Schedule3.png
[21]: ./media/mobile-engagement-how-tos/TextView1.png
[22]: ./media/mobile-engagement-how-tos/TextView2.png
[23]: ./media/mobile-engagement-how-tos/TextView3.png
[24]: ./media/mobile-engagement-how-tos/TextView4.png
[25]: ./media/mobile-engagement-how-tos/TextView5.png
[26]: ./media/mobile-engagement-how-tos/TextView6.png
[27]: ./media/mobile-engagement-how-tos/TextView7.png
[28]: ./media/mobile-engagement-how-tos/WebView1.png
[29]: ./media/mobile-engagement-how-tos/WebView2.png
[30]: ./media/mobile-engagement-how-tos/WebView3.png
[31]: ./media/mobile-engagement-how-tos/WebView4.png
[32]: ./media/mobile-engagement-how-tos/WebView5.png

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

