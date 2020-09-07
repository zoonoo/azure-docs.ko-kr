---
title: Azure Attestation 클레임 규칙 문법
description: Azure Attestation 정책 클레임 및 클레임 규칙에 대한 세부 정보입니다.
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.openlocfilehash: a0f3e517e60037de6456bc3a549e072e58e2fa67
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89244741"
---
# <a name="claim-and-claim-rules"></a>클레임 및 클레임 규칙

클레임 규칙의 문법을 이해하려면 먼저 증명 정책 클레임을 이해해야 합니다.

## <a name="claim"></a>클레임

클레임은 관련 정보를 제공하기 위해 모두 그룹화된 속성 세트입니다. Azure Attestation의 경우 클레임에는 다음 속성이 포함됩니다.

- **type**: 클레임 유형을 나타내는 문자열 값입니다.
- **value**: 클레임 값을 나타내는 부울, 정수 또는 문자열 값입니다.
- **valueType**: value 속성에 저장된 정보의 데이터 형식입니다. 지원되는 형식은 String, Integer 및 Boolean입니다. 정의되지 않은 경우 기본값은 "String"입니다.
- **issuer**: 클레임 발급자에 대한 정보입니다. 발급자는 다음 유형 중 하나입니다.
  - **AttestationService**: Azure Attestation에서 정책 작성자에게 특정 클레임을 제공하며, 증명 정책 작성자는 이 클레임을 사용하여 적절한 정책을 작성할 수 있습니다.
  - **AttestationPolicy**: 관리자가 정의한 정책 자체는 처리 중에 클레임을 들어오는 증거에 추가할 수 있습니다. 이 경우 발급자는 "AttestationPolicy"로 설정됩니다.
  - **CustomClaim**: 증명자(클라이언트)는 추가 클레임을 증명 증거에 추가할 수도 있습니다. 이 경우 발급자는 "CustomClaim"으로 설정됩니다.

정의되지 않은 경우 기본값은 "CustomClaim"입니다.

## <a name="claim-rule"></a>클레임 규칙

들어오는 클레임 집합은 정책 엔진에서 증명 결과를 계산하는 데 사용됩니다. 클레임 규칙은 들어오는 클레임의 유효성을 검사하고 정의된 작업을 수행하는 데 사용되는 조건 세트입니다.

```
Conditions list => Action (Claim)
```

클레임 규칙의 Azure Attestation 평가에 포함되는 단계는 다음과 같습니다.

- 조건 목록이 없으면 지정된 클레임을 사용하여 작업을 실행합니다. 
- 그렇지 않으면 조건 목록에서 조건을 평가합니다.
- 조건 목록이 false로 평가되면 중지합니다. 그렇지 않으면 계속 진행합니다.

클레임 규칙의 조건은 작업을 실행해야 하는지 여부를 결정하는 데 사용됩니다. 조건 목록은 "&&" 연산자로 구분된 일련의 조건입니다.

조건 목록은 다음과 같이 구성됩니다.

```
Condition && Condition && ...
```

조건은 다음과 같이 구성됩니다.

```
Identifier:[ClaimPropertyCondition, ClaimPropertyCondition,…]
```

조건은 클레임의 다양한 속성에 대한 개별 조건으로 구성됩니다. 조건에는 조건을 충족하는 클레임을 참조하는 데 사용할 수 있는 선택적 식별자가 있을 수 있습니다. 이 참조는 다른 조건 또는 동일한 규칙의 작업에서 사용할 수 있습니다.

예를 들면 다음과 같습니다.

```
F1:[type=="OSName" , issuer=="CustomClaim"] && 
[type=="OSName" , issuer=="AttestationService", value== F1.value ] 
=> issueproperty(type="report_validity_in_minutes", value=1440);

F1:[type=="OSName" , issuer=="CustomClaim"] && 
C2:[type=="OSName" , issuer=="AttestationService", value== F1.value ] 
=> issue(claim = C2);
```

조건을 확인하는 데 사용할 수 있는 연산자는 다음과 같습니다.

| Valuetype | 지원되는 작업 |
|--|--|
| 정수 | ==(같음), \!=(같지 않음), <=(보다 작거나 같음), <(보다 작음), >=(보다 크거나 같음), >(보다 큼) |
| String | ==(같음), \!=(같지 않음) |
| 부울 | ==(같음), \!=(같지 않음) |

조건 목록 평가:

- "&&" 연산자가 있으면 목록의 모든 조건이 true로 평가되는 경우에만 조건 목록이 true로 평가됩니다.
- 조건은 클레임 세트에 대한 필터링 조건을 나타냅니다. 조건을 충족하는 클레임이 하나 이상 있으면 조건 자체는 true로 평가됩니다.
- 각 속성이 조건에 있는 해당 클레임 속성 조건을 충족하면 클레임은 조건에서 나타내는 필터링 조건을 충족하는 것으로 평가됩니다.  

정책에서 허용되는 작업 세트는 아래에서 설명하고 있습니다.

| 작업 동사 | Description | 이러한 항목이 적용되는 정책 섹션 |
|--|--|--|
| permit() | 들어오는 클레임 집합을 사용하여 **issuancerules**를 계산할 수 있습니다. claim을 매개 변수로 사용하지 않습니다. | **authorizationrules** |
| deny() | 들어오는 클레임 집합을 **issuancerules**를 계산하는 데 사용하면 안 됩니다. claim을 매개 변수로 사용하지 않습니다. | **authorizationrules** |
| add(claim) | claim을 들어오는 클레임 집합에 추가합니다. 들어오는 클레임 집합에 추가된 모든 클레임은 후속 클레임 규칙에 사용할 수 있습니다. |**authorizationrules**, **issuancerules** |
| issue(claim) | claim을 들어오는 클레임 집합 및 클레임 집합에 추가합니다. | **issuancerules** |
| issueproperty(claim) | claim을 들어오는 클레임 집합 및 속성 클레임 집합에 추가합니다. | **issuancerules**

## <a name="next-steps"></a>다음 단계

- [증명 정책을 작성하고 서명하는 방법](author-sign-policy.md)
- [PowerShell을 사용하여 Azure Attestation 설정](quickstart-powershell.md)

