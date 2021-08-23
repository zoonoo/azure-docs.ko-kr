---
title: 적격 권한 부여 만들기
description: Azure Lighthouse에 고객을 온보딩하는 경우 관리 테넌트의 사용자가 Just-In-Time 방식으로 해당 역할을 승격할 수 있습니다.
ms.date: 06/11/2021
ms.topic: how-to
ms.openlocfilehash: 938b0ae8f2d105d79237164287b00ec4fdf4d607
ms.sourcegitcommit: 23040f695dd0785409ab964613fabca1645cef90
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/14/2021
ms.locfileid: "112060755"
---
# <a name="create-eligible-authorizations"></a>적격 권한 부여 만들기

Azure Lighthouse에 고객을 온보딩하는 경우 관리 테넌트의 사용자에게 지정된 Azure 기본 제공 역할을 부여하는 권한 부여를 만듭니다. 또한 [Azure AD(Azure Active Directory) PIM(Privileged Identity Management)](../../active-directory/privileged-identity-management/pim-configure.md)을 사용하여 관리 테넌트의 사용자가 역할을 일시적으로 승격할 수 있는 적격 권한 부여를 만들 수도 있습니다. 이렇게 하면 사용자가 해당 권한만 보유하도록 Just-In-Time 기준으로 추가 권한을 부여할 수 있습니다.

적격 권한 부여를 만들면 권한 있는 역할에 대한 사용자의 영구 할당 수를 최소화하여 테넌트의 사용자로 인한 권한 있는 액세스와 관련된 보안 위험을 줄일 수 있습니다.

이 항목에서는 적격 권한 부여가 작동하는 방법 및 [Azure Lighthouse에 고객을 온보딩할](onboard-customer.md) 때 이러한 권한 부여를 만드는 방법을 설명합니다.

> [!IMPORTANT]
> 적격 권한 부여는 현재 퍼블릭 미리 보기로 제공됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="license-requirements"></a>라이선스 요구 사항

적격 권한 부여를 만들려면 EMS E5(Enterprise Mobility + Security E5) 또는 Azure AD Premium P2 라이선스가 필요합니다. 요구 사항에 적합한 라이선스를 찾으려면 [Free, Basic 및 Premium 버전의 일반적으로 사용할 수 있는 기능 비교](https://azure.microsoft.com/pricing/details/active-directory/)를 참조하세요.

EMS E5 또는 Azure AD Premium P2 라이선스는 고객 테넌트가 아니라 관리하는 테넌트가 보유해야 합니다.

적격 역할과 관련된 추가 비용은 사용자가 해당 역할에 대한 액세스 권한을 승격한 기간 동안에만 적용됩니다.

사용자의 라이선스에 대한 자세한 내용은 [Privileged Identity Management를 사용하기 위한 라이선스 요구 사항](../../active-directory/privileged-identity-management/subscription-requirements.md)을 참조하세요.

## <a name="how-eligible-authorizations-work"></a>적격 권한 부여의 작동 방식

적격 권한 부여는 권한 있는 작업을 수행해야 할 때 사용자가 역할을 활성화해야 하는 역할 할당을 정의합니다. 적격 역할을 활성화하면 지정된 기간 동안 해당 역할이 부여한 모든 권한을 갖게 됩니다.

고객 테넌트의 사용자는 온보딩 프로세스 이전에 적격 권한 부여의 역할 할당을 비롯한 모든 역할 할당을 검토할 수 있습니다.

사용자가 적격 역할을 성공적으로 활성화하면 해당 범위의 영구 역할 할당 외에도 미리 구성된 기간 동안 위임된 범위에서 승격된 역할을 갖게 됩니다.

관리하는 테넌트의 관리자는 관리하는 테넌트에서 감사 로그를 확인하여 모든 Privileged Identity Management 활동을 검토할 수 있습니다. 고객은 위임된 구독에 대한 Azure 활동 로그에서 이러한 작업을 볼 수 있습니다.

적격 권한 부여를 만들 때 사용자, 역할 및 액세스 정책의 세 가지 요소를 정의합니다.

- **사용자** 는 관리 테넌트의 개별 사용자이거나 Azure AD 그룹일 수 있습니다. 그룹이 정의된 경우 해당 그룹의 모든 멤버는 액세스 정책에 따라 역할에 대한 고유한 개별 액세스 권한을 승격할 수 있습니다. 서비스 주체에는 적격 권한 부여를 사용할 수 없습니다.
- **역할** 은 사용자 액세스 관리자를 제외하고 Azure 위임 리소스 관리가 지원되는 모든 Azure 기본 제공 역할일 수 있습니다.
- **액세스 정책** 은 MFA(다단계 인증) 요구 사항 및 만료되기 전에 사용자가 역할에서 활성화되는 기간을 정의합니다. 모든 역할에 지정할 수 있는 최대 기간은 8시간입니다.

이러한 요소 및 정의 방법에 대한 자세한 내용은 아래에 설명되어 있습니다.

## <a name="create-eligible-authorizations-using-azure-resource-manager-templates"></a>Azure Resource Manager 템플릿을 사용하여 적격 권한 부여 만들기

Azure Lighthouse 고객을 온보딩하려면 수정하는 [해당 매개 변수 파일과 함께 Azure Resource Manager 템플릿](onboard-customer.md#create-an-azure-resource-manager-template)을 사용합니다. 선택하는 템플릿은 전체 구독, 리소스 그룹 또는 구독 내의 여러 리소스 그룹 중 어떤 항목을 온보딩할지에 따라 달라집니다.

> [!NOTE]
> Azure Marketplace의 관리형 서비스 제품을 사용하여 고객을 온보딩할 수도 있지만, 현재 해당 제품에 적격 권한 부여를 포함할 수 없습니다.

고객을 온보딩할 때 적격 권한 부여를 포함하려면 [샘플 리포지토리의 delegated-resource-management-eligible-authorizations 섹션](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/delegated-resource-management-eligible-authorizations)에 있는 템플릿 중 하나를 사용합니다.

|온보딩하려면(적격 권한 부여 사용)  |사용하는 Azure Resource Manager 템플릿  |수정할 매개 변수 파일 |
|---------|---------|---------|
|Subscription   |[subscription.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/delegated-resource-management-eligible-authorizations/subscription/subscription.json)  |[subscription.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/delegated-resource-management-eligible-authorizations/subscription/subscription.Parameters.json)    |
|Resource group   |[rg.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/delegated-resource-management-eligible-authorizations/rg/rg.json)  |[rg.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/delegated-resource-management-eligible-authorizations/rg/rg.parameters.json)    |
|구독 내의 여러 리소스 그룹   |[multiple-rg.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/delegated-resource-management-eligible-authorizations/rg/multiple-rg.json)  |[multiple-rg.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/delegated-resource-management-eligible-authorizations/rg/multiple-rg.parameters.json)    |

적격 권한 부여를 사용하여 구독을 온보딩하는 데 사용할 수 있는 **subscription.json** 템플릿은 다음과 같습니다. 

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-08-01/subscriptionDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "mspOfferName": {
            "type": "string",
            "metadata": {
                "description": "Specify a unique name for your offer"
            },
            "defaultValue": "<to be filled out by MSP> Specify a title for your offer"
        },
        "mspOfferDescription": {
            "type": "string",
            "metadata": {
                "description": "Name of the Managed Service Provider offering"
            },
            "defaultValue": "<to be filled out by MSP> Provide a brief description of your offer"
        },
        "managedByTenantId": {
            "type": "string",
            "metadata": {
                "description": "Specify the tenant id of the Managed Service Provider"
            },
            "defaultValue": "<to be filled out by MSP> Provide your tenant id"
        },
        "authorizations": {
            "type": "array",
            "metadata": {
                "description": "Specify an array of objects, containing tuples of Azure Active Directory principalId, a Azure roleDefinitionId, and an optional principalIdDisplayName. The roleDefinition specified is granted to the principalId in the provider's Active Directory and the principalIdDisplayName is visible to customers."
            },
            "defaultValue": [
                { 
                    "principalId": "00000000-0000-0000-0000-000000000000", 
                    "roleDefinitionId": "acdd72a7-3385-48ef-bd42-f606fba81ae7",
                    "principalIdDisplayName": "PIM_Group" 
                }, 
                { 
                    "principalId": "00000000-0000-0000-0000-000000000000", 
                    "roleDefinitionId": "91c1777a-f3dc-4fae-b103-61d183457e46",
                    "principalIdDisplayName": "PIM_Group" 
                }   
            ]
        }, 
        "eligibleAuthorizations": { 
            "type": "array", 
            "metadata": { 
                "description": "Provide the authorizations that will have just-in-time role assignments on customer environments" 
            },
           "defaultValue": [ 
                { 
                        "justInTimeAccessPolicy": { 
                            "multiFactorAuthProvider": "Azure", 
                            "maximumActivationDuration": "PT8H" 
                        },
                        "principalId": "00000000-0000-0000-0000-000000000000", 
                        "principalIdDisplayName": "PIM_Group",
                        "roleDefinitionId": "36243c78-bf99-498c-9df9-86d9f8d28608" 
                        
                }                    
            ]    

        }                 
    },
    "variables": {
        "mspRegistrationName": "[guid(parameters('mspOfferName'))]",
        "mspAssignmentName": "[guid(parameters('mspOfferName'))]"
    },
    "resources": [
        {
            "type": "Microsoft.ManagedServices/registrationDefinitions",
            "apiVersion": "2020-02-01-preview",
            "name": "[variables('mspRegistrationName')]",
            "properties": {
                "registrationDefinitionName": "[parameters('mspOfferName')]",
                "description": "[parameters('mspOfferDescription')]",
                "managedByTenantId": "[parameters('managedByTenantId')]",
                "authorizations": "[parameters('authorizations')]", 
                "eligibleAuthorizations": "[parameters('eligibleAuthorizations')]" 
            }
        },
        {
            "type": "Microsoft.ManagedServices/registrationAssignments",
            "apiVersion": "2020-02-01-preview",
            "name": "[variables('mspAssignmentName')]",
            "dependsOn": [
                "[resourceId('Microsoft.ManagedServices/registrationDefinitions/', variables('mspRegistrationName'))]"
            ],
            "properties": {
                "registrationDefinitionId": "[resourceId('Microsoft.ManagedServices/registrationDefinitions/', variables('mspRegistrationName'))]"
            }
        }
    ],
    
    "outputs": {
        "mspOfferName": {
            "type": "string",
            "value": "[concat('Managed by', ' ', parameters('mspOfferName'))]"
        },
        "authorizations": {
            "type": "array",
            "value": "[parameters('authorizations')]"
        }, 
        "eligibleAuthorizations": { 
            "type": "array", 
            "value": "[parameters('eligibleAuthorizations')]" 

        } 
    }
}
```

### <a name="define-eligible-authorizations-in-your-parameters-file"></a>parameters 파일에서 적격 권한 부여 정의

구독을 온보딩할 때 [ subscription.Parameters.json 샘플 템플릿](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/delegated-resource-management-eligible-authorizations/subscription/subscription.Parameters.json)을 사용하여 적격 권한 부여를 비롯한 권한 부여를 정의할 수 있습니다.

각 적격 권한 부여는 `eligibleAuthorizations` 매개 변수에서 정의해야 합니다. 이 예제는 한 가지 적격 권한 부여를 포함합니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "mspOfferName": {
            "value": "Relecloud Managed Services"
        },
        "mspOfferDescription": {
            "value": "Relecloud Managed Services"
        },
        "managedByTenantId": {
            "value": "<insert the managing tenant id>"
        },
        "authorizations": {
            "value": [
                { 
                    "principalId": "00000000-0000-0000-0000-000000000000",
                    "roleDefinitionId": "acdd72a7-3385-48ef-bd42-f606fba81ae7",
                    "principalIdDisplayName": "PIM group"
                }
            ]
        }, 
        "eligibleAuthorizations":{
            "value": [
                {
                        "justInTimeAccessPolicy": {
                            "multiFactorAuthProvider": "Azure",
                            "maximumActivationDuration": "PT8H"
                        },
                        "principalId": "00000000-0000-0000-0000-000000000000", 
                        "principalIdDisplayName": "Tier 2 Support",
                        "roleDefinitionId": "b24988ac-6180-42a0-ab88-20f7382dd24c"

                }
            ]
        }
    }
}
```

`eligibleAuthorizations` 매개 변수 내에서 `principalId`는 적격 권한 부여가 적용될 Azure AD 사용자 또는 그룹의 ID를 지정합니다. 현재 서비스 주체 계정에서 액세스 권한을 승격하고 적격 역할을 사용할 수 있는 방법이 없기 때문에 서비스 주체 계정의 ID는 사용하지 않도록 합니다.

> [!IMPORTANT]
> 읽기 권한자(또는 읽기 권한자 액세스 권한을 포함하는 다른 Azure 기본 제공 역할)와 같은 적격 권한 부여의 다른 역할이 있는 템플릿의 `authorizations` 섹션에 동일한 `principalId`를 포함해야 합니다. 그렇지 않으면 해당 사용자는 Azure Portal에서 역할을 승격할 수 없습니다.

`roleDefinitionId`에는 사용자가 Just-In-Time 방식으로 사용할 수 있는 [Azure 기본 제공 역할](../../role-based-access-control/built-in-roles.md)에 대한 역할 정의 ID가 포함됩니다.

`justInTimeAccessPolicy`는 다음과 같은 두 가지 요소를 지정합니다.

- `multiFactorAuthProvider`는 Azure MFA(Multi-Factor Authentication)를 사용하는 인증을 요구하는 **Azure** 로 설정하거나, 다단계 인증이 필요하지 않을 경우 **None** 으로 설정할 수 있습니다.
- `maximumActivationDuration`은 사용자가 적격 역할을 보유하게 될 총 기간을 설정합니다. 이 값은 ISO 8601 기간 형식을 사용해야 합니다. 최솟값은 PT30M(30분)이고 최댓값은 PT8H(8시간)입니다.

> [!NOTE]
> 참고: Just-In-Time 액세스는 사용자 액세스 관리자가 [관리 ID에 할당](deploy-policy-remediation.md)할 수 있는 `delegatedRoleDefinitionIds`에는 적용되지 않습니다. 이러한 역할 할당은 적격 권한 부여로 만들 수 없습니다. 마찬가지로 사용자 액세스 관리자 역할 자체에 대한 적격 권한 부여를 만들 수 없습니다.

## <a name="elevation-process-for-users"></a>사용자에 대한 권한 상승 프로세스

Azure Lighthouse에 고객을 온보딩하면 포함된 적격 역할을 지정된 사용자(또는 지정된 그룹의 사용자)가 사용할 수 있게 됩니다.

각 사용자는 언제든지 Azure Portal의 **내 고객** 페이지를 방문하여 위임을 선택한 다음, **적격 역할 관리** 를 선택하여 액세스 권한을 승격할 수 있습니다. 그런 다음, 단계에 따라 Azure AD Privileged Identity Management에서 [역할을 활성화](../../active-directory/privileged-identity-management/pim-how-to-activate-role.md)할 수 있습니다.

:::image type="content" source="../media/manage-eligible-roles.png" alt-text="Azure Portal의 적격 역할 관리 단추를 보여 주는 스크린샷":::

적격 역할이 활성화되면 사용자는 적격 권한 부여에 지정된 전체 기간 동안 해당 역할을 갖게 됩니다. 이 기간이 지나면 권한 상승 프로세스를 반복하여 액세스 권한을 다시 승격하지 않는 한, 더 이상 해당 역할을 사용할 수 없게 됩니다.

## <a name="next-steps"></a>다음 단계

- [ARM 템플릿을 사용하여 Azure Lighthouse에 고객을 온보딩](onboard-customer.md)하는 방법을 알아봅니다.
- [Azure AD Privileged Identity Management](../../active-directory/privileged-identity-management/pim-configure.md)에 대해 자세히 알아봅니다.
- [Azure Lighthouse의 테넌트, 사용자 및 역할](../concepts/tenants-users-roles.md)에 대해 자세히 알아봅니다.