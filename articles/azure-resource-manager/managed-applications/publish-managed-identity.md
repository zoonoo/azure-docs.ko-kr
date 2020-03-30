---
title: 관리되는 ID를 가진 관리되는 응용 프로그램
description: 기존 리소스에 연결하고, Azure 리소스를 관리하고, 활동 로그에 대한 운영 ID를 제공하기 위해 관리되는 ID로 관리되는 응용 프로그램을 구성합니다.
ms.topic: conceptual
ms.author: jobreen
author: jjbfour
ms.date: 05/13/2019
ms.openlocfilehash: dbf75262440474c5cb50a6d733ac7cba212b5f3f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75651658"
---
# <a name="azure-managed-application-with-managed-identity"></a>관리되는 ID를 가진 Azure 관리 응용 프로그램

> [!NOTE]
> 관리되는 응용 프로그램에 대한 관리되는 ID 지원은 현재 미리 보기 상태입니다. 관리되는 ID를 활용하려면 2018-09-01 미리 보기 API 버전을 사용하십시오.

관리되는 ID를 포함하도록 관리되는 응용 프로그램을 구성하는 방법을 알아봅니다. 관리되는 ID를 사용하여 고객이 관리되는 응용 프로그램 액세스 권한을 추가 기존 리소스에 부여할 수 있습니다. ID는 Azure 플랫폼에서 관리하며 비밀을 프로비전하거나 회전할 필요가 없습니다. Azure Active Directory(AAD)에서 관리되는 ID에 대한 자세한 내용은 [Azure 리소스에 대한 관리ID를](../../active-directory/managed-identities-azure-resources/overview.md)참조하십시오.

애플리케이션에 두 가지 형식의 ID를 부여할 수 있습니다.

- **시스템 할당 ID**는 애플리케이션에 연결되어 있어 해당 앱을 삭제하면 이 ID도 삭제됩니다. 앱에는 하나의 시스템 할당 ID만 있을 수 있습니다.
- **사용자 할당된 ID는** 앱에 할당할 수 있는 독립 실행형 Azure 리소스입니다. 앱에는 여러 사용자 할당 ID가 있을 수 있습니다.

## <a name="how-to-use-managed-identity"></a>관리되는 ID를 사용하는 방법

관리되는 ID를 사용하면 관리되는 응용 프로그램에 대한 많은 시나리오를 사용할 수 있습니다. 해결할 수 있는 몇 가지 일반적인 시나리오는 다음과 같습니다.

- 기존 Azure 리소스에 연결된 관리되는 응용 프로그램 배포 예를 들어 [기존 네트워크 인터페이스에](../../virtual-network/virtual-network-network-interface-vm.md)연결된 관리되는 응용 프로그램 내에 Azure 가상 시스템(VM)을 배포하는 경우입니다.
- 관리되는 **리소스 그룹**외부의 Azure 리소스에 대한 관리되는 응용 프로그램 및 게시자 액세스 권한 부여.
- Azure 내의 활동 로그 및 기타 서비스에 대해 관리되는 응용 프로그램의 운영 ID를 제공합니다.

## <a name="adding-managed-identity"></a>관리되는 ID 추가

관리되는 ID로 관리되는 응용 프로그램을 만들려면 Azure 리소스에 추가 속성을 설정해야 합니다. 다음 예제에서는 샘플 **ID** 속성을 보여 주며 있습니다.

```json
{
"identity": {
    "type": "SystemAssigned, UserAssigned",
    "userAssignedIdentities": {
        "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.ManagedIdentity/userassignedidentites/myuserassignedidentity": {}
    }
}
```

**ID를**사용하여 관리되는 응용 프로그램을 만드는 [CreateUIDefinition.json](./create-uidefinition-overview.md) 방법에는 두 가지가 [있습니다.](../templates/template-syntax.md) 간단한 단일 만들기 시나리오의 경우 보다 풍부한 환경을 제공하기 때문에 CreateUIDefinition를 사용하여 관리되는 ID를 사용하도록 설정해야 합니다. 그러나 자동화되거나 여러 개의 관리되는 응용 프로그램 배포가 필요한 고급 또는 복잡한 시스템을 처리할 때템플릿을 사용할 수 있습니다.

### <a name="using-createuidefinition"></a>만들기 UI정의 사용

관리되는 응용 프로그램은 [CreateUIDefinition.json](./create-uidefinition-overview.md)을 통해 관리되는 ID로 구성할 수 있습니다. 출력 [섹션에서](./create-uidefinition-overview.md#outputs)키를 `managedIdentity` 사용하여 관리되는 응용 프로그램 템플릿의 ID 속성을 재정의할 수 있습니다. 샘플 벨로우는 관리되는 응용 프로그램에서 **시스템 할당** ID를 활성화합니다. CreateUIDefinition 요소를 사용하여 소비자에게 입력을 요청하여 보다 복잡한 ID 개체를 만들 수 있습니다. 이러한 입력은 **사용자 할당ID로**관리되는 응용 프로그램을 생성하는 데 사용할 수 있습니다.

```json
"outputs": {
    "managedIdentity": "[parse('{\"Type\":\"SystemAssigned\"}')]"
}
```

#### <a name="when-to-use-createuidefinition-for-managed-identity"></a>관리되는 ID에 CreateUIDEFINITION를 사용하는 경우

다음은 관리되는 응용 프로그램에서 관리되는 ID를 사용하도록 설정하기 위해 CreateUIDefinition를 사용하는 시기에 대한 몇 가지 권장 사항입니다.

- 관리되는 응용 프로그램 만들기는 Azure 포털 또는 마켓플레이스를 거칩니다.
- 관리되는 ID에는 복잡한 소비자 입력이 필요합니다.
- 관리되는 응용 프로그램을 만들 때 관리되는 ID가 필요합니다.

#### <a name="systemassigned-createuidefinition"></a>시스템 할당된 만들기 UI정의

관리되는 응용 프로그램에 대해 SystemAssigned ID를 사용할 수 있는 기본 CreateUIDefinition입니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
    "parameters": {
        "basics": [
            {}
        ],
        "steps": [
        ],
        "outputs": {
            "managedIdentity": "[parse('{\"Type\":\"SystemAssigned\"}')]"
        }
    }
}
```

#### <a name="userassigned-createuidefinition"></a>사용자 할당된 CreateUI정의

**사용자가 할당한 ID** 리소스를 입력으로 사용하며 관리되는 응용 프로그램에 대해 UserAssigned ID를 사용할 수 있는 기본 CreateUIDefinition입니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
    "parameters": {
        "basics": [
            {}
        ],
        "steps": [
            {
                "name": "manageIdentity",
                "label": "Identity",
                "subLabel": {
                    "preValidation": "Manage Identities",
                    "postValidation": "Done"
                },
                "bladeTitle": "Identity",
                "elements": [
                    {
                        "name": "userAssignedText",
                        "type": "Microsoft.Common.TextBox",
                        "label": "User assigned managed identity",
                        "defaultValue": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.ManagedIdentity/userassignedidentites/myuserassignedidentity",
                        "visible": true
                    }
                ]
            }
        ],
        "outputs": {
            "managedIdentity": "[parse(concat('{\"Type\":\"UserAssigned\",\"UserAssignedIdentities\":{',string(steps('manageIdentity').userAssignedText),':{}}}'))]"
        }
    }
}
```

위의 CreateUIDefinition.json은 소비자가 **사용자 할당ID** Azure 리소스 ID를 입력할 수 있는 텍스트 상자가 있는 만들기 사용자 환경을 생성합니다. 생성된 환경은 다음과 같습니다.

![샘플 사용자 할당 ID CreateUI정의](./media/publish-managed-identity/user-assigned-identity.png)

### <a name="using-azure-resource-manager-templates"></a>Azure 리소스 관리자 템플릿 사용

> [!NOTE]
> 마켓플레이스 관리되는 응용 프로그램 템플릿은 Azure 포털 만들기 환경을 통과하는 고객을 위해 자동으로 생성됩니다.
> 이러한 시나리오의 `managedIdentity` 경우 CreateUIDefinition의 출력 키를 사용하여 ID를 활성화해야 합니다.

관리되는 ID는 Azure 리소스 관리자 템플릿을 통해 활성화할 수도 있습니다. 샘플 벨로우는 관리되는 응용 프로그램에서 **시스템 할당** ID를 활성화합니다. Azure Resource Manager 템플릿 매개 변수를 사용하여 입력을 제공하여 보다 복잡한 ID 개체를 만들 수 있습니다. 이러한 입력은 **사용자 할당ID로**관리되는 응용 프로그램을 생성하는 데 사용할 수 있습니다.

#### <a name="when-to-use-azure-resource-manager-templates-for-managed-identity"></a>관리되는 ID에 Azure 리소스 관리자 템플릿을 사용하는 시기

다음은 관리되는 응용 프로그램에서 관리되는 ID를 사용하도록 설정하기 위해 Azure 리소스 관리자 템플릿을 사용하는 시기에 대한 몇 가지 권장 사항입니다.

- 관리되는 응용 프로그램은 템플릿을 기반으로 프로그래밍 방식으로 배포할 수 있습니다.
- 관리되는 응용 프로그램을 프로비전하려면 관리되는 ID에 대한 사용자 지정 역할 할당이 필요합니다.
- 관리되는 응용 프로그램에는 Azure 포털 및 마켓플레이스 만들기 흐름이 필요하지 않습니다.

#### <a name="systemassigned-template"></a>시스템 할당 템플릿

**시스템 할당** 된 ID와 함께 관리되는 응용 프로그램을 배포 하는 기본 Azure 리소스 관리자 템플릿입니다.

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

### <a name="userassigned-template"></a>사용자 할당된 템플릿

**사용자 할당된 ID로**관리되는 응용 프로그램을 배포하는 기본 Azure 리소스 관리자 템플릿입니다.

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

관리되는 응용 프로그램에 ID가 부여되면 기존 Azure 리소스에 대한 액세스 권한을 부여할 수 있습니다. 이 프로세스는 Azure 포털의 IAM(액세스 제어) 인터페이스를 통해 수행할 수 있습니다. 관리되는 응용 프로그램 또는 **사용자가 할당한 ID의** 이름을 검색하여 역할 할당을 추가할 수 있습니다.

![관리되는 응용 프로그램에 대한 역할 할당 추가](./media/publish-managed-identity/identity-role-assignment.png)

## <a name="linking-existing-azure-resources"></a>기존 Azure 리소스 연결

> [!NOTE]
> 관리되는 응용 프로그램을 배포하기 전에 **사용자 할당된 ID를** [구성해야](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md) 합니다. 또한 관리되는 응용 프로그램의 연결된 리소스 배포는 **마켓플레이스** 종류에 대해서만 지원됩니다.

관리되는 ID를 사용하여 배포 하는 동안 기존 리소스에 액세스 해야 하는 관리되는 응용 프로그램을 배포할 수도 있습니다. 고객이 관리되는 응용 프로그램을 프로비전할 때 **사용자 할당ID를** 추가하여 **mainTemplate** 배포에 추가 권한을 제공할 수 있습니다.

### <a name="authoring-the-createuidefinition-with-a-linked-resource"></a>연결된 리소스로 CreateUIDefinition 작성

관리되는 응용 프로그램의 배포를 기존 리소스에 연결할 때 기존 Azure 리소스와 해당 리소스에 적용 가능한 역할 할당이 있는 **사용자 할당 ID를** 모두 제공해야 합니다.

 네트워크 인터페이스 리소스 ID와 사용자 할당된 ID 리소스 ID의 두 가지 입력이 필요한 CreateUIDefinition 샘플입니다.

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

이 CreateUIDefinition.json두 개의 필드가 있는 사용자 환경을 생성합니다. 첫 번째 필드를 사용하면 관리되는 응용 프로그램 배포에 연결된 리소스에 대해 Azure 리소스 ID를 입력할 수 있습니다. 두 번째는 소비자가 연결된 Azure 리소스에 액세스할 수 있는 **사용자 할당된 ID** Azure 리소스 ID를 입력하는 것입니다. 생성된 환경은 다음과 같습니다.

![두 개의 입력을 가진 샘플 CreateUIDefinition: 네트워크 인터페이스 리소스 ID 및 사용자 할당된 ID 리소스 ID](./media/publish-managed-identity/network-interface-cuid.png)

### <a name="authoring-the-maintemplate-with-a-linked-resource"></a>연결된 리소스가 있는 기본 템플릿 작성

CreateUIDefinition를 업데이트하는 것 외에도 연결된 리소스 ID에서 전달된 템플릿을 수락하도록 기본 템플릿도 업데이트해야 합니다. 새 매개 변수를 추가하여 새 출력을 허용하도록 기본 템플릿을 업데이트할 수 있습니다. 출력은 `managedIdentity` 생성된 관리되는 응용 프로그램 템플릿의 값을 재정의하므로 기본 템플릿으로 전달되지 않으며 매개 변수 섹션에 포함되지 않아야 합니다.

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

### <a name="consuming-the-managed-application-with-a-linked-resource"></a>연결된 리소스를 사용 하 고 관리되는 응용 프로그램 사용

관리되는 응용 프로그램 패키지가 만들어지면 관리되는 응용 프로그램을 Azure 포털을 통해 사용할 수 있습니다. 사용하기 전에 몇 가지 필수 단계가 있습니다.

- 필요한 연결된 Azure 리소스의 인스턴스를 만들어야 합니다.
- **사용자가 할당한 ID를** 만들고 연결된 리소스에 [역할 할당을 지정해야](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md) 합니다.
- 기존 링크된 리소스 ID와 **사용자가 할당한 ID** ID는 CreateUIDefinition에 제공됩니다.

## <a name="accessing-the-managed-identity-token"></a>관리되는 ID 토큰 액세스

이제 게시자 테넌트의 api를 `listTokens` 통해 관리되는 응용 프로그램의 토큰에 액세스할 수 있습니다. 예제 요청은 다음과 같습니다.

``` HTTP
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Solutions/applications/{applicationName}/listTokens?api-version=2018-09-01-preview HTTP/1.1

{
    "authorizationAudience": "https://management.azure.com/",
    "userAssignedIdentities": [
        "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{userAssignedIdentityName}"
    ]
}
```

본문 매개 변수 요청:

매개 변수 | 필수 | 설명
---|---|---
권한 부여대상 | *아니요* | 대상 리소스의 앱 ID URI입니다. 또한 발행 `aud` 된 토큰의 (대상) 클레임입니다. 기본값은 "https://management.azure.com/
userAssignedIdentities | *아니요* | 토큰을 검색하기 위해 사용자가 할당한 관리되는 ID 목록입니다. 지정하지 `listTokens` 않으면 시스템에서 할당된 관리되는 ID에 대한 토큰을 반환합니다.


샘플 응답은 다음과 같습니다.

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

응답에는 속성 아래에 토큰 배열이 `value` 포함됩니다.

매개 변수 | 설명
---|---
access_token | 요청된 액세스 토큰입니다.
expires_in | 액세스 토큰이 유효한 시간(초)입니다.
expires_on | 액세스 토큰이 만료되는 시간 범위입니다. 이는 시대로부터의 초 수로 표시됩니다.
not_before | 액세스 토큰이 적용되는 시간 범위입니다. 이는 시대로부터의 초 수로 표시됩니다.
권한 부여대상 | 액세스 `aud` 토큰이 요청된 (대상) 입니다. 이는 요청에 제공된 내용과 동일합니다. `listTokens`
resourceId | 발급된 토큰에 대한 Azure 리소스 ID입니다. 관리되는 응용 프로그램 ID 또는 사용자가 할당한 ID ID입니다.
token_type | 토큰 형식입니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [사용자 지정 공급자를 사용하여 관리되는 응용 프로그램을 구성하는 방법](../custom-providers/overview.md)
