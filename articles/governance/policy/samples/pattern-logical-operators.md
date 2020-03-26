---
title: 패턴 정책 정의의 논리 연산자
description: 이 Azure Policy 패턴에서는 정책 정의에서 논리 연산자를 사용하는 방법에 대한 예를 제공합니다.
ms.date: 01/31/2020
ms.topic: sample
ms.openlocfilehash: 8e57efaea81848c6b2d0188dbf3f91e06ed74c67
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/24/2020
ms.locfileid: "77170241"
---
# <a name="azure-policy-pattern-logical-operators"></a>Azure Policy 패턴: 논리 연산자

정책 정의는 여러 조건문을 포함할 수 있습니다. 각 명령문이 true이어야 하거나 그 중 일부만 true이어야 합니다. 이러한 요구를 지원하기 위해 언어에는 **not**, **allOf** 및 **anyOf**에 대한 [논리 연산자](../concepts/definition-structure.md#logical-operators)가 있습니다. 이러한 항목은 선택 사항이며 복잡한 시나리오를 만들기 위해 중첩될 수 있습니다.

## <a name="sample-1-one-logical-operator"></a>샘플 1: 하나의 논리 연산자

이 정책 정의는 CosmosDB 계정을 평가하여 자동 장애 조치(failover) 및 여러 쓰기 위치가 구성되어 있는지 확인합니다. 그렇지 않은 경우 [감사](../concepts/effects.md#audit)는 비규격 리소스를 만들거나 업데이트할 때 트리거하고 로그 항목을 만듭니다.

:::code language="json" source="~/policy-templates/patterns/pattern-logical-operators-1.json":::

### <a name="sample-1-explanation"></a>샘플 1: 설명

:::code language="json" source="~/policy-templates/patterns/pattern-logical-operators-1.json" range="6-22" highlight="3":::

**policyRule.if** 블록은 단일 **allOf**를 사용하여 세 가지 조건이 모두 true가 되도록 합니다.
이러한 조건이 모두 true로 평가되는 경우에만 **감사** 효과가 트리거됩니다.

## <a name="sample-2-multiple-logical-operators"></a>샘플 2: 여러 논리 연산자

이 정책 정의는 이름 지정 패턴에 대한 리소스를 평가합니다. 리소스가 일치하지 않으면 [거부](../concepts/effects.md#deny)가 됩니다.

:::code language="json" source="~/policy-templates/patterns/pattern-logical-operators-2.json":::

### <a name="sample-2-explanation"></a>샘플 2: 설명

:::code language="json" source="~/policy-templates/patterns/pattern-logical-operators-2.json" range="7-21" highlight="2,3,9":::

이 **policyRule.if** 블록에는 단일 **allOf**도 포함되지만 각 조건은 **not** 논리 연산자로 래핑됩니다. **not** 논리 연산자 내부의 조건을 먼저 평가한 다음, **not**을 평가하여 전체 절이 true인지 false인지 확인합니다. **not** 논리 연산자가 모두 true로 평가되면 정책 효과가 트리거됩니다.

## <a name="next-steps"></a>다음 단계

- 다른 [패턴 및 기본 제공 정의](./index.md)를 검토합니다.
- [Azure Policy 정의 구조](../concepts/definition-structure.md)를 검토합니다.
- [정책 효과 이해](../concepts/effects.md)를 검토합니다.