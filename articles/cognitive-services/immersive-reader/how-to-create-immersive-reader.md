---
title: 몰입형 판독기 리소스 만들기
titleSuffix: Azure Applied AI Services
description: 이 문서에서는 사용자 지정 하위 도메인이 있는 새 몰입형 리더 리소스를 만든 다음 Azure 테넌트에서 Azure AD를 구성하는 방법을 보여 줍니다.
services: cognitive-services
author: rwaller
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: how-to
ms.date: 07/22/2019
ms.author: rwaller
ms.openlocfilehash: 56d7c56e77382dcf2b06c2ab79c712b42796e67e
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/25/2021
ms.locfileid: "110377749"
---
# <a name="create-an-immersive-reader-resource-and-configure-azure-active-directory-authentication"></a>몰입형 리더 리소스 만들기 및 Azure Active Directory 인증 구성

이 문서에서는 몰입형 리더 리소스를 만들고 Azure AD(Azure Active Directory) 인증을 구성하는 스크립트를 제공합니다. 이 스크립트로든 포털에서든 몰입형 리더 리소스를 만들 때마다 Azure AD 사용 권한도 구성해야 합니다. 이 스크립트는 이에 도움이 됩니다.

스크립트는 필요한 모든 몰입형 리더 및 Azure AD 리소스를 한 단계 실행으로 만들고 구성하도록 설계되었습니다. 그러나 예를 들어 혹시라도 Azure Portal에 이미 만들어 놓은 게 있다면 기존 몰입형 리더 리소스에 대해 Azure AD 인증 구성만 할 수도 있습니다.

일부 고객에 대해서는 개발용 및 프로덕션용으로 또는 아마도 서비스가 배포되는 여러 다른 지역용으로 여러 개의 몰입형 리더 리소스를 만들어야 할 수 있습니다. 이러한 경우에 대해서는 돌아와서 스크립트를 여러 번 사용하여 여러 가지 몰입형 리더 리소스를 만들고 Azure AD 권한으로 구성할 수 있습니다.

스크립트는 유연하도록 설계되었습니다. 먼저 구독에서 기존 몰입형 리더 및 Azure AD 리소스를 검색하여 아직 없다면 필요한 경우에만 이를 만듭니다. 몰입형 리더 리소스를 처음 만들어 보는 경우 스크립트가 필요한 모든 작업을 수행합니다. 스크립트를 사용해서 포털에서 만든 기존 몰입형 리더 리소스에 대해 Azure AD 구성만 하려는 경우에도 마찬가지입니다. 여러 개의 몰입형 리더 리소스를 만들고 구성하는 데 사용할 수도 있습니다.

## <a name="set-up-powershell-environment"></a>PowerShell 환경 설정

1. [Azure Cloud Shell](../../cloud-shell/overview.md)을 열어 시작합니다. 왼쪽 위의 드롭다운에서 또는 `pwsh`를 입력하여 Cloud Shell이 PowerShell로 설정되어 있는지 확인합니다.

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
        [Parameter(Mandatory=$true)] [String] $AADAppClientSecret,
        [Parameter(Mandatory=$true)] [String] $AADAppClientSecretExpiration
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
            $clientId = az ad app create --password $AADAppClientSecret --end-date "$AADAppClientSecretExpiration" --display-name $AADAppDisplayName --identifier-uris $AADAppIdentifierUri --query "appId" -o tsv

            if (-not $clientId) {
                throw "Error: Failed to create Azure Active Directory app"
            }
            Write-Host "Azure Active Directory app created successfully."
            Write-Host "NOTE: To manage your Active Directory app client secrets after this Immersive Reader Resource has been created please visit https://portal.azure.com and go to Home -> Azure Active Directory -> App Registrations -> $AADAppDisplayName -> Certificates and Secrets blade -> Client Secrets section" -ForegroundColor Yellow
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

1. `Create-ImmersiveReaderResource` 함수를 실행합니다. 아래의 '<PARAMETER_VALUES>' 자리 표시자에 적절한 사용자 고유의 값을 제공합니다.

    ```azurepowershell-interactive
    Create-ImmersiveReaderResource -SubscriptionName '<SUBSCRIPTION_NAME>' -ResourceName '<RESOURCE_NAME>' -ResourceSubdomain '<RESOURCE_SUBDOMAIN>' -ResourceSKU '<RESOURCE_SKU>' -ResourceLocation '<RESOURCE_LOCATION>' -ResourceGroupName '<RESOURCE_GROUP_NAME>' -ResourceGroupLocation '<RESOURCE_GROUP_LOCATION>' -AADAppDisplayName '<AAD_APP_DISPLAY_NAME>' -AADAppIdentifierUri '<AAD_APP_IDENTIFIER_URI>' -AADAppClientSecret '<AAD_APP_CLIENT_SECRET>' -AADAppClientSecretExpiration '<AAD_APP_CLIENT_SECRET_EXPIRATION>'
    ```

    전체 명령은 다음과 비슷합니다. 여기서는 명확성을 위해 각 매개 변수를 개별 줄에 입력했으므로 명령 전체를 볼 수 있습니다. 이 명령을 그대로 복사하거나 사용하지 않습니다. 위의 명령을 사용자 고유의 값으로 복사하여 사용합니다. 이 예에는 위의 '<PARAMETER_VALUES>'에 대한 더미 값이 있습니다. 이러한 값에 대해 사용자는 사용자 고유의 이름을 지정하게 되므로 사용자가 사용하는 값은 다를 것입니다.

    ```
    Create-ImmersiveReaderResource
        -SubscriptionName 'MyOrganizationSubscriptionName'
        -ResourceName 'MyOrganizationImmersiveReader'
        -ResourceSubdomain 'MyOrganizationImmersiveReader'
        -ResourceSKU 'S0'
        -ResourceLocation 'westus2'
        -ResourceGroupName 'MyResourceGroupName'
        -ResourceGroupLocation 'westus2'
        -AADAppDisplayName 'MyOrganizationImmersiveReaderAADApp'
        -AADAppIdentifierUri 'https://MyOrganizationImmersiveReaderAADApp'
        -AADAppClientSecret 'SomeStrongPassword'
        -AADAppClientSecretExpiration '2021-12-31'
    ```

    | 매개 변수 | 주석 |
    | --- | --- |
    | SubscriptionName |몰입형 리더 리소스에 사용할 Azure 구독의 이름입니다. 리소스를 만들기 위해서는 구독이 있어야 합니다. |
    | ResourceName |  영숫자여야 하며 '-'가 첫 번째 또는 마지막 문자가 아닌 한 '-'를 포함할 수 있습니다. 길이는 63자를 초과할 수 없습니다.|
    | ResourceSubdomain |사용자 지정 하위 도메인이 몰입형 리더 리소스에 필요합니다. 하위 도메인은 몰입형 리더 서비스를 호출하여 리더를 시작할 때 SDK에서 사용됩니다. 하위 도메인은 전역적으로 고유해야 합니다. 하위 도메인은 영숫자여야 하며 '-'가 첫 번째 또는 마지막 문자가 아닌 한 '-'를 포함할 수 있습니다. 길이는 63자를 초과할 수 없습니다. 리소스가 이미 존재하는 경우 이 매개 변수는 선택 사항입니다. |
    | ResourceSKU |옵션: `S0`(표준 계층) 또는 `S1`(교육/비영리 조직)이 있습니다. 사용 가능한 각 SKU에 대해 자세히 알아보려면 [Cognitive Services 가격 책정 페이지](https://azure.microsoft.com/pricing/details/cognitive-services/immersive-reader/)를 참조합니다. 리소스가 이미 존재하는 경우 이 매개 변수는 선택 사항입니다. |
    | ResourceLocation |옵션: `eastus`, `eastus2`, `southcentralus`, `westus`, `westus2`, `australiaeast`, `southeastasia`, `centralindia`, `japaneast`, `northeurope`, `uksouth`, `westeurope`. 리소스가 이미 존재하는 경우 이 매개 변수는 선택 사항입니다. |
    | ResourceGroupName |리소스는 구독 내의 리소스 그룹에 만들어집니다. 기존 리소스 그룹의 이름을 제공합니다. 리소스 그룹이 아직 없는 경우 이 이름을 갖는 새 리소스 그룹이 만들어집니다. |
    | ResourceGroupLocation |리소스 그룹이 없는 경우 그룹을 만들 위치를 제공해야 합니다. 위치 목록을 찾으려면 `az account list-locations`를 실행합니다. 반환된 결과의 *name* 속성(공백 없음)을 사용합니다. 리소스 그룹이 이미 있는 경우 이 매개 변수는 선택 사항입니다. |
    | AADAppDisplayName |Azure Active Directory 애플리케이션 표시 이름입니다. 기존 Azure AD 애플리케이션을 찾을 수 없는 경우 이 이름을 갖는 새 애플리케이션이 만들어집니다. Azure AD 애플리케이션이 이미 있는 경우 이 매개 변수는 선택 사항입니다. |
    | AADAppIdentifierUri |Azure AD 앱의 URI입니다. 기존 Azure AD 앱을 찾을 수 없는 경우 이 URI로 새 앱이 만들어집니다. 예들 들어 `https://immersivereaderaad-mycompany`입니다. |
    | AADAppClientSecret |사용자가 만들어서 나중에 몰입형 리더를 시작하기 위한 토큰을 획득할 때 인증에 사용하는 암호입니다. 암호는 길이가 16자 이상이며 특수 문자 1자 이상 및 숫자 1자 이상을 포함해야 합니다. 이 리소스를 만든 후 Azure AD 애플리케이션 클라이언트 비밀을 관리하려면 https://portal.azure.com 을 방문하여 아래의 "Azure AD 애플리케이션 비밀 관리"스크린샷에 표시된 대로 홈 -> Azure Active Directory -> 앱 등록 -> `[AADAppDisplayName]` -> 인증서 및 비밀 블레이드 -> 클라이언트 비밀 섹션으로 이동하세요. |
    | AADAppClientSecretExpiration |`[AADAppClientSecret]`이 만료되는 날짜 또는 날짜/시간입니다(예: '2020-12-31T11:59:59+00:00' 또는 '2020-12-31'). |

    Azure AD 애플리케이션 비밀 관리

    ![Azure Portal 인증서 및 비밀 블레이드](./media/client-secrets-blade.png)

1. 나중에 사용하기 위해 JSON 출력을 텍스트 파일로 복사합니다. 출력은 다음과 비슷합니다.

    ```json
    {
      "TenantId": "...",
      "ClientId": "...",
      "ClientSecret": "...",
      "Subdomain": "..."
    }
    ```

## <a name="next-steps"></a>다음 단계

* Node.js를 사용하여 몰입형 판독기 SDK를 통해 수행할 수 있는 다른 작업을 확인하려면 [Node.js 빠른 시작](./quickstarts/client-libraries.md?pivots=programming-language-nodejs)을 살펴보세요.
* [Android 자습서](./how-to-launch-immersive-reader.md)를 보고 Java 또는 Android용 Kotlin을 사용하여 몰입형 리더 SDK를 통해 수행할 수 있는 다른 작업을 확인합니다.
* [iOS 자습서](./how-to-launch-immersive-reader.md)를 보고 iOS용 Swift를 사용하여 몰입형 리더 SDK를 통해 수행할 수 있는 다른 작업을 확인합니다.
* Python을 사용하여 몰입형 리더 SDK를 통해 수행할 수 있는 다른 작업을 확인하려면 [Python 자습서](./how-to-launch-immersive-reader.md)를 살펴보세요.
* [몰입형 판독기 SDK](https://github.com/microsoft/immersive-reader-sdk) 및 [몰입형 판독기 SDK 참조](./reference.md) 살펴보기