---
title: Azure Lighthouse에 고객 온보딩
description: 고객을 Azure Lighthouse에 온보딩하고, Azure 위임 리소스 관리를 사용하여 고유한 테넌트를 통해 해당 리소스를 액세스하고 관리하는 방법을 알아봅니다.
ms.date: 03/29/2021
ms.topic: how-to
ms.openlocfilehash: c02bbb789ffac262521c2f76c62081a21cd6602c
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105934311"
---
# <a name="onboard-a-customer-to-azure-lighthouse"></a>Azure Lighthouse에 고객 온보딩

이 문서에서는 서비스 공급자로서 Azure Lighthouse에 고객을 온보딩하는 방법을 설명합니다. 그러면 [Azure 위임된 리소스 관리](../concepts/azure-delegated-resource-management.md)를 사용하여 고객의 Azure AD(Azure Active Directory) 테넌트에서 위임된 리소스(구독 및/또는 리소스 그룹)를 자체 테넌트를 통해 관리할 수 ​​있습니다.

> [!TIP]
> 이 토픽에서는 서비스 공급자 및 고객 관련 내용을 다루지만 [여러 테넌트를 관리하는 엔터프라이즈](../concepts/enterprise.md)는 같은 프로세스를 사용하여 Azure Lighthouse를 설정하고 관리 환경을 통합할 수 있습니다.

여러 고객에 대해 온보딩 프로세스를 반복할 수 있습니다. 적절한 권한이 있는 사용자가 관리 테넌트에 로그인한 경우 모든 개별 고객 테넌트에 로그인하지 않고도 고객 테넌시 범위에서 관리 작업을 수행하기 위한 권한을 받을 수 있습니다.

고객 계약에 미치는 영향을 추적하고 인지도를 얻으려면 MPN(Microsoft 파트너 네트워크) ID를 온보딩한 각 구독에 대해 액세스 권한이 있는 하나 이상의 사용자 계정과 연결합니다. 서비스 공급자 테넌트에서 이 연결을 수행해야 합니다. MPN ID와 연결된 테넌트에서 서비스 주체 계정을 만든 다음, 고객을 온보딩할 때마다 해당 서비스 주체를 포함하는 것이 좋습니다. 자세한 내용은 [위임된 리소스에서 파트너 획득 크레딧을 사용하도록 파트너 ID 연결](partner-earned-credit.md)을 참조하세요.

> [!NOTE]
> 또는 [Azure Marketplace에 게시](publish-managed-services-offers.md)하는 Managed Service 제품(퍼블릭 또는 프라이빗)을 구매할 때 고객이 Azure Lighthouse에 온보딩할 수 있습니다. 여기에 설명된 온보딩 프로세스를 Azure Marketplace에 게시된 제품에 사용할 수도 있습니다.

온보딩 프로세스를 사용하려면 서비스 공급자의 테넌트와 고객의 테넌트 둘 다에서 작업을 수행해야 합니다. 이러한 모든 단계는 이 문서에 설명되어 있습니다.

## <a name="gather-tenant-and-subscription-details"></a>테넌트 및 구독 세부 정보 수집

고객의 테넌트를 온보딩하려면 활성 Azure 구독이 있어야 합니다. 또한 다음 사항을 알고 있어야 합니다.

- 서비스 공급자 테넌트의 테넌트 ID(고객의 리소스를 관리하는 위치)
- 고객 테넌트의 테넌트 ID(서비스 공급자가 관리하는 리소스 포함)
- 서비스 공급자가 관리할(또는 서비스 공급자가 관리할 리소스 그룹을 포함하는) 고객 테넌트에 있는 각 특정 구독의 구독 ID

이러한 ID 값을 아직 모르는 경우 다음 방법 중 하나를 통해 검색할 수 있습니다. 배포에서 이러한 정확한 값을 사용해야 합니다.

### <a name="azure-portal"></a>Azure portal

Azure Portal의 오른쪽 위에 있는 계정 이름으로 마우스를 가져가거나 **디렉터리 전환** 을 선택하여 테넌트 ID를 확인할 수 있습니다. 테넌트 ID를 선택하고 복사하려면 포털 내에서 "Azure Active Directory"를 검색한 다음, **속성** 을 선택하고 **디렉터리 ID** 필드에 표시된 값을 복사합니다. 고객 테넌트에서 구독의 ID를 찾으려면 "구독"을 검색한 다음, 적절한 구독 ID를 선택합니다.

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

Select-AzSubscription <subscriptionId>
```

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

az account set --subscription <subscriptionId/name>
az account show
```

> [!NOTE]
> 여기에 설명된 프로세스를 사용하여 구독 또는 구독 내의 하나 이상의 리소스 그룹을 온보딩하면 **Microsoft.ManagedServices** 리소스 공급자가 해당 구독에 등록됩니다.

## <a name="define-roles-and-permissions"></a>역할 및 권한 정의

서비스 공급자는 단일 고객을 위해 여러 작업을 수행하고, 범위마다 다른 액세스 권한을 요구할 수 있습니다. 적절한 [Azure 기본 제공 역할](../../role-based-access-control/built-in-roles.md)을 할당하기 위해 필요한 만큼의 권한을 정의할 수 있습니다. 각 권한 부여에는 관리 테넌트에서 Azure AD 사용자, 그룹 또는 서비스 주체를 참조하는 **principalId** 가 포함됩니다.

> [!NOTE]
> 명시적으로 지정되지 않은 경우 Azure Lighthouse 설명서의 “사용자”에 대한 참조는 권한 부여의 Azure AD 사용자, 그룹 또는 서비스 주체에 적용될 수 있습니다.

더 쉽게 ​​관리하려면 가능할 때마다 개별 사용자가 아니라 각 역할에 대해 Azure AD 사용자 그룹을 사용하는 것이 좋습니다. 그러면 액세스 권한이 있는 그룹에 개별 사용자를 추가하거나 제거할 수 있는 유연성이 제공되므로 사용자를 변경하기 위해 온보딩 프로세스를 반복할 필요가 없습니다. 자동화 시나리오에 유용할 수 있는 서비스 주체에도 역할을 할당할 수 있습니다.

> [!IMPORTANT]
> Azure AD 그룹에 대한 사용 권한을 추가하려면 **그룹 형식** 이 **보안** 으로 설정되어 있어야 합니다. 이 옵션은 그룹을 만들 때 선택됩니다. 자세한 내용은 [Azure Active Directory를 사용하여 기본 그룹 만들기 및 멤버 추가](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)를 참조하세요.

권한 부여를 정의할 때 사용자가 작업을 완료하는 데 필요한 권한만 갖도록 최소 권한 원칙을 따라야 합니다. 지원되는 역할과 모범 사례에 관한 정보는 [Azure Lighthouse 시나리오의 테넌트, 사용자 및 역할](../concepts/tenants-users-roles.md)을 참조하세요.

권한 부여를 정의하려면 액세스 권한을 부여하려는 서비스 공급자 테넌트의 각 사용자, 사용자 그룹 또는 서비스 주체의 ID 값을 알고 있어야 합니다. 또한 할당하려는 각 기본 제공 역할에 대한 역할 정의 ID도 필요합니다. 아직 없는 경우 서비스 공급자 테넌트 내에서 아래 명령을 실행하여 검색할 수 있습니다.

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

# To retrieve the objectId for an Azure AD group
(Get-AzADGroup -DisplayName '<yourGroupName>').id

# To retrieve the objectId for an Azure AD user
(Get-AzADUser -UserPrincipalName '<yourUPN>').id

# To retrieve the objectId for an SPN
(Get-AzADApplication -DisplayName '<appDisplayName>' | Get-AzADServicePrincipal).Id

# To retrieve role definition IDs
(Get-AzRoleDefinition -Name '<roleName>').id
```

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

# To retrieve the objectId for an Azure AD group
az ad group list --query "[?displayName == '<yourGroupName>'].objectId" --output tsv

# To retrieve the objectId for an Azure AD user
az ad user show --id "<yourUPN>" --query "objectId" --output tsv

# To retrieve the objectId for an SPN
az ad sp list --query "[?displayName == '<spDisplayName>'].objectId" --output tsv

# To retrieve role definition IDs
az role definition list --name "<roleName>" | grep name
```

> [!TIP]
> 나중에 필요할 때 테넌트의 사용자가 [위임에 대한 액세스 권한을 제거](remove-delegation.md)할 수 있도록 고객을 온보딩할 때 [관리 서비스 등록 할당 삭제 역할](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role)을 할당하는 것이 좋습니다. 이 역할을 할당하지 않으면 고객 테넌트의 사용자만 위임된 리소스를 제거할 수 있습니다.

## <a name="create-an-azure-resource-manager-template"></a>Azure Resource Manager 템플릿 만들기

고객을 온보딩하려면 다음 정보가 포함된 제품에 대한 [Azure Resource Manager](../../azure-resource-manager/index.yml) 템플릿을 만들어야 합니다. Azure Portal의 [서비스 공급자 페이지](view-manage-service-providers.md)에서 **mspOfferName** 및 **mspOfferDescription** 값이 고객에게 표시됩니다.

|필드  |정의  |
|---------|---------|
|**mspOfferName**     |이 정의를 설명하는 이름입니다. 이 값은 고객에게 제품의 제목으로 표시되며, 고유한 값이어야 합니다.        |
|**mspOfferDescription**     |제품에 대한 간단한 설명(예: "Contoso VM 관리 제품")      |
|**managedByTenantId**     |테넌트 ID          |
|**권한 부여**     |테넌트의 사용자/그룹/SPN에 대한 **principalid** 값으로, 각 값은 고객이 권한 부여의 목적을 이해하는 데 도움이 되는 **principalIdDisplayName** 값을 가지며, 액세스 수준을 지정하는 기본 제공 **roleDefinitionId** 값에 매핑되어 있습니다.      |

온보딩 프로세스에는 [샘플 리포지토리](https://github.com/Azure/Azure-Lighthouse-samples/)에 제공되는 Azure Resource Manager 템플릿 및 구성과 일치하도록 수정하고 사용자의 권한 부여를 정의하는 해당 매개 변수 파일이 필요합니다.

> [!IMPORTANT]
> 여기에 설명된 프로세스에는 같은 고객 테넌트에 구독을 온보딩하는 경우에도 온보딩되는 각 구독에 대한 별도의 배포가 필요합니다. 동일한 고객 테넌트의 여러 다른 구독 내에서 여러 리소스 그룹을 온보딩하는 경우에도 별도의 배포가 필요합니다. 그러나 단일 구독 내에 여러 리소스 그룹을 온보딩하는 것은 하나의 배포에서 수행할 수 있습니다.
>
> 동일한 구독 또는 구독 내의 리소스 그룹에 여러 제품이 적용되는 경우 별도의 배포도 필요합니다. 적용된 각 제품은 다른 **mspOfferName** 을 사용해야 합니다.

선택하는 템플릿은 전체 구독, 리소스 그룹 또는 구독 내의 여러 리소스 그룹 중 어떤 항목을 온보딩할지에 따라 달라집니다. 또한 이러한 방식으로 구독을 온보딩하려는 경우, Azure Marketplace에 게시한 관리형 서비스 제품을 구매한 고객에게 사용할 수 있는 템플릿도 제공합니다.

|온보딩할 대상  |사용하는 Azure Resource Manager 템플릿  |수정할 매개 변수 파일 |
|---------|---------|---------|
|Subscription   |[delegatedResourceManagement.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/delegated-resource-management/delegatedResourceManagement.json)  |[delegatedResourceManagement.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/delegated-resource-management/delegatedResourceManagement.parameters.json)    |
|Resource group   |[rgDelegatedResourceManagement.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/rg-delegated-resource-management/rgDelegatedResourceManagement.json)  |[rgDelegatedResourceManagement.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/rg-delegated-resource-management/rgDelegatedResourceManagement.parameters.json)    |
|구독 내의 여러 리소스 그룹   |[multipleRgDelegatedResourceManagement.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/rg-delegated-resource-management/multipleRgDelegatedResourceManagement.json)  |[multipleRgDelegatedResourceManagement.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/rg-delegated-resource-management/multipleRgDelegatedResourceManagement.parameters.json)    |
|구독(Azure Marketplace 게시된 제품을 사용하는 경우)   |[marketplaceDelegatedResourceManagement.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/marketplace-delegated-resource-management/marketplaceDelegatedResourceManagement.json)  |[marketplaceDelegatedResourceManagement.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/marketplace-delegated-resource-management/marketplaceDelegatedResourceManagement.parameters.json)    |

> [!TIP]
> 한 번의 배포로 전체 관리 그룹을 온보딩할 수는 없지만, [관리 그룹 수준에서 정책을 배포](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-delegate-management-groups)할 수 있습니다. 이 정책에서는 [deployIfNotExists 효과](../../governance/policy/concepts/effects.md#deployifnotexists)를 사용하여 관리 그룹 내의 각 구독이 지정된 관리 테넌트에 위임되었는지 확인하고, 그렇지 않으면 사용자가 제공하는 값을 기반으로 할당을 만듭니다. 그러면 관리 그룹 전체에서 작업을 수행하는 대신 개별 구독으로 작업해야 하지만, 관리 그룹의 모든 구독에 액세스할 수 있습니다.

다음 예제에서는 구독을 온보딩하는 데 사용할 수 있는 수정된 **delegatedResourceManagement.parameters.json** 파일을 보여 줍니다. 리소스 그룹 매개 변수 파일([rg-delegated-resource-management](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/rg-delegated-resource-management) 폴더에 있음)은 유사하지만 온보딩할 특정 리소스 그룹을 식별하기 위한 **rgName** 매개 변수를 포함합니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "mspOfferName": {
            "value": "Fabrikam Managed Services - Interstellar"
        },
        "mspOfferDescription": {
            "value": "Fabrikam Managed Services - Interstellar"
        },
        "managedByTenantId": {
            "value": "df4602a3-920c-435f-98c4-49ff031b9ef6"
        },
        "authorizations": {
            "value": [
                {
                    "principalId": "0019bcfb-6d35-48c1-a491-a701cf73b419",
                    "principalIdDisplayName": "Tier 1 Support",
                    "roleDefinitionId": "b24988ac-6180-42a0-ab88-20f7382dd24c"
                },
                {
                    "principalId": "0019bcfb-6d35-48c1-a491-a701cf73b419",
                    "principalIdDisplayName": "Tier 1 Support",
                    "roleDefinitionId": "36243c78-bf99-498c-9df9-86d9f8d28608"
                },
                {
                    "principalId": "0afd8497-7bff-4873-a7ff-b19a6b7b332c",
                    "principalIdDisplayName": "Tier 2 Support",
                    "roleDefinitionId": "acdd72a7-3385-48ef-bd42-f606fba81ae7"
                },
                {
                    "principalId": "9fe47fff-5655-4779-b726-2cf02b07c7c7",
                    "principalIdDisplayName": "Service Automation Account",
                    "roleDefinitionId": "b24988ac-6180-42a0-ab88-20f7382dd24c"
                },
                {
                    "principalId": "3kl47fff-5655-4779-b726-2cf02b05c7c4",
                    "principalIdDisplayName": "Policy Automation Account",
                    "roleDefinitionId": "18d7d88d-d35e-4fb5-a5c3-7773c20a72d9",
                    "delegatedRoleDefinitionIds": [
                        "b24988ac-6180-42a0-ab88-20f7382dd24c",
                        "92aaf0da-9dab-42b6-94a3-d43ce8d16293"
                    ]
                }
            ]
        }
    }
}
```

위 예제의 마지막 권한 부여는 사용자 액세스 관리자 역할이 있는 **principalId** 를 추가합니다(18d7d88d-d35e-4fb5-a5c3-7773c20a72d9). 이 역할을 할당할 때 **delegatedRoleDefinitionIds** 속성과 하나 이상의 지원되는 Azure 기본 제공 역할을 포함해야 합니다. 이 권한 부여에서 만든 사용자는 고객 테넌트의 [관리 ID](../../active-directory/managed-identities-azure-resources/overview.md)에 해당 역할을 할당할 수 있습니다. 이러한 역할 할당은 [수정할 수 있는 정책을 배포](deploy-policy-remediation.md)하는 데 필요합니다.  또한 사용자는 지원 인시던트를 만들 수 있습니다. 사용자 액세스 관리자 역할에 연결된 다른 사용 권한이 이 **principalId** 에 적용되지 않습니다.

## <a name="deploy-the-azure-resource-manager-templates"></a>Azure Resource Manager 템플릿 배포

매개 변수 파일을 업데이트한 후 고객 테넌트의 사용자는 해당 테넌트 내의 Azure Resource Manager 템플릿을 배포해야 합니다. 온보딩하려는 구독(또는 온보딩하려는 리소스 그룹을 포함하는 각 구독)마다 별도의 배포가 필요합니다.

> [!IMPORTANT]
> 이 배포는 온보딩하려는 구독(또는 온보딩하려는 리소스 그룹을 포함하는 구독)에 대해 [소유자](../../role-based-access-control/built-in-roles.md#owner)와 같이 `Microsoft.Authorization/roleAssignments/write` 권한을 갖는 역할이 있는 고객의 테넌트에서 게스트가 아닌 계정으로 수행해야 합니다. 구독을 위임할 수 있는 사용자를 찾기 위해 고객 테넌트의 사용자는 Azure Portal에서 구독을 선택하고, **IAM(액세스 제어)** 을 열고, [소유자 역할이 있는 모든 사용자를 볼](../../role-based-access-control/role-assignments-list-portal.md#list-owners-of-a-subscription) 수 있습니다. 
>
> [CSP(클라우드 솔루션 공급자) 프로그램](../concepts/cloud-solution-provider.md)를 통해 구독을 만든 경우 서비스 공급자 테넌트에서 [관리 에이전트](/partner-center/permissions-overview#manage-commercial-transactions-in-partner-center-azure-ad-and-csp-roles) 역할이 있는 모든 사용자가 배포를 수행할 수 있습니다.

배포는 아래와 같이 Azure Portal, PowerShell 또는 Azure CLI를 사용하여 수행할 수 있습니다.

### <a name="azure-portal"></a>Azure portal

1. [GitHub 리포지토리](https://github.com/Azure/Azure-Lighthouse-samples/)에서 사용하려는 템플릿 옆에 있는 **Azure에 배포** 단추를 선택합니다. 그러면 Azure Portal에서 템플릿이 열립니다.
1. **Msp 제품 이름**,  **Msp 제품 설명**, **테넌트 ID로 관리**, **권한 부여** 값을 입력합니다. 원하는 경우 **매개 변수 편집** 을 선택하여 매개 변수 파일에 직접 `mspOfferName`, `mspOfferDescription`, `managedbyTenantId`, `authorizations`의 값을 입력할 수 있습니다. 템플릿의 기본값을 사용하는 대신 해당 값을 업데이트해야 합니다.
1. **검토 및 만들기** 를 선택한 다음, **만들기** 를 선택합니다.

몇 분 후에 배포가 완료되었다는 알림이 표시되어야 합니다.

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

# Deploy Azure Resource Manager template using template and parameter file locally
New-AzSubscriptionDeployment -Name <deploymentName> `
                 -Location <AzureRegion> `
                 -TemplateFile <pathToTemplateFile> `
                 -TemplateParameterFile <pathToParameterFile> `
                 -Verbose

# Deploy Azure Resource Manager template that is located externally
New-AzSubscriptionDeployment -Name <deploymentName> `
                 -Location <AzureRegion> `
                 -TemplateUri <templateUri> `
                 -TemplateParameterUri <parameterUri> `
                 -Verbose
```

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

# Deploy Azure Resource Manager template using template and parameter file locally
az deployment sub create --name <deploymentName> \
                         --location <AzureRegion> \
                         --template-file <pathToTemplateFile> \
                         --parameters <parameters/parameterFile> \
                         --verbose

# Deploy external Azure Resource Manager template, with local parameter file
az deployment sub create --name <deploymentName> \
                         --location <AzureRegion> \
                         --template-uri <templateUri> \
                         --parameters <parameterFile> \
                         --verbose
```

## <a name="confirm-successful-onboarding"></a>온보딩 성공 확인

고객 구독이 Azure Lighthouse에 성공적으로 온보딩되면 서비스 공급자의 테넌트에 있는 사용자는 구독 및 해당 리소스를 볼 수 있습니다(개별적으로 또는 해당 권한이 있는 Azure AD 그룹의 멤버로서 위의 프로세스를 통해 액세스 권한이 부여된 경우). 이를 확인하려면 다음 방법 중 하나를 사용하여 구독이 표시되는지 확인합니다.  

### <a name="azure-portal"></a>Azure portal

서비스 공급자의 테넌트에서 다음을 수행합니다.

1. [내 고객 페이지](view-manage-customers.md)로 이동합니다.
2. **고객** 을 선택합니다.
3. Resource Manager 템플릿에서 제공한 제품 이름의 구독을 볼 수 있는지 확인합니다.

> [!IMPORTANT]
> [내 고객](view-manage-customers.md)에서 위임된 구독을 보려면 구독이 온보딩될 때 서비스 공급자의 테넌트에 있는 사용자에게 [읽기 권한자](../../role-based-access-control/built-in-roles.md#reader) 역할(또는 읽기 권한자 액세스를 포함하는 다른 기본 제공 역할)을 부여해야 합니다.

고객의 테넌트에서 다음을 수행합니다.

1. [서비스 공급자 페이지](view-manage-service-providers.md)로 이동합니다.
2. **서비스 공급자 제품** 을 선택합니다.
3. Resource Manager 템플릿에서 제공한 제품 이름의 구독을 볼 수 있는지 확인합니다.

> [!NOTE]
> 배포가 완료되고 업데이트가 Azure Portal에 반영되기까지 최대 15분이 걸릴 수 있습니다. 브라우저를 새로 고치거나 로그인 및 로그아웃하거나 새 토큰을 요청하여 Azure Resource Manager 토큰을 업데이트하면 더 빨리 업데이트를 확인할 수 있습니다.

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

Get-AzContext

# Confirm successful onboarding for Azure Lighthouse

Get-AzManagedServicesDefinition
Get-AzManagedServicesAssignment
```

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

az account list
```

고객이 온보딩된 후 변경해야 하는 경우 [위임을 업데이트](update-delegation.md)할 수 있습니다. [위임에 대한 액세스를 완전히 제거](remove-delegation.md)할 수도 있습니다.

## <a name="troubleshooting"></a>문제 해결

고객을 제대로 온보딩할 수 없거나 사용자가 위임된 리소스에 액세스하는 데 문제가 있으면 다음 팁과 요구 사항을 확인하고 다시 시도합니다.

- `managedbyTenantId` 값은 온보드 중인 구독의 테넌트 ID와 달라야 합니다.
- 같은 범위에서 `mspOfferName`이 같은 할당이 여러 개일 수 없습니다.
- 위임된 구독에 대해 **Microsoft.ManagedServices** 리소스 공급자를 등록해야 합니다. 등록은 배포 중에 자동으로 수행되어야 하지만, 그렇지 않으면 [수동으로 등록](../../azure-resource-manager/management/resource-providers-and-types.md#register-resource-provider)할 수 있습니다.
- 권한 부여에는 [소유자](../../role-based-access-control/built-in-roles.md#owner) 기본 제공 역할이 있는 사용자나 [DataActions](../../role-based-access-control/role-definitions.md#dataactions)가 있는 기본 제공 역할이 포함되지 않아야 합니다.
- [**그룹 형식**](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md#group-types)을 **Microsoft 365** 가 아니라 **보안** 으로 설정하여 그룹을 만들어야 합니다.
- [중첩된 그룹](../..//active-directory/fundamentals/active-directory-groups-membership-azure-portal.md)에 대한 액세스가 사용으로 설정되기 전에 추가로 지연될 수 있습니다.
- Azure Portal에서 리소스를 확인해야 하는 사용자는 [독자](../../role-based-access-control/built-in-roles.md#reader) 역할(또는 독자 액세스 권한을 포함하는 다른 기본 제공 역할)이 있어야 합니다.
- 권한 부여에 포함하는 [Azure 기본 제공 역할](../../role-based-access-control/built-in-roles.md)에는 사용되지 않는 역할이 포함되지 않아야 합니다. Azure 기본 제공 역할이 사용되지 않으면 해당 역할로 온보딩된 모든 사용자는 액세스 권한을 잃게 되며 추가 위임을 온보딩할 수 없습니다. 이 문제를 해결하려면 지원되는 기본 제공 역할만 사용하도록 템플릿을 업데이트한 다음, 새 배포를 수행합니다.

## <a name="next-steps"></a>다음 단계

- [테넌트 간 관리 환경](../concepts/cross-tenant-management-experience.md)에 대해 알아봅니다.
- Azure Portal의 **내 고객** 으로 이동하여 [고객을 보고 관리](view-manage-customers.md)합니다.
- 위임을 [업데이트](update-delegation.md)하거나 [제거](remove-delegation.md)하는 방법에 관해 알아봅니다.
