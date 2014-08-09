<properties pageTitle="Get Started with Azure Mobile Services for iOS apps" metaKeywords="Azure iOS application, mobile service iOS, getting started Azure iOS" description="Follow this tutorial to get started using Azure Mobile Services for iOS development. " metaCanonical="" services="" documentationCenter="Mobile" title="Get started with Mobile Services" authors="glenga" solutions="" manager="" editor="" />

모바일 서비스 시작
==================

[Windows 스토어](/ko-kr/documentation/articles/mobile-services-windows-store-get-started "Windows 스토어") [Windows Phone](/ko-kr/documentation/articles/mobile-services-windows-phone-get-started "Windows Phone") [iOS](/ko-kr/documentation/articles/mobile-services-ios-get-started "iOS") [Android](/ko-kr/documentation/articles/mobile-services-android-get-started "Android") [HTML](/ko-kr/documentation/articles/mobile-services-html-get-started "HTML") [Xamarin.iOS](/ko-kr/documentation/articles/partner-xamarin-mobile-services-ios-get-started "Xamarin.iOS") [Xamarin.Android](/ko-kr/documentation/articles/partner-xamarin-mobile-services-android-get-started "Xamarin.Android") [Sencha](/ko-kr/documentation/articles/partner-sencha-mobile-services-get-started/ "Sencha") [PhoneGap](/ko-kr/documentation/articles/mobile-services-javascript-backend-phonegap-get-started/ "PhoneGap")

[.NET 백 엔드](/ko-kr/documentation/articles/mobile-services-dotnet-backend-ios-get-started/ ".NET 백 엔드") | [JavaScript 백 엔드](/ko-kr/documentation/articles/mobile-services-ios-get-started/ "JavaScript 백 엔드")

이 자습서는 Azure 모바일 서비스를 사용하여 iOS 앱에 클라우드 기반 백 엔드 서비스를 추가하는 방법을 보여 줍니다.

동영상을 시청하려는 경우 오른쪽에 있는 클립은 이 자습서와 동일한 단계를 따릅니다.

[자습서 보기](http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/iOS-Creating-your-first-app-using-the-Windows-Azure-Mobile-Services-Quickstart) [동영상 재생](http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/iOS-Creating-your-first-app-using-the-Windows-Azure-Mobile-Services-Quickstart) 9:38

이 자습서에서는 새 모바일 서비스와 새 모바일 서비스에 앱 데이터를 저장하는 간단한 *할 일 모음* 앱을 둘 다 만듭니다. 만드는 모바일 서비스는 서버 쪽 비즈니스 논리에 JavaScript를 사용합니다. Visual Studio를 사용하여 지원되는 .NET 언어로 서버 쪽 비즈니스 논리를 작성할 수 있게 해 주는 모바일 서비스를 만들려면 이 토픽의 [.NET 백 엔드 버전]을 참조하십시오.

완성된 앱의 스크린샷은 다음과 같습니다.

![][0]

이 자습서를 완료하려면 XCode 4.5 및 iOS 5.0 이상 버전이 필요합니다.

**참고**

이 자습서를 완료하려면 Azure 계정이 필요합니다. 계정이 없는 경우 몇 분 만에 무료 평가판 계정을 만들 수 있습니다. 자세한 내용은 [Azure 무료 평가판](http://www.windowsazure.com/ko-kr/pricing/free-trial/?WT.mc_id=AE564AB28&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fen-us%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-ios%2F)을 참조하십시오.

새 모바일 서비스 만들기
-----------------------

[WACOM.INCLUDE [mobile-services-create-new-service](../includes/mobile-services-create-new-service.md)]

새 앱 만들기새 iOS 앱 만들기
----------------------------

모바일 서비스를 만든 후 관리 포털에서 쉬운 빠른 시작을 따라 모바일 서비스에 연결할 새 앱을 만들거나 기존 앱을 수정할 수 있습니다.

이 섹션에서는 모바일 서비스에 연결된 새 iOS 앱을 만듭니다.

1.  관리 포털에서 **모바일 서비스**를 클릭한 후 방금 만든 모바일 서비스를 클릭합니다.

2.  빠른 시작 탭에서 **플랫폼 선택** 아래의 **iOS**를 클릭하고 **새 iOS 앱 만들기**를 확장합니다.

    ![][6]

    This displays the three easy steps to create an iOS app connected to your mobile service.

	![][7]

3.  아직 수행하지 않은 경우 [Xcode] v4.4 이상 버전을 다운로드하여 설치합니다.

4.  **TodoItems 테이블 만들기**를 클릭하여 앱 데이터를 저장할 테이블을 만듭니다.

5.  **앱 다운로드 및 실행**에서 **다운로드**를 클릭합니다.

모바일 서비스 iOS SDK와 함께 모바일 서비스에 연결된 샘플 *할 일 모음* 응용 프로그램에 대한 프로젝트가 다운로드됩니다. 압축된 프로젝트 파일을 로컬 컴퓨터에 저장하고 저장 위치를 기록해 둡니다.

새 iOS 앱 실행
--------------

[WACOM.INCLUDE [mobile-services-ios-run-app](../includes/mobile-services-ios-run-app.md)]

1.  관리 포털로 돌아가서 **데이터** 탭을 클릭한 후 **TodoItems** 테이블을 클릭합니다.

    ![](./media/mobile-services-ios-get-started/mobile-data-tab.png)

    이 명령을 사용하면 앱이 테이블에 삽입한 데이터를 찾아볼 수 있습니다.

    ![](./media/mobile-services-ios-get-started/mobile-data-browse.png)

## 다음 단계 
이제 빠른 시작을 완료했으며 모바일 서비스에서 중요한 추가 작업을 수행하는 방법을 알아보겠습니다.
 
* [데이터 시작]
	<br/>모바일 서비스를 사용하여 데이터를 저장 및 쿼리하는 방법을 자세히 알아봅니다.
 
* [인증 시작]
    <br/>ID 공급자를 사용하여 앱 사용자를 인증하는 방법을 알아봅니다.
 
* [푸시 알림 시작]
    <br/>기본적인 푸시 알림을 앱에 보내는 방법을 알아봅니다.

[모바일 서비스 시작]:\#getting-started 
[새 모바일 서비스 만들기]:\#create-new-service 
[모바일 서비스 인스턴스 정의]:\#define-mobile-service-instance 
[다음 단계]:\#next-steps 

[0]: ./media/mobile-services-ios-get-started/mobile-quickstart-completed-ios.png 
[6]: ./media/mobile-services-ios-get-started/mobile-portal-quickstart-ios.png 
[7]: ./media/mobile-services-ios-get-started/mobile-quickstart-steps-ios.png 
[8]: ./media/mobile-services-ios-get-started/mobile-xcode-project.png 
[10]: ./media/mobile-services-ios-get-started/mobile-quickstart-startup-ios.png 
[11]: ./media/mobile-services-ios-get-started/mobile-data-tab.png 
[12]: ./media/mobile-services-ios-get-started/mobile-data-browse.png 

[데이터 시작]: /en-us/develop/mobile/tutorials/get-started-with-data-ios 
[인증 시작]: /en-us/develop/mobile/tutorials/get-started-with-users-ios 
[푸시 알림 시작]: /en-us/develop/mobile/tutorials/get-started-with-push-ios 
[모바일 서비스 iOS SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533 
[관리 포털]: https://manage.windowsazure.com/ 
[XCode]: https://go.microsoft.com/fwLink/p/?LinkID=266532 
[.NET 백 엔드 버전]: /ko-kr/documentation/articles/mobile-services-dotnet-backend-ios-get-started



<!-- Anchors. -->
[Getting started with Mobile Services]:#getting-started
[Create a new mobile service]:#create-new-service
[Define the mobile service instance]:#define-mobile-service-instance
[Next Steps]:#next-steps

<!-- Images. -->
[0]: ./media/mobile-services-ios-get-started/mobile-quickstart-completed-ios.png

[6]: ./media/mobile-services-ios-get-started/mobile-portal-quickstart-ios.png
[7]: ./media/mobile-services-ios-get-started/mobile-quickstart-steps-ios.png
[8]: ./media/mobile-services-ios-get-started/mobile-xcode-project.png

[10]: ./media/mobile-services-ios-get-started/mobile-quickstart-startup-ios.png
[11]: ./media/mobile-services-ios-get-started/mobile-data-tab.png
[12]: ./media/mobile-services-ios-get-started/mobile-data-browse.png


<!-- URLs. -->
[Get started with data]: /en-us/develop/mobile/tutorials/get-started-with-data-ios
[Get started with authentication]: /en-us/develop/mobile/tutorials/get-started-with-users-ios
[Get started with push notifications]: /en-us/develop/mobile/tutorials/get-started-with-push-ios

[Mobile Services iOS SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[Management Portal]: https://manage.windowsazure.com/
[XCode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[.NET backend version]: /ko-kr/documentation/articles/mobile-services-dotnet-backend-ios-get-started