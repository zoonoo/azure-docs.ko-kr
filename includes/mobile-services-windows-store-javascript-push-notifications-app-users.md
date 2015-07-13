
이제 등록이 시도되기 전에 사용자가 인증되도록 푸시 알림이 등록되는 방법을 변경해야 합니다. 클라이언트 앱 업데이트는 푸시 알림을 구현한 방법에 따라 달라집니다.

###Visual Studio 2013 업데이트 2 이상 버전에서 푸시 알림 마법사 사용

이 방법에서는 마법사가 프로젝트에 새 push.register.js 및 service.js 파일을 생성합니다.

1. Visual Studio 솔루션 탐색기에서 push.register.js 프로젝트 파일을 열고 **addEventListener**에 대한 호출을 주석 처리하거나 삭제합니다. 

2. default.js 프로젝트 파일에서 기존 **login** 함수를 다음 코드로 바꿉니다.
 
		// Request authentication from Mobile Services using a Facebook login.
		var login = function () {
		    return new WinJS.Promise(function (complete) {
		        client.login("facebook").done(function (results) {
		            userId = results.userId;
		            // Request a push notification channel.
		            Windows.Networking.PushNotifications
		                .PushNotificationChannelManager
		                .createPushNotificationChannelForApplicationAsync()
		                .then(function (channel) {
		                    // Register for notifications using the new channel
		                    client.push.registerNative(channel.uri);
		                });
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

###수동으로 설정한 푸시 알림		

이 방법에서는 사용자가 default.js 프로젝트 파일에 자습서의 등록 코드를 직접 추가합니다.

1. Visual Studio 솔루션 탐색기에서 default.js 프로젝트 파일을 열고 **onActivated** 이벤트 처리기에서 다음과 같이 **createPushNotificationChannelForApplicationAsync** 함수를 호출하는 코드 줄을 찾습니다.

		// Request a push notification channel.
		Windows.Networking.PushNotifications
		    .PushNotificationChannelManager
		    .createPushNotificationChannelForApplicationAsync()
		    .then(function (channel) {
		        // Register for notifications using the new channel
		        client.push.registerNative(channel.uri);
		    }); 
 
2. **login** 함수가 다음과 같이 되도록 이 코드 줄을 **login** 함수에서 **refreshTodoItems**에 대한 호출 바로 앞으로 이동합니다.
 
		// Request authentication from Mobile Services using a Facebook login.
		var login = function () {
		    return new WinJS.Promise(function (complete) {
		        client.login("facebook").done(function (results) {
		            userId = results.userId;
		            // Request a push notification channel.
		            Windows.Networking.PushNotifications
		                .PushNotificationChannelManager
		                .createPushNotificationChannelForApplicationAsync()
		                .then(function (channel) {
		                    // Register for notifications using the new channel
		                    client.push.registerNative(channel.uri);
		                });
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

<!---HONumber=July15_HO1-->