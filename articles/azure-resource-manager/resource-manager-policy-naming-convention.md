---
title: "명명 규칙에 대한 Azure 리소스 정책 | Microsoft Docs"
description: "리소스 명명 규칙에 대한 Azure Resource Manager 정책을 설명합니다."
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
ms.date: 06/27/2017
ms.author: tomfitz
ms.openlocfilehash: 51b3519bbba8cb4c768bfdd7dadf92fced434f22
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/11/2017
---
# <a name="apply-resource-policies-for-names-and-text"></a>이름 및 텍스트에 대한 리소스 정책 적용
이 항목에서는 명명 및 텍스트 규칙 설정에 적용할 수 있는 몇 가지 [리소스 정책](resource-manager-policy.md)을 보여 줍니다. 이러한 정책은 리소스 이름 및 태그 값에 대해 일관성을 보장합니다. 

## <a name="set-naming-convention-with-wildcard"></a>와일드 카드로 명명 규칙 설정
아래 예제에서는 **like** 조건으로 지원되는 와일드 카드의 사용을 보여 줍니다. 이 조건은 앞에서 언급된 패턴(namePrefix\*nameSuffix)과 일치하는 이름이면 요청을 거부한다는 것을 나타냅니다.

```json
{
  "if": {
    "not": {
      "field": "name",
      "like": "namePrefix*nameSuffix"
    }
  },
  "then": {
    "effect": "deny"
  }
}
```

## <a name="set-naming-convention-with-pattern"></a>패턴으로 명명 규칙 설정

리소스 이름이 패턴과 일치하도록 지정하려면 match 조건을 사용합니다. 다음 예제에서는 `contoso`로 시작하고 여섯 개의 추가 문자가 포함된 이름이 필요합니다.

```json
{
  "if": {
    "not": {
      "field": "name",
      "match": "contoso??????"
    }
  },
  "then": {
    "effect": "deny"
  }
}
```

## <a name="set-date-pattern-for-tag-value"></a>태그 값에 대한 날짜 패턴 설정

2자리 숫자, 대시, 3개 문자, 대시 및 4자리 숫자로 구성되는 날짜 패턴이 필요하면 다음을 사용합니다.

```json
{
  "if": {
    "field": "tags.date",
    "match": "##-???-####"
  },
  "then": {
    "effect": "deny"
  }
}
```

## <a name="next-steps"></a>다음 단계
* 앞의 예제와 표시된 바와 같이 정책 규칙을 정의한 후에는 정책 정의를 만들고 범위에 할당해야 합니다. 범위는 구독, 리소스 그룹 또는 리소스일 수 있습니다. 포털을 통해 정책을 할당하려면 [Azure Portal을 사용하여 리소스 정책 할당 및 관리](resource-manager-policy-portal.md)를 참조하세요. REST API, PowerShell 또는 Azure CLI를 통해 정책을 할당하려면 [스크립트를 통해 정책 할당 및 관리](resource-manager-policy-create-assign.md)를 참조하세요. 
* 엔터프라이즈에서 리소스 관리자를 사용하여 구독을 효과적으로 관리할 수 있는 방법에 대한 지침은 [Azure 엔터프라이즈 스캐폴드 - 규범적 구독 거버넌스](resource-manager-subscription-governance.md)를 참조하세요.

