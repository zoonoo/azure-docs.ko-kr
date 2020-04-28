---
title: Azure Security Center |에서 사용자 지정 보안 정책 만들기 Microsoft Docs
description: Azure Security Center에서 모니터링 하는 Azure 사용자 지정 정책 정의입니다.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 04/22/2020
ms.author: memildin
ms.openlocfilehash: d703ea38c39ed556102271ac0cf9a609ce449bc3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82195921"
---
# <a name="using-custom-security-policies"></a>사용자 지정 보안 정책 사용

시스템 및 환경을 보호 하기 위해 Azure Security Center는 보안 권장 사항을 생성 합니다. 이러한 권장 사항은 모든 고객에 게 제공 되는 일반 기본 보안 정책에 통합 되는 업계 모범 사례를 기반으로 합니다. 또한 업계 및 규제 표준에 대 한 Security Center 지식을 얻을 수 있습니다.

이 기능을 사용 하 여 *사용자 지정* 이니셔티브를 추가할 수 있습니다. 그러면 환경이 만든 정책을 따르지 않는 경우 권장 사항을 받게 됩니다. 사용자가 만드는 모든 사용자 지정 이니셔티브는 규정 [준수](security-center-compliance-dashboard.md)에 설명 된 대로 규정 준수 대시보드의 기본 제공 이니셔티브와 함께 표시 됩니다.

[Azure Policy 설명서](https://docs.microsoft.com/azure/governance/policy/concepts/definition-structure#definition-location)에 설명 된 대로 사용자 지정 이니셔티브의 위치를 지정 하는 경우 관리 그룹 또는 구독 이어야 합니다. 

## <a name="to-add-a-custom-initiative-to-your-subscription"></a>구독에 사용자 지정 이니셔티브를 추가 하려면 

1. Security Center의 사이드바에서 **보안 정책** 페이지를 엽니다.

1. 사용자 지정 이니셔티브를 추가할 구독 또는 관리 그룹을 선택 합니다.

    [![사용자 지정 정책을 만들 구독 선택](media/custom-security-policies/custom-policy-selecting-a-subscription.png)](media/custom-security-policies/custom-policy-selecting-a-subscription.png#lightbox)

    > [!NOTE]
    > 평가 되 고 Security Center 표시 되려면 구독 수준 (이상)에서 사용자 지정 표준을 추가 해야 합니다. 
    >
    > 사용자 지정 표준을 추가 하는 경우 해당 범위에 *이니셔티브* 를 할당 합니다. 따라서 해당 할당에 필요한 가장 넓은 범위를 선택 하는 것이 좋습니다.

1. 보안 정책 페이지의 사용자 지정 이니셔티브 아래에서 **사용자 지정 이니셔티브 추가**를 클릭 합니다.

    [![* * 사용자 지정 이니셔티브 추가 * *를 클릭 합니다.](media/custom-security-policies/custom-policy-add-initiative.png)](media/custom-security-policies/custom-policy-add-initiative.png#lightbox)

    표시 되는 페이지는 다음과 같습니다.

    ![정책 만들기 또는 추가](media/custom-security-policies/create-or-add-custom-policy.png)

1. 사용자 지정 이니셔티브 추가 페이지에서 조직에 이미 생성 된 사용자 지정 정책 목록을 검토 합니다. 구독에 할당 하려는 항목을 확인 하는 경우 **추가**를 클릭 합니다. 목록에 요구 사항을 충족 하는 이니셔티브가 없는 경우이 단계를 건너뜁니다.

1. 새 사용자 지정 이니셔티브를 만들려면 다음을 수행 합니다.

    1. **새로 만들기**를 클릭 합니다.
    1. 정의의 위치와 이름을 입력 합니다.
    1. 포함할 정책을 선택 하 고 **추가**를 클릭 합니다.
    1. 원하는 매개 변수를 입력 합니다.
    1. **저장**을 클릭합니다.
    1. 사용자 지정 이니셔티브 추가 페이지에서 새로 고침을 클릭 합니다. 새 이니셔티브를 사용 가능한 것으로 표시 합니다.
    1. **추가** 를 클릭 하 고 구독에 할당 합니다.

    > [!NOTE]
    > 새 이니셔티브를 만들려면 구독 소유자 자격 증명이 필요 합니다. Azure 역할에 대 한 자세한 내용은 [Azure Security Center의 사용 권한](security-center-permissions.md)을 참조 하세요.

    새 이니셔티브는 적용 되며 다음과 같은 두 가지 방법으로 영향을 볼 수 있습니다.

    * Security Center 사이드바의 정책 & 준수에서 **규제 준수**를 선택 합니다. 기본 제공 이니셔티브와 함께 새로운 사용자 지정 이니셔티브를 표시 하는 준수 대시보드가 열립니다.
    
    * 사용자 환경이 정의한 정책을 따르지 않는 경우 권장 사항을 받기 시작 합니다.

1. 정책에 대 한 결과 권장 사항을 보려면 사이드바에서 **권장** 사항을 클릭 하 여 권장 사항 페이지를 엽니다. 권장 사항은 "사용자 지정" 레이블을 사용 하 여 표시 되 고 약 1 시간 이내에 사용할 수 있습니다.

    [![사용자 지정 권장 사항](media/custom-security-policies/custom-policy-recommendations.png)](media/custom-security-policies/custom-policy-recommendations-in-context.png#lightbox)

## <a name="enhancing-your-custom-recommendations-with-detailed-information"></a>세부 정보를 사용 하 여 사용자 지정 권장 사항 향상

Azure Security Center에서 제공 하는 기본 제공 권장 사항에는 심각도 수준 및 수정 지침과 같은 세부 정보가 포함 됩니다. 이 정보 유형을 사용자 지정 권장 사항에 추가 하 여 Azure Portal 또는 권장 사항에 액세스 하는 모든 위치에 표시 되도록 하려면 REST API를 사용 해야 합니다. 

추가할 수 있는 두 가지 유형의 정보는 다음과 같습니다.

- **RemediationDescription** – 문자열
- **심각도** – 열거형 [낮음, 중간, 높음]

사용자 지정 이니셔티브의 일부인 정책에 대 한 정책 정의에 메타 데이터를 추가 해야 합니다. 다음과 같이 ' securityCenter ' 속성에 있어야 합니다.

```json
 "metadata": {
    "securityCenter": {
        "RemediationDescription": "Custom description goes here",
        "Severity": "High",
    },
```

다음은 메타 데이터/보안 센터 속성을 포함 하는 사용자 지정 정책의 예입니다.

  ```json
  {
"properties": {
    "displayName": "Security - ERvNet - AuditRGLock",
    "policyType": "Custom",
    "mode": "All",
    "description": "Audit required resource groups lock",
    "metadata": {
        "securityCenter": {
            "remediationDescription": "Resource Group locks can be set via Azure Portal -> Resource Group -> Locks",
            "severity": "High",
        },
    },
    "parameters": {
        "expressRouteLockLevel": {
            "type": "String",
            "metadata": {
                "displayName": "Lock level",
                "description": "Required lock level for ExpressRoute resource groups."
            },
            "allowedValues": [
                "CanNotDelete",
                "ReadOnly"
            ]
        }
    },
    "policyRule": {
        "if": {
            "field": "type",
            "equals": "Microsoft.Resources/subscriptions/resourceGroups"
        },
        "then": {
            "effect": "auditIfNotExists",
            "details": {
                "type": "Microsoft.Authorization/locks",
                "existenceCondition": {
                    "field": "Microsoft.Authorization/locks/level",
                    "equals": "[parameters('expressRouteLockLevel')]"
                }
            }
        }
    }
}
}
  ```

SecurityCenter 속성 사용에 대 한 다른 예 [는 REST API 설명서의이 섹션](https://docs.microsoft.com/rest/api/securitycenter/assessmentsmetadata/createinsubscription#examples)을 참조 하세요.


## <a name="next-steps"></a>다음 단계

이 문서에서는 사용자 지정 보안 정책을 만드는 방법을 배웠습니다. 

기타 관련 자료는 다음 문서를 참조 하세요. 

- [보안 정책 개요](tutorial-security-policy.md)
- [기본 제공 보안 정책 목록](security-center-policy-definitions.md)