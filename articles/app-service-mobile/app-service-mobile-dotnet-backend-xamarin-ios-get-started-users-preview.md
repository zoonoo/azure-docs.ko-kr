<properties 
	pageTitle="Xamarin iOS에서 모바일 앱에 대한 인증 시작" 
	description="모바일 앱을 사용하여 AAD, Google, Facebook, Twitter, Microsoft 등의 다양한 ID 공급자를 통해 Xamarin iOS 앱 사용자를 인증하는 방법을 알아봅니다." 
	services="app-service\mobile" 
	documentationCenter="xamarin" 
	authors="mattchenderson" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="app-service" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-xamarin-ios" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="06/30/2015" 
	ms.author="mahender"/>

# Xamarin.iOS 앱에 인증 추가

[AZURE.INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

이 항목에서는 클라이언트 응용 프로그램에서 앱 서비스 모바일 앱의 사용자를 인증하는 방법을 보여 줍니다. 이 자습서에서는 앱 서비스가 지원하는 ID 공급자를 사용하여 퀵 스타트 프로젝트에 인증을 추가합니다. 모바일 앱에서 인증되고 권한이 부여된 후 사용자 ID 값이 표시됩니다.

이 자습서는 모바일 앱 퀵 스타트를 기반으로 합니다. 또한 [Xamarin.iOS 앱 만들기] 자습서를 먼저 완료해야 합니다.

##<a name="register"></a>인증을 위해 앱 등록 및 앱 서비스 구성

[AZURE.INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

##<a name="permissions"></a>사용 권한을 인증된 사용자로 제한

[AZURE.INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

<ol start="7">
<li><p>Visual Studio 또는 Xamarin Studio에서 클라이언트 프로젝트를 장치 또는 시뮬레이터에서 실행합니다. 앱이 시작된 후 상태 코드 401(인증되지 않음)의 처리되지 않은 예외가 발생하는지 확인합니다.</p>
   
   	<p>이 예외는 앱이 인증되지 않은 사용자로 모바일 앱 코드에 액세스하려고 시도하는데 <em>TodoItem</em> 테이블에서 이제 인증을 요구하기 때문에 발생합니다.</p></li>
</ol>

다음에는 앱 서비스에서 리소스를 요청하기 전에 사용자를 인증하도록 앱을 업데이트합니다.

##<a name="add-authentication"></a>앱에 인증 추가

이 섹션에서는 데이터 표시 전에 로그인 화면을 표시하도록 앱을 수정합니다. 앱이 시작될 때 앱은 앱 서비스에 연결하지 않으며, 데이터를 표시하지 않습니다. 사용자가 새로 고침 제스처를 처음 수행한 다음 로그인 화면이 나타나고, 로그인이 성공하면 todo 항목 목록이 표시됩니다.

1. 클라이언트 프로젝트에서 **QSTodoService.cs** 파일을 열고 다음 선언을 QSTodoService에 추가합니다.

		// Logged in user
		private MobileServiceUser user; 
		public MobileServiceUser User { get { return user; } }

2. 다음 정의를 포함하여 새 메서드 **Authenticate**를 **QSTodoService**에 추가합니다.

        public async Task Authenticate(UIViewController view)
        {
            try
            {
                user = await client.LoginAsync(view, MobileServiceAuthenticationProvider.Facebook);
            }
            catch (Exception ex)
            {
                Console.Error.WriteLine (@"ERROR - AUTHENTICATION FAILED {0}", ex.Message);
            }
        }

> [AZURE.NOTE]Facebook 이외의 ID 공급자를 사용하는 경우, 위의 **LoginAsync**에 전달된 값을 _MicrosoftAccount_, _Twitter_, _Google_ 또는 _WindowsAzureActiveDirectory_ 중 하나로 변경합니다.

3. **QSTodoListViewController.cs**를 엽니다. **ViewDidLoad**의 메서드 정의를 수정해서 끝 부근의 **RefreshAsync()** 호출을 제거합니다.

		public override async void ViewDidLoad ()
		{
			base.ViewDidLoad ();

			todoService = QSTodoService.DefaultService;
           await todoService.InitializeStoreAsync ();

           RefreshControl.ValueChanged += async (sender, e) => {
                await RefreshAsync ();
           }

			// Comment out the call to RefreshAsync
			// await RefreshAsync ();
		}


4. **User** 속성이 null인 경우 인증을 수행하고 로그인 화면을 표시하도록 **RefreshAsync** 메서드를 수정합니다. 다음 코드의 메서드 정의 상단에서 다음을 수행합니다.

		// start of RefreshAsync method
		if (todoService.User == null) {
			await QSTodoService.DefaultService.Authenticate (this);
			if (todoService.User == null) {
				Console.WriteLine ("couldn't login!!");
				return;
			}
		}
		// rest of RefreshAsync method
	
5. **실행** 단추를 눌러 프로젝트를 빌드하고 iPhone 시뮬레이터에서 앱을 시작합니다. 앱이 데이터를 표시하지 않는지 확인합니다.

	항목 목록을 아래로 끌어서 새로 고침 제스처를 수행하고 로그인 화면이 나타나도록 합니다. 유효한 자격 증명을 성공적으로 입력하면 앱이 todo 항목 목록을 표시하고 사용자가 데이터를 업데이트할 수 있습니다.

 
<!-- URLs. -->
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Xamarin.iOS 앱 만들기]: app-service-mobile-dotnet-backend-xamarin-ios-get-started-preview.md

[Azure Management Portal]: https://portal.azure.com
 

<!---HONumber=July15_HO3-->