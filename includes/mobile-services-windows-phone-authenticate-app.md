1. 프로젝트 파일 mainpage.xaml.cs를 열고 MainPage 클래스에 다음 코드 조각을 추가합니다.
	
        private MobileServiceUser user;
        private async System.Threading.Tasks.Task Authenticate()
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

                MessageBox.Show(message);
            }
        }

    현재 사용자를 저장하기 위한 멤버 변수와 인증 프로세스를 처리할 메서드가 만들어집니다. 사용자는 Facebook 로그인을 사용하여 인증됩니다.

    >[WACOM.NOTE]Facebook 이외의 ID 공급자를 사용하는 경우 위의 <strong>MobileServiceAuthenticationProvider</strong> 값을 공급자에 대한 값으로 변경합니다.</p>
    </div>

2. 기존 **OnNavigatedTo** 메서드 재정의를 메서드 삭제하거나 주석으로 처리하고 페이지의 **Loaded** 이벤트를 처리하는 다음 메서드로 바꿉니다. 

        async void MainPage_Loaded(object sender, RoutedEventArgs e)
        {
            await Authenticate();
            RefreshTodoItems();
        }

   	이 메서드는 새 **Authenticate** 메서드를 호출합니다. 

3. MainPage 생성자를 다음 코드로 바꿉니다.

        // Constructor
        public MainPage()
        {
            InitializeComponent();
            this.Loaded += MainPage_Loaded;
        }

   	이 생성자는 또한 Loaded 이벤트의 처리기를 등록합니다.
		
4. F5 키를 눌러 앱을 실행하고 선택한 ID 공급자로 앱에 로그인합니다. 

   	로그인하고 나면 앱이 오류 없이 실행되며 모바일 서비스를 쿼리하고 데이터를 업데이트할 수 있게 됩니다.
