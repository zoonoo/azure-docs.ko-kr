---
title: 정책 정의 구조에 대한 세부 정보
description: 정책 정의를 사용하여 조직에서 Azure 리소스에 대한 규칙을 설정하는 방법을 설명합니다.
ms.date: 04/03/2020
ms.topic: conceptual
ms.openlocfilehash: e6b1d5c43f290fc2dd953492440670608a15faca
ms.sourcegitcommit: 0450ed87a7e01bbe38b3a3aea2a21881f34f34dd
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80638088"
---
# <a name="azure-policy-definition-structure"></a>Azure Policy 정의 구조

Azure Policy는 리소스에 대한 규칙을 설정합니다. 정책 정의는 리소스 준수 [조건과](#conditions) 조건이 충족되는 경우 취하는 영향을 설명합니다. 조건은 리소스 속성 [필드를](#fields) 필요한 값과 비교합니다. 리소스 속성 필드는 [별칭을 사용하여 액세스됩니다.](#aliases) 리소스 속성 필드는 단일 값 필드 또는 여러 값의 [배열입니다.](#understanding-the--alias) 조건 평가는 배열에서 다릅니다.
[조건에](#conditions)대해 자세히 알아보십시오.

규칙을 정의하여 비용을 제어하고 리소스를 보다 쉽게 관리할 수 있습니다. 예를 들어, 특정 유형의 가상 머신만 허용되게 지정할 수 있습니다. 또는 모든 리소스가 특정 태그를 갖도록 요구할 수 있습니다. 정책은 모든 자식 리소스에 의해 상속됩니다. 리소스 그룹에 정책을 적용하면 해당 리소스 그룹의 모든 리소스에 해당 정책을 적용할 수 있습니다.

정책 정의 스키마는 다음과 같습니다.[https://schema.management.azure.com/schemas/2019-06-01/policyDefinition.json](https://schema.management.azure.com/schemas/2019-06-01/policyDefinition.json)

JSON을 사용하여 정책 정의를 만듭니다. 정책 정의에는 다음 요소가 포함됩니다.

- mode
- 매개 변수
- 표시 이름
- description
- 정책 규칙
  - 논리 평가
  - 영향

예를 들어 다음 JSON에서는 리소스가 배포되는 위치를 제한하는 정책을 보여줍니다.

```json
{
    "properties": {
        "mode": "all",
        "parameters": {
            "allowedLocations": {
                "type": "array",
                "metadata": {
                    "description": "The list of locations that can be specified when deploying resources",
                    "strongType": "location",
                    "displayName": "Allowed locations"
                },
                "defaultValue": [ "westus2" ]
            }
        },
        "displayName": "Allowed locations",
        "description": "This policy enables you to restrict the locations your organization can specify when deploying resources.",
        "policyRule": {
            "if": {
                "not": {
                    "field": "location",
                    "in": "[parameters('allowedLocations')]"
                }
            },
            "then": {
                "effect": "deny"
            }
        }
    }
}
```

모든 Azure 정책 샘플은 [Azure 정책 샘플에 있습니다.](../samples/index.md)

## <a name="mode"></a>Mode

**모드는** 정책이 Azure Resource Manager 속성 또는 리소스 공급자 속성을 대상으로 하는지 여부에 따라 구성됩니다.

### <a name="resource-manager-modes"></a>리소스 관리자 모드

**mode**는 정책에 대해 평가할 리소스 종류를 결정합니다. 지원되는 모드는 다음과 같습니다.

- `all`: 리소스 그룹 및 모든 리소스 종류를 평가합니다.
- `indexed`: 태그 및 위치를 지원하는 리소스 종류만 평가합니다.

예를 들어 `Microsoft.Network/routeTables` 리소스는 태그와 위치를 지원하며 두 모드에서 모두 평가됩니다. 그러나 리소스에 `Microsoft.Network/routeTables/routes` 태그를 지정할 수 없으며 `Indexed` 모드에서 평가되지 않습니다.

대부분 **mode**를 `all`로 설정하는 것이 좋습니다. 포털을 통해 생성된 모든 정책 정의는 `all` 모드를 사용합니다. PowerShell 또는 Azure CLI를 사용하는 경우 **mode** 매개 변수를 수동으로 지정할 수 있습니다. 정책 정의에 **mode** 값이 포함되지 않으면 기본적으로 Azure PowerShell에서는 `all`로 설정되고 Azure CLI에서는 `null`로 설정됩니다. `null` 모드는 이전 버전과의 호환성을 지원하기 위해 `indexed`를 사용하는 것과 같습니다.

`indexed`는 태그 또는 위치를 시스템에 적용하는 정책을 만들 때 사용해야 합니다. 이 모드는 반드시 사용해야 하는 것은 아니지만, 사용하는 경우 태그와 위치를 지원하지 않는 리소스가 규정 준수 결과에 미준수 항목으로 표시되지 않습니다. 예외는 **리소스 그룹**입니다. 리소스 그룹에서 위치 또는 태그를 적용하는 정책은 **mode**를 `all`로 설정하고 구체적으로 `Microsoft.Resources/subscriptions/resourceGroups` 형식을 대상으로 지정해야 합니다. 예를 들어 [리소스 그룹 태그 적용](../samples/enforce-tag-rg.md)을 참조하세요. 태그를 지원하는 리소스 목록은 [Azure 리소스에 대한 태그 지원을](../../../azure-resource-manager/management/tag-support.md)참조하십시오.

### <a name="resource-provider-modes-preview"></a><a name="resource-provider-modes" />리소스 공급자 모드(미리 보기)

다음 리소스 공급자 모드는 현재 미리 보기 중에 지원됩니다.

- `Microsoft.ContainerService.Data`[Azure Kubernetes 서비스의](../../../aks/intro-kubernetes.md)입학 컨트롤러 규칙 관리를 위한 것입니다. 이 리소스 공급자 모드를 사용하는 정책은 [EnforceRegoPolicy](./effects.md#enforceregopolicy) 효과를 사용해야 **합니다.**
- `Microsoft.Kubernetes.Data`Azure에서 자체 관리되는 AKS 엔진 Kubernetes 클러스터를 관리하기 위한 것입니다.
  이 리소스 공급자 모드를 사용하는 정책은 [EnforceOPAConstraint](./effects.md#enforceopaconstraint) 효과를 사용해야 **합니다.**
- `Microsoft.KeyVault.Data`Azure 키 자격 증명 모음에서 볼트 및 인증서 를 [관리하기 위한](../../../key-vault/key-vault-overview.md)것입니다.

> [!NOTE]
> 리소스 공급자 모드는 기본 제공 정책 정의만 지원하며 미리 보기 동안 이니셔티브를 지원하지 않습니다.

## <a name="parameters"></a>매개 변수

매개 변수는 정책 정의의 수를 줄여 정책 관리를 간소화하는 데 도움이 됩니다. 양식의 필드 `name`, `address`, `city`, `state`와 같은 매개 변수에 관해 생각해 봅니다. 이러한 매개 변수는 항상 그대로 유지되지만, 그 값은 양식을 작성하는 개별 값에 기초하여 달라집니다.
매개 변수는 정책을 만들 때와 같은 방법으로 작동합니다. 정책 정의에 매개 변수를 포함함으로써 서로 다른 값을 사용하여 다양한 시나리오에 대해 해당 정책을 재사용할 수 있습니다.

> [!NOTE]
> 매개 변수를 기존 및 할당된 정의에 추가할 수 있습니다. 새 매개 변수는 **defaultValue** 속성을 포함해야 합니다. 이렇게 하면 정책 또는 이니셔티브의 기존 지정이 간접적으로 유효하지 않게 됩니다.

### <a name="parameter-properties"></a>매개 변수 속성

매개 변수에는 정책 정의에 사용되는 다음 속성이 있습니다.

- **이름**: 매개 변수의 이름입니다. 정책 규칙 내의 `parameters` 배포 함수에서 사용됩니다. 자세한 내용은 [매개 변수 값 사용](#using-a-parameter-value)을 참조하세요.
- `type`: 매개 변수가 **문자열,** **배열,** **개체,** **부울,** **정수,** **부동, 부동**또는 **datetime인지**확인합니다.
- `metadata`: Azure 포털에서 주로 사용자 친화적인 정보를 표시하기 위해 사용하는 하위 속성을 정의합니다.
  - `description`: 매개 변수가 사용되는 내용에 대한 설명입니다. 허용 가능한 값의 예를 제공하는 데 사용할 수 있습니다.
  - `displayName`: 매개 변수에 대한 포털에 표시된 친숙한 이름입니다.
  - `version`: (선택 사항) 정책 정의의 내용 버전에 대한 세부 정보를 추적합니다.

    > [!NOTE]
    > Azure Policy 서비스는 `version` `preview`에서 `deprecated` 및 속성을 사용하여 기본 제공 정책 정의 또는 이니셔티브 및 상태로 변경 수준을 전달합니다. 의 `version` 형식은 `{Major}.{Minor}.{Patch}`다음과 입니다. _더 이상 사용되지 않거나_ 미리 _보기와_같은 특정 `version` 상태는 속성 또는 다른 속성에 **부울로**추가됩니다.

  - `category`: (선택 사항) Azure Portal에서 정책 정의가 표시되는 범주를 결정합니다.
  - `strongType`: (선택 사항) 포털을 통해 정책 정의를 할당할 때 사용됩니다. 컨텍스트 인식 목록을 제공합니다. 자세한 내용은 [strongType](#strongtype)을 참조하세요.
  - `assignPermissions`: (선택 사항) Azure Portal이 정책 할당 중에 역할 할당을 만들도록 _true로_ 설정합니다. 이 속성은 할당 범위 외부에 권한을 할당하려는 경우에 유용합니다. 정책(또는 이니셔티브의 모든 정책의 역할 정의당)에는 역할 정의당 하나의 역할 할당이 있습니다. 매개 변수 값은 유효한 리소스 또는 범위여야 합니다.
- `defaultValue`: (선택 사항) 값이 제공되지 않으면 할당에서 매개 변수값을 설정합니다.
  할당된 기존 정책 정의를 업데이트할 때 필요합니다.
- `allowedValues`: (선택 사항) 할당 중에 매개 변수가 허용하는 값의 배열을 제공합니다.

예를 들어 리소스를 배포할 수 있는 위치를 제한하는 정책 정의를 정의할 수 있습니다. 해당 정책 정의의 매개 변수는 **allowedLocations**일 수 있습니다. 이 매개 변수는 정책 정의의 각 할당에서 허용되는 값을 제한하는 데 사용됩니다. **strongType**을 사용하면 포털을 통해 할당을 완료할 때 경험이 개선됩니다.

```json
"parameters": {
    "allowedLocations": {
        "type": "array",
        "metadata": {
            "description": "The list of allowed locations for resources.",
            "displayName": "Allowed locations",
            "strongType": "location"
        },
        "defaultValue": [ "westus2" ],
        "allowedValues": [
            "eastus2",
            "westus2",
            "westus"
        ]
    }
}
```

### <a name="using-a-parameter-value"></a>매개 변수 값 사용

정책 규칙에서 다음 `parameters` 함수 구문으로 매개 변수를 참조합니다.

```json
{
    "field": "location",
    "in": "[parameters('allowedLocations')]"
}
```

이 샘플은 [매개 변수 속성](#parameter-properties)에 설명된 **allowedLocations** 매개 변수를 참조합니다.

### <a name="strongtype"></a>strongType

`metadata` 속성 안에 **strongType**을 사용하여 Azure Portal 내에서 다중 선택 옵션 목록을 제공할 수 있습니다. **strongType은** 지원되는 _리소스 유형_ 또는 허용된 값일 수 있습니다. _리소스 형식이_ **strongType에**유효한지 확인하려면 [Get-AzResourceProvider](/powershell/module/az.resources/get-azresourceprovider)를 사용합니다.

**Get-AzResourceProvider에서** 반환되지 않은 일부 _리소스 형식이_ 지원됩니다. 다음은 다음과 같습니다.

- `Microsoft.RecoveryServices/vaults/backupPolicies`

**strongType에** 대해 허용된 _비리소스 형식값은_ 다음과 같습니다.

- `location`
- `resourceTypes`
- `storageSkus`
- `vmSKUs`
- `existingResourceGroups`

## <a name="definition-location"></a>정의 위치

이니셔티브 또는 정책을 만드는 동안 정의 위치를 지정해야 합니다. 정의 위치는 관리 그룹 또는 구독이어야 합니다. 이 위치는 이니셔티브 또는 정책을 할당할 수 있는 범위를 결정합니다. 리소스는 할당 대상으로 지정할 정의 위치의 계층 구조 내의 직접 멤버 또는 자식 멤버여야 합니다.

정의 위치는 다음과 같습니다.

- **구독** - 해당 구독 내의 리소스만 정책에 할당할 수 있습니다.
- **관리 그룹 ** - 하위 관리 그룹과 자식 구독 내의 리소스만 정책에 할당할 수 있습니다. 정책 정의를 여러 구독에 적용하려는 경우 위치는 이러한 구독이 포함된 관리 그룹이어야 합니다.

## <a name="display-name-and-description"></a>표시 이름 및 설명

**displayName** 및 **description**을 사용하여 정책 정의를 식별하고 사용하는 시기에 대한 컨텍스트를 제공합니다. **displayName**은 최대 길이가 _128_자이고 **description**은 최대 길이가 _512_자입니다.

> [!NOTE]
> 정책 정의를 만들거나 업데이트하는 동안 **id**, **type**및 **name은** JSON 외부의 속성에 의해 정의되며 JSON 파일에는 필요하지 않습니다. SDK를 통해 정책 정의를 가져오면 JSON의 일부로 **id,** **type**및 **name** 속성을 반환하지만 각 정보는 정책 정의와 관련된 읽기 전용 정보입니다.

## <a name="policy-rule"></a>정책 규칙

정책 규칙은 **If** 및 **Then** 블록으로 이루어집니다. **If** 블록에서 정책이 적용되는 시점을 지정하는 하나 이상의 조건을 정의합니다. 이러한 조건에 논리 연산자를 적용하여 정책에 대한 시나리오를 정확하게 정의할 수 있습니다.

**Then** 블록에서 **If** 조건이 충족된 경우에 발생하는 효과를 정의합니다.

```json
{
    "if": {
        <condition> | <logical operator>
    },
    "then": {
        "effect": "deny | audit | append | auditIfNotExists | deployIfNotExists | disabled"
    }
}
```

### <a name="logical-operators"></a>논리 연산자

지원되는 논리 연산자는 다음과 같습니다.

- `"not": {condition  or operator}`
- `"allOf": [{condition or operator},{condition or operator}]`
- `"anyOf": [{condition or operator},{condition or operator}]`

**not** 구문은 조건의 결과를 반전시킵니다. **allOf** 구문(논리 **And** 작업과 비슷함)은 모든 조건이 true여야 합니다. **anyOf** 구문(논리 **Or** 작업과 비슷함)은 하나 이상의 조건이 true여야 합니다.

논리 연산자를 중첩할 수 있습니다. 다음 예제에서는 **allOf** 작업 내에 중첩된 **not** 작업을 표시합니다.

```json
"if": {
    "allOf": [{
            "not": {
                "field": "tags",
                "containsKey": "application"
            }
        },
        {
            "field": "type",
            "equals": "Microsoft.Storage/storageAccounts"
        }
    ]
},
```

### <a name="conditions"></a>조건

조건은 **field** 또는 **value** 접근자가 특정 기준을 충족하는지 여부를 평가합니다. 지원되는 조건은 다음과 같습니다.

- `"equals": "stringValue"`
- `"notEquals": "stringValue"`
- `"like": "stringValue"`
- `"notLike": "stringValue"`
- `"match": "stringValue"`
- `"matchInsensitively": "stringValue"`
- `"notMatch": "stringValue"`
- `"notMatchInsensitively": "stringValue"`
- `"contains": "stringValue"`
- `"notContains": "stringValue"`
- `"in": ["stringValue1","stringValue2"]`
- `"notIn": ["stringValue1","stringValue2"]`
- `"containsKey": "keyName"`
- `"notContainsKey": "keyName"`
- `"less": "value"`
- `"lessOrEquals": "value"`
- `"greater": "value"`
- `"greaterOrEquals": "value"`
- `"exists": "bool"`

**like** 및 **notLike** 조건을 사용하는 경우 값에 와일드카드 `*`를 제공합니다.
값에 와일드카드 `*`를 두 개 이상 포함하면 안 됩니다.

**일치** 및 **notMatch** 조건을 사용하는 `#` 경우, 숫자, `?` 문자, `.` 모든 문자 및 실제 문자와 일치하는 다른 문자를 일치하도록 제공합니다. **일치** 및 **notMatch은** 대/소문자를 구분하지만 _stringValue를_ 평가하는 다른 모든 조건은 대/소문자를 구분하지 않습니다. 대/소문자를 구분하지 않는 대안은 **matchInsensitively** 및 **notMatchInsensitively**에서 확인할 수 있습니다.

별칭 배열 필드 값에서 배열의 각 요소는 논리적 **및** 요소 간을 개별적으로 평가합니다. ** \[ \* \] ** 자세한 내용은 [ \[ \* \] 별칭 평가를](../how-to/author-policies-for-arrays.md#evaluating-the--alias)참조하십시오.

### <a name="fields"></a>필드

조건은 필드를 사용하여 구성됩니다. 필드는 리소스 요청 페이로드의 속성을 일치시키고 리소스 상태를 설명합니다.

다음 필드가 지원됩니다.

- `name`
- `fullName`
  - 리소스의 전체 이름을 반환합니다. 리소스의 전체 이름은 리소스 이름에 상위 리소스 이름을 접두어로 추가하여 만듭니다(예: "myServer/myDatabase").
- `kind`
- `type`
- `location`
  - 위치에 관계없는 리소스에는 **전역**을 사용합니다.
- `identity.type`
  - 리소스에서 사용 가능한 [관리 ID](../../../active-directory/managed-identities-azure-resources/overview.md) 형식을 반환합니다.
- `tags`
- `tags['<tagName>']`
  - 이 대괄호 구문은 하이픈, 마침표, 공백 등의 문장 부호가 있는 태그 이름을 지원합니다.
  - ** \<여기서 tagName은\> ** 조건을 검사하는 태그의 이름입니다.
  - 예: `tags['Acct.CostCenter']`. 여기서 **Acct.CostCenter**는 태그 이름입니다.
- `tags['''<tagName>''']`
  - 이 대괄호 구문은 이중 아포스트로피로 이스케이프 처리하여 아포스트로피가 있는 태그 이름을 지원합니다.
  - **여기서\<'\>tagName '** 조건의 유효성을 검사 하는 태그의 이름입니다.
  - 예: `tags['''My.Apostrophe.Tag''']` **'My.Apostrophe.Tag'가** 태그의 이름입니다.
- 속성 별칭 - 목록은 [별칭](#aliases)을 참조하세요.

> [!NOTE]
> `tags.<tagName>`, `tags[tagName]` 및 `tags[tag.with.dots]`도 여전히 허용되는 태그 필드 선언 방법입니다. 그러나 기본 식은 위에 나열된 식입니다.

#### <a name="use-tags-with-parameters"></a>매개 변수와 함께 태그 사용

매개 변수 값을 태그 필드에 전달할 수 있습니다. 매개 변수를 태그 필드에 전달하면 정책 할당 중에 정책 정의의 유연성이 증가합니다.

다음 예제에서 `concat`는 이름이 **tagName** 매개 변수의 값인 태그에 대한 태그 필드 조회를 만드는 데 사용됩니다. 해당 태그가 없는 경우 **수정** 효과 조회 함수를 사용 하 여 감사 된 리소스 상위 리소스 그룹에 설정 `resourcegroup()` 된 동일한 명명 된 태그의 값을 사용 하 여 태그를 추가 하는 데 사용 됩니다.

```json
{
    "if": {
        "field": "[concat('tags[', parameters('tagName'), ']')]",
        "exists": "false"
    },
    "then": {
        "effect": "modify",
        "details": {
            "operations": [{
                "operation": "add",
                "field": "[concat('tags[', parameters('tagName'), ']')]",
                "value": "[resourcegroup().tags[parameters('tagName')]]"
            }],
            "roleDefinitionIds": [
                "/providers/microsoft.authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c"
            ]
        }
    }
}
```

### <a name="value"></a>값

**value**를 사용하여 조건을 구성할 수도 있습니다. **value**는 [매개 변수](#parameters), [지원되는 템플릿 함수](#policy-functions) 또는 리터럴에 대해 조건을 확인합니다.
**value**는 지원되는 모든 [조건](#conditions)과 쌍을 이룹니다.

> [!WARNING]
> _템플릿 함수_의 결과가 오류이면 정책 평가가 실패합니다. 실패한 평가는 암시적 **거부**입니다. 자세한 내용은 [템플릿 오류 방지](#avoiding-template-failures)를 참조하세요. [적용모드를](./assignment-structure.md#enforcement-mode) 사용하여 **DoNotEnforce의** 모드를 사용하여 새 정책 정의를 테스트하고 유효성을 검사하는 동안 새 리소스 또는 업데이트된 리소스에 대한 실패한 평가의 영향을 방지합니다.

#### <a name="value-examples"></a>값 예제

이 정책 규칙 예제는 **value**를 사용하여 `resourceGroup()` 함수의 결과와 반환된 **name** 속성을 `*netrg`의 **like** 조건과 비교합니다. 규칙은 이름이 `*netrg`로 끝나는 리소스 그룹에서 `Microsoft.Network/*` **type**이 아닌 리소스를 모두 거부합니다.

```json
{
    "if": {
        "allOf": [{
                "value": "[resourceGroup().name]",
                "like": "*netrg"
            },
            {
                "field": "type",
                "notLike": "Microsoft.Network/*"
            }
        ]
    },
    "then": {
        "effect": "deny"
    }
}
```

이 정책 규칙 예제에서는 **value**를 사용하여 여러 중첩 함수의 결과가 **equals** `true`인지 확인합니다. 이 규칙은 최소 3개의 태그가 없는 리소스를 모두 거부합니다.

```json
{
    "mode": "indexed",
    "policyRule": {
        "if": {
            "value": "[less(length(field('tags')), 3)]",
            "equals": "true"
        },
        "then": {
            "effect": "deny"
        }
    }
}
```

#### <a name="avoiding-template-failures"></a>템플릿 오류 방지

**값에서** _템플릿 함수를_ 사용하면 많은 복잡한 중첩 함수가 허용됩니다. _템플릿 함수_의 결과가 오류이면 정책 평가가 실패합니다. 실패한 평가는 암시적 **거부**입니다. 특정 시나리오에서 실패하는 **값의** 예입니다.

```json
{
    "policyRule": {
        "if": {
            "value": "[substring(field('name'), 0, 3)]",
            "equals": "abc"
        },
        "then": {
            "effect": "audit"
        }
    }
}
```

위의 예제 정책 규칙은 [substring()를](../../../azure-resource-manager/templates/template-functions-string.md#substring) 사용하여 **이름의** 처음 세 문자를 **abc와**비교합니다. **이름이** 3자보다 짧으면 `substring()` 함수에 오류가 발생합니다. 이 오류로 인해 정책이 **거부** 효과가 됩니다.

대신 [if()](../../../azure-resource-manager/templates/template-functions-logical.md#if) 함수를 사용하여 **이름의** 처음 세 문자가 세 문자보다 짧은 **이름을** 허용하지 않고 **abc와** 동일한지 확인하여 오류가 발생합니다.

```json
{
    "policyRule": {
        "if": {
            "value": "[if(greaterOrEquals(length(field('name')), 3), substring(field('name'), 0, 3), 'not starting with abc')]",
            "equals": "abc"
        },
        "then": {
            "effect": "audit"
        }
    }
}
```

개정된 정책 규칙을 `if()` 사용하여 3자 미만의 값을 `substring()` 얻기 전에 **이름의** 길이를 확인합니다. **이름이** 너무 짧으면 "abc로 시작하지 않음"값이 대신 반환되고 **abc와**비교됩니다. **abc에서** 시작되지 않는 짧은 이름의 리소스는 여전히 정책 규칙에 실패하지만 평가 중에 더 이상 오류가 발생하지 않습니다.

### <a name="count"></a>개수

리소스 페이로드에서 배열의 멤버 수를 계산하는 조건은 조건 식을 만족시면 **count** 식을 사용하여 형성될 수 있습니다. 일반적인 시나리오는 배열 멤버가 조건을 충족하는지 '하나 이상', '정확히 하나', '모두'또는 '없음'인지 확인하는 것입니다. **count는** 조건 식에 대해 각 [ \[ \* \] 별칭](#understanding-the--alias) 배열 멤버를 평가하고 _실제_ 결과를 합산한 다음 식 연산자와 비교합니다.

**count** 식의 구조는 다음과 있습니다.

```json
{
    "count": {
        "field": "<[*] alias>",
        "where": {
            /* condition expression */
        }
    },
    "<condition>": "<compare the count of true condition expression array members to this value>"
}
```

다음 속성은 **count와**함께 사용됩니다.

- **count.field(필수):** 배열에 대한 경로를 포함하며 배열 별칭이어야 합니다. 배열이 없는 경우 조건 식을 고려하지 않고 _식이 false로_ 평가됩니다.
- **count.where(선택** 사항): **count.field의**각 [ \[ \* \] 별칭](#understanding-the--alias) 배열 멤버를 개별적으로 평가하는 조건 식입니다. 이 속성을 제공하지 않으면 '필드' 경로가 있는 모든 배열 멤버가 _true로_평가됩니다. 모든 [조건은](../concepts/definition-structure.md#conditions) 이 속성 내에서 사용할 수 있습니다.
  [논리 연산자는](#logical-operators) 이 속성 내에서 복잡한 평가 요구 사항을 만드는 데 사용할 수 있습니다.
- **조건(필수): 값은 count.where 조건 식을 충족한 항목 수와 비교됩니다.\> \<** **count.where** 숫자 [조건을](../concepts/definition-structure.md#conditions) 사용해야 합니다.

#### <a name="count-examples"></a>예제 수

예제 1: 배열이 비어 있는지 확인

```json
{
    "count": {
        "field": "Microsoft.Network/networkSecurityGroups/securityRules[*]"
    },
    "equals": 0
}
```

예제 2: 조건 식을 충족하도록 배열 멤버가 하나만 있는지 확인합니다.

```json
{
    "count": {
        "field": "Microsoft.Network/networkSecurityGroups/securityRules[*]",
        "where": {
            "field": "Microsoft.Network/networkSecurityGroups/securityRules[*].description",
            "equals": "My unique description"
        }
    },
    "equals": 1
}
```

예 3: 조건 식을 충족하려면 하나 이상의 배열 멤버가 있는지 확인합니다.

```json
{
    "count": {
        "field": "Microsoft.Network/networkSecurityGroups/securityRules[*]",
        "where": {
            "field": "Microsoft.Network/networkSecurityGroups/securityRules[*].description",
            "equals": "My common description"
        }
    },
    "greaterOrEquals": 1
}
```

예제 4: 모든 개체 배열 멤버가 조건 식과 만나는지 확인합니다.

```json
{
    "count": {
        "field": "Microsoft.Network/networkSecurityGroups/securityRules[*]",
        "where": {
            "field": "Microsoft.Network/networkSecurityGroups/securityRules[*].description",
            "equals": "description"
        }
    },
    "equals": "[length(field('Microsoft.Network/networkSecurityGroups/securityRules[*]'))]"
}
```

예제 5: 모든 문자열 배열 멤버가 조건 식을 충족하는지 확인합니다.

```json
{
    "count": {
        "field": "Microsoft.Sql/servers/securityAlertPolicies/emailAddresses[*]",
        "where": {
            "field": "Microsoft.Sql/servers/securityAlertPolicies/emailAddresses[*]",
            "like": "*@contoso.com"
        }
    },
    "equals": "[length(field('Microsoft.Sql/servers/securityAlertPolicies/emailAddresses[*]'))]"
}
```

예제 6: 내부 **값** 에 **필드를** 사용하여 모든 배열 멤버가 조건 식을 충족하는지 확인합니다.

```json
{
    "count": {
        "field": "Microsoft.Sql/servers/securityAlertPolicies/emailAddresses[*]",
        "where": {
            "value": "[last(split(first(field('Microsoft.Sql/servers/securityAlertPolicies/emailAddresses[*]')), '@'))]",
            "equals": "contoso.com"
        }
    },
    "equals": "[length(field('Microsoft.Sql/servers/securityAlertPolicies/emailAddresses[*]'))]"
}
```

예 7: 하나 이상의 배열 멤버가 조건 식의 여러 속성과 일치하는지 확인합니다.

```json
{
    "count": {
        "field": "Microsoft.Network/networkSecurityGroups/securityRules[*]",
        "where": {
            "allOf": [
                {
                    "field": "Microsoft.Network/networkSecurityGroups/securityRules[*].direction",
                    "equals": "Inbound"
                },
                {
                    "field": "Microsoft.Network/networkSecurityGroups/securityRules[*].access",
                    "equals": "Allow"
                },
                {
                    "field": "Microsoft.Network/networkSecurityGroups/securityRules[*].destinationPortRange",
                    "equals": "3389"
                }
            ]
        }
    },
    "greater": 0
}
```

### <a name="effect"></a>영향

Azure 정책은 다음과 같은 유형의 효과를 지원합니다.

- **추가**는 정의된 필드 집합을 요청에 추가합니다.
- **Audit**: 활동 로그에 경고 이벤트를 생성하지만 요청을 실패하지는 않습니다.
- **AuditIfNotExists**: 관련 리소스가 없는 경우 활동 로그에 경고 이벤트를 생성합니다.
- **거부**: 활동 로그에 이벤트를 생성하고 요청을 실패합니다.
- **DeployIfNotExists**: 관련 리소스가 아직 존재하지 않는 경우 배포합니다.
- **Disabled**: 정책 규칙 준수에 대해 리소스를 평가하지 않습니다.
- **EnforceOPAConstraint** (미리 보기): Azure에서 자체 관리 되는 Kubernetes 클러스터에 대 한 게이트 키퍼 v3와 오픈 정책 에이전트 입학 컨트롤러를 구성 합니다 (미리 보기)
- **EnforceRegoPolicy(미리** 보기): Azure Kubernetes 서비스에서 게이트키퍼 v2를 사용하면 열린 정책 에이전트 입학 컨트롤러를 구성합니다.
- **수정**: 리소스에서 정의된 태그를 추가, 업데이트 또는 제거합니다.

각 효과, 평가 순서, 속성 및 예제에 대한 자세한 내용은 [Azure 정책 효과 이해를](effects.md)참조하십시오.

### <a name="policy-functions"></a>정책 함수

다음 함수 및 사용자 정의 [함수를](../../../azure-resource-manager/templates/template-functions.md) 제외한 모든 리소스 관리자 템플릿 함수는 정책 규칙 내에서 사용할 수 있습니다.

- copyIndex()
- deployment()
- list*
- newGuid()
- 픽존()
- providers()
- reference()
- resourceId()
- variables()

> [!NOTE]
> 이러한 함수는 `details.deployment.properties.template` **deployIfNotExists** 정책 정의에서 템플릿 배포 부분 내에서 계속 사용할 수 있습니다.

다음 함수는 정책 규칙에서 사용할 수 있지만 Azure 리소스 관리자 템플릿에서 사용하는 것과 다릅니다.

- `utcNow()`- 리소스 관리자 템플릿과 달리 기본값 외부에서 사용할 수 있습니다.
  - 유니버설 ISO 8601 DateTime 형식의 현재 날짜 및 시간으로 설정된 문자열을 반환합니다.

다음 기능은 정책 규칙에서만 사용할 수 있습니다.

- `addDays(dateTime, numberOfDaysToAdd)`
  - **dateTime**: [필수] 문자열 - 유니버설 ISO 8601 DateTime 형식의 문자열 'yyy-MM-ddTHH:mm:ss.ffffffZ'
  - **numberOfDaysToAdd**: [필수] 정수 - 추가할 일 수
- `field(fieldName)`
  - **필드 이름**: [필수] 문자열 - 검색할 [필드의](#fields) 이름
  - If 조건에서 평가 중인 리소스에서 해당 필드의 값을 반환합니다.
  - `field`는 주로 평가 중인 리소스의 필드를 참조하기 위해 **AuditIfNotExists** 및 **DeployIfNotExists**와 함께 사용합니다. 이 사용 예제는 [DeployIfNotExists 예제](effects.md#deployifnotexists-example)에서 볼 수 있습니다.
- `requestContext().apiVersion`
  - 정책 평가를 트리거한 요청의 API 버전을 반환합니다(예: `2019-09-01`).
    이 API 버전은 PUT/PATCH 요청에 사용된 API 버전으로, 리소스 생성/업데이트에 대한 평가를 위해 사용됩니다. 최신 API 버전은 항상 기존 리소스에 대한 규정 준수 평가 중에 사용됩니다.
  
#### <a name="policy-function-example"></a>정책 함수 예제

이 정책 규칙 예제에서는 `resourceGroup` 리소스 함수를 `concat` 배열 및 개체 함수와 함께 사용하여 **name** 속성을 가져오고 리소스 이름을 리소스 그룹 이름으로 시작하도록 하는 `like` 조건을 작성합니다.

```json
{
    "if": {
        "not": {
            "field": "name",
            "like": "[concat(resourceGroup().name,'*')]"
        }
    },
    "then": {
        "effect": "deny"
    }
}
```

## <a name="aliases"></a>별칭

리소스 유형에 대한 특정 속성에 액세스하려면 속성 별칭을 사용합니다. 별칭을 사용하면 리소스의 속성에 허용되는 값이나 조건을 제한할 수 있습니다. 각 별칭은 주어진 리소스 유형에 대해 서로 다른 API 버전의 경로에 매핑됩니다. 정책 평가 중에 정책 엔진은 해당 API 버전에 대한 속성 경로를 가져옵니다.

별칭의 목록은 항상 업데이트됩니다. 현재 Azure Policy에서 지원하는 별칭을 찾으려면 다음 방법 중 하나를 사용합니다.

- 비주얼 스튜디오 코드에 대 한 Azure 정책 확장 (권장)

  Visual [Studio 코드에 대한 Azure 정책 확장을](../how-to/extension-for-vscode.md) 사용하여 리소스 속성에 대한 별칭을 보고 검색합니다.

  ![비주얼 스튜디오 코드에 대 한 Azure 정책 확장](../media/extension-for-vscode/extension-hover-shows-property-alias.png)

- Azure Resource Graph

  연산자에서 `project` 리소스의 **별칭을** 표시합니다.

  ```kusto
  Resources
  | where type=~'microsoft.storage/storageaccounts'
  | limit 1
  | project aliases
  ```
  
  ```azurecli-interactive
  az graph query -q "Resources | where type=~'microsoft.storage/storageaccounts' | limit 1 | project aliases"
  ```
  
  ```azurepowershell-interactive
  Search-AzGraph -Query "Resources | where type=~'microsoft.storage/storageaccounts' | limit 1 | project aliases"
  ```

- Azure PowerShell

  ```azurepowershell-interactive
  # Login first with Connect-AzAccount if not using Cloud Shell

  # Use Get-AzPolicyAlias to list available providers
  Get-AzPolicyAlias -ListAvailable

  # Use Get-AzPolicyAlias to list aliases for a Namespace (such as Azure Compute -- Microsoft.Compute)
  (Get-AzPolicyAlias -NamespaceMatch 'compute').Aliases
  ```

- Azure CLI

  ```azurecli-interactive
  # Login first with az login if not using Cloud Shell

  # List namespaces
  az provider list --query [*].namespace

  # Get Azure Policy aliases for a specific Namespace (such as Azure Compute -- Microsoft.Compute)
  az provider show --namespace Microsoft.Compute --expand "resourceTypes/aliases" --query "resourceTypes[].aliases[].name"
  ```

- REST API/ARMClient

  ```http
  GET https://management.azure.com/providers/?api-version=2017-08-01&$expand=resourceTypes/aliases
  ```

### <a name="understanding-the--alias"></a>[*] 별칭 이해

사용 가능한 별칭 중 일부는 '일반' 이름으로 표시되는 버전과 ** \[ \* ** 연결된 다른 별칭을 가지고 있습니다. 다음은 그 예입니다.

- `Microsoft.Storage/storageAccounts/networkAcls.ipRules`
- `Microsoft.Storage/storageAccounts/networkAcls.ipRules[*]`

'일반' 별칭은 필드를 단일 값으로 나타냅니다. 이 필드는 전체 값 집합이 정의된 대로 정확하게 정의되어야 하는 경우 정확한 일치 비교 시나리오에 적합합니다.

별칭을 ** \[ \* ** 사용하면 배열의 각 요소 값과 각 요소의 특정 속성을 비교할 수 있습니다. 이 방법을 사용하면 요소 속성을 '없는 경우', '있는 경우'또는 '모두'시나리오에 대해 비교할 수 있습니다. 보다 복잡한 시나리오의 경우 [count](#count) 조건 식을 사용합니다. **ipRules를\[\*** 사용 하 여 예제는 모든 _작업이_ _Deny,_ 하지만 얼마나 많은 규칙 존재 또는 IP _값에_ 대 한 걱정 하지 확인 될 것 이다.
이 샘플 규칙은 **\[\*\]ipRules .value의** 일치 항목을 **10.0.4.1로** 검사하고 하나 이상의 일치 항목을 찾지 못하는 경우에만 **effectType을** 적용합니다.

```json
"policyRule": {
    "if": {
        "allOf": [
            {
                "field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules",
                "exists": "true"
            },
            {
                "field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules[*].value",
                "notEquals": "10.0.4.1"
            }
        ]
    },
    "then": {
        "effect": "[parameters('effectType')]"
    }
}
```

자세한 내용은 [[]\*별칭 을 평가합니다.](../how-to/author-policies-for-arrays.md#evaluating-the--alias)

## <a name="initiatives"></a>이니셔티브

그룹을 단일 항목으로 작업할 수 있기 때문에 이니셔티브를 사용하면 여러 관련 정책 정의를 그룹화할 수 있어 할당 및 관리를 간소화합니다. 예를 들어 관련 태그 지정 정책 정의를 단일 이니셔티브로 그룹화할 수 있습니다. 각 정책을 개별적으로 할당하는 대신 이니셔티브를 적용합니다.

> [!NOTE]
> 이니셔티브가 할당되면 이니셔티브 수준 매개 변수를 변경할 수 없습니다. 이 때문에 매개 변수를 정의할 때 **defaultValue를** 설정하는 것이 좋습니다.

다음 예제에서는 두 태그 `costCenter`과 `productName`를 처리하기 위한 이니셔티브를 만드는 방법을 보여 줍니다. 기본 태그 값을 적용하려면 두 가지 기본 제공 정책을 사용합니다.

```json
{
    "properties": {
        "displayName": "Billing Tags Policy",
        "policyType": "Custom",
        "description": "Specify cost Center tag and product name tag",
        "parameters": {
            "costCenterValue": {
                "type": "String",
                "metadata": {
                    "description": "required value for Cost Center tag"
                },
                "defaultValue": "DefaultCostCenter"
            },
            "productNameValue": {
                "type": "String",
                "metadata": {
                    "description": "required value for product Name tag"
                },
                "defaultValue": "DefaultProduct"
            }
        },
        "policyDefinitions": [{
                "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62",
                "parameters": {
                    "tagName": {
                        "value": "costCenter"
                    },
                    "tagValue": {
                        "value": "[parameters('costCenterValue')]"
                    }
                }
            },
            {
                "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/2a0e14a6-b0a6-4fab-991a-187a4f81c498",
                "parameters": {
                    "tagName": {
                        "value": "costCenter"
                    },
                    "tagValue": {
                        "value": "[parameters('costCenterValue')]"
                    }
                }
            },
            {
                "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62",
                "parameters": {
                    "tagName": {
                        "value": "productName"
                    },
                    "tagValue": {
                        "value": "[parameters('productNameValue')]"
                    }
                }
            },
            {
                "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/2a0e14a6-b0a6-4fab-991a-187a4f81c498",
                "parameters": {
                    "tagName": {
                        "value": "productName"
                    },
                    "tagValue": {
                        "value": "[parameters('productNameValue')]"
                    }
                }
            }
        ]
    }
}
```

## <a name="next-steps"></a>다음 단계

- Azure 정책 [샘플의 예제를 검토합니다.](../samples/index.md)
- [정책 효과 이해](effects.md)를 검토합니다.
- [프로그래밍 방식으로 정책을 만드는](../how-to/programmatically-create.md)방법을 이해합니다.
- [규정 준수 데이터를 얻는](../how-to/get-compliance-data.md)방법에 대해 알아봅니다.
- [비준수 리소스를 수정하는](../how-to/remediate-resources.md)방법에 대해 알아봅니다.
- 관리 그룹이 Azure 관리 그룹으로 리소스 구성을 통해 어떤 내용인지 [검토합니다.](../../management-groups/overview.md)
