<properties
	pageTitle="Azure 알림 허브 사용자에게 알림"
	description="Azure에서 보안 푸시 알림을 보내는 방법에 대해 알아봅니다. 코드 샘플은 .NET API를 사용하여 C#으로 작성되었습니다."
	documentationCenter="windows"
	authors="wesmc7777"
	manager="dwrede"
	services="notification-hubs"
	editor=""/>

<tags
	ms.service="notification-hubs"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-windows"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="09/24/2015"
	ms.author="wesmc"/>

#Azure 알림 허브 사용자에게 알림

[AZURE.INCLUDE [notification-hubs-selector-aspnet-backend-notify-users](../../includes/notification-hubs-selector-aspnet-backend-notify-users.md)]


##개요

Azure의 푸시 알림 지원을 통해 사용하기 쉬운 다중 플랫폼 및 규모 확장 푸시 인프라에 액세스할 수 있어, 모바일 플랫폼용 소비자 응용 프로그램 및 엔터프라이즈 응용 프로그램 모두에 대한 푸시 알림을 매우 간단하게 구현할 수 있습니다. 이 자습서에서는 Azure 알림 허브를 사용하여 특정 장치에서 특정 앱 사용자에게 푸시 알림을 보내는 방법을 보여 줍니다. ASP.NET WebAPI 백 엔드는 클라이언트를 인증하는 데 사용 됩니다. 인증된 클라이언트 사용자를 사용하면 백 엔드에 의해 태그가 자동으로 알림 등록에 추가됩니다. 이 태그는 백 엔드에서 특정 사용자에 대해 알림을 생성하고 보내는 데 사용됩니다. 앱 백 엔드를 사용하여 알림에 등록하는 방법에 대한 자세한 내용은 지침 항목 [앱 백 엔드에서 등록](http://msdn.microsoft.com/library/dn743807.aspx)을 참조하세요. 이 자습서는 [알림 허브 시작] 자습서에서 만든 알림 허브 및 프로젝트를 기반으로 합니다.

이 자습서는 [보안 푸시] 자습서의 필수 조건이기도 합니다. 이 자습서의 단계를 완료해야 푸시 알림을 안전하게 보내도록 이 자습서의 코드를 수정하는 방법을 보여주는 [보안 푸시] 자습서를 진행할 수 있습니다.


##필수 조건

이 자습서를 시작하려면 먼저 다음 모바일 서비스 자습서를 완료해야 합니다.

+ [알림 허브 시작]<br/>알림 허브를 만들고 앱 이름을 예약하고 이 자습서의 알림을 받도록 등록합니다. 이 자습서에서는 다음 단계를 이미 완료했다고 가정합니다. 그렇지 않은 경우 [알림 허브 시작(Windows 스토어)](notification-hubs-windows-store-dotnet-get-started.md), 특히 [Windows 스토어에 앱 등록](notification-hubs-windows-store-dotnet-get-started.md#register-your-app-for-the-windows-store) 및 [알림 허브 구성](notification-hubs-windows-store-dotnet-get-started.md#configure-your-notification-hub) 섹션의 단계를 수행하세요. 특히 알림 허브의 **구성** 탭에서 포털의 **패키지 SID** 및 **클라이언트 암호** 값을 입력했어야 합니다. 이 구성 절차는 [알림 허브 구성](notification-hubs-windows-store-dotnet-get-started.md#configure-your-notification-hub) 섹션에서 설명합니다. 이는 중요한 단계입니다. 포털의 자격 증명이 선택한 앱 이름에 대해 지정된 자격 증명과 일치하지 않으면 푸시 알림이 실패합니다.




> [AZURE.NOTE]모바일 서비스를 백 엔드 서비스로 사용 중인 경우 이 자습서의 [모바일 서비스 버전](../mobile-services-javascript-backend-windows-store-dotnet-push-notifications-app-users.md)을 참조하세요.




[AZURE.INCLUDE [notification-hubs-aspnet-backend-notifyusers](../../includes/notification-hubs-aspnet-backend-notifyusers.md)]

## 클라이언트 프로젝트에 대한 코드 업데이트

이 섹션에서는 [알림 허브 시작] 자습서에 대해 완료한 프로젝트의 코드를 업데이트합니다. 이미 저장소와 연결되어 알림 허브에 대해 구성되어 있어야 합니다. 이 섹션에서는 새 WebAPI 백 엔드를 호출할 코드를 추가하고, 알림을 등록하고 보내는 데 이 코드를 사용합니다.

1. Visual Studio에서 [알림 허브 시작] 자습서에 대해 만든 솔루션을 엽니다.

2. 솔루션 탐색기에서 **(Windows 8.1)** 프로젝트를 마우스 오른쪽 단추로 클릭한 다음 **NuGet 패키지 관리**를 클릭합니다.

3. 왼쪽에서 **온라인**을 클릭합니다.

4. **검색** 상자에 **Http 클라이언트**를 입력합니다.

5. 결과 목록에서 **Microsoft HTTP 클라이언트 라이브러리**를 클릭하고 **설치**를 클릭합니다. 설치를 완료합니다.

6. 다시 NuGet **검색** 상자에 **Json.net**을 입력합니다. **Json.NET** 패키지를 설치하고 NuGet 패키지 관리자 창을 닫습니다.

7. **(Windows Phone 8.1)** 프로젝트에 대해 위의 단계를 반복하여 Windows Phone 프로젝트에 대해 **JSON.NET** NuGet 패키지를 설치합니다.


8. 솔루션 탐색기의 **(Windows 8.1)** 프로젝트에서 **MainPage.xaml**을 두 번 클릭하여 Visual Studio 편집기에서 엽니다.

9. **MainPage.xaml** XML 코드에서 `<Grid>` 섹션을 다음 코드로 바꿉니다. 이 코드는 사용자가 인증하는 데 사용할 사용자 이름 및 암호 텍스트 상자를 추가합니다. 또한 알림 메시지 및 알림을 받을 사용자 이름 태그용 텍스트 상자도 추가합니다.

        <Grid>
            <Grid.RowDefinitions>
                <RowDefinition Height="Auto"/>
                <RowDefinition Height="*"/>
            </Grid.RowDefinitions>

            <TextBlock Grid.Row="0" Text="Notify Users" HorizontalAlignment="Center" FontSize="48"/>

            <StackPanel Grid.Row="1" VerticalAlignment="Center">
                <Grid>
                    <Grid.RowDefinitions>
                        <RowDefinition Height="Auto"/>
                        <RowDefinition Height="Auto"/>
                        <RowDefinition Height="Auto"/>
                        <RowDefinition Height="Auto"/>
                        <RowDefinition Height="Auto"/>
                        <RowDefinition Height="Auto"/>
                        <RowDefinition Height="Auto"/>
                        <RowDefinition Height="Auto"/>
                        <RowDefinition Height="Auto"/>
                        <RowDefinition Height="Auto"/>
                        <RowDefinition Height="Auto"/>
                    </Grid.RowDefinitions>
                    <Grid.ColumnDefinitions>
                        <ColumnDefinition></ColumnDefinition>
                        <ColumnDefinition></ColumnDefinition>
                        <ColumnDefinition></ColumnDefinition>
                    </Grid.ColumnDefinitions>
                    <TextBlock Grid.Row="0" Grid.ColumnSpan="3" Text="Username" FontSize="24" Margin="20,0,20,0"/>
                    <TextBox Name="UsernameTextBox" Grid.Row="1" Grid.ColumnSpan="3" Margin="20,0,20,0"/>
                    <TextBlock Grid.Row="2" Grid.ColumnSpan="3" Text="Password" FontSize="24" Margin="20,0,20,0" />
                    <PasswordBox Name="PasswordTextBox" Grid.Row="3" Grid.ColumnSpan="3" Margin="20,0,20,0"/>

                    <Button Grid.Row="4" Grid.ColumnSpan="3" HorizontalAlignment="Center" VerticalAlignment="Center"
                                Content="1. Login and register" Click="LoginAndRegisterClick" Margin="0,0,0,20"/>

                    <ToggleButton Name="toggleWNS" Grid.Row="5" Grid.Column="0" HorizontalAlignment="Right" Content="WNS" IsChecked="True" />
                    <ToggleButton Name="toggleGCM" Grid.Row="5" Grid.Column="1" HorizontalAlignment="Center" Content="GCM" />
                    <ToggleButton Name="toggleAPNS" Grid.Row="5" Grid.Column="2" HorizontalAlignment="Left" Content="APNS" />

                    <TextBlock Grid.Row="6" Grid.ColumnSpan="3" Text="Username Tag To Send To" FontSize="24" Margin="20,0,20,0"/>
                    <TextBox Name="ToUserTagTextBox" Grid.Row="7" Grid.ColumnSpan="3" Margin="20,0,20,0" TextWrapping="Wrap" />
                    <TextBlock Grid.Row="8" Grid.ColumnSpan="3" Text="Enter Notification Message" FontSize="24" Margin="20,0,20,0"/>
                    <TextBox Name="NotificationMessageTextBox" Grid.Row="9" Grid.ColumnSpan="3" Margin="20,0,20,0" TextWrapping="Wrap" />
                    <Button Grid.Row="10" Grid.ColumnSpan="3" HorizontalAlignment="Center" Content="2. Send push" Click="PushClick" />
                </Grid>
            </StackPanel>
        </Grid>


10. 솔루션 탐색기의 **(Windows Phone 8.1)** 프로젝트에서 **MainPage.xaml**을 열고 Windows Phone 8.1 바꾸기 `<Grid>` 섹션을 위와 동일한 코드로 바꿉니다. 인터페이스는 아래 표시된 것과 유사합니다.

	![][13]

11. 솔루션 탐색기에서 **(Windows 8.1)** 및 **(Windows Phone 8.1)** 프로젝트의 **MainPage.xaml.cs** 파일을 엽니다. 두 파일의 맨 위에 다음 `using` 문을 추가합니다.

		using System.Net.Http;
		using Windows.Storage;
		using System.Net.Http.Headers;
		using Windows.Networking.PushNotifications;
		using Windows.UI.Popups;
		using System.Threading.Tasks;

12. **(Windows 8.1)** 및 **(Windows Phone 8.1)** 프로젝트의 **MainPage.xaml.cs**에서 다음 멤버를 `MainPage` 클래스에 추가합니다. `<Enter Your Backend Endpoint>`을 이전에 얻은 실제 백 엔드 끝점으로 바꿔야 합니다. 예: `http://mybackend.azurewebsites.net`

        private static string BACKEND_ENDPOINT = "<Enter Your Backend Endpoint>";



13. **(Windows 8.1)** 및 **(Windows Phone 8.1)** 프로젝트의 **MainPage.xaml.cs**에서 아래 코드를 MainPage 클래스에 추가합니다.

	`PushClick` 메서드는 **Send Push(푸시 전송)** 단추의 클릭 처리기입니다. `to_tag` 매개 변수와 일치하는 사용자 이름 태그가 있는 모든 장치로 알림을 트리거하도록 백 엔드를 호출합니다. 알림 메시지는 요청 본문의 JSON 콘텐츠로 전송됩니다.

	`LoginAndRegisterClick` 메서드는 **Log in and register(로그인 및 등록)** 단추의 클릭 처리기입니다. 기본 인증 토큰(이는 인증 체계에서 사용하는 모든 토큰을 나타냄)을 로컬 저장소에 저장하고 `RegisterClient`를 사용하여 백 엔드가 사용되는 알림에 등록합니다.


        private async void PushClick(object sender, RoutedEventArgs e)
        {
            if (toggleWNS.IsChecked.Value)
            {
                await sendPush("wns", ToUserTagTextBox.Text, this.NotificationMessageTextBox.Text);
            }
            if (toggleGCM.IsChecked.Value)
            {
                await sendPush("gcm", ToUserTagTextBox.Text, this.NotificationMessageTextBox.Text);
            }
            if (toggleAPNS.IsChecked.Value)
            {
                await sendPush("apns", ToUserTagTextBox.Text, this.NotificationMessageTextBox.Text);

            }
        }

        private async Task sendPush(string pns, string userTag, string message)
        {
            var POST_URL = BACKEND_ENDPOINT + "/api/notifications?pns=" +
                pns + "&to_tag=" + userTag;

            using (var httpClient = new HttpClient())
            {
                var settings = ApplicationData.Current.LocalSettings.Values;
                httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Basic", (string)settings["AuthenticationToken"]);

                try
                {
                    await httpClient.PostAsync(POST_URL, new StringContent(""" + message + """,
                        System.Text.Encoding.UTF8, "application/json"));
                }
                catch (Exception ex)
                {
                    MessageDialog alert = new MessageDialog(ex.Message, "Failed to send " + pns + " message");
                    alert.ShowAsync();
                }
            }
        }

        private async void LoginAndRegisterClick(object sender, RoutedEventArgs e)
        {
            SetAuthenticationTokenInLocalStorage();

            var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

            // The "username:<user name>" tag gets automatically added by the message handler in the backend.
            // The tag passed here can be whatever other tags you may want to use.
            try
            {
                await new RegisterClient(BACKEND_ENDPOINT).RegisterAsync(channel.Uri, new string[] { "myTag" });

                var dialog = new MessageDialog("Registered as: " + UsernameTextBox.Text);
                dialog.Commands.Add(new UICommand("OK"));
                await dialog.ShowAsync();
                SendPushButton.IsEnabled = true;
            }
            catch (Exception ex)
            {
                MessageDialog alert = new MessageDialog(ex.Message, "Failed to register with RegisterClient");
                alert.ShowAsync();
            }
        }

        private void SetAuthenticationTokenInLocalStorage()
        {
            string username = UsernameTextBox.Text;
            string password = PasswordTextBox.Password;

            var token = Convert.ToBase64String(System.Text.Encoding.UTF8.GetBytes(username + ":" + password));
            ApplicationData.Current.LocalSettings.Values["AuthenticationToken"] = token;
        }



14. 솔루션 탐색기의 **공유** 프로젝트 아래에서 **App.xaml.cs** 파일을 엽니다. `OnLaunched()` 이벤트 처리기에서 `InitNotificationsAsync()`에 대한 호출을 찾습니다. `InitNotificationsAsync()`에 대한 호출을 주석으로 처리하거나 삭제합니다. 위에서 추가한 단추 처리기는 알림 등록을 초기화합니다.


        protected override void OnLaunched(LaunchActivatedEventArgs e)
        {
            //InitNotificationsAsync();


15. 솔루션 탐색기에서 **공유** 프로젝트를 마우스 오른쪽 단추로 클릭하고 **추가**, **클래스**를 차례로 클릭합니다. 클래스 이름을 **RegisterClient.cs**로 지정하고 **확인**을 클릭하여 클래스를 생성합니다.

	이 클래스는 푸시 알림에 등록하기 위해 앱 백 엔드에 접속하는 데 필요한 REST 호출을 래핑합니다. 또한 *앱 백 엔드에서 등록*에 설명된 대로 알림 허브에서 생성된 [registrationId](http://msdn.microsoft.com/library/dn743807.aspx)를 로컬로 저장합니다. 이 구성 요소는 **로그인 및 등록** 단추를 클릭할 때 로컬 저장소에 저장된 인증 토큰을 사용합니다.


16. RegisterClient.cs 파일의 맨 위에 다음 `using` 문을 추가합니다.

		using Windows.Storage;
		using System.Net;
		using System.Net.Http;
		using System.Net.Http.Headers;
		using Newtonsoft.Json;
		using System.Threading.Tasks;
		using System.Linq;

17. 다음 코드를 `RegisterClient` 클래스 정의 내에 추가합니다.

		private string POST_URL;

        private class DeviceRegistration
        {
            public string Platform { get; set; }
            public string Handle { get; set; }
            public string[] Tags { get; set; }
        }

        public RegisterClient(string backendEndpoint)
        {
            PostURL = backendEndpoint + "/api/register";
        }

        public async Task RegisterAsync(string handle, IEnumerable<string> tags)
        {
            var regId = await RetrieveRegistrationIdOrRequestNewOneAsync();

            var deviceRegistration = new DeviceRegistration
            {
                Platform = "wns",
                Handle = handle,
                Tags = tags.ToArray<string>()
            };

            var statusCode = await UpdateRegistrationAsync(regId, deviceRegistration);

            if (statusCode == HttpStatusCode.Gone)
            {
                // regId is expired, deleting from local storage & recreating
                var settings = ApplicationData.Current.LocalSettings.Values;
                settings.Remove("__NHRegistrationId");
                regId = await RetrieveRegistrationIdOrRequestNewOneAsync();
                statusCode = await UpdateRegistrationAsync(regId, deviceRegistration);
            }

            if (statusCode != HttpStatusCode.Accepted)
            {
                // log or throw
            }
        }

        private async Task<HttpStatusCode> UpdateRegistrationAsync(string regId, DeviceRegistration deviceRegistration)
        {
            using (var httpClient = new HttpClient())
            {
                var settings = ApplicationData.Current.LocalSettings.Values;
                httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Basic", (string) settings["AuthenticationToken"]);

                var putUri = POST_URL + "/" + regId;

                string json = JsonConvert.SerializeObject(deviceRegistration);
                                var response = await httpClient.PutAsync(putUri, new StringContent(json, Encoding.UTF8, "application/json"));
                return response.StatusCode;
            }
        }

        private async Task<string> RetrieveRegistrationIdOrRequestNewOneAsync()
        {
            var settings = ApplicationData.Current.LocalSettings.Values;
            if (!settings.ContainsKey("__NHRegistrationId"))
            {
                using (var httpClient = new HttpClient())
                {
                    httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Basic", (string)settings["AuthenticationToken"]);

                    var response = await httpClient.PostAsync(POST_URL, new StringContent(""));
                    if (response.IsSuccessStatusCode)
                    {
                        string regId = await response.Content.ReadAsStringAsync();
                        regId = regId.Substring(1, regId.Length - 2);
                        settings.Add("__NHRegistrationId", regId);
                    }
                    else
                    {
                        throw new Exception();
                    }
                }
            }
            return (string)settings["__NHRegistrationId"];

        }

18. 변경 내용을 모두 저장합니다.


## 응용 프로그램 테스트

1. Windows 8.1 및 Windows Phone 8.1 모두에서 응용 프로그램을 시작합니다. Windows Phone 8.1의 경우 에뮬레이터 또는 실제 장치에서 인스턴스를 실행할 수 있습니다.

2. 앱의 Windows 8.1 인스턴스에서 아래 화면에 표시된 것처럼 **사용자 이름** 및 **암호**를 입력합니다. Windows Phone에서 입력하는 사용자 이름 및 암호와 달라야 합니다.


3. **Log in and register(로그인 및 등록)**를 클릭하고 대화 상자에 로그인되었다고 표시되는지 확인합니다. 이렇게 하면 **Send Push(푸시 전송)** 단추도 사용하도록 설정됩니다.

    ![][14]

4. Windows Phone 8.1 인스턴스에서 **사용자 이름** 및 **암호** 필드 모두에 사용자 이름 문자열을 입력하고 **Log in and register(로그인 및 등록)**를 클릭합니다.
5. 그런 다음 **받는 사람 사용자 이름 태그** 필드에 Windows 8.1에서 등록한 사용자 이름을 입력합니다. 알림 메시지를 입력하고 **Send Push(푸시 전송)**를 클릭합니다.

    ![][16]

6. 일치하는 사용자 이름 태그로 등록된 장치만 이 알림 메시지를 받습니다.

	![][15]

## 다음 단계

* 사용자를 관심 그룹별로 분할하려면 [알림 허브를 사용하여 뉴스 속보 보내기](영문)를 참조하십시오.
* 알림 허브에 대한 자세한 내용은 [알림 허브 지침]을 참조하십시오.



[9]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-secure-push9.png
[10]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-secure-push10.png
[11]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-secure-push11.png
[12]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-secure-push12.png
[13]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-wp-ui.png
[14]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-windows-instance-username.png
[15]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-notification-received.png
[16]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-wp-send-message.png



<!-- URLs. -->
[알림 허브 시작]: notification-hubs-windows-store-dotnet-get-started.md
[보안 푸시]: notification-hubs-aspnet-backend-windows-dotnet-secure-push.md
[알림 허브를 사용하여 뉴스 속보 보내기]: notification-hubs-windows-store-dotnet-send-breaking-news.md
[알림 허브 지침]: http://msdn.microsoft.com/library/jj927170.aspx

<!---HONumber=Oct15_HO1-->