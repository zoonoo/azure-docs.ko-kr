
이전 예제에서는 앱이 시작될 때마다 클라이언트가 ID 공급자와 모바일 서비스 둘 다에 접근해야 하는 표준 로그인을 보여 주었습니다. 이 방법은 비효율적일 뿐 아니라 많은 고객이 동시에 앱을 시작하려고 할 경우 사용 관련 문제가 발생할 수도 있습니다. 더 나은 접근 방법은 모바일 서비스에서 반환된 권한 부여 토큰을 캐시한 다음 공급자 기반 로그인을 사용하기 전에 이 토큰을 먼저 사용하는 것입니다.

>[AZURE.NOTE]클라이언트 관리 인증 또는 서비스 관리 인증을 사용하는지에 관계없이 모바일 서비스에서 발급된 토큰을 캐시할 수 있습니다. 이 자습서에서는 서비스 관리 인증을 사용합니다.

1. MainPage.xaml.cs 프로젝트 파일에서 다음 **using** 문을 추가합니다.

		using System.IO.IsolatedStorage;
		using System.Security.Cryptography;		

2. **AuthenticateAsync** 메서드를 다음 코드로 바꿉니다.

        private async System.Threading.Tasks.Task AuthenticateAsync()
        {
            string message;
            // This sample uses the Facebook provider.
            var provider = "Facebook";

            // Provide some additional app-specific security for the encryption.
            byte [] entropy = { 1, 8, 3, 6, 5 };

            // Authorization credential.
            MobileServiceUser user = null;

            // Isolated storage for the app.
            IsolatedStorageSettings settings =
                IsolatedStorageSettings.ApplicationSettings;

            while (user == null)
            {
                // Try to get an existing encrypted credential from isolated storage.                    
                if (settings.Contains(provider))
                {
                    // Get the encrypted byte array, decrypt and deserialize the user.
                    var encryptedUser = settings[provider] as byte[];
                    var userBytes = ProtectedData.Unprotect(encryptedUser, entropy);
                    user = JsonConvert.DeserializeObject<MobileServiceUser>(
                        System.Text.Encoding.Unicode.GetString(userBytes, 0, userBytes.Length));
                }
                if (user != null)
                {
                    // Set the user from the stored credentials.
                    App.MobileService.CurrentUser = user;

                    try
                    {
                        // Try to return an item now to determine if the cached credential has expired.
                        await App.MobileService.GetTable<TodoItem>().Take(1).ToListAsync();
                    }
                    catch (MobileServiceInvalidOperationException ex)
                    {
                        if (ex.Response.StatusCode == System.Net.HttpStatusCode.Unauthorized)
                        {
                            // Remove the credential with the expired token.
                            settings.Remove(provider);
                            user = null;
                            continue;
                        }
                    }
                }
                else
                {
                    try
                    {
                        // Login with the identity provider.
                        user = await App.MobileService
                            .LoginAsync(provider);

                        // Serialize the user into an array of bytes and encrypt with DPAPI.
                        var userBytes = System.Text.Encoding.Unicode
                            .GetBytes(JsonConvert.SerializeObject(user));
                        byte[] encryptedUser = ProtectedData.Protect(userBytes, entropy);

                        // Store the encrypted user credentials in local settings.
                        settings.Add(provider, encryptedUser);
                        settings.Save();
                    }
                    catch (MobileServiceInvalidOperationException ex)
                    {
                        message = "You must log in. Login Required";
                    }
                }
                message = string.Format("You are now logged in - {0}", user.UserId);
                MessageBox.Show(message);
            }
        }

	이 버전의 **AuthenticateAsync**에서는 앱이 로컬 저장소에 저장된 암호화된 자격 증명을 사용하여 모바일 서비스에 액세스합니다. 저장된 토큰이 만료되지 않았는지 확인하기 위해 단순 쿼리가 전송됩니다. 401이 반환되면 일반 공급자 기반 로그인이 시도됩니다. 일반 로그인은 저장된 자격 증명이 없는 경우에도 수행됩니다.

	>[AZURE.NOTE]이 앱은 로그인하는 동안 만료된 토큰을 테스트하지만 토큰 만료는 인증 후 앱을 사용할 때 발생할 수도 있습니다. 토큰 만료와 관련된 권한 부여 오류를 처리하는 방법은 [Azure 모바일 서비스 관리 SDK에서 만료된 토큰 캐시 및 처리](http://blogs.msdn.com/b/carlosfigueira/archive/2014/03/13/caching-and-handling-expired-tokens-in-azure-mobile-services-managed-sdk.aspx) 게시물을 참조하세요.
	
3. 앱을 두 번 다시 시작합니다.

	첫 번째 시작에서는 공급자를 사용한 로그인이 다시 필요합니다. 그러나 두 번째 다시 시작에서는 캐시된 자격 증명이 사용되고 로그인이 무시됩니다.

<!---HONumber=August15_HO6-->