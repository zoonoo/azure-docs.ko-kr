<properties 
	pageTitle="데이터에 페이징 추가(Windows Phone) | 모바일 개발자 센터" 
	description="페이징을 사용하여 모바일 서비스에서 Windows Phone 앱에 반환되는 데이터의 양을 관리하는 방법에 대해 알아봅니다." 
	services="mobile-services" 
	documentationCenter="windows" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-phone" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="09/25/2014" 
	ms.author="glenga"/>

# 페이징을 사용하여 모바일 서비스 쿼리 구체화

[AZURE.INCLUDE [mobile-services-selector-add-paging-data](../includes/mobile-services-selector-add-paging-data.md)]

이 항목에서는 페이징을 사용하여 Azure 모바일 서비스에서 Windows Phone 앱에 반환되는 데이터의 양을 관리하는 방법을 보여 줍니다. 이 자습서에서는 클라이언트에서 **Take** 및 **Skip** 쿼리 메서드를 사용하여 데이터의 특정 "페이지"를 요청합니다.

>[AZURE.NOTE]모바일 장치 클라이언트에서 데이터 오버플로를 방지하기 위해 모바일 서비스는 기본적으로 응답의 최대 항목 수를 50개로 설정하는 자동 페이지 제한을 구현합니다. 페이지 크기를 지정하여 응답에서 최대 1,000개 항목을 명시적으로 요청할 수 있습니다.

이 자습서는 이전 자습서인 [기존 앱에 모바일 서비스 추가](/ko-kr/documentation/articles/mobile-services-windows-phone-get-started-data/)의 단계와 샘플 앱을 기반으로 합니다. 이 자습서를 시작하기 전에 먼저 이 자습서를 완료해야 합니다.  

[AZURE.INCLUDE [mobile-services-windows-dotnet-paging](../includes/mobile-services-windows-dotnet-paging.md)]

## <a name="next-steps"> </a>다음 단계

이제 모바일 서비스의 데이터 작업 기본 사항을 설명하는 자습서를 마쳤습니다. 다음의 모바일 서비스 항목에 대해서도 자세히 알아보세요.

* [인증 시작](영문)
  <br/>Windows 계정을 사용하여 앱 사용자를 인증하는 방법을 알아봅니다.

* [푸시 알림 시작] 
  <br/>앱에 기본적인 푸시 알림을 보내는 방법을 알아봅니다.

<!-- Anchors. -->

[다음 단계]:#next-steps

<!-- Images. -->


<!-- URLs. -->
[모바일 서비스 시작](영문): /ko-kr/documentation/articles/mobile-services-windows-phone-get-started/
[데이터 시작]: /ko-kr/documentation/articles/mobile-services-windows-phone-get-started-data/
[인증 시작](영문): /ko-kr/documentation/articles/mobile-services-windows-phone-get-started-users/
[푸시 알림 시작]: /ko-kr/documentation/articles/mobile-services-windows-phone-get-started-push/


[관리 포털]: https://manage.windowsazure.com/



<!--HONumber=42-->
