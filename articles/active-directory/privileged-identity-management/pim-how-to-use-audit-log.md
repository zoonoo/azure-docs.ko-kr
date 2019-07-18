---
title: PIM-Azure Active Directory에서에서 Azure AD 역할에 대 한 감사 기록을 보고 | Microsoft Docs
description: Azure AD Privileged Identity Management (PIM)에서 Azure AD 역할에 대 한 감사 내역을 보는 방법에 알아봅니다.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 06/10/2019
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8061cff8d39db66cb22a5650c7688657aa8b3554
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67053952"
---
# <a name="view-audit-history-for-azure-ad-roles-in-pim"></a>PIM에서 Azure AD 역할에 대 한 감사 기록 보기

모든 권한 있는 역할에 대 한 지난 30 일이 내의 모든 역할 할당 및 활성화를 확인 하려면 Azure Active Directory (Azure AD) Privileged Identity Management (PIM) 감사 기록에 사용할 수 있습니다. 작업 관리자, 최종 사용자 및 동기화 작업을 포함 하 여 디렉터리의 전체 감사 기록을 확인 하려는 경우 사용할 수 있습니다 합니다 [Azure Active Directory 보안 및 작업 보고서](../reports-monitoring/overview-reports.md)합니다.

## <a name="view-audit-history"></a>감사 기록 보기

Azure AD 역할에 대 한 감사 기록을 보려면 다음이 단계를 수행 합니다.

1. [권한 있는 역할 관리자](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) 역할의 구성원인 사용자로 [Azure Portal](https://portal.azure.com/)에 로그인합니다.

1. **Azure AD Privileged Identity Management**를 엽니다.

1. **Azure AD 역할**을 클릭합니다.

1. 클릭 **디렉터리 역할 감사 기록**합니다.

    사용자 감사 기록에 따라 세로 막대형 차트는 총 활성화 수, 일일 최대 활성화 및 일일 평균 활성화와 함께 표시 됩니다.

    ![디렉터리 역할 감사 기록](media/pim-how-to-use-audit-log/directory-roles-audit-history.png)

    페이지의 맨 아래에 테이블을 사용할 수 있는 감사 기록의 각 작업에 대 한 정보를 사용 하 여 표시 됩니다. 열에는 다음과 같은 의미가 있습니다.

    | 열 | 설명 |
    | --- | --- |
    | Time | 작업이 발생 합니다. |
    | 요청자 | 역할 활성화 또는 변경을 요청한 사용자입니다. 값이 **Azure 시스템**, 자세한 내용은 Azure 감사 기록을 확인 합니다. |
    | 액션(Action) | 요청자에 의해 수행 된 작업입니다. 작업 할당, 할당 해제, 활성화, 비활성화, 또는 AddedOutsidePIM 포함할 수 있습니다. |
    | 멤버 | 사용자를 활성화 또는 역할에 할당 합니다. |
    | 역할 | 역할 할당 또는 사용자가 활성화 합니다. |
    | 추론 | 활성화 하는 동안 이유 필드에 입력 된 텍스트입니다. |
    | 만료 | 경우는 활성화 된 역할에 만료 됩니다. 적격 역할 할당에만 적용 됩니다. |

1. 감사 기록을 정렬 하려면 클릭 합니다 **시간**, **동작**, 및 **역할** 단추입니다.

## <a name="filter-audit-history"></a>감사 기록 필터링

1. 감사 기록 페이지의 위쪽을 클릭 합니다 **필터** 단추입니다.

    합니다 **차트 매개 변수를 업데이트** 창이 나타납니다.

1. **시간 범위**, 시간 범위를 선택 합니다.

1. **역할**, 확인 하려는 역할에 대 한 확인 표시를 추가 합니다.

    ![차트 매개 변수 창 업데이트](media/pim-how-to-use-audit-log/update-chart-parameters.png)

1. 클릭 **수행** 필터링 된 감사 내역을 보고 합니다.

## <a name="next-steps"></a>다음 단계

- [PIM에서 Azure 리소스 역할에 대한 작업 및 감사 기록 보기](azure-pim-resource-rbac.md)
