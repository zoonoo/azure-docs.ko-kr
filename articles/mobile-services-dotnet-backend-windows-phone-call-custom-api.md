<properties pageTitle="Call a custom API from a Windows Phone app - Mobile Services" metaKeywords="" description="Learn how to define a custom API and then call it from a Windows Phone app that use Windows Azure Mobile Services." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Call a custom API from the client" authors="glenga"  solutions="" writer="glenga" manager="" editor=""  />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-phone" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="glenga" />

# 클라이언트에서 사용자 지정 API 호출

<div class="dev-center-tutorial-selector sublanding"><a href="/ko-kr/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-call-custom-api" title="Windows 스토어 C#">Windows 스토어 C#</a><a href="/ko-kr/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-call-custom-api" title="Windows 스토어 JavaScript">Windows 스토어 JavaScript</a><a href="/ko-kr/documentation/articles/mobile-services-dotnet-backend-windows-phone-call-custom-api" title="Windows Phone" class="current">Windows Phone</a><a href="/ko-kr/documentation/articles/mobile-services-dotnet-backend-ios-call-custom-api" title="iOS">iOS</a><a href="/ko-kr/documentation/articles/mobile-services-dotnet-backend-android-call-custom-api" title="Android" class="current">Android</a>
</div>

<div class="dev-center-tutorial-subselector"><a href="/ko-kr/documentation/articles/mobile-services-dotnet-backend-windows-phone-call-custom-api" title=".NET 백 엔드" class="current">.NET 백 엔드</a> | <a href="/ko-kr/documentation/articles/mobile-services-windows-phone-call-custom-api"  title="JavaScript 백 엔드">JavaScript 백 엔드</a></div>

이 항목에서는 Windows Phone 앱에서 사용자 지정 API를 호출하는 방법을 보여 줍니다. 사용자 지정 API는 삽입, 업데이트, 삭제 또는 읽기 작업에 매핑되지 않는 서버 기능을 노출하는 사용자 지정 끝점을 정의할 수 있게 합니다. 사용자 지정 API를 사용하면 HTTP 메시지 헤더 읽기와 설정 및 JSON 이외의 메시지 본문 형식 정의를 비롯하여 더 효율적으로 메시징을 제어할 수 있습니다.

이 항목에서 만드는 사용자 지정 API는 테이블의 모든 todo 항목에 대해 완료 플래그를 `true`로 설정하는 단일 POST 요청을 보내는 기능을 제공합니다. 이 사용자 정의 API가 없으면 클라이언트는 테이블의 각 todo 항목에 대해 플래그를 업데이트하도록 개별 요청을 보내야 합니다.

[모바일 서비스 시작][모바일 서비스 시작] 또는 [데이터 시작][데이터 시작] 자습서를 마쳤을 때 만든 앱에 이 기능을 추가합니다. 그러려면 다음 단계를 완료합니다.

1.  [사용자 지정 API 정의][사용자 지정 API 정의]
2.  [사용자 지정 API를 호출하도록 앱 업데이트][사용자 지정 API를 호출하도록 앱 업데이트]
3.  [앱 테스트][앱 테스트]

이 자습서는 모바일 서비스 퀵 스타트를 기반으로 합니다. 이 자습서를 시작하기 전에 먼저 [모바일 서비스 시작][모바일 서비스 시작] 또는 [데이터 시작][데이터 시작]을 완료해야 합니다. 이 자습서에서는 Visual Studio 2012 Express for Windows Phone을 사용합니다.

## <a name="define-custom-api"></a>사용자 지정 API 정의

[WACOM.INCLUDE [mobile-services-dotnet-backend-create-custom-api](../includes/mobile-services-dotnet-backend-create-custom-api.md)]

[WACOM.INCLUDE [mobile-services-windows-phone-call-custom-api](../includes/mobile-services-windows-phone-call-custom-api.md)]

## 다음 단계

지금까지 사용자 지정 API를 만들어 Windows Phone 앱에서 호출했습니다. 이제 다음 모바일 서비스 항목에 대해 자세히 알아보십시오.

-   [모바일 서비스 서버 스크립트 참조][모바일 서비스 서버 스크립트 참조]
    사용자 지정 API 만들기에 대해 알아봅니다.

-   [원본 제어에 서버 스크립트 저장][원본 제어에 서버 스크립트 저장]
     원본 제어 기능을 사용하여 더 쉽고 안전하게 사용자 지정 API 스크립트 코드를 개발하고 게시하는 방법을 알아봅니다.

<!-- Anchors. --> 
<!-- Images. --> 
<!-- URLs. -->

  [모바일 서비스 시작]: /ko-kr/documentation/articles/mobile-services-windows-phone-get-started/
  [데이터 시작]: /ko-kr/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-data/
  [사용자 지정 API 정의]: #define-custom-api
  [사용자 지정 API를 호출하도록 앱 업데이트]: #update-app
  [앱 테스트]: #test-app
  [mobile-services-dotnet-backend-create-custom-api]: ../includes/mobile-services-dotnet-backend-create-custom-api.md
  [mobile-services-windows-phone-call-custom-api]: ../includes/mobile-services-windows-phone-call-custom-api.md
  [모바일 서비스 서버 스크립트 참조]: http://go.microsoft.com/fwlink/?LinkId=262293
  [원본 제어에 서버 스크립트 저장]: /ko-kr/documentation/articles/mobile-services-store-scripts-source-control
