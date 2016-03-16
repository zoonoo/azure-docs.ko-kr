<properties
	pageTitle="PowerApps에서 Azure Active Directory 도메인의 백 엔드 시스템에 연결하도록 API 구성 | Microsoft Azure"
	description="PowerApps에서 AAD 보호 백 엔드에 연결하도록 API 구성"
	services=""
    suite="powerapps"
	documentationCenter="" 
	authors="MandiOhlinger"
	manager="dwrede"
	editor=""/>

<tags
   ms.service="powerapps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="11/25/2015"
   ms.author="guayan"/>

# API를 Azure Active Directory 도메인의 백 엔드 리소스에 연결하도록 구성
더 많은 사용자가 Azure Active Directory(AAD)에서 도메인을 만들게 됨에 따라 이러한 AAD 도메인에 백 엔드 리소스도 추가되고 있습니다. API를 만들어 이러한 백 엔드 리소스에 연결하도록 구성할 수 있습니다.

#### 시작하기 위한 필수 조건

- [PowerApps 엔터프라이즈](powerapps-get-started-azure-portal.md)에 등록합니다.
- [앱 서비스 환경](powerapps-get-started-azure-portal.md)을 만듭니다.
- [Azure PowerShell][11] 1.0 미리 보기 이상을 설치합니다.
- [앱 서비스 환경](powerapps-register-api-hosted-in-app-service.md)에 API를 등록합니다.

## 1단계: Active Directory 응용 프로그램 만들기 및 사용 권한 지정

AAD 도메인의 백 엔드 시스템에 액세스하려면 AAD 응용 프로그램을 만들고 기존 백 엔드(역시 AAD 응용 프로그램)에 적절한 권한을 지정합니다. 단계:

1. [Azure 클래식 포털][13]에서 Azure Active Directory로 이동하여 테넌트(또는 디렉터리)를 열고 **응용 프로그램** 탭을 선택합니다. ![][14]
2. 아래쪽의 **추가** 단추를 선택합니다. 그런 다음:  

	a) **조직에서 개발 중인 응용 프로그램 추가**를 선택합니다. b) 응용 프로그램의 이름을 입력하고 **웹 응용 프로그램 및/또는 웹 API**를 선택합니다. c) **로그온 URL** 및 **앱 ID URI**에 로그온하고 AAD 내에서 고유한 URL 및 조직에 의미 있는 URL을 입력합니다. 예를 들어 http://powerappssignon.contoso.com 또는 http://powerappsappid.contoso.com을 입력할 수 있습니다. 조직의 AAD 도메인 내에서 URL을 사용하는 것이 좋습니다. URL은 식별자로 사용되며 존재하기 위해 필요한 요구 사항은 없습니다. 아무도 사용자가 입력한 URL을 탐색하려고 하지 않습니다. HTTP 또는 HTTPS를 입력할 수 있습니다.

3. 새로 만든된 AAD 응용 프로그램 페이지에서 **구성** 탭으로 이동합니다. ![][15]
4. **키** 섹션에서 드롭다운 목록을 사용하여 기간을 선택합니다. 참고로 키는 **저장**을 선택한 후 표시됩니다. ![][16]
5. **Single Sign-On**에서 ``https://<your App Service Environment name>.azure-apim.net:456/redirect``를 **회신 URL**로 추가합니다.
6. **다른 응용 프로그램에 대한 권한**에서:  

	a) **응용 프로그램 추가**를 선택합니다. 팝업 창에서 기존 백 엔드를 보호하는 AAD 응용 프로그램을 선택합니다. ![][17]

	b) 드롭다운 목록을 사용하여 사용 권한을 추가합니다. ![][18]

7. 아래쪽의 **저장**을 선택합니다.
8. **클라이언트 ID** 및 **키**를 복사하여 저장합니다. 키는 Azure 포털을 닫은 후 다시 표시되지 않습니다. 

AAD 응용 프로그램에 대해 더 자세히 알아보려면 [Azure Active Directory와 응용 프로그램 통합](../active-directory-integrating-applications.md)을 참조하세요.

## 2단계: Azure PowerShell을 사용하여 AP를 구성

이 시점에서 Azure 포털에서는 API에 필요한 구성을 초기화할 수 없습니다. Azure 포털에서 API를 구성하려면 다음 Auzre PowerShell 스크립트를 사용합니다.

> [AZURE.TIP]Azure PowerShell을 설치, 구성 및 실행하는 방법에 대해 알아보려면 [Azure PowerShell 설치 및 구성 방법][11]을 참조하세요. 다음 스크립트는 Azure PowerShell 1.0 미리 보기 이상에서 작동합니다.

```powershell
# get the API resource
$api = Get-AzureRmResource -ResourceType Microsoft.Web/apiManagementAccounts/apis -ResourceName <App Service Environment name>/<API name> -ResourceGroupName <resource group name>

# configure the API resource for AAD authentication
$connectionParameters = @{
    token = @{
        type = "oauthSetting";
        oAuthSettings = @{
            identityProvider = "aad";
            clientId = "<your AAD app client id>";
            clientSecret = "<your AAD app key>";
            customParameters = @{
                TenantId = @{ # this property is optional
                    value = "<your AAD tenant ID>"
                };
                ResourceUri = @{ # this property is required
                    value = "<the app ID URI of the AAD app protecting your backend>"
                }
            }
        }
    }
}
Add-Member -InputObject $api.Properties -MemberType NoteProperty -Name ConnectionParameters -Value $connectionParameters -Force

# update the API resource
New-AzureRmResource -Location $api.Location -ResourceId $api.ResourceId -Properties $api.Properties
```

**여기서** **토큰** 연결 매개 변수 이름이 중요 합니다. 카멜식 대/소문자만한 길이의 고유한 이름을 선택할 수 있습니다. 나중에 백 엔드 코드 또는 API 정책에서 이름을 사용합니다.

다음으로 [Azure 포털][19]로 이동하고 API의 **일반** 설정 블레이드로 이동합니다. 추가 구성 옵션이 표시됩니다. ![][21]


## 사용해보기

PowerApps에서 앱을 엽니다. **사용 가능한 연결**에 새 API가 나열됩니다. **연결**을 선택하면 AAD 로그인 창이 표시됩니다. 조직의 AAD 계정 세부 정보를 입력 하면 연결이 생성됩니다.

이제 앱에서 이 연결을 사용하여 API에 대한 런타임 호출을 수행하면 백 엔드가 다음과 같은 [Base64 인코딩][20] 형식의 **x-ms-apim-tokens** HTTP 헤더에서 사용자의 AAD 토큰을 받습니다.

```json
{
  "token": {
    "AccessToken": ""
    // ...
  }
}
```

**여기서** 속성 이름 **토큰**은 설정을 구성할 때 사용하는 연결 매개 변수 이름과 일치합니다.

그런 다음 백 엔드 코드가 **AccessToken** 속성에서 AAD 토큰을 가져오며 필요한 경우 이를 사용할 수 있습니다. 앱 서비스 환경에서 토큰을 자동으로 새로 고칩니다.

## API 정책 구성

선택적으로 API 정책을 사용하여 AAD 토큰을 표준 HTTP **권한 부여** 헤더로 설정할 수도 있습니다. 이러한 방식으로 백 엔드 코드가 AAD 토큰을 사용해야 하는 경우 사용자 지정 HTTP 헤더를 조사하지 않고 표준 방법으로 해당 토큰을 가져와서 Base64 디코딩을 수행할 수 있습니다. 이렇게 하려면 Azure 포털로 이동하고 API의 **정책** 블레이드로 이동한하여 다음 정책을 설정합니다.

```xml
<policies>
	<inbound>
		<base/>
		<choose>
			<when condition="@(context.Variables.ContainsKey(";tokens";) &amp;&amp; ((JObject)context.Variables[";tokens";])[";token";] != null &amp;&amp; !String.IsNullOrEmpty((string)((JObject)context.Variables[";tokens";])[";token";][";AccessToken";]))">
				<set-header exists-action="override" name="Authorization">
					<value>@("Bearer " + (string)((JObject)context.Variables["tokens"])[";token";]["AccessToken"])</value>
				</set-header>
			</when>
		</choose>
	</inbound>
	<backend>
		<base/>
	</backend>
	<outbound>
		<base/>
	</outbound>
</policies>
```

이 정책을 살펴보면 기본적으로 **tokens** 변수를 사용하여 **x-ms-apim-tokens** 헤더의 값을 디코딩된 JObject로 참조할 수 있습니다. 그런 다음 **set-header** 정책을 사용하여 실제 AAD 토큰을 가져와서 **권한 부여** 헤더로 설정할 수 있습니다. 이 정책은 [Azure API 관리](https://azure.microsoft.com/services/api-management/)에서 사용하는 정책과 동일합니다. 더 자세히 알아보려면 [Azure API 관리의 정책](../api-management-howto-policies.md)을 참조하세요.

**여기서** 속성 이름 **토큰**은 설정을 구성할 때 사용한 연결 매개 변수 이름과 일치합니다.

## 요약 및 다음 단계

이 항목에서는 API를 Azure Active Directory 도메인의 백 엔드 리소스에 연결(및 인증)하는 방법을 살펴보았습니다. 다음은 PowerApps에 대한 자세한 내용을 확인할 수 있는 몇 가지 관련 항목 및 리소스입니다.

- [PowerApps용 API 개발](powerapps-develop-api.md)


<!--References-->
[11]: ../powershell-install-configure.md
[13]: https://manage.windowsazure.com
[14]: ./media/powerapps-configure-apis-aad/aad-applications-tab.png
[15]: ./media/powerapps-configure-apis-aad/aad-application-configure-tab.png
[16]: ./media/powerapps-configure-apis-aad/aad-application-configure-keys.png
[17]: ./media/powerapps-configure-apis-aad/aad-application-add-other-application.png
[18]: ./media/powerapps-configure-apis-aad/aad-application-add-permissions.png
[19]: https://portal.azure.com
[20]: https://tools.ietf.org/html/rfc4648
[21]: ./media/powerapps-configure-apis-aad/api-settings-aad.png

<!---HONumber=AcomDC_1203_2015-->