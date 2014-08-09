<properties urlDisplayName="Call a custom API from the client" pageTitle="Call a custom API from a Windows Store client - Mobile Services" metaKeywords="" description="Learn how to define a custom API and then call it from a Windows Store app that use Azure Mobile Services." metaCanonical="" services="" documentationCenter="" title="Call a custom API from the client" authors="glenga" solutions="" manager="" editor="" />

클라이언트에서 사용자 지정 API 호출
===================================

[Windows 스토어 C\#](/ko-kr/documentation/articles/mobile-services-windows-store-dotnet-call-custom-api "Windows 스토어 C#")[Windows 스토어 JavaScript](/ko-kr/documentation/articles/mobile-services-windows-store-javascript-call-custom-api "Windows 스토어 JavaScript")[Windows Phone](/ko-kr/documentation/articles/mobile-services-windows-phone-call-custom-api "Windows Phone")[iOS](/ko-kr/documentation/articles/mobile-services-ios-call-custom-api "iOS")[Android](/ko-kr/documentation/articles/mobile-services-android-call-custom-api "Android") [.NET 백 엔드](/ko-kr/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-call-custom-api ".NET 백 엔드") | [JavaScript 백 엔드](/ko-kr/documentation/articles/mobile-services-windows-store-dotnet-call-custom-api "JavaScript 백 엔드")

이 항목에서는 Windows 스토어 앱에서 사용자 지정 API를 호출하는 방법을 보여 줍니다. 사용자 지정 API는 삽입, 업데이트, 삭제 또는 읽기 작업에 매핑되지 않는 서버 기능을 노출하는 사용자 지정 끝점을 지정할 수 있게 합니다. 사용자 지정 API를 사용하면 HTTP 메시지 헤더 읽기와 설정 및 JSON 이외의 메시지 본문 형식 정의를 비롯하여 더 효율적으로 메시징을 제어할 수 있습니다.

이 항목에서 만드는 사용자 지정 API는 테이블의 모든 할 일 항목에 대해 완료 플래그를 `true`로 설정하는 단일 POST 요청을 보내는 기능을 제공합니다. 이 사용자 정의 API가 없으면 클라이언트는 테이블의 각 할 일 항목에 대해 플래그를 업데이트하도록 개별 요청을 보내야 합니다.

[모바일 서비스 시작](/ko-kr/documentation/articles/mobile-services-windows-store-get-started/) 또는 [데이터 시작](/ko-kr/documentation/articles/mobile-services-windows-store-dotnet-get-started-data/) 자습서를 마쳤을 때 만든 앱에 이 기능을 추가합니다. 그러려면 다음 단계를 완료합니다.

1.  [사용자 지정 API 정의](#define-custom-api)
2.  [사용자 지정 API를 요청하도록 앱 업데이트](#update-app)
3.  [앱 테스트](#test-app)

이 자습서는 모바일 서비스 quickstart를 기반으로 합니다. 이 자습서를 시작하기 전에 먼저 [모바일 서비스 시작](/ko-kr/documentation/articles/mobile-services-windows-store-get-started/) 또는 [데이터 시작](/ko-kr/documentation/articles/mobile-services-windows-store-dotnet-get-started-data/)을 완료해야 합니다. 이 자습서에서는 Visual Studio 2012 Express for Windows 8을 사용합니다.

사용자 지정 API 정의
--------------------

[WACOM.INCLUDE [mobile-services-create-custom-api](../includes/mobile-services-create-custom-api.md)]

[WACOM.INCLUDE [mobile-services-windows-store-dotnet-call-custom-api](../includes/mobile-services-windows-store-dotnet-call-custom-api.md)]

다음 단계
---------

지금까지 사용자 지정 API를 만들어 Windows 스토어 앱에서 호출했습니다. 이제 다음 모바일 서비스 항목에 대해 자세히 알아보십시오.

-   [정기 알림을 지원하는 사용자 지정 API 정의](/ko-kr/documentation/articles/mobile-services-windows-store-dotnet-create-pull-notifications)
    
    사용자 지정 API를 사용하여 Windows 스토어 앱에서 정기 알림을 지원하는 방법을 알아봅니다. 정기 알림을 사용하면 Windows에서 주기적으로 사용자 지정 API 끝점에 액세스하고 타일 특정 형식으로 반환된 XML을 사용하여 시작 메뉴의 앱 타일을 업데이트합니다.

-   [모바일 서비스 서버 스크립트 참조](http://go.microsoft.com/fwlink/?LinkId=262293)
    
    사용자 지정 API 만들기에 대해 알아봅니다.

-   [원본 제어에 서버 스크립트 저장](/ko-kr/documentation/articles/mobile-services-store-scripts-source-control)
     
    원본 제어 기능을 사용하여 더 쉽고 안전하게 사용자 지정 API 스크립트 코드를 개발하고 게시하는 방법을 알아봅니다.


