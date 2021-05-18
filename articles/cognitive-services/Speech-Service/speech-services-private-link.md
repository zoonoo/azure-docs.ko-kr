---
title: Speech Services에서 프라이빗 엔드포인트를 사용하는 방법
titleSuffix: Azure Cognitive Services
description: Azure Private Link에서 제공하는 프라이빗 엔드포인트에서 Speech Services를 사용하는 방법을 알아봅니다.
services: cognitive-services
author: alexeyo26
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/04/2021
ms.author: alexeyo
ms.openlocfilehash: 6971c6f0959135c7de1f41bcd49adde514f87941
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105625486"
---
# <a name="use-speech-services-through-a-private-endpoint"></a>프라이빗 엔드포인트를 통해 Speech Services 사용

[Azure Private Link](../../private-link/private-link-overview.md)를 사용하면 [프라이빗 엔드포인트](../../private-link/private-endpoint-overview.md)를 사용하여 Azure의 서비스에 연결할 수 있습니다. 프라이빗 엔드포인트는 특정 [가상 네트워크](../../virtual-network/virtual-networks-overview.md) 및 서브넷 내에서만 액세스할 수 있는 개인 IP 주소입니다.

이 문서에서는 Azure Cognitive Services에서 Speech Services를 사용하여 Private Link 및 프라이빗 엔드포인트를 설정하고 사용하는 방법을 설명합니다.
그런 다음 이 문서에서는 나중에 프라이빗 엔드포인트를 제거하지만 여전히 Speech 리소스를 사용하는 방법을 설명합니다.

> [!NOTE]
> 계속하기 전에 [Cognitive Services에서 가상 네트워크를 사용하는 방법](../cognitive-services-virtual-networks.md)을 검토합니다.



## <a name="create-a-custom-domain-name"></a>사용자 지정 도메인 이름 만들기

프라이빗 엔드포인트에는 [Cognitive Services에 대한 사용자 지정 하위 도메인 이름](../cognitive-services-custom-subdomains.md)이 필요합니다. 다음 지침에 따라 Speech 리소스에 대해 하나를 만듭니다.

> [!WARNING]
> 사용자 지정 도메인 이름을 사용하는 Speech 리소스는 다른 방식으로 Speech Services와 상호 작용합니다.
> 프라이빗 엔드포인트를 사용하는 Speech 리소스를 사용하도록 또는 프라이빗 엔드포인트를 _사용하지 않는_ Speech 리소스를 사용하도록 애플리케이션 코드를 조정해야 할 수 있습니다.
> 사용자 지정 도메인 이름으로의 전환은 취소할 수 _없으므로_ 두 시나리오가 모두 필요할 수 있습니다.
>
> 사용자 지정 도메인 이름을 설정하면 작업을 [되돌릴 수 없습니다](../cognitive-services-custom-subdomains.md#can-i-change-a-custom-domain-name). [지역 이름](../cognitive-services-custom-subdomains.md#is-there-a-list-of-regional-endpoints)으로 되돌리는 유일한 방법은 새 음성 리소스를 만드는 것입니다.
>
> Speech 리소스에 [Speech Studio](https://speech.microsoft.com/)를 통해 생성된 많은 관련 사용자 지정 모델 및 프로젝트가 있는 경우 프로덕션에 사용되는 리소스를 수정하기 전에 테스트 리소스로 구성을 시도하는 것이 좋습니다.

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

Azure Portal을 사용하여 사용자 지정 도메인 이름을 만들려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com/)로 이동하여 Azure 계정에 로그인합니다.
1. 필요한 Speech 리소스를 선택합니다.
1. 왼쪽 창의 **리소스 관리** 그룹에서 **네트워킹** 을 선택합니다.
1. **방화벽 및 가상 네트워크** 탭에서 **사용자 지정 도메인 이름 생성** 을 선택합니다. 리소스에 대한 고유한 사용자 지정 하위 도메인을 만드는 지침이 포함된 새 패널이 오른쪽에 나타납니다.
1. **사용자 지정 도메인 이름 생성** 패널에서 사용자 지정 도메인 이름을 입력합니다. 전체 사용자 지정 도메인의 형식은 다음과 같습니다. `https://{your custom name}.cognitiveservices.azure.com` 
    
    사용자 지정 도메인 이름을 만든 후에는 변경할 수 _없습니다_.
    
    사용자 지정 도메인 이름을 입력한 후 **저장** 을 선택합니다.
1. 작업이 완료되면 **리소스 관리** 그룹에서 **키 및 엔드포인트** 를 선택합니다. 리소스의 새 엔드포인트 이름이 `https://{your custom name}.cognitiveservices.azure.com` 형식으로 시작하는지 확인합니다.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

PowerShell을 사용하여 사용자 지정 도메인 이름을 만들려면 컴퓨터에 Azure PowerShell 모듈 버전 5.1.0 이상이 포함된 PowerShell 버전 7.x 이상이 있는지 확인합니다. 이러한 도구의 버전을 확인하려면 다음 단계를 수행합니다.

1. PowerShell 창에서 다음을 입력합니다.

    `$PSVersionTable`

    `PSVersion` 값이 7.x 이상인지 확인합니다. PowerShell을 업그레이드하려면 [다양한 버전의 PowerShell 설치](/powershell/scripting/install/installing-powershell) 지침을 따르세요.

1. PowerShell 창에서 다음을 입력합니다.

    `Get-Module -ListAvailable Az`

    아무 것도 표시되지 않거나 해당 버전의 Azure PowerShell 모듈이 5.1.0 이전인 경우 [Azure PowerShell 모듈 설치](/powershell/azure/install-Az-ps)의 지침에 따라 업그레이드합니다.

계속하기 전에 `Connect-AzAccount`를 실행하여 Azure와의 연결을 만듭니다.

## <a name="verify-that-a-custom-domain-name-is-available"></a>사용자 지정 도메인 이름을 사용할 수 있는지 확인

사용하려는 사용자 지정 도메인을 사용할 수 있는지 확인합니다. 다음 코드는 Cognitive Services REST API에서 [도메인 가용성 확인](/rest/api/cognitiveservices/accountmanagement/checkdomainavailability/checkdomainavailability) 작업을 사용하여 도메인을 사용할 수 있는지 확인합니다.

> [!TIP]
> 다음 코드는 Azure Cloud Shell에서 작동하지 *않습니다*.

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
원하는 이름을 사용할 수 있는 경우 다음과 같은 응답이 표시됩니다.
```azurepowershell
isSubdomainAvailable : True
reason               :
type                 :
subdomainName        : my-custom-name
```
이름이 이미 사용 중인 경우 다음 응답이 표시됩니다.
```azurepowershell
isSubdomainAvailable : False
reason               : Sub domain name 'my-custom-name' is already used. Please pick a different name.
type                 :
subdomainName        : my-custom-name
```
## <a name="create-your-custom-domain-name"></a>사용자 지정 도메인 이름 만들기

선택한 Speech 리소스에 대해 사용자 지정 도메인 이름을 설정하려면 [Set-AzCognitiveServicesAccount](/powershell/module/az.cognitiveservices/set-azcognitiveservicesaccount) cmdlet을 사용합니다.

> [!WARNING]
> 다음 코드가 성공적으로 실행되면 Speech 리소스에 대한 사용자 지정 도메인 이름을 생성합니다. 이 이름은 변경할 수 *없습니다*.

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

이 섹션에는 최신 버전의 Azure CLI가 필요합니다. Azure Cloud Shell을 사용하는 경우 최신 버전이 이미 설치되어 있습니다.

## <a name="verify-that-the-custom-domain-name-is-available"></a>사용자 지정 도메인 이름을 사용할 수 있는지 확인

사용하려는 사용자 지정 도메인을 사용할 수 있는지 확인합니다. Cognitive Services REST API에서 [도메인 가용성 확인](/rest/api/cognitiveservices/accountmanagement/checkdomainavailability/checkdomainavailability) 메서드를 사용합니다.

다음 코드 블록을 복사하고 원하는 사용자 지정 도메인 이름을 삽입한 후 `subdomain.json` 파일에 저장합니다.

```json
{
    "subdomainName": "custom domain name",
    "type": "Microsoft.CognitiveServices/accounts"
}
```

파일을 현재 폴더에 복사하거나 Azure Cloud Shell에 업로드하고 다음 명령을 실행합니다. `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` 를 Azure 구독 ID로 바꿉니다.

```azurecli-interactive
az rest --method post --url "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/providers/Microsoft.CognitiveServices/checkDomainAvailability?api-version=2017-04-18" --body @subdomain.json
```
원하는 이름을 사용할 수 있는 경우 다음과 같은 응답이 표시됩니다.
```azurecli
{
  "isSubdomainAvailable": true,
  "reason": null,
  "subdomainName": "my-custom-name",
  "type": null
}
```

이름이 이미 사용 중인 경우 다음 응답이 표시됩니다.
```azurecli
{
  "isSubdomainAvailable": false,
  "reason": "Sub domain name 'my-custom-name' is already used. Please pick a different name.",
  "subdomainName": "my-custom-name",
  "type": null
}
```
## <a name="turn-on-a-custom-domain-name"></a>사용자 지정 도메인 이름 설정

선택한 Speech 리소스에 사용자 지정 도메인 이름을 사용하려면 [az cognitiveservices account update](/cli/azure/cognitiveservices/account#az_cognitiveservices_account_update) 명령을 사용합니다.

Speech 리소스가 포함된 Azure 구독을 선택합니다. Azure 계정에 활성 구독이 하나만 있는 경우 이 단계를 건너뛸 수 있습니다. `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` 를 Azure 구독 ID로 바꿉니다.
```azurecli-interactive
az account set --subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```
사용자 지정 도메인 이름을 선택한 리소스로 설정합니다. 샘플 매개 변수 값을 실제 값으로 바꾸고 다음 명령을 실행합니다.

> [!WARNING]
> 다음 명령을 성공적으로 실행한 후 Speech 리소스에 대한 사용자 지정 도메인 이름을 생성합니다. 이 이름은 변경할 수 *없습니다*.

```azurecli
az cognitiveservices account update --name my-speech-resource-name --resource-group my-resource-group-name --custom-domain my-custom-name
```

***

## <a name="turn-on-private-endpoints"></a>프라이빗 엔드포인트 설정

프라이빗 엔드포인트에 필요한 업데이트와 함께 가상 네트워크에 연결된 [프라이빗 DNS 영역](../../dns/private-dns-overview.md)을 사용하는 것이 좋습니다. 프로비저닝 프로세스 중에 프라이빗 DNS 영역을 만들 수 있습니다. 자체 DNS 서버를 사용하는 경우 DNS 구성을 변경해야 할 수도 있습니다.

프로덕션 Speech 리소스에 대한 프라이빗 엔드포인트를 프로비저닝하기 *전* 에 DNS 전략을 결정합니다. 특히 자체 DNS 서버를 사용하는 경우 DNS 변경 사항을 테스트합니다.

다음 문서 중 하나를 사용하여 프라이빗 엔드포인트를 만듭니다.
이러한 문서에서는 프라이빗 엔드포인트를 통해 사용할 수 있도록 웹앱을 샘플 리소스로 사용합니다.

- [Azure Portal을 사용하여 프라이빗 엔드포인트 만들기](../../private-link/create-private-endpoint-portal.md)
- [Azure PowerShell을 사용하여 프라이빗 엔드포인트 만들기](../../private-link/create-private-endpoint-powershell.md)
- [Azure CLI를 사용하여 프라이빗 엔드포인트 만들기](../../private-link/create-private-endpoint-cli.md)

선택한 문서의 매개 변수 대신 다음 매개 변수를 사용합니다.

| 설정             | 값                                    |
|---------------------|------------------------------------------|
| 리소스 종류       | **Microsoft.CognitiveServices/accounts** |
| 리소스            | **\<your-speech-resource-name>**         |
| 대상 하위 리소스 | **account**                              |

**프라이빗 엔드포인트를 위한 DNS:** [Cognitive Services 리소스의 프라이빗 엔드포인트를 위한 DNS](../cognitive-services-virtual-networks.md#dns-changes-for-private-endpoints)의 일반 원칙을 검토합니다. 그런 다음 아래 섹션에 설명된 검사를 수행하여 DNS 구성이 올바르게 작동하는지 확인합니다.

### <a name="resolve-dns-from-the-virtual-network"></a>가상 네트워크에서 DNS 확인

이 확인은 *필수* 입니다.

다음 단계에 따라 가상 네트워크에서 사용자 지정 DNS 항목을 테스트합니다.

1. 프라이빗 엔드포인트를 연결한 가상 네트워크에 있는 가상 머신에 로그인합니다. 
1. Windows 명령 프롬프트 또는 Bash 셸을 열고 `nslookup`을 실행하여 리소스의 사용자 지정 도메인 이름이 성공적으로 확인되는지 봅니다.

   ```dos
   C:\>nslookup my-private-link-speech.cognitiveservices.azure.com
   Server:  UnKnown
   Address:  168.63.129.16

   Non-authoritative answer:
   Name:    my-private-link-speech.privatelink.cognitiveservices.azure.com
   Address:  172.28.0.10
   Aliases:  my-private-link-speech.cognitiveservices.azure.com
   ```

1. IP 주소가 프라이빗 엔드포인트의 IP 주소와 일치하는지 확인합니다.

### <a name="resolve-dns-from-other-networks"></a>다른 네트워크에서 DNS 확인

리소스의 **네트워킹** 섹션에서 **모든 네트워크** 옵션 또는 **선택한 네트워크 및 프라이빗 엔드포인트** 액세스 옵션을 설정한 경우에만 이 확인을 수행합니다. 

프라이빗 엔드포인트만 사용하여 리소스에 액세스하려는 경우 이 섹션을 건너뛸 수 있습니다.

1. 리소스에 액세스할 수 있는 네트워크에 연결된 컴퓨터에 로그인합니다.
2. Windows 명령 프롬프트 또는 Bash 셸을 열고 `nslookup`을 실행하여 리소스의 사용자 지정 도메인 이름이 성공적으로 확인되는지 봅니다.

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

> [!NOTE]
> 확인된 IP 주소는 네트워크 트래픽을 Cognitive Services 리소스의 프라이빗 엔드포인트로 보내는 가상 네트워크 프록시 엔드포인트를 가리킵니다. 사용자 지정 도메인 이름은 사용하지만 프라이빗 엔드포인트는 *사용하지 않는* 리소스의 경우 동작이 다릅니다. 자세한 내용은 [이 섹션](#dns-configuration)을 참조하세요.

## <a name="adjust-an-application-to-use-a-speech-resource-with-a-private-endpoint"></a>프라이빗 엔드포인트를 사용하는 Speech 리소스를 사용하도록 애플리케이션 조정

사용자 지정 도메인이 있는 Speech 리소스는 다른 방식으로 Speech Services와 상호 작용합니다. 이는 프라이빗 엔드포인트 사용 여부에 관계없이 custom-domain-enabled Speech 리소스에 적용됩니다. 이 섹션의 정보는 두 시나리오에 모두 적용됩니다.

이 섹션의 지침에 따라 사용자 지정 도메인 이름 및 프라이빗 엔드포인트가 설정된 Speech 리소스를 사용하도록 기존 애플리케이션 및 솔루션을 조정합니다.

사용자 지정 도메인 이름과 프라이빗 엔드포인트가 설정된 Speech 리소스는 Speech Services와 상호 작용하는 데 다른 방법을 사용합니다. 이 섹션에서는 이러한 리소스를 Speech Services REST API 및 [Speech SDK](speech-sdk.md)와 함께 사용하는 방법을 설명합니다.

> [!NOTE]
> 사용자 지정 도메인 이름은 사용하고 프라이빗 엔드포인트는 사용하지 않는 Speech 리소스도 특별한 방법을 통해 Speech Services와 상호 작용할 수 있습니다.
> 이 방법은 프라이빗 엔드포인트를 사용하는 Speech 리소스의 시나리오와 다릅니다. 나중에 프라이빗 엔드포인트를 제거할 수도 있으므로 이 방법을 알아두는 것이 좋습니다.
> 이 문서 뒷부분의 _프라이빗 엔드포인트를 사용하지 않는 Speech 리소스를 사용하도록 애플리케이션 조정_ 을 참조하세요.

### <a name="speech-resource-with-a-custom-domain-name-and-a-private-endpoint-usage-with-the-rest-apis"></a>사용자 지정 도메인 이름 및 프라이빗 엔드포인트를 사용하는 Speech 리소스: REST API 사용

이 섹션에서는 `my-private-link-speech.cognitiveservices.azure.com`을 샘플 Speech 리소스 DNS 이름(사용자 지정 도메인)으로 사용합니다.

Speech Services에는 [Speech-to-text](rest-speech-to-text.md) 및 [Text-to-speech](rest-text-to-speech.md)용 REST API가 있습니다. private-endpoint-enabled 시나리오에 대해서는 다음 정보를 고려합니다.

Speech-to-text에는 두 개의 REST API가 있습니다. 각 API는 서로 다른 용도로 사용되고 서로 다른 엔드포인트를 사용하므로 private-endpoint-enabled 시나리오에서 사용할 때 다른 접근 방식이 필요합니다.

Speech-to-text REST API는 다음과 같습니다.
- [Speech-to-text REST API v3.0](rest-speech-to-text.md#speech-to-text-rest-api-v30) - [Batch Transcription](batch-transcription.md) 및 [Custom Speech](custom-speech-overview.md)에 사용됩니다. v3.0은 [v2.0의 후속 버전](./migrate-v2-to-v3.md)입니다.
- [짧은 오디오용 Speech-to-text REST API](rest-speech-to-text.md#speech-to-text-rest-api-for-short-audio) - 온라인 전사에 사용됩니다. 

프라이빗 엔드포인트 시나리오에서의 짧은 오디오용 Speech-to-text REST API와 Text-to-speech REST API 사용은 동일합니다. 이 문서의 뒷부분에서 설명하는 [Speech SDK 사례](#speech-resource-with-a-custom-domain-name-and-a-private-endpoint-usage-with-the-speech-sdk)와 동일합니다. 

Speech-to-text REST API v3.0은 다른 엔드포인트 집합을 사용하므로 private-endpoint-enabled 시나리오에 대해 다른 접근 방식이 필요합니다.

다음 하위 섹션에서는 두 경우 모두에 대해 설명합니다.

#### <a name="speech-to-text-rest-api-v30"></a>Speech-to-text REST API v3.0

일반적으로 Speech 리소스는 [Cognitive Services 지역 엔드포인트](../cognitive-services-custom-subdomains.md#is-there-a-list-of-regional-endpoints)를 사용하여 [Speech-to-text REST API v3.0](rest-speech-to-text.md#speech-to-text-rest-api-v30)과 통신합니다. 이러한 리소스에는 다음과 같은 이름 지정 형식이 있습니다. <p/>`{region}.api.cognitive.microsoft.com`.

다음은 샘플 요청 URL입니다.

```http
https://westeurope.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions
```

> [!NOTE]
> Azure Government 및 Azure 중국 엔드포인트는 [이 문서](sovereign-clouds.md)를 참조하세요.

Speech 리소스(프라이빗 엔드포인트에 필요)에 대해 사용자 지정 도메인을 설정한 경우 해당 리소스는 기본 REST API 엔드포인트에 다음과 같은 DNS 이름 패턴을 사용합니다. <p/>`{your custom name}.cognitiveservices.azure.com`

즉, 이 예에서 REST API 엔드포인트 이름은 다음과 같습니다. <p/>`my-private-link-speech.cognitiveservices.azure.com`

그리고 샘플 요청 URL을 다음으로 변환해야 합니다.
```http
https://my-private-link-speech.cognitiveservices.azure.com/speechtotext/v3.0/transcriptions
```
이 URL은 프라이빗 엔드포인트가 연결된 가상 네트워크에서 도달할 수 있어야 합니다([올바른 DNS 확인](#resolve-dns-from-the-virtual-network) 제공).

Speech 리소스에 대해 사용자 지정 도메인 이름을 설정한 후 일반적으로 모든 요청 URL의 호스트 이름을 새 사용자 지정 도메인 호스트 이름으로 바꿉니다. 요청의 다른 모든 부분(이전 예의 경로 `/speechtotext/v3.0/transcriptions`와 같음)은 동일하게 유지됩니다.

> [!TIP]
> 일부 고객은 지역 엔드포인트 DNS 이름의 지역 부분을 사용하는 애플리케이션을 개발합니다(예: 특정 Azure 지역에 배포된 Speech 리소스에 요청을 보내기 위해).
>
> Speech 리소스의 사용자 지정 도메인에는 리소스가 배포된 지역에 대한 정보가 *없습니다*. 따라서 앞에서 설명한 애플리케이션 논리가 작동하지 *않으므로* 변경해야 합니다.

#### <a name="speech-to-text-rest-api-for-short-audio-and-text-to-speech-rest-api"></a>짧은 오디오용 Speech-to-text REST API 및 Text-to-speech REST API

[짧은 오디오용 Speech-to-text REST API](rest-speech-to-text.md#speech-to-text-rest-api-for-short-audio) 및 [Text-to-speech REST API](rest-text-to-speech.md)는 두 가지 유형의 엔드포인트를 사용합니다.
- Cognitive Services REST API와 통신하여 권한 부여 토큰을 얻기 위한 [Cognitive Services 지역 엔드포인트](../cognitive-services-custom-subdomains.md#is-there-a-list-of-regional-endpoints)
- 다른 모든 작업을 위한 특수 엔드포인트

> [!NOTE]
> Azure Government 및 Azure 중국 엔드포인트는 [이 문서](sovereign-clouds.md)를 참조하세요.

특수 엔드포인트에 대한 자세한 설명과 private-endpoint-enabled Speech 리소스를 위해 URL을 변환하는 방법은 [이 하위 섹션](#construct-endpoint-url)에서 Speech SDK 사용에 대한 내용을 참조하세요. 짧은 오디오용 Speech-to-text REST API 및 Text-to-speech REST API에는 SDK에 대해 설명된 동일한 원칙이 적용됩니다.

이전 단락에서 언급한 하위 섹션의 자료를 숙지하고 다음 예를 참조하세요. 이 예에서는 Text-to-speech REST API에 대해 설명합니다. 짧은 오디오용 Speech-to-text REST API 사용 방법도 완전히 동일합니다.

> [!NOTE]
> 짧은 오디오용 Speech-to-text REST API를 사용하고 프라이빗 엔드포인트 시나리오에서 Text-to-speech REST API를 사용하는 경우 `Ocp-Apim-Subscription-Key` 헤더를 통해 전달된 구독 키를 사용합니다. (자세한 내용은 [짧은 오디오용 Speech-to-text REST API](rest-speech-to-text.md#request-headers) 및 [Text-to-speech REST API](rest-text-to-speech.md#request-headers) 참조)
>
> 권한 부여 토큰을 사용하고 `Authorization` 헤더를 통해 특수 엔드포인트로 전달하는 것은 Speech 리소스의 **네트워킹** 섹션에서 **모든 네트워크** 액세스 옵션을 설정한 경우에 *만* 작동합니다. 다른 경우에 권한 부여 토큰을 얻으려고 하면 `Forbidden` 또는 `BadRequest` 오류가 발생합니다.

**Text-to-speech REST API 사용 예**

서유럽을 샘플 Azure 지역으로 사용하고 `my-private-link-speech.cognitiveservices.azure.com`을 샘플 Speech 리소스 DNS 이름(사용자 지정 도메인)으로 사용합니다. 이 예에서 사용자 지정 도메인 이름 `my-private-link-speech.cognitiveservices.azure.com`은 서유럽 지역에서 생성된 Speech 리소스에 속합니다.

해당 지역에서 지원되는 음성 목록을 가져오려면 다음 요청을 수행합니다.

```http
https://westeurope.tts.speech.microsoft.com/cognitiveservices/voices/list
```
자세한 내용은 [Text-to-speech REST API 설명서](rest-text-to-speech.md)를 참조하세요.

private-endpoint-enabled Speech 리소스의 경우 동일한 작업에 대한 엔드포인트 URL을 수정해야 합니다. 동일한 요청은 다음과 같습니다.

```http
https://my-private-link-speech.cognitiveservices.azure.com/tts/cognitiveservices/voices/list
```
Speech SDK에 대한 자세한 내용은 [엔드포인트 URL 생성](#construct-endpoint-url) 하위 섹션을 참조하세요.

### <a name="speech-resource-with-a-custom-domain-name-and-a-private-endpoint-usage-with-the-speech-sdk"></a>사용자 지정 도메인 이름 및 프라이빗 엔드포인트가 있는 Speech 리소스: Speech SDK와 함께 사용

사용자 지정 도메인 이름 및 private-endpoint-enabled Speech 리소스와 함께 Speech SDK를 사용하려면 애플리케이션 코드를 검토하고 변경해야 합니다.

이 섹션에서는 `my-private-link-speech.cognitiveservices.azure.com`을 샘플 Speech 리소스 DNS 이름(사용자 지정 도메인)으로 사용합니다.

#### <a name="construct-endpoint-url"></a>엔드포인트 URL 생성

일반적으로 SDK 시나리오(짧은 오디오용 Speech-to-text REST API 및 Text-to-speech REST API 시나리오도 포함)에서 Speech 리소스는 다양한 서비스 제공 사항을 위해 전용 지역 엔드포인트를 사용합니다. 이러한 엔드포인트의 DNS 이름 형식은 다음과 같습니다.

`{region}.{speech service offering}.speech.microsoft.com`

DNS 이름의 예는 다음과 같습니다.

`westeurope.stt.speech.microsoft.com`

지역에 가능한 모든 값(DNS 이름의 첫 번째 요소)은 [Speech Service 지원 지역](regions.md)에 나열되어 있습니다. (Azure Government 및 Azure 중국 엔드포인트에 대해서는 [이 문서](sovereign-clouds.md)를 참조하세요.) 다음 표는 Speech Services 제공 사항에 대해 가능한 값을 보여 줍니다(DNS 이름의 두 번째 요소).

| DNS 이름 값 | Speech Service 제공 사항                                    |
|----------------|-------------------------------------------------------------|
| `commands`     | [사용자 지정 명령](custom-commands.md)                       |
| `convai`       | [대화 전사](conversation-transcription.md) |
| `s2s`          | [Speech Translation](speech-translation.md)                 |
| `stt`          | [음성 텍스트 변환](speech-to-text.md)                         |
| `tts`          | [Text-to-speech](text-to-speech.md)                         |
| `voice`        | [Custom Voice](how-to-custom-voice.md)                      |

따라서 앞의 예(`westeurope.stt.speech.microsoft.com`)는 서유럽의 Speech-to-text 엔드포인트를 나타냅니다.

private-endpoint-enabled 엔드포인트는 특수 프록시를 통해 Speech Services와 통신합니다. 따라서 *엔드포인트 연결 URL을 변경해야 합니다*. 

"표준" 엔드포인트 URL은 다음과 같습니다. <p/>`{region}.{speech service offering}.speech.microsoft.com/{URL path}`

프라이빗 엔드포인트 URL의 형식은 다음과 같습니다. <p/>`{your custom name}.cognitiveservices.azure.com/{speech service offering}/{URL path}`

**예 1.** 애플리케이션이 다음 URL을 사용하여 통신합니다(서유럽에서 미국 영어에 대한 기본 모델을 사용한 음성 인식).

```
wss://westeurope.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1?language=en-US
```

Speech 리소스의 사용자 지정 도메인 이름이 `my-private-link-speech.cognitiveservices.azure.com`인 경우 private-endpoint-enabled 시나리오에서 이를 사용하려면 다음과 같이 URL을 수정해야 합니다.

```
wss://my-private-link-speech.cognitiveservices.azure.com/stt/speech/recognition/conversation/cognitiveservices/v1?language=en-US
```

다음 세부 정보를 확인합니다.

- 호스트 이름 `westeurope.stt.speech.microsoft.com`이 사용자 지정 도메인 호스트 이름 `my-private-link-speech.cognitiveservices.azure.com`으로 대체됩니다.
- 원래 DNS 이름(`stt`)의 두 번째 요소는 URL 경로의 첫 번째 요소가 되고 원래 경로 앞에 옵니다. 따라서 원래 URL `/speech/recognition/conversation/cognitiveservices/v1?language=en-US`는 `/stt/speech/recognition/conversation/cognitiveservices/v1?language=en-US`가 됩니다.

**예 2.** 애플리케이션이 다음 URL을 사용하여 사용자 지정 음성 모델을 통해 서유럽에서 음성을 합성합니다.
```http
https://westeurope.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId=974481cc-b769-4b29-af70-2fb557b897c4
```

다음의 동등한 URL은 Speech 리소스의 사용자 지정 도메인 이름이 `my-private-link-speech.cognitiveservices.azure.com`인 프라이빗 엔드포인트를 사용합니다.

```http
https://my-private-link-speech.cognitiveservices.azure.com/voice/cognitiveservices/v1?deploymentId=974481cc-b769-4b29-af70-2fb557b897c4
```

예 1과 동일한 원칙이 적용되지만 이번에는 키 요소가 `voice`입니다.

#### <a name="modifying-applications"></a>애플리케이션 수정

코드를 수정하려면 다음 단계를 따르세요.

1. 애플리케이션 엔드포인트 URL을 확인합니다.

   - [애플리케이션에 대한 로깅을 설정](how-to-use-logging.md)하고 실행하여 작업을 기록합니다.
   - 로그 파일에서 `SPEECH-ConnectionUrl`을 검색합니다. 일치하는 줄에서 `value` 매개 변수에는 애플리케이션이 Speech Services에 연결하는 데 사용한 전체 URL이 포함됩니다.

   예:

   ```
   (114917): 41ms SPX_DBG_TRACE_VERBOSE:  property_bag_impl.cpp:138 ISpxPropertyBagImpl::LogPropertyAndValue: this=0x0000028FE4809D78; name='SPEECH-ConnectionUrl'; value='wss://westeurope.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1?traffictype=spx&language=en-US'
   ```

   따라서 이 예에 사용된 애플리케이션의 URL은 다음과 같습니다.

   ```
   wss://westeurope.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1?language=en-US
   ```

2. 전체 엔드포인트 URL을 사용하여 `SpeechConfig` 인스턴스를 만듭니다.

   1. 이전 [엔드포인트 URL 구성](#construct-endpoint-url) 섹션에 설명된 대로 방금 결정한 엔드포인트를 수정합니다.

   1. `SpeechConfig` 인스턴스 작성 방법을 수정합니다. 대부분의 경우 애플리케이션은 다음과 같은 항목을 사용합니다.
      ```csharp
      var config = SpeechConfig.FromSubscription(subscriptionKey, azureRegion);
      ```
      이전 섹션에서 설명한 호스트 이름 및 URL 변경으로 인해 private-endpoint-enabled Speech 리소스에는 이 방법이 작동하지 않습니다. private-endpoint-enabled 리소스의 키를 사용하여 수정하지 않고 기존 애플리케이션을 실행하려고 하면 인증 오류(401)가 발생합니다.

      오류를 해결하려면 `SpeechConfig` 클래스를 인스턴스화하는 방법을 수정하고 "엔드포인트에서"/"엔드포인트를 사용하여" 초기화를 사용합니다. 다음과 같은 두 개의 변수가 정의되어 있다고 가정합니다.
      - `subscriptionKey`에는 private-endpoint-enabled Speech 리소스의 키가 포함됩니다.
      - `endPoint`에는 전체 *수정된* 엔드포인트 URL이 포함됩니다(해당 프로그래밍 언어에 필요한 유형 사용). 이 예에서 이 변수는 다음을 포함해야 합니다.
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
> 엔드포인트 URI에 지정된 쿼리 매개 변수는 다른 API에서 설정한 경우에도 변경되지 않습니다. 예를 들어, 인식 언어가 URI에서 쿼리 매개 변수 `language=en-US`로 정의되고 해당 속성을 통해 `ru-RU`로 설정된 경우 URI의 언어 설정이 사용됩니다. 이 경우 유효한 언어는 `en-US`입니다.
>
> 엔드포인트 URI에 설정된 매개 변수가 항상 우선합니다. 다른 API는 엔드포인트 URI에 지정되지 않은 매개 변수만 재정의할 수 있습니다.

이 수정 후 애플리케이션은 private-endpoint-enabled Speech 리소스와 함께 작동해야 합니다. 현재 프라이빗 엔드포인트 시나리오를 보다 원활하게 지원하기 위해 노력하고 있습니다.

## <a name="adjust-an-application-to-use-a-speech-resource-without-private-endpoints"></a>프라이빗 엔드포인트 없이 Speech 리소스를 사용하도록 애플리케이션 조정

이 문서에서는 Speech 리소스에 대한 사용자 지정 도메인 사용 설정을 *되돌릴 수 없다* 는 점을 여러 번 지적했습니다. 이러한 리소스는 [지역 엔드포인트 이름](../cognitive-services-custom-subdomains.md#is-there-a-list-of-regional-endpoints)을 사용하는 리소스와는 다른 방법으로 Speech Services와 통신합니다.

이 섹션에서는 Speech Services REST API 및 [Speech SDK](speech-sdk.md)를 사용하여 사용자 지정 도메인 이름은 사용하고 프라이빗 엔드포인트는 *사용하지 않는* Speech 리소스를 사용하는 방법을 설명합니다. 이 리소스는 한 때 프라이빗 엔드포인트 시나리오에서 사용되었지만 해당 프라이빗 엔드포인트가 삭제된 리소스일 수 있습니다.

### <a name="dns-configuration"></a>DNS 구성

private-endpoint-enabled Speech 리소스의 사용자 지정 도메인 DNS 이름이 [퍼블릭 네트워크에서 어떻게 확인](#resolve-dns-from-other-networks)되는지 기억해 두세요. 이 경우 확인된 IP 주소는 가상 네트워크의 프록시 엔드포인트를 가리킵니다. 해당 엔드포인트는 네트워크 트래픽을 private-endpoint-enabled Cognitive Services 리소스로 발송하는 데 사용됩니다.

그러나 *모든* 리소스 프라이빗 엔드포인트가 제거되면(또는 사용자 지정 도메인 이름을 사용하도록 설정한 직후) Speech 리소스의 CNAME 레코드가 다시 프로비저닝됩니다. 이제 해당 [Cognitive Services 지역 엔드포인트](../cognitive-services-custom-subdomains.md#is-there-a-list-of-regional-endpoints)의 IP 주소를 가리킵니다.

따라서 `nslookup` 명령의 출력은 다음과 같습니다.
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
[이 섹션](#resolve-dns-from-other-networks)의 출력과 비교합니다.

### <a name="speech-resource-with-a-custom-domain-name-and-without-private-endpoints-usage-with-the-rest-apis"></a>사용자 지정 도메인 이름은 사용하고 프라이빗 엔드포인트는 사용하지 않는 Speech 리소스: REST API 사용

#### <a name="speech-to-text-rest-api-v30"></a>Speech-to-text REST API v3.0

Speech-to-text REST API v3.0 사용은 [private-endpoint-enabled Speech 리소스](#speech-to-text-rest-api-v30)의 경우와 완전히 동일합니다.

#### <a name="speech-to-text-rest-api-for-short-audio-and-text-to-speech-rest-api"></a>짧은 오디오용 Speech-to-text REST API 및 Text-to-speech REST API

이 경우 짧은 오디오용 Speech-to-text REST API 및 Text-to-speech REST API 사용 방법은 한 가지 예외를 제외하고는 일반적인 경우와 차이가 없습니다. (다음 참고 참조) [짧은 오디오용 Speech-to-text REST API](rest-speech-to-text.md#speech-to-text-rest-api-for-short-audio) 및 [Text-to-speech REST API](rest-text-to-speech.md) 설명서에 설명된 대로 두 API를 모두 사용해야 합니다.

> [!NOTE]
> 사용자 지정 도메인 시나리오에서 짧은 오디오용 Speech-to-text REST API 및 Text-to-speech REST API를 사용하는 경우 `Ocp-Apim-Subscription-Key` 헤더를 통해 전달된 구독 키를 사용합니다. (자세한 내용은 [짧은 오디오용 Speech-to-text REST API](rest-speech-to-text.md#request-headers) 및 [Text-to-speech REST API](rest-text-to-speech.md#request-headers) 참조)
>
> 권한 부여 토큰을 사용하고 `Authorization` 헤더를 통해 특수 엔드포인트로 전달하는 것은 Speech 리소스의 **네트워킹** 섹션에서 **모든 네트워크** 액세스 옵션을 설정한 경우에 *만* 작동합니다. 다른 경우에 권한 부여 토큰을 얻으려고 하면 `Forbidden` 또는 `BadRequest` 오류가 발생합니다.

### <a name="speech-resource-with-a-custom-domain-name-and-without-private-endpoints-usage-with-the-speech-sdk"></a>사용자 지정 도메인 이름은 사용하고 프라이빗 엔드포인트는 사용하지 않는 Speech 리소스: Speech SDK 사용

프라이빗 엔드포인트를 *사용하지 않는* custom-domain-enabled Speech 리소스와 함께 Speech SDK를 사용하는 방법은 [Speech SDK 설명서](speech-sdk.md)에 설명된 일반적인 경우와 동일합니다.

[private-endpoint-enabled Speech 리소스](#speech-resource-with-a-custom-domain-name-and-a-private-endpoint-usage-with-the-speech-sdk)와 함께 사용하기 위해 코드를 수정한 경우 다음을 고려합니다.

[private-endpoint-enabled Speech 리소스](#speech-resource-with-a-custom-domain-name-and-a-private-endpoint-usage-with-the-speech-sdk) 섹션에서 엔드포인트 URL을 확인하고 수정하고 `SpeechConfig` 클래스 인스턴스의 "엔드 포인트에서"/"엔드 포인트를 사용하여" 초기화를 통해 작동하도록 하는 방법을 설명했습니다.

그러나 모든 프라이빗 엔드포인트를 제거한 후 동일한 애플리케이션을 실행하려고 하면(해당 DNS 레코드를 다시 프로비저닝하는 데 약간의 시간이 허용됨) 내부 서비스 오류(404)가 발생합니다. 그 이유는 이제 [DNS 레코드](#dns-configuration)가 가상 네트워크 프록시 대신 지역 Cognitive Services 엔드포인트를 가리키고 `/stt/speech/recognition/conversation/cognitiveservices/v1?language=en-US`와 같은 URL 경로를 찾을 수 없기 때문입니다.

다음과 같은 코드 스타일에서 애플리케이션을 `SpeechConfig`의 표준 인스턴스화로 롤백해야 합니다.

```csharp
var config = SpeechConfig.FromSubscription(subscriptionKey, azureRegion);
```

## <a name="pricing"></a>가격 책정

가격 책정에 대한 자세한 내용은 [Azure Private Link 가격 책정](https://azure.microsoft.com/pricing/details/private-link)을 참조하세요.

## <a name="learn-more"></a>자세한 정보

* [Azure Private Link](../../private-link/private-link-overview.md)
* [Speech SDK](speech-sdk.md)
* [Speech-to-Text REST API](rest-speech-to-text.md)
* [Text-to-Speech REST API](rest-text-to-speech.md)