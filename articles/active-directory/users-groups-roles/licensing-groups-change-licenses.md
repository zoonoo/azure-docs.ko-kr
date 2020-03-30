---
title: 사용자 및 그룹에 대한 라이선스 계획 변경 - Azure AD | 마이크로 소프트 문서
description: Azure Active Directory에서 그룹 라이선스를 사용하여 그룹 내의 사용자를 다른 서비스 계획으로 마이그레이션하는 방법
services: active-directory
keywords: Azure AD 라이선스
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: article
ms.workload: identity
ms.subservice: users-groups-roles
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: sumitp
ms.custom: it-pro;seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: bf2f04e1728f94c89bddcc31c287cc017a79020f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74025896"
---
# <a name="change-license-assignments-for-a-user-or-group-in-azure-active-directory"></a>Azure Active Directory에서 사용자 또는 그룹에 대한 라이선스 할당 변경

이 문서에서는 Azure Active Directory(Azure AD)에서 서비스 라이선스 계획 간에 사용자와 그룹을 이동하는 방법을 설명합니다. Azure AD의 목표는 라이선스 변경 중에 서비스 또는 데이터가 손실되지 않도록 하는 것입니다. 사용자는 서비스 간에 원활하게 전환해야 합니다. 이 문서의 라이센스 계획 할당 단계는 Office 365 E1에서 Office 365 E3로 사용자 또는 그룹을 변경하는 방법을 설명하지만 단계는 모든 라이선스 계획에 적용됩니다. 사용자 또는 그룹에 대한 라이선스 할당을 업데이트하면 라이선스 할당 제거 및 새 할당이 동시에 수행되므로 사용자가 라이선스 를 변경하는 동안 서비스에 대한 액세스 권한을 상실하거나 계획 간에 라이선스 충돌이 표시되지 않습니다.

## <a name="before-you-begin"></a>시작하기 전에

라이선스 할당을 업데이트하기 전에 업데이트할 모든 사용자 또는 그룹에 대해 특정 가정이 사실인지 확인하는 것이 중요합니다. 그룹의 모든 사용자에 대한 가정이 사실이 아닌 경우 일부 사용자에 대해 마이그레이션이 실패할 수 있습니다. 결과적으로, 일부 사용자가 서비스 또는 데이터에 액세스하지 못할 수 있습니다. 다음 사항을 확인합니다.

- 사용자는 그룹에 할당되고 사용자가 상속하고 직접 할당되지 않은 현재 라이선스 계획(이 경우 Office 365 E1)을 가지고 있습니다.

- 할당하는 라이선스 플랜에 사용할 수 있는 라이선스가 충분합니다. 라이선스가 충분하지 않은 경우 일부 사용자에게 새 라이선스 요금제에 할당되지 않을 수 있습니다. 사용 가능한 라이선스 수를 확인할 수 있습니다.

- 사용자는 원하는 라이선스와 충돌하거나 현재 라이선스의 제거를 방지할 수 있는 다른 할당된 서비스 라이센스가 없습니다. 예를 들어 다른 서비스에 종속된 Workplace 분석 또는 프로젝트 온라인과 같은 서비스의 라이선스입니다.

- 온-프레미스 그룹을 관리하고 Azure AD Connect를 통해 Azure AD에 동기화하는 경우 온-프레미스 시스템을 사용하여 사용자를 추가하거나 제거합니다. 변경 내용이 Azure AD와 동기화되려면 그룹 라이선스에 의해 선택되는 데 다소 시간이 걸릴 수 있습니다.

- Azure AD 동적 그룹 구성원을 사용하는 경우 해당 특성을 변경하여 사용자를 추가하거나 제거하지만 라이선스 할당에 대한 업데이트 프로세스는 동일하게 유지됩니다.

## <a name="change-user-license-assignments"></a>사용자 라이선스 할당 변경

라이선스 **할당 업데이트** 페이지에서 일부 확인란을 사용할 수 없는 경우 그룹 라이센스에서 상속되기 때문에 변경할 수 없는 서비스를 나타냅니다.

1. Azure AD 조직의 라이센스 관리자 계정을 사용하여 Azure [포털에](https://portal.azure.com/) 로그인합니다.
1. **Azure Active Directory** > **사용자를**선택한 다음 사용자에 대한 **프로필** 페이지를 엽니다.
1. **라이선스**를 선택합니다.
1. 사용자 또는 그룹에 대한 라이선스 할당을 편집하려면 **할당을** 선택합니다. **할당** 페이지는 라이센스 할당 충돌을 해결할 수 있는 곳입니다.
1. Office 366 E3의 확인란을 선택하고 사용자에게 할당된 모든 E1 서비스가 선택되었는지 확인합니다.
1. Office 365 E1의 확인란을 선택 취소합니다.

    ![Office 365 E1을 표시 하는 사용자에 대 한 라이센스 할당 페이지 지우고 Office 365 E3 선택](media/licensing-groups-change-licenses/update-user-license-assignments.png)

1. **저장**을 선택합니다.

Azure AD는 새 라이선스를 적용하고 이전 라이선스를 동시에 제거하여 서비스 연속성을 제공합니다.

## <a name="change-group-license-assignments"></a>그룹 라이센스 할당 변경

1. Azure AD 조직의 라이센스 관리자 계정을 사용하여 Azure [포털에](https://portal.azure.com/) 로그인합니다.
1. **Azure Active 디렉터리** > **그룹을**선택한 다음 그룹에 대한 **개요** 페이지를 엽니다.
1. **라이선스**를 선택합니다.
1. 사용자 또는 그룹에 대한 라이선스 할당을 편집하려면 **할당** 명령을 선택합니다.
1. Office 366 E3의 확인란을 선택합니다. 서비스의 연속성을 유지하려면 사용자에게 이미 할당된 모든 E1 서비스를 선택해야 합니다.
1. Office 365 E1의 확인란을 선택 취소합니다.

    ![사용자 또는 그룹 라이선스 페이지에서 과제 명령 선택](media/licensing-groups-change-licenses/update-group-license-assignments.png)

1. **저장**을 선택합니다.

서비스 연속성을 제공하기 위해 Azure AD는 새 라이선스를 적용하고 그룹의 모든 사용자에 대해 이전 라이선스를 동시에 제거합니다.

## <a name="next-steps"></a>다음 단계

다음 문서에서 그룹을 통한 라이선스 관리에 대한 기타 시나리오를 알아보세요.

- [Azure Active Directory에서 그룹에 라이선스 할당](../users-groups-roles/licensing-groups-assign.md)
- [Azure Active Directory에서 그룹에 대한 라이선스 문제 식별 및 해결](../users-groups-roles/licensing-groups-resolve-problems.md)
- [Azure Active Directory에서 라이선스를 그룹화하는 개별 사용권 사용자를 마이그레이션하는 방법](../users-groups-roles/licensing-groups-migrate-users.md)
- [Azure Active Directory 그룹 라이선스 추가 시나리오](../users-groups-roles/licensing-group-advanced.md)
- [Azure Active Directory에서 그룹 라이선스에 대한 PowerShell 예제](../users-groups-roles/licensing-ps-examples.md)
