---
author: erhopf
ms.author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 07/23/2019
ms.openlocfilehash: 8754504655cdd08c9bf9f89311cb6c5d1057f0e6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78262559"
---
## <a name="authenticate-with-azure-active-directory"></a>Azure Active Directory를 사용하여 인증

> [!IMPORTANT]
> 1. 현재 컴퓨터 비전 API, Face API, 텍스트 분석 API, 몰입형 판독기, 양식 인식기, 변칙 탐지기 및 Azure Active Directory(AAD)를 사용한 Bing 사용자 지정 검색 지원 인증을 제외한 모든 Bing **서비스만** 이 서비스를 지원합니다.
> 2. AAD 인증은 Azure 리소스의 사용자 지정 하위 도메인 이름과 함께 항상 사용해야 합니다. [지역 끝점은](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-custom-subdomains#is-there-a-list-of-regional-endpoints) AAD 인증을 지원하지 않습니다.

이전 섹션에서는 단일 서비스 또는 다중 서비스 구독 키를 사용하여 Azure Cognitive 서비스에 대해 인증하는 방법을 보여 주어 도모했습니다. 이러한 키는 개발을 시작하는 빠르고 쉬운 경로를 제공하지만 역할 기반 액세스 제어가 필요한 보다 복잡한 시나리오에서는 부족합니다. Azure Active Directory(AAD)를 사용하여 인증하는 데 필요한 내용을 살펴보겠습니다.

다음 섹션에서는 Azure Cloud Shell 환경 또는 Azure CLI를 사용하여 하위 도메인을 만들고, 역할을 할당하고, Azure Cognitive Services를 호출하는 베어러 토큰을 얻습니다. 문제가 발생하면 Azure Cloud Shell/Azure CLI의 각 명령에 대해 사용 가능한 모든 옵션이 있는 링크가 각 섹션에 제공됩니다.

### <a name="create-a-resource-with-a-custom-subdomain"></a>사용자 지정 하위 도메인을 사용하여 리소스 만들기

첫 번째 단계는 사용자 지정 하위 도메인을 만드는 것입니다. 사용자 지정 하위 도메인 이름이 없는 기존 코그너티브 서비스 리소스를 사용하려면 [코그너티브 서비스 사용자 지정 하위 도메인의](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-custom-subdomains#how-does-this-impact-existing-resources) 지침을 따라 리소스에 대한 사용자 지정 하위 도메인을 사용하도록 설정합니다.

1. Azure 클라우드 셸을 열어 시작합니다. 그런 다음 [구독을 선택합니다.](https://docs.microsoft.com/powershell/module/az.accounts/set-azcontext?view=azps-3.3.0)

   ```powershell-interactive
   Set-AzContext -SubscriptionName <SubscriptionName>
   ```

2. 다음으로 사용자 지정 하위 도메인을 사용하여 [인지 서비스 리소스를 만듭니다.](https://docs.microsoft.com/powershell/module/az.cognitiveservices/new-azcognitiveservicesaccount?view=azps-1.8.0) 하위 도메인 이름은 전역고유해야 하며 "",", "!", ""와 같은 특수 문자를 포함할 수 없습니다.

   ```powershell-interactive
   New-AzCognitiveServicesAccount -ResourceGroupName <RESOURCE_GROUP_NAME> -name <ACCOUNT_NAME> -Type <ACCOUNT_TYPE> -SkuName <SUBSCRIPTION_TYPE> -Location <REGION> -CustomSubdomainName <UNIQUE_SUBDOMAIN>
   ```

3. 성공하면 **끝점에** 리소스에 고유한 하위 도메인 이름이 표시되어야 합니다.


### <a name="assign-a-role-to-a-service-principal"></a>서비스 주체에 역할 할당

리소스와 연결된 사용자 지정 하위 도메인이 있으므로 서비스 주체에 역할을 할당해야 합니다.

> [!NOTE]
> AAD 역할 할당이 전파되는 데 최대 5분이 걸릴 수 있습니다.

1. 먼저 [AAD 응용 프로그램을](https://docs.microsoft.com/powershell/module/Az.Resources/New-AzADApplication?view=azps-1.8.0)등록해 보겠습니다.

   ```powershell-interactive
   $SecureStringPassword = ConvertTo-SecureString -String <YOUR_PASSWORD> -AsPlainText -Force

   New-AzADApplication -DisplayName <APP_DISPLAY_NAME> -IdentifierUris <APP_URIS> -Password $SecureStringPassword
   ```

   다음 단계에서 **ApplicationId가** 필요합니다.

2. 그런 다음 AAD 응용 프로그램에 대한 [서비스 주체를 만들어야](https://docs.microsoft.com/powershell/module/az.resources/new-azadserviceprincipal?view=azps-1.8.0) 합니다.

   ```powershell-interactive
   New-AzADServicePrincipal -ApplicationId <APPLICATION_ID>
   ```

   >[!NOTE]
   > Azure 포털에 응용 프로그램을 등록하면 이 단계가 완료됩니다.

3. 마지막 단계는 서비스 주체(리소스에 대한 범위)에 ["인지 서비스 사용자" 역할을 할당하는](https://docs.microsoft.com/powershell/module/az.Resources/New-azRoleAssignment?view=azps-1.8.0) 것입니다. 역할을 할당하면 이 리소스에 대한 서비스 주체 액세스 권한을 부여하게 됩니다. 구독의 여러 리소스에 대해 동일한 서비스 주체 액세스 권한을 부여할 수 있습니다.
   >[!NOTE]
   > 서비스 주체의 ObjectId는 응용 프로그램에 대한 ObjectId가 아니라 사용됩니다.
   > ACCOUNT_ID 만든 코그너티브 서비스 계정의 Azure 리소스 ID입니다. Azure 포털에서 리소스의 "속성"에서 Azure 리소스 ID를 찾을 수 있습니다.

   ```azurecli-interactive
   New-AzRoleAssignment -ObjectId <SERVICE_PRINCIPAL_OBJECTID> -Scope <ACCOUNT_ID> -RoleDefinitionName "Cognitive Services User"
   ```

### <a name="sample-request"></a>샘플 요청

이 샘플에서는 암호가 서비스 주체를 인증하는 데 사용됩니다. 그런 다음 제공된 토큰을 사용하여 컴퓨터 비전 API를 호출합니다.

1. **테넌트 ID**받기:
   ```powershell-interactive
   $context=Get-AzContext
   $context.Tenant.Id
   ```

2. 토큰 받기:
   ```powershell-interactive
   $authContext = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext" -ArgumentList "https://login.windows.net/<TENANT_ID>"
   $secureSecretObject = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.SecureClientSecret" -ArgumentList $SecureStringPassword   
   $clientCredential = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.ClientCredential" -ArgumentList $app.ApplicationId, $secureSecretObject
   $token=$authContext.AcquireTokenAsync("https://cognitiveservices.azure.com/", $clientCredential).Result
   $token
   ```
3. 컴퓨터 비전 API호출:
   ```powershell-interactive
   $url = $account.Endpoint+"vision/v1.0/models"
   $result = Invoke-RestMethod -Uri $url  -Method Get -Headers @{"Authorization"=$token.CreateAuthorizationHeader()} -Verbose
   $result | ConvertTo-Json
   ```

또는 서비스 주체를 인증서로 인증할 수 있습니다. 서비스 주체 외에도 사용자 주체는 다른 AAD 응용 프로그램을 통해 권한을 위임하여 지원됩니다. 이 경우 암호 나 인증서 대신 토큰을 획득할 때 2 단계 인증을 묻는 메시지가 표시됩니다.
