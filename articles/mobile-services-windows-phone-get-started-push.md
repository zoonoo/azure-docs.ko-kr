<properties pageTitle="Get started with push notifications (legacy push) | Mobile Dev Center" metaKeywords="" description="Learn how to use Azure Mobile Services to send push notifications to your Windows Phone app (legacy push)." metaCanonical="" services="mobile-services,notification-hubs" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services (legacy push)" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-phone" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="glenga" />

# 모바일 서비스에서 푸시 알림 시작(기존 푸시)

<div class="dev-center-tutorial-selector sublanding">

[Windows 스토어 C#][Windows 스토어 C#] [Windows 스토어 JavaScript][Windows 스토어 JavaScript] [Windows Phone][Windows Phone] [iOS][iOS] [Android][Android] <!--    <a href="/ko-kr/documentation/articles/partner-xamarin-mobile-services-ios-get-started-push" title="Xamarin.iOS">Xamarin.iOS</a>     <a href="/ko-kr/documentation/articles/partner-xamarin-mobile-services-android-get-started-push" title="Xamarin.Android">Xamarin.Android</a>    --> [Appcelerator][Appcelerator]

</div>

<div class="dev-center-tutorial-subselector">

[.NET 백 엔드][.NET 백 엔드] | [JavaScript 백 엔드][JavaScript 백 엔드]

</div>

이 항목에서는 Azure 모바일 서비스를 사용하여 Windows Phone 8 앱에 푸시 알림을 보내는 방법을 보여 줍니다.
이 자습서에서는 빠른 시작 프로젝트에 대한 MPNS(Microsoft 푸시 알림 서비스)를 사용하여 푸시 알림을 추가합니다. 이 작업을 완료하면 레코드가 삽입될 때마다 모바일 서비스에서 푸시 알림을 전송합니다.

> [WACOM.NOTE]이 항목에서는 알림 허브 통합을 사용하도록 *아직 업그레이드되지 않은* *기존* 모바일 서비스를 지원합니다. *새* 모바일 서비스를 만드는 경우 이 통합 기능이 자동으로 사용하도록 설정됩니다. 새 모바일 서비스의 경우 [푸시 알림 시작][푸시 알림 시작]을 참조하십시오.
>
> 모바일 서비스가 Azure 알림 허브와 통합되어 템플릿, 다중 플랫폼, 향상된 확장 등 추가적인 푸시 알림 기능을 지원합니다. *가능한 경우 알림 허브를 사용하도록 기존 모바일 서비스를 업그레이드해야 합니다*. 업그레이드한 후에는 [푸시 알림 시작][푸시 알림 시작]을 참조하십시오.

이 자습서에서는 푸시 알림을 사용하도록 설정하는 다음 기본 단계를 단계별로 안내합니다.

1.  [Registrations 테이블 만들기][Registrations 테이블 만들기]
2.  [앱에 푸시 알림 추가][앱에 푸시 알림 추가]
3.  [푸시 알림을 전송하도록 스크립트 업데이트][푸시 알림을 전송하도록 스크립트 업데이트]
4.  [알림을 받기 위한 데이터 삽입][알림을 받기 위한 데이터 삽입]

이 자습서에는 [Visual Studio 2012 Express for Windows Phone][Visual Studio 2012 Express for Windows Phone] 이상 버전이 필요합니다.

이 자습서는 모바일 서비스 퀵 스타트를 기반으로 합니다. 이 자습서를 시작하기 전에 먼저 [모바일 서비스 시작하기][모바일 서비스 시작하기]를 완료해야 합니다.

> [WACOM.NOTE]매일 사용자당 500개 이상의 메시지를 보내는 경우 대신 알림 허브를 사용해야 합니다. 자세한 내용은 [알림 허브 시작하기][알림 허브 시작하기]를 참조하십시오.

## <a name="create-table"></a>새 테이블 만들기

[WACOM.INCLUDE [mobile-services-create-new-push-table](../includes/mobile-services-create-new-push-table.md)]

## <a name="add-push"></a><span class="short-header">푸시 알림 추가</span>앱에 푸시 알림 추가

1.  Visual Studio에서 프로젝트 파일 MainPage.xaml.cs를 열고 새 **Registrations** 클래스를 만드는 다음 코드를 추가합니다.

        public class Registrations
        {
            public string Id { get; set; }

            [JsonProperty(PropertyName = "handle")]
            public string Handle { get; set; }
        }

    Handle 속성은 채널 URI를 저장하는 데 사용됩니다.

2.  App.xaml.cs 파일을 열고 다음 using 문을 추가합니다.

        using Microsoft.Phone.Notification;

3.  App.xaml.cs에 다음을 추가합니다.

        public static HttpNotificationChannel CurrentChannel { get; private set; }

        private async void AcquirePushChannel()
        {
            CurrentChannel = HttpNotificationChannel.Find("MyPushChannel");

            if (CurrentChannel == null)
            {
                CurrentChannel = new HttpNotificationChannel("MyPushChannel");
                CurrentChannel.Open();
                CurrentChannel.BindToShellTile();
            }

           IMobileServiceTable<Registrations> registrationsTable = App.MobileService.GetTable<Registrations>();
           var registration = new Registrations { Handle = CurrentChannel.ChannelUri.AbsoluteUri };
           await registrationsTable.InsertAsync(registration);
        }

    This code acquires and stores a channel for a push notification subscription and binds it to the app's default tile.

    <div class="dev-callout">

    **참고**
    이 자습서에서 모바일 서비스는 대칭 이동 타일 알림을 장치에 보냅니다. 알림 메시지를 보내는 경우 채널에서 **BindToShellToast** 메서드를 대신 호출해야 합니다. 알림과 타일 알림을 둘 다 지원하려면 **BindToShellTile** 및 **BindToShellToast**를 둘 다 호출합니다.

    </div>

4.  App.xaml.cs에서 **Application\_Launching** 이벤트 처리기의 맨 위에 다음과 같은 새 **AcquirePushChannel** 메서드 호출을 추가합니다.

        AcquirePushChannel();

    이제 응용 프로그램이 시작될 때마다 **CurrentChannel** 속성이 초기화됩니다.

5.  솔루션 탐색기에서 **속성**을 확장하고 WMAppManifest.xml 파일을 연 후 **기능** 탭을 클릭하고 \*\*ID\_**CAP**\_PUSH\_NOTIFICATION\*\* 기능이 선택되었는지 확인합니다.

    ![][0]

    이제 앱이 푸시 알림을 받을 수 있습니다.

## <a name="update-scripts"></a><span class="short-header">삽입 스크립트 업데이트</span>관리 포털에서 등록된 삽입 스크립트 업데이트

[WACOM.INCLUDE [mobile-services-update-registrations-script](../includes/mobile-services-update-registrations-script.md)]

1.  **TodoItem**을 클릭하고 **스크립트**를 클릭한 후 **삽입**을 선택합니다.

    ![][1]

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
                            push.mpns.sendFlipTile(registration.handle, {
                                title: item.text
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

## <a name="test"></a><span class="short-header">앱 테스트</span>앱에서 푸시 알림 테스트

1.  Visual Studio의 **빌드** 메뉴에서 **솔루션 배포**를 선택합니다.

2.  에뮬레이터에서 왼쪽으로 살짝 밀어 설치된 앱 목록을 표시하고 새 **TodoList** 앱을 찾습니다.

3.  앱 아이콘을 길게 누르고 상황에 맞는 메뉴에서 **시작 화면에 고정**을 선택합니다.

    ![][2]

    **TodoList**라는 타일이 시작 메뉴에 고정됩니다.

4.  **TodoList**라는 타일을 탭하여 앱을 시작합니다.

    ![][3]

5.  앱에서 "hello push" 텍스트를 텍스트 상자에 입력한 후 **저장**을 클릭합니다.

    ![][4]

    추가된 항목을 저장하기 위해 삽입 요청이 모바일 서비스로 전송됩니다.

6.  **Start** 단추를 눌러 시작 메뉴로 돌아갑니다.

    ![][5]

    응용 프로그램이 푸시 알림을 받고 이제 타일 제목이 **hello push**로 표시됩니다.

## <a name="next-steps"> </a>다음 단계

이 자습서는 모바일 서비스에서 제공하는 기본 푸시 알림 기능을 보여 줍니다. 플랫폼 간 알림 보내기, 구독 기반 라우팅, 대량 볼륨 등의 고급 기능이 앱에 필요한 경우 모바일 서비스와 함께 Azure 알림 허브를 사용하는 것이 좋습니다. 자세한 내용은 다음과 같은 알림 허브 토픽 중 하나를 참조하십시오.

-   [알림 허브 시작하기][6]
    Windows 스토어 앱에서 알림 허브를 활용하는 방법에 대해 알아보십시오.

-   [알림 허브 정의][알림 허브 정의]
    알림을 만들고 여러 플랫폼의 사용자에게 푸시하는 방법을 알아봅니다.

-   [구독자에게 알림 보내기][구독자에게 알림 보내기]
    관심이 있는 범주에 대해 푸시 알림을 등록하고 받아보는 방법에 대해 알아봅니다.

<!--+ [Send notifications to users]     <br/>Learn how to send push notifications from a Mobile Service to specific users on any device.  + [Send cross-platform notifications to users]     <br/>Learn how to use templates to send push notifications from a Mobile Service, without having to craft platform-specific payloads in your back-end. -->

다음의 모바일 서비스 항목에 대해서도 자세히 알아보십시오.

-   [데이터 시작하기][데이터 시작하기]
    모바일 서비스를 사용하여 데이터를 저장 및 쿼리하는 방법에 대해 자세히 알아보세요.

-   [인증 시작하기][인증 시작하기]
    Windows 계정으로 앱의 사용자를 인증하는 방법에 대해 알아보십시오.

-   [모바일 서비스 서버 스크립트 참조][모바일 서비스 서버 스크립트 참조]
    서버 스크립트의 등록 및 사용에 대해 자세히 알아보십시오.

-   [모바일 서비스 .NET 방법 개념 참조][모바일 서비스 .NET 방법 개념 참조]
    .NET과 함께 모바일 서비스를 사용하는 방법에 대해 자세히 알아보세요.



  [Windows 스토어 C#]: /ko-kr/documentation/articles/mobile-services-windows-store-dotnet-get-started-push "Windows 스토어 C#"
  [Windows 스토어 JavaScript]: /ko-kr/documentation/articles/mobile-services-windows-store-javascript-get-started-push "Windows 스토어 JavaScript"
  [Windows Phone]: /ko-kr/documentation/articles/mobile-services-windows-phone-get-started-push "Windows Phone"
  [iOS]: /ko-kr/documentation/articles/mobile-services-ios-get-started-push "iOS"
  [Android]: /ko-kr/documentation/articles/mobile-services-android-get-started-push "Android"
  [Appcelerator]: /ko-kr/documentation/articles/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started-push "Appcelerator"
  [.NET 백 엔드]: /ko-kr/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-push/ ".NET 백 엔드"
  [JavaScript 백 엔드]: /ko-kr/documentation/articles/mobile-services-windows-phone-get-started-push/ "JavaScript 백 엔드"
  [푸시 알림 시작]: /ko-kr/documentation/articles/mobile-services-javascript-backend-windows-phone-get-started-push/
  [Registrations 테이블 만들기]: #create-table
  [앱에 푸시 알림 추가]: #add-push
  [푸시 알림을 전송하도록 스크립트 업데이트]: #update-scripts
  [알림을 받기 위한 데이터 삽입]: #test
  [Visual Studio 2012 Express for Windows Phone]: https://go.microsoft.com/fwLink/p/?LinkID=268374
  [모바일 서비스 시작하기]: /ko-kr/develop/mobile/tutorials/get-started-wp8
  [알림 허브 시작하기]: /ko-kr/manage/services/notification-hubs/getting-started-windows-dotnet/
  [mobile-services-create-new-push-table]: ../includes/mobile-services-create-new-push-table.md
  [0]: ./media/mobile-services-windows-phone-get-started-push/mobile-app-enable-push-wp8.png
  [mobile-services-update-registrations-script]: ../includes/mobile-services-update-registrations-script.md
  [1]: ./media/mobile-services-windows-phone-get-started-push/mobile-insert-script-push2.png
  [2]: ./media/mobile-services-windows-phone-get-started-push/mobile-quickstart-push1-wp8.png
  [3]: ./media/mobile-services-windows-phone-get-started-push/mobile-quickstart-push2-wp8.png
  [4]: ./media/mobile-services-windows-phone-get-started-push/mobile-quickstart-push3-wp8.png
  [5]: ./media/mobile-services-windows-phone-get-started-push/mobile-quickstart-push4-wp8.png
  [6]: /ko-kr/manage/services/notification-hubs/get-started-notification-hubs-wp8/
  [알림 허브 정의]: /ko-kr/develop/net/how-to-guides/service-bus-notification-hubs/
  [구독자에게 알림 보내기]: /ko-kr/manage/services/notification-hubs/breaking-news-wp8/
  [데이터 시작하기]: /ko-kr/develop/mobile/tutorials/get-started-with-data-wp8
  [인증 시작하기]: /ko-kr/develop/mobile/tutorials/get-started-with-users-wp8
  [모바일 서비스 서버 스크립트 참조]: http://go.microsoft.com/fwlink/?LinkId=262293
  [모바일 서비스 .NET 방법 개념 참조]: /ko-kr/develop/mobile/how-to-guides/work-with-net-client-library/
