<properties 
	pageTitle="Xamarin Android에서 모바일 앱에 대한 인증 시작" 
	description="모바일 앱을 사용하여 AAD, Google, Facebook, Twitter, Microsoft 등의 다양한 ID 공급자를 통해 Xamarin Android 앱 사용자를 인증하는 방법을 알아봅니다." 
	services="app-service\mobile" 
	documentationCenter="xamarin" 
	authors="mattchenderson" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="app-service" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-xamarin-android" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="06/23/2015" 
	ms.author="mahender"/>

# Xamarin Android 앱에 인증 추가

[AZURE.INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

[AZURE.INCLUDE [app-service-mobile-note-mobile-services-preview](../../includes/app-service-mobile-note-mobile-services-preview.md)]

이 항목에서는 클라이언트 응용 프로그램에서 앱 서비스 모바일 앱의 사용자를 인증하는 방법을 보여 줍니다. 이 자습서에서는 앱 서비스가 지원하는 ID 공급자를 사용하여 퀵 스타트 프로젝트에 인증을 추가합니다. 모바일 앱에서 인증되고 권한이 부여된 후 사용자 ID 값이 표시됩니다.

이 자습서는 모바일 앱 퀵 스타트를 기반으로 합니다. 또한 [Xamarin Android 앱 만들기] 자습서를 먼저 완료해야 합니다.

##<a name="review"></a>서버 프로젝트 구성 검토(옵션)

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-enable-auth-preview](../../includes/app-service-mobile-dotnet-backend-enable-auth-preview.md)]

##<a name="register"></a>인증을 위해 앱 등록 및 앱 서비스 구성

[AZURE.INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

##<a name="permissions"></a>사용 권한을 인증된 사용자로 제한

[AZURE.INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

<ol start="5">
<li><p>Visual Studio 또는 Xamarin Studio에서 클라이언트 프로젝트를 장치 또는 에뮬레이터에서 실행합니다. 앱이 시작된 후 상태 코드 401(인증되지 않음)의 처리되지 않은 예외가 발생하는지 확인합니다.</p>
   
   	<p>이 예외는 앱이 인증되지 않은 사용자로 모바일 앱 코드에 액세스하려고 시도하는데 <em>TodoItem</em> 테이블에서 이제 인증을 요구하기 때문에 발생합니다.</p></li>
</ol>

다음에는 앱 서비스에서 리소스를 요청하기 전에 사용자를 인증하도록 앱을 업데이트합니다.

##<a name="add-authentication"></a>앱에 인증 추가

1. **TodoActivity** 클래스에 다음 속성을 추가합니다.

			private MobileServiceUser user;

2. **TodoActivity** 클래스에 다음 메서드를 추가합니다.

	        private async Task Authenticate()
	        {
	            try
	            {
	                user = await client.LoginAsync(this, MobileServiceAuthenticationProvider.Facebook);
	                CreateAndShowDialog(string.Format("you are now logged in - {0}", user.UserId), "Logged in!");
	            }
	            catch (Exception ex)
	            {
	                CreateAndShowDialog(ex, "Authentication failed");
	            }
	        }

    인증 프로세스를 처리하는 새 메서드가 만들어집니다. 사용자는 Facebook 로그인을 사용하여 인증됩니다. 인증된 사용자의 ID를 표시하는 대화 상자가 나타납니다.

    > [AZURE.NOTE]Facebook 이외의 ID 공급자를 사용하는 경우, 위의 **LoginAsync**에 전달된 값을 _MicrosoftAccount_, _Twitter_, _Google_ 또는 _WindowsAzureActiveDirectory_ 중 하나로 변경합니다.

3. **OnCreate** 메서드에서 `MobileServiceClient` 개체를 인스턴스화하는 코드 다음에 다음 코드 줄을 추가합니다.

		// Get the Mobile App Table instance to use
        toDoTable = client.GetTable <ToDoItem> ();

        await Authenticate(); // add this line

	이 호출은 인증 프로세스를 시작하고 비동기적으로 기다립니다.


4. **Run** 메뉴에서 **Run**을 클릭하여 앱을 시작하고 원하는 ID 공급자에 로그인합니다.

   	성공적으로 로그인하면 앱이 todo 항목 목록을 표시하고 사용자가 데이터를 업데이트할 수 있습니다.


<!-- URLs. -->
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039

[Xamarin Android 앱 만들기]: app-service-mobile-dotnet-backend-xamarin-android-get-started-preview.md

[Azure Management Portal]: https://portal.azure.com
 

<!---HONumber=August15_HO6-->