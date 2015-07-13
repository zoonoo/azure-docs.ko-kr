
이제 등록이 시도되기 전에 사용자가 인증되도록 푸시 알림이 등록되는 방법을 변경해야 합니다. 클라이언트 앱 업데이트는 푸시 알림을 구현한 방법에 따라 달라집니다.

###Visual Studio 2013 업데이트 2 이상 버전에서 푸시 알림 추가 마법사 사용

이 방법에서는 마법사가 프로젝트에 새 push.register.cs 파일을 생성합니다.

1. Visual Studio 솔루션 탐색기에서 app.xaml.cs 프로젝트 파일을 열고 **OnLaunched** 이벤트 처리기에서 **UploadChannel** 메서드에 대한 호출을 주석 처리하거나 삭제합니다. 

2. push.register.cs 프로젝트 파일을 열고 **UploadChannel** 메서드를 다음 코드로 바꿉니다.

		public async static void UploadChannel()
		{
		    var channel = 
		        await Windows.Networking.PushNotifications.PushNotificationChannelManager
		        .CreatePushNotificationChannelForApplicationAsync();
		
		    try
		    {
		        // Create a native push notification registration.
		        await App.MobileService.GetPush().RegisterNativeAsync(channel.Uri);		        
		
		    }
		    catch (Exception exception)
		    {
		        HandleRegisterException(exception);
		    }
		}

	이는 인증된 사용자 자격 증명이 있는 동일한 클라이언트 인스턴스를 사용하여 등록이 수행되도록 합니다. 인증된 사용자 자격 증명이 없으면 권한 없음(401) 오류로 인해 등록에 실패합니다.

3. MainPage.xaml.cs 프로젝트 파일을 열고 **OnNavigatedTo** 메서드 재정의를 다음 코드로 바꿉니다.

	    protected override async void OnNavigatedTo(NavigationEventArgs e)
        {
            await AuthenticateAsync();            
            todolistPush.UploadChannel();
            RefreshTodoItems();
        }

	이 코드에서는 생성된 푸시 클래스 이름(`todolistPush`)을 마법사에 의해 생성된 클래스 이름(일반적으로 <code><em>mobile_service</em>Push</code> 형식)으로 바꿔야 합니다.

###수동으로 설정한 푸시 알림		

이 방법에서는 사용자가 app.xaml.cs 프로젝트 파일에 자습서의 등록 코드를 직접 추가합니다.

1. Visual Studio 솔루션 탐색기에서 app.xaml.cs 프로젝트 파일을 열고 **OnLaunched** 이벤트 처리기에서 **InitNotificationsAsync** 메서드에 대한 호출을 주석 처리하거나 삭제합니다. 
 
2. **InitNotificationsAsync** 메서드의 액세스 가능성을 `private`에서 `public`(으)로 변경하고 `static` 한정자를 추가합니다.

3. MainPage.xaml.cs 프로젝트 파일을 열고 **OnNavigatedTo** 메서드 재정의를 다음 코드로 바꿉니다.

	    protected override async void OnNavigatedTo(NavigationEventArgs e)
        {
            await AuthenticateAsync();            
            App.InitNotificationsAsync();
            RefreshTodoItems();
        }

<!---HONumber=62-->