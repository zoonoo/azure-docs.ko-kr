<properties 
	pageTitle="Azure Mobile Engagement 인증 API 시작하기"
	description="이전에 Capptain API 인증을 사용하다가 이제 새 Azure Mobile Enagagement API 인증을 사용해야 하는 고객을 위한 마이그레이션 가이드입니다." 
	services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="wesmc7777"
	manager="erikre"
	editor=""/>

<tags
	ms.service="mobile-engagement"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="mobile-multiple"
	ms.workload="mobile" 
	ms.date="02/17/2016"
	ms.author="wesmc"/>

# Azure Mobile Engagement - 인증에 API 사용

## 개요

이 문서에서는 새로운 API에 대해 인증 메커니즘을 작동하는 방법을 자세히 설명합니다.

여러분이 유효한 Azure 구독을 보유하고 있고 [개발자 자습서](mobile-engagement-windows-store-dotnet-get-started.md) 중 하나를 사용하여 Mobile Engagement 앱을 만들었다고 가정합니다.

## 인증

Microsoft Azure Active Directory 기반 OAuth 토큰을 사용하여 인증해야 합니다.

API 요청을 인증하려면 모든 요청에 인증 헤더를 추가해야 합니다. 다음과 같은 형식이어야 합니다.

	Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGmJlNmV2ZWJPamg2TTNXR1E...

>[AZURE.NOTE] Azure Active Directory 토큰은 1시간 후에 만료됩니다.

토큰을 얻는 몇 가지 방법이 있습니다. API는 일반적으로 클라우드 서비스에서 호출되므로 주로 API 키를 사용할 것입니다. Azure 용어에서는 API 키를 서비스 주체 암호라고 부릅니다. 다음은 키를 수동으로 설정하는 방법을 설명하는 절차입니다.

> [AZURE.WARNING] Azure Active Directory에 표시되는 키는 포털에 표시되는 Mobile Engagement API 키가 아닙니다. Mobile Engagement API 키의 개념은 사용되지 않고 이 문서에서 설명하는 AAD 인증으로 대체되었습니다.

#### 일 회 설정(수동)

이 작업을 수행하는 동안 다음 정보를 메모해 두십시오. 나중에 필요한 정보입니다.
	
1. [이 가이드](../resource-group-create-service-principal-portal.md)를 사용하여 Azure Active Directory 응용 프로그램을 만듭니다.

	- 선택한 응용 프로그램 이름. 이 문서에서는 `{AD_APP_NAME}`입니다.
	- 구성 메뉴에 표시되는 클라이언트 식별자. 이 문서에서는 `{CLIENT_ID}`입니다.
	- 저장 후 한 번만 표시되는 키. 이 문서에서는 `{CLIENT_SECRET}`입니다.
	- 아래쪽 메뉴 모음에서 **끝점 보기** 단추를 클릭하고, **OAUTH 2.0 토큰 끝점 URL**을 복사합니다. 이 문서에서는 `https://login.microsoftonline.com/{TENANT_ID}/oauth2/token`입니다. <br/>                                    
2. [Azure CLI](../xplat-cli-install.md)를 사용하여 서비스 주체에 읽기 권한자, 소유자 등의 역할을 할당합니다.

	Windows의 경우 `C:\Program Files (x86)\Microsoft SDKs\Azure\CLI\bin`을 포함하도록 Azure 명령을 수정해야만 `PATH` 환경 변수를 사용할 수 있습니다.

	다음 명령을 실행하여 Azure CLI(명령줄 인터페이스)를 통해 계정을 설정합니다.

		azure config mode arm 
		azure login

	그런 다음 이 명령을 사용하여 응용 프로그램의 개체 Id를 찾습니다.

		$ azure ad sp show --search {AD_APP_NAME} 
		info: Executing command ad sp show 
		+ Getting active directory service principals
		data: Object Id: 71785194-b7f5-4701-a9d6-fefb6cd32d18 
		data: Display Name: {AD_APP_NAME} 
		data: Service Principal Names:
		data: {AD_APP_URI}
		data: 8cdaf270-763c-4577-b2a2-ce559b47d353 
		data: 
		info: ad sp show command OK

	출력에서 `Object Id`를 메모해 둡니다.

	그런 다음 이 명령을 사용하여 서비스 주체에 `Owner` 역할을 할당합니다.
  
		$ azure role assignment create --objectId {OBJECT_ID} -o Owner 
		info: Executing command role assignment create
		+ Finding role with specified name
		-data: RoleAssignmentId :
		/subscriptions/{SUBSCRIPTION_ID}/providers/Microsoft.Authorization/roleAssignm
		ents/009392b1-2b7c-4de8-8f70-1fccb2e0a331 
		data: RoleDefinitionName : Reader
		data: RoleDefinitionId : acdd72a7-3385-48ef-bd42-f606fba81ae7 
		data: Scope : /subscriptions/{SUBSCRIPTION_ID} 
		data: Display Name : {AD_APP_NAME}
		data: SignInName :
		data: ObjectId : {OBJECT_ID} 
		data: ObjectType : ServicePrincipal

#### 일 회 설정(스크립트 사용)

PowerShell 스크립트를 사용하여 앞에서 언급한 단계를 수행하는 대체 방법입니다.

1. Azure PowerShell 최신 버전을 받습니다.

	현재 [여기](https://github.com/Azure/azure-powershell/releases/tag/v1.2.1-February2016)서 1.2.1 버전을 다운로드할 수 있습니다.

2. 관리자 모드에서 Windows PowerShell을 열고 [Azure 리소스 관리자 cmdlet](https://msdn.microsoft.com/library/mt125356.aspx)이 설치되어 있는지 확인합니다.

		Install-Module AzureRM
		Install-AzureRM

3. 다음 명령을 실행합니다.

		Import-Module AzureRM.profile

4. 다음 명령을 실행하여 로그인 대화 상자를 시작합니다. 로그인하면 명령에 "활성" 구독이 표시될 것입니다. 사용자가 실행하는 모든 명령에 영향을 받는 구독이라는 뜻입니다.

		Add-AzureRmAccount

5. 다음 명령을 실행하여 모든 구독을 나열합니다. 사용할 구독의 guid를 복사합니다.

		Get-AzureRmSubscription

6. 다음 명령을 실행하여 구독 guid를 제공하고 사용할 구독을 구성합니다. 이 방법은 구독이 여러 개인 경우에 특히 유용합니다.

		Select-AzureRmSubscription –SubscriptionId <subscriptionId>

7. [New-AzureRmServicePrincipalOwner.ps1](https://raw.githubusercontent.com/matt-gibbs/azbits/master/src/New-AzureRmServicePrincipalOwner.ps1) 스크립트의 텍스트를 로컬 컴퓨터에 복사하여 실행합니다.

	>[Azure.Note] PowerShell 스크립트를 실행할 수 없도록 기본 보안 정책이 설정되었을 수 있습니다. 이 경우 다음 명령을 사용하여 스크립트 실행을 허용하도록 실행 정책을 일시적으로 구성합니다.

	>	Set-ExecutionPolicy RemoteSigned

	ServicePrincipal에 할당할 “이름”을 입력하라고 스크립트에서 요구할 것입니다. 아무 이름이나 원하는 이름을 입력하면 됩니다.

	스크립트가 완료되면 AD를 사용하여 프로그래밍 방식으로 인증해야 하는 **TenantId**, **SubscriptionId**, **ApplicationId** 및 **Secret** 값이 표시됩니다.

	이러한 값을 참조용으로 복사해 둡니다. 이제 액세스 토큰을 얻기 위해 TenantId를 `{TENANT_ID}`로, ApplicationId `{CLIENT_ID}`로, Secret을 `{CLIENT_SECRET}`으로 사용합니다.

8. Azure 관리 포털에서 새 AD 응용 프로그램이 **회사가 보유한 응용 프로그램 표시** 아래에 있는지 확인합니다.

#### 유효한 토큰을 얻는 단계

새 토큰을 얻으려면 이 API를 호출합니다.

	https://login.microsoftonline.com/{TENANT_ID}/oauth2/token 

다음은 요청 예제입니다.

	POST /{TENANT_ID}/oauth2/token HTTP/1.1
	Host: login.microsoftonline.com
	Content-Type: application/x-www-form-urlencoded Content-Length: 195
	grant_type=client_credentials&client_id={CLIENT_ID}&client_secret={CLIENT_SECRET}&reso
	urce=https%3A%2F%2Fmanagement.core.windows.net%2F

다음은 응답 예제입니다.

	HTTP/1.1 200 OK
	Content-Type: application/json; charset=utf-8
	Content-Length: 1234
	
	{"token_type":"Bearer","expires_in":"3599","expires_on":"1445395811","not_before":"144
	5391911","resource":"https://management.core.windows.net/","access_token":{ACCESS_T
	OKEN}}

이 예에서는 POST 매개 변수의 URL 인코딩을 포함했으며, `resource` 값은 실제로 `https://management.core.windows.net/`입니다. 또한 URL에 특수 문자가 포함될 수 있으므로 `{CLIENT_SECRET}` URL 인코딩에 주의해야 합니다.

이제 모든 API 호출에 권한 부여 요청 헤더를 포함합니다.

	Authorization: Bearer {ACCESS_TOKEN}

401 상태 코드가 반환되면 응답 본문을 검사합니다. 토큰이 만료되었을 수도 있습니다. 토큰이 만료된 경우 새 토큰을 가져옵니다.

##API 사용

이제 유효한 토큰이 있으니 API 호출을 만들 준비가 완료되었습니다.

1. 각 API 요청에서, 이전 섹션에서 얻은 만료되지 않은 유효한 토큰을 전달해야 합니다.

2. 응용 프로그램을 식별하는 일부 매개 변수를 요청 URI에 연결해야 합니다. 요청 URI의 모양은 다음과 같습니다.

		https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/MobileEngagement/
		providers/Microsoft.MobileEngagement/appcollections/{app-collection}/apps/{app-resource-name}/

	매개 변수를 가져오려면 응용 프로그램 이름을 클릭하고 대시보드를 클릭합니다. 그러면 다음과 같은 페이지에 매개 변수 3개가 모두 표시될 것입니다.

	- **1** `{subscription-id}`
	- **2** `{app-collection}`
	- **3** `{app-resource-name}`

	![](./media/mobile-engagement-api-authentication/mobile-engagement-api-uri-params.png)

>[AZURE.NOTE] <br/> 1. 이 API 루트 주소는 이전 API의 것이므로 무시합니다.<br/> 2. 응용 프로그램 이름 자체와 다른 응용 프로그램 리소스 이름을 사용해야 합니다.

<!---HONumber=AcomDC_0224_2016-->