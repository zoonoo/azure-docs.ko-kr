<properties pageTitle="Windows Phone 앱에서 사용자 지정 API 호출 - 모바일 서비스" metaKeywords="" description="사용자 지정 API를 정의한 다음 Microsoft Azure 모바일 서비스를 사용하는 Windows Phone 앱에서 이를 호출하는 방법에 대해 알아봅니다." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Call a custom API from the client" authors="glenga"  solutions="" writer="glenga" manager="dwrede" editor=""  />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-phone" ms.devlang="dotnet" ms.topic="article" ms.date="09/26/2014" ms.author="glenga" />

# 클라이언트에서 사용자 지정 API 호출

[WACOM.INCLUDE [mobile-services-selector-call-custom-api](../includes/mobile-services-selector-call-custom-api.md)]

이 항목에서는 Windows Phone 앱에서 사용자 지정 API를 호출하는 방법을 보여 줍니다. 사용자 지정 API는 삽입, 업데이트, 삭제 또는 읽기 작업에 매핑되지 않는 서버 기능을 노출하는 사용자 지정 끝점을 정의할 수 있게 합니다. 사용자 지정 API를 사용하면 HTTP 메시지 헤더 읽기와 설정 및 JSON 이외의 메시지 본문 형식 정의를 비롯하여 더 세밀하게 메시징을 제어할 수 있습니다.

이 항목에서 만드는 사용자 지정 API는 테이블의 모든 todo 항목에 대해 완료 플래그를 `true`로 설정하는 단일 POST 요청을 보내는 기능을 제공합니다. 이 사용자 지정 API가 없으면 클라이언트는 테이블의 각 todo 항목에 대해 플래그를 업데이트하도록 개별 요청을 보내야 합니다.

[기존 앱에 모바일 서비스 추가](/ko-kr/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-data/) 자습서를 완료했을 때 만든 앱에 이 기능을 추가합니다. 그러려면 다음 단계를 완료합니다.

1. [사용자 지정 API 정의]
2. [사용자 지정 API를 호출하도록 앱 업데이트]
3. [앱 테스트] 

이 자습서는 간단한 TodoList 앱인 GetStartedWithData 샘플을 기반으로 합니다. 이 자습서를 시작하기 전에 먼저 [기존 앱에 모바일 서비스 추가](/ko-kr/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-data/)를 완료해야 합니다.

## <a name="define-custom-api"></a>사용자 지정 API 정의

[WACOM.INCLUDE [mobile-services-dotnet-backend-create-custom-api](../includes/mobile-services-dotnet-backend-create-custom-api.md)]

[WACOM.INCLUDE [mobile-services-windows-phone-call-custom-api](../includes/mobile-services-windows-phone-call-custom-api.md)]


## 다음 단계

지금까지 사용자 지정 API를 만들어 Windows Phone 앱에서 호출했습니다. 이제 다음 모바일 서비스 항목에 대해 자세히 알아보세요.

* [모바일 서비스 서버 스크립트 참조]
  <br/>사용자 지정 API 만들기에 대해 자세히 알아봅니다.

* [원본 제어에 서버 스크립트 저장]
  <br/> 소스 제어 기능을 사용하여 사용자 지정 API 스크립트 코드를 보다 쉽고 안전하게 개발하고 게시하는 방법에 대해 알아봅니다.

<!-- Anchors. -->
[사용자 지정 API 정의]: #define-custom-api
[사용자 지정 API를 호출하도록 앱 업데이트]: #update-app
[앱 테스트]: #test-app
[다음 단계]: #next-steps

<!-- Images. -->

<!-- URLs. -->
[모바일 서비스 서버 스크립트 참조]: http://go.microsoft.com/fwlink/?LinkId=262293
[모바일 서비스 시작]: /ko-kr/documentation/articles/mobile-services-windows-phone-get-started/
[데이터 작업 시작]: /ko-kr/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-data/
[인증 시작]: /ko-kr/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-users/
[푸시 알림 시작]: /ko-kr/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-push/

[원본 제어에 서버 스크립트 저장]: /ko-kr/documentation/articles/mobile-services-store-scripts-source-control
