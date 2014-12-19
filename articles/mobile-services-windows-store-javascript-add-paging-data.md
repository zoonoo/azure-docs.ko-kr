<properties pageTitle="데이터에 페이징 추가(JavaScript) - Azure 모바일 서비스" metaKeywords="" description="Learn how to use paging to manage the amount of data returned to your Windows Store JavaScript app from Mobile Services." metaCanonical="http://www.windowsazure.com/ko-kr/develop/mobile/tutorials/add-paging-to-data-dotnet/" services="mobile-services" documentationCenter="Mobile" title="Refine Mobile Services queries with paging" authors="glenga" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="javascript" ms.topic="article" ms.date="09/24/2014" ms.author="glenga" />


# 페이징을 사용하여 모바일 서비스 쿼리 구체화

[WACOM.INCLUDE [mobile-services-selector-add-paging-data](../includes/mobile-services-selector-add-paging-data.md)]


이 항목에서는 페이징을 사용하여 Azure 모바일 서비스에서 Windows 스토어 앱으로 반환되는 데이터 양을 관리하는 방법을 보여 줍니다. 이 자습서에서는 클라이언트에서 **Take** 및 **Skip** 쿼리 메서드를 사용하여 데이터의 특정 "페이지"를 요청합니다.

>[WACOM.NOTE]모바일 장치 클라이언트에서 데이터 오버플로를 방지하기 위해 모바일 서비스는 기본적으로 응답의 최대 항목 수를 50개로 설정하는 자동 페이지 제한을 구현합니다. 페이지 크기를 지정하여 응답에서 최대 1,000개 항목을 명시적으로 요청할 수 있습니다.

이 자습서는 이전 자습서인 [데이터 작업 시작]의 단계 및 샘플 앱을 기반으로 합니다. 이 자습서를 시작하기 전에 적어도 데이터 계열 작업에 대한 첫 번째 자습서인 [데이터 작업 시작] 자습서를 완료해야 합니다. 

[WACOM.INCLUDE [mobile-services-javascript-paging](../includes/mobile-services-javascript-paging.md)]

## <a name="next-steps"> </a>다음 단계

이제 모바일 서비스의 데이터 작업 기본 사항을 설명하는 자습서를 마쳤습니다. 다음의 모바일 서비스 항목에 대해서도 자세히 알아보세요.

* [인증 시작]
  <br/>Windows 계정을 사용하여 앱 사용자를 인증하는 방법을 알아봅니다.

* [푸시 알림 시작] 
  <br/>매우 기본적인 푸시 알림을 앱에 보내는 방법을 알아봅니다.

<!-- Anchors. -->

[다음 단계]:#next-steps

<!-- Images. -->


<!-- URLs. -->
[모바일 서비스 시작]: /ko-kr/documentation/articles/mobile-services-windows-store-get-started/
[데이터 작업 시작]: /ko-kr/documentation/articles/mobile-services-windows-store-javascript-get-started-data/
[인증 시작]: /ko-kr/documentation/articles/mobile-services-windows-store-javascript-get-started-users/
[푸시 알림 시작]: /ko-kr/documentation/articles/mobile-services-windows-store-javascript-get-started-push/

[관리 포털]: https://manage.windowsazure.com/
