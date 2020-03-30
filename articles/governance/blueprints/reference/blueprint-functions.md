---
title: Azure 블루프린트 기능
description: Azure Blueprint 정의 및 할당에서 Blueprint 아티팩트와 함께 사용할 수 있는 함수에 대해 설명합니다.
ms.date: 12/09/2019
ms.topic: reference
ms.openlocfilehash: 0aab2fe0511ccc11842d0e132a83d6e3f7fac27f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79280678"
---
# <a name="functions-for-use-with-azure-blueprints"></a>Azure Blueprint와 함께 사용할 수 있는 기능

Azure Blueprints는 Blueprint 정의를 보다 동적인 기능으로 만드는 기능을 제공합니다. 이러한 함수는 청사진 정의 및 청사진 아티팩트와 함께 사용할 수 있습니다. 리소스 관리자 템플릿 아티팩트는 Blueprint 매개 변수를 통해 동적 값을 가져오는 것 외에도 리소스 관리자 함수의 전체 사용을 지원합니다.

다음 기능이 지원됩니다.

- [아티팩트](#artifacts)
- [concat](#concat)
- [매개 변수](#parameters)
- [리소스 그룹](#resourcegroup)
- [resourceGroups](#resourcegroups)
- [구독](#subscription)

## <a name="artifacts"></a>아티팩트

`artifacts(artifactName)`

해당 Blueprint 아티팩트 출력으로 채워진 속성의 개체를 반환합니다.

> [!NOTE]
> 리소스 `artifacts()` 관리자 템플릿 내에서이 함수를 사용할 수 없습니다. 이 함수는 블루프린트 정의 JSON 또는 아티팩트 JSON에서만 사용할 수 있으며 [블루프린트 코드로](https://github.com/Azure/azure-blueprints/blob/master/README.md)Azure PowerShell 또는 REST API를 사용하여 Blueprint를 관리할 때.

### <a name="parameters"></a>매개 변수

| 매개 변수 | 필수 | Type | Description |
|:--- |:--- |:--- |:--- |
| 아티팩트 이름 |yes |문자열 |청사진 아티팩트의 이름입니다. |

### <a name="return-value"></a>반환 값

출력 속성의 개체입니다. **출력** 속성은 참조되는 Blueprint 아티팩트의 유형에 따라 달라집니다. 모든 형식은 형식을 따릅니다.

```json
{
  "outputs": {collectionOfOutputProperties}
}
```

#### <a name="policy-assignment-artifact"></a>정책 할당 아티팩트

```json
{
    "outputs": {
        "policyAssignmentId": "{resourceId-of-policy-assignment}",
        "policyAssignmentName": "{name-of-policy-assignment}",
        "policyDefinitionId": "{resourceId-of-policy-definition}",
    }
}
```

#### <a name="resource-manager-template-artifact"></a>리소스 관리자 템플릿 아티팩트

반환된 개체의 **출력** 속성은 리소스 관리자 템플릿 내에서 정의되고 배포에서 반환됩니다.

#### <a name="role-assignment-artifact"></a>역할 할당 아티팩트

```json
{
    "outputs": {
        "roleAssignmentId": "{resourceId-of-role-assignment}",
        "roleDefinitionId": "{resourceId-of-role-definition}",
        "principalId": "{principalId-role-is-being-assigned-to}",
    }
}
```

### <a name="example"></a>예제

다음 샘플 출력 속성을 포함하는 ID _myTemplateArtifact가_ 있는 리소스 관리자 템플릿 아티팩트:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    ...
    "outputs": {
        "myArray": {
            "type": "array",
            "value": ["first", "second"]
        },
        "myString": {
            "type": "string",
            "value": "my string value"
        },
        "myObject": {
            "type": "object",
            "value": {
                "myProperty": "my value",
                "anotherProperty": true
            }
        }
    }
}
```

_myTemplateArtifact_ 샘플에서 데이터를 검색하는 몇 가지 예는 다음과 같습니다.

| 식 | Type | 값 |
|:---|:---|:---|
|`[artifacts("myTemplateArtifact").outputs.myArray]` | Array | \["첫 번째", "두 번째"\] |
|`[artifacts("myTemplateArtifact").outputs.myArray[0]]` | String | "첫 번째" |
|`[artifacts("myTemplateArtifact").outputs.myString]` | String | "내 문자열 값" |
|`[artifacts("myTemplateArtifact").outputs.myObject]` | Object | { "myproperty": "내 가치", "다른 속성": true } |
|`[artifacts("myTemplateArtifact").outputs.myObject.myProperty]` | String | "가격대비 만족" |
|`[artifacts("myTemplateArtifact").outputs.myObject.anotherProperty]` | Bool | True |

## <a name="concat"></a>concat

`concat(string1, string2, string3, ...)`

여러 문자열 값을 결합하고 연결된 문자열을 반환합니다.

### <a name="parameters"></a>매개 변수

| 매개 변수 | 필수 | Type | Description |
|:--- |:--- |:--- |:--- |
| string1 |yes |문자열 |연결할 첫 번째 값입니다. |
| 추가 인수 |예 |문자열 |연결에 대한 순차적 순서의 추가 값 |

### <a name="return-value"></a>반환 값

연결된 값의 문자열입니다.

### <a name="remarks"></a>설명

Azure Blueprint 함수는 문자열에서만 작동한다는 점에서 Azure 리소스 관리자 템플릿 함수와 다릅니다.

### <a name="example"></a>예제

`concat(parameters('organizationName'), '-vm')`

## <a name="parameters"></a>매개 변수

`parameters(parameterName)`

Blueprint 매개 변수 값을 반환합니다. 지정된 매개변수 이름은 Blueprint 정의 또는 Blueprint 아티팩트에 정의되어야 합니다.

### <a name="parameters"></a>매개 변수

| 매개 변수 | 필수 | Type | Description |
|:--- |:--- |:--- |:--- |
| parameterName |yes |문자열 |반환할 매개 변수의 이름입니다. |

### <a name="return-value"></a>반환 값

지정된 Blueprint 또는 Blueprint 아티팩트 매개변수의 값입니다.

### <a name="remarks"></a>설명

Azure Blueprint 함수는 블루프린트 매개 변수에서만 작동한다는 점에서 Azure 리소스 관리자 템플릿 함수와 다릅니다.

### <a name="example"></a>예제

청사진 정의에서 매개 변수 _보안 원칙을 정의합니다._

```json
{
    "type": "Microsoft.Blueprint/blueprints",
    "properties": {
        ...
        "parameters": {
            "principalIds": {
                "type": "array",
                "metadata": {
                    "displayName": "Principal IDs",
                    "description": "This is a blueprint parameter that any artifact can reference. We'll display these descriptions for you in the info bubble. Supply principal IDs for the users,groups, or service principals for the RBAC assignment.",
                    "strongType": "PrincipalId"
                }
            }
        },
        ...
    }
}
```

그런 다음 blueprint 아티팩트에서 `parameters()` _principalIds를_ 인수로 사용합니다.

```json
{
    "type": "Microsoft.Blueprint/blueprints/artifacts",
    "kind": "roleAssignment",
    ...
    "properties": {
        "roleDefinitionId": "/providers/Microsoft.Authorization/roleDefinitions/8e3af657-a8ff-443c-a75c-2fe8c4bcb635",
        "principalIds": "[parameters('principalIds')]",
        ...
    }
}
```

## <a name="resourcegroup"></a>resourceGroup

`resourceGroup()`

현재 리소스 그룹을 나타내는 개체를 반환합니다.

### <a name="return-value"></a>반환 값

반환된 개체는 다음 형식으로 되어 있습니다.

```json
{
  "name": "{resourceGroupName}",
  "location": "{resourceGroupLocation}",
}
```

### <a name="remarks"></a>설명

Azure Blueprint 함수는 Azure 리소스 관리자 템플릿 함수와 다릅니다. 이 `resourceGroup()` 함수는 구독 수준 아티팩트 또는 Blueprint 정의에서 사용할 수 없습니다. 리소스 그룹 아티팩트의 일부인 Blueprint 아티팩트에서만 사용할 수 있습니다.

`resourceGroup()` 이 함수의 일반적인 용도는 리소스 그룹 아티팩트와 동일한 위치에 리소스를 만드는 것입니다.

### <a name="example"></a>예제

Blueprint 정의또는 할당 중에 다른 아티팩트의 위치로 설정된 리소스 그룹의 위치를 사용하여 Blueprint 정의에서 리소스 그룹 자리 표시자 개체를 선언합니다. 이 예제에서 _네트워킹자리 표시자는_ 리소스 그룹 자리 표시자의 이름입니다.

```json
{
    "type": "Microsoft.Blueprint/blueprints",
    "properties": {
        ...
        "resourceGroups": {
            "NetworkingPlaceholder": {
                "location": "eastus"
            }
        }
    }
}
```

그런 다음 `resourceGroup()` 리소스 그룹 자리 표시자 개체를 대상으로 하는 Blueprint 아티팩트의 컨텍스트에서 함수를 사용합니다. 이 예제에서는 템플릿 아티팩트가 _NetworkingPlaceholder_ 리소스 그룹에 배포되고 _NetworkingPlaceholder_ 리소스 그룹 위치로 동적으로 채워진 매개 변수 _리소스를_ 템플릿에 제공합니다. _NetworkingPlaceholder_ 리소스 그룹의 위치는 Blueprint 정의에 정적으로 정의되었거나 할당 중에 동적으로 정의되었을 수 있습니다. 두 경우 모두 템플릿 아티팩트는 해당 정보를 매개 변수로 제공하고 이를 사용하여 리소스를 올바른 위치에 배포합니다.

```json
{
  "type": "Microsoft.Blueprint/blueprints/artifacts",
  "kind": "template",
  "properties": {
      "template": {
        ...
      },
      "resourceGroup": "NetworkingPlaceholder",
      ...
      "parameters": {
        "resourceLocation": {
          "value": "[resourceGroup().location]"
        }
      }
  }
}
```

## <a name="resourcegroups"></a>resourceGroups

`resourceGroups(placeholderName)`

지정된 리소스 그룹 아티팩트를 나타내는 개체를 반환합니다. 아티팩트의 컨텍스트가 필요한 과 달리 `resourceGroup()`이 함수는 해당 리소스 그룹의 컨텍스트가 아닌 경우 특정 리소스 그룹 자리 표시자의 속성을 얻는 데 사용됩니다.

### <a name="parameters"></a>매개 변수

| 매개 변수 | 필수 | Type | Description |
|:--- |:--- |:--- |:--- |
| 자리 표시자 이름 |yes |문자열 |반환할 리소스 그룹 아티팩트의 자리 표시자 이름입니다. |

### <a name="return-value"></a>반환 값

반환된 개체는 다음 형식으로 되어 있습니다.

```json
{
  "name": "{resourceGroupName}",
  "location": "{resourceGroupLocation}",
}
```

### <a name="example"></a>예제

Blueprint 정의또는 할당 중에 다른 아티팩트의 위치로 설정된 리소스 그룹의 위치를 사용하여 Blueprint 정의에서 리소스 그룹 자리 표시자 개체를 선언합니다. 이 예제에서 _네트워킹자리 표시자는_ 리소스 그룹 자리 표시자의 이름입니다.

```json
{
    "type": "Microsoft.Blueprint/blueprints",
    "properties": {
        ...
        "resourceGroups": {
            "NetworkingPlaceholder": {
                "location": "eastus"
            }
        }
    }
}
```

그런 다음 `resourceGroups()` Blueprint 아티팩트의 컨텍스트에서 함수를 사용하여 리소스 그룹 자리 표시자 개체에 대한 참조를 가져옵니다. 이 예제에서 템플릿 아티팩트는 _NetworkingPlaceholder_ 리소스 그룹 외부에 배포되고 _NetworkingPlaceholder_ 리소스 그룹 위치로 동적으로 채워진 매개 변수 _아티팩트를_ 템플릿에 제공합니다. _NetworkingPlaceholder_ 리소스 그룹의 위치는 Blueprint 정의에 정적으로 정의되었거나 할당 중에 동적으로 정의되었을 수 있습니다. 두 경우 모두 템플릿 아티팩트는 해당 정보를 매개 변수로 제공하고 이를 사용하여 리소스를 올바른 위치에 배포합니다.

```json
{
  "kind": "template",
  "properties": {
      "template": {
          ...
      },
      ...
      "parameters": {
        "artifactLocation": {
          "value": "[resourceGroups('NetworkingPlaceholder').location]"
        }
      }
  },
  "type": "Microsoft.Blueprint/blueprints/artifacts",
  "name": "myTemplate"
}
```

## <a name="subscription"></a>subscription

`subscription()`

현재 Blueprint 할당에 대한 구독에 대한 세부 정보를 반환합니다.

### <a name="return-value"></a>반환 값

반환된 개체는 다음 형식으로 되어 있습니다.

```json
{
    "id": "/subscriptions/{subscriptionId}",
    "subscriptionId": "{subscriptionId}",
    "tenantId": "{tenantId}",
    "displayName": "{name-of-subscription}"
}
```

### <a name="example"></a>예제

구독의 표시 이름과 함수를 `concat()` 사용하여 템플릿 아티팩트에 매개 변수 _resourceName으로_ 전달된 명명 규칙을 만듭니다.

```json
{
  "kind": "template",
  "properties": {
      "template": {
          ...
      },
      ...
      "parameters": {
        "resourceName": {
          "value": "[concat(subscription().displayName, '-vm')]"
        }
      }
  },
  "type": "Microsoft.Blueprint/blueprints/artifacts",
  "name": "myTemplate"
}
```

## <a name="next-steps"></a>다음 단계

- [청사진 수명 주기에](../concepts/lifecycle.md)대해 자세히 알아봅니다.
- [정적 및 동적 매개 변수](../concepts/parameters.md) 사용 방법 이해
- [청사진 시퀀싱 순서](../concepts/sequencing-order.md)를 사용자 지정하는 방법 알아보기
- [청사진 리소스 잠금](../concepts/resource-locking.md)을 활용하는 방법 알아보기
- [기존 할당을 업데이트](../how-to/update-existing-assignments.md)하는 방법 알아보기
- [일반 문제 해결 방법](../troubleshoot/general.md)을 통해 청사진 할당 중에 발생하는 문제 해결