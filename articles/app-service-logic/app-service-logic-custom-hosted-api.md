<properties 
	pageTitle="논리 앱에서 사용자 지정 API 호출" 
	description="논리 앱으로 앱 서비스에서 호스팅되는 사용자 지정 API 사용" 
	authors="stepsic-microsoft-com" 
	manager="dwrede" 
	editor="" 
	services="app-service\logic" 
	documentationCenter=""/>

<tags
	ms.service="app-service-logic"
	ms.workload="integration"
	ms.tgt_pltfrm="na"
	ms.devlang="na"	
	ms.topic="article"
	ms.date="02/23/2016"
	ms.author="stepsic"/>
	
# 논리 앱으로 앱 서비스에서 호스팅되는 사용자 지정 API 사용

논리 앱에는 다양한 서비스에 대해 40개가 넘는 다양한 집합의 커넥터가 있으나 고유의 코드를 실행할 수 있는 고유의 사용자 지정 API를 호출하려 할 수 있습니다. 고유의 사용자 지정 Web API를 호스트하는 가장 쉽고 확장성이 뛰어난 방법 중 하나는 앱 서비스를 사용하는 것입니다. 이 문서에서는 앱 서비스 API 앱, 웹앱 또는 모바일 앱에서 호스트되는 Web API를 호출하는 방법을 설명합니다.

## 웹앱 배포

우선 API를 앱 서비스에서 웹앱으로 배포해야 합니다. 여기서 지침은 기본 배포, [ASP.NET 웹앱 만들기](../app-service-web/web-sites-dotnet-get-started.md)를 다룹니다. 논리 앱에서 API를 호출할 수 있지만 최상의 경험을 위해 논리 앱 작업과 쉽게 통합하도록 Swagger 메타데이터를 추가하는 것이 좋습니다. [swagger 추가](../app-service-api/app-service-api-dotnet-get-started.md/#use-swagger-metadata-and-ui)에서 세부정보를 찾을 수 있습니다.

### API 설정

논리 앱 디자이너가 사용자 Swagger를 구문 분석하려면 CORS를 사용하도록 설정하고 웹앱의 APIDefinition 속성을 설정해야 합니다. Azure 포털 내에서 이 속성을 쉽게 설정할 수 있습니다. 웹앱의 설정 블레이드를 열고 API 섹션 아래에서 'API 정의'를 swagger.json 파일의 URL로 설정합니다. 이는 일반적으로 https://{name}.azurewebsites.net/swagger/docs/v1)이며, 논리 앱 디자이너에서 요청할 수 있도록 '*'에 대한 CORS 정책을 추가합니다.

## API로 호출

논리 앱 포털 내에 있을 때 CORS 및 API 정의 속성을 설정한 경우 사용자의 흐름 내에서 사용자 지정 API 작업을 쉽게 추가할 수 있어야 합니다. 디자이너에서 구독 웹 사이트를 탐색하여 swagger URL이 정의된 웹 사이트를 나열하도록 선택할 수 있습니다. 또한 HTTP + Swagger 작업을 사용하여 swagger를 가리키고 사용 가능한 작업 및 입력을 나열할 수 있습니다. 마지막으로 언제나 HTTP 작업을 사용하여 API(swagger 문서가 없거나 이 문서를 노출하지 않는 API도 가능)를 호출하는 요청을 만들 수 있습니다.

API를 보호하려면 두 가지 다른 방법이 있습니다.

1. 코드 변경 필요하지 않음 - Azure Active Directory를 사용하여 코드 변경 또는 다시 배포 없이 API를 보호할 수 있습니다.
1. API의 코드에서 기본 인증, AAD 인증 또는 인증서 인증을 적용합니다.

## 코드 변경 없이 API에 호출 보호 

이 섹션에서는 하나의 논리 앱 및 하나의 웹앱, 총 두 가지 Azure Active Directory 응용 프로그램을 만듭니다. 논리 앱용 AAD 응용 프로그램과 관련된 서비스 주체(클라이언트 ID 및 암호)를 사용하여 웹앱에 호출을 인증할 수 있습니다. 마지막으로 논리 앱 정의에서 응용 프로그램 ID를 포함합니다.

### 1부: 논리 앱용 응용 프로그램 ID 설치

이는 논리 앱이 Active Directory에 대해 인증하기 위해 사용하는 것입니다. 디렉터리에 대해 한 번만 수행하면 *됩니다*. 예를 들어 모든 논리 앱에 대해 같은 ID를 사용하도록 선택할 수 있지만 원한다면 논리 앱마다 고유한 ID를 만들 수도 있습니다. UI에서 이 작업을 수행하거나 PowerShell을 사용할 수 있습니다.

#### Azure 클래식 포털을 사용하여 응용 프로그램 ID 만들기

1. [Azure 클래식 포털의 Active directory](https://manage.windowsazure.com/#Workspaces/ActiveDirectoryExtension/directory)로 이동하고 웹앱에 사용할 디렉터리를 선택합니다.
2. **응용 프로그램** 탭을 클릭합니다.
3. 페이지 아래쪽의 명령 모음에서 **추가**를 클릭합니다.
4. ID에 사용할 이름을 지정하고 다음 화살표를 클릭합니다.
5. 두 텍스트 상자에 도메인으로 형식이 지정된 고유 문자열에 넣고 확인 표시를 클릭합니다.
6. 응용 프로그램에 **구성** 탭을 클릭합니다.
7. **클라이언트 ID**를 복사하고 논리 앱에서 사용합니다.
8. **키** 섹션에서 **기간 선택**을 클릭하고 1년 또는 2년 중 하나를 선택합니다.
9. 화면 아래쪽에 있는 **저장** 단추를 클릭합니다.(몇 초 기다릴 수 있음)
10. 이제 상자에 키를 복사해야 합니다. 이 또한 논리 앱에 사용합니다.

#### PowerShell을 사용하여 응용 프로그램 ID 만들기
1. `Switch-AzureMode AzureResourceManager`
2. `Add-AzureAccount`
3. `New-AzureADApplication -DisplayName "MyLogicAppID" -HomePage "http://someranddomain.tld" -IdentifierUris "http://someranddomain.tld" -Password "Pass@word1!"`
4. **테넌트 ID**, **응용 프로그램 ID** 및 사용한 암호를 복사합니다.

### 2부: AAD 앱 ID로 웹앱 보호

웹앱이 이미 배포된 경우 포털에서 사용할 수 있습니다. 그렇지 않은 경우 Azure 리소스 관리자 배포의 권한 부여 일부를 사용할 수 있습니다.

#### Azure 포털에서 권한 부여 사용

1. 웹앱으로 이동하고 명령 모음에서 **설정**을 클릭합니다.
2. **권한 부여/인증**을 클릭합니다. 
3. **켭니다**.

이 시점에서 응용 프로그램은 사용자에 대해 자동으로 만들어집니다. 이 응용 프로그램의 클라이언트 ID는 3부에 필요하므로 다음을 수행해야 합니다.

1. [Azure 클래식 포털의 Active Directory](https://manage.windowsazure.com/#Workspaces/ActiveDirectoryExtension/directory)로 이동하고 디렉터리를 선택합니다. 
2. 검색 상자에서 앱 검색
3. 목록에서 클릭
4. **구성** 탭을 클릭합니다.
5. **클라이언트 ID**가 표시되어야 합니다.

#### ARM 템플릿을 사용하여 웹앱 배포

우선 웹앱용 응용 프로그램을 만들어야 합니다. 논리 앱에 사용되는 응용 프로그램과 달라야 합니다. 위의 1부에서 다음 단계를 시작하지만 이제 **홈페이지** 및 **IdentifierUris**의 경우 실제 웹앱의 https://**URL**을 사용합니다.

>[AZURE.NOTE]웹앱용 응용 프로그램을 만들 때 PowerShell commandlet가 웹 사이트에 사용자가 로그인하는 데 필요한 사용 권한을 설정하지 않기 때문에 [Azure 클래식 포털 접근 방식](https://manage.windowsazure.com/#Workspaces/ActiveDirectoryExtension/directory)을 사용해야 합니다.

클라이언트 ID가 있으면 테넌트 ID는 배포 템플릿에서 웹앱의 하위 리소스처럼 다음을 포함합니다.

```
"resources" : [
	{
		"apiVersion" : "2015-08-01",
		"name" : "web",
		"type" : "config",
		"dependsOn" : [
			"[concat('Microsoft.Web/sites/','parameters('webAppName'))]"
		],
		"properties" : {
			"siteAuthEnabled": true,
			"siteAuthSettings": {
			  "clientId": "<<clientID>>",
			  "issuer": "https://sts.windows.net/<<tenantID>>/",
			}
		}
	}
]
```

AAD를 사용하는 빈 웹앱 및 논리 앱을 함께 배포하는 자동 배포를 실행하려면 다음 단추를 클릭합니다. [![Azure에 배포](http://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-logic-app-custom-api%2Fazuredeploy.json)

전체 템플릿은 [AAD에서 보호하고 앱 서비스에서 호스팅되는 사용자 지정 API로 논리 앱 호출](https://github.com/Azure/azure-quickstart-templates/blob/master/201-logic-app-custom-api/azuredeploy.json)을 참조하세요.


### 3부: 논리 앱의 권한 부여 섹션 채우기

**HTTP** 동작의 **권한 부여** 섹션에서 입니다. `{"tenant":"<<tenantId>>", "audience":"<<clientID from Part 2>>", "clientId":"<<clientID from Part 1>>","secret": "<<Password or Key from Part 1>>","type":"ActiveDirectoryOAuth" }`

| 요소 | 설명 |
|---------|-------------|
| type | 인증 유형입니다. ActiveDirectoryOAuth 인증의 경우 이 값은 ActiveDirectoryOAuth입니다. |
| tenant | AD 테넌트를 식별하는 데 사용되는 테넌트 ID입니다. |
| audience | 필수입니다. 연결 중인 리소스입니다. |
| clientID | Azure AD 응용 프로그램의 클라이언트 ID입니다. |
| secret | 필수입니다. 토큰을 요청하는 클라이언트의 암호입니다. | 

위의 템플릿이 이미 이를 설치했지만 직접 논리 앱에 권한을 부여하려면 전체 권한 부여 섹션을 포함해야 합니다.

## 코드에서 API 보호

### 인증서 인증

클라이언트 인증서를 사용하여 웹앱에 들어오는 요청을 확인할 수 있습니다. 코드를 설치하는 방법은 [웹앱에 대 한 TLS 상호 인증을 구성하는 방법](../app-service-web/app-service-web-configure-tls-mutual-auth.md)을 참조하세요.

*권한 부여* 섹션에서 다음을 제공해야 합니다. `{"type": "clientcertificate","password": "test","pfx": "long-pfx-key"}`

| 요소 | 설명 |
|---------|-------------|
| type | 필수입니다. 인증 형식입니다. SSL 클라이언트 인증서의 경우 이 값은 ClientCertificate입니다. |
| pfx | 필수입니다. PFX 파일의 Base64 인코딩 콘텐츠입니다. |
| password | 필수입니다. PFX 파일에 액세스하기 위한 암호입니다. |

### 기본 인증

기본 인증(예: 사용자 이름 및 암호)을 사용하여 들어오는 요청의 유효성을 검사할 수 있습니다. 기본 인증은 일반적인 패턴이며 앱을 작성하는 모든 언어에서 수행할 수 있습니다.

*권한 부여* 섹션에서 다음을 제공해야 합니다. `{"type": "basic","username": "test","password": "test"}`

| 요소 | 설명 |
|---------|-------------|
| type | 필수입니다. 인증 유형입니다. 기본 인증의 경우 이 값은 기본입니다. |
| username | 필수입니다. 인증하기 위한 사용자 이름입니다. |
| password | 필수입니다. 인증하기 위한 암호입니다. |
 
### 코드에서 AAD 인증 처리

기본적으로 포털에서 사용할 수 있는 Azure Active Directory 인증은 세분화된 권한 부여를 수행하지 않습니다. 예를 들어 API를 특정 사용자 또는 앱에 잠금을 만들지 않지만 특정 테넌트에는 잠금을 만듭니다.

예를 들어 코드에서 논리 앱에 API를 제한하려는 경우 JWT을 포함하고 호출자를 검사하는 헤더를 추출할 수 있으며 이는 일치하지 않는 요청을 거부합니다.

계속 진행하면 포털 기능을 활용하지 않고 고유의 코드에서 완전히 구현하려는 경우 다음 문서를 읽습니다. [Azure 앱 서비스에서 인증을 위해 Active Directory 사용](../app-service-web/web-sites-authentication-authorization.md).

위의 단계를 계속해서 수행하여 논리 앱에 대한 응용 프로그램 ID를 만들고 해당 ID를 사용하여 API를 호출해야 합니다.

<!---HONumber=AcomDC_0224_2016-->