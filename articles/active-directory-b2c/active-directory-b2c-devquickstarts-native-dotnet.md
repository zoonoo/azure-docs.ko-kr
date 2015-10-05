<properties
	pageTitle="Azure AD B2C 미리 보기 | Microsoft Azure"
	description="로그인, 등록 및 Azure AD B2C를 사용하는 프로필 관리를 사용하여 Windows 데스크톱 응용 프로그램을 빌드하는 방법입니다."
	services="active-directory-b2c"
	documentationCenter=".net"
	authors="dstrockis"
	manager="msmbaldwin"
	editor=""/>

<tags
	ms.service="active-directory-b2c"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="09/22/2015"
	ms.author="dastrock"/>

# Azure AD B2C 미리 보기: Windows 데스크톱 앱 빌드

<!-- TODO [AZURE.INCLUDE [active-directory-b2c-devquickstarts-native-switcher](../../includes/active-directory-b2c-devquickstarts-native-switcher.md)]-->

Azure AD B2C를 사용하여 몇가지 간단한 단계에서 강력한 셀프 서비스 ID 관리 기능을 데스크톱 앱에 추가할 수 있습니다. 이 문서에서는 사용자 등록, 로그인 및 프로필 관리를 포함하는 .NET WPF "할 일 모음" 앱을 만드는 방법을 보여줍니다. Facebook 및 Google과 같은 소셜 계정 뿐만 아니라 사용자 이름 또는 전자 메일로 등록 및 로그인에 대한 지원을 포함합니다.

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## 1\. Azure AD B2C 디렉터리 가져오기

Azure AD B2C를 사용하기 전에 디렉터리 또는 테넌트를 만들어야 합니다. 디렉터리는 모든 사용자, 앱, 그룹 등을 위한 컨테이너입니다. 디렉터리가 없는 경우 넘어가기 전에 [B2C 디렉터리 만들기](active-directory-b2c-get-started.md)로 이동합니다.

## 2\. 응용 프로그램 만들기

이제 B2C 디렉터리에 앱을 만들어야 하며 Azure AD가 앱과 안전하게 통신해야 한다는 일부 정보를 제공합니다. 앱을 만들려면 [다음 지침](active-directory-b2c-app-registration.md)에 따릅니다. 반드시

- 응용 프로그램에서 **네이티브 클라이언트** 포함
- **리디렉션 URI** `urn:ietf:wg:oauth:2.0:oob`를 복사 - 이 코드 샘플에 대한 기본 URL입니다.
- 앱에 할당된 **응용 프로그램 ID**를 적복사합니다. 곧 필요합니다.

    > [AZURE.IMPORTANT][Azure 포털](https://manage.windowsazure.com/)의 **응용 프로그램** 탭에 등록된 응용 프로그램은 사용할 수 없습니다.

## 3\. 정책 만들기

Azure AD B2C에서 모든 사용자 환경을[**정책**](active-directory-b2c-reference-policies.md)에서 정의합니다. 이 코드 샘플은 등록, 로그인 및 편집 프로필 등 세 가지 ID 환경을 포함합니다. [정책 참조 문서](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy)에서 설명한 대로 각 형식에 하나의 정책을 만들어야 합니다. 세 가지 정책을 만들 때 다음을 확인합니다.

- ID 공급자 블레이드에서 **사용자 ID 등록** 또는 **메일 등록**을 선택합니다.
- 등록 정책에서 **표시 이름** 및 다른 몇가지 등록 특성을 선택합니다.
- 모든 정책에서 **표시 이름** 및 **개체 ID** 클레임을 응용 프로그램 클레임으로 선택합니다. 물론 다른 클레임을 선택할 수 있습니다.
- 각 정책을 만든 후에 **이름**을 복사합니다. 접두사 `b2c_1_`이 있어야 합니다. 이러한 정책 이름이 곧 필요합니다. 

세 가지 정책을 성공적으로 만들었다면 앱을 빌드할 준비가 되었습니다.

## 4\. 코드 다운로드

이 자습서에 대한 코드는 [GitHub](https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet)에서 유지 관리됩니다. 진행하면서 샘플을 빌드하려면 [골격 프로젝트를 .zip으로 다운로드](https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet/archive/skeleton.zip)하거나 골격을 복제합니다.

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet.git
```

완성된 앱도 [.zip으로 다운로드하거나](https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet/archive/complete.zip) 동일한 리포지토리의 `complete` 분기에서 사용할 수 있습니다.

샘플 코드를 다운로드하면 Visual Studio `.sln` 파일을 열어 시작합니다. 솔루션에 `TaskClient` 프로젝트와 `TaskService` 프로젝트라는 두 프로젝트가 있는 것을 확인합니다. `TaskClient`는 사용자와 상호 작용하는 WPF 데스크톱 응용 프로그램입니다. `TaskService`는 각 사용자의 할 일 모음을 저장하는 앱의 백 엔드 웹 API입니다. 이 경우 하나의 논리 응용 프로그램을 구성하기 때문에 `TaskClient`과 `TaskService` 모두는 단일 **응용 프로그램 ID**에서 표현됩니다.

## 5\. 작업 서비스 구성

`TaskService`는 `TaskClient`의 요청을 받을 경우 유효한 액세스 토큰을 검사하여 요청을 인증합니다. 액세스 토큰의 유효성을 검사하려면 앱에 대한 일부 정보를 `TaskService`에 제공해야 합니다. `TaskService` 프로젝트에서 프로젝트 루트에 있는 `web.config` 파일을 열고 `<appSettings>` 섹션의 값을 바꿉니다.

```
<appSettings>
    <add key="webpages:Version" value="3.0.0.0" />
    <add key="webpages:Enabled" value="false" />
    <add key="ClientValidationEnabled" value="true" />
    <add key="UnobtrusiveJavaScriptEnabled" value="true" />
    <add key="ida:AadInstance" value="https://login.microsoftonline.com/{0}/{1}/{2}?p={3}" />
    <add key="ida:Tenant" value="{Enter the name of your B2C tenant - it usually looks like constoso.onmicrosoft.com}" />
    <add key="ida:ClientId" value="{Enter the Application ID assigned to your app by the Azure Portal}" />
    <add key="ida:PolicyId" value="{Enter the name of one of the policies you created, like `b2c_1_my_sign_in_policy`}" />
  </appSettings>
  ```

If you want to learn how a web API securely authenticates requests using Azure AD B2C, check out our
[Web API Getting Started article](active-directory-b2c-devquickstarts-api-dotnet.md).

## 6. Execute policies
Now that the `TaskService` is ready to authenticate requests, we can implement the `TaskClient`.  Your app communicates with Azure AD B2C by sending HTTP authentication requests,
specifying the policy it wishes to execute as part of the request.  For .NET desktop applications, you can use the **Active Directory Authentication Library (ADAL)**
to send OAuth 2.0 authentication messages, execute policies, and get tokens for calling web APIs.

#### Install ADAL
Begin by adding ADAL to the TaskClient project using the Visual Studio Package Manager Console.

```
PM> Install-Package Microsoft.Experimental.IdentityModel.Clients.ActiveDirectory -ProjectName TaskClient -IncludePrerelease
```

#### B2C 세부 정보 입력
`Globals.cs` 파일을 열고 각각의 속성 값을 사용자 고유의 값으로 바꿉니다. 이 클래스는 일반적으로 사용되는 값을 참조하기 위해 `TaskClient` 전반에 사용됩니다.

```C#
public static class Globals
{
	public static string tenant = "{Enter the name of your B2C tenant - it usually looks like constoso.onmicrosoft.com}";
	public static string clientId = "{Enter the Application ID assigned to your app by the Azure Portal}";
	public static string signInPolicy = "{Enter the name of your sign in policy, e.g. b2c_1_sign_in}";
	public static string signUpPolicy = "{Enter the name of your sign up policy, e.g. b2c_1_sign_up}";
	public static string editProfilePolicy = "{Enter the name of your edit profile policy, e.g. b2c_1_edit_profile}";

	public static string taskServiceUrl = "https://localhost:44332";
	public static string aadInstance = "https://login.microsoftonline.com/";
	public static string redirectUri = "urn:ietf:wg:oauth:2.0:oob";

}
``` 


#### AuthenticationContext 만들기
ADAL의 기본 클래스는 `AuthenticationContext`입니다. - B2C 디렉터리와 앱의 연결을 나타냅니다. 앱을 시작하면 `MainWindow.xaml.cs`에서 `AuthenticationContext`의 인스턴스를 만들며 이는 창 전체에서 사용할 수 있습니다.

```C#
public partial class MainWindow : Window
{
	private HttpClient httpClient = new HttpClient();
	private AuthenticationContext authContext = null;

	protected async override void OnInitialized(EventArgs e)
	{
		base.OnInitialized(e);

		// The authority parameter can be constructed by appending the name of your tenant to 'https://login.microsoftonline.com/'.
		// ADAL implements an in-memory cache by default.  Since we want tokens to persist when the user closes the app, 
		// we've extended the ADAL TokenCache and created a simple FileCache in this app.
		authContext = new AuthenticationContext("https://login.microsoftonline.com/contoso.onmicrosoft.com", new FileCache());
		...
	...
```

#### 등록 흐름 시작
등록 단추를 클릭하면 만든 등록 정책을 사용하여 등록 흐름을 시작하려고 합니다. ADAL을 사용하여 `authContext.AcquireTokenAsync(...)`을 호출해야 합니다. `AcquireTokenAsync(...)`에 전달할 매개 변수는 받을 토큰의 종류, 인증 요청에서 사용되는 정책 등을 결정합니다.

```C#
private async void SignUp(object sender, RoutedEventArgs e)
{
	AuthenticationResult result = null;
	try
	{
		// Use the app's clientId here as the scope parameter, indicating that we want a token to the our own backend API
		// Use the PromptBehavior.Always flag to indicate to ADAL that it should show a sign-up UI no matter what.
		// Pass in the name of your sign-up policy to execute the sign-up experience.
		result = await authContext.AcquireTokenAsync(new string[] { Globals.clientId },
			null, Globals.clientId, new Uri(Globals.redirectUri),
			new PlatformParameters(PromptBehavior.Always, null), Globals.signUpPolicy);

		// Indicate in the app that the user is signed in.
		SignInButton.Visibility = Visibility.Collapsed;
		SignUpButton.Visibility = Visibility.Collapsed;
		EditProfileButton.Visibility = Visibility.Visible;
		SignOutButton.Visibility = Visibility.Visible;
		
		// When the request completes successfully, you can get user information form the AuthenticationResult
		UsernameLabel.Content = result.UserInfo.Name;

		// After the sign up successfully completes, display the user's To-Do List
		GetTodoList();
	}
	
	// Handle any exeptions that occurred during execution of the policy.
	catch (AdalException ex)
	{
		if (ex.ErrorCode == "authentication_canceled")
		{
			MessageBox.Show("Sign up was canceled by the user");
		}
		else
		{
			// An unexpected error occurred.
			string message = ex.Message;
			if (ex.InnerException != null)
			{
				message += "Inner Exception : " + ex.InnerException.Message;
			}

			MessageBox.Show(message);
		}

		return;
	}
}
```

#### 로그인 흐름 시작
로그인 흐름을 등록 흐름과 동일한 방식으로 시작할 수 있습니다. 사용자가 로그인 단추를 클릭하면 로그인 정책을 사용하여 ADAL에 동일한 호출을 합니다.

```C#
private async void SignIn(object sender = null, RoutedEventArgs args = null)
{
	AuthenticationResult result = null;
	try
	{
		result = await authContext.AcquireTokenAsync(new string[] { Globals.clientId },
                    null, Globals.clientId, new Uri(Globals.redirectUri),
                    new PlatformParameters(PromptBehavior.Always, null), Globals.signInPolicy);
		...			
```

#### 프로필 편집 흐름 시작
다시 동일한 방식으로 편집 프로필 정책 을 실행할 수 있습니다.

```C#
private async void EditProfile(object sender, RoutedEventArgs e)
{
	AuthenticationResult result = null;
	try
	{
		result = await authContext.AcquireTokenAsync(new string[] { Globals.clientId },
                    null, Globals.clientId, new Uri(Globals.redirectUri),
                    new PlatformParameters(PromptBehavior.Always, null), Globals.editProfilePolicy);
```

이 모든 경우에 ADAL은 `AuthenticationResult`에서 토큰을 반환하거나 예외를 throw 합니다. ADAL에서 토큰을 가져올 때마다 `AuthenticationResult.UserInfo` 개체를 사용하여 UI와 같은 앱의 사용자 데이터를 업데이트합니다. 또한 ADAL는 응용 프로그램의 다른 부분에 사용하기 위해 토큰을 캐시합니다.

## 7\. API 호출
ADAL을 사용하여 정책을 실행하고 토큰을 가져옵니다. 그러나 많은 경우 정책을 실행하지 않고 기존의 캐시된 토큰을 확인하려 합니다. 이러한 한 가지 경우는 앱이 `TaskService`에서 사용자의 할 일 모음을 가져오려고 할 때입니다. 이렇게 하려면 `clientId`을 다시 한 번 범위 매개 변수로 사용하여 동일한 `authContext.AcquireTokenAsync(...)` 메서드를 사용할 수 있지만 이번에는 `PromptBehavior.Never`를 사용합니다.

```C#
private async void GetTodoList()
{
	AuthenticationResult result = null;
	try
	{
		// Here we want to check for a cached token, independent of whatever policy was used to acquire it.
		TokenCacheItem tci = authContext.TokenCache.ReadItems().Where(i => i.Scope.Contains(Globals.clientId) && !string.IsNullOrEmpty(i.Policy)).FirstOrDefault();
		string existingPolicy = tci == null ? null : tci.Policy;

		// We use the PromptBehavior.Never flag to indicate that ADAL should throw an exception if a token 
		// could not be acquired from the cache, rather than automatically prompting the user to sign in. 
		result = await authContext.AcquireTokenAsync(new string[] { Globals.clientId },
			null, Globals.clientId, new Uri(Globals.redirectUri),
			new PlatformParameters(PromptBehavior.Never, null), existingPolicy);
	
	}

	// If a token could not be acquired silently, we'll catch the exception and show the user a message.
	catch (AdalException ex)
	{
		// There is no access token in the cache, so prompt the user to sign-in.
		if (ex.ErrorCode == "user_interaction_required")
		{
			MessageBox.Show("Please sign up or sign in first");
			SignInButton.Visibility = Visibility.Visible;
			SignUpButton.Visibility = Visibility.Visible;
			EditProfileButton.Visibility = Visibility.Collapsed;
			SignOutButton.Visibility = Visibility.Collapsed;
			UsernameLabel.Content = string.Empty;

		}
		else
		{
			// An unexpected error occurred.
			string message = ex.Message;
			if (ex.InnerException != null)
			{
				message += "Inner Exception : " + ex.InnerException.Message;
			}
			MessageBox.Show(message);
		}

		return;
	}
	...
```

`AcquireTokenAsync(...)`에 대한 호출이 성공하고 캐시에 토큰이 발견되면 HTTP 요청의 `Authorization` 헤더에 토큰을 추가할 수 있습니다. 따라서 `TaskService`은 요청을 인증하여 사용자의 할 일 모음을 읽을 수 있습니다.

```C#
	...
	// Once the token has been returned by ADAL, add it to the http authorization header, before making the call to the TaskService.
	httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.Token);

	// Call the To Do list service.
	HttpResponseMessage response = await httpClient.GetAsync(taskServiceUrl + "/api/tasks");
	...
``` 

토큰에 대한 토큰 캐시를 확인하려면 사용자에게 로그인하라는 메시지를 표시하지 않고 언제든지 동일한 패턴을 사용합니다. 예를 들어 앱을 시작하면 기존 토큰에 대해 `FileCache`를 확인하려 하므로 사용자의 로그인 세션이 앱이 실행될 때마다 유지됩니다. `MainWindow`의 `OnInitialized` 이벤트에서 동일한 코드를 표시하며 여기서 첫 실행 사례를 처리합니다.

## 8\. 사용자를 로그아웃
마지막으로 "로그아웃" 단추를 클릭할 때 ADAL을 사용하여 사용자의 앱 세션을 종료합니다. ADAL을 사용하여 토큰 캐시에서 토큰을 비우는 것만큼 간단합니다.

```C#
private void SignOut(object sender, RoutedEventArgs e)
{
	// Clear any remnants of the user's session.
	authContext.TokenCache.Clear();

	// This is a helper method that clears browser cookies in the browser control that ADAL uses, it is not part of ADAL.
	ClearCookies();

	// Update the UI to show the user as signed out.
	TaskList.ItemsSource = string.Empty;
	SignInButton.Visibility = Visibility.Visible;
	SignUpButton.Visibility = Visibility.Visible;
	EditProfileButton.Visibility = Visibility.Collapsed;
	SignOutButton.Visibility = Visibility.Collapsed;
	return;
}
```

## 9\. 샘플 앱 실행

마지막으로, `TaskClient` 및 `TaskService` 둘 다를 빌드 및 실행합니다. 전자 메일 주소 또는 사용자 이름을 사용하여 앱에 등록합니다. 로그아웃했다가 동알한 사용자로 다시 로그인합니다. 해당 사용자의 프로필을 편집합니다. 로그아웃했다가 다른 사용자로 등록합니다.

## 10\. 소셜 IDP 추가

현재 앱은 **로컬 계정**, 즉 사용자 이름 및 암호와 함께 B2C 디렉터리에 저장된 계정을 사용한 사용자 등록 및 로그인만 지원합니다. Azure AD B2C를 사용하면 코드를 변경하지 않고도 다른 **ID 공급자**, 즉 IDP에 대한 지원을 추가할 수 있습니다.

소셜 IDP를 앱에 추가하려면 이 문서 중에서 한두 개의 상세한 지침을 수행하여 시작합니다. 지원하려는 각 IDP의 경우 해당 시스템에서 응용 프로그램을 등록하고 클라이언트 ID를 얻어야 합니다.

- [Facebook을 IDP로 설정](active-directory-b2c-setup-fb-app.md)
- [Google을 IDP로 설정](active-directory-b2c-setup-goog-app.md)
- [Amazon을 IDP로 설정](active-directory-b2c-setup-amzn-app.md)
- [LinkedIn을 IDP로 설정](active-directory-b2c-setup-li-app.md) 

B2C 디렉터리에 ID 공급자를 추가한 경우 다시 돌아가 [정책 참조 문서](active-directory-b2c-reference-policies.md)에서 설명한 대로 새 IDP를 포함하도록 세 가지 정책을 각각 편집해야 합니다. 정책을 저장한 후에 앱을 다시 실행합니다. ID 환경 각각에서 로그인 및 등록으로 추가된 새 IDP가 표시되어야 합니다.

정책을 자유롭게 실험하고 샘플 앱에서 영향을 확인할 수 있습니다. IDP를 추가/제거하고 응용 프로그램 클레임을 조작하며 특성 등록을 변경합니다. 어떻게 정책, 인증 요청 및 ADAL을 모두 함께 연결하는지 이해할 수 있을 때까지 실험해 보세요.

참조를 위해 완성된 샘플이 [여기서 .zip으로 제공](https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet/archive/complete.zip)되거나 GitHub에서 복제할 수 있습니다.

```git clone --branch complete https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet.git```

<!--

## Next Steps

You can now move onto more advanced B2C topics.  You may want to try:

[Calling a Web API from a Web App >>]()

[Customizing the your B2C App's UX >>]()

-->

<!---HONumber=Sept15_HO4-->