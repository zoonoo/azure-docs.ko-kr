<properties linkid="develop-mobile-tutorials-get-started-with-push-dotnet" urlDisplayName="Get Started with Push Notifications" pageTitle="Get started with push notifications - Mobile Services" metaKeywords="push notifications c#" description="Learn how to use push notifications with Azure Mobile Services." metaCanonical="http://www.windowsazure.com/en-us/develop/mobile/tutorials/get-started-with-push-dotnet/" disqusComments="0" umbracoNaviHide="1" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services using Visual Studio 2012" authors="" />

Visual Studio 2012를 사용하여 모바일 서비스에서 푸시 알림 시작하기
==================================================================

[Windows 스토어 C\#](/en-us/develop/mobile/tutorials/get-started-with-push-dotnet-vs2012 "Windows 스토어 C#") [Windows 스토어 JavaScript](/en-us/develop/mobile/tutorials/get-started-with-push-js-vs2012 "Windows 스토어 JavaScript") [Windows Phone](/en-us/develop/mobile/tutorials/get-started-with-push-wp8 "Windows Phone") [iOS](/en-us/develop/mobile/tutorials/get-started-with-push-ios "iOS") [Android](/en-us/develop/mobile/tutorials/get-started-with-push-android "Android") [Xamarin.iOS](/en-us/develop/mobile/tutorials/get-started-with-push-xamarin-ios "Xamarin.iOS") [Xamarin.Android](/en-us/develop/mobile/tutorials/get-started-with-push-xamarin-android "Xamarin.Android")

이 항목에서는 Azure 모바일 서비스를 사용하여 Windows 스토어 앱에 푸시 알림을 보내는 방법을 보여 줍니다. 이 자습서에서는 WNS(Windows 푸시 알림 서비스)를 사용하여 quickstart 프로젝트에 푸시 알림을 추가합니다. 이 작업을 완료하면 레코드가 삽입될 때마다 모바일 서비스에서 푸시 알림을 전송합니다.

**참고**

이 자습서에서는 Visual Studio 2012에서 만든 Windows 스토어 앱에 푸시 알림을 추가합니다. Visual Studio 2013에는 모바일 서비스를 사용하여 Windows 스토어 앱에서 푸시 알림을 쉽게 설정할 수 있는 새로운 기능이 포함되어 있습니다. Visual Studio 2013 버전에 대한 자세한 내용은 [푸시 알림 시작](/en-us/develop/mobile/tutorials/get-started-with-push-dotnet)을 참조하십시오.

이 자습서에서는 푸시 알림을 사용하도록 설정하는 다음 기본 단계를 단계별로 안내합니다.

1.  [푸시 알림을 위한 앱 등록 및 모바일 서비스 구성](#register)
2.  [Registrations 테이블 만들기](#create-table)
3.  [앱에 푸시 알림 추가](#add-push)
4.  [푸시 알림을 전송하도록 스크립트 업데이트](#update-scripts)
5.  [알림을 받기 위한 데이터 삽입](#test)

이 자습서를 사용하려면 다음이 필요합니다.

-   Microsoft Visual Studio 2012 Express for Windows 8
-   활성 Windows 스토어 계정

이 자습서는 [데이터 시작](/en-us/develop/mobile/tutorials/get-started-with-data-dotnet) 자습서를 기반으로 합니다. 이 자습서를 시작하기 전에 먼저 [이 자습서](/en-us/develop/mobile/tutorials/get-started-with-data-dotnet)를 완료해야 합니다.

앱 등록Windows 스토어에 앱 등록
-------------------------------

모바일 서비스에서 Windows 스토어 앱으로 푸시 알림을 보내려면 앱을 Windows 스토어에 제출해야 합니다. 그런 다음 WNS와 통합되도록 모바일 서비스를 구성해야 합니다.

[WACOM.INCLUDE [mobile-services-register-windows-store-app](../includes/mobile-services-register-windows-store-app.md)]

이제 모바일 서비스와 앱이 WNS를 사용하도록 둘 다 구성되었습니다. 이제 등록을 저장할 새 테이블을 만듭니다.

새 테이블 만들기
----------------

[WACOM.INCLUDE [mobile-services-create-new-push-table](../includes/mobile-services-create-new-push-table.md)]

푸시 알림 추가앱에 푸시 알림 추가
---------------------------------

1.  프로젝트 파일 MainPage.xaml.cs를 열고 새 **Registrations** 클래스를 만드는 다음 코드를 추가합니다.

         public class Registrations
         {
             public string Id { get; set; }

             [JsonProperty(PropertyName = "handle")]
             public string Handle { get; set; }
         }

    Handle 속성은 채널 URI를 저장하는 데 사용됩니다.

2.  App.xaml.cs 파일을 열고 다음 using 문을 추가합니다.

         using Windows.Networking.PushNotifications;

3.  App.xaml.cs에 다음을 추가합니다.

         private async void AcquirePushChannel()
         {
            CurrentChannel = 
                await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

            IMobileServiceTable<Registrations> registrationsTable = App.MobileService.GetTable<Registrations>();
            var registration = new Registrations { Handle = CurrentChannel.Uri };
            await registrationsTable.InsertAsync(registration);
         }

    이 코드에서는 Registrations 테이블에 현재 채널을 삽입합니다.

4.  App.xaml.cs에서 **OnLaunched** 이벤트 처리기의 맨 위에 다음과 같은 새 **AcquirePushChannel** 메서드 호출을 추가합니다.

         AcquirePushChannel();

    이제 응용 프로그램이 시작될 때마다 **CurrentChannel** 속성이 초기화됩니다.

5.  Package.appxmanifest 파일을 열고 **Application UI** 탭에서 **Toast capable**이 **Yes**로 설정되어 있는지 확인합니다.

   	![][15]

   	이제 앱에서 알림 메시지를 표시할 수 있습니다. 

삽입 스크립트 업데이트관리 포털에서 등록된 삽입 스크립트 업데이트
-----------------------------------------------------------------

[WACOM.INCLUDE [mobile-services-update-registrations-script](../includes/mobile-services-update-registrations-script.md)]

1.  **TodoItem**을 클릭하고 **스크립트**를 클릭한 후 **삽입**을 선택합니다.

   	![][5]

2.  삽입 함수를 다음의 코드로 바꾼 후 **저장**을 클릭합니다.

	    function insert(item, user, request) {
    	    request.execute({
        	    success: function() {
            	    request.respond();
            	    sendNotifications();
        	    }
    	    });

	        function sendNotifications() {
        	    var registrationsTable = tables.getTable('Registrations');
        	    registrationsTable.read({
            	    success: function(registrations) {
                	    registrations.forEach(function(registration) {
                    	    push.wns.sendToastText04(registration.handle, {
                        	    text1: item.text
                    	    }, {
                        	    success: function(pushResponse) {
                            	    console.log("Sent push:", pushResponse);
                        	    }
                    	    });
                	    });
            	    }
        	    });
    	    }
	    }

    이 삽입 스크립트는 **Registrations** 테이블에 저장된 모든 채널에 삽입된 항목의 텍스트가 포함된 푸시 알림을 보냅니다.

앱 테스트앱에서 푸시 알림 테스트
--------------------------------

1.  Visual Studio에서 F5 키를 눌러 앱을 실행합니다.

2.  앱에서 **Insert a TodoItem**에 텍스트를 입력하고 **저장**을 클릭합니다.

   	![][13]

   	삽입이 완료되고 나면 WNS로부터 푸시 알림을 받습니다.

   	![][14]

다음 단계
---------

이 자습서는 모바일 서비스에서 제공하는 기본 푸시 알림 기능을 보여 줍니다. 플랫폼 간 알림 보내기, 구독 기반 라우팅, 대량 볼륨 등의 고급 기능이 앱에 필요한 경우 모바일 서비스와 함께 Azure 알림 허브를 사용하는 것이 좋습니다. 자세한 내용은 다음과 같은 알림 허브 토픽 중 하나를 참조하십시오.

-   [알림 허브 시작하기](/en-us/manage/services/notification-hubs/getting-started-windows-dotnet/)
    <br/>Windows 스토어 앱에서 알림 허브를 활용하는 방법에 대해 알아보십시오.

-   [알림 허브 정의](/en-us/develop/net/how-to-guides/service-bus-notification-hubs/)
    <br/>알림을 만들고 여러 플랫폼의 사용자에게 푸시하는 방법을 알아봅니다.

-   [구독자에게 알림 보내기](/en-us/manage/services/notification-hubs/breaking-news-dotnet/)
    <br/>관심이 있는 범주에 대해 푸시 알림을 등록하고 받아보는 방법에 대해 알아보십시오.

-   [사용자에게 알림 보내기](/en-us/manage/services/notification-hubs/notify-users/)
    <br/>모바일 서비스에서 장치와 상관없이 특정 사용자에게 푸시 알림을 보내는 방법에 대해 알아보십시오.

-   [사용자에게 플랫폼 간 알림 보내기](/en-us/manage/services/notification-hubs/notify-users-xplat-mobile-services/)
    <br/>백 엔드에 플랫폼 전용 페이로드를 마련할 필요 없이 템플릿을 사용하여 모바일 서비스에서 푸시 알림을 보내는 방법에 대해 알아보십시오.

다음의 모바일 서비스 항목에 대해서도 자세히 알아보십시오.

-   [데이터 시작하기](/en-us/develop/mobile/tutorials/get-started-with-data-dotnet)
    <br/>모바일 서비스를 사용하여 데이터를 저장 및 쿼리하는 방법에 대해 자세히 알아보십시오.

-   [인증 시작하기](/en-us/develop/mobile/tutorials/get-started-with-users-dotnet)
    <br/>Windows 계정으로 앱의 사용자를 인증하는 방법에 대해 알아보십시오.

-   [모바일 서비스 서버 스크립트 참조](http://go.microsoft.com/fwlink/?LinkId=262293)
    <br/>서버 스크립트의 등록 및 사용에 대해 자세히 알아보십시오.

-   [모바일 서비스 .NET 방법 개념 참조](/en-us/develop/mobile/how-to-guides/work-with-net-client-library/)
    <br/>.NET과 함께 모바일 서비스를 사용하는 방법에 대해 자세히 알아보십시오.

<!-- Anchors. -->
[Register your app for push notifications and configure Mobile Services]: #register
[Create the Registrations table]: #create-table
[Update scripts to send push notifications]: #update-scripts
[Add push notifications to the app]: #add-push
[Insert data to receive notifications]: #test
[Next Steps]:#next-steps

<!-- Images. -->





[5]: ./media/mobile-services-windows-store-dotnet-get-started-push-vs2012/mobile-insert-script-push2.png







[13]: ./media/mobile-services-windows-store-dotnet-get-started-push-vs2012/mobile-quickstart-push1.png
[14]: ./media/mobile-services-windows-store-dotnet-get-started-push-vs2012/mobile-quickstart-push2.png
[15]: ./media/mobile-services-windows-store-dotnet-get-started-push-vs2012/mobile-app-enable-toast-win8.png
