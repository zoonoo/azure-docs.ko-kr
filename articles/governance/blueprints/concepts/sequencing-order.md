---
title: Azure Blueprint의 배포 순서 이해
description: 청사진이 거치는 수명 주기 및 각 단계의 세부 정보를 알아봅니다.
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 09/18/2018
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: c09fb26d8375e08281241aaed3f6f6e30acc755b
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46955455"
---
# <a name="understand-the-deployment-sequence-in-azure-blueprints"></a>Azure Blueprint의 배포 순서 이해

Azure Blueprints는 청사진의 할당을 처리할 때 **시퀀싱 순서**를 사용하여 리소스 만들기 순서를 결정합니다. 이 문서에서는 사용되는 기본 시퀀싱 순서, 순서를 사용자 지정하는 방법 및 사용자 지정된 순서대로 처리하는 방법을 설명합니다.

JSON 예제에는 사용자 고유의 값으로 바꿔야 하는 변수가 있습니다.

- `{YourMG}` - 사용자의 관리 그룹 이름으로 대체

## <a name="default-sequencing-order"></a>기본 시퀀싱 순서

청사진에 아티팩트 배포 순서에 대한 지시문이 없거나 지시문이 null이면 다음 순서가 사용됩니다.

- 아티팩트 이름을 기준으로 정렬된 구독 수준 **역할 할당** 아티팩트
- 아티팩트 이름을 기준으로 정렬된 구독 수준 **정책 할당** 아티팩트
- 아티팩트 이름을 기준으로 정렬된 구독 수준 **Azure Resource Manager 템플릿** 아티팩트
- 자리 표시자 이름을 기준으로 정렬된 **리소스 그룹** 아티팩트(자식 아티팩트 포함)

처리되는 각 **리소스 그룹** 아티팩트 내에서, 다음 시퀀스 순서를 사용하여 해당 리소스 그룹 내에 아티팩트를 만듭니다.

- 아티팩트 이름을 기준으로 정렬된 리소스 그룹 자식 **역할 할당** 아티팩트
- 아티팩트 이름을 기준으로 정렬된 리소스 그룹 자식 **정책 할당** 아티팩트
- 아티팩트 이름을 기준으로 정렬된 리소스 그룹 자식 **Azure Resource Manager 템플릿** 아티팩트

## <a name="customizing-the-sequencing-order"></a>시퀀싱 순서 사용자 지정

대형 청사진을 작성할 때 다른 리소스와 관련된 특정 순서에 따라 리소스를 만들어야 할 때가 있습니다. 이 패턴은 청사진에 여러 Azure Resource Manager 템플릿이 들어 있을 때 주로 사용됩니다. Blueprints는 시퀀싱 순서를 정의할 수 있도록 허용하여 이 문제를 처리합니다.

JSON에서 `dependsOn` 속성을 정의하여 처리합니다. 오직 (리소스 그룹에 대한) 청사진 및 아티팩트 개체만 이 속성을 지원합니다. `dependsOn`은 특정 아티팩트가 만들어지기 전에 만들어야 하는 아티팩트 이름의 문자열 배열입니다.

### <a name="example---blueprint-with-ordered-resource-group"></a>예 - 순서가 지정된 리소스 그룹을 사용하는 청사진

다음은 표준 리소스 그룹과 함께 `dependsOn` 값을 선언하여 사용자 지정 시퀀스 순서가 정의된 리소스 그룹을 사용하는 청사진 예제입니다. 이 예에서 **assignPolicyTags**라는 이름의 아티팩트가 **ordered-rg** 리소스 그룹보다 먼저 처리됩니다.
**standard-rg**는 기본 시퀀싱 순서대로 처리됩니다.

```json
{
    "properties": {
        "description": "Example blueprint with custom sequencing order",
        "resourceGroups": {
            "ordered-rg": {
                "dependsOn": [
                    "assignPolicyTags"
                ],
                "metadata": {
                    "description": "Resource Group that waits for 'assignPolicyTags' creation"
                }
            },
            "standard-rg": {
                "metadata": {
                    "description": "Resource Group that follows the standard sequence ordering"
                }
            }
        },
        "targetScope": "subscription"
    },
    "id": "/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/mySequencedBlueprint",
    "type": "Microsoft.Blueprint/blueprints",
    "name": "mySequencedBlueprint"
}
```

### <a name="example---artifact-with-custom-order"></a>예 - 사용자 지정 순서를 사용하는 아티팩트

다음은 Azure Resource Manager 템플릿을 사용하는 예제 정책 아티팩트입니다. 기본 순서를 사용하여 정책 아티팩트를 만든 후 Azure Resource Manager 템플릿을 만듭니다. 이렇게 하면 Azure Resource Manager 템플릿이 만들어질 때까지 정책 아티팩트가 대기할 수 있습니다.

```json
{
    "properties": {
        "displayName": "Assigns an identifying tag",
        "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/2a0e14a6-b0a6-4fab-991a-187a4f81c498",
        "resourceGroup": "standard-rg",
        "dependsOn": [
            "customTemplate"
        ]
    },
    "kind": "policyAssignment",
    "id": "/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/mySequencedBlueprint/artifacts/assignPolicyTags",
    "type": "Microsoft.Blueprint/artifacts",
    "name": "assignPolicyTags"
}
```

## <a name="processing-the-customized-sequence"></a>사용자 지정된 시퀀스 처리

만들기 프로세스가 진행되는 동안 토폴로지 정렬을 사용하여 청사진 및 해당 아티팩트의 종속성 그래프가 만들어집니다. 이렇게 하면 리소스 그룹과 아티팩트 간의 여러 종속성 수준을 지원할 수 있습니다.

기본 순서를 변경하지 않는 청사진 또는 아티팩트에서 종속성이 선언되면 시퀀싱 순서가 변경되지 않습니다. 구독 수준 정책을 사용하는 리소스 그룹 또는 리소스 그룹 'standard-rg' 자식 역할 할당을 사용하는 리소스 그룹 'standard-rg' 자식 정책 할당을 예로 들 수 있습니다. 두 경우 모두 `dependsOn`이 기본값을 변경하지 않으며 아무 것도 변경되지 않습니다.

## <a name="next-steps"></a>다음 단계

- [청사진 수명 주기](lifecycle.md)에 대해 알아보기
- [정적 및 동적 매개 변수](parameters.md) 사용 방법 이해
- [청사진 리소스 잠금](resource-locking.md)을 사용하는 방법 확인
- [기존 할당을 업데이트](../how-to/update-existing-assignments.md)하는 방법 알아보기
- [일반 문제 해결 방법](../troubleshoot/general.md)을 통해 청사진 할당 중에 발생하는 문제 해결