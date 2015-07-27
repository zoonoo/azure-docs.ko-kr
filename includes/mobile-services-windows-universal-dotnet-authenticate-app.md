
1. 공유 프로젝트 파일 MainPage.cs를 열고 MainPage 클래스에 다음 코드 조각을 추가합니다.
	
		// Define a member variable for storing the signed-in user. 
        private MobileServiceUser user;

		// Define a method that performs the authentication process
		// using a Facebook sign-in. 
        private async System.Threading.Tasks.Task AuthenticateAsync()
        {
            while (user == null)
            {
                string message;
                try
                {
					// Change 'MobileService' to the name of your MobileServiceClient instance.
					// Sign-in using Facebook authentication.
                    user = await App.MobileService
                        .LoginAsync(MobileServiceAuthenticationProvider.Facebook);
                    message = 
                        string.Format("You are now signed in - {0}", user.UserId);
                }
                catch (InvalidOperationException)
                {
                    message = "You must log in. Login Required";
                }
                        
                var dialog = new MessageDialog(message);
                dialog.Commands.Add(new UICommand("OK"));
                await dialog.ShowAsync();
            }
        }

    사용자는 Facebook 로그인을 사용하여 인증됩니다. Facebook 이외의 ID 공급자를 사용하는 경우 위의 **MobileServiceAuthenticationProvider** 값을 공급자에 대한 값으로 변경합니다.

3. 기존 **OnNavigatedTo** 메서드 재정의에서 **RefreshTodoItems** 메서드 호출을 주석 처리하거나 삭제합니다.

	그러면 사용자가 인증되기 전에 데이터가 로드되지 않습니다.

	>[AZURE.NOTE]Windows Phone 스토어 8.1 앱에서 정상적으로 인증하려면 **OnNavigated** 메서드가 호출되고 페이지의 **Loaded** 이벤트가 발생한 후에 LoginAsync를 호출해야 합니다. 이 자습서에서는 앱에 **로그인** 단추를 추가하여 이 작업을 수행합니다.

4. MainPage 클래스에 다음 코드 조각을 추가합니다.

        private async void ButtonLogin_Click(object sender, RoutedEventArgs e)
        {
            // Login the user and then load data from the mobile service.
            await AuthenticateAsync();

            // Hide the login button and load items from the mobile service.
            this.ButtonLogin.Visibility = Windows.UI.Xaml.Visibility.Collapsed;
            await RefreshTodoItems();
        }
		
5. Windows 스토어 앱 프로젝트에서 MainPage.xaml 프로젝트 파일을 열고 **저장** 단추를 정의하는 요소 바로 앞에 다음 **Button** 요소를 추가합니다.

		<Button Name="ButtonLogin" Click="ButtonLogin_Click" 
                        Visibility="Visible">Sign in</Button>

6. Windows Phone 앱 프로젝트에서 **저장** 단추를 정의하는 요소 바로 앞에 다음 **단추** 요소를 추가합니다.

		<Button Grid.Row ="1" Grid.Column="1" Name="ButtonLogin" Click="ButtonLogin_Click" 
                        Visibility="Visible" Margin="10, 0, 0, 0">Sign in</Button> 

5. 공유 App.xaml.cs 프로젝트 파일을 열고 다음 using 문이 없으면 추가합니다.

        using Microsoft.WindowsAzure.MobileServices;  
 
6. App.xaml.cs 프로젝트 파일에서 다음 코드를 추가합니다.

        protected override void OnActivated(IActivatedEventArgs args)
        {
			// Windows Phone 8.1 requires you to handle the respose from the WebAuthenticationBroker.
            #if WINDOWS_PHONE_APP
            if (args.Kind == ActivationKind.WebAuthenticationBrokerContinuation)
            {
				// Completes the sign-in process started by LoginAsync.
				// Change 'MobileService' to the name of your MobileServiceClient instance. 
                App.MobileService.LoginComplete(args as WebAuthenticationBrokerContinuationEventArgs);
            }
            #endif

            base.OnActivated(args);
        }

	**OnActivated** 메서드가 이미 있으면 `#if...#endif` 코드 블록만 추가합니다.

8. F5 키를 눌러 Windows 스토어 앱을 실행하고 **로그인** 단추를 클릭한 다음 선택한 ID 공급자로 앱에 로그인합니다.

   	로그인하고 나면 앱이 오류 없이 실행되며 모바일 서비스를 쿼리하고 데이터를 업데이트할 수 있게 됩니다.

9. Windows Phone 스토어 앱 프로젝트를 마우스 오른쪽 단추로 클릭하고 **시작 프로젝트로 설정**을 클릭한 다음 위의 단계를 반복하여 Windows Phone 스토어 앱도 올바르게 실행되는지 확인합니다.

<!---HONumber=July15_HO3-->