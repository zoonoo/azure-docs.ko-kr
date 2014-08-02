<properties pageTitle="Refine Mobile Services queries with paging (Windows Store) | Mobile Dev Center" metaKeywords="" description="Learn how to use paging to manage the amount of data returned to your Windows Store app from Mobile Services." metaCanonical="" services="" documentationCenter="Mobile" title="Refine Mobile Services queries with paging" authors="glenga" solutions="" manager="" editor="" />

페이징을 사용하여 모바일 서비스 쿼리 구체화
===========================================

> [AZURE.SELECTOR-LIST (Platform | Backend )]
>  - [(Windows 스토어 C\# | .NET)](mobile-services-dotnet-backend-windows-store-dotnet-add-paging-data.md)
>  - [(Windows 스토어 C\# | JavaScript)](mobile-services-windows-store-dotnet-add-paging-data.md)
>  - [(Windows 스토어 JavaScript | .NET)](mobile-services-dotnet-backend-windows-store-javascript-add-paging-data.md)
>  - [(Windows 스토어 JavaScript | JavaScript)](mobile-services-windows-store-javascript-add-paging-data.md)
>  - [(Windows Phone | .NET)](mobile-services-dotnet-backend-windows-phone-add-paging-data) 
>  - [(Windows Phone | JavaScript)](mobile-services-windows-phone-add-paging-data)
>  - [(iOS | JavaScript)](mobile-services-ios-add-paging-data)
>  - [(Android | JavaScript)](mobile-services-android-add-paging-data) 
>  - [(HTML | .NET)](mobile-services-html-add-paging-data) 
>  - [(Xamarin iOS | .NET)](partner-xamarin-mobile-services-ios-add-paging-data) 
>  - [(Xamarin Android | .NET)](partner-xamarin-mobile-services-android-add-paging-data)

이 항목에서는 페이징을 사용하여 Azure 모바일 서비스에서 Windows 스토어 앱으로 반환되는 데이터 양을 관리하는 방법을 보여 줍니다. 이 자습서에서는 클라이언트의 **Take** 및 **Skip** 쿼리 메서드를 사용하여 특정 "페이지"의 데이터를 요청합니다.

> [WACOM.NOTE]모바일 장치 클라이언트에서 데이터 오버플로를 방지하기 위해 모바일 서비스는 기본적으로 응답의 최대 항목 수를 50개로 설정하는 자동 페이지 제한을 구현합니다. 페이지 크기를 지정하여 응답의 항목 수를 명시적으로 최대 1,000개까지 요청할 수 있습니다.

이 자습서는 이전 자습서인 [데이터 시작](/en-us/documentation/articles/mobile-services-windows-store-dotnet-get-started-data/)의 단계 및 샘플 앱을 기반으로 합니다. 이 자습서를 시작하기 전에 적어도 데이터 시리즈 작업의 첫 번째 자습서인 [데이터 시작](/en-us/documentation/articles/mobile-services-windows-store-dotnet-get-started-data/)을 완료해야 합니다.

[WACOM.INCLUDE [mobile-services-windows-dotnet-paging](../includes/mobile-services-windows-dotnet-paging.md)]

다음 단계
---------

이제 모바일 서비스 데이터 작업의 기본 사항을 설명하는 자습서 집합을 마쳤습니다. 다음의 모바일 서비스 항목에 대해서도 자세히 알아보십시오.

* [인증 시작]
  <br/>Windows 계정으로 앱의 사용자를 인증하는 방법을 알아봅니다.

* [푸시 알림 시작] 
  <br/>기본적인 푸시 알림을 앱에 보내는 방법을 알아봅니다.
  
* [모바일 서비스 .NET 방법 개념 참조]
  <br/>.NET과 함께 모바일 서비스를 사용하는 방법을 자세히 알아봅니다.

<!-- Anchors. -->

[Next Steps]:#next-steps

<!-- Images. -->


<!-- URLs. -->
[Get started with Mobile Services]: /en-us/documentation/articles/mobile-services-windows-store-get-started/
[Get started with data]: /en-us/documentation/articles/mobile-services-windows-store-dotnet-get-started-data/
[인증 시작]: /en-us/documentation/articles/mobile-services-windows-store-dotnet-get-started-users/
[푸시 알림 시작]: /en-us/documentation/articles/mobile-services-windows-store-dotnet-get-started-push/

[Management Portal]: https://manage.windowsazure.com/
[모바일 서비스 .NET 방법 개념 참조]: /en-us/develop/mobile/how-to-guides/work-with-net-client-library
