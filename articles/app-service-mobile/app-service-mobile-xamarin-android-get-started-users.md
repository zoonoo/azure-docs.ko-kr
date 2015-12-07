<properties 
	pageTitle="Xamarin Android에서 모바일 앱에 대한 인증 시작" 
	description="모바일 앱을 사용하여 AAD, Google, Facebook, Twitter, Microsoft 등의 다양한 ID 공급자를 통해 Xamarin Android 앱 사용자를 인증하는 방법을 알아봅니다." 
	services="app-service\mobile" 
	documentationCenter="xamarin" 
	authors="mattchenderson" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="app-service-mobile" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-xamarin-android" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="08/27/2015" 
	ms.author="mahender"/>

# Xamarin Android 앱에 인증 추가

[AZURE.INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]&nbsp;[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

이 항목에서는 클라이언트 응용 프로그램에서 모바일 앱의 사용자를 인증하는 방법을 보여 줍니다. 이 자습서에서는 Azure 모바일 앱이 지원하는 ID 공급자를 사용하여 퀵 스타트 프로젝트에 인증을 추가합니다. 모바일 앱에서 인증되고 권한이 부여된 후 사용자 ID 값이 표시됩니다.

이 자습서는 모바일 앱 퀵 스타트를 기반으로 합니다. 또한 [Xamarin Android 앱 만들기] 자습서를 먼저 완료해야 합니다. 다운로드한 빠른 시작 서버 프로젝트를 사용하지 않는 경우 프로젝트에 인증 확장 패키지를 추가해야 합니다. 서버 확장 패키지에 대한 자세한 내용은 [Azure 모바일 앱용 .NET 백 엔드 서버 SDK 사용](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md)을 참조하세요.

##<a name="register"></a>인증을 위해 앱 등록 및 앱 서비스 구성

[AZURE.INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

##<a name="permissions"></a>사용 권한을 인증된 사용자로 제한

[AZURE.INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

Visual Studio 또는 Xamarin Studio에서 클라이언트 프로젝트를 장치 또는 에뮬레이터에서 실행합니다. 앱 시작 후 상태 코드가 401(권한이 부여되지 않음)인 처리되지 않은 예외가 발생했는지 확인합니다. 이 예외는 앱이 인증되지 않은 사용자로 모바일 앱 백 엔드에 액세스하려고 시도합니다. *TodoItem* 테이블에서 이제 인증을 요구합니다.

다음으로 클라이언트 앱을 업데이트하여 모바일 앱 백엔드에서 인증된 된 사용자를 사용하여 리소스를 요청합니다.

##<a name="add-authentication"></a>앱에 인증 추가

앱은 데이터가 표시되기 전에 사용자가 **로그인** 단추를 탭하고 인증하는 것을 요구하도록 업데이트되었습니다.

1. **TodoActivity** 클래스에 다음 코드를 추가합니다.

	    // Define a authenticated user.
	    private MobileServiceUser user;
	    private async Task<bool> Authenticate()
	    {
	            var success = false;
	            try
	            {
	                // Sign in with Facebook login using a server-managed flow.
	                user = await client.LoginAsync(this,
	                    MobileServiceAuthenticationProvider.Facebook);
	                CreateAndShowDialog(string.Format("you are now logged in - {0}",
	                    user.UserId), "Logged in!");
	
	                success = true;
	            }
	            catch (Exception ex)
	            {
	                CreateAndShowDialog(ex, "Authentication failed");
	            }
	            return success;
	    }

        [Java.Interop.Export()]
        public async void LoginUser(View view)
        {
            // Load data only after authentication succeeds.
            if (await Authenticate())
            {
                //Hide the button after authentication succeeds. 
                FindViewById<Button>(Resource.Id.buttonLoginUser).Visibility = ViewStates.Gone;

                // Load the data.
                OnRefreshItemsSelected();
            }
        }

    이렇게 하면 사용자를 인증하는 새 메서드와 새 **로그인** 단추에 대한 메서드 처리기가 생성됩니다. 위 예제 코드의 사용자는 Facebook 로그인을 사용하여 인증됩니다. 대화 상자는 한 번 인증된 사용자 ID를 표시하는 데 사용됩니다.

    > [AZURE.NOTE]Facebook 이외의 ID 공급자를 사용하는 경우, 위의 **LoginAsync**에 전달된 값을 _MicrosoftAccount_, _Twitter_, _Google_ 또는 _WindowsAzureActiveDirectory_ 중 하나로 변경합니다.

3. **OnCreate** 메서드에서 다음 코드 줄을 삭제하거나 주석으로 처리합니다.

		OnRefreshItemsSelected ();

4. Activity\_To\_Do.axml 파일에서 기존의 *AddItem* 단추 전에 다음 *LoginUser* 단추 정의를 추가합니다.

      	<Button
            android:id="@+id/buttonLoginUser"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:onClick="LoginUser"
            android:text="@string/login_button_text" />

4. Visual Studio 또는 Xamarin Studio에서 클라이언트 프로젝트를 장치 또는 에뮬레이터에서 실행하고 선택된 ID 공급자로 로그인합니다.

   	성공적으로 로그인하면 앱이 로그인 ID 및 todo 항목 목록을 표시하고 사용자가 데이터를 업데이트할 수 있습니다.


<!-- URLs. -->
[Xamarin Android 앱 만들기]: app-service-mobile-xamarin-android-get-started.md
[Azure Management Portal]: https://portal.azure.com
 

<!---HONumber=AcomDC_1125_2015-->