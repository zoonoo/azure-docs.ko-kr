---
title: Azure Policy 정의 구조 | Microsoft Docs
description: 정책이 언제 적용되고 어떤 작업이 실행될지 설명함으로써 Azure Policy가 조직의 리소스에 대한 규칙을 설정하기 위해 리소스 정책 정의가 어떻게 사용되는지 설명합니다.
services: azure-policy
keywords: ''
author: DCtheGeek
ms.author: dacoulte
ms.date: 04/18/2018
ms.topic: article
ms.service: azure-policy
ms.custom: ''
ms.openlocfilehash: 8b89e1c8ccfcfd7b53ecdd9172590424d1c7ae4c
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
---
# <a name="azure-policy-definition-structure"></a>Azure Policy 정의 구조

Azure Policy에서 사용되는 리소스 정책을 통해 정책이 언제 적용되고 어떤 작업이 실행될지 설명함으로써 조직의 리소스에 대한 규칙을 설정할 수 있습니다. 규칙을 정의하여 비용을 제어하고 리소스를 보다 쉽게 관리할 수 있습니다. 예를 들어, 특정 유형의 가상 머신만 허용되게 지정할 수 있습니다. 또는 모든 리소스가 특정 태그를 갖도록 요구할 수 있습니다. 정책은 모든 자식 리소스에 의해 상속됩니다. 이에 따라 리소스 그룹에 정책을 적용하면 해당 리소스 그룹의 모든 리소스에 해당 정책을 적용할 수 있습니다.

JSON을 사용하여 정책 정의를 만듭니다. 정책 정의에는 다음 요소가 포함됩니다.

* 모드
* 매개 변수
* 표시 이름
* description
* 정책 규칙
  * 논리 평가
  * 영향

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
        }
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

모든 Azure Policy 템플릿 샘플은 [Azure Policy에 대한 템플릿](json-samples.md)에 있습니다.

## <a name="mode"></a>Mode

**mode**는 정책에 대해 평가할 리소스 종류를 결정합니다. 지원되는 모드는 다음과 같습니다.
* `all`: 리소스 그룹 및 모든 리소스 종류를 평가합니다.
* `indexed`: 태그 및 위치를 지원하는 리소스 종류만 평가합니다.

대부분 **mode**를 `all`로 설정하는 것이 좋습니다. 포털을 통해 생성된 모든 정책 정의는 `all` 모드를 사용합니다. PowerShell 또는 Azure CLI를 사용하는 경우 **mode** 매개 변수를 수동으로 지정해야 합니다. 정책 정의에 **모드** 값이 포함되어 있지 않으면 이전 버전과의 호환성을 위해 `indexed`로 기본값이 설정됩니다.

`indexed`는 태그 또는 위치를 시스템에 적용하는 정책을 만들 때 사용해야 합니다. 필수는 아니지만, 태그 및 위치를 지원하지 않는 리소스가 준수 결과에서 호환되지 않음으로 표시되지 않도록 방지합니다. 한 가지 예외는 **리소스 그룹**입니다. 리소스 그룹에서 위치 또는 태그를 적용하려고 하는 정책은 **모드**를 `all`로 설정하고 구체적으로 `Microsoft.Resources/subscriptions/resourceGroup` 형식을 대상으로 지정해야 합니다. 예를 들어 [리소스 그룹 태그 적용](scripts/enforce-tag-rg.md)을 참조하세요.

## <a name="parameters"></a>매개 변수

매개 변수는 정책 정의의 수를 줄여 정책 관리를 간소화하는 데 도움이 됩니다. 양식의 필드 `name`, `address`, `city`, `state`와 같은 매개 변수에 관해 생각해 봅니다. 이러한 매개 변수는 항상 그대로 유지되지만, 그 값은 양식을 작성하는 개별 값에 기초하여 달라집니다. 매개 변수는 정책을 만들 때와 같은 방법으로 작동합니다. 정책 정의에 매개 변수를 포함함으로써 서로 다른 값을 사용하여 다양한 시나리오에 대해 해당 정책을 재사용할 수 있습니다.

예를 들어 리소스를 배포할 수 있는 위치를 제한하는 리소스 속성에 대한 정책을 정의할 수 있습니다. 이 경우 정책을 만들 때 다음 매개 변수를 선언합니다.


```json
"parameters": {
  "allowedLocations": {
    "type": "array",
    "metadata": {
      "description": "The list of allowed locations for resources.",
      "displayName": "Allowed locations",
      "strongType": "location"
    }
  }
}
```

매개 변수의 형식은 문자열 또는 배열이 될 수 있습니다. 메타데이터 속성은 Azure Portal과 같은 도구에 사용되어 사용자 친화적 정보를 표시합니다.

메타데이터 속성 안에서 **strongType**을 사용하여 Azure Portal 내의 다중 선택 옵션 목록을 제공할 수 있습니다.  **strongType**에서 현재 허용되는 값은 다음과 같습니다.

* `"location"`
* `"resourceTypes"`
* `"storageSkus"`
* `"vmSKUs"`
* `"existingResourceGroups"`
* `"omsWorkspace"`

정책 규칙에서 다음 구문을 사용하여 매개 변수를 참조할 수 있습니다.

```json
{
    "field": "location",
    "in": "[parameters('allowedLocations')]"
}
```

## <a name="display-name-and-description"></a>표시 이름 및 설명

**displayName** 및 **설명**을 사용하여 정책 정의를 식별하고 사용하는 시기에 대한 컨텍스트를 제공할 수 있습니다.

## <a name="policy-rule"></a>정책 규칙

정책 규칙은 **If** 및 **Then** 블록으로 이루어집니다. **If** 블록에서 정책이 적용되는 시점을 지정하는 하나 이상의 조건을 정의합니다. 이러한 조건에 논리 연산자를 적용하여 정책에 대한 시나리오를 정확하게 정의할 수 있습니다.

**Then** 블록에서 **If** 조건이 충족된 경우에 발생하는 효과를 정의합니다.

```json
{
  "if": {
    <condition> | <logical operator>
  },
  "then": {
    "effect": "deny | audit | append | auditIfNotExists | deployIfNotExists"
  }
}
```

### <a name="logical-operators"></a>논리 연산자

지원되는 논리 연산자는 다음과 같습니다.

* `"not": {condition  or operator}`
* `"allOf": [{condition or operator},{condition or operator}]`
* `"anyOf": [{condition or operator},{condition or operator}]`

**not** 구문은 조건의 결과를 반전시킵니다. **allOf** 구문(논리 **And** 작업과 비슷함)은 모든 조건이 true여야 합니다. **anyOf** 구문(논리 **Or** 작업과 비슷함)은 하나 이상의 조건이 true여야 합니다.

논리 연산자를 중첩할 수 있습니다. 다음 예제에서는 **allOf** 작업 내에 중첩된 **not** 작업을 표시합니다.

```json
"if": {
  "allOf": [
    {
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

조건은 **field**에서 특정 기준을 충족하는지를 평가합니다. 지원되는 조건은 다음과 같습니다.

* `"equals": "value"`
* `"notEquals": "value"`
* `"like": "value"`
* `"notLike": "value"`
* `"match": "value"`
* `"notMatch": "value"`
* `"contains": "value"`
* `"notContains": "value"`
* `"in": ["value1","value2"]`
* `"notIn": ["value1","value2"]`
* `"containsKey": "keyName"`
* `"notContainsKey": "keyName"`
* `"exists": "bool"`

**like** 및 **notLike** 조건을 사용하는 경우 값에 와일드카드(*)를 제공할 수 있습니다.

**match** 및 **notMatch** 조건을 사용하는 경우 자릿수 하나를 나타내려면 `#`를, 문자 하나를 나타내려면 `?`를, 해당 실제 문자를 나타내려면 다른 문자를 입력합니다. 예를 들어 [승인된 VM 이미지](scripts/allowed-custom-images.md)를 참조하세요.

### <a name="fields"></a>필드
조건은 필드를 사용하여 구성됩니다. 필드는 리소스의 상태를 설명하는 데 사용되는 리소스 요청 페이로드의 속성을 나타냅니다.  

다음 필드가 지원됩니다.

* `name`
* `fullName`
  * 부모(예: "myServer/myDatabase")를 비롯한 리소스의 전체 이름을 반환합니다.
* `kind`
* `type`
* `location`
* `tags`
* `tags.tagName`
* `tags[tagName]`
  * 이 대괄호 구문은 마침표가 포함된 태그 이름을 지원합니다.
* 속성 별칭 - 목록은 [별칭](#aliases)을 참조하세요.

### <a name="alternative-accessors"></a>대체 접근자
**필드**는 정책 규칙에서 사용되는 기본 접근자입니다. 평가되는 리소스를 직접 검사합니다. 그러나 정책에서는 다른 하나의 접근자인 **원본**을 지원합니다.

```json
"source": "action",
"equals": "Microsoft.Compute/virtualMachines/write"
```

**원본**은 하나의 값인 **작업**만 지원합니다. 작업은 평가되는 요청의 권한 부여 동작을 반환합니다. 권한 부여 작업은 [활동 로그](../monitoring-and-diagnostics/monitoring-activity-log-schema.md)의 권한 부여 섹션에서 노출됩니다.

정책이 백그라운드에서 기존 리소스를 평가하는 경우 **작업**을 리소스 형식에 대한 `/write` 권한 부여 작업으로 설정합니다.

### <a name="effect"></a>결과
정책은 다음과 같은 형식의 결과 지원합니다.

* **거부**는 감사 로그에 이벤트를 생성하고 요청을 실패합니다.
* **감사**는 감사 로그에 경고 이벤트를 생성하지만 요청을 실패하지는 않습니다.
* **추가**는 정의된 필드 집합을 요청에 추가합니다.
* **AuditIfNotExists** - 리소스가 없으면 감사를 사용하도록 설정합니다.
* **DeployIfNotExists**: 리소스가 아직 없으면 배포합니다. 현재 이 결과는 기본 제공 정책을 통해서만 지원됩니다.

**append**의 경우 아래와 같이 details(세부 정보)를 제공해야 합니다.

```json
"effect": "append",
"details": [
  {
    "field": "field name",
    "value": "value of the field"
  }
]
```

값은 문자열 또는 JSON 형식의 개체일 수 있습니다.

**AuditIfNotExists** 및 **DeployIfNotExists**를 사용하면 관련 리소스의 존재 여부를 평가하고, 해당 리소스가 없을 경우 규칙과 그 결과를 적용할 수 있습니다. 예를 들어 모든 가상 네트워크에 대해 네트워크 감시자를 배포하도록 요구할 수 있습니다.
가상 머신 확장이 배포되지 않은 경우의 감사 예제는 [확장이 존재하지 않을 경우 감사](scripts/audit-ext-not-exist.md)를 참조하세요.


## <a name="aliases"></a>Aliases

리소스 유형에 대한 특정 속성에 액세스하려면 속성 별칭을 사용합니다. 별칭을 사용하면 리소스의 속성에 허용되는 값이나 조건을 제한할 수 있습니다. 각 별칭은 주어진 리소스 유형에 대해 서로 다른 API 버전의 경로에 매핑됩니다. 정책 평가 중에 정책 엔진은 해당 API 버전에 대한 속성 경로를 가져옵니다.

**Microsoft.Cache/Redis**

| Alias | 설명 |
| ----- | ----------- |
| Microsoft.Cache/Redis/enableNonSslPort | 비-ssl Redis 서버 포트(6379)가 사용하도록 설정되었는지 여부를 설정합니다. |
| Microsoft.Cache/Redis/shardCount | 프리미엄 클러스터 캐시에 만들 분할된 데이터베이스 수를 설정합니다.  |
| Microsoft.Cache/Redis/sku.capacity | 배포할 Redis Cache의 크기를 설정합니다.  |
| Microsoft.Cache/Redis/sku.family | 사용할 SKU 제품군을 설정합니다. |
| Microsoft.Cache/Redis/sku.name | 배포할 Redis Cache 유형을 설정합니다. |

**Microsoft.Cdn/profiles**

| Alias | 설명 |
| ----- | ----------- |
| Microsoft.CDN/profiles/sku.name | 가격 책정 계층의 이름을 설정합니다. |

**Microsoft.Compute/disks**

| Alias | 설명 |
| ----- | ----------- |
| Microsoft.Compute/imageOffer | 플랫폼 이미지 또는 가상 머신을 만드는 데 사용된 Marketplace 이미지 제안을 설정합니다. |
| Microsoft.Compute/imagePublisher | 플랫폼 이미지 또는 가상 컴퓨터를 만드는 데 사용된 Marketplace 이미지의 게시자를 설정합니다. |
| Microsoft.Compute/imageSku | 플랫폼 이미지 또는 가상 머신을 만드는 데 사용된 Marketplace 이미지의 SKU를 설정합니다. |
| Microsoft.Compute/imageVersion | 플랫폼 이미지 또는 가상 머신을 만드는 데 사용된 Marketplace 이미지의 버전을 설정합니다. |


**Microsoft.Compute/virtualMachines**

| Alias | 설명 |
| ----- | ----------- |
| Microsoft.Compute/imageId | 가상 컴퓨터를 만드는 데 사용되는 이미지의 식별자를 설정합니다. |
| Microsoft.Compute/imageOffer | 플랫폼 이미지 또는 가상 머신을 만드는 데 사용된 Marketplace 이미지 제안을 설정합니다. |
| Microsoft.Compute/imagePublisher | 플랫폼 이미지 또는 가상 컴퓨터를 만드는 데 사용된 Marketplace 이미지의 게시자를 설정합니다. |
| Microsoft.Compute/imageSku | 플랫폼 이미지 또는 가상 머신을 만드는 데 사용된 Marketplace 이미지의 SKU를 설정합니다. |
| Microsoft.Compute/imageVersion | 플랫폼 이미지 또는 가상 머신을 만드는 데 사용된 Marketplace 이미지의 버전을 설정합니다. |
| Microsoft.Compute/licenseType | 온-프레미스로 사용이 허가된 이미지 또는 디스크를 설정합니다. 이 값은 Windows Server 운영 체제를 포함하는 이미지에만 사용됩니다.  |
| Microsoft.Compute/virtualMachines/imageOffer | 플랫폼 이미지 또는 가상 머신을 만드는 데 사용된 Marketplace 이미지 제안을 설정합니다. |
| Microsoft.Compute/virtualMachines/imagePublisher | 플랫폼 이미지 또는 가상 머신을 만드는 데 사용된 Marketplace 이미지의 게시자를 설정합니다. |
| Microsoft.Compute/virtualMachines/imageSku | 플랫폼 이미지 또는 가상 머신을 만드는 데 사용된 Marketplace 이미지의 SKU를 설정합니다. |
| Microsoft.Compute/virtualMachines/imageVersion | 플랫폼 이미지 또는 가상 컴퓨터를 만드는 데 사용된 Marketplace 이미지의 버전을 설정합니다. |
| Microsoft.Compute/virtualMachines/osDisk.Uri | Vhd URI를 설정합니다. |
| Microsoft.Compute/virtualMachines/sku.name | 가상 머신의 크기를 설정합니다. |
| Microsoft.Compute/virtualMachines/availabilitySet.id | 가상 머신에 사용할 가용성 집합 ID를 설정합니다. |

**Microsoft.Compute/virtualMachines/extensions**

| Alias | 설명 |
| ----- | ----------- |
| Microsoft.Compute/virtualMachines/extensions/publisher | 확장의 게시자 이름을 설정합니다. |
| Microsoft.Compute/virtualMachines/extensions/type | 확장 유형을 설정합니다. |
| Microsoft.Compute/virtualMachines/extensions/typeHandlerVersion | 확장 버전을 설정합니다. |

**Microsoft.Compute/virtualMachineScaleSets**

| Alias | 설명 |
| ----- | ----------- |
| Microsoft.Compute/imageId | 가상 컴퓨터를 만드는 데 사용되는 이미지의 식별자를 설정합니다. |
| Microsoft.Compute/imageOffer | 플랫폼 이미지 또는 가상 머신을 만드는 데 사용된 Marketplace 이미지 제안을 설정합니다. |
| Microsoft.Compute/imagePublisher | 플랫폼 이미지 또는 가상 컴퓨터를 만드는 데 사용된 Marketplace 이미지의 게시자를 설정합니다. |
| Microsoft.Compute/imageSku | 플랫폼 이미지 또는 가상 머신을 만드는 데 사용된 Marketplace 이미지의 SKU를 설정합니다. |
| Microsoft.Compute/imageVersion | 플랫폼 이미지 또는 가상 머신을 만드는 데 사용된 Marketplace 이미지의 버전을 설정합니다. |
| Microsoft.Compute/licenseType | 온-프레미스로 사용이 허가된 이미지 또는 디스크를 설정합니다. 이 값은 Windows Server 운영 체제를 포함하는 이미지에만 사용됩니다. |
| Microsoft.Compute/VirtualMachineScaleSets/computerNamePrefix | 확장 집합에서 모든 가상 머신에 대해 컴퓨터 이름 접두사를 설정합니다. |
| Microsoft.Compute/VirtualMachineScaleSets/osdisk.imageUrl | 사용자 이미지에 대한 Blob URI를 설정합니다. |
| Microsoft.Compute/VirtualMachineScaleSets/osdisk.vhdContainers | 확장 집합에 대한 운영 체제 디스크를 저장하는 데 사용되는 컨테이너 URL을 설정합니다. |
| Microsoft.Compute/VirtualMachineScaleSets/sku.name | 확장 집합에서 가상 머신 크기를 설정합니다. |
| Microsoft.Compute/VirtualMachineScaleSets/sku.tier | 확장 집합에서 가상 머신 계층을 설정합니다. |

**Microsoft.Network/applicationGateways**

| Alias | 설명 |
| ----- | ----------- |
| Microsoft.Network/applicationGateways/sku.name | 게이트웨이의 크기를 설정합니다. |

**Microsoft.Network/virtualNetworkGateways**

| Alias | 설명 |
| ----- | ----------- |
| Microsoft.Network/virtualNetworkGateways/gatewayType | 이 가상 네트워크 게이트웨이의 형식을 설정합니다. |
| Microsoft.Network/virtualNetworkGateways/sku.name | 게이트웨이 SKU 이름을 설정합니다. |

**Microsoft.Sql/servers**

| Alias | 설명 |
| ----- | ----------- |
| Microsoft.Sql/servers/version | 서버 버전을 설정합니다. |

**Microsoft.Sql/databases**

| Alias | 설명 |
| ----- | ----------- |
| Microsoft.Sql/servers/databases/edition | 데이터베이스의 버전을 설정합니다. |
| Microsoft.Sql/servers/databases/elasticPoolName | 데이터베이스가 들어 있는 탄력적 풀의 이름을 설정합니다. |
| Microsoft.Sql/servers/databases/requestedServiceObjectiveId | 데이터베이스의 구성된 서비스 수준 목표 ID를 설정합니다. |
| Microsoft.Sql/servers/databases/requestedServiceObjectiveName | 데이터베이스의 구성된 서비스 수준 목표의 이름을 설정합니다.  |

**Microsoft.Sql/elasticpools**

| Alias | 설명 |
| ----- | ----------- |
| servers/elasticpools | Microsoft.Sql/servers/elasticPools/dtu | 탄력적 데이터베이스 풀에 대한 총 공유 DTU를 설정합니다. |
| servers/elasticpools | Microsoft.Sql/servers/elasticPools/edition | 탄력적 풀의 버전을 설정합니다. |

**Microsoft.Storage/storageAccounts**

| Alias | 설명 |
| ----- | ----------- |
| Microsoft.Storage/storageAccounts/accessTier | 청구에 사용되는 액세스 계층을 설정합니다. |
| Microsoft.Storage/storageAccounts/accountType | SKU 이름을 설정합니다. |
| Microsoft.Storage/storageAccounts/enableBlobEncryption | 서비스에서 데이터가 Blob Storage 서비스에 저장될 때 데이터를 암호화할지 여부를 설정합니다. |
| Microsoft.Storage/storageAccounts/enableFileEncryption | 서비스에서 데이터가 File Storage 서비스에 저장될 때 데이터를 암호화할지 여부를 설정합니다. |
| Microsoft.Storage/storageAccounts/sku.name | SKU 이름을 설정합니다. |
| Microsoft.Storage/storageAccounts/supportsHttpsTrafficOnly | 저장소 서비스에 https 트래픽만 허용하도록 설정합니다. |
| Microsoft.Storage/storageAccounts/networkAcls.virtualNetworkRules[*].id | 가상 네트워크 서비스 엔드포인트가 설정되어 있는지 확인합니다. |

## <a name="initiatives"></a>이니셔티브

그룹을 단일 항목으로 작업할 수 있기 때문에 이니셔티브를 사용하면 여러 관련 정책 정의를 그룹화할 수 있어 할당 및 관리를 간소화합니다. 예를 들어 관련 태그 지정 정책 정의 모두를 단일 이니셔티브로 그룹화할 수 있습니다. 각 정책을 개별적으로 할당하는 대신 이니셔티브를 적용합니다.

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
                }
            },
            "productNameValue": {
                "type": "String",
                "metadata": {
                    "description": "required value for product Name tag"
                }
            }
        },
        "policyDefinitions": [
            {
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
    },
    "id": "/subscriptions/<subscription-id>/providers/Microsoft.Authorization/policySetDefinitions/billingTagsPolicy",
    "type": "Microsoft.Authorization/policySetDefinitions",
    "name": "billingTagsPolicy"
}
```

## <a name="next-steps"></a>다음 단계

- [Azure Policy에 대한 템플릿](json-samples.md)에서 Azure Policy 템플릿 샘플을 검토합니다.
