---
title: "Azure 리소스 정책 | Microsoft Docs"
description: "Azure Resource Manager 정책을 사용하여 배포하는 동안 일관된 리소스 속성을 설정했는지 확인하는 방법에 대해 설명합니다. 구독 또는 리소스 그룹에 정책을 적용할 수 있습니다."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: abde0f73-c0fe-4e6d-a1ee-32a6fce52a2d
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/09/2017
ms.author: tomfitz
ms.openlocfilehash: 6faa8b4d259eddb1b26345d8900e829f6ca4dba8
ms.sourcegitcommit: 6a6e14fdd9388333d3ededc02b1fb2fb3f8d56e5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/07/2017
---
# <a name="resource-policy-overview"></a>리소스 정책 개요
리소스 정책을 사용하면 조직에서 리소스에 대한 규칙을 설정할 수 있습니다. 규칙을 정의하여 비용을 제어하고 리소스를 보다 쉽게 관리할 수 있습니다. 예를 들어, 특정 유형의 가상 컴퓨터만 허용되게 지정할 수 있습니다. 또는 모든 리소스가 특정 태그를 갖도록 요구할 수 있습니다. 정책은 모든 자식 리소스에 의해 상속됩니다. 이에 따라 리소스 그룹에 정책을 적용하면 해당 리소스 그룹의 모든 리소스에 해당 정책을 적용할 수 있습니다.

정책에 대해 이해하기 위한 두 가지 개념이 있습니다.

* 정책 정의 - 정책이 적용되는 시점 및 수행할 작업을 설명합니다.
* 정책 할당 - 정책 정의를 범위(구독 또는 리소스 그룹)에 적용합니다.

이 항목은 정책 정의에 중점을 둡니다. 정책 할당에 대한 자세한 내용은 [Azure Portal을 사용하여 리소스 정책 할당 및 관리](resource-manager-policy-portal.md) 또는 [스크립트를 통해 리소스 정책 할당 및 관리](resource-manager-policy-create-assign.md)를 참조하세요.

리소스(PUT 및 PATCH 작업)를 만들고 업데이트할 때 정책이 평가됩니다.

## <a name="how-is-it-different-from-rbac"></a>RBAC(역할 기반 액세스 제어)와 어떻게 다르나요?
정책 및 RBAC(역할 기반 액세스 제어) 간에 몇 가지 차이점이 있습니다. RBAC는 다른 범위에 있는 **사용자** 작업에 중점을 둡니다. 예를 들어 사용자가 원하는 범위에서 리소스 그룹에 대한 참가자 역할에 추가됩니다. 따라서 사용자는 해당 리소스 그룹을 변경할 수 있습니다. 정책은 배포하는 동안 **리소스** 속성에 중점을 둡니다. 예를 들어, 정책을 통해 프로비전 가능한 리소스 유형을 제어할 수 있습니다. 또는 리소스를 프로비전할 수 있는 위치를 제한할 수 있습니다. RBAC와는 달리 정책은 기본적으로 허용 및 명시적 거부 시스템입니다. 

사용자는 RBAC를 통해 인증되어야 정책을 사용할 수 있습니다. 구체적으로 사용자 계정에는 다음 권한이 필요합니다.

* 정책을 정의할 수 있는 `Microsoft.Authorization/policydefinitions/write` 사용 권한
* 정책을 할당할 수 있는 `Microsoft.Authorization/policyassignments/write` 사용 권한 

이러한 사용 권한은 **참가자** 역할에 포함되지 않습니다.

## <a name="built-in-policies"></a>기본 제공 정책

Azure에서는 일부 기본 제공 정책 정의을 제공하여 정의해야 하는 정책의 수를 줄일 수 있습니다. 정책 정의를 진행하기 전에 기본 제공 정책에서 필요한 정의를 이미 제공하는지 고려해야 합니다. 기본 제공 정책은 다음과 같습니다.

* 허용되는 위치
* 허용되는 리소스 유형
* 허용되는 저장소 계정 SKU
* 허용되는 가상 컴퓨터 SKU
* 태그 및 기본값 적용
* 태그 및 값 적용
* 허용되지 않는 리소스 종류
* SQL Server 버전 12.0 필요
* 저장소 계정 암호화 필요

## <a name="policy-definition-structure"></a>정책 정의 구조
JSON을 사용하여 정책 정의를 만듭니다. 정책 정의에는 다음 요소가 포함됩니다.

* 모드
* 매개 변수
* 표시 이름
* description
* 정책 규칙
  * 논리 평가
  * 영향

다음 예제에서는 리소스가 배포되는 위치를 제한하는 정책을 보여줍니다.

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

## <a name="mode"></a>Mode

`mode`를 `all`로 설정하는 것이 좋습니다. **all**로 설정하면 정책에 대한 리소스 그룹 및 모든 리소스 종류가 평가됩니다. 포털에서는 모든 정책에 대해 **all**을 사용합니다. PowerShell 또는 Azure CLI를 사용하는 경우 `mode` 매개 변수를 지정하고 **all**로 설정해야 합니다.
 
이전에는 태그와 위치를 지원하는 리소스 종류에 대해서만 정책이 평가되었습니다. `indexed` 모드는 이 동작을 계속합니다. **all** 모드를 사용하면 태그와 위치를 지원하지 않는 리소스 종류에 대해서도 정책이 평가됩니다. [가상 네트워크 서브넷](https://github.com/Azure/azure-policy-samples/tree/master/samples/Network/enforce-nsg-on-subnet)은 새로 추가된 유형의 예제입니다. 또한 모드가 **all**로 설정되면 리소스 그룹이 평가됩니다. 예를 들어 [리소스 그룹에 태그를 적용](https://github.com/Azure/azure-policy-samples/tree/master/samples/ResourceGroup/enforce-resourceGroup-tags)할 수 있습니다. 

## <a name="parameters"></a>매개 변수
매개 변수의 사용은 정책 정의의 수를 줄여 정책 관리를 간소화하는 데 도움이 됩니다. 리소스 속성에 대한 정책을 정의하고(예: 리소스를 배포할 수 있는 위치 제한) 정의에 매개 변수를 포함합니다. 그런 다음 정책을 할당할 때 다른 값(예: 구독에 한 가지 일련의 위치 지정)을 전달하여 다양한 시나리오에 대한 정책 정의를 다시 사용할 수 있습니다.

정책 정의 만들 때 매개 변수를 선언합니다.

```json
"parameters": {
  "allowedLocations": {
    "type": "array",
    "metadata": {
      "description": "The list of allowed locations for resources.",
      "displayName": "Allowed locations"
    }
  }
}
```

매개 변수의 형식은 문자열 또는 배열이 될 수 있습니다. 메타데이터 속성은 Azure Portal과 같은 도구에 사용되어 사용자 친화적 정보를 표시합니다. 

정책 규칙에서 다음 구문을 사용하여 매개 변수를 참조할 수 있습니다. 

```json
{ 
    "field": "location",
    "in": "[parameters('allowedLocations')]"
}
```

## <a name="display-name-and-description"></a>표시 이름 및 설명

**displayName** 및 **설명**을 사용하여 정책 정의를 식별하고 사용하는 시기에 대한 컨텍스트를 제공합니다.

## <a name="policy-rule"></a>정책 규칙

정책 규칙은 **If** 및 **Then** 블록으로 이루어집니다. **If** 블록에서 정책이 적용되는 시점을 지정하는 하나 이상의 조건을 정의합니다. 이러한 조건에 논리 연산자를 적용하여 정책에 대한 시나리오를 정확하게 정의할 수 있습니다. **Then** 블록에서 **If** 조건이 충족된 경우에 발생하는 효과를 정의합니다.

```json
{
  "if": {
    <condition> | <logical operator>
  },
  "then": {
    "effect": "deny | audit | append"
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
* `"like": "value"`
* `"match": "value"`
* `"contains": "value"`
* `"in": ["value1","value2"]`
* `"containsKey": "keyName"`
* `"exists": "bool"`

**like** 조건을 사용하는 경우 값에 와일드카드(*)를 제공할 수 있습니다.

**match** 조건을 사용하는 경우 자릿수 하나를 나타내려면 `#`를, 문자 하나를 나타내려면 `?`를, 해당 실제 문자를 나타내려면 다른 모든 문자를 제공합니다. 관련 예제는 [이름 및 텍스트에 대한 리소스 정책 적용](resource-manager-policy-naming-convention.md)을 참조하세요.

### <a name="fields"></a>필드
조건은 필드를 사용하여 구성됩니다. 필드는 리소스의 상태를 설명하는 데 사용되는 리소스 요청 페이로드의 속성을 나타냅니다.  

다음 필드가 지원됩니다.

* `name`
* `kind`
* `type`
* `location`
* `tags`
* `tags.*` 
* 속성 별칭 - 목록은 [별칭](#aliases)을 참조하세요.

### <a name="effect"></a>결과
정책은 `deny`, `audit`, `append`, `AuditIfNotExists` 및 `DeployIfNotExists`라는 5가지 종류의 결과를 지원합니다. 

* **거부**는 감사 로그에 이벤트를 생성하고 요청을 실패합니다.
* **감사**는 감사 로그에 경고 이벤트를 생성하지만 요청을 실패하지는 않습니다.
* **추가**는 정의된 필드 집합을 요청에 추가합니다. 
* **AuditIfNotExists** - 리소스가 없으면 감사를 사용하도록 설정합니다.
* **DeployIfNotExists** - 리소스가 아직 없으면 배포합니다. 현재 이 결과는 기본 제공 정책을 통해서만 지원됩니다.

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

**AuditIfNotExists** 및 **DeployIfNotExists**를 사용하면 자식 리소스의 존재 여부를 평가하고, 해당 리소스가 없는 경우 규칙을 적용할 수 있습니다. 예를 들어 모든 가상 네트워크에 대해 네트워크 감시자를 배포하도록 요구할 수 있습니다.

가상 컴퓨터 확장이 배포되지 않은 경우의 감사 예제는 [VM 확장 감사](https://github.com/Azure/azure-policy-samples/blob/master/samples/Compute/audit-vm-extension/azurepolicy.json)를 참조하세요.

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
| Microsoft.Compute/imageOffer | 플랫폼 이미지 또는 가상 컴퓨터를 만드는 데 사용된 Marketplace 이미지 제안을 설정합니다. |
| Microsoft.Compute/imagePublisher | 플랫폼 이미지 또는 가상 컴퓨터를 만드는 데 사용된 Marketplace 이미지의 게시자를 설정합니다. |
| Microsoft.Compute/imageSku | 플랫폼 이미지 또는 가상 컴퓨터를 만드는 데 사용된 Marketplace 이미지의 SKU를 설정합니다. |
| Microsoft.Compute/imageVersion | 플랫폼 이미지 또는 가상 컴퓨터를 만드는 데 사용된 Marketplace 이미지의 버전을 설정합니다. |


**Microsoft.Compute/virtualMachines**

| Alias | 설명 |
| ----- | ----------- |
| Microsoft.Compute/imageId | 가상 컴퓨터를 만드는 데 사용되는 이미지의 식별자를 설정합니다. |
| Microsoft.Compute/imageOffer | 플랫폼 이미지 또는 가상 컴퓨터를 만드는 데 사용된 Marketplace 이미지 제안을 설정합니다. |
| Microsoft.Compute/imagePublisher | 플랫폼 이미지 또는 가상 컴퓨터를 만드는 데 사용된 Marketplace 이미지의 게시자를 설정합니다. |
| Microsoft.Compute/imageSku | 플랫폼 이미지 또는 가상 컴퓨터를 만드는 데 사용된 Marketplace 이미지의 SKU를 설정합니다. |
| Microsoft.Compute/imageVersion | 플랫폼 이미지 또는 가상 컴퓨터를 만드는 데 사용된 Marketplace 이미지의 버전을 설정합니다. |
| Microsoft.Compute/licenseType | 온-프레미스로 사용이 허가된 이미지 또는 디스크를 설정합니다. 이 값은 Windows Server 운영 체제를 포함하는 이미지에만 사용됩니다.  |
| Microsoft.Compute/virtualMachines/imageOffer | 플랫폼 이미지 또는 가상 컴퓨터를 만드는 데 사용된 Marketplace 이미지 제안을 설정합니다. |
| Microsoft.Compute/virtualMachines/imagePublisher | 플랫폼 이미지 또는 가상 컴퓨터를 만드는 데 사용된 Marketplace 이미지의 게시자를 설정합니다. |
| Microsoft.Compute/virtualMachines/imageSku | 플랫폼 이미지 또는 가상 컴퓨터를 만드는 데 사용된 Marketplace 이미지의 SKU를 설정합니다. |
| Microsoft.Compute/virtualMachines/imageVersion | 플랫폼 이미지 또는 가상 컴퓨터를 만드는 데 사용된 Marketplace 이미지의 버전을 설정합니다. |
| Microsoft.Compute/virtualMachines/osDisk.Uri | Vhd URI를 설정합니다. |
| Microsoft.Compute/virtualMachines/sku.name | 가상 컴퓨터의 크기를 설정합니다. |

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
| Microsoft.Compute/imageOffer | 플랫폼 이미지 또는 가상 컴퓨터를 만드는 데 사용된 Marketplace 이미지 제안을 설정합니다. |
| Microsoft.Compute/imagePublisher | 플랫폼 이미지 또는 가상 컴퓨터를 만드는 데 사용된 Marketplace 이미지의 게시자를 설정합니다. |
| Microsoft.Compute/imageSku | 플랫폼 이미지 또는 가상 컴퓨터를 만드는 데 사용된 Marketplace 이미지의 SKU를 설정합니다. |
| Microsoft.Compute/imageVersion | 플랫폼 이미지 또는 가상 컴퓨터를 만드는 데 사용된 Marketplace 이미지의 버전을 설정합니다. |
| Microsoft.Compute/licenseType | 온-프레미스로 사용이 허가된 이미지 또는 디스크를 설정합니다. 이 값은 Windows Server 운영 체제를 포함하는 이미지에만 사용됩니다. |
| Microsoft.Compute/VirtualMachineScaleSets/computerNamePrefix | 확장 집합에서 모든 가상 컴퓨터에 대해 컴퓨터 이름 접두사를 설정합니다. |
| Microsoft.Compute/VirtualMachineScaleSets/osdisk.imageUrl | 사용자 이미지에 대한 Blob URI를 설정합니다. |
| Microsoft.Compute/VirtualMachineScaleSets/osdisk.vhdContainers | 확장 집합에 대한 운영 체제 디스크를 저장하는 데 사용되는 컨테이너 URL을 설정합니다. |
| Microsoft.Compute/VirtualMachineScaleSets/sku.name | 확장 집합에서 가상 컴퓨터 크기를 설정합니다. |
| Microsoft.Compute/VirtualMachineScaleSets/sku.tier | 확장 집합에서 가상 컴퓨터 계층을 설정합니다. |
  
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

## <a name="policy-sets"></a>정책 집합

정책 집합을 사용하면 관련된 여러 관련 정책 정의를 그룹화할 수 있습니다. 그룹을 단일 항목으로 사용하므로 정책 집합은 할당 및 관리를 간소화합니다. 예를 들어 관련 태그 지정 정책 모두를 단일 정책 집합으로 그룹화할 수 있습니다. 각 정책을 개별적으로 할당하는 대신 정책 집합을 적용합니다.
 
다음 예제에서는 두 태그(costCenter 및 productName)를 처리하기 위한 정책 집합을 만드는 방법을 보여 줍니다. 하나는 기본 태그 값을 적용하고, 다른 하나는 태그 값을 적용하는 두 가지 기본 제공 정책을 사용합니다. 정책 집합은 두 매개 변수, 즉 costCenterValue 및 productNameValue를 다시 사용할 수 있도록 선언합니다. 서로 다른 매개 변수를 사용하여 두 가지 기본 제공 정책 정의를 여러 번 참조합니다. tagName에 대해 표시한 것처럼 각 매개 변수에 대해 고정 값을 제공하거나, tagValue에 대해 표시한 것처럼 정책 집합의 매개 변수를 제공할 수 있습니다.

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

**New-AzureRMPolicySetDefinition** PowerShell 명령을 사용하여 정책 집합을 추가합니다.

REST 작업의 경우 다음 예제와 같이 **2017-06-01-preview** API 버전을 사용합니다.

```
PUT /subscriptions/<subId>/providers/Microsoft.Authorization/policySetDefinitions/billingTagsPolicySet?api-version=2017-06-01-preview
```

## <a name="next-steps"></a>다음 단계
* 정책 규칙을 정의한 후에 범위에 할당합니다. 포털을 통해 정책을 할당하려면 [Azure Portal을 사용하여 리소스 정책 할당 및 관리](resource-manager-policy-portal.md)를 참조하세요. REST API, PowerShell 또는 Azure CLI를 통해 정책을 할당하려면 [스크립트를 통해 정책 할당 및 관리](resource-manager-policy-create-assign.md)를 참조하세요.
* 정책 예제는 [Azure 리소스 정책 GitHub 리포지토리](https://github.com/Azure/azure-policy-samples)를 참조하세요.
* 엔터프라이즈에서 리소스 관리자를 사용하여 구독을 효과적으로 관리할 수 있는 방법에 대한 지침은 [Azure 엔터프라이즈 스캐폴드 - 규범적 구독 거버넌스](resource-manager-subscription-governance.md)를 참조하세요.
* 정책 스키마는 [http://schema.management.azure.com/schemas/2016-12-01/policyDefinition.json](http://schema.management.azure.com/schemas/2016-12-01/policyDefinition.json)에 게시되어 있습니다. 

