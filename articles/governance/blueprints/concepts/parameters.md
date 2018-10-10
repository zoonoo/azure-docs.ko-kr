---
title: Azure Blueprint에서 매개 변수를 통해 동적 청사진 만들기
description: 정적 및 동적 매개 변수란 무엇이며 동적 청사진을 만드는 데 사용하는 방법을 알아봅니다.
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 09/18/2018
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: e1a1d736eb9b22cd444a75b94d112abfe3fbe5af
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46993581"
---
# <a name="creating-dynamic-blueprints-through-parameters"></a>매개 변수를 통해 동적 청사진 만들기

다양한 아티팩트(예: 리소스 그룹, Resource Manager 템플릿, 정책 또는 역할 할당)를 사용하여 완벽하게 정의된 청사진은 Azure 내에서 개체를 신속하게 만들고 일관적으로 프로비전하는 방법을 제공합니다. 이러한 재사용 가능한 디자인 패턴과 컨테이너를 유연하게 사용할 수 있도록, Azure Blueprint는 매개 변수를 지원합니다. 매개 변수는 정의 및 할당 과정에서 청사진을 통해 배포된 아티팩트의 속성을 변경할 수 있는 유연성을 제공합니다.

간단한 예로 리소스 그룹 아티팩트가 있습니다. 리소스 그룹을 만들 때 반드시 제공해야 하는 두 가지 값이 있는데, 그것은 바로 이름과 위치입니다. 청사진에 리소스 그룹을 추가할 때 매개 변수가 없으면 청사진을 사용할 때마다 이름과 위치를 정의해야 합니다. 따라서 청사진을 사용할 때마다 동일한 리소스 그룹에 아티팩트를 만들게 됩니다. 리소스 그룹 자체에는 문제가 되지 않지만, 해당 리소스 그룹 내부의 리소스가 중복되고 충돌을 일으킵니다.

> [!NOTE]
> 서로 다른 두 청사진에 이름이 같은 리소스 그룹이 포함되더라도 아무 문제가 없습니다.
> 한 청사진에 포함된 리소스 그룹이 이미 있는 경우 청사진이 계속해서 해당 리소스 그룹에 관련 아티팩트를 만듭니다. 이름 및 리소스 종류가 같은 두 리소스가 한 구독 내에 존재할 수 없기 때문에 이로 인해 충돌이 발생할 수 있습니다.

그래서 매개 변수가 필요한 것입니다. 리소스 그룹의 경우 이러한 속성의 값이 이름 및 위치 속성이며, Blueprints를 사용하면 청사진을 정의할 때 속성을 정의하지 않고, 그 대신 구독에 할당할 때 값을 정의할 수 있습니다. 이렇게 하면 충돌 없이 단일 구독 내에 리소스 그룹과 기타 리소스를 만드는 청사진을 재사용할 수 있습니다.

## <a name="blueprint-parameters"></a>청사진 매개 변수

REST API를 통해, 지원되는 각 아티팩트 외에도 청사진 자체에 매개 변수를 만들 수 있습니다. 청사진에 매개 변수를 만들면 해당 청사진의 아티팩트에서 매개 변수를 사용할 수 있습니다. 리소스 그룹의 이름을 지정하는 접두사를 예로 들 수 있습니다. 그러면 아티팩트에서 청사진 매개 변수를 사용하여 "대부분은 동적인" 매개 변수를 만들 수 있습니다. 할당하는 동안 여전히 매개 변수를 정의할 수 있지만 조직의 명명 규칙을 준수하는 일관성을 유지하기 때문입니다. 자세한 단계는 [정적 매개 변수 설정 - 청사진 수준 매개 변수](#blueprint-level-parameter)를 참조하세요.

### <a name="using-securestring-and-secureobject-parameters"></a>secureString 및 secureObject 매개 변수 사용

Resource Manager 템플릿 _아티팩트_가 **secureString** 및 **secureObject** 유형의 매개 변수를 지원하지만, Azure Blueprints는 각 매개 변수를 Azure Key Vault와 연결할 것을 요구합니다.
이렇게 하면 비밀을 청사진과 함께 저장하는 안전하지 않은 사례를 방지하고 안전한 패턴을 사용하도록 장려할 수 있습니다. Azure Blueprint는 Resource Manager 템플릿 _아티팩트_에 보안 매개 변수가 포함되었는지 감지하고 청사진을 할당하는 동안 감지된 보안 매개 변수마다 다음 Key Vault 속성을 요청하여 이를 수행합니다.

- Key Vault 리소스 ID
- Key Vault 비밀 이름
- Key Vault 비밀 버전

참조되는 Key Vault는 청사진이 할당되는 구독과 동일한 구독에 있어야 하며, Key Vault의 **액세스 정책** 페이지에서 **템플릿 배포를 위해 Azure Resource Manager에 대한 액세스 사용**이 구성되어야 합니다. 이 기능을 사용하는 방법에 대한 지침은 [Key Vault - 템플릿 배포 사용](../../../managed-applications/key-vault-access.md#enable-template-deployment)을 참조하세요.
Azure Key Vault에 대한 자세한 내용은 [Key Vault 개요](../../../key-vault/key-vault-overview.md)를 참조하세요.

## <a name="parameter-types"></a>매개 변수 유형

### <a name="static-parameters"></a>정적 매개 변수

청사진의 정의에 정의된 매개 변수 값을 **정적 매개 변수**라고 합니다. 청사진을 사용할 때마다 해당 정적 값을 사용하여 아티팩트를 배포하기 때문입니다. 리소스 그룹 예제에서, 리소스 그룹의 이름에는 이것이 적용되지 않지만 위치에는 적용됩니다. 그리고 청사진을 할당할 때마다 이름이 무엇이 됐든 같은 위치에 리소스 그룹이 생성됩니다. 이렇게 하면 필수로 정의할 항목과 할당 과정에서 변경 가능한 항목을 선택할 수 있습니다.

#### <a name="setting-static-parameters-in-the-portal"></a>포털에서 정적 매개 변수 설정

1. **모든 서비스**를 클릭한 후 왼쪽 창에서 **정책**을 검색하고 선택하여 Azure Portal에서 Azure Blueprints 서비스를 시작합니다. **정책** 페이지에서 **청사진**을 클릭합니다.

1. 왼쪽 페이지에서 **청사진 정의**를 선택합니다.

1. 기존 청사진을 클릭한 다음, **청사진 편집** 또는 **+ 청사진 만들기**를 클릭하고 **기본 사항** 탭에서 정보를 입력합니다.

1. **다음: 아티팩트** 또는 **아티팩트** 탭을 클릭합니다.

1. 청사진에 추가되고 매개 변수 옵션이 있는 아티팩트는 **매개 변수** 열에 **X/Y 매개 변수 채워짐**으로 표시됩니다. 아티팩트 행을 클릭하여 아티팩트 매개 변수를 편집합니다.

   ![청사진 매개 변수](../media/parameters/parameter-column.png)

1. **아티팩트 편집** 페이지에는 클릭한 아티팩트에 적절한 값 옵션이 표시됩니다. 아티팩트의 각 매개 변수에는 제목, 값 상자 및 확인란이 있습니다. **정적 매개 변수**로 만들려면 확인란을 선택 취소하도록 상자를 설정합니다. 아래 예제에서는 _위치_가 선택 취소되고 _리소스 그룹 이름_이 선택되었기 때문에 위치만이 **정적 매개 변수**입니다.

   ![청사진 정적 매개 변수](../media/parameters/static-parameter.png)

#### <a name="setting-static-parameters-from-rest-api"></a>REST API에서 정적 매개 변수 설정

각 REST API URI에는 사용자가 자신의 값으로 대체해야 하는 변수가 있습니다.

- `{YourMG}` - 사용자의 관리 그룹 이름으로 대체
- `{subscriptionId}` - 사용자의 구독 ID로 대체

##### <a name="blueprint-level-parameter"></a>청사진 수준 매개 변수

REST API를 통해 청사진을 만들 때 [청사진 매개 변수](#blueprint-parameters)를 만들 수 있습니다. 이렇게 하려면 다음 REST API URI 및 본문 형식을 사용합니다.

- REST API URI

  ```http
  PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint?api-version=2017-11-11-preview
  ```

- 요청 본문

  ```json
  {
      "properties": {
          "description": "This blueprint has blueprint level parameters.",
          "targetScope": "subscription",
          "parameters": {
              "owners": {
                  "type": "array",
                  "metadata": {
                      "description": "List of AAD object IDs that is assigned Owner role at the resource group"
                  }
              }
          },
          "resourceGroups": {
              "storageRG": {
                  "description": "Contains the resource template deployment and a role assignment."
              }
          }
      }
  }
  ```

청사진 수준 매개 변수가 만들어지면 해당 청사진에 추가된 아티팩트에 사용할 수 있습니다.
다음 REST API 예제에서는 청사진에 역할 할당 아티팩트를 만들고 청사진 수준 매개 변수를 사용합니다.

- REST API URI

  ```http
  PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint/artifacts/roleOwner?api-version=2017-11-11-preview
  ```

- 요청 본문

  ```json
  {
      "kind": "roleAssignment",
      "properties": {
          "resourceGroup": "storageRG",
          "roleDefinitionId": "/providers/Microsoft.Authorization/roleDefinitions/8e3af657-a8ff-443c-a75c-2fe8c4bcb635",
          "principalIds": "[parameters('owners')]"
      }
  }
  ```

이 예제에서는 `[parameters('owners')]` 값을 제공하여 **principalIds** 속성에 **소유자** 청사진 수준 매개 변수를 사용했습니다. 청사진 수준 매개 변수를 사용하여 아티팩트에서 매개 변수를 설정하는 것은 여전히 **정적 매개 변수**의 예입니다. 청사진 할당 과정에 설정할 수 없으며 각 할당에서 이 값이 되기 때문입니다.

##### <a name="artifact-level-parameter"></a>아티팩트 수준 매개 변수

아티팩트에서 **정적 매개 변수**를 만드는 방법도 비슷하지만, `parameters()` 함수 대신 직접 값을 사용합니다. 다음은 두 개의 정적 매개 변수 **tagName** 및 **tagValue**를 만드는 예제입니다. 각 매개 변수의 값은 직접 제공되며 함수 호출을 사용하지 않습니다.

- REST API URI

  ```http
  PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint/artifacts/policyStorageTags?api-version=2017-11-11-preview
  ```

- 요청 본문

  ```json
  {
      "kind": "policyAssignment",
      "properties": {
          "description": "Apply storage tag and the parameter also used by the template to resource groups",
          "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/49c88fc8-6fd1-46fd-a676-f12d1d3a4c71",
          "parameters": {
              "tagName": {
                  "value": "StorageType"
              },
              "tagValue": {
                  "value": "Premium_LRS"
              }
          }
      }
  }
  ```

### <a name="dynamic-parameters"></a>동적 매개 변수

정적 매개 변수와 대조되는 것이 **동적 매개 변수**입니다. 이 매개 변수는 청사진에서 정의되는 것이 아니라 청사진을 할당할 때 정의됩니다. 리소스 그룹 예제에서, 리소스 그룹과 관련하여 청사진의 각 할당에 서로 다른 이름을 입력하면 됩니다.

#### <a name="setting-dynamic-parameters-in-the-portal"></a>포털에서 동적 매개 변수 설정

1. **모든 서비스**를 클릭한 후 왼쪽 창에서 **정책**을 검색하고 선택하여 Azure Portal에서 Azure Blueprints 서비스를 시작합니다. **정책** 페이지에서 **청사진**을 클릭합니다.

1. 왼쪽 페이지에서 **청사진 정의**를 선택합니다.

1. 할당하려는 청사진을 마우스 오른쪽 단추로 클릭하고 **청사진 할당**을 클릭하거나 할당하려는 청사진을 클릭한 다음, **청사진 할당** 단추를 클릭합니다.

1. **청사진 할당** 페이지에서 **아티팩트 매개 변수** 섹션을 찾습니다. **동적 매개 변수**가 하나 이상 있는 아티팩트는 아티팩트 및 구성 옵션을 표시합니다. 매개 변수에 필요한 값을 제공한 후 청사진을 할당합니다. 아래 예제에서 _이름_은 청사진을 완료하려면 정의해야 하는 **동적 매개 변수**입니다.

   ![청사진 동적 매개 변수](../media/parameters/dynamic-parameter.png)

#### <a name="setting-dynamic-parameters-from-rest-api"></a>REST API에서 정적 매개 변수 설정

할당 과정에서 **동적 매개 변수**를 설정하는 작업은 원하는 값을 직접 입력하는 방식으로 수행됩니다. `parameters()` 같은 함수를 사용하는 대신, 입력한 값이 적절한 문자열입니다. 리소스 그룹에 대한 아티팩트는 "템플릿 이름"과 **이름** 및 **위치** 속성으로 정의됩니다. 포함된 각 아티팩트에 대한 그 외의 매개 변수는 **\<이름\>** 및 **값** 키 쌍을 사용하여 **매개 변수** 아래에 정의됩니다. 할당 과정에서 제공하지 않는 동적 매개 변수에 대해 청사진이 구성된 경우 할당이 실패합니다.

- REST API URI

  ```http
  PUT https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Blueprint/blueprintAssignments/assignMyBlueprint?api-version=2017-11-11-preview
  ```

- 요청 본문

  ```json
  {
      "properties": {
          "blueprintId": "/providers/Microsoft.Management/managementGroups/{YourMG}  /providers/Microsoft.Blueprint/blueprints/MyBlueprint",
          "resourceGroups": {
              "storageRG": {
                  "name": "StorageAccount",
                  "location": "eastus2"
              }
          },
          "parameters": {
              "storageAccountType": {
                  "value": "Standard_GRS"
              },
              "tagName": {
                  "value": "CostCenter"
              },
              "tagValue": {
                  "value": "ContosoIT"
              },
              "contributors": {
                  "value": [
                      "7be2f100-3af5-4c15-bcb7-27ee43784a1f",
                      "38833b56-194d-420b-90ce-cff578296714"
                  ]
                },
              "owners": {
                  "value": [
                      "44254d2b-a0c7-405f-959c-f829ee31c2e7",
                      "316deb5f-7187-4512-9dd4-21e7798b0ef9"
                  ]
              }
          }
      },
      "identity": {
          "type": "systemAssigned"
      },
      "location": "westus"
  }
  ```

## <a name="next-steps"></a>다음 단계

- [청사진 수명 주기](lifecycle.md)에 대해 알아보기
- [청사진 시퀀싱 순서](sequencing-order.md)를 사용자 지정하는 방법 알아보기
- [청사진 리소스 잠금](resource-locking.md)을 활용하는 방법 알아보기
- [기존 할당을 업데이트](../how-to/update-existing-assignments.md)하는 방법 알아보기
- [일반 문제 해결 방법](../troubleshoot/general.md)을 통해 청사진 할당 중에 발생하는 문제 해결