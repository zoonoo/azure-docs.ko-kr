---
title: 음성 서비스에서 전용 끝점을 사용 하는 방법
titleSuffix: Azure Cognitive Services
description: Azure 개인 링크에서 제공 하는 개인 끝점에서 음성 서비스를 사용 하는 방법을 알아봅니다.
services: cognitive-services
author: alexeyo26
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/15/2020
ms.author: alexeyo
ms.openlocfilehash: 61be4b45df94c902c0473b94a6dd83237c72da3c
ms.sourcegitcommit: 0aec60c088f1dcb0f89eaad5faf5f2c815e53bf8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/14/2021
ms.locfileid: "98196125"
---
# <a name="use-speech-services-through-a-private-endpoint"></a>개인 끝점을 통해 Speech Services 사용

[Azure 개인 링크](../../private-link/private-link-overview.md) 를 사용 하면 [개인 끝점](../../private-link/private-endpoint-overview.md)을 사용 하 여 azure에서 서비스에 연결할 수 있습니다. 개인 끝점은 특정 [가상 네트워크](../../virtual-network/virtual-networks-overview.md) 및 서브넷 내 에서만 액세스할 수 있는 개인 IP 주소입니다.

이 문서에서는 Azure Cognitive Services에서 음성 서비스를 사용 하 여 개인 링크 및 개인 끝점을 설정 하 고 사용 하는 방법을 설명 합니다.

> [!NOTE]
> 계속 하기 전에 Cognitive Services에서 [가상 네트워크를 사용 하는 방법](../cognitive-services-virtual-networks.md)을 검토 하세요.

이 문서는 [나중에 개인 끝점을 제거 하는 방법에 대해서도 설명 하지만 여전히 음성 리소스를 사용](#use-a-speech-resource-with-a-custom-domain-name-and-without-private-endpoints)합니다.

## <a name="create-a-custom-domain-name"></a>사용자 지정 도메인 이름 만들기

개인 끝점에는 [Cognitive Services에 대 한 사용자 지정 하위 도메인 이름이](../cognitive-services-custom-subdomains.md)필요 합니다. 다음 지침을 사용 하 여 음성 리소스에 대해 하나를 만듭니다.

> [!WARNING]
> 사용자 지정 도메인 이름이 설정 된 음성 리소스는 음성 서비스와 상호 작용 하는 다른 방법을 사용 합니다. 이러한 두 시나리오 모두에 대해 응용 프로그램 코드를 조정 해야 할 수 있습니다 [. 개인 끝점](#use-a-speech-resource-with-a-custom-domain-name-and-a-private-endpoint-enabled) 은 사용 하도록 설정 되 고 개인 끝점은 [사용 하도록 설정 되어 *있지 않습니다*](#use-a-speech-resource-with-a-custom-domain-name-and-without-private-endpoints).
>
> 사용자 지정 도메인 이름을 사용 하도록 설정 하면 작업을 [되돌릴 수 없습니다](../cognitive-services-custom-subdomains.md#can-i-change-a-custom-domain-name). [지역 이름](../cognitive-services-custom-subdomains.md#is-there-a-list-of-regional-endpoints) 으로 다시 이동 하는 유일한 방법은 새 음성 리소스를 만드는 것입니다.
>
> 음성 리소스에 많은 연결 된 사용자 지정 모델과 [Speech Studio](https://speech.microsoft.com/)를 통해 만든 프로젝트가 있는 경우 프로덕션에 사용 되는 리소스를 수정 하기 전에 테스트 리소스를 사용 하 여 구성을 시도 하는 것이 좋습니다.

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

Azure Portal를 사용 하 여 사용자 지정 도메인 이름을 만들려면 다음 단계를 수행 합니다.

1. [Azure Portal](https://portal.azure.com/)로 이동하여 Azure 계정에 로그인합니다.
1. 필요한 음성 리소스를 선택 합니다.
1. 왼쪽 창의 **리소스 관리** 그룹에서 **네트워킹** 을 선택 합니다.
1. **방화벽 및 가상 네트워크** 탭에서 **사용자 지정 도메인 이름 생성** 을 선택 합니다. 리소스에 대해 고유한 사용자 지정 하위 도메인을 만드는 지침이 포함 된 새 오른쪽 패널이 나타납니다.
1. **사용자 지정 도메인 이름 생성** 패널에서 사용자 지정 도메인 이름을 입력 합니다. 전체 사용자 지정 도메인은와 같습니다 `https://{your custom name}.cognitiveservices.azure.com` . 
    
    사용자 지정 도메인 이름을 만든 후에는 변경할 수 _없습니다_ .
    
    사용자 지정 도메인 이름을 입력 한 후 **저장** 을 선택 합니다.
1. 작업이 완료 되 면 **리소스 관리** 그룹에서 **키 및 끝점** 을 선택 합니다. 리소스의 새 끝점 이름이 다음과 같은 방식으로 시작 되는지 확인 `https://{your custom name}.cognitiveservices.azure.com` 합니다.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

PowerShell을 사용 하 여 사용자 지정 도메인 이름을 만들려면 컴퓨터의 Azure PowerShell 모듈 버전이 5.1.0 이상인 PowerShell 버전 4.x 이상 인지 확인 합니다. 이러한 도구의 버전을 확인 하려면 다음 단계를 수행 합니다.

1. PowerShell 창에서 다음을 입력 합니다.

    `$PSVersionTable`

    `PSVersion`값이 7. x 이상 인지 확인 합니다. PowerShell을 업그레이드 하려면 [다양 한 버전의 powershell을 설치](/powershell/scripting/install/installing-powershell)하는 방법의 지침을 따르세요.

1. PowerShell 창에서 다음을 입력 합니다.

    `Get-Module -ListAvailable Az`

    아무 것도 표시 되지 않거나 해당 Azure PowerShell 모듈이 5.1.0 보다 이전 버전인 경우 [Azure PowerShell 모듈 설치의](/powershell/azure/install-Az-ps) 지침에 따라 업그레이드 합니다.

계속 하기 전에를 실행 `Connect-AzAccount` 하 여 Azure와의 연결을 만듭니다.

## <a name="verify-that-a-custom-domain-name-is-available"></a>사용자 지정 도메인 이름을 사용할 수 있는지 확인

사용 하려는 사용자 지정 도메인을 사용할 수 있는지 여부를 확인 합니다. 다음 코드는 Cognitive Services REST API에서 [도메인 가용성 확인](/rest/api/cognitiveservices/accountmanagement/checkdomainavailability/checkdomainavailability) 작업을 사용 하 여 도메인을 사용할 수 있는지 확인 합니다.

> [!TIP]
> 다음 코드는 Azure Cloud Shell에서 작동 *하지* 않습니다.

```azurepowershell
$subId = "Your Azure subscription Id"
$subdomainName = "custom domain name"

# Select the Azure subscription that contains the Speech resource.
# You can skip this step if your Azure account has only one active subscription.
Set-AzContext -SubscriptionId $subId

# Prepare the OAuth token to use in the request to the Cognitive Services REST API.
$Context = Get-AzContext
$AccessToken = (Get-AzAccessToken -TenantId $Context.Tenant.Id).Token
$token = ConvertTo-SecureString -String $AccessToken -AsPlainText -Force

# Prepare and send the request to the Cognitive Services REST API.
$uri = "https://management.azure.com/subscriptions/" + $subId + `
    "/providers/Microsoft.CognitiveServices/checkDomainAvailability?api-version=2017-04-18"
$body = @{
subdomainName = $subdomainName
type = "Microsoft.CognitiveServices/accounts"
}
$jsonBody = $body | ConvertTo-Json
Invoke-RestMethod -Method Post -Uri $uri -ContentType "application/json" -Authentication Bearer `
    -Token $token -Body $jsonBody | Format-List
```
원하는 이름을 사용할 수 있는 경우 다음과 같은 응답이 표시 됩니다.
```azurepowershell
isSubdomainAvailable : True
reason               :
type                 :
subdomainName        : my-custom-name
```
이름이 이미 사용 되 고 있으면 다음 응답이 표시 됩니다.
```azurepowershell
isSubdomainAvailable : False
reason               : Sub domain name 'my-custom-name' is already used. Please pick a different name.
type                 :
subdomainName        : my-custom-name
```
## <a name="create-your-custom-domain-name"></a>사용자 지정 도메인 이름 만들기

선택한 음성 리소스에 대해 사용자 지정 도메인 이름을 사용 하도록 설정 하려면 [AzCognitiveServicesAccount](/powershell/module/az.cognitiveservices/set-azcognitiveservicesaccount) cmdlet을 사용 합니다.

> [!WARNING]
> 다음 코드가 성공적으로 실행 되 면 음성 리소스에 대 한 사용자 지정 도메인 이름을 만듭니다. 이 이름은 변경할 수 *없습니다* .

```azurepowershell
$resourceGroup = "Resource group name where Speech resource is located"
$speechResourceName = "Your Speech resource name"
$subdomainName = "custom domain name"

# Select the Azure subscription that contains the Speech resource.
# You can skip this step if your Azure account has only one active subscription.
$subId = "Your Azure subscription Id"
Set-AzContext -SubscriptionId $subId

# Set the custom domain name to the selected resource.
# WARNING: THIS CANNOT BE CHANGED OR UNDONE!
Set-AzCognitiveServicesAccount -ResourceGroupName $resourceGroup `
    -Name $speechResourceName -CustomSubdomainName $subdomainName
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

이 섹션에는 최신 버전의 Azure CLI 필요 합니다. Azure Cloud Shell 사용 하는 경우 최신 버전이 이미 설치 되어 있습니다.

## <a name="verify-that-the-custom-domain-name-is-available"></a>사용자 지정 도메인 이름을 사용할 수 있는지 확인 합니다.

사용 하려는 사용자 지정 도메인이 무료 인지 확인 합니다. Cognitive Services REST API에서 [도메인 가용성 확인](/rest/api/cognitiveservices/accountmanagement/checkdomainavailability/checkdomainavailability) 방법을 사용 합니다.

다음 코드 블록을 복사 하 고 원하는 사용자 지정 도메인 이름을 삽입 한 다음 파일에 저장 `subdomain.json` 합니다.

```json
{
    "subdomainName": "custom domain name",
    "type": "Microsoft.CognitiveServices/accounts"
}
```

현재 폴더에 파일을 복사 하거나 Azure Cloud Shell에 업로드 하 고 다음 명령을 실행 합니다. `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` 를 Azure 구독 ID로 바꿉니다.

```azurecli-interactive
az rest --method post --url "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/providers/Microsoft.CognitiveServices/checkDomainAvailability?api-version=2017-04-18" --body @subdomain.json
```
원하는 이름을 사용할 수 있는 경우 다음과 같은 응답이 표시 됩니다.
```azurecli
{
  "isSubdomainAvailable": true,
  "reason": null,
  "subdomainName": "my-custom-name",
  "type": null
}
```

이름이 이미 사용 되 고 있으면 다음 응답이 표시 됩니다.
```azurecli
{
  "isSubdomainAvailable": false,
  "reason": "Sub domain name 'my-custom-name' is already used. Please pick a different name.",
  "subdomainName": "my-custom-name",
  "type": null
}
```
## <a name="enable-a-custom-domain-name"></a>사용자 지정 도메인 이름 사용

선택한 음성 리소스에 대해 사용자 지정 도메인 이름을 사용 하도록 설정 하려면 [az cognitiveservices account account update](/cli/azure/cognitiveservices/account#az_cognitiveservices_account_update) 명령을 사용 합니다.

음성 리소스를 포함 하는 Azure 구독을 선택 합니다. Azure 계정에 활성 구독이 하나만 있는 경우이 단계를 건너뛸 수 있습니다. `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` 를 Azure 구독 ID로 바꿉니다.
```azurecli-interactive
az account set --subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```
사용자 지정 도메인 이름을 선택한 리소스에 설정 합니다. 샘플 매개 변수 값을 실제 값으로 바꾸고 다음 명령을 실행 합니다.

> [!WARNING]
> 다음 명령을 성공적으로 실행 한 후에는 음성 리소스에 대 한 사용자 지정 도메인 이름을 만듭니다. 이 이름은 변경할 수 *없습니다* .

```azurecli
az cognitiveservices account update --name my-speech-resource-name --resource-group my-resource-group-name --custom-domain my-custom-name
```

***

## <a name="enable-private-endpoints"></a>전용 끝점 사용

개인 끝점에 필요한 업데이트를 사용 하 여 가상 네트워크에 연결 된 [개인 DNS 영역](../../dns/private-dns-overview.md) 을 사용 하는 것이 좋습니다. 프로 비전 프로세스 중에 기본적으로 개인 DNS 영역을 만듭니다. 자체 DNS 서버를 사용 하는 경우 DNS 구성을 변경 해야 할 수도 있습니다. 

프로덕션 음성 리소스에 대 한 개인 끝점을 프로 비전 *하기 전에* DNS 전략을 결정 합니다. DNS 변경 (특히 사용자의 DNS 서버를 사용 하는 경우)을 테스트 합니다.

다음 문서 중 하나를 사용 하 여 개인 끝점을 만듭니다. 이러한 문서에서는 웹 앱을 샘플 리소스로 사용 하 여 전용 끝점을 사용 하도록 설정 합니다.

- [Azure Portal을 사용하여 프라이빗 엔드포인트 만들기](../../private-link/create-private-endpoint-portal.md)
- [Azure PowerShell을 사용하여 프라이빗 엔드포인트 만들기](../../private-link/create-private-endpoint-powershell.md)
- [Azure CLI를 사용하여 프라이빗 엔드포인트 만들기](../../private-link/create-private-endpoint-cli.md)

선택한 아티클의 매개 변수 대신 다음 매개 변수를 사용 합니다.

| 설정             | 값                                    |
|---------------------|------------------------------------------|
| 리소스 유형       | **Microsoft.CognitiveServices/accounts** |
| 리소스            | **\<your-speech-resource-name>**         |
| 대상 하위 리소스 | **account**                              |

**전용 끝점에 대 한 DNS:** [Cognitive Services 리소스의 개인 끝점에 대 한 DNS](../cognitive-services-virtual-networks.md#dns-changes-for-private-endpoints)의 일반적인 원리를 검토 합니다. 그런 다음, 다음 섹션에 설명 된 검사를 수행 하 여 DNS 구성이 제대로 작동 하는지 확인 합니다.

### <a name="resolve-dns-from-the-virtual-network"></a>가상 네트워크에서 DNS를 확인 합니다.

이 확인은 *필수* 입니다.

가상 네트워크에서 사용자 지정 DNS 항목을 테스트 하려면 다음 단계를 수행 합니다.

1. 개인 끝점을 연결한 가상 네트워크에 있는 가상 컴퓨터에 로그인 합니다. 
1. Windows 명령 프롬프트 또는 Bash 셸을 열고 `nslookup` 를 실행 한 다음 리소스의 사용자 지정 도메인 이름이 성공적으로 확인 되는지 확인 합니다.

   ```dos
   C:\>nslookup my-private-link-speech.cognitiveservices.azure.com
   Server:  UnKnown
   Address:  168.63.129.16

   Non-authoritative answer:
   Name:    my-private-link-speech.privatelink.cognitiveservices.azure.com
   Address:  172.28.0.10
   Aliases:  my-private-link-speech.cognitiveservices.azure.com
   ```

1. IP 주소가 개인 끝점의 IP 주소와 일치 하는지 확인 합니다.

### <a name="resolve-dns-from-other-networks"></a>다른 네트워크에서 DNS 확인

리소스의 **네트워킹** 섹션에서 **모든 네트워크** 옵션이 나 **선택한 네트워크 및 개인 끝점** 액세스 옵션을 사용 하도록 설정한 경우에만이 확인을 수행 합니다. 

전용 엔드포인트를 사용 하 여 리소스에 액세스할 계획인 경우이 섹션을 건너뛸 수 있습니다.

1. 리소스에 액세스할 수 있는 네트워크에 연결 된 컴퓨터에 로그인 합니다.
2. Windows 명령 프롬프트 또는 Bash 셸을 열고 `nslookup` 를 실행 한 다음 리소스의 사용자 지정 도메인 이름이 성공적으로 확인 되는지 확인 합니다.

   ```dos
   C:\>nslookup my-private-link-speech.cognitiveservices.azure.com
   Server:  UnKnown
   Address:  fe80::1

   Non-authoritative answer:
   Name:    vnetproxyv1-weu-prod.westeurope.cloudapp.azure.com
   Address:  13.69.67.71
   Aliases:  my-private-link-speech.cognitiveservices.azure.com
             my-private-link-speech.privatelink.cognitiveservices.azure.com
             westeurope.prod.vnet.cog.trafficmanager.net
   ```

3. IP 주소가 개인 끝점의 IP 주소와 일치 하는지 확인 합니다.

> [!NOTE]
> 확인 된 IP 주소는 가상 네트워크 프록시 끝점을 가리키며,이 끝점은 네트워크 트래픽을 Cognitive Services 리소스의 개인 끝점으로 디스패치합니다. 이 동작은 사용자 지정 도메인 이름이 있지만 전용 끝점이 *없는* 리소스에 대해 다릅니다. 자세한 내용은 [이 섹션](#dns-configuration) 을 참조 하세요.

## <a name="adjust-existing-applications-and-solutions"></a>기존 응용 프로그램 및 솔루션 조정

사용자 지정 도메인이 설정 된 음성 리소스는 음성 서비스와 상호 작용 하는 다른 방법을 사용 합니다. 개인 끝점을 사용 하거나 사용 하지 않는 사용자 지정 도메인 사용 음성 리소스의 경우에도 마찬가지입니다. 이 섹션의 정보는 두 시나리오에 모두 적용 됩니다.

### <a name="use-a-speech-resource-with-a-custom-domain-name-and-a-private-endpoint-enabled"></a>사용자 지정 도메인 이름 및 개인 끝점이 설정 된 음성 리소스 사용

사용자 지정 도메인 이름 및 개인 끝점이 설정 된 음성 리소스는 음성 서비스와 상호 작용 하는 다른 방법을 사용 합니다. 이 섹션에서는 Speech Service REST Api와 [SPEECH SDK](speech-sdk.md)를 사용 하 여 이러한 리소스를 사용 하는 방법을 설명 합니다.

> [!NOTE]
> 개인 끝점이 없고 사용자 지정 도메인 이름을 사용 하도록 설정 된 음성 리소스도 음성 서비스와 상호 작용 하는 특별 한 방법이 있습니다. 이러한 방식은 개인 끝점 사용 음성 리소스의 시나리오와는 다릅니다. 이러한 리소스가 있는 경우 (예: 전용 끝점을 포함 하는 리소스가 있지만 제거 하기로 결정 한 경우)에는 [사용자 지정 도메인 이름을 사용 하 여 전용 끝점 없이 음성 리소스 사용](#use-a-speech-resource-with-a-custom-domain-name-and-without-private-endpoints)섹션을 참조 하세요.

#### <a name="speech-resource-with-a-custom-domain-name-and-a-private-endpoint-usage-with-the-rest-apis"></a>사용자 지정 도메인 이름 및 개인 끝점을 사용 하는 음성 리소스: REST Api 사용

`my-private-link-speech.cognitiveservices.azure.com`이 섹션에 대 한 샘플 음성 리소스 DNS 이름 (사용자 지정 도메인)으로를 사용 합니다.

음성 서비스에는 [음성 텍스트](rest-speech-to-text.md) 및 [텍스트 음성 변환](rest-text-to-speech.md)에 사용할 수 있는 REST api가 있습니다. 개인 끝점 사용 시나리오에 대 한 다음 정보를 고려 합니다.

음성 텍스트에는 두 개의 REST Api가 있습니다. 각 API는 다른 용도로 사용 되 고, 다른 끝점을 사용 하며, 개인 끝점 사용 시나리오에서 사용 하는 경우 다른 접근 방식이 필요 합니다.

음성 텍스트 REST Api는 다음과 같습니다.
- [일괄 처리](batch-transcription.md) 기록 및 [Custom Speech](custom-speech-overview.md)에 사용 되는 [음성 텍스트 REST API v 3.0](rest-speech-to-text.md#speech-to-text-rest-api-v30) v 3.0은 v2.0 [의 후속 작업](/azure/cognitive-services/speech-service/migrate-v2-to-v3) 입니다.
- 온라인 기록에 사용 되는 [짧은 오디오의 음성 텍스트 REST API](rest-speech-to-text.md#speech-to-text-rest-api-for-short-audio) 

짧은 오디오에 음성 텍스트 REST API를 사용 하 고 개인 끝점 시나리오에서 텍스트 음성 변환 REST API를 사용 하는 것은 동일 합니다. 이 문서의 뒷부분에 설명 된 [SPEECH SDK 케이스](#speech-resource-with-a-custom-domain-name-and-a-private-endpoint-usage-with-the-speech-sdk) 와 동일 합니다. 

음성 텍스트 REST API v 3.0은 다른 끝점 집합을 사용 하므로 개인 끝점 사용 시나리오에 대해 다른 접근 방식이 필요 합니다.

다음 하위 섹션에서는 두 경우 모두에 대해 설명 합니다.

##### <a name="speech-to-text-rest-api-v30"></a>음성 텍스트 REST API v 3.0

일반적으로 음성 리소스는 [Cognitive Services 지역 끝점](../cognitive-services-custom-subdomains.md#is-there-a-list-of-regional-endpoints) 을 사용 하 여 [음성 텍스트 REST API v 3.0](rest-speech-to-text.md#speech-to-text-rest-api-v30)과 통신 합니다. 이러한 리소스에는 다음과 같은 명명 형식이 있습니다. <p/>`{region}.api.cognitive.microsoft.com`.

샘플 요청 URL은 다음과 같습니다.

```http
https://westeurope.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions
```
개인 끝점에 필요한 음성 리소스에 대해 사용자 지정 도메인을 사용 하도록 설정 하면 해당 리소스는 기본 REST API 끝점에 대해 다음 DNS 이름 패턴을 사용 합니다. <p/>`{your custom name}.cognitiveservices.azure.com`.

즉,이 예제에서 REST API 끝점 이름은 다음과 같습니다. <p/>`my-private-link-speech.cognitiveservices.azure.com`.

그리고 샘플 요청 URL을로 변환 해야 합니다.
```http
https://my-private-link-speech.cognitiveservices.azure.com/speechtotext/v3.0/transcriptions
```
이 URL은 가상 네트워크에서 연결 된 개인 끝점 ( [올바른 DNS 확인](#resolve-dns-from-the-virtual-network)제공)을 사용 하 여 연결할 수 있어야 합니다.

음성 리소스에 대해 사용자 지정 도메인 이름을 사용 하도록 설정한 후에는 일반적으로 모든 요청 Url의 호스트 이름을 새로운 사용자 지정 도메인 호스트 이름으로 바꿉니다. 이전 예제의 경로와 같은 요청의 다른 모든 부분은 `/speechtotext/v3.0/transcriptions` 동일 하 게 유지 됩니다.

> [!TIP]
> 일부 고객은 지역 끝점의 DNS 이름 (예: 특정 Azure 지역에 배포 된 음성 리소스에 요청을 보내는)의 지역 부분을 사용 하는 응용 프로그램을 개발 합니다.
>
> 음성 리소스의 사용자 지정 도메인에는 리소스가 배포 되는 지역에 대 한 정보가 포함 되어 *있지 않습니다* . 따라서 앞에서 설명한 응용 프로그램 논리는 작동 *하지* 않으며 변경 해야 합니다.

##### <a name="speech-to-text-rest-api-for-short-audio-and-text-to-speech-rest-api"></a>짧은 오디오 및 텍스트 음성 변환 REST API에 대 한 음성 텍스트 REST API

짧은 오디오 및 [텍스트 음성 변환 REST API](rest-text-to-speech.md) [에 대 한 음성 텍스트 REST API](rest-speech-to-text.md#speech-to-text-rest-api-for-short-audio) 는 두 가지 유형의 끝점을 사용 합니다.
- Cognitive Services REST API와 통신 하 여 권한 부여 토큰을 가져오는 [지역 끝점 Cognitive Services](../cognitive-services-custom-subdomains.md#is-there-a-list-of-regional-endpoints)
- 다른 모든 작업에 대 한 특수 끝점

특수 끝점에 대 한 자세한 설명 및 개인 끝점 사용 음성 리소스에 대 한 URL을 변환 하는 방법에 대 한 자세한 설명은 [이 하위 단원](#general-principles) 에서 Speech SDK 사용에 대 한 정보를 제공 합니다. SDK에 대해 설명 하는 것과 동일한 원칙이 음성 텍스트 REST API v 1.0 및 텍스트 음성 변환 REST API에 적용 됩니다.

이전 단락에서 언급 한 하위 섹션의 자료를 숙지 하 고 다음 예제를 참조 하세요. 이 예제에서는 텍스트 음성 변환 REST API에 대해 설명 합니다. 짧은 오디오에 대 한 음성 텍스트 REST API 사용은 완전히 동일 합니다.

> [!NOTE]
> 개인 끝점 시나리오에서 짧은 오디오에 대해 음성 텍스트 REST API를 사용 하는 경우 헤더를 [통해 전달 된](rest-speech-to-text.md#request-headers) 인증 토큰을 사용 합니다 `Authorization` [](rest-speech-to-text.md#request-headers). 헤더를 통해 특수 끝점에 음성 구독 키를 전달 해도 `Ocp-Apim-Subscription-Key` 작동 *하지* 않으며 401 오류가 생성 됩니다.

**텍스트 음성 변환 REST API 사용 예**

유럽 서부 샘플 Azure 지역으로 사용 하 고 `my-private-link-speech.cognitiveservices.azure.com` 샘플 음성 리소스 DNS 이름 (사용자 지정 도메인)으로 사용 합니다. 이 예제의 사용자 지정 도메인 이름은 `my-private-link-speech.cognitiveservices.azure.com` 유럽 서부 지역에서 만든 음성 리소스에 속합니다.

지역에서 지원 되는 음성 목록을 가져오려면 다음 두 작업을 수행 합니다.

- 권한 부여 토큰을 가져옵니다.
  ```http
  https://westeurope.api.cognitive.microsoft.com/sts/v1.0/issuetoken
  ```
- 토큰을 사용 하 여 음성 목록을 가져옵니다.
  ```http
  https://westeurope.tts.speech.microsoft.com/cognitiveservices/voices/list
  ```
[텍스트 음성 변환 REST API 설명서](rest-text-to-speech.md)의 이전 단계에 대 한 자세한 내용을 참조 하세요.

개인 끝점 사용 음성 리소스의 경우 동일한 작업 순서에 대 한 끝점 Url을 수정 해야 합니다. 동일한 시퀀스는 다음과 같습니다.

- 권한 부여 토큰을 가져옵니다.
  ```http
  https://my-private-link-speech.cognitiveservices.azure.com/v1.0/issuetoken
  ```
  이전 [음성 텍스트 REST API v 3.0](#speech-to-text-rest-api-v30) 하위 섹션의 자세한 설명을 참조 하세요.

- 가져온 토큰을 사용 하 여 음성 목록을 가져옵니다.
  ```http
  https://my-private-link-speech.cognitiveservices.azure.com/tts/cognitiveservices/voices/list
  ```
  Speech SDK의 [일반 원칙](#general-principles) 하위 섹션에서 자세한 설명을 참조 하세요.

#### <a name="speech-resource-with-a-custom-domain-name-and-a-private-endpoint-usage-with-the-speech-sdk"></a>사용자 지정 도메인 이름 및 개인 끝점을 사용 하는 음성 리소스: Speech SDK 사용

사용자 지정 도메인 이름 및 개인 끝점 사용 음성 리소스와 함께 Speech SDK를 사용 하려면 응용 프로그램 코드를 검토 하 고 변경 해야 합니다.

`my-private-link-speech.cognitiveservices.azure.com`이 섹션에 대 한 샘플 음성 리소스 DNS 이름 (사용자 지정 도메인)으로를 사용 합니다.

##### <a name="general-principles"></a>일반 원칙

일반적으로 SDK 시나리오와 텍스트 음성 변환 REST API 시나리오에서 음성 리소스는 다양 한 서비스 제공에 대 한 전용 지역 끝점을 사용 합니다. 이러한 끝점에 대 한 DNS 이름 형식은 다음과 같습니다.

`{region}.{speech service offering}.speech.microsoft.com`

DNS 이름 예는 다음과 같습니다.

`westeurope.stt.speech.microsoft.com`

지역 (DNS 이름의 첫 번째 요소)에 대 한 모든 가능한 값이 [음성 서비스 지원 지역](regions.md)에 나열 됩니다. 다음 표에서는 음성 서비스 제공 (DNS 이름의 두 번째 요소)에 사용할 수 있는 값을 보여 줍니다.

| DNS 이름 값 | 음성 서비스 제공                                    |
|----------------|-------------------------------------------------------------|
| `commands`     | [사용자 지정 명령](custom-commands.md)                       |
| `convai`       | [대화 전사](conversation-transcription.md) |
| `s2s`          | [Speech Translation](speech-translation.md)                 |
| `stt`          | [음성 텍스트 변환](speech-to-text.md)                         |
| `tts`          | [텍스트 음성 변환](text-to-speech.md)                         |
| `voice`        | [Custom Voice](how-to-custom-voice.md)                      |

따라서 앞의 예제 ( `westeurope.stt.speech.microsoft.com` )는 유럽 서부에서 음성 텍스트 끝점을 의미 합니다.

개인 끝점 사용 끝점이 특수 프록시를 통해 음성 서비스와 통신 합니다. 따라서 *끝점 연결 url을 변경 해야* 합니다. 

"표준" 끝점 URL은 다음과 같습니다. <p/>`{region}.{speech service offering}.speech.microsoft.com/{URL path}`

개인 끝점 URL은 다음과 같습니다. <p/>`{your custom name}.cognitiveservices.azure.com/{speech service offering}/{URL path}`

**예 1.** 응용 프로그램은 다음 URL을 사용 하 여 통신 합니다 (미국 영어의 기본 모델을 사용 하는 음성 인식 유럽 서부).

```
wss://westeurope.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1?language=en-US
```

음성 리소스의 사용자 지정 도메인 이름이 인 경우 개인 끝점 사용 시나리오에서 사용 하려면 `my-private-link-speech.cognitiveservices.azure.com` 다음과 같이 URL을 수정 해야 합니다.

```
wss://my-private-link-speech.cognitiveservices.azure.com/stt/speech/recognition/conversation/cognitiveservices/v1?language=en-US
```

세부 정보를 확인 합니다.

- 호스트 이름이 `westeurope.stt.speech.microsoft.com` 사용자 지정 도메인 호스트 이름으로 바뀝니다 `my-private-link-speech.cognitiveservices.azure.com` .
- 원래 DNS 이름 ()의 두 번째 요소는 `stt` URL 경로의 첫 번째 요소가 되며 원래 경로 앞에 옵니다. 따라서 원래 URL은가 됩니다 `/speech/recognition/conversation/cognitiveservices/v1?language=en-US` `/stt/speech/recognition/conversation/cognitiveservices/v1?language=en-US` .

**예 2.** 응용 프로그램은 다음 URL을 사용 하 여 사용자 지정 음성 모델을 사용 하 여 유럽 서부의 음성을 합성 합니다.
```http
https://westeurope.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId=974481cc-b769-4b29-af70-2fb557b897c4
```

다음 URL은 음성 리소스의 사용자 지정 도메인 이름이 인 개인 끝점을 사용 합니다 `my-private-link-speech.cognitiveservices.azure.com` .

```http
https://my-private-link-speech.cognitiveservices.azure.com/voice/cognitiveservices/v1?deploymentId=974481cc-b769-4b29-af70-2fb557b897c4
```

예 1의 동일한 원칙이 적용 되지만이 시간에 키 요소는입니다 `voice` .

##### <a name="modifying-applications"></a>응용 프로그램 수정

코드를 수정 하려면 다음 단계를 따르세요.

1. 응용 프로그램 끝점 URL을 결정 합니다.

   - [응용 프로그램에 대 한 로깅을 사용 하도록 설정](how-to-use-logging.md) 하 고 실행 하 여 작업을 로깅합니다.
   - 로그 파일에서을 검색 `SPEECH-ConnectionUrl` 합니다. 일치 하는 줄에서 `value` 매개 변수는 음성 서비스에 도달 하는 데 사용 되는 응용 프로그램의 전체 URL을 포함 합니다.

   예제:

   ```
   (114917): 41ms SPX_DBG_TRACE_VERBOSE:  property_bag_impl.cpp:138 ISpxPropertyBagImpl::LogPropertyAndValue: this=0x0000028FE4809D78; name='SPEECH-ConnectionUrl'; value='wss://westeurope.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1?traffictype=spx&language=en-US'
   ```

   따라서이 예제에서 사용 하는 응용 프로그램의 URL은 다음과 같습니다.

   ```
   wss://westeurope.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1?language=en-US
   ```

2. `SpeechConfig`전체 끝점 URL을 사용 하 여 인스턴스를 만듭니다.

   1. 이전 [일반 원리](#general-principles) 섹션에 설명 된 대로 방금 결정 한 끝점을 수정 합니다.

   1. 인스턴스를 만드는 방법을 수정 `SpeechConfig` 합니다. 응용 프로그램에서 다음과 같은 항목을 사용 하 고 있을 가능성이 높습니다.
      ```csharp
      var config = SpeechConfig.FromSubscription(subscriptionKey, azureRegion);
      ```
      이는 이전 섹션에서 설명한 호스트 이름 및 URL 변경으로 인해 개인 끝점 사용 음성 리소스에는 적용 되지 않습니다. 개인 끝점 사용 리소스의 키를 사용 하 여 수정 하지 않고 기존 응용 프로그램을 실행 하려고 하면 인증 오류 (401)가 발생 합니다.

      이 작업을 수행 하려면 클래스를 인스턴스화하고 "끝점에서"를 사용 하 여 "초기화"를 사용 하는 방법을 수정 `SpeechConfig` 합니다. 다음과 같은 두 개의 변수가 정의 되어 있다고 가정 합니다.
      - `subscriptionKey` 개인 끝점 사용 음성 리소스의 키를 포함 합니다.
      - `endPoint` 해당 프로그래밍 언어에 필요한 형식을 사용 하 여 전체 *수정* 된 끝점 URL을 포함 합니다. 이 예제에서이 변수에는 다음이 포함 되어야 합니다.
        ```
        wss://my-private-link-speech.cognitiveservices.azure.com/stt/speech/recognition/conversation/cognitiveservices/v1?language=en-US
        ```

      `SpeechConfig` 인스턴스를 만듭니다.
      ```csharp
      var config = SpeechConfig.FromEndpoint(endPoint, subscriptionKey);
      ```
      ```cpp
      auto config = SpeechConfig::FromEndpoint(endPoint, subscriptionKey);
      ```
      ```java
      SpeechConfig config = SpeechConfig.fromEndpoint(endPoint, subscriptionKey);
      ```
      ```python
      import azure.cognitiveservices.speech as speechsdk
      speech_config = speechsdk.SpeechConfig(endpoint=endPoint, subscription=subscriptionKey)
      ```
      ```objectivec
      SPXSpeechConfiguration *speechConfig = [[SPXSpeechConfiguration alloc] initWithEndpoint:endPoint subscription:subscriptionKey];
      ```

> [!TIP]
> 끝점 URI에 지정 된 쿼리 매개 변수는 다른 Api에 의해 설정 된 경우에도 변경 되지 않습니다. 예를 들어 인식 언어가 URI에서 쿼리 매개 변수로 정의 되 `language=en-US` 고 해당 속성을 통해로 설정 된 경우 `ru-RU` uri의 언어 설정이 사용 됩니다. 그러면 유효한 언어가 됩니다 `en-US` .
>
> 끝점 URI에 설정 된 매개 변수는 항상 우선적으로 적용 됩니다. 다른 Api는 끝점 URI에 지정 되지 않은 매개 변수만 재정의할 수 있습니다.

이 수정 후에는 응용 프로그램이 개인 끝점 사용 음성 리소스를 사용 하 여 작업 해야 합니다. 개인 끝점 시나리오를 더욱 원활 하 게 지원 하기 위해 노력 하 고 있습니다.

### <a name="use-a-speech-resource-with-a-custom-domain-name-and-without-private-endpoints"></a>사용자 지정 도메인 이름 및 전용 끝점 없이 음성 리소스 사용

이 문서에서는 음성 리소스에 대해 사용자 지정 도메인을 사용 하도록 설정 하 *는 것을* 여러 번 확인 하지 않습니다. 이러한 리소스는 [지역 끝점 이름을](../cognitive-services-custom-subdomains.md#is-there-a-list-of-regional-endpoints)사용 하는 것과 비교 하 여 음성 서비스와 통신 하는 다른 방법을 사용 합니다.

이 섹션에서는 사용 하도록 설정 된 사용자 지정 도메인 이름을 사용 하는 음성 리소스  를 사용 하는 방법에 대해 설명 [합니다.](speech-sdk.md) 이 리소스는 개인 끝점 시나리오에서 사용 된 후 전용 끝점이 삭제 된 것일 수 있습니다.

#### <a name="dns-configuration"></a>DNS 구성

개인 끝점 사용 음성 리소스의 사용자 지정 도메인 DNS 이름이 [공용 네트워크에서 확인](#resolve-dns-from-other-networks)되는 방법을 유념 하세요. 이 경우 확인 된 IP 주소는 가상 네트워크에 대 한 프록시 끝점을 가리킵니다. 이 끝점은 개인 끝점 사용 Cognitive Services 리소스에 대 한 네트워크 트래픽을 발송 하는 데 사용 됩니다.

그러나 *모든* 리소스 개인 끝점을 제거 하는 경우 (또는 사용자 지정 도메인 이름을 사용 하도록 설정한 직후), 음성 리소스의 CNAME 레코드가 다시 프로 비전 됩니다. 이제 해당 [Cognitive Services 지역 끝점](../cognitive-services-custom-subdomains.md#is-there-a-list-of-regional-endpoints)의 IP 주소를 가리킵니다.

따라서 명령의 출력은 `nslookup` 다음과 같습니다.
```dos
C:\>nslookup my-private-link-speech.cognitiveservices.azure.com
Server:  UnKnown
Address:  fe80::1

Non-authoritative answer:
Name:    apimgmthskquihpkz6d90kmhvnabrx3ms3pdubscpdfk1tsx3a.cloudapp.net
Address:  13.93.122.1
Aliases:  my-private-link-speech.cognitiveservices.azure.com
          westeurope.api.cognitive.microsoft.com
          cognitiveweprod.trafficmanager.net
          cognitiveweprod.azure-api.net
          apimgmttmdjylckcx6clmh2isu2wr38uqzm63s8n4ub2y3e6xs.trafficmanager.net
          cognitiveweprod-westeurope-01.regional.azure-api.net
```
[이 섹션](#resolve-dns-from-other-networks)의 출력과 비교 합니다.

#### <a name="speech-resource-with-a-custom-domain-name-and-without-private-endpoints-usage-with-the-rest-apis"></a>사용자 지정 도메인 이름 및 전용 끝점이 없는 Speech 리소스: REST Api 사용

##### <a name="speech-to-text-rest-api-v30"></a>음성 텍스트 REST API v 3.0

음성 텍스트 REST API v 3.0 사용은 [개인 끝점 사용 음성 리소스](#speech-to-text-rest-api-v30)의 경우와 완전히 동일 합니다.

##### <a name="speech-to-text-rest-api-for-short-audio-and-text-to-speech-rest-api"></a>짧은 오디오 및 텍스트 음성 변환 REST API에 대 한 음성 텍스트 REST API

이 경우 짧은 오디오의 경우 음성 텍스트 REST API를 사용 하 고 텍스트 음성 변환 REST API를 사용 하는 데는 일반적인 경우와는 차이가 없으며 짧은 오디오에 대 한 음성 텍스트 REST API에 대 한 한 가지 예외가 있습니다. 다음 참고를 참조 하세요. 짧은 오디오 및 [텍스트 음성 변환 REST API](rest-text-to-speech.md) 설명서 [에 대 한 음성 텍스트 REST API](rest-speech-to-text.md#speech-to-text-rest-api-for-short-audio) 설명 된 대로 두 api를 모두 사용 해야 합니다.

> [!NOTE]
> 사용자 지정 도메인 시나리오에서 짧은 오디오에 음성 텍스트 REST API를 사용 하는 경우 헤더를 [통해 전달 된](rest-speech-to-text.md#request-headers) 인증 토큰을 사용 합니다 `Authorization` [](rest-speech-to-text.md#request-headers). 헤더를 통해 특수 끝점에 음성 구독 키를 전달 해도 `Ocp-Apim-Subscription-Key` 작동 *하지* 않으며 401 오류가 생성 됩니다.

#### <a name="speech-resource-with-a-custom-domain-name-and-without-private-endpoints-usage-with-the-speech-sdk"></a>사용자 지정 도메인 이름 및 개인 끝점이 없는 음성 리소스: Speech SDK 사용

개인 끝점이 *없는* 사용자 지정 도메인 사용 음성 리소스와 함께 speech SDK를 사용 하려면 응용 프로그램 코드를 검토 하 고 변경 해야 합니다. 이러한 변경 내용은 [개인 끝점 사용 음성 리소스](#speech-resource-with-a-custom-domain-name-and-a-private-endpoint-usage-with-the-speech-sdk)의 경우와 다릅니다. 개인 끝점 및 사용자 지정 도메인 시나리오를 더욱 원활 하 게 지원 하기 위해 노력 하 고 있습니다.

`my-private-link-speech.cognitiveservices.azure.com`이 섹션에 대 한 샘플 음성 리소스 DNS 이름 (사용자 지정 도메인)으로를 사용 합니다.

[개인 끝점 사용 음성 리소스](#speech-resource-with-a-custom-domain-name-and-a-private-endpoint-usage-with-the-speech-sdk)섹션에서 끝점 URL을 확인 하 고, 수정 하 고, 클래스 인스턴스의 "끝점에서 끝점"/"초기화를 사용 하 여 작동 하도록 하는 방법에 대해 설명 했습니다 `SpeechConfig` .

그러나 모든 개인 끝점을 제거한 후에도 동일한 응용 프로그램을 실행 하려고 하면 (해당 DNS 레코드 다시 프로 비전 시간을 허용) 내부 서비스 오류 (404)가 발생 합니다. 그 이유는 [DNS 레코드가](#dns-configuration) 이제 가상 네트워크 프록시 대신 지역 Cognitive Services 끝점을 가리키지만,와 같은 URL 경로를 찾을 수 없기 때문입니다 `/stt/speech/recognition/conversation/cognitiveservices/v1?language=en-US` .

응용 프로그램을 다음 코드의 스타일로의 표준 인스턴스화에 롤백하는 경우 `SpeechConfig` 응용 프로그램은 인증 오류 (401)와 함께 종료 됩니다.

```csharp
var config = SpeechConfig.FromSubscription(subscriptionKey, azureRegion);
```

##### <a name="modifying-applications"></a>응용 프로그램 수정

응용 프로그램이 개인 끝점이 없는 사용자 지정 도메인 이름으로 음성 리소스를 사용 하도록 하려면 다음 단계를 수행 합니다.

1. Cognitive Services REST API에서 권한 부여 토큰을 요청 합니다. [이 문서](../authentication.md#authenticate-with-an-authentication-token) 에서는 토큰을 가져오는 방법을 보여 줍니다.

   끝점 URL에 사용자 지정 도메인 이름을 사용 합니다. 이 예제에서 URL은 다음과 같습니다.
   ```http
   https://my-private-link-speech.cognitiveservices.azure.com/sts/v1.0/issueToken
   ```
   > [!TIP]
   > 이 URL은 Azure Portal에서 찾을 수 있습니다. 음성 리소스 페이지의 **리소스 관리** 그룹에서 **키 및 끝점** 을 선택 합니다.

1. `SpeechConfig`이전 섹션에서 얻은 권한 부여 토큰을 사용 하 여 인스턴스를 만듭니다. 다음 변수가 정의 되어 있다고 가정 합니다.

   - `token`: 이전 섹션에서 얻은 권한 부여 토큰
   - `azureRegion`: 음성 리소스 [영역의](regions.md) 이름 (예: `westeurope` )
   - `outError`: ( [목표 C](/objectivec/cognitive-services/speech/spxspeechconfiguration#initwithauthorizationtokenregionerror) 사례에만 해당)

   `SpeechConfig`다음과 같이 인스턴스를 만듭니다.

   ```csharp
   var config = SpeechConfig.FromAuthorizationToken(token, azureRegion);
   ```
   ```cpp
   auto config = SpeechConfig::FromAuthorizationToken(token, azureRegion);
   ```
   ```java
   SpeechConfig config = SpeechConfig.fromAuthorizationToken(token, azureRegion);
   ```
   ```python
   import azure.cognitiveservices.speech as speechsdk
   speech_config = speechsdk.SpeechConfig(auth_token=token, region=azureRegion)
   ```
   ```objectivec
   SPXSpeechConfiguration *speechConfig = [[SPXSpeechConfiguration alloc] initWithAuthorizationToken:token region:azureRegion error:outError];
   ```
> [!NOTE]
> 호출자는 인증 토큰이 유효한 지 확인 해야 합니다. 권한 부여 토큰이 만료 되기 전에 호출자는 새 유효한 토큰으로이 setter를 호출 하 여 새로 고쳐야 합니다. 새 인식기 또는 신시사이저를 만들 때 구성 값이 복사 되기 때문에 새 토큰 값은 이미 생성 된 인식자 또는 synthesizers에는 적용 되지 않습니다.
>
> 이러한 경우 토큰을 새로 고치기 위해 해당 인식기 또는 신시사이저의 권한 부여 토큰을 설정 합니다. 토큰을 새로 고치지 않으면 인식기 또는 신시사이저가 작동 하는 동안 오류가 발생 합니다.

이 수정 후 응용 프로그램은 전용 끝점이 없는 사용자 지정 도메인 이름을 사용 하는 음성 리소스를 사용 하 여 작업 해야 합니다.

## <a name="pricing"></a>가격 책정

가격 책정에 대한 자세한 내용은 [Azure Private Link 가격 책정](https://azure.microsoft.com/pricing/details/private-link)을 참조하세요.

## <a name="learn-more"></a>자세한 정보

* [Azure Private Link](../../private-link/private-link-overview.md)
* [Speech SDK](speech-sdk.md)
* [음성 텍스트 REST API](rest-speech-to-text.md)
* [텍스트 음성 변환 REST API](rest-text-to-speech.md)
