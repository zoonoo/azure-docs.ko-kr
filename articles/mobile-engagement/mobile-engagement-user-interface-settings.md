<properties 
   pageTitle="Azure Mobile Engagement 사용자 인터페이스 - 설정" 
   description="Azure Mobile Engagement를 사용하여 응용 프로그램의 전역 설정을 관리하는 방법 알아보기" 
   services="mobile-engagement" 
   documentationCenter="" 
   authors="piyushjo" 
   manager="dwrede" 
   editor=""/>

<tags
   ms.service="mobile-engagement"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="mobile-multiple"
   ms.workload="mobile" 
   ms.date="02/17/2015"
   ms.author="piyushjo"/>

# 응용 프로그램의 전역 설정을 관리하는 방법
응용 프로그램에서 제공되는 설명 메뉴 옵션은 응용 프로그램의 플랫폼과 응용 프로그램에 대해 부여한 권한에 따라 달라집니다. 설정에는 세부 정보, 프로젝트, 네이티브 푸시, 푸시 속도, SDK, 추적, 앱 정보, 상업적 압력, 권한 등이 있습니다. UI 설정 섹션의 앱 정보 메뉴 옵션에만 장치 API의 "태그" 기능을 사용하여 관리할 수 있는 요소가 포함되어 있습니다. "개념"의 "용어집"에 용어와 약어의 정의가 나와 있습니다. 이러한 용어로는 APNS, GCM, IDFA, API, SDK, API 키, SDK 키, 응용 프로그램 ID(앱 ID), 앱 스토어 ID, 태그 계획, 사용자 ID, 장치 ID, 앱 대리자, 스택 추적, 딥 링크 등이 있습니다.

### 참고 항목
- [API 설명서 - 장치 API][Link 4], [개념 - 용어집][Link 6], [문제 해결 가이드 - 서비스][Link 24]

  ![settings1][46]

## 세부 정보
응용 프로그램의 이름과 설명을 변경하고 응용 프로그램 소유자와 역할 권한을 확인할 수 있습니다. 분석 구성에서는 주의 시작 요일과 보존 기간(을) 확인/변경할 수 있습니다.
 
  ![settings2][47]
 
## 프로젝트
응용 프로그램을 표시할 모든 프로젝트를 선택할 수 있습니다. 또한 프로젝트를 검색하고 응용 프로그램이 속한 프로젝트의 이름, 설명, 소유자, 역할 권한을 확인할 수 있습니다.

### 참고 항목
-    [UI 설명서 – 홈][Link 13]
 
  ![settings3][48]

## 네이티브 푸시
네이티브 푸시에 사용할 새 인증서를 등록하거나 기존 인증서를 삭제할 수 있습니다. Azure Mobile Engagement에서는 네이티브 푸시를 통해 응용 프로그램이 실행 중이지 않을 때라도 언제든지 응용 프로그램에 푸시를 보낼 수 있습니다. 하나 이상의 네이티브 푸시 서비스에 대해 자격 증명이나 인증서를 제공한 후에는 도달률 캠페인을 만들 때 "항상"을 선택할 수 있으며 푸시 API에서 "알림 구성 요소" 매개 변수도 사용할 수 있습니다.

### 참고 항목
- [API 설명서 - 도달률 API][Link 4],[API 설명서 - 푸시 API][Link 4], [UI 설명서 - 도달률 - 새 푸시 캠페인][Link 27]

### APNS(Apple 푸시 알림 서비스)
Apple 푸시 알림 서비스를 사용하여 네이티브 푸시를 사용하도록 설정하려면 인증서를 등록해야 합니다. 인증서 유형은 개발(DEV) 또는 프로덕션(PROD)으로 지정해야 합니다. 그런 후에 인증서와 암호를 업로드해야 합니다.

### 참고 항목
- [SDK 설명서 - iOS - Apple 푸시 알림을 받도록 응용 프로그램을 준비하는 방법][Link 5]
 
![settings4][49]
 
### WPNS(Windows 푸시 알림 서비스)
Windows 알림 서비스를 사용하여 네이티브 푸시를 사용하도록 설정하려면 응용 프로그램의 자격 증명을 제공해야 합니다. 이렇게 하려면 패키지 SID(보안 식별자)와 비밀 키가 필요합니다.
 
![settings5][50]
 
### Google Cloud Messaging for Android(GCM)
GCM을 사용하여 네이티브 푸시를 사용하도록 설정하려면 Google의 지침을 따라야 합니다. 그런 후에 IP 제한 없이 구성된 서버의 단순 API 키를 붙여 넣어야 합니다. 이렇게 하려면 Android v1.12.0 이상 버전용 SDK를 통합해야 합니다.

### 참고 항목
- [SDK 설명서 - Android - GCM을 통합하는 방법][Link 5], [Google 개발자 - GCM 가이드](http://developer.android.com/guide/google/gcm/gs.html), [Google 개발자 - GCM 설명서](http://developer.android.com/google/gcm/index.html)
 
### Amazon Device Messaging for Android(ADM)
ADM을 사용하여 네이티브 푸시를 사용하도록 설정하려면 클라이언트 ID와 클라이언트 암호로 구성된 Amazon <OAuth credentials>을(를) 제공해야 합니다. 이렇게 하려면 Android v2.1.0 이상 버전용 SDK를 통합해야 합니다.

### 참고 항목
- [SDK 설명서 - Android - ADM을 통합하는 방법][Link 5], [Amazon 개발자 - ADM 설명서](https://developer.amazon.com/sdk/adm/credentials.html#Getting)

![settings6][51]

## 푸시 속도
응용 프로그램의 현재 푸시 속도가 표시되며 원하는 푸시 속도를 정의할 수 있습니다. 푸시 속도에 따라 도달률 모듈에서 수행할 초당 최대 푸시 횟수가 정의됩니다. 따라서 캠페인 활성화 후 초당 요청 수가 너무 많아 서버가 오버로드되는 경우에 유용할 수 있습니다.
 
  ![settings7][52]

## 추적
추적 기능을 사용하면 iOS 및 Android 응용 프로그램 설치 원본을 추적할 수 있습니다. 즉, 사용자가 응용 프로그램을 다운로드한 위치(예: 응용 프로그램 스토어)와 해당 스토어로 이동하게 된 경로(예: 광고 캠페인, 블로그, 웹 사이트, 전자 메일, 문자 메시지 등)를 확인할 수 있습니다. Azure Mobile Engagement의 추적 기능은 별도의 단계를 통해 SDK에서 응용 프로그램에 통합해야 합니다.

### 참고 항목
- [SDK 설명서 - Android - 통합 방법][Link 5], [SDK 설명서 - iOS - 통합 방법][Link 5]
 
### 스토어
응용 프로그램의 이름 및 연결된 다운로드 URL에 따라 응용 프로그램을 다운로드할 수 있는 모든 스토어를 등록할 수 있습니다. 이렇게 하면 추적 URL을 호스트할 위치를 만들 수 있습니다. 이 페이지에서 스토어를 만들거나 삭제할 수 있습니다. 아이콘을 사용하여 새 추적 URL을 만들면 아래에서 설명하는 추적 URL 페이지로 이동하게 됩니다. 다음과 같은 여러 방법을 통해 사용자가 앱을 다운로드하는 위치를 추적할 수 있습니다.

-    타사 광고 서버 사용. Azure Mobile Engagement에서는 현재 [SmartAd](http://smartadserver.fr/) 및 [Surikate](http://www.surikate.com/)를 지원합니다.
-    타사 특성 서비스 사용. Azure Mobile Engagement에서는 현재 [Mobile App Tracking](http://www.mobileapptracking.com/)을지원하며, 조만간 [Trademob](http://www.trademob.com/)도 지원될 수 있습니다.
-    타사 설치 참조 페이지 사용. Azure Mobile Engagement에서는 현재 [Google Play 설치 참조 페이지](https://developers.google.com/app-conversion-tracking/docs/third-party-trackers/)(Android 전용)를 지원합니다.
-    수동 보고 사용
 
  ![settings8][53]
 
### 광고 캠페인
광고 서버 이름, 캠페인 ID 및 응용 프로그램 다운로드가 가능한 원본으로 구성된 새 광고 캠페인을 만들 수 있습니다.
 
  ![settings9][54]
 
### 추적 URL
광고 캠페인, 블로그, 웹 사이트, 전자 메일, 문자 메시지 등의 원본에서 대상 URL로 사용할 추적 URL을 작성할 수 있습니다. 이 URL은 응용 프로그램을 다운로드할 수 있는 스토어로 사용자를 리디렉션할 수 있습니다. 또한 이미 만든 모든 추적 URL을 표시할 수도 있습니다. 추적 URL을 광고 캠페인이나 도달률 알림의 작업 URL로 사용하여 사용자가 다른 앱에서 앱 중 하나를 다운로드하도록 초대할 수 있습니다. 추적 URL은 선택한 스토어와 연결된 다운로드 URL로 리디렉션되며, 추적 시스템에서 응용 프로그램을 설치 시에 다운로드한 스토어를 기록하도록 허용합니다. 원본을 제공하는 경우 추적 시스템에서는 해당 원본도 기록하므로 응용 프로그램용으로 만든 여러 광고 캠페인을 구분할 수 있습니다.

새 추적 URL을 만들려면 스토어와 원본(없음, 사용자 지정, 서버 추가)을 지정해야 합니다.

-    원본을 없음으로 지정하면 기본 추적 URL이 작성됩니다.
-    원본을 사용자 지정으로 지정하면 응용 프로그램을 다운로드할 외부 서버의 URL을 지정할 수 있습니다.
-    원본을 광고 서버로 지정하면 기본 이름인 '광고 서버'를 사용하여 기본 추적 URL이 작성됩니다.
 
### 참고 항목
- [UI 설명서 - 도달률 - 새 푸시 캠페인][Link 27] 
 
### 추적 URL 작성
사용자가 새 도달률 캠페인의 콘텐츠 섹션에서 응용 프로그램 중 하나를 다운로드할 수 있도록 추적 URL을 작성할 수 있습니다.

### 참고 항목
- [UI 설명서 - 도달률 - 푸시 콘텐츠][Link 29]

![settings10][55]

## 앱 정보
응용 프로그램 사용자와 관련된 추가 정보를 등록할 수 있습니다. 이 정보는 응용 프로그램에서 SDK를 사용하여 삽입하거나 백 엔드에서 장치 API를 사용하여 삽입할 수 있습니다.

### 참고 항목
- [API 설명서-장치 API][Link 4]

응용 프로그램 정보 태그를 등록하면 해당 태그를 기준으로 특정 도달률 대상 기준을 만들어 도달률 캠페인을 구분할 수 있습니다. 기존 앱 정보 태그의 이름과 형식을 확인하거나, 이름 및 형식(문자열/날짜/정수/부울)을 기준으로 새 앱 정보를 추가할 수 있습니다.

### 참고 항목
- [UI 설명서 - 도달률 - 새 푸시 캠페인][Link 27]
 
![settings11][56]
 
## 상업적 압력
푸시 할당량을 사용하면 특정 기간 동안 장치에 푸시를 보낼 수 있는 최대 횟수를 정의할 수 있습니다. "푸시 할당량 적용" 옵션을 사용하도록 설정한 도달률 캠페인에서만 푸시 할당량이 사용됩니다. 세그먼트별로 또는 기본적으로 푸시 할당량을 관리할 수 있습니다. 지난 1시간, 1일, 1주, 1개월의 슬라이딩 기간에 최대 횟수의 푸시만 보내도록 할당량을 설정할 수 있습니다.

### 참고 항목
- [UI 설명서 - 도달률 - 새 푸시 캠페인][Link 27], [UI 설명서 - 세그먼트][Link 18]

### 할당량:
- 기본 할당량: 이 할당량은 아래의 '세그먼트별 할당량' 섹션에서 어떤 세그먼트와도 일치하지 않는 사용자에게 적용됩니다. 기본적으로는 할당량이 설정되지 않습니다.
- 세그먼트별 할당량: 할당량은 사용자 그룹에 적용해야 하므로 할당량을 적용할 사용자를 식별하는 세그먼트를 만들어야 합니다. 이 세그먼트는 사용자가 만드는 세션 수로 정의되는 '사용량이 많은 사용자' 세그먼트일 수도 있고 사용자 그룹을 정의하는 데 사용하려는 관심 세그먼트일 수도 있습니다. 장치 하나가 할당량이 정의된 여러 세그먼트와 일치하는 경우에는 시간당 최대 푸시 수가 가장 많은 할당량만 적용됩니다.

![settings12][57]
 
## 권한
응용 프로그램 사용자의 전자 메일, 이름, 조직 및 권한 수준을 검색하고 확인할 수 있습니다. 권한 개념은 프로젝트 역할에서 추가적으로 제공되며, 응용 프로그램 액세스 권한이 있는 특정 사용자에 단일 권한 집합을 연결하는 데 사용할 수 있습니다.

### 현재 사용 가능한 권한 수준은 다음과 같습니다.
-    "도달률 캠페인 작성자"(도달률 캠페인을 만들 수 있는 사용자) 
-    "도달률 캠페인 관리자"(도달률 캠페인을 만들고 활성화/일시 중단/완료/삭제할 수 있는 사용자)

> 참고: 사용자가 프로젝트 역할과 지정된 응용 프로그램에 대한 권한 집합을 모두 보유하고 있으면 허용 수준이 더 높은 개념이 사용됩니다. 따라서 권한을 사용할 때는 사용자의 프로젝트 역할을 "보기 권한자"로 설정한 다음 응용 프로그램 수준에서 보다 권한 수준이 높은 권한을 추가하는 것이 좋습니다.

### 참고 항목
- [UI 설명서 - 홈][Link 13]  
 
![settings13][58]

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
 

<!---HONumber=July15_HO4-->