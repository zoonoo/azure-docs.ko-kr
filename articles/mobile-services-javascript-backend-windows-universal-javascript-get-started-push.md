<properties pageTitle="Get started with push notification using a JavaScript backend mobile service" metaKeywords="" description="Learn how to use Azure Mobile Services and Notification Hubs to send push notifications to your universal Windows app." metaCanonical="" services="mobile-services,notification-hubs" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services" authors="glenga" solutions="mobile-services,notification-hubs" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="javascript" ms.topic="article" ms.date="09/15/2014" ms.author="glenga" />

# 모바일 서비스에서 푸시 알림 시작

[WACOM.INCLUDE [mobile-services-selector-get-started-push](../includes/mobile-services-selector-get-started-push.md)]

이 항목에서는 JavaScript 백 엔드와 함께 Azure 모바일 서비스를 사용하여 범용 Windows 앱에 푸시 알림을 보내는 방법을 보여 줍니다. 이 자습서에서는 범용 Windows 앱 프로젝트에서 Azure 알림 허브를 사용하여 푸시 알림을 사용하도록 설정합니다. 이 작업을 완료하면 TodoList 테이블에 레코드를 삽입할 때마다 모바일 서비스가 JavaScript 백 엔드에서 등록된 모든 Windows 스토어 및 Windows Phone 스토어 앱으로 푸시 알림을 보냅니다. 모바일 서비스를 통해 무료로 만드는 알림 허브는 모바일 서비스와 별도로 관리할 수 있으며, 다른 응용 프로그램과 서비스에서도 사용 가능합니다.

> [WACOM.NOTE]이 항목에서는 Visual Studio 2013 업데이트 3의 도구를 사용하여 모바일 서비스에서 범용 Windows 앱으로의 푸시 알림에 대한 지원을 추가하는 방법을 보여 줍니다. 같은 단계를 사용하여 모바일 서비스에서 Windows 스토어 또는 Windows Phone 스토어 8.1 앱으로의 푸시 알림을 추가할 수 있습니다. Visual Studio 2013 업데이트 3으로 업그레이드할 수 없거나 Windows 스토어 앱 솔루션에 모바일 서비스 프로젝트를 수동으로 추가하려는 경우에는 항목의 [이 버전][이 버전]을 참조하세요.

이 자습서에서는 푸시 알림을 사용하도록 설정하는 다음 기본 단계를 단계별로 안내합니다.

1.  [푸시 알림에 대해 앱 등록][푸시 알림에 대해 앱 등록]
2.  [푸시 알림을 전송하도록 서비스 업데이트][푸시 알림을 전송하도록 서비스 업데이트]
3.  [앱에서 푸시 알림 테스트][앱에서 푸시 알림 테스트]

이 자습서를 완료하려면 다음이 필요합니다.

-   활성 [Microsoft 스토어 계정][Microsoft 스토어 계정]
-   [Visual Studio 2013 Express for Windows][Visual Studio 2013 Express for Windows] 업데이트 3 이상 버전

## <span id="register"></span></a>푸시 알림에 대해 앱 등록

[WACOM.INCLUDE [mobile-services-create-new-push-vs2013](../includes/mobile-services-create-new-push-vs2013.md)]

1.  `\services\mobileServices\scripts` 프로젝트 폴더로 이동하여 생성된 \<*your\_service\_name*\>.push.register.js 스크립트 파일을 공유 `\js` 폴더에 복사한 다음 개별 Windows 및 WindowsPhone 앱 프로젝트에서 모두 이 파일을 삭제합니다.

2.  공유 `\js` 프로젝트 폴더에서 이 스크립트 파일을 열고 장치의 채널 URL을 알림 허브에 등록하는 *활성화된* 이벤트 수신기에서 코드를 찾은 다음 **done** promise 함수를 삭제합니다.

    이 자습서에서는 사용자 지정 API를 호출할 때가 아니라 새 항목을 삽입할 때 알림을 보냅니다.

3.  Windows 앱 프로젝트에서 default.html 파일을 열고 공유 `\js` 프로젝트 폴더에 대한 스크립트 파일 참조의 경로를 다음과 같이 변경합니다.

        <script src="/js/your_service_name.push.register.js"></script>

4.  WindowsPhone 앱 프로젝트에 대해 이 단계를 반복합니다.

    이제 두 프로젝트가 모두 푸시 등록 스크립트의 공유 버전을 사용합니다.

이제 앱에서 푸시 알림을 사용하도록 설정했으므로 모바일 서비스가 푸시 알림을 보내도록 업데이트해야 합니다.

## <span id="update-service"></span></a>푸시 알림을 전송하도록 서비스 업데이트

다음 단계에서는 TodoItem 테이블에 등록된 삽입 스크립트를 업데이트합니다. 모든 서버 스크립트 또는 백 엔드 서비스의 모든 위치에서도 비슷한 코드를 구현할 수 있습니다.

[WACOM.INCLUDE [mobile-services-javascript-update-script-notification-hubs](../includes/mobile-services-javascript-update-script-notification-hubs.md)]

## <span id="test"></span></a>앱에서 푸시 알림 테스트

[WACOM.INCLUDE [mobile-services-javascript-backend-windows-universal-test-push](../includes/mobile-services-javascript-backend-windows-universal-test-push.md)]

## <a name="next-steps"> </a>다음 단계

이 자습서에서는 Windows 스토어 앱에서 모바일 서비스 및 알림 허브를 사용하여 푸시 알림을 보내도록 설정하기 위한 기본 사항에 대해 설명했습니다. 다음으로는 태그를 사용하여 모바일 서비스에서 인증된 사용자에게만 푸시 알림을 보내는 방법을 보여 주는 다음 자습서인 [인증된 사용자에게 푸시 알림 보내기][인증된 사용자에게 푸시 알림 보내기]를 완료할 수 있습니다.

다음 항목에서 모바일 서비스 및 알림 허브에 대해 알아보세요.

-   [데이터 시작하기][데이터 시작하기]
    모바일 서비스를 사용하여 데이터를 저장 및 쿼리하는 방법에 대해 자세히 알아봅니다.

-   [인증 시작][인증 시작]
    모바일 서비스를 사용하여 서로 다른 계정 유형의 앱 사용자를 인증하는 방법에 대해 알아봅니다.

-   [알림 허브 정의][알림 허브 정의]
    모든 주요 클라이언트 플랫폼에 걸쳐 알림 허브가 앱에 알림을 전달하는 방법에 대해 알아봅니다.

-   [Azure 모바일 서비스용 HTML/JavaScript 클라이언트를 사용하는 방법][Azure 모바일 서비스용 HTML/JavaScript 클라이언트를 사용하는 방법]
    HTML 및 JavaScript 앱에서 모바일 서비스를 사용하는 방법을 자세히 알아봅니다.



  [mobile-services-selector-get-started-push]: ../includes/mobile-services-selector-get-started-push.md
  [이 버전]: /ko-kr/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-push
  [푸시 알림에 대해 앱 등록]: #register
  [푸시 알림을 전송하도록 서비스 업데이트]: #update-service
  [앱에서 푸시 알림 테스트]: #test
  [Microsoft 스토어 계정]: http://go.microsoft.com/fwlink/p/?LinkId=280045
  [Visual Studio 2013 Express for Windows]: http://go.microsoft.com/fwlink/?LinkId=257546
  [mobile-services-create-new-push-vs2013]: ../includes/mobile-services-create-new-push-vs2013.md
  [mobile-services-javascript-update-script-notification-hubs]: ../includes/mobile-services-javascript-update-script-notification-hubs.md
  [mobile-services-javascript-backend-windows-universal-test-push]: ../includes/mobile-services-javascript-backend-windows-universal-test-push.md
  [인증된 사용자에게 푸시 알림 보내기]: /ko-kr/documentation/articles/mobile-services-javascript-backend-windows-store-javascript-push-notifications-app-users/
  [데이터 시작하기]: /ko-kr/documentation/articles/mobile-services-javascript-backend-windows-universal-javascript-get-started-data
  [인증 시작]: /ko-kr/documentation/articles/mobile-services-javascript-backend-windows-universal-javascript-get-started-users
  [알림 허브 정의]: /ko-kr/documentation/articles/notification-hubs-overview/
  [Azure 모바일 서비스용 HTML/JavaScript 클라이언트를 사용하는 방법]: /ko-kr/documentation/articles/mobile-services-html-how-to-use-client-library
