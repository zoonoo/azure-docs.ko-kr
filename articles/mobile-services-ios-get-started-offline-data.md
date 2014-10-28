<properties linkid="develop-mobile-tutorials-get-started-offline-data-ios" urlDisplayName="Getting Started with Offline Data" pageTitle="Get started with offline data in Mobile Services (iOS) | Mobile Dev Center" metaKeywords="" description="Learn how to use Azure Mobile Services to cache and sync offline data in your iOS application" metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile" title="Get started with offline data sync in Mobile Services" authors="wesmc" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="01/01/1900" ms.author="wesmc"></tags>

# 모바일 서비스에서 오프라인 데이터 동기화 시작

<div class="dev-center-tutorial-selector sublanding">
<a href="/ko--kr/documentation/articles/mobile-services-windows-store-dotnet-get-started-offline-data" title="Windows 스토어 C#">Windows 스토어 C#</a>
<a href="/ko--kr/documentation/articles/mobile-services-windows-phone-get-started-offline-data" title="Windows Phone">Windows Phone</a>
<a href="/ko--kr/documentation/articles/mobile-services-ios-get-started-offline-data" title="iOS" class="current">iOS</a>
<a href="/ko--kr/documentation/articles/mobile-services-xamarin-ios-get-started-offline-data" title="Xamarin.iOS">Xamarin.iOS</a>
<a href="/ko--kr/documentation/articles/mobile-services-xamarin-android-get-started-offline-data" title="Xamarin.Android">Xamarin.Android</a>
</div>

이 자습서에서는 최종 사용자에게 네트워크 액세스가 없어도 사용 가능한 앱을 개발자가 작성할 수 있게 해주는 모바일 서비스의 오프라인 동기화 기능에 대해 설명합니다.

오프라인 동기화의 몇 가지 잠재적인 용도는 다음과 같습니다.

-   서버 데이터를 장치에 로컬로 캐시하여 앱 응답성 향상
-   일시적인 네트워크 연결 중단에도 앱 복원력 제공
-   최종 사용자가 네트워크에 액세스할 수 없는 경우에도 데이터를 만들고 수정할 수 있도록 허용하여 네트워크에 연결되지 않은 시나리오까지 지원
-   여러 장치 간에 데이터를 동기화하고 동일한 레코드를 두 개의 장치에서 수정할 때 충돌 감지

이 자습서에서는 [모바일 서비스 시작][모바일 서비스 시작] 또는 [데이터 시작][데이터 시작] 자습서에서 작성한 앱을 업데이트하여 Azure 모바일 서비스의 오프라인 기능을 지원하는 방법을 보여 줍니다. 그다음 연결이 끊긴 오프라인 시나리오에서 데이터를 추가하고, 해당 항목을 온라인 데이터베이스와 동기화한 후 Azure 관리 포털에 로그인하여 앱을 실행할 때 수행된 데이터 변경 사항을 확인합니다.

> [WACOM.NOTE] 이 자습서를 완료하려면 Azure 계정이 필요합니다. 계정이 없는 경우 몇 분 만에 무료 평가판 계정을 만들 수 있습니다. 자세한 내용은 [Azure 무료 평가판][Azure 무료 평가판]을 참조하세요.

## 샘플 앱 가져오기

**전체 자습서는 곧 제공될 예정입니다. 그전까지는 샘플 [iOS 오프라인 ToDo 목록][iOS 오프라인 ToDo 목록](영문) 및 [동반 블로그 게시물][동반 블로그 게시물](영문)을 참조하세요.**



  [Windows 스토어 C#]: /ko--kr/documentation/articles/mobile-services-windows-store-dotnet-get-started-offline-data "Windows 스토어 C#"
  [Windows Phone]: /ko--kr/documentation/articles/mobile-services-windows-phone-get-started-offline-data "Windows Phone"
  [iOS]: /ko--kr/documentation/articles/mobile-services-ios-get-started-offline-data "iOS"
  [Xamarin.iOS]: /ko--kr/documentation/articles/mobile-services-xamarin-ios-get-started-offline-data "Xamarin.iOS"
  [Xamarin.Android]: /ko--kr/documentation/articles/mobile-services-xamarin-android-get-started-offline-data "Xamarin.Android"
  [모바일 서비스 시작]: /ko--kr/documentation/articles/mobile-services-ios-get-started/
  [데이터 시작]: /ko--kr/documentation/articles/mobile-services-ios-get-started-data/
  [Azure 무료 평가판]: http://www.windowsazure.com/ko--kr/pricing/free-trial/?WT.mc_id=AE564AB28
  [iOS 오프라인 ToDo 목록]: https://github.com/Azure/mobile-services-samples/tree/master/TodoOffline/iOS/blog20140611
  [동반 블로그 게시물]: http://aka.ms/iosoffline
