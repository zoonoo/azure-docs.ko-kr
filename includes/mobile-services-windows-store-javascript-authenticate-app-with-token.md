
이전 예제에서는 앱이 시작될 때마다 클라이언트가 ID 공급자와 모바일 서비스 둘 다에 접근해야 하는 표준 로그인을 보여 주었습니다. 이 방법은 비효율적일 뿐 아니라 많은 고객이 동시에 앱을 시작하려고 할 경우 사용 관련 문제가 발생할 수도 있습니다. 더 나은 접근 방법은 모바일 서비스에서 반환된 권한 부여 토큰을 캐시한 다음 공급자 기반 로그인을 사용하기 전에 이 토큰을 먼저 사용하는 것입니다.

>[WACOM.NOTE]클라이언트 관리 인증 또는 서비스 관리 인증을 사용하는지에 관계없이 모바일 서비스에서 발급된 토큰을 캐시할 수 있습니다. 이 자습서에서는 서비스 관리 인증을 사용합니다.

1. default.js 프로젝트 파일에서 기존 **Login** 함수를 다음 코드로 바꿉니다.

        var credential = null;
        var vault = new Windows.Security.Credentials.PasswordVault();

        // Request authentication from Mobile Services using a Facebook login.
        var login = function () {
            return new WinJS.Promise(function (complete) {
                client.login("facebook").done(function (results) {
                    // Create a credential for the returned user.
                    credential = new Windows.Security.Credentials
                        .PasswordCredential("Facebook", results.userId,
                        results.mobileServiceAuthenticationToken);
                    vault.add(credential);
                    userId = results.userId;

                    refreshTodoItems();
                    var message = "You are now logged in as: " + userId;
                    var dialog = new Windows.UI.Popups.MessageDialog(message);
                    dialog.showAsync().done(complete);
                }, function (error) {
                    var dialog = new Windows.UI.Popups
                        .MessageDialog("An error occurred during login", "Login Required");
                    dialog.showAsync().done(complete);
                });
            });
        }

2. 기존 **authenticate** 함수를 다음 코드로 바꿉니다.

        var authenticate = function () {
            // Try to get a stored credential from the PasswordVault.                
            try{
                credential = vault.findAllByResource("Facebook").getAt(0);
            }
            catch (error) {
                // This is expected when there's no stored credential.
            }
            
            if (credential) {
                // Set the user from the returned credential.   
                credential.retrievePassword();
                client.currentUser = {
                    "userId": credential.userName,
                    "mobileServiceAuthenticationToken": credential.password
                };

                // Try to return an item now to determine if the cached credential has expired.
                todoTable.take(1).read()
                            .done(function () {
                                refreshTodoItems();
                            }, function (error) {
                                if (error.request.status === 401) {
                                    login(credential, vault).then(function () {
                                        if (!credential) {

                                            // Authentication failed, try again.
                                            authenticate();
                                        }
                                    });
                                }                                   
                            });
            } else {

                login().then(function () {
                    if (!credential) {
                        // Authentication failed, try again.
                        authenticate();
                    }
                });
            }
        }

	이 버전의 **authenticate**에서는 앱이 **PasswordVault**에 저장된 자격 증명을 사용하여 모바일 서비스에 액세스합니다. 저장된 토큰이 만료되지 않았는지 확인하기 위해 단순 쿼리가 전송됩니다. 401이 반환되면 일반 공급자 기반 로그인이 시도됩니다. 일반 로그인은 저장된 자격 증명이 없는 경우에도 수행됩니다.

3. 앱을 두 번 다시 시작합니다.

	첫 번째 시작에서는 공급자를 사용한 로그인이 다시 필요합니다. 그러나 두 번째 다시 시작에서는 캐시된 자격 증명이 사용되고 로그인이 무시됩니다. 
