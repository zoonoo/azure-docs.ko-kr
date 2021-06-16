---
title: 관리 ID를 사용하는 관리형 앱
description: 기존 리소스에 연결하고 Azure 리소스를 관리하고 활동 로그에 운영 ID를 제공하기 위해 관리 ID를 사용하여 관리형 애플리케이션을 구성합니다.
ms.topic: conceptual
ms.author: jobreen
author: jjbfour
ms.date: 05/13/2019
ms.openlocfilehash: 018995ed973679a617b409b82e076198a558eef7
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111968779"
---
# <a name="azure-managed-application-with-managed-identity"></a>관리 ID를 사용하는 Azure 관리형 애플리케이션

> [!NOTE]
> 관리형 애플리케이션에 대한 관리 ID 지원은 현재 미리 보기로 제공됩니다. 관리 ID를 활용하려면 2018-09-01-preview API 버전을 사용하세요.

관리 ID를 포함하도록 관리형 애플리케이션을 구성하는 방법을 알아봅니다. 관리 ID를 사용하여 고객이 추가적인 기존 리소스에 대한 관리형 애플리케이션 액세스 권한을 부여할 수 있습니다. ID는 Azure 플랫폼에서 관리하며 비밀을 프로비전하거나 회전할 필요가 없습니다. AAD(Azure Active Directory)의 관리 ID에 관한 자세한 내용은 [Azure 리소스에 대한 관리 ID](../../active-directory/managed-identities-azure-resources/overview.md)를 참조하세요.

애플리케이션에 두 가지 형식의 ID를 부여할 수 있습니다.

- **시스템 할당 ID** 는 애플리케이션에 연결되어 있어 해당 앱을 삭제하면 이 ID도 삭제됩니다. 앱에는 하나의 시스템 할당 ID만 있을 수 있습니다.
- **사용자 할당 ID** 는 앱에 할당할 수 있는 독립 실행형 Azure 리소스입니다. 앱에는 여러 사용자 할당 ID가 있을 수 있습니다.

## <a name="how-to-use-managed-identity"></a>관리 ID를 사용하는 방법

관리 ID를 통해 관리형 애플리케이션에 대한 많은 시나리오를 사용할 수 있습니다. 해결할 수 있는 몇 가지 일반적인 시나리오는 다음과 같습니다.

- 기존 Azure 리소스에 연결된 관리형 애플리케이션 배포. [기존 네트워크 인터페이스](../../virtual-network/virtual-network-network-interface-vm.md)에 연결된 관리형 애플리케이션 내에서 Azure VM(가상 머신)을 배포하는 경우를 예로 들 수 있습니다.
- 관리형 애플리케이션 및 게시자에게 **관리형 리소스 그룹** 외부의 Azure 리소스에 대한 액세스 권한 부여.
- Azure 내 활동 로그 및 기타 서비스에 대한 관리형 애플리케이션의 운영 ID를 제공합니다.

## <a name="adding-managed-identity"></a>관리 ID 추가

관리 ID를 사용하여 관리형 애플리케이션을 만들려면 Azure 리소스에서 추가적인 속성을 설정해야 합니다. 다음 예제에서는 샘플 **ID** 속성을 보여 줍니다.

```json
{
"identity": {
    "type": "SystemAssigned, UserAssigned",
    "userAssignedIdentities": {
        "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.ManagedIdentity/userassignedidentites/myuserassignedidentity": {}
    }
}
```

**ID** 를 사용하여 관리형 애플리케이션을 만드는 두 가지 일반적인 방법으로는 [CreateUIDefinition.json](./create-uidefinition-overview.md) 및 [Azure Resource Manager 템플릿](../templates/syntax.md)이 있습니다. 간단한 단일 만들기 시나리오의 경우에는 보다 풍부한 환경을 제공하므로 CreateUIDefinition을 사용하여 관리 ID를 사용하도록 설정해야 합니다. 그러나 자동화된 애플리케이션이나 여러 관리형 애플리케이션 배포가 필요한 고급 또는 복합 시스템을 처리할 때는 템플릿을 사용할 수 있습니다.

### <a name="using-createuidefinition"></a>CreateUIDefinition 사용

관리형 애플리케이션은 [CreateUIDefinition.json](./create-uidefinition-overview.md)에서 관리 ID를 사용하여 구성할 수 있습니다. [출력 섹션](./create-uidefinition-overview.md#outputs)에서 `managedIdentity` 키를 사용하여 관리형 애플리케이션 템플릿의 ID 속성을 재정의할 수 있습니다. 아래 샘플에서는 관리형 애플리케이션에서 **시스템이 할당** 한 ID를 사용하도록 설정합니다. 더 복잡한 ID 개체는 소비자에게 입력을 요청하는 CreateUIDefinition 요소를 사용하여 구성할 수 있습니다. 이러한 입력은 **사용자 할당 ID** 를 사용하여 관리형 애플리케이션을 구성하는 데 사용할 수 있습니다.

```json
"outputs": {
    "managedIdentity": { "Type": "SystemAssigned" }
}
```

#### <a name="when-to-use-createuidefinition-for-managed-identity"></a>관리 ID에 대해 CreateUIDefinition을 사용하는 경우

관리형 애플리케이션에서 관리 ID를 사용하도록 설정하기 위해 CreateUIDefinition을 사용하는 경우에 대한 몇 가지 권장 사항이 아래에 나와 있습니다.

- 관리형 애플리케이션 생성은 Azure Portal 또는 마켓플레이스를 거칩니다.
- 관리 ID에는 복잡한 소비자 입력이 필요합니다.
- 관리형 애플리케이션을 만들려면 관리 ID가 필요합니다.

#### <a name="managed-identity-createuidefinition-control"></a>관리 ID CreateUIDefinition 컨트롤

CreateUIDefinition은 기본 제공 [관리 ID 컨트롤](./microsoft-managedidentity-identityselector.md)을 지원합니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.0.1-preview",
  "parameters": {
    "basics": [],
    "steps": [
      {
        "name": "applicationSettings",
        "label": "Application Settings",
        "subLabel": {
          "preValidation": "Configure your application settings",
          "postValidation": "Done"
        },
        "bladeTitle": "Application Settings",
        "elements": [
          {
            "name": "appName",
            "type": "Microsoft.Common.TextBox",
            "label": "Managed application Name",
            "toolTip": "Managed application instance name",
            "visible": true
          },
          {
            "name": "appIdentity",
            "type": "Microsoft.ManagedIdentity.IdentitySelector",
            "label": "Managed Identity Configuration",
            "toolTip": {
              "systemAssignedIdentity": "Enable system assigned identity to grant the managed application access to additional existing resources.",
              "userAssignedIdentity": "Add user assigned identities to grant the managed application access to additional existing resources."
            },
            "defaultValue": {
              "systemAssignedIdentity": "Off"
            },
            "options": {
              "hideSystemAssignedIdentity": false,
              "hideUserAssignedIdentity": false,
              "readOnlySystemAssignedIdentity": false
            },
            "visible": true
          }
        ]
      }
    ],
    "outputs": {
      "applicationResourceName": "[steps('applicationSettings').appName]",
      "location": "[location()]",
      "managedIdentity": "[steps('applicationSettings').appIdentity]"
    }
  }
}
```

![관리 ID CreateUIDefinition](./media/publish-managed-identity/msi-cuid.png)

### <a name="using-azure-resource-manager-templates"></a>Azure Resource Manager 템플릿 사용

> [!NOTE]
> Marketplace 관리형 애플리케이션 템플릿은 Azure Portal 만들기 환경을 통해 고객에게 자동으로 생성됩니다.
> 이러한 시나리오의 경우 ID를 사용하도록 설정하려면 `managedIdentity` 출력 키를 사용해야 합니다.

Azure Resource Manager 템플릿을 통해 관리 ID를 사용하도록 설정할 수도 있습니다. 아래 샘플에서는 관리형 애플리케이션에서 **시스템이 할당** 한 ID를 사용하도록 설정합니다. 더 복잡한 ID 개체는 Azure Resource Manager 템플릿 매개 변수를 사용하여 입력을 제공하여 구성될 수 있습니다. 이러한 입력은 **사용자 할당 ID** 를 사용하여 관리형 애플리케이션을 구성하는 데 사용할 수 있습니다.

#### <a name="when-to-use-azure-resource-manager-templates-for-managed-identity"></a>관리 ID에 Azure Resource Manager 템플릿을 사용해야 하는 경우

다음은 관리형 애플리케이션에서 관리 ID를 사용하도록 설정하기 위해 Azure Resource Manager 템플릿을 사용해야 하는 경우에 대한 권장 사항입니다.

- 템플릿을 기반으로 관리형 애플리케이션을 프로그래밍 방식으로 배포할 수 있습니다.
- 관리형 애플리케이션을 프로비저닝하려면 관리 ID에 대한 사용자 지정 역할 할당이 필요합니다.
- 관리형 애플리케이션에는 Azure Portal 및 마켓플레이스 생성 흐름이 필요하지 않습니다.

#### <a name="systemassigned-template"></a>SystemAssigned 템플릿

**시스템 할당** ID를 사용하여 관리형 애플리케이션을 배포하는 기본 Azure Resource Manager 템플릿입니다.

```json
"resources": [
    {
        "type": "Microsoft.Solutions/applications",
        "name": "[parameters('applicationName')]",
        "apiVersion": "2018-09-01-preview",
        "location": "[parameters('location')]",
        "identity": {
            "type": "SystemAssigned"
        },
        "properties": {
            "ManagedResourceGroupId": "[parameters('managedByResourceGroupId')]",
            "parameters": { }
        }
    }
]
```

### <a name="userassigned-template"></a>UserAssigned 템플릿

**사용자 할당** ID를 사용하여 관리형 애플리케이션을 배포하는 기본 Azure Resource Manager 템플릿입니다.

```json
"resources": [
    {
      "type": "Microsoft.ManagedIdentity/userAssignedIdentities",
      "name": "[parameters('managedIdentityName')]",
      "apiVersion": "2018-11-30",
      "location": "[parameters('location')]"
    },
    {
        "type": "Microsoft.Solutions/applications",
        "name": "[parameters('applicationName')]",
        "apiVersion": "2018-09-01-preview",
        "location": "[parameters('location')]",
        "identity": {
            "type": "UserAssigned",
            "userAssignedIdentities": {
                "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',parameters('managedIdentityName'))]": {}
            }
        },
        "properties": {
            "ManagedResourceGroupId": "[parameters('managedByResourceGroupId')]",
            "parameters": { }
        }
    }
]
```

## <a name="granting-access-to-azure-resources"></a>Azure 리소스에 대한 액세스 권한 부여

관리형 애플리케이션에 ID가 부여되면 기존 Azure 리소스에 대한 액세스 권한을 부여할 수 있습니다. 해당 프로세스는 Azure Portal의 IAM(액세스 제어) 인터페이스를 통해 수행할 수 있습니다. 관리형 애플리케이션 또는 **사용자 할당 ID** 의 이름을 검색하여 역할 할당을 추가할 수 있습니다.

![관리형 애플리케이션에 대한 역할 할당 추가](./media/publish-managed-identity/identity-role-assignment.png)

## <a name="linking-existing-azure-resources"></a>기존 Azure 리소스 연결

> [!NOTE]
> 관리형 애플리케이션을 배포하기 전에 **사용자 할당 ID** 를 [구성](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)해야 합니다. 또한 관리형 애플리케이션의 연결된 리소스 배포는 **마켓플레이스** 종류에 대해서만 지원됩니다.

관리 ID를 사용하여 배포 중에 기존 리소스에 액세스해야 하는 관리형 애플리케이션을 배포할 수도 있습니다. 고객이 관리형 애플리케이션을 프로비저닝할 때 **사용자 할당 ID** 를 추가하여 **mainTemplate** 배포에 대한 추가적인 권한 부여를 할 수 있습니다.

### <a name="authoring-the-createuidefinition-with-a-linked-resource"></a>연결된 리소스를 사용하여 CreateUIDefinition 작성

관리형 애플리케이션의 배포를 기존 리소스에 연결할 때 기존 Azure 리소스와 해당 리소스에 대한 해당 역할 할당을 가진 **사용자 할당 ID** 를 모두 제공해야 합니다.

 네트워크 인터페이스 리소스 ID와 사용자 할당 ID 리소스 ID라는 두 가지 입력이 필요한 샘플 CreateUIDefinition입니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
    "handler": "Microsoft.Compute.MultiVm",
    "version": "0.1.2-preview",
    "parameters": {
        "basics": [
            {}
        ],
        "steps": [
            {
                "name": "managedApplicationSetting",
                "label": "Managed Application Settings",
                "subLabel": {
                    "preValidation": "Managed Application Settings",
                    "postValidation": "Done"
                },
                "bladeTitle": "Managed Application Settings",
                "elements": [
                    {
                        "name": "networkInterfaceId",
                        "type": "Microsoft.Common.TextBox",
                        "label": "network interface resource id",
                        "defaultValue": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Network/networkInterfaces/existingnetworkinterface",
                        "toolTip": "Must represent the identity as an Azure Resource Manager resource identifer format ex. /subscriptions/sub1/resourcegroups/myGroup/providers/Microsoft.Network/networkInterfaces/networkinterface1",
                        "visible": true
                    },
                    {
                        "name": "userAssignedId",
                        "type": "Microsoft.Common.TextBox",
                        "label": "user assigned identity resource id",
                        "defaultValue": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.ManagedIdentity/userassignedidentites/myuserassignedidentity",
                        "toolTip": "Must represent the identity as an Azure Resource Manager resource identifer format ex. /subscriptions/sub1/resourcegroups/myGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/identity1",
                        "visible": true
                    }
                ]
            }
        ],
        "outputs": {
            "existingNetworkInterfaceId": "[steps('managedApplicationSetting').networkInterfaceId]",
            "managedIdentity": "[parse(concat('{\"Type\":\"UserAssigned\",\"UserAssignedIdentities\":{',string(steps('managedApplicationSetting').userAssignedId),':{}}}'))]"
        }
    }
}
```

CreateUIDefinition.json에서는 두 개의 필드가 있는 만들기 사용자 환경을 생성합니다. 사용자는 첫 번째 필드를 사용하여 관리형 애플리케이션 배포에 연결되는 리소스에 대한 Azure 리소스 ID를 입력할 수 있습니다. 두 번째는 소비자가 연결된 Azure 리소스에 대한 액세스 권한이 있는 **사용자 할당 ID** Azure 리소스 ID를 입력하는 것입니다. 생성된 환경은 다음과 같습니다.

![네트워크 인터페이스 리소스 ID와 사용자 할당 ID 리소스 ID라는 두 가지 입력이 있는 샘플 CreateUIDefinition](./media/publish-managed-identity/network-interface-cuid.png)

### <a name="authoring-the-maintemplate-with-a-linked-resource"></a>연결된 리소스를 사용하여 mainTemplate 작성

CreateUIDefinition을 업데이트하는 것 외에도 기본 템플릿은 전달된 연결된 리소스 ID를 수락하도록 업데이트되어야 합니다. 새 매개 변수를 추가하여 새 출력을 허용하도록 기본 템플릿을 업데이트할 수 있습니다. `managedIdentity` 출력은 생성된 관리형 애플리케이션 템플릿의 값을 재정의하므로 기본 템플릿에 전달되지 않으며 매개 변수 섹션에 포함되지 않아야 합니다.

CreateUIDefinition에서 제공하는 기존 네트워크 인터페이스로 네트워크 프로필을 설정하는 샘플 기본 템플릿입니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "existingNetworkInterfaceId": { "type": "string" }
    },
    "variables": {
    },
    "resources": [
        {
            "apiVersion": "2016-04-30-preview",
            "type": "Microsoft.Compute/virtualMachines",
            "name": "myLinkedResourceVM",
            "location": "[resourceGroup().location]",
            "properties": {
                …,
                "networkProfile": {
                    "networkInterfaces": [
                        {
                            "id": "[parameters('existingNetworkInterfaceId')]"
                        }
                    ]
                }
            }
        }
    ]
}
```

### <a name="consuming-the-managed-application-with-a-linked-resource"></a>연결된 리소스를 통해 관리형 애플리케이션 사용

관리형 애플리케이션 패키지를 만든 후에는 Azure Portal을 통해 관리형 애플리케이션을 사용할 수 있습니다. 이를 사용할 수 있도록 하려면 몇 가지 필수 단계가 있습니다.

- 필요한 연결된 Azure 리소스의 인스턴스를 만들어야 합니다.
- **사용자 할당 ID** 를 [만들고 연결된 리소스에 역할 할당이 지정](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)되어야 합니다.
- 기존의 연결된 리소스 ID 및 **사용자 할당 ID** 가 CreateUIDefinition에 제공됩니다.

## <a name="accessing-the-managed-identity-token"></a>관리 ID 토큰에 액세스

이제 게시자 테넌트의 `listTokens` API를 통해 관리형 애플리케이션의 토큰에 액세스할 수 있습니다. 예제 요청은 다음과 유사할 수 있습니다.

``` HTTP
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Solutions/applications/{applicationName}/listTokens?api-version=2018-09-01-preview HTTP/1.1

{
    "authorizationAudience": "https://management.azure.com/",
    "userAssignedIdentities": [
        "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{userAssignedIdentityName}"
    ]
}
```

요청 본문 매개 변수:

매개 변수 | 필수 | Description
---|---|---
authorizationAudience | *아니요* | 대상 리소스의 앱 ID URI입니다. 발급된 토큰의 `aud`(대상) 클레임이기도 합니다. 기본값은 https://management.azure.com/ 입니다.
userAssignedIdentities | *아니요* | 토큰을 검색할 사용자 할당 관리 ID 목록입니다. 지정하지 않으면 `listTokens`는 시스템 할당 관리 ID에 대한 토큰을 반환합니다.


샘플 응답은 다음과 유사할 수 있습니다.

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json

{
    "value": [
        {
            "access_token": "eyJ0eXAi…",
            "expires_in": "2…",
            "expires_on": "1557…",
            "not_before": "1557…",
            "authorizationAudience": "https://management.azure.com/",
            "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Solutions/applications/{applicationName}",
            "token_type": "Bearer"
        }
    ]
}
```

응답은 `value` 속성 아래에 토큰 배열을 포함합니다.

매개 변수 | Description
---|---
access_token | 요청된 액세스 토큰입니다.
expires_in | 액세스 토큰이 유효한 시간(초)입니다.
expires_on | 액세스 토큰이 만료되는 시간 범위입니다. Epoch의 초 수로 표시됩니다.
not_before | 액세스 토큰이 적용되는 시간 범위입니다. Epoch의 초 수로 표시됩니다.
authorizationAudience | 액세스 토큰이 요청된 `aud`(대상)입니다. 이는 `listTokens` 요청에서 제공된 것과 동일합니다.
resourceId | 발급된 토큰에 대한 Azure 리소스 ID입니다. 관리형 애플리케이션 ID 또는 사용자 할당 ID입니다.
token_type | 토큰 형식입니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [사용자 지정 공급자를 사용하여 관리형 애플리케이션을 구성하는 방법](../custom-providers/overview.md)