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
ms.date: 02/09/2017
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: 72d398c529fc7dd5eef450da0e134dcdab534ac5
ms.openlocfilehash: 375a8df763eb6b4b8f7349e0061ab39c076ebfc6


---
# <a name="apply-resource-policies-for-tags"></a>태그에 대한 리소스 정책 적용

이 항목에서는 리소스에서 태그의 일관된 사용을 확인하는 데 적용할 수 있는 일반적인 정책 규칙을 제공합니다.

기존 리소스를 사용하는 리소스 그룹 또는 구독에 태그 정책을 적용하면 해당 리소스에 정책을 소급 적용하지 않습니다. [기존 리소스에 대한 업데이트 트리거](#trigger-updates-to-existing-resources)에 표시된 대로 해당 리소스에 대한 정책을 적용하려면 기존 리소스에 대한 업데이트를 트리거합니다.

## <a name="ensure-all-resources-in-a-resource-group-have-a-tagvalue"></a>리소스 그룹의 모든 리소스에 태그/값이 있는지 확인

일반적으로 리소스 그룹의 모든 리소스에 특정 태그 및 값이 있어야 합니다. 이 요구 사항은 부서별로 비용을 추적하는 데 종종 필요합니다. 다음 조건이 충족되어야 합니다.

* 필수 태그 및 값은 모든 기존 태그가 없는 신규 및 업데이트된 리소스에 추가됩니다.
* 필수 태그 및 값은 필수 태그 및 값이 아닌 다른 태그가 없는 신규 및 업데이트된 리소스에 추가됩니다.
* 기존 리소스에서 필수 태그 및 값을 제거할 수 없습니다.

다음 세 가지 정책을 리소스 그룹에 적용하여 이 요구 사항을 달성할 수 있습니다.

* [태그 추가](#append-tag) 
* [다른 태그를 사용하여 태그 추가](#append-tag-with-other-tags)
* [태그 및 값 요구](#require-tag-and-value)

### <a name="append-tag"></a>태그 추가

태그가 없는 경우 다음 정책 규칙은 미리 정의된 값을 포함한 costCenter 태그를 추가합니다.

```json
{
  "if": {
    "field": "tags",
    "exists": "false"
  },
  "then": {
    "effect": "append",
    "details": [
      {
        "field": "tags",
        "value": {"costCenter":"myDepartment" }
      }
    ]
  }
}
```

### <a name="append-tag-with-other-tags"></a>다른 태그를 사용하여 태그 추가

다음 정책 규칙은 costCenter 태그를 정의하지 않지만 태그가 있는 경우 미리 정의된 값을 포함한 costCenter 태그를 추가합니다.

```json
{
  "if": {
    "allOf": [
      {
        "field": "tags",
        "exists": "true"
      },
      {
        "field": "tags.costCenter",
        "exists": "false"
      }
    ]
  },
  "then": {
    "effect": "append",
    "details": [
      {
        "field": "tags.costCenter",
        "value": "myDepartment"
      }
    ]
  }
}
```

### <a name="require-tag-and-value"></a>태그 및 값 요구

다음 정책 규칙은 미리 정의된 값에 costCenter 태그를 할당하지 않은 리소스의 업데이트 및 생성을 거부합니다.

```json
{
  "if": {
    "not": {
      "field": "tags.costCenter",
      "equals": "myDepartment"
    }
  },
  "then": {
    "effect": "deny"
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

## <a name="trigger-updates-to-existing-resources"></a>기존 리소스에 대한 업데이트 트리거

다음 PowerShell 스크립트는 기존 리소스에 대한 업데이트를 트리거하여 추가한 태그 정책을 적용합니다.

```powershell
$group = Get-AzureRmResourceGroup -Name "ExampleGroup" 

$resources = Find-AzureRmResource -ResourceGroupName $group.ResourceGroupName 

foreach($r in $resources)
{
    try{
        $r | Set-AzureRmResource -Tags ($a=if($_.Tags -eq $NULL) { @{}} else {$_.Tags}) -Force -UsePatchSemantics
    }
    catch{
        Write-Host  $r.ResourceId + "can't be updated"
    }
}
```

## <a name="next-steps"></a>다음 단계
* 앞의 예제와 표시된 바와 같이 정책 규칙을 정의한 후에는 정책 정의를 만들고 범위에 할당해야 합니다. 범위는 구독, 리소스 그룹 또는 리소스일 수 있습니다. 정책을 만들고 할당하는 예제는 [정책 할당 및 관리](resource-manager-policy-create-assign.md)를 참조하세요. 
* 리소스 정책에 대한 소개는 [리소스 정책 개요](resource-manager-policy.md)를 참조하세요.
* 엔터프라이즈에서 리소스 관리자를 사용하여 구독을 효과적으로 관리할 수 있는 방법에 대한 지침은 [Azure 엔터프라이즈 스캐폴드 - 규범적 구독 거버넌스](resource-manager-subscription-governance.md)를 참조하세요.




<!--HONumber=Feb17_HO3-->


