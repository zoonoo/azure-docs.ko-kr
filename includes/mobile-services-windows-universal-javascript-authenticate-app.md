

1. default.js 프로젝트 파일을 열고 **app.OnActivated** 메서드 오버로드에서 **refreshTodoItems** 메서드에 대한 마지막 호출을 다음 코드로 바꿉니다. 

        // Define a member variable for storing the signed-in user.
        var userId = null;

        // Request authentication from Mobile Services using a Facebook login.
        var authenticate = function () {
            return new WinJS.Promise(function (complete) {
                // Change 'client' to the name of your MobileServiceClient instance.
                // Sign-in using Facebook authentication.
                client.login("facebook").done(function (results) {
                    userId = results.userId;
                    refreshTodoItems();
                    buttonLogin.disabled = true;
                    var message = "You are now signed in as: " + userId;
                    var dialog = new Windows.UI.Popups.MessageDialog(message);
                    dialog.showAsync().done(complete);
                }, function (error) {
                    userId = null;
                    var dialog = new Windows.UI.Popups
                    .MessageDialog(error);
                        //.MessageDialog("An error occurred during login", "Login Required");
                    dialog.showAsync().done(complete);
                });
            });
        };

        // Handle the sign in button click event.
        buttonLogin.addEventListener("click", function () {
            authenticate();
        });

	Facebook 이외의 ID 공급자를 사용하는 경우 위의 <strong>login</strong> 메서드에 전달된 값을 _microsoftaccount_, _twitter_, _google_ 또는 _windowsazureactivedirectory_ 중 하나로 변경합니다.

    >[AZURE.NOTE]모바일 서비스에 Windows 스토어 앱 패키지 정보를 등록한 경우 <em>useSingleSignOn</em> 매개 변수에 <strong>true</strong> 값을 제공하여 <a href="http://go.microsoft.com/fwlink/p/?LinkId=322050" target="_blank">login</a> 메서드를 호출해야 합니다. 그렇지 않으면 로그인 메서드가 호출될 때마다 사용자에게 로그인 프롬프트가 표시됩니다.

2. Windows 스토어 앱 프로젝트에서 default.html 프로젝트 파일을 열고 **저장** 단추를 정의하는 요소 바로 앞에 다음 **button** 요소를 추가합니다.

      	<button id="buttonLogin" style="margin-left: 5px">Sign in</button>

3. F5 키를 눌러 앱을 실행하고 선택한 ID 공급자로 앱에 로그인합니다.

   	로그인하고 나면 앱이 오류 없이 실행되며 모바일 서비스를 쿼리하고 데이터를 업데이트할 수 있게 됩니다.

<!---HONumber=Oct15_HO3-->