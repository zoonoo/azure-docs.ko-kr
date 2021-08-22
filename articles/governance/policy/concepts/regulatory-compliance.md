---
title: 이니셔티브 정의 규정 준수
description: 이니셔티브 정의를 통해 정책을 규정 도메인(Access Control, 구성 관리 등)별로 그룹화하는 방법을 설명합니다.
ms.date: 08/17/2021
ms.topic: conceptual
ms.openlocfilehash: a9b32f22136b91bc57aee33a5cdfc99f77212436
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/17/2021
ms.locfileid: "122530646"
---
# <a name="regulatory-compliance-in-azure-policy"></a>Azure Policy 규정 준수

Azure Policy 규정 준수는 기본 제공 이니셔티브 정의를 제공하여 책임(_고객_, _Microsoft_, _공유_)에 따라 **제어** 및 **규정 준수 도메인** 목록을 볼 수 있습니다.
Microsoft에서 담당하는 시각적 개체를 위해 타사 증명을 기반으로 하는 감사 결과와 해당 규정 준수를 실현하는 구현 세부 정보를 추가로 제공합니다.
Microsoft에서 담당하는 시각적 개체는 `type` [정적](./definition-structure.md#type)입니다.

> [!NOTE]
> 규정 준수는 미리 보기 기능입니다. 업데이트된 기본 기능의 경우 이니셔티브 컨트롤은 해당하는 규정 준수 표준에 매핑됩니다. 기존 규정 준수 표준 이니셔티브는 규정 준수를 지원하도록 업데이트되고 있습니다.

## <a name="regulatory-compliance-defined"></a>정의된 규정 준수

규정 준수는 이니셔티브 정의의 [그룹화](./initiative-definition-structure.md#policy-definition-groups) 부분을 기반으로 합니다. 기본 제공에서 이니셔티브 정의의 각 그룹화는 이름(**시각적 개체**), 범주(**규정 준수 도메인**)를 정의하고 해당 **시각적 개체** 에 관한 정보를 포함하는 [policyMetadata](./initiative-definition-structure.md#metadata-objects) 개체에 관한 참조를 제공합니다. 규정 준수 이니셔티브 정의에는 **규정 준수** 로 설정된 `category` 속성이 있어야 합니다. 다른 방식의 표준 이니셔티브 정의로서 규정 준수 이니셔티브는 동적 할당을 만들기 위한 [매개 변수](./initiative-definition-structure.md#parameters)를 지원합니다.

고객은 자신의 규정 준수 이니셔티브를 만들 수 있습니다. 해당 정의는 기존 기본 제공 정의에서 원본이거나 또는 복사될 수 있습니다. 기본 제공 규정 준수 이니셔티브 정의를 참조로 사용하는 경우 [Azure Policy GitHub 리포지토리](https://github.com/Azure/azure-policy/tree/master/built-in-policies/policySetDefinitions/Regulatory%20Compliance)에서 규정 준수 정의의 원본을 모니터링하는 것이 좋습니다.

사용자 지정 규정 준수 이니셔티브를 Azure Security Center 대시보드에 연결하려면 [Azure Security Center - 사용자 지정 보안 정책 사용](../../../security-center/custom-security-policies.md)을 참조하세요.

## <a name="regulatory-compliance-in-portal"></a>포털 규정 준수

[그룹](./initiative-definition-structure.md#policy-definition-groups)을 사용하여 이니셔티브 정의를 만든 경우 포털에서 해당 이니셔티브를 위한 **규정 준수** 세부 정보 페이지에는 추가 정보가 포함됩니다.

새로운 **시각적 개체** 탭이 해당 페이지에 추가되었습니다. 필터링은 **규정 준수 도메인** 에서 사용할 수 있으며 정책 정의는 **policyMetadata** 개체의 `title` 필드를 기준으로 그룹화됩니다. 각 행은 시각적 개체의 준수 상태, 해당 **규정 준수 도메인**, 책임 정보, 해당 **시각적 개체** 를 구성하는 비준수 및 준수 정책 정의의 수를 표시하는 **시각적 개체** 를 나타냅니다.

:::image type="content" source="../media/regulatory-compliance/regulatory-compliance-overview.png" alt-text="준수 및 비준수 시각적 개체를 보여 주는 NIST SP 800-53 R4. 기본 제공 정의를 위한 규정 준수 개요의 스크린샷.":::

**시각적 개체** 를 선택하면 해당 시각적 개체에 관한 세부 정보 페이지가 열립니다. **개요** 에는 `description` 및 `requirements`의 정보가 포함되어 있습니다. **정책** 탭에는 해당 **시각적 개체** 에 기여하는 이니셔티브의 개별 정책 정의가 모두 있습니다. **리소스 규정 준수** 탭은 현재 표시된 **시각적 개체** 의 멤버 정책에 의해 평가되는 각 리소스의 세부적인 뷰를 제공합니다.

> [!NOTE]
> **Microsoft에서 관리형** 평가 형식은 [정적](./definition-structure.md#type) 정책 정의 `type`을 위한 것입니다.

:::image type="content" source="../media/regulatory-compliance/regulatory-compliance-policies.png" alt-text="NIST SP 800-53 R4, 기본 제공 정의의 경계 보호 제어를 위한 규정 준수 정보의 스크린샷.":::

동일한 **시각적 개체** 페이지에서 **리소스 규정 준수** 탭으로 변경하면 해당 **시각적 개체** 의 정책 정의에 포함된 모든 리소스가 표시됩니다. 이름 또는 ID, 준수 상태, 리소스 종류, 위치 필터를 사용할 수 있습니다.

:::image type="content" source="../media/regulatory-compliance/regulatory-compliance-resources.png" alt-text="NIST SP 800-53 R4, 기본 제공 정의의 경계 보호 제어를 위한 리소스 규정 준수의 스크린샷.":::

## <a name="regulatory-compliance-in-sdk"></a>SDK 규정 준수

이니셔티브 정의에 규정 준수를 사용하는 경우 평가 검사 레코드, 이벤트, 정책 상태 SDK는 각각 추가 속성을 반환합니다. 추가 속성은 준수 상태별로 그룹화되며, 각 상태에 있는 그룹이 몇 개인지에 관한 정보를 제공합니다.

다음 코드는 `summarize`호출에서 추가된 결과의 예입니다.

```json
"policyGroupDetails": [{
        "complianceState": "noncompliant",
        "count": 4
    },
    {
        "complianceState": "compliant",
        "count": 76
    }
]
```

## <a name="next-steps"></a>다음 단계

- [이니셔티브 정의 구조](./initiative-definition-structure.md)를 참조합니다.
- [Azure Policy 샘플](../samples/index.md)에서 예제를 검토합니다.
- [정책 효과 이해](./effects.md)를 검토합니다.
- [규정 비준수 리소스를 수정](../how-to/remediate-resources.md)하는 방법을 알아봅니다.
