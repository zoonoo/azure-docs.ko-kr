<properties linkid="notification-hubs-how-to-guides-howto-register-user-with-aspnet-webapi-windowsphonedotnet" urlDisplayName="Notify Windows Store app users by using Web API" pageTitle="Register the current user for push notifications by using Web API - Notification Hubs" metaKeywords="Azure registering application, Notification Hubs, Azure push notifications, push notification Windows Store app" description="Learn how to request push notification registration in a Windows Store app with Azure Notification Hubs when registeration is performed by ASP.NET Web API." metaCanonical="" services="notification-hubs" documentationCenter="" title="Register the current user for push notifications by using ASP.NET" authors="glenga" solutions="" manager="dwrede" editor="" />

<tags ms.service="notification-hubs" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="glenga" />

# ASP.NET을 사용하여 푸시 알림에 현재 사용자 등록

<div class="dev-center-tutorial-selector sublanding">
    <a href="/ko-KR/documentation/articles/notification-hubs-windows-store-aspnet-register-user-push-notifications/" title="Windows 스토어 C#" class="current">Windows 스토어 C#</a><a href="/ko-KR/documentation/articles/notification-hubs-ios-aspnet-register-user-push-notifications/" title="iOS">iOS</a>
</div>

이 항목에서는 ASP.NET Web API에서 등록을 수행할 때 Azure 알림 허브를 통해 푸시 알림 등록을 요청하는 방법을 보여 줍니다. 이 항목은 [알림 허브를 통해 사용자에게 알림][알림 허브를 통해 사용자에게 알림] 자습서를 확장합니다. 이미 해당 자습서의 필수 단계를 완료하여 인증된 모바일 서비스를 만든 상태여야 합니다. 사용자 알림 시나리오에 대한 자세한 내용은 [알림 허브를 통해 사용자에게 알림][알림 허브를 통해 사용자에게 알림]을 참조하십시오.

1.  Visual Studio 2012에서 [알림 허브를 통해 사용자에게 알림][알림 허브를 통해 사용자에게 알림] 필수 자습서를 완료할 때 만든 프로젝트에서 app.xaml.cs 파일을 엽니다.

2.  **InitNotificationsAsync** 메서드를 찾아 메서드의 코드를 주석으로 처리합니다.

    대신 ASP.NET Web API를 사용하여 알림을 등록합니다.

3.  **솔루션 탐색기**에서 프로젝트 이름을 마우스 오른쪽 단추로 클릭한 후 **NuGet 패키지 관리**를 선택합니다.

4.  왼쪽 창에서 **온라인** 범주를 선택하고 `json.net`을 검색한 후 **Json.NET** 패키지에서 **설치**를 클릭하고 라이선스 계약에 동의합니다.

    ![][0]

    그러면 타사 Newtonsoft.Json.dll 어셈블리가 프로젝트에 추가됩니다.

5.  솔루션 탐색기에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **추가**, **클래스**를 차례로 클릭한 후 `LocalStorageManager`를 입력하고 **추가**를 클릭합니다.

    ![][1]

    그러면 **LocalStorageManager** 클래스의 코드 파일이 프로젝트에 추가됩니다.

6.  새 LocalStorageManager.cs 프로젝트 파일을 열고 다음 **using** 문을 추가합니다.

        using Windows.Storage;

7.  **LocalStorageManager** 클래스 정의를 다음 코드로 바꿉니다.

        class LocalStorageManager
        {
            private static ApplicationDataContainer GetLocalStorageContainer()
            {
                if (!ApplicationData.Current.LocalSettings
                    .Containers.ContainsKey("InstallationContainer"))
                {
                    ApplicationData.Current.LocalSettings
                        .CreateContainer("InstallationContainer",                                                            
                        ApplicationDataCreateDisposition.Always);
                }
                return ApplicationData.Current.LocalSettings
                    .Containers["InstallationContainer"];
            }

            public static string GetInstallationId()
            {
                var container = GetLocalStorageContainer();
                if (!container.Values.ContainsKey("InstallationId"))
                {
                    container.Values["InstallationId"] = Guid.NewGuid().ToString();
                }
                return (string)container.Values["InstallationId"];
            }
        }

    이 코드는 알림을 만들 때 태그로 사용되는 장치별 설치 ID를 만든 후 저장합니다. 설치 ID가 있는 경우 해당 설치 ID가 대신 사용됩니다.

8.  MainPage.xaml 프로젝트 파일을 열고 루트 **Grid** 요소를 다음 XAML 코드로 바꿉니다.

        <Grid Background="{StaticResource ApplicationPageBackgroundThemeBrush}">
            <Grid Margin="120, 58, 120, 80" >
                <Grid.RowDefinitions>
                    <RowDefinition />
                    <RowDefinition/>
                    <RowDefinition/>
                    <RowDefinition/>
                    <RowDefinition/>
                </Grid.RowDefinitions>
                <Grid.ColumnDefinitions>
                    <ColumnDefinition MaxWidth="200"/>
                    <ColumnDefinition/>
                </Grid.ColumnDefinitions>
                <TextBlock Grid.Row="0" Grid.Column="0" Grid.ColumnSpan="2"  
                               TextWrapping="Wrap" Text="Push To Users with Notification Hubs" 
                               FontSize="42"  VerticalAlignment="Top"/>
                <TextBlock Grid.Row="1" Grid.Column="0" Text="Installation Id:" 
                               FontSize="24" VerticalAlignment="Center" />
                <TextBlock Grid.Row="1" Grid.Column="1" Name="InstId" FontSize="24" 
                               VerticalAlignment="Center"/>
                <TextBlock Grid.Row="2" Grid.Column="0" Text="Username:" FontSize="24" 
                               VerticalAlignment="Center"/>
                <TextBox Grid.Row="2" Grid.Column="1" Name="User" FontSize="24" 
                             Width="300" Height="40" HorizontalAlignment="Left"/>
                <TextBlock Grid.Row="3" Grid.Column="0" Text="Password:" FontSize="24" 
                               VerticalAlignment="Center" />
                <PasswordBox Grid.Row="3" Grid.Column="1" Name="Password" FontSize="24" 
                                 Width="300" Height="40" HorizontalAlignment="Left"/>
                <Button Name="Login" Grid.Row="4" Grid.Column="0" Grid.ColumnSpan="2" 
                            Content="Login and Register for Push" FontSize="24" Click="Login_Click" />
            </Grid>
        </Grid>

    이 코드는 사용자 이름과 암호를 수집하고 설치 ID를 표시하는 데 사용되는 UI를 만듭니다.

9.  MainPage.xaml.cs 파일로 돌아가서 다음 **using** 지시문을 추가합니다.

        using Newtonsoft.Json;
        using System.Net.Http;
        using System.Text;
        using Windows.Networking.PushNotifications;
        using Windows.UI.Popups;

10. **MainPage** 클래스에 다음 정의를 추가합니다.

        private string registerUri = "https://<SERVICE_ROOT_URL>/api/register";

    *`<SERVICE_ROOT_URL>`*을 **알림 허브를 통해 사용자에게 알림**에서 만든 웹 API의 루트 URI로 바꿉니다.

11. **MainPage** 클래스에 다음 메서드를 추가합니다.

        private async void Login_Click(object sender, RoutedEventArgs e)
        {
            // Get the info that we need to request registration.
            var installationId = InstId.Text = LocalStorageManager.GetInstallationId();
            var channel = await PushNotificationChannelManager
                .CreatePushNotificationChannelForApplicationAsync();
            var user = User.Text;
            var pwd = Password.Password;

            // Define a registration to pass in the body of the POST request.
            var registration = new Dictionary<string, string>()
                                   {{"platform", "windows"},
                                   {"instId", installationId.ToString()},
                                   {"channelUri", channel.Uri}};

            // Create a client to send the HTTP registration request.
            var client = new HttpClient();
            var request =
                new HttpRequestMessage(HttpMethod.Post, new Uri(registerUri));

            // Add the Authorization header with the basic login credentials.
            var auth = "Basic " +
                Convert.ToBase64String(Encoding.UTF8.GetBytes(user + ":" + pwd));
            request.Headers.Add("Authorization", auth);
            request.Content = new StringContent(JsonConvert.SerializeObject(registration),
                Encoding.UTF8, "application/json");

            string message;

            try
            {
                // Send the registration request.
                HttpResponseMessage response = await client.SendAsync(request);

                // Get and display the response, either the registration ID
                // or an error message.
                message = await response.Content.ReadAsStringAsync();
                message = string.Format("Registration ID: {0}", message);
            }
            catch (Exception ex)
            {
                message = ex.Message;
            }

            // Display a message dialog.
            var dialog = new MessageDialog(message);
            dialog.Commands.Add(new UICommand("OK"));
            await dialog.ShowAsync();
        }

    이 메서드는 푸시 알림에 대한 설치 ID와 채널을 모두 가져온 다음, 알림 허브에서 등록을 만드는 인증된 웹 API 메서드에 장치 유형과 함께 보냅니다. 이 웹 API는 [알림 허브를 통해 사용자에게 알림][알림 허브를 통해 사용자에게 알림]에서 정의했습니다.

클라이언트 앱이 업데이트되었으므로 [알림 허브를 통해 사용자에게 알림][알림 허브를 통해 사용자에게 알림]으로 돌아가서 알림 허브를 사용하여 알림을 보내도록 모바일 서비스를 업데이트합니다.

 
 


  [알림 허브를 통해 사용자에게 알림]: /ko-KR/manage/services/notification-hubs/notify-users-aspnet
  [0]: ./media/notification-hubs-windows-store-aspnet-register-user-push-notifications/notification-hub-add-nuget-package-json.png
  [1]: ./media/notification-hubs-windows-store-aspnet-register-user-push-notifications/notification-hub-create-aspnet-class.png
