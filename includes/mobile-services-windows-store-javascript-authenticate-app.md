

1. default.js 프로젝트 파일을 열고 **app.OnActivated** 메서드 오버로드에서 **refreshTodoItems** 메서드에 대한 마지막 호출을 다음 코드로 바꿉니다. 
	
        var userId = null;

        // Request authentication from Mobile Services using a Facebook login.
        var login = function () {
            return new WinJS.Promise(function (complete) {
                client.login("facebook").done(function (results) {
                    userId = results.userId;
                    refreshTodoItems();
                    var message = "You are now logged in as: " + userId;
                    var dialog = new Windows.UI.Popups.MessageDialog(message);
                    dialog.showAsync().done(complete);
                }, function (error) {
                    userId = null;
                    var dialog = new Windows.UI.Popups
                        .MessageDialog("An error occurred during login", "Login Required");
                    dialog.showAsync().done(complete);
                });
            });
        }            

        var authenticate = function () {
            login().then(function () {
                if (userId === null) {

                    // Authentication failed, try again.
                    authenticate();
                }
            });
        }

        authenticate();

    현재 사용자를 저장하기 위한 멤버 변수와 인증 프로세스를 처리할 메서드가 만들어집니다. 사용자는 Facebook 로그인을 사용하여 인증됩니다. Facebook 이외의 ID 공급자를 사용하는 경우 위의 <strong>login</strong> 메서드에 전달된 값을 _microsoftaccount_, _twitter_, _google_ 또는 _windowsazureactivedirectory_ 중 하나로 변경합니다.

    >[AZURE.NOTE]모바일 서비스에 Windows 스토어 앱 패키지 정보를 등록한 경우 <em>useSingleSignOn</em> 매개 변수에 <strong>true</strong> 값을 제공하여 <a href="http://go.microsoft.com/fwlink/p/?LinkId=322050" target="_blank">login</a> 메서드를 호출해야 합니다. 그렇지 않으면 로그인 메서드가 호출될 때마다 사용자에게 로그인 프롬프트가 표시됩니다.

2. F5 키를 눌러 앱을 실행하고 선택한 ID 공급자로 앱에 로그인합니다.

   	로그인하고 나면 앱이 오류 없이 실행되며 모바일 서비스를 쿼리하고 데이터를 업데이트할 수 있게 됩니다.

<!---HONumber=August15_HO6-->