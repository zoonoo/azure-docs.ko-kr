1.  Visual Studio에서 MainPage.xaml.cs를 열고 파일의 맨 위에 `using` 문을 추가합니다.

        using System.Net.Http;

2.  MainPage.xaml.cs에서, 네임스페이스에 사용자 정보를 직렬화하는 다음 클래스 정의를 추가합니다.

        public class UserInfo
        {
            public String displayName { get; set; }
            public String streetAddress { get; set; }
            public String city { get; set; }
            public String state { get; set; }
            public String postalCode { get; set; }
        }

3.  MainPage.xaml.cs에서, 사용자 지정 API를 호출하여 ADD에서 사용자에 대한 추가 정보를 반환하도록 `AuthenticateAsync` 메서드를 업데이트합니다.

        private async System.Threading.Tasks.Task AuthenticateAsync()
        {
            while (user == null)
            {
                string message;
                try
                {
                    user = await App.MobileService
                        .LoginAsync(MobileServiceAuthenticationProvider.WindowsAzureActiveDirectory);
                    UserInfo userInfo = await App.MobileService.InvokeApiAsync<UserInfo>("getuserinfo", 
                        HttpMethod.Get, null);
                    message = string.Format("{0}, you are now logged in.\n\nYour address is...\n\n{1}\n{2}, {3} {4}", 
                        userInfo.displayName, userInfo.streetAddress, userInfo.city, userInfo.state, 
                        userInfo.postalCode);
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

4.  변경 내용을 저장하고 서비스를 빌드하여 구문 오류가 없는지 확인합니다.


