---
title: 사용자 및 그룹에 대 한 라이선스 계획 변경-Azure AD | Microsoft Docs
description: Azure Active Directory에서 그룹 라이선스를 사용 하 여 그룹 내의 사용자를 다른 서비스 계획으로 마이그레이션하는 방법
services: active-directory
keywords: Azure AD 라이선스
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: users-groups-roles
ms.date: 07/07/2020
ms.author: curtand
ms.reviewer: sumitp
ms.custom: it-pro;seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 94c0d1efa83af53804be89a9e86f4cafd5bc0ae0
ms.sourcegitcommit: bcb962e74ee5302d0b9242b1ee006f769a94cfb8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/07/2020
ms.locfileid: "86057758"
---
# <a name="change-license-assignments-for-a-user-or-group-in-azure-active-directory"></a>Azure Active Directory에서 사용자 또는 그룹에 대 한 라이선스 할당 변경

이 문서에서는 Azure Active Directory (Azure AD)에서 서비스 라이선스 계획 간에 사용자 및 그룹을 이동 하는 방법을 설명 합니다. Azure AD의 접근 방식은 라이선스 변경을 수행 하는 동안 서비스 또는 데이터가 손실 되지 않도록 하는 것입니다. 사용자는 서비스 간을 원활 하 게 전환 해야 합니다. 이 문서의 라이선스 계획 할당 단계에서는 Office 365 E1의 사용자 또는 그룹을 Office 365 E3으로 변경 하는 방법에 대해 설명 하지만,이 단계는 모든 라이선스 계획에 적용 됩니다. 사용자 또는 그룹에 대 한 라이선스 할당을 업데이트 하는 경우 라이선스를 변경 하는 동안 사용자가 서비스에 액세스 하지 못하게 하거나 계획 간 라이선스 충돌을 확인 하기 위해 라이선스 할당 제거 및 새 할당이 동시에 수행 됩니다.

## <a name="before-you-begin"></a>시작하기 전에

라이선스 할당을 업데이트 하기 전에 업데이트할 모든 사용자 또는 그룹에 대 한 특정 가정이 true 인지 확인 하는 것이 중요 합니다. 그룹의 모든 사용자에 대해 가정이 적용 되지 않는 경우 일부에 대해 마이그레이션이 실패할 수 있습니다. 결과적으로, 일부 사용자가 서비스 또는 데이터에 액세스하지 못할 수 있습니다. 다음 사항을 확인합니다.

- 사용자는 그룹에 할당 되 고 사용자가 상속 되며 직접 할당 되지 않은 현재 라이선스 계획 (이 경우에는 Office 365 E1)이 있습니다.

- 할당 하는 라이선스 계획에 사용할 수 있는 라이선스가 충분 합니다. 라이선스가 부족 한 경우에는 일부 사용자에 게 새 라이선스 계획이 할당 되지 않을 수 있습니다. 사용 가능한 라이선스 수를 확인할 수 있습니다.

- 사용자에 게 원하는 라이선스와 충돌 하거나 현재 라이선스의 제거를 방지할 수 있는 다른 할당 된 서비스 라이선스가 없습니다. 예를 들어, 다른 서비스에 대 한 종속성이 있는 작업 공간 분석 또는 Project Online과 같은 서비스의 라이선스가 있습니다.

- 온-프레미스에서 그룹을 관리 하 고 Azure AD Connect를 통해 Azure AD에 동기화 하는 경우 온-프레미스 시스템을 사용 하 여 사용자를 추가 하거나 제거 합니다. 변경 내용을 Azure AD와 동기화 하 여 그룹 라이선스에서 선택 하는 데 다소 시간이 걸릴 수 있습니다.

- Azure AD 동적 그룹 멤버 자격을 사용 하는 경우 해당 특성을 변경 하 여 사용자를 추가 하거나 제거 하지만 라이선스 할당에 대 한 업데이트 프로세스는 동일 하 게 유지 됩니다.

## <a name="change-user-license-assignments"></a>사용자 라이선스 할당 변경

**라이선스 할당 업데이트** 페이지에서 일부 확인란을 사용할 수 없는 경우 그룹 라이선스에서 상속 되므로 변경할 수 없는 서비스를 나타냅니다.

1. Azure AD 조직의 라이선스 관리자 계정을 사용 하 여 [Azure Portal](https://portal.azure.com/) 에 로그인 합니다.
1. **Azure Active Directory**  >  **사용자**를 선택 하 고 사용자에 대 한 **프로필** 페이지를 엽니다.
1. **라이선스**를 선택합니다.
1. **할당** 을 선택 하 여 사용자 또는 그룹에 대 한 라이선스 할당을 편집 합니다. **할당** 페이지에서 라이선스 할당 충돌을 해결할 수 있습니다.
1. Office 365 E3의 확인란을 선택 하 고 최소한 사용자에 게 할당 된 모든 E1 서비스가 선택 되어 있는지 확인 합니다.
1. Office 365 E1의 확인란을 선택 취소 합니다.

    ![Office 365 E1의 선택을 취소 하 고 Office 365 E3을 선택한 사용자에 대 한 라이선스 할당 페이지](media/licensing-groups-change-licenses/update-user-license-assignments.png)

1. **저장**을 선택합니다.

Azure AD는 새 라이선스를 적용 하 고 이전 라이선스를 동시에 제거 하 여 서비스 연속성을 제공 합니다.

## <a name="change-group-license-assignments"></a>그룹 라이선스 할당 변경

1. Azure AD 조직의 라이선스 관리자 계정을 사용 하 여 [Azure Portal](https://portal.azure.com/) 에 로그인 합니다.
1. **Azure Active Directory**  >  **그룹**을 선택 하 고 그룹에 대 한 **개요** 페이지를 엽니다.
1. **라이선스**를 선택합니다.
1. **할당** 명령을 선택 하 여 사용자 또는 그룹에 대 한 라이선스 할당을 편집 합니다.
1. Office 365 E3의 확인란을 선택 합니다. 서비스 연속성을 유지 하려면 사용자에 게 이미 할당 된 모든 E1 서비스를 선택 했는지 확인 합니다.
1. Office 365 E1의 확인란을 선택 취소 합니다.

    ![사용자 또는 그룹 라이선스 페이지에서 할당 명령 선택](media/licensing-groups-change-licenses/update-group-license-assignments.png)

1. **저장**을 선택합니다.

서비스 연속성을 제공 하기 위해 Azure AD는 새 라이선스를 적용 하 고 그룹의 모든 사용자에 대해 이전 라이선스를 동시에 제거 합니다.

## <a name="next-steps"></a>다음 단계

다음 문서에서 그룹을 통한 라이선스 관리에 대한 기타 시나리오를 알아보세요.

- [Azure Active Directory에서 그룹에 라이선스 할당](../users-groups-roles/licensing-groups-assign.md)
- [Azure Active Directory에서 그룹에 대한 라이선스 문제 식별 및 해결](../users-groups-roles/licensing-groups-resolve-problems.md)
- [Azure Active Directory에서 개별 라이선스 사용자를 그룹 라이선스로 마이그레이션하는 방법](../users-groups-roles/licensing-groups-migrate-users.md)
- [Azure Active Directory 그룹 라이선스 추가 시나리오](../users-groups-roles/licensing-group-advanced.md)
- [Azure Active Directory의 그룹 라이선스에 대 한 PowerShell 예제](../users-groups-roles/licensing-ps-examples.md)
