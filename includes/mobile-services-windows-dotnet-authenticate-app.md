
1. 프로젝트 파일 MainPage.xaml.cs를 열고 다음 using 문을 추가합니다.

        using Windows.UI.Popups;

2. MainPage 클래스에 다음 코드 조각을 추가합니다.
	
        private MobileServiceUser user;
        private async System.Threading.Tasks.Task AuthenticateAsync()
        {
            while (user == null)
            {
                string message;
                try
                {
                    user = await App.MobileService
                        .LoginAsync(MobileServiceAuthenticationProvider.Facebook);
                    message = 
                        string.Format("You are now logged in - {0}", user.UserId);
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

    현재 사용자를 저장하기 위한 멤버 변수와 인증 프로세스를 처리할 메서드가 만들어집니다. 사용자는 Facebook 로그인을 사용하여 인증됩니다. Facebook 이외의 ID 공급자를 사용하는 경우 위의 **MobileServiceAuthenticationProvider** 값을 공급자에 대한 값으로 변경합니다.

3. 기존 **OnNavigatedTo** 메서드 재정의를 새 **Authenticate** 메서드를 호출하는 다음 메서드로 바꿉니다.

        protected override async void OnNavigatedTo(NavigationEventArgs e)
        {
            await AuthenticateAsync();
            RefreshTodoItems();
        }
		
4. F5 키를 눌러 앱을 실행하고 선택한 ID 공급자로 앱에 로그인합니다.

   	로그인하고 나면 앱이 오류 없이 실행되며 모바일 서비스를 쿼리하고 데이터를 업데이트할 수 있게 됩니다.

<!---HONumber=August15_HO6-->