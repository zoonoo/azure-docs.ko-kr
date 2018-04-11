---
title: Azure Mobile Engagement 사용자 인터페이스 - 도달률 콘텐츠
description: Azure Mobile Engagement에서 다양한 푸시 알림 캠페인 유형의 고유한 콘텐츠를 관리하는 방법을 알아봅니다.
services: mobile-engagement
documentationcenter: ''
author: piyushjo
manager: erikre
editor: ''
ms.assetid: add64f06-43c9-475c-8722-51cd00bb844b
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 982cc66ffe98aa6dff8fe290cc1c2d4bad03c9ac
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2018
---
# <a name="how-to-manage-the-unique-content-of-the-different-types-of-push-notification-campaigns"></a>다양한 푸시 알림 캠페인 유형의 고유한 콘텐츠를 관리하는 방법
> [!IMPORTANT]
> Azure Mobile Engagement는 2018/3/31에 사용이 중지됩니다. 이 페이지는 이후에 삭제됩니다.
> 

새 도달률 캠페인의 콘텐츠 섹션을 사용하면 알림, 설문 조사, 데이터 푸시 및 타일(Windows Phone 전용)의 콘텐츠를 수정할 수 있습니다. 푸시 캠페인의 콘텐츠 설정은 캠페인의 유형에 따라 다릅니다. 

### <a name="content-types"></a>콘텐츠 형식:
* 알림
* 설문 조사
* 데이터 푸시
* 파일(Windows Phone 전용)

## <a name="content-of-announcements"></a>알림의 내용
 ![도달률 콘텐츠1][30] 

### <a name="choose-the-type-of-your-announcement"></a>알림의 유형을 선택합니다.
* 알림 전용: 단순한 표준 알림입니다. 즉, 사용자가 알림을 클릭해도 추가 뷰는 표시되지 않으며 해당 알림과 연결된 작업만 수행됩니다.
* 텍스트 알림: 사용자가 텍스트 뷰를 확인하도록 하는 알림입니다.
* 웹 알림: 사용자가 웹 뷰를 확인하도록 하는 알림입니다.

### <a name="see-also"></a>참고 항목
* [도달률 - 방법 - 알림][Link 3] 

### <a name="about-web-view-announcements"></a>웹 뷰 알림 정보
여기서 입력하는 JavaScript 코드 또는 HTML 코드에서 나타나는 "{deviceid}" 패턴은 자동으로 알림을 표시하는 장치의 식별자로 바뀝니다. 따라서 백오피스에서 호스트되는 외부 웹 서비스에서 Azure Mobile Engagement 장치 식별자를 쉽게 검색할 수 있습니다.
기본적으로 제공되는 작업 및 종료 단추를 포함하지 않고 전체 화면 웹 뷰를 만들려는 경우 웹 뷰 알림의 JavaScript 코드에서 다음 함수를 사용하면 됩니다. 

* 알림 작업 수행: ReachContent.actionContent()
* 알림 종료: ReachContent.exitContent()

### <a name="choose-your-action"></a>작업 선택
### <a name="about-action-urls"></a>작업 URL 정보
대상으로 지정된 장치의 운영 체제에서 해석할 수 있는 모든 URL을 작업 URL로 사용할 수 있습니다.
사용자가 특정 화면으로 이동하도록 하는 데 사용되는 URL 등 응용 프로그램이 지원할 수 있는 모든 전용 URL도 작업 URL로 사용할 수 있습니다.
URL에서 나타나는 각 {deviceid} 패턴은 자동으로 작업을 수행하는 장치의 식별자로 바뀝니다. 따라서 백오피스에서 호스트되는 외부 웹 서비스에서 Azure Mobile Engagement 장치 식별자를 쉽게 검색할 수 있습니다.

* **Android + iOS 작업**
  * 웹 페이지 열기
  * http://\[web-site-domain\] 
  * 예:http://www.azure.com
  * 메일 보내기
  * mailto:\[e-mail-recipient\]?subject=\[subject\]&body=\[message\] 
  * Example:mailto:foo@example.com?subject=Greetings%20from%20Azure%20Mobile%20Engagement!&body=Good%20stuff!
  * 문자 메시지 보내기
  * sms:\[phone-number\] 
  * 예: sms:2125551212
  * 특정 번호로 전화 걸기
  * tel:\[phone-number\] 
  * 예: tel:2125551212
* **Android 전용 작업**
  * PlayStore에서 응용 프로그램 다운로드
  * market://details?id=\[app package\] 
  * 예: market://details?id=com.microsoft.office.word
  * 지리적 위치에 따른 검색 시작
  * geo:0,0?q=\[search query\] 
  * 예: geo:0,0?q=starbucks,paris
* **iOS 전용 작업**
  * 앱 스토어에서 응용 프로그램 다운로드
  * http://itunes.apple.com/[country]/app/[app name]/id[app id]?mt=8 
  * 예:http://itunes.apple.com/fr/app/briquet-virtuel/id430154748?mt=8
  * Windows 작업
  * 웹 페이지 열기
  * http://\[web-site-domain\] 
  * 예:http://www.azure.com
  * 메일 보내기
  * mailto:\[e-mail-recipient\]?subject=\[subject\]&body=\[message\] 
  * Example:mailto:foo@example.com?subject=Greetings%20from%20Azure%20Mobile%20Engagement!&body=Good%20stuff!
  * 문자 메시지 보내기(Skype 스토어 앱 필요)
  * sms:\[phone-number\] 
  * 예: sms:2125551212
  * 특정 번호로 전화 걸기(Skype 스토어 앱 필요)
  * tel:\[phone-number\] 
  * 예: tel:2125551212
  * PlayStore에서 응용 프로그램 다운로드
  * ms-windows-store:PDP?PFN=\[app package ID\] 
  * 예: ms-windows-store:PDP?PFN=4d91298a-07cb-40fb-aecc-4cb5615d53c1
  * Bing 지도 검색 수행
  * bingmaps:?q=\[search query\] 
  * 예: bingmaps:?q=starbucks,paris
  * 사용자 지정 체계 사용
  * \[사용자 지정 체계\]://\[사용자 지정 체계 매개 변수\] 
  * 예: myCustomProtocol://myCustomParams
  * 패키지 데이터 사용(확장명 읽기를 위한 스토어 앱 필요)
  * \[folder\]\[data\].\[extension\] 
  * 예: myfolderdata.txt

### <a name="build-a-tracking-url"></a>추적 URL 작성
* 사용자가 다른 응용 프로그램 중 하나를 다운로드하는 데 사용할 수 있는 추적 URL을 작성하는 지침은 <UI Documentation>의 "설정" 섹션을 참조하세요.

### <a name="define-the-texts-of-your-announcement"></a>알림 텍스트 정의
알림의 제목, 내용 및 단추 텍스트를 입력합니다. 사용자가 이 캠페인에 응답한 방법과 관련한 도달률 피드백을 기반으로 하여 이후 캠페인의 대상을 지정할 수 있습니다. 이 캠페인에 대한 피드백(푸시만, 회신, 작업, 종료)에 따라 대상을 지정할 수 있습니다.

### <a name="see-also"></a>참고 항목
* [UI 설명서 - 도달률 - 새 푸시 기준][Link 28]

## <a name="content-of-polls"></a>설문 조사의 내용
![도달률 콘텐츠2][31] 

알림의 제목, 설명 및 단추 텍스트를 입력합니다. 그런 다음 질문 및 질문에 대한 대답의 선택 항목을 추가합니다.
사용자가 이 캠페인에 응답한 방법과 관련한 도달률 피드백을 기반으로 하여 이후 캠페인의 대상을 지정할 수 있습니다. 이 캠페인에 대한 응답 방법(푸시만, 회신, 작업, 종료)에 따라 대상을 지정할 수 있습니다. 설문 조사 대답 피드백을 기준으로 대상을 지정할 수도 있습니다. 이 경우 질문과 대답 선택 항목이 기준으로 사용됩니다.

### <a name="see-also"></a>참고 항목
* [UI 설명서 - 도달률 - 새 푸시 기준][Link 28]

## <a name="content-of-data-pushes"></a>데이터 푸시의 내용
![도달률 콘텐츠3][32] 

### <a name="choose-the-type-of-your-data"></a>데이터 형식을 선택합니다.
* 텍스트
* 이진 데이터
* Base64 데이터

### <a name="define-the-content-of-your-data"></a>데이터의 내용을 정의합니다.
* 텍스트 데이터를 푸시하도록 선택한 경우 텍스트를 복사하여 "콘텐츠" 상자에 붙여 넣습니다.
* 이진 또는 base64 데이터를 푸시하도록 선택한 경우에는 "파일 업로드" 단추를 사용하여 파일을 업로드합니다.
* 사용자가 이 캠페인에 응답한 방법과 관련한 도달률 피드백을 기반으로 하여 이후 캠페인의 대상을 지정할 수 있습니다. 이 캠페인에 대한 응답 방법(푸시만, 회신, 작업, 종료)에 따라 대상을 지정할 수 있습니다.

### <a name="see-also"></a>참고 항목
* [UI 설명서 - 도달률 - 새 푸시 기준][Link 28]

## <a name="content-of-tiles-windows-phone-only"></a>타일의 내용(Windows Phone 전용)
![도달률 콘텐츠4][33]

### <a name="define-the-content-of-your-tile"></a>타일의 내용을 정의합니다.
타일 페이로드는 Windows Phone 장치에서 앱의 타일에 표시할 텍스트입니다.
타일 푸시는 Windows Phone용 네이티브 푸시의 MPNS(Microsoft 푸시 알림 서비스) 버전입니다. 타일 푸시 유형은 유일하게 응답이 없는 푸시 유형이므로 타일 푸시 캠페인의 결과를 기준으로 이후 캠페인의 대상을 지정할 수는 없습니다. 

### <a name="see-also"></a>참고 항목
* [API 설명서 - 도달률 API - 네이티브 푸시][Link 4]

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

