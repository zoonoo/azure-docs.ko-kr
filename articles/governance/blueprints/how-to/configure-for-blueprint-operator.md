---
title: 블루프린트 오퍼레이터를 위한 환경 설정
description: Blueprint 연산자 기본 제공 역할 기반 액세스 제어(RBAC) 역할과 함께 사용하도록 Azure 환경을 구성하는 방법을 알아봅니다.
ms.date: 08/26/2019
ms.topic: how-to
ms.openlocfilehash: fba0dd3f2eeb69f768800d1d04640510462d3c86
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74873219"
---
# <a name="configure-your-environment-for-a-blueprint-operator"></a>청사진 운영자를 위한 환경 구성

청사진 정의 및 청사진 할당 관리는 다른 팀에 할당할 수 있습니다. 설계자 나 거버넌스 팀이 Blueprint 정의의 수명 주기 관리를 담당하는 반면 운영 팀은 중앙에서 제어되는 청사진 정의의 할당을 관리하는 것이 일반적입니다.

**BLUEPRINT 연산자** 기본 제공 역할 기반 액세스 제어(RBAC)는 이러한 유형의 시나리오에서 사용하도록 특별히 설계되었습니다. 이 역할을 사용하면 운영 유형 팀이 조직 청사진 정의의 할당을 관리할 수 있지만 이를 수정할 수는 없습니다. 이렇게 하려면 Azure 환경에서 일부 구성이 필요하며 이 문서에서는 필요한 단계를 설명합니다.

## <a name="grant-permission-to-the-blueprint-operator"></a>블루프린트 운영자에게 권한 부여

첫 번째 단계는 **Blueprint를** 할당할 계정 또는 보안 그룹(권장)에 Blueprint 운영자 역할을 부여하는 것입니다. 이 작업은 운영 팀이 Blueprint 할당 액세스 권한이 있어야 하는 모든 관리 그룹 및 구독을 포함하는 관리 그룹 계층 구조의 최상위 수준에서 수행해야 합니다. 이러한 권한을 부여할 때 최소 권한 원칙을 따르는 것이 좋습니다.

1. (권장) [보안 그룹을 만들고 구성원을 추가합니다.](../../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)

1. 계정 또는 보안 그룹에 **Blueprint 연산자의** [역할 할당](../../../role-based-access-control/role-assignments-portal.md#add-a-role-assignment) 추가

## <a name="user-assign-managed-identity"></a>관리되는 ID를 사용자 할부로 지정

Blueprint 정의는 시스템 할당 또는 사용자 할당된 관리 ID를 사용할 수 있습니다. 그러나 **Blueprint 연산자** 역할을 사용하는 경우 사용자가 할당한 관리 ID를 사용하도록 Blueprint 정의를 구성해야 합니다. 또한 **Blueprint 운영자** 역할이 부여되는 계정 또는 보안 그룹에는 사용자가 할당한 관리 ID에 대해 **관리되는 ID 운영자** 역할을 부여해야 합니다. 이 권한이 없으면 사용 권한이 부족하여 Blueprint 할당이 실패합니다.

1. 할당된 Blueprint에서 사용할 [사용자 할당관리 ID 만들기](../../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity)

1. **관리되는 ID 연산자의** [역할 할당을](../../../role-based-access-control/role-assignments-portal.md#add-a-role-assignment) 계정 또는 보안 그룹에 추가합니다. 역할 할당을 새 사용자 할당관리 ID로 범위를 지정합니다.

1. Blueprint **연산자는**새 사용자 할당된 관리 ID를 사용하는 [Blueprint를 할당합니다.](../create-blueprint-portal.md#assign-a-blueprint)

## <a name="next-steps"></a>다음 단계

- [청사진 수명 주기에](../concepts/lifecycle.md)대해 자세히 알아봅니다.
- [정적 및 동적 매개 변수](../concepts/parameters.md) 사용 방법 이해
- [청사진 시퀀싱 순서](../concepts/sequencing-order.md)를 사용자 지정하는 방법 알아보기
- [청사진 리소스 잠금](../concepts/resource-locking.md)을 활용하는 방법 알아보기
- [일반 문제 해결 방법](../troubleshoot/general.md)을 통해 청사진 할당 중에 발생하는 문제 해결