
다음으로, 등록을 시도하기 전에 사용자가 인증되도록 푸시 알림을 등록하는 방법을 변경해야 합니다.

1. Visual Studio의 솔루션 탐색기에서 app.xaml.cs 프로젝트 파일을 열고 **Application_Launching** 이벤트 처리기에서 **AcquirePushChannel** 메서드에 대한 호출을 주석으로 처리하거나 삭제합니다. 
 
2. **AcquirePushChannel** 메서드의 액세스 가능성을 `private`에서 `public`(으)로 변경하고 `static` 한정자를 추가합니다.

3. MainPage.xaml.cs 프로젝트 파일을 열고 **OnNavigatedTo** 메서드 재정의를 다음 코드로 바꿉니다.

	    protected override async void OnNavigatedTo(NavigationEventArgs e)
        {
            await AuthenticateAsync();            
            App.AcquirePushChannel();
            RefreshTodoItems();
        }

<!---HONumber=July15_HO4-->