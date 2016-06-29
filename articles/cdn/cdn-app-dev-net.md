<properties
	pageTitle=".NET용 Azure CDN 라이브러리 시작하기 | Microsoft Azure"
	description="Visual Studio로 Azure CDN을 관리하는 .NET 응용 프로그램을 만드는 방법에 대해 알아보기"
	services="cdn"
	documentationCenter=".net"
	authors="camsoper"
	manager="erikre"
	editor=""/>

<tags
	ms.service="cdn"
	ms.workload="tbd"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/14/2016"
	ms.author="casoper"/>

# .NET용 Azure CDN 라이브러리 시작하기

[.NET용 Azure CDN 라이브러리](https://msdn.microsoft.com/library/mt657769.aspx)를 사용하여 CDN 프로필과 끝점의 생성 및 관리를 자동화할 수 있습니다. 이 자습서에서는 여러 가지 사용 가능한 작업을 보여주는 간단한 .NET 콘솔 응용 프로그램을 살펴볼 것입니다. 이 자습서는 .NET용 Azure CDN 라이브러리의 모든 측면을 상세하게 설명하지 않습니다.

이 자습서를 완료하려면 Visual Studio 2015가 필요합니다. [Visual Studio Community 2015](https://www.visualstudio.com/products/visual-studio-community-vs.aspx)는 무료로 다운로드할 수 있습니다.

이 자습서의 완성된 예제는 [여기](https://code.msdn.microsoft.com/Azure-CDN-Management-1f2fba2c)에 있습니다.

## 준비

CDN 관리 코드를 작성하기 전에 준비 작업이 필요합니다. 먼저, 자습서에서 만들 CDN 프로필을 담기 위한 리소스 그룹을 만들어야 합니다. 그런 다음 Azure Active Directory를 설정하고 응용 프로그램에 대한 인증을 제공합니다. 이 작업이 완료되면 리소스 그룹에 권한을 적용하고, Azure AD 테넌트의 권한이 있는 사용자만 CDN 프로필과 상호 작용하게 합니다.

### 리소스 그룹 만들기

1. [Azure 포털](https://portal.azure.com)에 로그인합니다.

2. 왼쪽 위에 있는 **새로 만들기** 단추를 클릭하고 **관리**, **리소스 그룹**을 클릭합니다.
	
	![새 리소스 그룹 만들기](./media/cdn-app-dev-net/cdn-new-rg-1.png)

3. *CdnConsoleTutorial* 리소스 그룹을 호출합니다. 구독을 선택하고 가까운 위치를 선택합니다. 원한다면 **대시보드에 고정** 확인란을 클릭하여 리소스 그룹을 포털 대시보드에 고정할 수 있습니다. 이렇게 하면 나중에 쉽게 찾을 수 있습니다. 항목을 선택한 후에 **만들기**를 클릭합니다.

	![리소스 그룹에 이름 지정하기](./media/cdn-app-dev-net/cdn-new-rg-2.png)

4. 리소스 그룹을 만들고 대시보드에 고정하지 않았다면 **찾아보기**, **리소스 그룹**을 클릭하여 찾을 수 있습니다. 리소스 그룹을 클릭하여 엽니다. **구독 ID**를 메모합니다. 나중에 필요합니다.

	 ![리소스 그룹에 이름 지정하기](./media/cdn-app-dev-net/cdn-subscription-id.png)

### Azure AD 응용 프로그램 만들기

Azure Active Directory로 앱을 인증하는 방법은 두 가지가 있습니다. 개별 사용자 또는 서비스 주체 인증입니다. 서비스 주체는 Windows의 서비스 계정과 비슷합니다. 특정 사용자에게 CDN 프로필과 상호 작용하는 권한을 부여하는 대신 서비스 주체에 권한을 부여합니다. 일반적으로, 서비스 주체는 자동화된 비대화형 프로세스에 사용됩니다. 이 자습서에서는 대화형 콘솔 앱을 작성하지만, 서비스 주체 인증 방식에 초점을 맞출 것입니다.

Azure Active Directory 응용 프로그램 만들기를 비롯하여 여러 반계로 구성된 서비스 주체를 만듭니다. 이 작업을 하려면 [이 자습서를 따라야 합니다](../resource-group-create-service-principal-portal.md).

> [AZURE.IMPORTANT] [링크된 자습서](../resource-group-create-service-principal-portal.md)의 모든 단계를 따르세요. 설명한 대로 완료하는 것이 *절대적으로 중요합니다*. **테넌트 ID**, **테넌트 도메인 이름**(사용자 지정 도메인을 지정하지 않을 경우, 일반적으로는 *.onmicrosoft.com* 도메인), **클라이언트 ID**, **클라이언트 인증 키**를 메모합니다. 나중에 필요합니다. **클라이언트 ID**와 **클라이언트 인증 키**를 매우 신중하게 보호해야 합니다. 이 자격 증명을 사용하는 사람은 누구나 서비스 주체로 작업을 실행할 수 있습니다.
> 	
> [다중 테넌트 응용 프로그램 구성](../resource-group-create-service-principal-portal.md#configure-multi-tenant-application) 단계에 도달하면 **아니요**를 선택합니다.
> 
> [역할에 응용 프로그램 할당](../resource-group-create-service-principal-portal.md#assign-application-to-role) 단계에 도달하면 **독자** 역할 대신 앞서 만든 리소스 그룹 *CdnConsoleTutorial*을 사용하여 **CDN 프로필 참가자** 역할을 할당합니다. 리소스 그룹에서 응용 프로그램에 **CDN 프로필 참가자** 역할을 할당한 후에 이 자습서로 돌아옵니다.

서비스 주체를 만들고 **CDN 프로필 참가자** 역할을 할당했으면 리소스 그룹의 **사용자** 블레이드는 다음과 같아야 합니다.

![사용자 블레이드](./media/cdn-app-dev-net/cdn-service-principal.png)


### 대화형 사용자 인증

서비스 주체 대신 대화형 개별 사용자 인증을 사용할 경우, 그 과정은 서비스 주체 인증과 매우 유사합니다. 사실, 똑같은 절차를 따르면서 사소한 몇 가지만 변경하면 됩니다.

>[AZURE.IMPORTANT] 서비스 주체 대신 개별 사용자 인증을 사용할 경우에만 다음 단계를 수행하세요.

1. 응용 프로그램을 만들 때 **웹앱** 대신 **네이티브 응용 프로그램**을 선택합니다. 
	
	![네이티브 응용 프로그램](./media/cdn-app-dev-net/cdn-native-application.png)
	
2. 다음 페이지에서 **URI 리디렉션** 메시지가 나타날 것입니다. 이 URI은 유효성을 검사하지 않지만, 입력한 내용을 기억해야 합니다. 나중에 필요합니다.

3. **클라이언트 인증 키**를 만들 필요가 없습니다.

4. 서비스 주체를 **CDN 프로필 참가자** 역할에 할당하는 대신 개별 사용자 또는 그룹을 할당합니다. 이 예제에서는 *CDN 데모 사용자*를 **CDN 프로필 참가자** 역할에 할당한 것을 알 수 있습니다.
	
	![개별 사용자 액세스](./media/cdn-app-dev-net/cdn-aad-user.png)


## 프로젝트 만들기 및 Nuget 패키지 추가하기

CDN 프로필용 리소스 그룹을 만들고 해당 그룹에서 CDN 프로필과 끝점을 관리하기 위한 Azure AD 응용 프로그램 권한을 부여했으므로, 응용 프로그램을 만들 수 있습니다.

Visual Studio 2015를 열고 **파일**, **새로 만들기**, **프로젝트...**를 클릭하여 새 프로젝트 대화 상자를 엽니다. **Visual C#**를 펼치고 왼쪽 창에서 **Windows**를 선택합니다. 중앙 창에서 **콘솔 응용 프로그램**을 클릭합니다. 프로젝트 이름을 지정하고 **확인**을 클릭합니다.

![새 프로젝트](./media/cdn-app-dev-net/cdn-new-project.png)

이 프로젝트에서는 Nuget 패키지에 포함된 일부 Azure 라이브러리를 사용할 것입니다. 라이브러리를 프로젝트에 추가하겠습니다.

1. **도구** 메뉴에서 **NuGet 패키지 관리자**를 클릭한 다음 **패키지 관리자 콘솔**을 클릭합니다.

	![Nuget 패키지 관리](./media/cdn-app-dev-net/cdn-manage-nuget.png)

2. 패키지 관리자 콘솔에서 다음 명령을 실행하여 **Active Directory 인증 라이브러리(ADAL)**를 설치합니다.

	`Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory`

3. 다음 명령을 실행하여 **Azure CDN 관리 라이브러리**를 설치합니다.

	`Install-Package Microsoft.Azure.Management.Cdn`

## 지시문, 상수, 메인 메서드 및 도우미 메서드

작성된 프로그램의 기본 구조를 살펴보겠습니다.

1. Program.cs 탭으로 돌아와서 위에 있는 `using` 지시문을 다음으로 교체합니다.

	```
	using System;
	using System.Collections.Generic;
	using Microsoft.Azure.Management.Cdn;
	using Microsoft.Azure.Management.Cdn.Models;
	using Microsoft.Azure.Management.Resources;
	using Microsoft.Azure.Management.Resources.Models;
	using Microsoft.IdentityModel.Clients.ActiveDirectory;
	using Microsoft.Rest;
	```

2. 메서드가 사용할 몇 가지 상수를 정의해야 합니다. `Main` 메서드 전에 `Program` 클래스에 다음 내용을 추가합니다. **&lt;꺽쇠 괄호&gt;**를 포함한 자리 표시자를 필요에 따라 고유 값으로 교체합니다.

	```
	//Tenant app constants
	private const string clientID = "<YOUR CLIENT ID>";
	private const string clientSecret = "<YOUR CLIENT AUTHENTICATION KEY>"; //Only for service principals
	private const string authority = "https://login.microsoftonline.com/<YOUR TENANT ID>/<YOUR TENANT DOMAIN NAME>";

	//Application constants
	private const string subscriptionId = "<YOUR SUBSCRIPTION ID>";
	private const string profileName = "CdnConsoleApp";
	private const string endpointName = "<A UNIQUE NAME FOR YOUR CDN ENDPOINT>";
	private const string resourceGroupName = "CdnConsoleTutorial";
	private const string resourceLocation = "<YOUR PREFERRED AZURE LOCATION, SUCH AS Central US>";
	```

3. 또한, 클래스 수준에서 다음 두 가지 변수를 정의합니다. 이 변수는 프로필과 끝점이 이미 존재하는지 확인할 때 사용할 것입니다.

	```
	static bool profileAlreadyExists = false;
    static bool endpointAlreadyExists = false;
	```

4.  `Main` 메서드를 다음과 같이 교체합니다.

	```
	static void Main(string[] args)
	{
		//Get a token
		AuthenticationResult authResult = GetAccessToken();

		// Create CDN client
		CdnManagementClient cdn = new CdnManagementClient(new TokenCredentials(authResult.AccessToken))
			{ SubscriptionId = subscriptionId };

		ListProfilesAndEndpoints(cdn);

		// Create CDN Profile
		CreateCdnProfile(cdn);

		// Create CDN Endpoint
		CreateCdnEndpoint(cdn);
		
		Console.WriteLine();

		// Purge CDN Endpoint
		PromptPurgeCdnEndpoint(cdn);

		// Delete CDN Endpoint
		PromptDeleteCdnEndpoint(cdn);

		// Delete CDN Profile
		PromptDeleteCdnProfile(cdn);

		Console.WriteLine("Press Enter to end program.");
		Console.ReadLine();
	}
	```

5. 일부 다른 메서드는 사용자에게 “Yes/No” 질문을 묻는 메시지를 표시합니다. 그 작업을 쉽게 수행할 수 있게 도와줄 다음 메서드를 추가합니다.

	```
	private static bool PromptUser(string Question)
	{
		Console.Write(Question + " (Y/N): ");
		var response = Console.ReadKey();
		Console.WriteLine();
		if (response.Key == ConsoleKey.Y)
		{
			return true;
		}
		else if (response.Key == ConsoleKey.N)
		{
			return false;
		}
		else
		{
			// They're not pressing Y or N.  Let's ask them again.
			return PromptUser(Question);
		}
	}
	```

이제 프로그램의 기본 구조가 작성되었으므로 `Main` 메서드가 호출할 메서드를 만들어야 합니다.

## 인증

Azure CDN 관리 라이브러리를 사용하기 전에 서비스 주체를 인증하고 인증 토큰을 가져와야 합니다. 이 메서드는 ADAL을 사용하여 토큰을 검색합니다.

```
private static AuthenticationResult GetAccessToken()
{
	AuthenticationContext authContext = new AuthenticationContext(authority); 
	ClientCredential credential = new ClientCredential(clientID, clientSecret);
	AuthenticationResult authResult = 
		authContext.AcquireTokenAsync("https://management.core.windows.net/", credential).Result;

	return authResult;
}
```

개별 사용자 인증을 사용한다면 `GetAccessToken` 메서드는 약간 다르게 표시됩니다.

>[AZURE.IMPORTANT] 서비스 주체가 아닌 개별 사용자 인증을 사용할 경우에만 다음 코드 샘플을 사용하세요.

```
private static AuthenticationResult GetAccessToken()
{
	AuthenticationContext authContext = new AuthenticationContext(authority);
	AuthenticationResult authResult = authContext.AcquireTokenAsync("https://management.core.windows.net/",
		clientID, new Uri("http://<redirect URI>"), new PlatformParameters(PromptBehavior.RefreshSession)).Result;

	return authResult;
}
```

`<redirect URI>`을(를) Azure AD에서 응용 프로그램을 등록할 때 입력한 URI 리디렉션으로 교체합니다.

## CDN 프로필 및 끝점 목록화하기

이제 CDN 작업을 수행할 준비가 되었습니다. 이 메서드는 가장 먼저 리소스 그룹에서 모든 프로필과 끝점의 목록을 작성합니다. 상수에서 정의된 프로필과 끝점 이름에 일치하는 항목이 발견되면, 나중에 중복을 만들지 않도록 메모해 둡니다.

```
private static void ListProfilesAndEndpoints(CdnManagementClient cdn)
{
	// List all the CDN profiles in this resource group
	var profileList = cdn.Profiles.ListByResourceGroup(resourceGroupName);
	foreach (Profile p in profileList)
	{
		Console.WriteLine("CDN profile {0}", p.Name);
		if (p.Name.Equals(profileName, StringComparison.OrdinalIgnoreCase))
		{
			// Hey, that's the name of the CDN profile we want to create!
			profileAlreadyExists = true;
		}

		//List all the CDN endpoints on this CDN profile
		Console.WriteLine("Endpoints:");
		var endpointList = cdn.Endpoints.ListByProfile(p.Name, resourceGroupName);
		foreach (Endpoint e in endpointList)
		{
			Console.WriteLine("-{0} ({1})", e.Name, e.HostName);
			if (e.Name.Equals(endpointName, StringComparison.OrdinalIgnoreCase))
			{
				// The unique endpoint name already exists.
				endpointAlreadyExists = true;
			}
		}
		Console.WriteLine();
	}
}
```

## CDN 프로필 및 끝점 만들기

다음으로 프로필을 만들어 보겠습니다.

```
private static void CreateCdnProfile(CdnManagementClient cdn)
{
	if (profileAlreadyExists)
	{
		Console.WriteLine("Profile {0} already exists.", profileName);
	}
	else
	{
		Console.WriteLine("Creating profile {0}.", profileName);
		ProfileCreateParameters profileParms =
			new ProfileCreateParameters() { Location = resourceLocation, Sku = new Sku(SkuName.StandardVerizon) };
		cdn.Profiles.Create(profileName, profileParms, resourceGroupName);
	}
}
```

프로필을 만든 후에는 끝점을 만듭니다.

```
private static void CreateCdnEndpoint(CdnManagementClient cdn)
{
	if (endpointAlreadyExists)
	{
		Console.WriteLine("Profile {0} already exists.", profileName);
	}
	else
	{
		Console.WriteLine("Creating endpoint {0} on profile {1}.", endpointName, profileName);
		EndpointCreateParameters endpointParms =
			new EndpointCreateParameters()
			{
				Origins = new List<DeepCreatedOrigin>() { new DeepCreatedOrigin("Contoso", "www.contoso.com") },
				IsHttpAllowed = true,
				IsHttpsAllowed = true,
				Location = resourceLocation
			};
		cdn.Endpoints.Create(endpointName, endpointParms, profileName, resourceGroupName);
	}
}
```

>[AZURE.NOTE] 위 예제에서는 끝점에 *Contoso*라는 원점을 할당했습니다. 호스트 이름은 `www.contoso.com`입니다. 이 값이 원래의 호스트 이름을 가리키도록 변경해야 합니다.

## 끝점 삭제

끝점을 만들었을 경우, 프로그램에서 흔히 수행하는 작업은 끝점의 콘텐츠를 삭제하는 것입니다.

```
private static void PromptPurgeCdnEndpoint(CdnManagementClient cdn)
{
	if (PromptUser(String.Format("Purge CDN endpoint {0}?", endpointName)))
	{
		Console.WriteLine("Purging endpoint. Please wait...");
		cdn.Endpoints.PurgeContent(endpointName, profileName, resourceGroupName, new List<string>() { "/*" });
		Console.WriteLine("Done.");
		Console.WriteLine();
	}
}
```

>[AZURE.NOTE] 위 예제에서 `/*` 문자열은 끝점 경로의 루트에 있는 모든 것을 삭제하겠다는 의미를 나타냅니다. 이는 Azure 포털의 “삭제" 대화 상자에서 **모두 삭제**에 표시하는 것과 같습니다. `CreateCdnProfile` 메서드에서는 `Sku = new Sku(SkuName.StandardVerizon)` 코드를 사용하여 **Verizon에서 Azure CDN** 프로필을 만들었으므로, 이 작업이 성공적으로 수행될 것입니다. 그러나 **Akamai에서 Azure CDN** 프로필은 **모두 삭제**를 지원하지 않습니다. 이 자습서에서 Akamai 프로필을 사용했다면 삭제할 구체적 경로가 필요했을 것입니다.

## CDN 프로필 및 끝점 삭제

마지막으로 포함할 메서드는 끝점 및 프로필 삭제입니다.

```
private static void PromptDeleteCdnEndpoint(CdnManagementClient cdn)
{
	if(PromptUser(String.Format("Delete CDN endpoint {0} on profile {1}?", endpointName, profileName)))
	{
		Console.WriteLine("Deleting endpoint. Please wait...");
		cdn.Endpoints.DeleteIfExists(endpointName, profileName, resourceGroupName);
		Console.WriteLine("Done.");
		Console.WriteLine();
	}
}

private static void PromptDeleteCdnProfile(CdnManagementClient cdn)
{
	if(PromptUser(String.Format("Delete CDN profile {0}?", profileName)))
	{
		Console.WriteLine("Deleting profile. Please wait...");
		cdn.Profiles.DeleteIfExists(profileName, resourceGroupName);
		Console.WriteLine("Done.");
		Console.WriteLine();
	}
}
```

## 프로그램 실행

이제 Visual Studio에서 **시작** 단추를 클릭하면 프로그램을 컴파일하고 실행할 수 있습니다.

![프로그램 실행](./media/cdn-app-dev-net/cdn-program-running-1.png)

프로그램이 위 프롬프트까지 도달하면 Azure 포털의 리소스 그룹에 돌아가서 프로필이 만들어진 것을 확인할 수 있어야 합니다.

![성공!](./media/cdn-app-dev-net/cdn-success.png)

나머지 프로그램을 실행시키는 프롬프트를 확인합니다.

![프로그램 완료](./media/cdn-app-dev-net/cdn-program-running-2.png)

## 다음 단계

이 연습에서 작성된 프로젝트를 보려면 [샘플을 다운로드하세요](https://code.msdn.microsoft.com/Azure-CDN-Management-1f2fba2c).

.NET용 Azure CDN 관리 라이브러리에 관한 추가 설명서는 [MSDN 참조](https://msdn.microsoft.com/library/mt657769.aspx)를 확인하세요.

<!---HONumber=AcomDC_0615_2016-->