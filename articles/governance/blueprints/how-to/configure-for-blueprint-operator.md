---
title: 청사진 운영자를 위한 환경 설정
description: Azure 기본 제공 역할 청사진 운영자를 사용하도록 Azure 환경을 구성하는 방법 알아보기.
ms.date: 08/17/2021
ms.topic: how-to
ms.openlocfilehash: 5c2da686a2a145c42e53260e96b95b2ae14c4770
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/17/2021
ms.locfileid: "122530690"
---
# <a name="configure-your-environment-for-a-blueprint-operator"></a>청사진 운영자를 위한 환경 구성

청사진 정의 및 할당 관리를 다른 팀에 할당할 수 있습니다. 설계자 또는 거버넌스 팀은 청사진 정의의 수명 주기 관리를 담당하고 운영 팀은 중앙에서 제어되는 청사진 정의의 할당을 관리하는 것이 일반적입니다.

**Blueprint Operator** 의 기본 제공 역할은 해당 유형의 시나리오에서 사용하기 위해 특별히 설계되었습니다. 역할을 사용하면 작업 유형 팀에서 조직 청사진 정의의 할당을 관리할 수 있지만 수정할 수는 없습니다. 이렇게 하려면 Azure 환경에서 몇 가지 구성이 필요합니다. 이 문서에서 이에 대해 설명하기로 합니다.

## <a name="grant-permission-to-the-blueprint-operator"></a>Blueprint Operator에 사용 권한 부여

첫 번째 단계는 청사진을 할당할 계정 또는 보안 그룹(권장)에 **Blueprint Operator** 역할을 할당하는 것입니다. 해당 작업은 운영 팀에서 청사진 할당 액세스 권한을 보유해야 하는 관리 그룹 및 구독을 모두 포함하는 관리 그룹 계층 구조의 최상위 수준에서 수행해야 합니다. 해당 사용 권한을 부여할 때 최소 권한의 원칙을 따르는 것이 좋습니다.

1. (권장)[보안 그룹 만들기 및 구성원 추가](../../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)

1. 계정 또는 보안 그룹에 **Blueprint Operator** 의 [Azure 역할 할당](../../../role-based-access-control/role-assignments-portal.md)

## <a name="user-assign-managed-identity"></a>사용자 할당 관리 ID

청사진 정의는 시스템 할당 또는 사용자 할당 관리 ID 중 하나를 사용할 수 있습니다. 그러나 **Blueprint Operator** 역할을 사용하는 경우에는 사용자 할당 관리 ID를 사용하도록 청사진 정의를 구성해야 합니다. 또한 **Blueprint Operator** 역할을 부여하는 계정 또는 보안 그룹에는 사용자 할당 관리 ID에 대해 **Managed Identity Operator** 역할을 부여해야 합니다. 해당 권한이 없으면 사용 권한 부족으로 청사진 할당에 실패합니다.

1. 할당된 청사진에서 사용할 [사용자 할당 관리 ID를 만듭니다](../../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity).

1. 사용자 할당 관리 ID에 대해 의도한 범위의 청사진 정의에 필요한 역할 또는 사용 권한을 부여합니다.

1. 계정 또는 보안 그룹에 **Managed Identity Operator** 의 [Azure 역할을 할당](../../../role-based-access-control/role-assignments-portal.md)합니다. 역할 할당의 범위를 새 사용자 할당 관리 ID로 지정합니다.

1. **Blueprint Operator** 는 새로운 사용자 할당 관리 ID를 사용하는 [청사진을 할당](../create-blueprint-portal.md#assign-a-blueprint)합니다.

## <a name="next-steps"></a>다음 단계

- [청사진 수명 주기](../concepts/lifecycle.md)에 대해 알아봅니다.
- [정적 및 동적 매개 변수](../concepts/parameters.md) 사용 방법 이해
- [청사진 시퀀싱 순서](../concepts/sequencing-order.md)를 사용자 지정하는 방법 알아보기
- [청사진 리소스 잠금](../concepts/resource-locking.md)을 활용하는 방법 알아보기
- [일반 문제 해결 방법](../troubleshoot/general.md)을 통해 청사진 할당 중에 발생하는 문제 해결