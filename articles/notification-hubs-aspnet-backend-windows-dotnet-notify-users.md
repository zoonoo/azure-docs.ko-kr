<properties title="Azure Notification Hubs Notify Users" pageTitle="Azure 알림 허브 사용자에게 알림" metaKeywords="Azure push notifications, Azure notification hubs" description="Learn how to send secure push notifications in Azure. Code samples written in C# using the .NET API." documentationCenter="" metaCanonical="" disqusComments="1" umbracoNaviHide="0" authors="glenga" manager="dwrede" services="notification-hubs" />

<tags ms.service="notification-hubs" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows" ms.devlang="dotnet" ms.topic="article" ms.date="11/22/2014" ms.author="glenga" />

#Azure 알림 허브 사용자에게 알림

<div class="dev-center-tutorial-selector sublanding"> 
    	<a href="/ko-kr/documentation/articles/notification-hubs-windows-dotnet-notify-users/" title="Windows Universal" class="current">Windows Universal</a><a href="/ko-kr/documentation/articles/notification-hubs-aspnet-backend-ios-notify-users/" title="iOS">iOS</a>
		<a href="/ko-kr/documentation/articles/notification-hubs-aspnet-backend-android-notify-users/" title="Android">Android</a>
</div>

Azure의 푸시 알림 지원을 통해 사용하기 쉬운 다중 플랫폼 및 규모 확장 푸시 인프라에 액세스할 수 있어, 모바일 플랫폼용 소비자 응용 프로그램 및 엔터프라이즈 응용 프로그램 모두에 대한 푸시 알림을 매우 간단하게 구현할 수 있습니다. 이 자습서에서는 Azure 알림 허브를 사용하여 특정 장치에서 특정 앱 사용자에게 푸시 알림을 보내는 방법을 보여 줍니다. 지침 항목 [앱 백 엔드에서 등록]에(http://msdn.microsoft.com/ko-kr/library/dn743807.aspx)나와 있는 대로 ASP.NET WebAPI 백 엔드는 클라이언트를 인증하고 알림을 생성하는 데 사용됩니다. 이 자습서는 **알림 허브 시작** 자습서에서 만든 알림 허브를 기반으로 합니다.

이 자습서는 **보안 푸시** 자습서의 필수 조건이기도 합니다. 이 **사용자에게 알림** 자습서의 단계를 완료해야 푸시 알림을 안전하게 보내도록 **사용자에게 알림** 코드를 수정하는 방법을 보여 주는 **보안 푸시** 자습서를 진행할 수 있습니다. 

> [AZURE.NOTE] 이 자습서에서는  [알림 허브 시작(Windows 스토어)]에(http://azure.microsoft.com/ko-kr/documentation/articles/notification-hubs-windows-store-dotnet-get-started/)설명된 대로 알림을 만들고 구성했다고 가정합니다.
> 모바일 서비스를 백 엔드 서비스로 사용 중인 경우 이 자습서의 [모바일 서비스 버전]을(/ko-kr/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-push-notifications-app-users/) 참조하세요.
>또한 이 자습서에서는 Windows Phone 스토어 8.1 앱을 만듭니다. Windows 스토어 및 Windows Universal 앱에도 같은 코드를 사용할 수 있습니다. 이러한 앱은 모두 Windows(Windows Phone이 아님) 자격 증명을 사용합니다.

## 알림 허브 만들기 및 구성

이 자습서를 시작하기 전에 응용 프로그램 이름을 예약하고 Azure 알림 허브를 만들고 나서 해당 응용 프로그램에 연결해야 합니다. [알림 허브 시작(Windows 스토어)],(http://azure.microsoft.com/ko-kr/documentation/articles/notification-hubs-windows-store-dotnet-get-started/)특히[Windows 스토어에 앱 등록](http://azure.microsoft.com/ko-kr/documentation/articles/notification-hubs-windows-store-dotnet-get-started/#register) 및 [알림 허브 구성](http://azure.microsoft.com/ko-kr/documentation/articles/notification-hubs-windows-store-dotnet-get-started/#configure-hub)섹션의 단계를 수행하세요. 특히 알림 허브의 **구성** 탭에서 포털의 **패키지 SID** 및 **클라이언트 암호** 값을 입력했어야 합니다. 이 구성 절차는 [알림 허브 구성](http://azure.microsoft.com/ko-kr/documentation/articles/notification-hubs-windows-store-dotnet-get-started/#configure-hub)섹션에서 설명합니다. 이는 중요한 단계입니다. 포털의 자격 증명이 선택한 앱 이름에 대해 지정된 자격 증명과 일치하지 않으면 푸시 알림이 실패합니다.

[WACOM.INCLUDE [notification-hubs-aspnet-backend-notifyusers](../includes/notification-hubs-aspnet-backend-notifyusers.md)]

## Windows Phone 프로젝트 만들기

다음은 Windows Phone 응용 프로그램을 만드는 단계입니다. 이 프로젝트를 현재 솔루션에 추가하려면 다음을 수행합니다.

1. 솔루션 탐색기에서 솔루션의 최상위 노드(이 경우 **Solution NotifyUsers**)를 마우스 오른쪽 단추로 클릭하고 **추가**, **새 프로젝트**를 차례로 클릭합니다.

2. **스토어 앱**을 확장하고 **Windows Phone 앱**, **빈 앱(Windows Phone)**을 차례로 클릭합니다.

	![][9]

3. **이름** 상자에 **NotifyUserWindowsPhone**을 입력하고 **확인**을 클릭하여 프로젝트를 생성합니다.

 
4. 이 응용 프로그램을 Windows Phone 스토어와 연결합니다. 솔루션 탐색기에서 **NotifyUserWindowsPhone (Windows Phone 8.1)**을 마우스 오른쪽 단추로 클릭하고 **스토어**, **응용 프로그램을 스토어에 연결...**을 차례로 클릭합니다.

	![][10]
 
5. 마법사의 단계에 따라 로그인하고 앱을 스토어와 연결합니다.

	![][11]
	
	> [AZURE.NOTE] 이 절차 중에 선택하는 응용 프로그램 이름을 기록해 두세요. 이 특정 예약된 앱 이름에 대해 [Windows 개발자 센터]에서(http://go.microsoft.com/fwlink/p/?linkid=266582&clcid=0x409) 얻은 자격 증명을 사용하여 포털에서 알림 허브를 구성해야 합니다. 이 구성 절차는 [알림 허브 구성](http://azure.microsoft.com/ko-kr/documentation/articles/notification-hubs-windows-store-dotnet-get-started/#configure-hub)섹션에서 설명합니다. 이는 중요한 단계입니다. 포털의 자격 증명이 선택한 앱 이름에 대해 지정된 자격 증명과 일치하지 않으면 푸시 알림이 실패합니다.

6. 솔루션 탐색기에서 **NotifyUserWindowsPhone (Windows Phone 8.1)** 프로젝트를 마우스 오른쪽 단추로 클릭하고 **NuGet 패키지 관리**를 클릭합니다.

7. 왼쪽에서 **온라인**을 클릭합니다.

8. **검색** 상자에 **Http 클라이언트**를 입력합니다.

9. 결과 목록에서 **Microsoft HTTP 클라이언트 라이브러리**를 클릭하고 **설치**를 클릭합니다. 설치를 완료합니다.

10. 다시 NuGet **검색** 상자에 **Json.net**을 입력합니다. **Json.NET** 패키지를 설치하고 NuGet 패키지 관리자 창을 닫습니다.

11. 솔루션 탐색기의 **NotifyUserWindowsPhone (Windows Phone 8.1)** 프로젝트에서 **MainPage.xaml**을 두 번 클릭하여 Visual Studio 편집기에서 엽니다.

12. **MainPage.xaml** XML 코드에서 `<Grid>` 섹션을 다음 코드로 바꿉니다.

		<Grid>
	        <Grid.RowDefinitions>
	            <RowDefinition Height="Auto"/>
	            <RowDefinition Height="*"/>
	        </Grid.RowDefinitions>

	        <TextBlock Grid.Row="0" Text="Secure Push" HorizontalAlignment="Center" FontSize="48"/>

        	<StackPanel Grid.Row="1" VerticalAlignment="Center">
        	    <Grid>
        	        <Grid.RowDefinitions>
        	            <RowDefinition Height="Auto"/>
        	            <RowDefinition Height="Auto"/>
        	            <RowDefinition Height="Auto"/>
        	            <RowDefinition Height="Auto"/>
        	            <RowDefinition Height="Auto"/>
        	            <RowDefinition Height="*"/>
        	        </Grid.RowDefinitions>
            	    <TextBlock Grid.Row="0" Text="Username" FontSize="24" Margin="20,0,20,0"/>
            	    <TextBox Name="UsernameTextBox" Grid.Row="1" Margin="20,0,20,0"/>
            	    <TextBlock Grid.Row="2" Text="Password" FontSize="24" Margin="20,0,20,0" />
            	    <PasswordBox Name="PasswordTextBox" Grid.Row="3" Margin="20,0,20,0"/>
	
            	    <Button Grid.Row="4" HorizontalAlignment="Center" VerticalAlignment="Center" Content="1. Login and register" Click="LoginAndRegisterClick" />

            	    <Button Grid.Row="5" HorizontalAlignment="Center" VerticalAlignment="Center" Content="2. Send push" Click="PushClick" />
            	</Grid>
        	</StackPanel>
    	</Grid>


13. 솔루션 탐색기에서 **NotifyUserWindowsPhone (Windows Phone 8.1)** 프로젝트를 마우스 오른쪽 단추로 클릭하고 **추가**, **클래스**를 차례로 클릭합니다. 클래스 이름을 **RegisterClient.cs**로 지정하고 **확인**을 클릭하여 클래스를 생성합니다. 이 구성 요소는 푸시 알림을 등록하기 위해 앱 백 엔드에 접속하는 데 필요한 REST 호출을 구현합니다. 또한 [앱 백 엔드에서 등록]에(http://msdn.microsoft.com/ko-kr/library/dn743807.aspx)설명된 대로 알림 허브에서 생성된 *registrationId*를 로컬로 저장합니다. . 이 구성 요소는 **로그인 및 등록** 단추를 클릭할 때 로컬 저장소에 저장된 인증 토큰을 사용합니다.

14. `RegisterClient` 클래스 정의 내에 다음 코드를 추가합니다. `{backend endpoint}`를 이전 섹션에서 얻은 백 엔드 끝점으로 바꿔야 합니다.

		private string POST_URL = "{backend endpoint}/api/register";

        private class DeviceRegistration
        {
            public string Platform { get; set; }
            public string Handle { get; set; }
            public string[] Tags { get; set; }
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


15. RegisterClient.cs 파일의 맨 위에 다음 `using` 문을 추가합니다.

		using Windows.Storage;
		using System.Net;
		using System.Net.Http;
		using System.Net.Http.Headers;
		using Newtonsoft.Json;
		
16. MainPage.xaml.cs에 단추에 대한 코드를 추가합니다. **로그인 및 등록**에 대한 콜백은 기본 인증 토큰(이는 인증 체계에서 사용하는 모든 토큰을 나타냄)을 로컬 저장소에 저장하고 `RegisterClient`를 사용하여 백 엔드를 호출합니다. **AppBackend**에 대한 콜백은 백 엔드를 호출하여 이 사용자의 모든 장치로 보안 알림을 트리거합니다. 

	MainPage.xaml.cs의 `OnNavigatedTo()` 메서드 뒤에 다음 코드를 추가합니다. `{backend endpoint}`를 이전 섹션에서 얻은 백 엔드 끝점으로 바꿔야 합니다.

		private async void PushClick(object sender, RoutedEventArgs e)
        {
            var POST_URL = "{backend endpoint}/api/notifications";

            using (var httpClient = new HttpClient())
            {
                var settings = ApplicationData.Current.LocalSettings.Values;
                httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Basic", (string) settings["AuthenticationToken"]);

                await httpClient.PostAsync(POST_URL, new StringContent(""));
            }
        }

        private async void LoginAndRegisterClick(object sender, RoutedEventArgs e)
        {
            SetAuthenticationTokenInLocalStorage();
            
            var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();
            await new RegisterClient().RegisterAsync(channel.Uri, new string[] { "myTag" });

            var dialog = new MessageDialog("Registered as: " + UsernameTextBox.Text);
            dialog.Commands.Add(new UICommand("OK"));
            await dialog.ShowAsync();
        }

        private void SetAuthenticationTokenInLocalStorage()
        {
            string username = UsernameTextBox.Text;
            string password = PasswordTextBox.Password;
            
            var token = Convert.ToBase64String(System.Text.Encoding.UTF8.GetBytes(username + ":" + password));
            ApplicationData.Current.LocalSettings.Values["AuthenticationToken"] = token;
        }


17. MainPage.xaml.cs 파일의 맨 위에 다음 `using` 문을 추가합니다.

		using System.Net.Http;
		using Windows.Storage;
		using System.Net.Http.Headers;
		using Windows.Networking.PushNotifications;
		using Windows.UI.Popups;

## 응용 프로그램 실행

응용 프로그램을 실행하려면 다음을 수행합니다.

1. Visual Studio에서 **NotifyUserWindowsPhone (Windows Phone 8.1)** Windows Phone 앱을 실행합니다. Windows Phone 에뮬레이터가 실행되고 자동으로 앱을 로드합니다.

2. **NotifyUserWindowsPhone** 앱 UI에서 사용자 이름과 암호를 입력합니다. 이는 임의 문자열일 수 있지만 같은 값이어야 합니다.

3. **NotifyUserWindowsPhone** 앱 UI에서 **로그인 및 등록**을 클릭합니다. 그리고 나서 **푸시 보내기**를 클릭합니다.


[9]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-secure-push9.png
[10]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-secure-push10.png
[11]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-secure-push11.png
[12]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-secure-push12.png
[13]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-secure-push13.png

<!--HONumber=35.1-->
