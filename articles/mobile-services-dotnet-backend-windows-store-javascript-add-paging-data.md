<properties pageTitle="Add paging to data (JavaScript) - Azure Mobile Services" metaKeywords="" description="Learn how to use paging to manage the amount of data returned to your Windows Store JavaScript app from Mobile Services." metaCanonical="http://www.windowsazure.com/ko-kr/develop/mobile/tutorials/add-paging-to-data-dotnet/" services="" documentationCenter="Mobile" title="Refine Mobile Services queries with paging" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="javascript" ms.topic="article" ms.date="01/01/1900" ms.author="glenga"></tags>

# 페이징을 사용하여 모바일 서비스 쿼리 구체화

<div class="dev-center-tutorial-selector sublanding"><a href="/ko-kr/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-add-paging-data" title="Windows 스토어 C#">Windows 스토어 C#</a><a href="/ko-kr/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-add-paging-data" title="Windows 스토어 JavaScript" class="current">Windows 스토어 JavaScript</a><a href="/ko-kr/documentation/articles/mobile-services-dotnet-backend-windows-phone-add-paging-data" title="Windows Phone" class="current">Windows Phone</a>
<!--<a href="/ko-kr/documentation/articles/mobile-services-ios-add-paging-data" title="iOS">iOS</a><a href="/ko-kr/documentation/articles/mobile-services-android-add-paging-data" title="Android">Android</a>-->
</div>

<div class="dev-center-tutorial-subselector"><a href="/ko-kr/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-add-paging-data" title=".NET 백 엔드" class="current">.NET 백 엔드</a> | <a href="/ko-kr/documentation/articles/mobile-services-windows-store-javascript-add-paging-data"  title="JavaScript 백 엔드">JavaScript 백 엔드</a></div>

이 항목에서는 페이징을 사용하여 Azure 모바일 서비스에서 Windows 스토어 앱으로 반환되는 데이터 양을 관리하는 방법을 보여 줍니다. 이 자습서에서는 클라이언트에서 **Take** 및 **Skip** 쿼리 메서드를 사용하여 데이터의 특정 "페이지"를 요청합니다.

> [WACOM.NOTE]모바일 장치 클라이언트에서 데이터 오버플로 방지를 위해 모바일 서비스에서는 자동 페이지 제한을 구현합니다. 기본값은 응답 시 최대 50개 항목입니다. 페이지 크기를 지정하여 응답에서 최대 1,000개 항목을 명시적으로 요청할 수 있습니다.

이 자습서는 이전 자습서인 [데이터 시작][데이터 시작]의 단계 및 샘플 앱을 기반으로 합니다. 이 자습서를 시작하기 전에 적어도 데이터 계열 작업에 대한 첫 번째 자습서인 [데이터 시작][데이터 시작] 자습서를 완료해야 합니다.

[WACOM.INCLUDE [mobile-services-javascript-paging][mobile-services-javascript-paging]]

## <a name="next-steps"> </a>다음 단계

이제 모바일 서비스의 데이터 작업 기본 사항을 설명하는 자습서를 마쳤습니다. 다음의 모바일 서비스 항목에 대해서도 자세히 알아보십시오.

-   [인증 시작하기][인증 시작하기]
    Windows 계정으로 앱의 사용자를 인증하는 방법에 대해 알아보십시오.

-   [푸시 알림 시작][푸시 알림 시작]
    기본적인 푸시 알림을 앱에 보내는 방법을 알아봅니다.

<!-- Anchors. --> 
<!-- Images. --> 
<!-- URLs. -->

  [Windows 스토어 C#]: /ko-kr/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-add-paging-data "Windows 스토어 C#"
  [Windows 스토어 JavaScript]: /ko-kr/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-add-paging-data "Windows 스토어 JavaScript"
  [Windows Phone]: /ko-kr/documentation/articles/mobile-services-dotnet-backend-windows-phone-add-paging-data "Windows Phone"
  [.NET 백 엔드]: /ko-kr/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-add-paging-data ".NET 백 엔드"
  [JavaScript 백 엔드]: /ko-kr/documentation/articles/mobile-services-windows-store-javascript-add-paging-data "JavaScript 백 엔드"
  [데이터 시작]: /ko-kr/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/
  [mobile-services-javascript-paging]: ../includes/mobile-services-javascript-paging.md
  [인증 시작하기]: /ko-kr/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-users/
  [푸시 알림 시작]: /ko-kr/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-push/
