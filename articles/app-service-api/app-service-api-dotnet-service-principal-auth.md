<properties
	pageTitle="Azure 앱 서비스의 API 앱에 대한 서비스 주체 인증 | Microsoft Azure"
	description="서비스 간 시나리오에 대해 Azure 앱 서비스에서 API 앱을 보호하는 방법에 알아봅니다."
	services="app-service\api"
	documentationCenter=".net"
	authors="tdykstra"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="app-service-api"
	ms.workload="na"
	ms.tgt_pltfrm="dotnet"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/04/2016" 
	ms.author="tdykstra"/>

# Azure 앱 서비스의 API 앱에 대한 서비스 주체 인증

[AZURE.INCLUDE [선택기](../../includes/app-service-api-auth-selector.md)]

## 개요

이 문서에서는 API 앱에 대한 [내부](app-service-api-authentication.md#internal) 액세스를 위해 앱 서비스 인증을 사용하는 방법을 설명합니다. 내부 시나리오에는 사용자 고유의 응용 프로그램 코드에 의해서만 사용되도록 한 API 앱이 있습니다. 앱 서비스에서 이 시나리오를 구현하는 가장 쉬운 방법은 호출된 API 앱을 보호하기 위해 Azure AD를 사용하는 것입니다. 응용 프로그램 ID(서비스 주체) 자격 증명을 제공하여 Azure AD에서 가져온 전달자 토큰으로 보호된 API 앱을 호출합니다.

이 문서에서는 다음에 대해 알아봅니다.

* Azure AD(Azure Active Directory)를 사용하여 인증되지 않은 액세스로부터 API 앱을 보호하는 방법
* Azure AD 서비스 주체(앱 ID) 자격 증명을 사용하여 API 앱, 웹앱 또는 모바일 앱의 보호된 API 앱을 사용하는 방법 논리 앱에서 사용하는 방법에 대한 자세한 내용은 [논리 앱으로 앱 서비스에서 호스팅되는 사용자 지정 API 사용](../app-service-logic/app-service-logic-custom-hosted-api.md)을 참조하세요.
* 로그온된 사용자가 보호된 API 앱을 브라우저에서 호출할 수 없도록 하는 방법입니다.
* 특정 Azure AD 서비스 주체만이 보호된 API 앱을 호출할 수 있도록 하는 방법입니다.

이 문서에는 두 섹션이 포함되어 있습니다.

* [Azure 앱 서비스에서 서비스 주체 인증을 구성하는 방법](#authconfig) 섹션에서는 API 앱에 인증을 구성하는 방법 및 보호된 API 앱을 사용하는 방법을 일반적으로 설명합니다. 이 섹션은 .NET, Node.js 및 Java를 포함하여 앱 서비스에서 지원되는 모든 프레임워크에 동일하게 적용됩니다.

* [.NET 시작 자습서 계속](#tutorialstart)으로 시작하는 자습서는 앱 서비스에서 실행되는 .NET 샘플 응용 프로그램에 대해 "내부 액세스" 시나리오를 구성하는 과정을 안내합니다.

## <a id="authconfig"></a> Azure 앱 서비스에서 서비스 주체를 인증하는 방법

이 섹션에서는 모든 API 앱에 적용되는 일반적인 지침을 제공합니다. 수행 목록 .NET 샘플 응용 프로그램에 특정한 단계는 [.NET 시작 자습서 계속](#tutorialstart)으로 이동합니다.

1. [Azure 포털](https://portal.azure.com/)에서 보호할 API 앱의 **설정** 블레이드로 이동한 후 **기능** 섹션을 찾고 **인증/권한 부여**를 클릭합니다.

	![Azure 포털에서 인증/권한 부여](./media/app-service-api-dotnet-user-principal-auth/features.png)

3. **인증/권한 부여** 블레이드에서 **설정**을 클릭합니다.

4. **요청이 인증되지 않은 경우에 수행할 동작** 드롭다운 목록에서 **Azure Active Directory를 사용하여 로그인**을 선택합니다.

5. **인증 공급자**에서 **Azure Active Directory**를 선택합니다.

	![Azure 포털에서 인증/권한 부여 블레이드](./media/app-service-api-dotnet-user-principal-auth/authblade.png)

6. **Azure Active Directory 설정** 블레이드를 구성하여 새 Azure AD 응용 프로그램을 만들거나 또는 이미 기존 Azure AD 응용 프로그램이 있는 경우 해당 응용 프로그램을 사용합니다.

	내부 시나리오는 일반적으로 API 앱을 호출하는 API 앱을 포함합니다. 각 API 앱 또는 하나의 Azure AD 응용 프로그램에 분리된 AD 응용 프로그램을 사용할 수 있습니다.

	이 블레이드에서 자세한 지침은 [Azure Active Directory 로그인을 사용하도록 앱 서비스 응용 프로그램을 구성하는 방법](../app-service-mobile/app-service-mobile-how-to-configure-active-directory-authentication.md)을 참조하세요.

7. 인증 공급자 구성 블레이드를 마치면 **확인**을 클릭합니다.

7. **인증/권한 부여** 블레이드에서 **저장**을 클릭합니다.

	![저장을 클릭합니다.](./media/app-service-api-dotnet-service-principal-auth/authsave.png)

이 작업이 완료되면 앱 서비스는 구성된 Azure AD 테넌트에 있는 호출자의 요청만 허용합니다. 보호된 API 앱에 인증 또는 권한 부여 코드가 필요합니다. 전달자 토큰은 HTTP 헤더에서 흔히 사용되는 클레임과 함께 API 앱에 전달되면, 서비스 주체와 같은 특정 호출자의 요청을 확인하기 위해 코드의 해당 정보를 읽을 수 있습니다.

인증 기능은 .NET, Node.js, Java 등 앱 서비스가 지원하는 모든 언어에 동일한 방법으로 작동합니다.

#### 보호된 API 앱을 사용하는 방법

호출자는 API 호출로 Azure AD 전달자 토큰을 제공해야 합니다. 서비스 주체 자격 증명을 사용하여 전달자 토큰을 가져오려면 호출자는 Active Directory 인증 라이브러리([.NET](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory), [Node.js](https://github.com/AzureAD/azure-activedirectory-library-for-nodejs) 또는 [Java](https://github.com/AzureAD/azure-activedirectory-library-for-java)용 ADAL)를 사용합니다. 토큰을 가져오려면 ADAL을 호출하는 코드는 ADAL에 다음 정보를 제공합니다.

* Azure AD 테넌트의 이름입니다.
* Azure AD 앱의 클라이언트 ID 및 클라이언트 암호(앱 키)는 호출자와 연관되었습니다.
* Azure AD 응용 프로그램의 클라이언트 ID는 보호된 API 앱과 연결되었습니다. (Azure AD 응용 프로그램을 하나만 사용한 경우 호출자에 대한 클라이언트 ID와 동일합니다.)

이러한 값은 [Azure 클래식 포털](https://manage.windowsazure.com/)의 Azure AD 페이지에서 사용할 수 있습니다.

토큰을 획득하면 호출자는 토큰을 권한 부여 헤더에 HTTP 요청으로 포함합니다. 앱 서비스는 토큰의 유효성을 검사하고 요청이 보호된 API 앱에 도달하도록 허용합니다.

#### 동일한 테넌트의 사용자가 API 앱을 액세스로부터 보호하는 방법

동일한 테넌트의 사용자에 대한 전달자 토큰은 보호된 API 앱에 대해 유효하다고 간주됩니다. 서비스 주체만이 보호된 API 앱을 호출할 수 있도록 하려는 경우 보호된 API 앱에 코드를 추가하여 토큰으로부터 다음과 같은 클레임의 유효성을 검사합니다.

* `appid`는 호출자와 관련된 Azure AD 응용 프로그램의 클라이언트 ID여야 합니다. 
* `oid`(`objectidentifier`)는 호출자의 서비스 주체 ID여야 합니다. 

앱 서비스는 X-MS-CLIENT-PRINCIPAL-ID 헤더에 있는 `objectidentifier` 클레임을 제공합니다.

### 브라우저 액세스로부터 API 앱을 보호하는 방법

보호된 API 앱의 코드에서 클레임의 유효성을 검사하지 않은 경우 및 보호된 API 앱에 별도의 Azure AD 응용 프로그램을 사용하는 경우 Azure AD 응용 프로그램의 회신 URL이 API 앱의 기본 URL과 동일하지 않도록 해야 합니다. 회신 URL이 직접 보호된 API 앱을 가리키는 경우 동일한 Azure AD 테넌트의 사용자는 API 앱을 찾고 로그온한 다음 성공적으로 API를 호출할 수 있습니다.

## <a id="tutorialstart"></a> .NET 시작 자습서 계속

API 앱에 Node.js 또는 Java 시작 시리즈를 수행 중인 경우 [다음 단계](#next-steps) 섹션으로 건너뜁니다.

이 문서의 나머지 부분에서는 API 앱에 .NET 시작 시리즈를 계속하며 [사용자 인증 자습서](app-service-api-dotnet-user-principal-auth.md)를 완료하고 사용자 인증을 사용하여 Azure에서 실행하는 샘플 응용 프로그램이 있다고 가정합니다.

## Azure에서 인증 설정

이 섹션에서는 앱 서비스를 구성하므로 데이터 계층 API 앱이 도달하도록 허용하는 HTTP 요청만이 유효한 Azure AD 전달자 토큰을 가집니다.

다음 섹션에서는 데이터 계층 API 앱을 구성하여 Azure AD에 응용 프로그램 자격 증명을 보내고 전달자 토큰을 다시 가져오며 해당 전달자 토큰을 중간 계층 API 앱에 전달합니다. 이 프로세스는 다이어그램에 나와 있습니다.

![서비스 인증 다이어그램](./media/app-service-api-dotnet-service-principal-auth/appdiagram.png)

자습서의 지침을 수행하는 동안 문제가 발생하는 경우 자습서 끝부분의 [문제 해결](#troubleshooting) 섹션을 참조하세요.

1. [Azure 포털](https://portal.azure.com/)에서 ToDoListDataAPI(데이터 계층) API 앱에 만든 API 앱의 **설정** 블레이드로 이동한 다음 **설정**을 클릭합니다.

2. **설정** 블레이드의 **기능** 섹션을 찾고 **인증/권한 부여**를 클릭합니다.

	![Azure 포털에서 인증/권한 부여](./media/app-service-api-dotnet-user-principal-auth/features.png)

3. **인증/권한 부여** 블레이드에서 **설정**을 클릭합니다.

4. **요청이 인증되지 않은 경우에 수행할 동작** 드롭다운 목록에서 **Azure Active Directory를 사용하여 로그인**을 선택합니다.

	앱 서비스를 발생시켜서 인증된 요청이 API 앱에 도달하도록 하는 설정입니다. 유효한 전달자 토큰을 가진 요청의 경우 앱 서비스는 API 앱에 따라 토큰을 전달하고 해당 정보를 코드에 보다 쉽게 사용할 수 있도록 하는 자주 사용된 클레임으로 HTTP 헤더를 채웁니다.

5. **인증 공급자** 아래에서 **Azure Active Directory**를 클릭합니다.

	![Azure 포털에서 인증/권한 부여 블레이드](./media/app-service-api-dotnet-user-principal-auth/authblade.png)

6. **Azure Active Directory 설정** 블레이드에서 **Express**를 클릭합니다.

	**Express** 옵션을 사용하면 Azure는 Azure AD [테넌트](https://msdn.microsoft.com/ko-KR/library/azure/jj573650.aspx#BKMK_WhatIsAnAzureADTenant)에서 자동으로 AAD 응용 프로그램을 만들 수 있습니다.

	자동으로 모든 Azure 계정에 하나씩 있기 때문에 테넌트를 만들 필요가 없습니다.

7. **관리 모드**에서 아직 선택하지 않은 경우 **새 AD 앱 만들기**를 클릭합니다.

	포털이 **앱 만들기** 입력 상자에 기본값을 채웁니다. 기본적으로 Azure AD 응용 프로그램의 이름은 API 앱과 동일합니다. 원하는 경우 다른 이름을 입력합니다.
	
	![Azure AD 설정](./media/app-service-api-dotnet-service-principal-auth/aadsettings.png)

	**참고**: 대신 API 앱 및 보호된 API 앱을 호출하기 위해 단일 Azure AD 응용 프로그램을 사용할 수 있습니다. 해당 대안을 선택한 경우 앞의 사용자 인증 자습서에서 Azure AD 응용 프로그램을 이미 만들었기 때문에 **새 AD 앱 만들기** 옵션은 필요하지 않습니다. 이 자습서에서는 API 앱 및 보호된 API 앱을 호출하기 위해 별도의 Azure AD 응용 프로그램을 사용합니다.

8. **앱 만들기** 입력 상자에 있는 값을 기록해 둡니다. 나중에 Azure 클래식 포털에서 이 AAD 응용 프로그램을 찾아 보겠습니다.

7. **확인**을 클릭합니다.

10. **인증/권한 부여** 블레이드에서 **저장**을 클릭합니다.

	![저장을 클릭합니다.](./media/app-service-api-dotnet-service-principal-auth/saveauth.png)

	앱 서비스는 자동으로 API 앱의 URL로 설정된 **로그온 URL** 및 **회신 URL**을 사용하여 Azure Active Directory 응용 프로그램을 만듭니다. 두 번째 값을 사용하면 AAD 테넌트의 사용자가 API 앱에 로그인하고 액세스할 수 있습니다.

### API 앱이 보호되는지 확인합니다.

1. 브라우저에서 API 앱의 URL로 이동: Azure 포털에서 **API 앱** 블레이드에서 **URL** 아래 링크를 클릭합니다. 

	인증되지 않은 요청은 API 앱에 도달할 수 없으므로 로그인 화면으로 리디렉션됩니다.

	브라우저가 Swagger UI로 이동하면 브라우저에서 이미 로그온했을 수 있습니다. 이 경우 InPrivate 또는 Incognito 창을 열고 Swagger UI URL로 이동합니다.

18. AAD 테넌트의 사용자 자격 증명으로 로그인합니다.

	로그온하면 "만들기 성공" 페이지가 브라우저에 표시됩니다.

## ToDoListAPI 프로젝트를 구성하여 Azure AD 토큰 획득 및 전송

이 섹션에서는 다음 작업을 수행합니다.

* Azure AD 응용 프로그램 자격 증명을 사용하는 중간 계층 API 앱에서 코드를 추가하여 토큰을 획득하고 데이터 계층 API 앱에 HTTP 요청으로 보냅니다.
* Azure AD에서 필요한 자격 증명을 가져옵니다.
* 중간 계층 API 앱의 Azure 앱 서비스 런타임 환경 설정에 자격 증명을 입력합니다. 

### ToDoListAPI 프로젝트를 구성하여 Azure AD 토큰 획득 및 전송

Visual Studio의 ToDoListAPI 프로젝트에서 다음과 같이 변경합니다.

1. *ServicePrincipal.cs* 파일의 모든 코드에 달린 주석을 제거합니다.

	Azure AD 전달자 토큰을 획득하기 위해 .NET용 ADAL을 사용하는 코드입니다. 나중에 Azure 런타임 환경에서 설정할 몇 가지 구성 값을 사용합니다. 코드는 다음과 같습니다.

		public static class ServicePrincipal
		{
		    static string authority = ConfigurationManager.AppSettings["ida:Authority"];
		    static string clientId = ConfigurationManager.AppSettings["ida:ClientId"];
		    static string clientSecret = ConfigurationManager.AppSettings["ida:ClientSecret"];
		    static string resource = ConfigurationManager.AppSettings["ida:Resource"];
		
		    public static AuthenticationResult GetS2SAccessTokenForProdMSA()
		    {
		        return GetS2SAccessToken(authority, resource, clientId, clientSecret);
		    }
		
		    static AuthenticationResult GetS2SAccessToken(string authority, string resource, string clientId, string clientSecret)
		    {
		        var clientCredential = new ClientCredential(clientId, clientSecret);
		        AuthenticationContext context = new AuthenticationContext(authority, false);
		        AuthenticationResult authenticationResult = context.AcquireToken(
		            resource,
		            clientCredential);
		        return authenticationResult;
		    }
		}

	**참고:** 이 코드는 .NET NuGet 패키지(Microsoft.IdentityModel.Clients.ActiveDirectory)에 ADAL이 필요하며 이는 프로젝트에 이미 설치되어 있습니다. 처음부터 이 프로젝트를 만들 때 이 패키지를 설치해야 했습니다. 이 패키지는 API 앱 new-project 템플릿에서 자동으로 설치되지 않습니다.

2. *컨트롤러/ToDoListController*에서 인증 헤더의 HTTP 요청에 토큰을 추가하는 `NewDataAPIClient` 메서드에서 코드의 주석 처리를 제거합니다.

		client.HttpClient.DefaultRequestHeaders.Authorization =
		    new AuthenticationHeaderValue("Bearer", ServicePrincipal.GetS2SAccessTokenForProdMSA().AccessToken);

3. ToDoListAPI 프로젝트 배포 (프로젝트를 마우스 오른쪽 단추로 클릭한 다음 **게시 > 게시**를 클릭합니다.)

	Visual Studio가 프로젝트를 배포하고 웹앱의 기본 URL로 브라우저를 엽니다. 일반적으로 브라우저에서 Web API 기본 URL로 이동하려고 하는 403 오류 페이지가 표시됩니다.

4. 브라우저를 닫습니다.

### Azure AD 구성 값 가져오기

11. [Azure 클래식 포털](https://manage.windowsazure.com/)에서 **Azure Active Directory**로 이동합니다.

12. **디렉터리** 탭에서 AAD 테넌트를 클릭합니다.

14. **응용 프로그램 > 회사 소유 응용 프로그램**을 클릭한 다음 확인 표시를 클릭합니다.

15. 응용 프로그램 목록에서 ToDoListDataAPI(데이터 계층) API 앱에 대한 인증을 활성화할 때 만들어진 응용 프로그램의 이름을 클릭합니다.

16. **구성** 탭을 클릭합니다.

5. **클라이언트 ID** 값을 복사하여 나중에 가져올 수 있는 위치에 복사합니다.

8. Azure 클래식 포털에서 **회사가 보유한 응용 프로그램** 목록으로 다시 돌아가 중간 계층 ToDoListAPI API 앱에 대해 만든 AAD 응용 프로그램을 클릭합니다(이 자습서에서 만든 것이 아닌 이전 자습서에서 만든 응용 프로그램).

16. **구성** 탭을 클릭합니다.

5. **클라이언트 ID** 값을 복사하여 나중에 가져올 수 있는 위치에 복사합니다.

6. **키** 아래의 **기간 선택** 드롭다운 목록에서 **1년**을 선택합니다.

6. **Save**를 클릭합니다.

	![앱 키 생성](./media/app-service-api-dotnet-service-principal-auth/genkey.png)

7. 키 값을 복사하여 나중에 가져올 수 있는 위치에 복사합니다.

	![새 앱 키 복사](./media/app-service-api-dotnet-service-principal-auth/genkeycopy.png)

### 중간 계층 API 앱의 런타임 환경에서 Azure AD 설정 구성

1. [Azure 포털](https://portal.azure.com/)로 이동한 다음 TodoListAPI(중간 계층) 프로젝트를 호스팅하는 API 앱에 대한 **API 앱** 블레이드로 이동합니다.

2. **설정 > 응용 프로그램 설정**을 클릭합니다.

3. **앱 설정** 섹션에서 다음 키와 값을 추가합니다.

	| **키** | ida:Authority |
	|---|---|
	| **값** | https://login.microsoftonline.com/{your Azure AD 테넌트 이름} |
	| **예제** | https://login.microsoftonline.com/contoso.onmicrosoft.com |

	| **키** | ida:ClientId |
	|---|---|
	| **값** | 호출하는 응용 프로그램(중간 계층 - ToDoListAPI)의 클라이언트 ID |
	| **예제** | 960adec2-b74a-484a-960adec2-b74a-484a |

	| **키** | ida:ClientSecret |
	|---|---|
	| **값** | 호출하는 응용 프로그램의 앱 키(중간 계층 - ToDoListAPI) |
	| **예제** | e65e8fc9-5f6b-48e8-e65e8fc9-5f6b-48e8 |

	| **키** | ida:Resource |
	|---|---|
	| **값** | 호출되는 응용 프로그램(중간 계층 - ToDoListDataAPI)의 클라이언트 ID |
	| **예제** | e65e8fc9-5f6b-48e8-e65e8fc9-5f6b-48e8 |

	**참고**: `ida:Resource`의 경우 **앱 ID URI**가 아닌 호출된 응용 프로그램의 **클라이언트 ID**를 사용해야 해야 합니다.

	이 자습서에서는 중간 계층 및 데이터 계층에 대해 구분된 Azure AD 응용 프로그램을 사용하므로 `ida:ClientId` 및 `ida:Resource`가 서로 다른 값입니다. API 앱 및 보호된 API 앱을 호출하기 위해 단일 Azure AD 응용 프로그램을 사용하는 경우 `ida:ClientId` 및 `ida:Resource` 모두에서 동일한 값을 사용합니다.

	코드는 ConfigurationManager를 사용하여 이러한 값을 가져오므로 프로젝트의 Web.config 파일 또는 Azure 런타임 환경에 저장될 수 있습니다. Azure 앱 서비스에서 ASP.NET 응용 프로그램을 실행하는 동안 환경 설정은 Web.config에서 설정을 자동으로 재정의합니다. 환경 설정은 일반적으로 [Web.config 파일에 비해 중요한 정보를 저장하는 더 안전한 방법](http://www.asp.net/identity/overview/features-api/best-practices-for-deploying-passwords-and-other-sensitive-data-to-aspnet-and-azure)입니다.

6. **Save**를 클릭합니다.

	![저장을 클릭합니다.](./media/app-service-api-dotnet-service-principal-auth/appsettings.png)

### 응용 프로그램 테스트

1. 브라우저에서 AngularJS 프런트 엔드 웹앱의 HTTPS URL로 이동합니다.

2. **할 일 목록** 탭을 클릭하고 Azure AD 테넌트의 사용자에 대한 자격 증명으로 로그인합니다.

4. 응용 프로그램이 작동하고 있는지 확인하려면 할 일 항목을 추가합니다.

	![할 일 목록 페이지](./media/app-service-api-dotnet-service-principal-auth/mvchome.png)

	응용 프로그램이 예상 대로 작동하지 않으면 Azure 포털에 입력한 모든 설정을 다시 확인합니다. 모든 설정이 올바르다고 나타나는 경우 이 자습서의 뒷부분에서 [문제 해결](#troubleshooting) 섹션을 참조하세요.

## 브라우저 액세스로부터 API 앱 보호

이 자습서의 경우 ToDoListDataAPI(데이터 계층) API 앱에 대한 별도의 Azure AD 응용 프로그램을 만들었습니다. 알고 있는 것처럼 앱 서비스가 AAD 응용 프로그램을 만들 때 사용자가 브라우저에서 API 앱의 URL로 이동한 후 로그인하도록 하는 방식으로 AAD 응용 프로그램을 구성합니다. 즉, 서비스 주체뿐만 아니라 Azure AD 테넌트의 최종 사용자도 API에 액세스할 수 있습니다.

보호된 API 앱에서 코드를 작성하지 않고 브라우저가 액세스하는 것을 방지하려면 AAD 응용 프로그램에서 **회신 URL**을 변경하여 API 앱의 기본 URL을 다르게 할 수 있습니다.

### 브라우저 액세스 사용 안 함

1. TodoListService에 대해 만든 AAD 응용 프로그램에 대한 클래식 포털 **구성** 탭에서 **회신 URL** 필드의 값을 변경하여 API 앱의 URL이 아닌 유효한 URL이 되도록 할 수 있습니다.
 
2. **Save**를 클릭합니다.

### 브라우저 액세스가 더 이상 작동하지 않는지 확인

개별 사용자의 자격 증명으로 로그온하여 브라우저에서 API 앱 URL로 이동할 수 있다는 것을 이전에 확인했습니다. 이 섹션에서 더 이상 가능하지 않은지 확인합니다.

1. 새 브라우저 창에서 API 앱의 URL로 다시 이동합니다.

2. 이렇게 하라는 메시지가 표시되면 로그인합니다.

3. 로그인에 성공하지만 오류 페이지가 표시됩니다.

	AAD 앱을 구성하여 AAD 테넌트의 사용자가 브라우저에서 API에 로그인하고 액세스할 수 없도록 합니다. 서비스 주체 토큰을 사용하여 API 앱에 계속 액세스할 수 있으며 이는 웹앱의 URL로 이동하고 할 일 항목을 더 추가하여 확인할 수 있습니다.

## 특정 서비스 주체에 대한 액세스 제한  

지금은 Azure AD 테넌트의 사용자 또는 서비스 주체에 대한 토큰을 얻을 수 있는 모든 호출자가 TodoListDataAPI(데이터 계층) API 앱을 호출할 수 있습니다. 데이터 계층 API 앱이 TodoListAPI(중간 계층) API 앱 및 특정 서비스 주체에서만 호출을 허용하도록 할 수 있습니다.

들어오는 호출에서 `appid` 및 `objectidentifier` 클레임의 유효성을 검사하는 코드를 추가하여 이러한 제한을 추가할 수 있습니다.

이 자습서의 경우 컨트롤러 작업에서 직접 앱 ID 및 서비스 주체 ID의 유효성을 검사하는 코드를 배치합니다. 대안은 사용자 지정 `Authorize` 특성을 사용하거나 시작 시퀀스(예: OWIN 미들웨어)에서 이 유효성 검사를 수행하는 것입니다. 두 번째 방법의 예제는 [이 샘플 응용 프로그램](https://github.com/mohitsriv/EasyAuthMultiTierSample/blob/master/MyDashDataAPI/Startup.cs)을 참조하세요.

TodoListDataAPI 프로젝트를 다음과 같이 변경합니다.

2. *Controllers/TodoListController.cs* 파일을 엽니다.

3. `trustedCallerClientId` 및 `trustedCallerServicePrincipalId`를 설정하는 줄의 주석 처리를 제거합니다.

		private static string trustedCallerClientId = ConfigurationManager.AppSettings["todo:TrustedCallerClientId"];
		private static string trustedCallerServicePrincipalId = ConfigurationManager.AppSettings["todo:TrustedCallerServicePrincipalId"];

4. CheckCallerId 메서드에서 코드의 주석 처리를 제거합니다. 이 메서드는 컨트롤러에서 모든 작업 메서드의 시작 부분에 호출됩니다.

		private static void CheckCallerId()
		{
		    string currentCallerClientId = ClaimsPrincipal.Current.FindFirst("appid").Value;
		    string currentCallerServicePrincipalId = ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/objectidentifier").Value;
		    if (currentCallerClientId != trustedCallerClientId || currentCallerServicePrincipalId != trustedCallerServicePrincipalId)
		    {
		        throw new HttpResponseException(new HttpResponseMessage { StatusCode = HttpStatusCode.Unauthorized, ReasonPhrase = "The appID or service principal ID is not the expected value." });
		    }
		}

5. 새 Azure 앱 서비스에 ToDoListDataAPI 프로젝트를 다시 배포합니다.

6. 브라우저에서 AngularJS 프런트 엔드 웹앱의 HTTPS URL로 이동하고 홈 페이지에서 **할 일 목록** 탭을 클릭합니다.

	백 엔드에 대한 호출이 실패하기 때문에 응용 프로그램이 작동하지 않습니다. 새 코드는 실제 appid 및 objectidentifier를 검사하지만 확인할 올바른 값이 아직 없습니다. 브라우저 개발자 도구 콘솔은 서버가 HTTP 401 오류를 반환하고 있는지를 보고합니다.

	![개발자 도구 콘솔의 오류](./media/app-service-api-dotnet-service-principal-auth/webapperror.png)

	다음 단계에서 예상 값을 구성합니다.

8. Azure AD PowerShell을 사용하여 TodoListWebApp 프로젝트에 대해 만든 Azure AD 응용 프로그램에 대한 서비스 주체의 값을 가져옵니다.

	a. Azure PowerShell을 설치하고 구독에 연결하는 방법에 대한 지침은 [Azure Resource Manager로 Azure PowerShell 사용](../powershell-azure-resource-manager.md)을 참조하세요.

	b. 서비스 주체의 목록을 가져오려면 `Login-AzureRmAccount` 명령 및 `Get-AzureRmADServicePrincipal` 명령을 차례로 실행합니다.

	c. TodoListAPI 응용 프로그램의 서비스 주체에 대한 objectid를 찾고 나중에 복사할 수 있는 위치에 저장합니다.

7. Azure 포털에서 ToDoListAngular 프로젝트를 배포한 웹앱에 대한 웹앱 블레이드로 이동합니다.

9. **설정 > 응용 프로그램 설정**을 클릭합니다.

3. **앱 설정** 섹션에서 다음 키와 값을 추가합니다.

	| **키** | todo:TrustedCallerServicePrincipalId |
	|---|---|
	| **값** | 호출하는 응용 프로그램의 서비스 주체 ID |
	| **예제** | 4f4a94a4-6f0d-4072-4f4a94a4-6f0d-4072 |

	| **키** | todo:TrustedCallerClientId |
	|---|---|
	| **값** | TodoListAPI Azure AD 응용 프로그램에서 복사한 호출하는 응용 프로그램의 클라이언트 ID |
	| **예제** | 960adec2-b74a-484a-960adec2-b74a-484a |

6. **Save**를 클릭합니다.

	![저장을 클릭합니다.](./media/app-service-api-dotnet-service-principal-auth/trustedcaller.png)

6. 브라우저에서 웹앱의 URL로 돌아가서 홈 페이지에서 **할 일 목록** 탭을 클릭합니다.

	신뢰할 수 있는 호출자의 앱 ID 및 서비스 주체 ID가 예상된 값이기 때문에 이번에는 응용 프로그램이 예상 대로 작동합니다.

	![할 일 목록 페이지](./media/app-service-api-dotnet-service-principal-auth/mvchome.png)

## 처음부터 프로젝트 빌드

**Azure API 앱** 프로젝트 템플릿을 사용하고 기본 Values 컨트롤러를 ToDoList 컨트롤러로 대체하여 두 개의 웹 API 프로젝트가 만들어졌습니다. ToDoListAPI 프로젝트에서 Azure AD 서비스 주체 토큰을 확보하기 위해 [.NET 용 ADAL(Active Directory 인증 라이브러리)](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/) NuGet 패키지가 설치되었습니다.
 
ToDoListAngular와 같은 Web API 백 엔드를 통한 AngularJS 단일 페이지 응용 프로그램을 만드는 방법에 대한 내용은 [Hands On Lab: ASP.NET Web API 및 Angular.js를 통해 SPA(단일 페이지 응용 프로그램) 빌드](http://www.asp.net/web-api/overview/getting-started-with-aspnet-web-api/build-a-single-page-application-spa-with-aspnet-web-api-and-angularjs)를 참조하세요. Azure AD 인증 코드를 추가하는 방법에 대한 정보는 [Azure AD로 AngularJS 단일 페이지 앱 보안](../active-directory/active-directory-devquickstarts-angular.md)을 참조하세요.

## 문제 해결

[AZURE.INCLUDE [문제 해결](../../includes/app-service-api-auth-troubleshooting.md)]

* ToDoListAPI(중간 계층) 및 ToDoListDataAPI(데이터 계층)를 혼동하지 않아야 합니다. 예를 들어 이 자습서에서는 데이터 계층 API 앱에 인증을 추가하지만 **앱 키는 중간 계층 API 앱에 대해 만든 Azure AD 응용 프로그램에서 가져와야** 합니다.

## 다음 단계

이 문서는 API 앱 시작 시리즈의 마지막 문서입니다.

Azure Active Directory에 대한 자세한 내용은 다음 리소스를 참조하세요.

* [Azure AD 개발자 가이드](http://aka.ms/aaddev)
* [Azure AD 시나리오](http://aka.ms/aadscenarios)
* [Azure AD 샘플](http://aka.ms/aadsamples)

	[WebApp-WebAPI-OAuth2-AppIdentity-DotNet](http://github.com/AzureADSamples/WebApp-WebAPI-OAuth2-AppIdentity-DotNet) 샘플은 이 자습서에 표시된 것과 비슷하지만, 앱 서비스 인증을 사용할 필요가 없습니다.

Visual Studio를 사용하거나 [원본 제어 시스템](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/source-control)에서 [배포를 자동화](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/continuous-integration-and-continuous-delivery)하여 API 앱에 Visual Studio를 배포하는 다른 방법에 대한 정보는 [Azure 앱 서비스 앱을 배포하는 방법](../app-service-web/web-sites-deploy.md)을 참조하세요.

<!---HONumber=AcomDC_0309_2016-->