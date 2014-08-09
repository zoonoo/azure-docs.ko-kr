<properties linkid="develop-mobile-tutorials-get-started-with-push-js-vs2013" urlDisplayName="Get Started with Push (JS)" pageTitle="Get started with push notifications (Windows Store JavaScript) | Mobile Dev Center" metaKeywords="" description="Learn how to use Azure Mobile Services to send push notifications to your Windows Store JavaScript app." metaCanonical="http://www.windowsazure.com/ko-kr/develop/mobile/tutorials/get-started-with-push-dotnet/" services="" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services" authors="glenga" solutions="" manager="" editor="" />

모바일 서비스에서 푸시 알림 시작
================================

[Windows Store C\#](/ko-kr/documentation/articles/mobile-services-windows-store-dotnet-get-started-push "Windows Store C#")[Windows Store JavaScript](/ko-kr/documentation/articles/mobile-services-windows-store-javascript-get-started-push "Windows Store JavaScript")[Windows Phone](/ko-kr/documentation/articles/mobile-services-windows-phone-get-started-push "Windows Phone")[iOS](/ko-kr/documentation/articles/mobile-services-ios-get-started-push "iOS")[Android](/ko-kr/documentation/articles/mobile-services-android-get-started-push "Android")[Xamarin.iOS](/ko-kr/documentation/articles/partner-xamarin-mobile-services-ios-get-started-push "Xamarin.iOS")[Xamarin.Android](/ko-kr/documentation/articles/partner-xamarin-mobile-services-android-get-started-push "Xamarin.Android")
[.NET 백 엔드](/ko-kr/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-push/ ".NET 백 엔드") | [JavaScript 백 엔드](/ko-kr/documentation/articles/mobile-services-windows-store-javascript-get-started-push/ "JavaScript 백 엔드")

이 항목에서는 Visual Studio 2013에서 Azure 모바일 서비스를 사용하여 Windows 스토어 앱에 푸시 알림을 전송하는 방법에 대해 설명합니다. 이 자습서에서는 WNS(Windows 푸시 알림 서비스)를 사용하여 Visual Studio에서 직접 quickstart 프로젝트에 푸시 알림을 추가합니다. 이 작업을 완료하면 레코드가 삽입될 때마다 모바일 서비스에서 푸시 알림을 전송합니다.

> [WACOM.NOTE]이제 모바일 서비스가 Azure 알림 허브와 통합되어 템플릿, 다중 플랫폼, 확장 등 추가적인 푸시 알림 기능을 지원합니다. 이 통합 기능은 현재 사전 검토 단계입니다. 자세한 내용은 [푸시 알림 시작하기](/ko-kr/documentation/articles/mobile-services-javascript-backend-windows-store-javascript-get-started-push/)를 참조하십시오.

이 자습서에서는 푸시 알림을 사용하도록 설정하는 다음 기본 단계를 단계별로 안내합니다.

1.  [푸시 알림을 위한 앱 등록 및 모바일 서비스 구성](#register)
2.  [생성된 푸시 알림 코드 업데이트](#update-scripts)
3.  [알림을 받기 위한 데이터 삽입](#test)

이 자습서는 모바일 서비스 quickstart를 기반으로 합니다. 이 자습서를 시작하기 전에 먼저 [모바일 서비스 시작하기](/en-us/develop/mobile/tutorials/get-started/) 또는 [데이터 시작하기](/en-us/develop/mobile/tutorials/get-started-with-data-js/)를 완료하여 프로젝트를 모바일 서비스에 연결해야 합니다. 모바일 서비스가 연결되지 않은 경우 푸시 알림 추가 마법사에서 대신 연결해줍니다.

앱 등록앱에서 푸시 알림 추가 및 구성
------------------------------------

[WACOM.INCLUDE [mobile-services-create-new-push-vs2013](../includes/mobile-services-create-new-push-vs2013.md)]

1.  생성된 push.register.js 코드 파일을 열고 장치의 설치 ID 및 채널을 가져오는 코드를 검사한 후 새 **channels** 테이블에 이 데이터를 삽입합니다.

    이 테이블은 푸시 알림 추가 마법사에 의해 모바일 서비스에 생성되었습니다. 이 코드에서는 앱이 활성화될 때마다 장치 등록이 시도됩니다.

2.  서버 탐색기에서 **Azure**, **모바일 서비스**, 서비스 이름, **channels**를 차례로 확장하고 insert.js 파일을 엽니다.

    모바일 서비스에 저장되는 이 파일에는, 클라이언트가 channels 테이블에 데이터를 삽입하여 장치를 등록해달라는 요청을 전송할 때 실행되는 JavaScript 코드가 포함되어 있습니다.

    **참고**

    이 파일의 초기 버전에는 기존 장치 등록을 확인하는 코드가 포함되어 있습니다. channels 테이블에 새 등록이 추가될 때 푸시 알림을 전송하는 코드도 포함되어 있습니다. 푸시 알림을 전송하는 코드는 다른 등록된 스크립트 파일에 포함될 수도 있습니다. 이 스크립트의 위치는 알림이 트리거되는 방법에 따라 다릅니다. 테이블에 대한 삽입, 업데이트, 삭제 또는 읽기 작업에 대해 예약된 작업으로서 또는 사용자 지정 API로서 스크립트를 등록할 수 있습니다. 자세한 내용은 [모바일 서비스에서 서버 스크립트 작업](http://go.microsoft.com/fwlink/p/?LinkID=287178)(영문)을 참조하십시오.

3.  F5 키를 눌러 앱을 실행하고, 모바일 서비스에서 알림이 즉시 수신되는지 확인합니다.

    이 알림은 새로운 channels 테이블에 행을 삽입하여(장치를 등록하여) 생성된 것입니다.

생성된 코드는 앱이 실행될 때 알림을 손쉽게 표시하는 데 도움이 되지만, 이것이 일반적으로 중요한 시나리오는 아닙니다. 다음 단계에서는 channels 테이블에서 알림 코드를 제거하고, TodoItem 테이블에서 변경된 내용으로 교체합니다.

코드 업데이트생성된 푸시 알림 코드 업데이트
-------------------------------------------

[WACOM.INCLUDE [mobile-services-create-new-push-vs2013-2](../includes/mobile-services-create-new-push-vs2013-2.md)]

앱 테스트앱에서 푸시 알림 테스트
--------------------------------

1.  Visual Studio에서 F5 키를 눌러 앱을 실행합니다.

2.  앱에서 **Insert a TodoItem**에 텍스트를 입력하고 **저장**을 클릭합니다.

   	![][13]

   	삽입이 완료되고 나면 앱이 WNS로부터 푸시 알림을 받습니다.

   	![][14]

다음 단계
---------

이 자습서는 모바일 서비스에서 제공하는 기본 푸시 알림 기능을 보여 줍니다. 플랫폼 간 알림 보내기, 구독 기반 라우팅, 대량 볼륨 등의 고급 기능이 앱에 필요한 경우 모바일 서비스와 함께 Azure 알림 허브를 사용하는 것이 좋습니다. 자세한 내용은 다음과 같은 알림 허브 토픽 중 하나를 참조하십시오.

-   [알림 허브 시작](/en-us/manage/services/notification-hubs/getting-started-windows-dotnet/)<br/>
    Windows 스토어 앱에서 알림 허브를 활용하는 방법을 알아봅니다.

-   [구독자에게 알림 보내기](/en-us/manage/services/notification-hubs/breaking-news-dotnet/)<br/>
    관심이 있는 범주에 대해 푸시 알림을 등록하고 받아보는 방법을 알아봅니다.

-   [사용자에게 알림 보내기](/en-us/manage/services/notification-hubs/notify-users/)<br/>
    모바일 서비스에서 장치와 상관없이 특정 사용자에게 푸시 알림을 보내는 방법을 알아봅니다.

-   [사용자에게 플랫폼 간 알림 보내기](/en-us/manage/services/notification-hubs/notify-users-xplat-mobile-services/)<br/>
    백 엔드에 플랫폼 특정 페이로드를 마련할 필요 없이 템플릿을 사용하여 모바일 서비스에서 푸시 알림을 보내는 방법을 알아봅니다.

다음의 모바일 서비스 항목에 대해서도 자세히 알아보십시오.

-   [데이터 시작](/en-us/develop/mobile/tutorials/get-started-with-data-js/)<br/>
    모바일 서비스를 사용하여 데이터를 저장 및 쿼리하는 방법을 자세히 알아봅니다.

-   [인증 시작](/en-us/develop/mobile/tutorials/get-started-with-users-js)<br/>
    Windows 계정으로 앱의 사용자를 인증하는 방법을 알아봅니다.

-   [모바일 서비스 서버 스크립트 참조](http://go.microsoft.com/fwlink/?LinkId=262293)<br/>
    서버 스크립트의 등록 및 사용에 대해 자세히 알아봅니다.

-   [모바일 서비스 HTML/JavaScript 방법 개념 참조](/en-us/develop/mobile/how-to-guides/work-with-html-js-client/)<br/>
    HTML 및 JavaScript와 함께 모바일 서비스를 사용하는 방법을 자세히 알아봅니다.


<!-- Anchors. -->
[Register your app for push notifications and configure Mobile Services]: #register
[Update the generated push notification code]: #update-scripts
[Insert data to receive notifications]: #test
[Next Steps]:#next-steps

<!-- Images. -->







[13]: ./media/mobile-services-windows-store-javascript-get-started-push/mobile-quickstart-push1.png
[14]: ./media/mobile-services-windows-store-javascript-get-started-push/mobile-quickstart-push2.png