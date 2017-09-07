---
title: "태그에 대한 Azure 리소스 정책 | Microsoft Docs"
description: "리소스에서 태그를 관리하기 위한 리소스 정책의 예제 제공"
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/24/2017
ms.author: tomfitz
ms.translationtype: HT
ms.sourcegitcommit: 646886ad82d47162a62835e343fcaa7dadfaa311
ms.openlocfilehash: 469bd8d637337e5900ea84c6bfaf88064695fb7e
ms.contentlocale: ko-kr
ms.lasthandoff: 08/25/2017

---
# <a name="apply-resource-policies-for-tags"></a>태그에 대한 리소스 정책 적용

이 항목에서는 리소스에서 태그의 일관된 사용을 확인하는 데 적용할 수 있는 일반적인 정책 규칙을 제공합니다.

기존 리소스를 사용하는 리소스 그룹 또는 구독에 태그 정책을 적용하면 해당 리소스에 정책을 소급 적용하지 않습니다. 해당 리소스에 대한 정책을 적용하려면 기존 리소스에 대한 업데이트를 트리거합니다. 이 문서는 업데이트를 트리거하기 위해 PowerShell 예제를 포함합니다.

## <a name="ensure-all-resources-in-a-resource-group-have-a-tagvalue"></a>리소스 그룹의 모든 리소스에 태그/값이 있는지 확인

일반적으로 리소스 그룹의 모든 리소스에 특정 태그 및 값이 있어야 합니다. 이 요구 사항은 부서별로 비용을 추적하는 데 종종 필요합니다. 다음 조건이 충족되어야 합니다.

* 필수 태그 및 값은 태그가 없는 신규 및 업데이트된 리소스에 추가됩니다.
* 기존 리소스에서 필수 태그 및 값을 제거할 수 없습니다.

두 가지 기본 제공 정책을 리소스 그룹에 적용하여 이 요구 사항을 달성합니다.

| ID | 설명 |
| ---- | ---- |
| 2a0e14a6-b0a6-4fab-991a-187a4f81c498 | 사용자가 지정하지 않으면 필수 태그와 해당 기본값을 적용합니다. |
| 1e30110a-5ceb-460c-a204-c1c3969c6d62 | 필수 태그와 해당 값을 적용합니다. |

### <a name="powershell"></a>PowerShell

다음 PowerShell 스크립트는 리소스 그룹에 두 가지 기본 제공 정책 정의를 할당합니다. 스크립트를 실행하기 전에 리소스 그룹에 모든 필수 태그를 할당합니다. 리소스 그룹의 각 태그는 그룹에 있는 리소스에 필요합니다. 사용자 구독의 모든 리소스 그룹에 할당하려면 리소스 그룹을 가져올 때 `-Name` 매개 변수를 제공하지 마세요.

```powershell
$appendpolicy = Get-AzureRmPolicyDefinition | Where-Object {$_.Name -eq '2a0e14a6-b0a6-4fab-991a-187a4f81c498'}
$denypolicy = Get-AzureRmPolicyDefinition | Where-Object {$_.Name -eq '1e30110a-5ceb-460c-a204-c1c3969c6d62'}

$rgs = Get-AzureRMResourceGroup -Name ExampleGroup

foreach($rg in $rgs)
{
    $tags = $rg.Tags
    foreach($key in $tags.Keys){
        $key 
        $tags[$key]
        New-AzureRmPolicyAssignment -Name ("append"+$key+"tag") -PolicyDefinition $appendpolicy -Scope $rg.ResourceId -tagName $key -tagValue  $tags[$key]
        New-AzureRmPolicyAssignment -Name ("denywithout"+$key+"tag") -PolicyDefinition $denypolicy -Scope $rg.ResourceId -tagName $key -tagValue  $tags[$key]
    }
}
```

정책을 할당한 후에는 모든 기존 리소스에 업데이트를 트리거하여 추가한 태그 정책을 적용합니다. 다음 스크립트는 리소스에 존재하는 다른 태그를 유지합니다.

```powershell
$group = Get-AzureRmResourceGroup -Name "ExampleGroup" 

$resources = Find-AzureRmResource -ResourceGroupName $group.ResourceGroupName 

foreach($r in $resources)
{
    try{
        $r | Set-AzureRmResource -Tags ($a=if($r.Tags -eq $NULL) { @{}} else {$r.Tags}) -Force -UsePatchSemantics
    }
    catch{
        Write-Host  $r.ResourceId + "can't be updated"
    }
}
```

## <a name="require-tags-for-a-resource-type"></a>리소스 유형에 대한 태그 요구
아래 예제에서는 특정 리소스 유형(이 경우, 저장소 계정)에만 응용 프로그램 태그를 요구하도록 논리 연산자를 중첩하는 방법을 보여 줍니다.

```json
{
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
    "then": {
        "effect": "audit"
    }
}
```

## <a name="require-tag"></a>태그 요구
아래 정책은 "costCenter" 키(모든 값을 적용할 수 있음)를 포함하고 있는 태그가 없는 요청을 거부합니다.

```json
{
  "if": {
    "not" : {
      "field" : "tags",
      "containsKey" : "costCenter"
    }
  },
  "then" : {
    "effect" : "deny"
  }
}
```

## <a name="next-steps"></a>다음 단계
* 앞의 예제와 표시된 바와 같이 정책 규칙을 정의한 후에는 정책 정의를 만들고 범위에 할당해야 합니다. 범위는 구독, 리소스 그룹 또는 리소스일 수 있습니다. 포털을 통해 정책을 할당하려면 [Azure Portal을 사용하여 리소스 정책 할당 및 관리](resource-manager-policy-portal.md)를 참조하세요. REST API, PowerShell 또는 Azure CLI를 통해 정책을 할당하려면 [스크립트를 통해 정책 할당 및 관리](resource-manager-policy-create-assign.md)를 참조하세요.
* 리소스 정책에 대한 소개는 [리소스 정책 개요](resource-manager-policy.md)를 참조하세요.
* 엔터프라이즈에서 리소스 관리자를 사용하여 구독을 효과적으로 관리할 수 있는 방법에 대한 지침은 [Azure 엔터프라이즈 스캐폴드 - 규범적 구독 거버넌스](resource-manager-subscription-governance.md)를 참조하세요.


