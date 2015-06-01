<properties 
   pageTitle="Azure Mobile Engagement 문제 해결 가이드 - 서비스" 
   description="Azure Mobile Engagement 에 대한 문제 해결 가이드" 
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


# 서비스 문제에 대한 문제 해결 가이드

다음은 Azure Mobile Engagement 실행 방법과 관련해서 발생할 수 있는 문제입니다.

## 서비스 중단

### 문제
- Azure Mobile Engagement 서비스 중단으로 인해 발생한 것처럼 보이는 문제입니다.

### 원인
- Azure Mobile Engagement 서비스 중단으로 인해 발생한 것으로 나타나는 문제에는 여러 가지 원인이 있을 수 있습니다.
    - 원래 모든 Azure Mobile Engagement에서 전체적으로 발생하는 것으로 확인된 문제
    - 서버 중단으로 인한 알려진 문제(서버 상태에 항상 표시되지는 않음):
	- 예약 지연, 대상 지정 오류, 배지 업데이트 문제, 통계 수집 중지, 푸시 작동 중지, API 작동 중지, 새 앱이나 사용자 만들기 불가, DNS 오류, 장치의 UI/API/앱 시간 초과 오류
    - 클라우드 종속성 작동 중단 [Azure 서비스 상태](http://status.azure.com/), [AWS(Amazon 웹 서비스) 상태](http://status.aws.amazon.com/) 
    - PNS(푸시 알림 서비스) 종속성 작동 중단 [Google - 서비스](http://www.google.com/appsstatus#hl=en&v=status), [Apple - 서비스](http://www.apple.com/support/systemstatus/), [Android - Google GCM](http://developer.android.com/google/gcm/index.html), [Android - Amazon ADM](https://developer.amazon.com/appsandservices/apis/engage/device-messaging), [Apple - APNS](https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html), [Windows Phone - WNS](http://msdn.microsoft.com/library/windows/apps/hh465407.aspx), [Windows Phone - MPNS][LinkMPNS], [Windows - WNS](https://developer.windows.com/)
    - 앱 스토어 작동 중단 [GooglePlay](https://play.google.com/), [iTunes](http://www.apple.com/itunes/charts/), [Windows Phone 스토어](http://www.windowsphone.com/), [Windows 스토어](http://windows.microsoft.com/)

*문제가 Azure Mobile Engagement 전체의 문제인지 테스트하려는 경우 다음의 개별 항목에서 같은 기능을 테스트할 수 있습니다.*

	- Azure Mobile Engagement integrated application
	- Test device
	- Test device OS version
	- Campaign
	- Administrator user account
	- Browser (IE, Firefox, Chrome, etc.)
	- Computer
*문제가 UI 또는 API에만 영향을 주는지 테스트하려면*

	- Test the same function from both the Azure Mobile Engagement UI and the Azure Mobile Engagement API's.

[API 설명서][Link 4], [UI 설명서][Link 1]

*휴대폰 네트워크 관련 문제인지를 테스트하려면*

	- Test while connected to the Internet via WIFI and while connected via your 3G cellular phone network.
	Confirm that your firewall is not blocking any of the Azure Mobile Engagement IP Addresses or Ports.

*장치 관련 문제인지를 테스트하려면*

	- Test if your Device is able to connect to Azure Mobile Engagement with another Azure Mobile Engagement integrated app.
	- Test that you can generate Events, Jobs, and Crashes from your phone that can be seen in the Azure Mobile Engagement UI. 
	- Test if you can send push notifications from the Azure Mobile Engagement UI to your device based on its Device ID. 
[UI 설명서 - 설정][Link 1]

*앱 관련 문제인지를 테스트하려면*

	- Install and test your application from an emulator instead of from a physical device:
		- Download and use Android SDK (includes an Android Emulator)
		- Download and use Apple Xcode (includes an iOS Simulator)
		- Download and use Windows Phone SDK (includes a Windows Phone 7, 8 and 8.1 Emulators)

최종 사용자 장치의 OS 업그레이드 관련 문제인지를 테스트하려면 다음을 수행합니다(문제를 해결하려면 SDK를 업그레이드해야 함).

	- Test your application on different devices with different versions of the OS.
	- Confirm that you are using the most recent version of the SDK.
[문제 해결 가이드 - SDK][Link 2]
 
## 연결 및 잘못된 정보 문제

### 문제
- Azure Mobile Engagement UI에 로그인하는 데 문제가 있습니다.
- Azure Mobile Engagement API에서 연결 문제가 발생합니다.
- 장치 API를 통해 앱 정보 태그를 업로드하는 데 문제가 있습니다.
- Azure Mobile Engagement에서 로그나 내보낸 데이터를 다운로드하는 데 문제가 있습니다.
- Azure Mobile Engagement UI에 잘못된 정보가 표시됩니다.
- Azure Mobile Engagement 로그에 잘못된 정보가 표시됩니다.

### 원인
- Azure Mobile Engagement 연결 관련 문제:
    - 사용자 계정에 작업을 수행할 수 있는 권한이 있는지 확인합니다.
    - 문제가 단일 컴퓨터나 로컬 네트워크에서만 발생하지 않는지 확인합니다.
    - Azure Mobile Engagement 서비스에서 작동 중단이 보고되지 않았는지 확인합니다.
    - 앱 정보 태그 파일이 다음 규칙을 모두 따르는지 확인합니다.
        - UTF8 문자 집합만 사용합니다. ANSI 문자 집합은 지원되지 않습니다.
        - 쉼표(",")를 구분 기호 문자로 사용합니다. .csv 구문 기호 문자를 쉼표(",")에서 세미콜론(";") 등의 다른 문자로 변경하도록 요청하는 서비스 요청을 개설할 수 있습니다.
        - 부울 값 "true"와 "false"에 소문자만 사용합니다.
        - 최대 파일 크기인 35MB보다 작은 파일을 사용합니다.

### 참고 항목

[API 설명서][Link 4], [UI 설명서 - 홈][Link 1]
 
## 기능 요청

### 문제
- 사용하려는 기능이 Azure Mobile Engagement에 아직 포함되어 있지 않은 것으로 보입니다.

### 원인

Azure Mobile Engagement에 아직 포함되지 않은 새로운 기능을 제안하려면 
	- Azure Mobile Engagement에서 사용하려는 새 기능에 대해 최대한 많은 세부 정보를 포함하여 Azure Mobile Engagement 서비스 요청을 개설합니다.

### 참고 항목

[Mobile Engagement 피드백/기능 요청](http://feedback.azure.com/forums/285737-mobile-engagement)

<!--Link references-->
[Link 1]: mobile-engagement-user-interface.md
[Link 2]: mobile-engagement-troubleshooting-guide.md
[Link 3]: mobile-engagement-how-tos.md
[Link 4]: http://go.microsoft.com/fwlink/?LinkID=525553
[Link 5]: http://go.microsoft.com/fwlink/?LinkID=525554
[Link 6]: http://go.microsoft.com/fwlink/?LinkId=525555
[Link 7]: https://account.windowsazure.com/PreviewFeatures
[Link 8]: https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=azuremobileengagement
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
[LinkMPNS]: http://msdn.microsoft.com/library/windows/apps/ff402558(v=vs.105).aspx

<!--HONumber=54-->