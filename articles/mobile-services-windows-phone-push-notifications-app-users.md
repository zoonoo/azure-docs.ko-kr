<properties linkid="develop-net-tutorials-push-notifications-to-users-wp8" urlDisplayName="Push Notifications to Users (WP8)" pageTitle="Push notifications to users (Windows Phone) | Mobile Dev Center" metaKeywords="" description="Learn how to use Mobile Services to push notifications to users of your Windows Phone app." metaCanonical="" services="" documentationCenter="" title="Push notifications to users by using Mobile Services" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-phone" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="glenga"></tags>

# 모바일 서비스를 사용하여 사용자에게 푸시 알림

<div class="dev-center-tutorial-selector sublanding"> 
    <a href="/ko-kr/develop/mobile/tutorials/push-notifications-to-users-wp8" title="Windows Phone" class="current">Windows Phone</a><a href="/ko-kr/develop/mobile/tutorials/push-notifications-to-users-ios" title="iOS">iOS</a><a href="/ko-kr/develop/mobile/tutorials/push-notifications-to-users-android" title="Android" class="current">Android</a>
</div>

이 항목에서는 MPNS(Microsoft 푸시 알림 서비스) 채널 URI를 저장하는 새 테이블을 추가하여 [이전의 푸시 알림 자습서][이전의 푸시 알림 자습서]를 확장했습니다. 이 채널을 사용하여 Windows Phone 8 앱 사용자에게 푸시 알림을 보낼 수 있습니다.

이 자습서에서는 앱에서 푸시 알림을 업데이트하는 다음 단계를 단계별로 안내합니다.

1.  [Channel 테이블 만들기][Channel 테이블 만들기]
2.  [앱 업데이트][앱 업데이트]
3.  [서버 스크립트 업데이트][서버 스크립트 업데이트]
4.  [푸시 알림 동작 확인][푸시 알림 동작 확인]

이 자습서는 모바일 서비스 빠른 시작을 기반으로 하며 이전 자습서인 [푸시 알림 시작][이전의 푸시 알림 자습서]에 내용을 추가했습니다. 이 자습서를 시작하기 전에 먼저 [푸시 알림 시작][이전의 푸시 알림 자습서]을 완료해야 합니다.

## <a name="create-table"></a>새 테이블 만들기

1.  [Azure 관리 포털][Azure 관리 포털]에 로그인하여 **모바일 서비스**를 클릭한 후 앱을 클릭합니다.

    ![][]

2.  **데이터** 탭을 클릭한 후 **만들기**를 클릭합니다.

    ![][1]

    **Create new table** 대화 상자가 표시됩니다.

3.  모든 권한에 기본값인 **응용 프로그램 키가 있는 모든 사용자** 설정을 유지하고 **테이블 이름**에 *Channel*을 입력한 후 확인 단추를 클릭합니다.

    ![][2]

    항목 데이터와 별개로 푸시 알림을 보내기 위해 사용되는 채널 URI를 저장하는 **Channel** 테이블이 생성됩니다.

다음으로, **TodoItem** 테이블 대신 이 새 테이블에 데이터를 저장하도록 푸시 알림 앱을 수정합니다.

## <a name="update-app"></a>앱 업데이트

1.  Visual Studio 2012 Express for Windows Phone에서 [푸시 알림 시작][이전의 푸시 알림 자습서] 자습서의 프로젝트를 열고 MainPage.xaml.cs 파일을 연 후 **TodoItem** 클래스에서 **Channel** 속성을 제거합니다. 이제 다음과 같습니다.

        public class TodoItem
        {
            public int Id { get; set; }

            [JsonProperty(PropertyName = "text")]
            public string Text { get; set; }

            [JsonProperty(PropertyName = "complete")]
            public bool Complete { get; set; }
        }

2.  다음과 같이 **ButtonSave\_Click** 이벤트 처리기 메서드를 이 메서드 원래 버전으로 바꿉니다.

        private void ButtonSave_Click(object sender, RoutedEventArgs e)
        {
            var todoItem = new TodoItem { Text = TextInput.Text };
            InsertTodoItem(todoItem);
        }

3.  새 **Channel** 클래스를 만드는 다음 코드를 추가합니다.

        public class Channel
        {
            public int Id { get; set; }

            [JsonProperty(PropertyName = "uri")]
            public string Uri { get; set; }
        }

4.  App.xaml.cs 파일을 열고 **AcquirePushChannel** 메서드를 다음 코드로 바꿉니다.

        private void AcquirePushChannel()
        {
            CurrentChannel = HttpNotificationChannel.Find("MyPushChannel");

            if (CurrentChannel == null)
            {
                CurrentChannel = new HttpNotificationChannel("MyPushChannel");
                CurrentChannel.Open();
                CurrentChannel.BindToShellTile();
            }

           IMobileServiceTable<Channel> channelTable = App.MobileService.GetTable<Channel>();
           var channel = new Channel { Uri = CurrentChannel.ChannelUri.ToString() };
           channelTable.InsertAsync(channel);
        }

    이 코드는 Channel 테이블에 채널을 삽입합니다.

## <a name="update-scripts"></a>서버 스크립트 업데이트

1.  관리 포털에서 **데이터** 탭을 클릭한 후 **Channel** 테이블을 클릭합니다.

    ![][3]

2.  **channel**에서 **스크립트** 탭을 클릭하고 **삽입**을 선택합니다.

    ![][4]

    **Channel** 테이블에 삽입 시 호출되는 함수가 표시됩니다.

3.  삽입 함수를 다음의 코드로 바꾼 후 **저장**을 클릭합니다.

            function insert(item, user, request) {
                var channelTable = tables.getTable('Channel');
                channelTable
                    .where({ uri: item.uri })
                    .read({ success: insertChannelIfNotFound });
                function insertChannelIfNotFound(existingChannels) {
                    if (existingChannels.length > 0) {
                        request.respond(200, existingChannels[0]);
                    } else {
                        request.execute();
                    }
                }
            }

    이 스크립트는 **Channel** 테이블에서 동일한 URI를 가진 기존 채널을 확인합니다. 일치하는 채널이 발견되지 않는 경우에만 삽입이 진행됩니다. 이를 통해 중복 채널 레코드가 생기지 않습니다.

4.  **TodoItem**을 클릭하고 **스크립트**를 클릭한 후 **삽입**을 선택합니다.

    ![][5]

5.  삽입 함수를 다음의 코드로 바꾼 후 **저장**을 클릭합니다.

        function insert(item, user, request) {
            request.execute({
                success: function() {
                    request.respond();
                    sendNotifications();
                }
            });

            function sendNotifications() {
                var channelTable = tables.getTable('Channel');
                channelTable.read({
                    success: function(channels) {
                        channels.forEach(function(channel) {
                            push.mpns.sendFlipTile(channel.uri, {
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

    이 삽입 스크립트는 **Channel** 테이블에 저장된 모든 채널에 삽입된 항목의 텍스트가 포함된 푸시 알림을 보냅니다.

## <a name="test-app"></a>앱 테스트

1.  Visual Studio의 **빌드** 메뉴에서 **솔루션 배포**를 선택합니다.

2.  **TodoList** 또는 **hello push**라는 타일을 탭하여 앱을 시작합니다.

    ![][6]

3.  앱에서 "hello push again" 텍스트를 텍스트 상자에 입력한 후 **저장**을 클릭합니다.

    ![][7]

    추가된 항목을 저장하기 위해 삽입 요청이 모바일 서비스로 전송됩니다.

4.  **Start** 단추를 눌러 시작 메뉴로 돌아갑니다.

    ![][8]

    응용 프로그램이 푸시 알림을 받고 이제 타일 제목이 **hello push**로 표시됩니다.

5.  (옵션) 두 개의 장치에서 동시에 앱을 실행하고 이전 단계를 반복합니다.

    실행 중인 모든 앱 인스턴스에 알림이 전송됩니다.

## 다음 단계

이제 푸시 알림 관련 작업의 기본 사항을 설명하는 자습서를 마쳤습니다. 다음의 모바일 서비스 항목에 대해서도 자세히 알아보십시오.

-   [데이터 시작하기][데이터 시작하기]
    모바일 서비스를 사용하여 데이터를 저장 및 쿼리하는 방법에 대해 자세히 알아보세요.

-   [인증 시작하기][인증 시작하기]
    Windows 계정으로 앱의 사용자를 인증하는 방법에 대해 알아보십시오.

-   [모바일 서비스 서버 스크립트 참조][모바일 서비스 서버 스크립트 참조]
    서버 스크립트의 등록 및 사용에 대해 자세히 알아보십시오.

<!-- anchors --> <!-- Images. --> <!-- URLs. -->

  [Windows Phone]: /ko-kr/develop/mobile/tutorials/push-notifications-to-users-wp8 "Windows Phone"
  [iOS]: /ko-kr/develop/mobile/tutorials/push-notifications-to-users-ios "iOS"
  [Android]: /ko-kr/develop/mobile/tutorials/push-notifications-to-users-android "Android"
  [이전의 푸시 알림 자습서]: /ko-kr/develop/mobile/tutorials/get-started-with-push-wp8
  [Channel 테이블 만들기]: #create-table
  [앱 업데이트]: #update-app
  [서버 스크립트 업데이트]: #update-scripts
  [푸시 알림 동작 확인]: #test-app
  [Azure 관리 포털]: https://manage.windowsazure.com/
  []: ./media/mobile-services-windows-phone-push-notifications-app-users/mobile-services-selection.png
  [1]: ./media/mobile-services-windows-phone-push-notifications-app-users/mobile-create-table.png
  [2]: ./media/mobile-services-windows-phone-push-notifications-app-users/mobile-create-channel-table.png
  [3]: ./media/mobile-services-windows-phone-push-notifications-app-users/mobile-portal-data-tables-channel.png
  [4]: ./media/mobile-services-windows-phone-push-notifications-app-users/mobile-insert-script-channel.png
  [5]: ./media/mobile-services-windows-phone-push-notifications-app-users/mobile-insert-script-push2.png
  [6]: ./media/mobile-services-windows-phone-push-notifications-app-users/mobile-quickstart-push4-wp8.png
  [7]: ./media/mobile-services-windows-phone-push-notifications-app-users/mobile-quickstart-push5-wp8.png
  [8]: ./media/mobile-services-windows-phone-push-notifications-app-users/mobile-quickstart-push6-wp8.png
  [데이터 시작하기]: /ko-kr/develop/mobile/tutorials/get-started-with-data-wp8
  [인증 시작하기]: /ko-kr/develop/mobile/tutorials/get-started-with-users-wp8
  [모바일 서비스 서버 스크립트 참조]: http://go.microsoft.com/fwlink/p/?LinkId=262293
