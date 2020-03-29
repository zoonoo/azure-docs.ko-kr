---
title: 몰입형 판독기 리소스 만들기
titleSuffix: Azure Cognitive Services
description: 이 문서에서는 사용자 지정 하위 도메인을 사용하여 새 몰입형 Reader 리소스를 만든 다음 Azure 테넌트에서 Azure AD를 구성하는 방법을 보여 줍니다.
services: cognitive-services
author: rwaller
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: rwaller
ms.openlocfilehash: 41efe4592c65ae3cdd85ce1b212554e50691905a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78330722"
---
# <a name="create-an-immersive-reader-resource-and-configure-azure-active-directory-authentication"></a>몰입형 Reader 리소스 를 만들고 Azure Active Directory 인증을 구성합니다.

이 문서에서는 몰입형 Reader 리소스를 만들고 Azure Active Directory(Azure AD) 인증을 구성하는 스크립트를 제공합니다. 이 스크립트를 사용하든 포털에서든 몰입형 Reader 리소스를 만들 때마다 Azure AD 권한으로 구성해야 합니다. 이 스크립트는 도움이 될 것입니다.

스크립트는 한 번에 모두 필요한 몰입형 리더 및 Azure AD 리소스를 만들고 구성하도록 설계되었습니다. 그러나 예를 들어 Azure Portal에서 이미 만든 경우 기존 몰입도 판독기 리소스에 대해 Azure AD 인증을 구성할 수도 있습니다.

일부 고객의 경우 개발 및 프로덕션을 위해 또는 서비스가 배포된 여러 다른 지역에 대해 여러 몰입형 Reader 리소스를 만들어야 할 수 있습니다. 이러한 경우 다시 돌아와 서 다른 몰입 리더 리소스를 만들고 Azure AD 권한으로 구성 하는 스크립트를 여러 번 사용할 수 있습니다.

스크립트는 유연하도록 설계되었습니다. 먼저 구독에서 기존 몰입형 판독기 및 Azure AD 리소스를 찾고 아직 존재하지 않는 경우에만 필요한 경우에만 만듭니다. 몰입형 Reader 리소스를 처음 만드는 경우 스크립트는 필요한 모든 작업을 수행합니다. 포털에서 만든 기존 몰입형 Reader 리소스에 대해 Azure AD를 구성하는 데만 사용하려는 경우 이 것도 그렇게 할 것입니다. 또한 여러 몰입형 Reader 리소스를 만들고 구성하는 데 사용할 수도 있습니다.

## <a name="set-up-powershell-environment"></a>PowerShell 환경 설정

1. Azure 클라우드 [셸을](https://docs.microsoft.com/azure/cloud-shell/overview)열어 시작합니다. 클라우드 셸이 왼쪽 위 쪽 드롭다운 또는 입력하여 `pwsh`PowerShell으로 설정되어 있는지 확인합니다.

1. 다음 코드 조각을 복사하여 셸에 붙여넣습니다.

    ```azurepowershell-interactive
    function Create-ImmersiveReaderResource(
        [Parameter(Mandatory=$true, Position=0)] [String] $SubscriptionName,
        [Parameter(Mandatory=$true)] [String] $ResourceName,
        [Parameter(Mandatory=$true)] [String] $ResourceSubdomain,
        [Parameter(Mandatory=$true)] [String] $ResourceSKU,
        [Parameter(Mandatory=$true)] [String] $ResourceLocation,
        [Parameter(Mandatory=$true)] [String] $ResourceGroupName,
        [Parameter(Mandatory=$true)] [String] $ResourceGroupLocation,
        [Parameter(Mandatory=$true)] [String] $AADAppDisplayName="ImmersiveReaderAAD",
        [Parameter(Mandatory=$true)] [String] $AADAppIdentifierUri,
        [Parameter(Mandatory=$true)] [String] $AADAppClientSecret
    )
    {
        $unused = ''
        if (-not [System.Uri]::TryCreate($AADAppIdentifierUri, [System.UriKind]::Absolute, [ref] $unused)) {
            throw "Error: AADAppIdentifierUri must be a valid URI"
        }

        Write-Host "Setting the active subscription to '$SubscriptionName'"
        $subscriptionExists = Get-AzSubscription -SubscriptionName $SubscriptionName
        if (-not $subscriptionExists) {
            throw "Error: Subscription does not exist"
        }
        az account set --subscription $SubscriptionName

        $resourceGroupExists = az group exists --name $ResourceGroupName
        if ($resourceGroupExists -eq "false") {
            Write-Host "Resource group does not exist. Creating resource group"
            $groupResult = az group create --name $ResourceGroupName --location $ResourceGroupLocation
            if (-not $groupResult) {
                throw "Error: Failed to create resource group"
            }
            Write-Host "Resource group created successfully"
        }

        # Create an Immersive Reader resource if it doesn't already exist
        $resourceId = az cognitiveservices account show --resource-group $ResourceGroupName --name $ResourceName --query "id" -o tsv
        if (-not $resourceId) {
            Write-Host "Creating the new Immersive Reader resource '$ResourceName' (SKU '$ResourceSKU') in '$ResourceLocation' with subdomain '$ResourceSubdomain'"
            $resourceId = az cognitiveservices account create `
                            --name $ResourceName `
                            --resource-group $ResourceGroupName `
                            --kind ImmersiveReader `
                            --sku $ResourceSKU `
                            --location $ResourceLocation `
                            --custom-domain $ResourceSubdomain `
                            --query "id" `
                            -o tsv

            if (-not $resourceId) {
                throw "Error: Failed to create Immersive Reader resource"
            }
            Write-Host "Immersive Reader resource created successfully"
        }

        # Create an Azure Active Directory app if it doesn't already exist
        $clientId = az ad app show --id $AADAppIdentifierUri --query "appId" -o tsv
        if (-not $clientId) {
            Write-Host "Creating new Azure Active Directory app"
            $clientId = az ad app create --password $AADAppClientSecret --display-name $AADAppDisplayName --identifier-uris $AADAppIdentifierUri --query "appId" -o tsv

            if (-not $clientId) {
                throw "Error: Failed to create Azure Active Directory app"
            }
            Write-Host "Azure Active Directory app created successfully"
        }

        # Create a service principal if it doesn't already exist
        $principalId = az ad sp show --id $AADAppIdentifierUri --query "objectId" -o tsv
        if (-not $principalId) {
            Write-Host "Creating new service principal"
            az ad sp create --id $clientId | Out-Null
            $principalId = az ad sp show --id $AADAppIdentifierUri --query "objectId" -o tsv

            if (-not $principalId) {
                throw "Error: Failed to create new service principal"
            }
            Write-Host "New service principal created successfully"
        }

        # Sleep for 5 seconds to allow the new service principal to propagate
        Write-Host "Sleeping for 5 seconds"
        Start-Sleep -Seconds 5

        Write-Host "Granting service principal access to the newly created Immersive Reader resource"
        $accessResult = az role assignment create --assignee $principalId --scope $resourceId --role "Cognitive Services User"
        if (-not $accessResult) {
            throw "Error: Failed to grant service principal access"
        }
        Write-Host "Service principal access granted successfully"

        # Grab the tenant ID, which is needed when obtaining an Azure AD token
        $tenantId = az account show --query "tenantId" -o tsv

        # Collect the information needed to obtain an Azure AD token into one object
        $result = @{}
        $result.TenantId = $tenantId
        $result.ClientId = $clientId
        $result.ClientSecret = $AADAppClientSecret
        $result.Subdomain = $ResourceSubdomain

        Write-Host "Success! " -ForegroundColor Green -NoNewline
        Write-Host "Save the following JSON object to a text file for future reference:"
        Write-Output (ConvertTo-Json $result)
    }
    ```

1. 함수를 `Create-ImmersiveReaderResource`실행하여 매개 변수를 적절하게 공급합니다.

    ```azurepowershell-interactive
    Create-ImmersiveReaderResource
      -SubscriptionName '<SUBSCRIPTION_NAME>' `
      -ResourceName '<RESOURCE_NAME>' `
      -ResourceSubdomain '<RESOURCE_SUBDOMAIN>' `
      -ResourceSKU '<RESOURCE_SKU>' `
      -ResourceLocation '<RESOURCE_LOCATION>' `
      -ResourceGroupName '<RESOURCE_GROUP_NAME>' `
      -ResourceGroupLocation '<RESOURCE_GROUP_LOCATION>' `
      -AADAppDisplayName '<AAD_APP_DISPLAY_NAME>' `
      -AADAppIdentifierUri '<AAD_APP_IDENTIFIER_URI>' `
      -AADAppClientSecret '<AAD_APP_CLIENT_SECRET>'
    ```

    | 매개 변수 | 주석 |
    | --- | --- |
    | SubscriptionName |몰입형 Reader 리소스에 사용할 Azure 구독의 이름입니다. 리소스를 만들려면 구독이 있어야 합니다. |
    | ResourceName |  영숫자여야 하며,'-'가 첫 번째 또는 마지막 문자가 아닌 한 '-'를 포함할 수 있습니다. 길이는 63자를 초과할 수 없습니다.|
    | 리소스하위 도메인 |몰입형 Reader 리소스에는 사용자 지정 하위 도메인이 필요합니다. 하위 도메인은 몰입형 판독기 서비스를 호출하여 Reader를 시작할 때 SDK에서 사용됩니다. 하위 도메인은 전역적으로 고유해야 합니다. 하위 도메인은 영숫자여야 하며'-'가 첫 번째 또는 마지막 문자가 아닌 한 '-'를 포함할 수 있습니다. 길이는 63자를 초과할 수 없습니다. 리소스가 이미 있는 경우 이 매개 변수는 선택 사항입니다. |
    | 리소스스쿠 |옵션: `S0`. 사용 가능한 각 SKU에 대해 자세히 알아보려면 [코그너티브 서비스 가격 페이지를](https://azure.microsoft.com/pricing/details/cognitive-services/immersive-reader/) 방문하십시오. 리소스가 이미 있는 경우 이 매개 변수는 선택 사항입니다. |
    | ResourceLocation |옵션 `eastus`: `eastus2` `southcentralus`, `westus` `westus2`, `australiaeast` `southeastasia`, `centralindia` `japaneast`, `northeurope` `uksouth`, `westeurope`, , , , , , 리소스가 이미 있는 경우 이 매개 변수는 선택 사항입니다. |
    | ResourceGroupName |리소스는 구독 내의 리소스 그룹에서 만들어집니다. 기존 리소스 그룹의 이름을 제공합니다. 리소스 그룹이 아직 없는 경우 이 이름의 새 그룹이 만들어집니다. |
    | ResourceGroupLocation |리소스 그룹이 없는 경우 그룹을 만들 위치를 제공해야 합니다. 위치 목록을 찾으려면 을 `az account list-locations`실행합니다. 반환된 결과의 *이름* 속성(공백 제외)을 사용합니다. 리소스 그룹이 이미 있는 경우 이 매개 변수는 선택 사항입니다. |
    | AADApp디스플레이이름 |Azure Active Directory 응용 프로그램 표시 이름입니다. 기존 Azure AD 응용 프로그램을 찾을 수 없는 경우 이 이름의 새 응용 프로그램이 만들어집니다. 이 매개 변수는 Azure AD 응용 프로그램이 이미 있는 경우 선택 사항입니다. |
    | AADApp식별자 |Azure AD 앱의 URI입니다. 기존 Azure AD 앱을 찾을 수 없는 경우 이 URI를 가진 새 앱이 만들어집니다. `https://immersivereaderaad-mycompany`)을 입력합니다. |
    | AADApp클라이언트시크릿 |나중에 생성한 암호는 몰입형 Reader를 시작하기 위해 토큰을 획득할 때 인증하는 데 사용됩니다. 암호는 16자 이상이어야 하며 특수 문자가 1개 이상이어야 하며 숫자 문자가 1개 이상이어야 합니다. |

1. 나중에 사용할 수 위해 JSON 출력을 텍스트 파일로 복사합니다. 출력은 다음과 비슷합니다.

    ```json
    {
      "TenantId": "...",
      "ClientId": "...",
      "ClientSecret": "...",
      "Subdomain": "..."
    }
    ```

## <a name="next-steps"></a>다음 단계

* Node.js를 사용하여 몰입형 판독기 SDK를 통해 수행할 수 있는 다른 작업을 확인하려면 [Node.js 빠른 시작](./quickstart-nodejs.md)을 살펴보세요.
* Python을 사용하여 몰입형 리더 SDK를 통해 수행할 수 있는 다른 작업을 확인하려면 [Python 자습서](./tutorial-python.md)를 살펴보세요.
* Swift [자습서를](./tutorial-ios-picture-immersive-reader.md) 보고 Swift를 사용하여 몰입형 리더 SDK로 수행할 수 있는 다른 작업을 확인합니다.
* [몰입형 판독기 SDK](https://github.com/microsoft/immersive-reader-sdk) 및 [몰입형 판독기 SDK 참조](./reference.md) 살펴보기




