---
title: Azure Policy 범위 이해
description: Azure Resource Manager 범위 개념과 Azure Policy에서 평가할 리소스를 제어 하는 Azure Policy에 적용 되는 방법에 대해 설명 합니다.
ms.date: 09/22/2020
ms.topic: conceptual
ms.openlocfilehash: 83eda330a35b007abfa37046a202b14728726849
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "90984429"
---
# <a name="understand-scope-in-azure-policy"></a>Azure Policy 범위 이해

평가할 수 있는 리소스와 Azure Policy 의해 평가 되는 리소스를 결정 하는 다양 한 설정이 있습니다. 이러한 컨트롤에 대 한 기본 개념은 _범위_입니다.
개략적인 개요는 [Azure Resource Manager 범위](../../../azure-resource-manager/management/overview.md#understand-scope)를 참조 하세요.
이 문서에서는 Azure Policy _범위_ 에 미치는 영향에 대해 설명 하 고 관련 개체 및 속성을 설명 합니다.

## <a name="definition-location"></a>정의 위치

Azure Policy에서 사용 하는 첫 번째 인스턴스 범위는 정책 정의를 만들 때입니다. 정의는 관리 그룹 또는 구독 중 하나에 저장 될 수 있습니다. 위치는 이니셔티브 또는 정책을 할당할 수 있는 범위를 결정 합니다. 리소스는 할당 대상으로 지정할 정의 위치의 리소스 계층 구조 내에 있어야 합니다.

정의 위치는 다음과 같습니다.

- **구독** -해당 구독 내의 리소스에는 정책 정의를 할당할 수 있습니다.
- **관리 그룹** -자식 관리 그룹 및 자식 구독 내의 리소스만 정책 정의에 할당할 수 있습니다. 여러 구독에 정책 정의를 적용 하려는 경우 해당 위치는 각 구독이 포함 된 관리 그룹 이어야 합니다.

위치는 정책 정의를 사용 하려는 모든 리소스에서 공유 하는 리소스 컨테이너 여야 합니다. 이 리소스 컨테이너는 일반적으로 루트 관리 그룹 근처의 관리 그룹입니다.

## <a name="assignment-scopes"></a>할당 범위

할당에는 범위를 설정 하는 몇 가지 속성이 있습니다. 이러한 속성을 사용 하면 평가할 Azure Policy 리소스와 규정 준수에 대 한 리소스를 결정할 수 있습니다. 이러한 속성은 다음 개념에 매핑됩니다.

- 포함-정의의 준수에 대 한 리소스 계층 또는 개별 리소스를 평가 해야 합니다. `properties.scope`할당 개체의 속성은 규정 준수를 포함 하 고 평가할 항목을 결정 합니다. 자세한 내용은 [할당 정의](./assignment-structure.md)를 참조 하세요.

- 제외-정의의 호환성을 위해 리소스 계층 또는 개별 리소스를 평가할 수 없습니다. `properties.notScopes`할당 개체의 _배열_ 속성은 제외할 항목을 결정 합니다. 이러한 범위 내의 리소스는 평가 되거나 준수 개수에 포함 되지 않습니다. 자세한 내용은 [할당 정의-제외 된 범위](./assignment-structure.md#excluded-scopes)를 참조 하세요.

정책 할당의 속성 외에도는 [정책 예외](./exemption-structure.md) 개체입니다. 예외는 평가할 할당의 일부를 식별 하는 메서드를 제공 하 여 범위 스토리를 향상 시킵니다.

- 예외 (**미리 보기 기능에서 무료** )-리소스 계층 또는 개별 리소스는 정의에의 한 준수 여부를 평가 해야 하지만, 면제가 있거나 다른 방법을 통해 완화 되는 등의 이유로 평가 되지 않습니다. 이 상태의 리소스는 추적 가능 하도록 준수 보고서에서 **제외** 된 것으로 표시 됩니다. 예외 개체는 리소스 계층 구조 또는 개별 리소스에 대해 예외 범위를 결정 하는 자식 개체로 생성 됩니다. 리소스 계층 또는 개별 리소스는 여러 할당에 대해 제외 될 수 있습니다. 예외는 속성을 사용 하 여 일정에 따라 만료 되도록 구성할 수 있습니다 `expiresOn` . 자세한 내용은 [예외 정의](./exemption-structure.md)를 참조 하세요.

  > [!NOTE]
  > 리소스 계층 또는 개별 리소스에 대 한 예외를 부여 하는 영향 때문에, 예외에는 추가 보안 조치가 있습니다. 리소스 계층 이나 개별 리소스에 대 한 작업을 요구 하는 것 외에도 `Microsoft.Authorization/policyExemptions/write` 예외 작성자에 게는 `exempt/Action` 대상 할당에 대 한 동사가 있어야 합니다.

## <a name="scope-comparison"></a>범위 비교

다음 표에서는 범위 옵션을 비교 합니다.

| | 주의 | 제외 (notScopes) | 예외 |
|---|:---:|:---:|:---:|
|**리소스 평가** | &#10004; | - | - |
|**리소스 관리자 개체** | - | - | &#10004; |
|**정책 할당 개체를 수정 해야 합니다.** | &#10004; | &#10004; | - |

## <a name="next-steps"></a>다음 단계

- [정책 정의 구조](./definition-structure.md)에 대해 알아봅니다.
- [프로그래밍 방식으로 정책을 만드는](../how-to/programmatically-create.md) 방법을 이해합니다.
- [규정 준수 데이터를 가져오는](../how-to/get-compliance-data.md) 방법을 알아봅니다.
- [규정 비준수 리소스를 수정](../how-to/remediate-resources.md)하는 방법을 알아봅니다.
- [Azure 관리 그룹으로 리소스 구성](../../management-groups/overview.md)을 포함하는 관리 그룹을 검토합니다.