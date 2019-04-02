---
title: 최근에 삭제된 사용자 복원 또는 영구 제거 - Azure Active Directory | Microsoft Docs
description: Azure Active Directory를 사용하여 복원 가능한 사용자를 보거나, 삭제된 사용자를 복원하거나, 사용자를 영구적으로 삭제하는 방법.
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 04/01/2018
ms.author: lizross
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: d69bd931f2f8c72fd1e6fc79c16662ea367617d6
ms.sourcegitcommit: 3341598aebf02bf45a2393c06b136f8627c2a7b8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/01/2019
ms.locfileid: "58802017"
---
# <a name="restore-or-remove-a-recently-deleted-user-using-azure-active-directory"></a>Azure Active Directory를 사용하여 최근에 삭제된 사용자를 복원하거나 제거
사용자를 삭제하면 30일 동안 계정이 일시 중단된 상태로 유지됩니다. 30일이라는 기간 동안 사용자는 모든 속성과 함께 계정을 복원할 수 있습니다. 30일이라는 기간이 지나면 사용자가 자동으로 영구적으로 삭제됩니다.

Azure Portal에서 Azure AD(Azure Active Directory)를 사용하여 복원 가능한 사용자를 보거나, 삭제된 사용자를 복원하거나, 사용자를 영구적으로 삭제할 수 있습니다.

>[!Important]
>사용자나 Microsoft 고객 지원에서는 영구적으로 삭제된 사용자를 복원할 수 없습니다.

## <a name="required-permissions"></a>필요한 사용 권한
사용자를 복원하거나 영구적으로 삭제하려면 다음 역할 중 하나가 있어야 합니다.

- 전역 관리자

- 파트너 계층1 지원

- 파트너 계층2 지원

- 사용자 관리자

## <a name="view-your-restorable-users"></a>복원 가능한 사용자 보기
30일 이내에 삭제된 모든 사용자를 볼 수 있습니다. 이러한 사용자를 복원할 수 있습니다.

### <a name="to-view-your-restorable-users"></a>복원 가능한 사용자를 보려면
1. 에 로그인 합니다 [Azure portal](https://portal.azure.com/) 조직의 전역 관리자 계정을 사용 합니다.

2. **Azure Active Directory**를 선택하고, **사용자**를 선택한 다음, **삭제된 사용자**를 선택합니다.

    복원에 사용할 수 있는 사용자 목록을 검토합니다.

    ![사용자 - 여전히 복원할 수 있는 사용자를 포함한 삭제된 사용자 페이지](media/active-directory-users-restore/users-deleted-users-view-restorable.png)

## <a name="restore-a-recently-deleted-user"></a>최근에 삭제된 사용자 복원

조직에서 사용자 계정이 삭제 되 면 계정이 일시 중단 된 상태에서 및 모든 관련된 조직 정보 보존 됩니다. 사용자를 복원할 때이 조직 정보도 복원 됩니다.

> [!Note]
> 사용자 복원 되 면 삭제 시 사용자에 게 할당 된 라이선스를 사용할 수 없는 사용자 라이선스에 대 한 경우에 복원 됩니다. 그런 다음 사용 하는 경우 자세히 라이선스를 구입한 것 보다 더, 조직의 라이선스 사용에 대 한 준수 일시적으로 수 있습니다.

### <a name="to-restore-a-user"></a>사용자를 복원하려면
1. **사용자 - 삭제된 사용자** 페이지에서 사용할 수 있는 사용자 중 하나를 검색하고 선택합니다. 예를 들어, _Mary Parker_입니다.

2. **사용자 복원**을 선택합니다.

    ![사용자 - 사용자 복원 옵션이 강조 표시된 삭제된 사용자 페이지](media/active-directory-users-restore/users-deleted-users-restore-user.png)

## <a name="permanently-delete-a-user"></a>사용자 영구 삭제
30 일 동안 자동 삭제를 기다리지 않고 조직에서 사용자를 영구적으로 삭제할 수 있습니다. 사용자나 다른 관리자 또는 Microsoft 고객 지원에서 영구적으로 삭제된 사용자를 복원할 수 없습니다.

>[!Note]
>사용자를 실수로 영구적으로 삭제하면 새 사용자를 만들고 모든 이전 정보를 수동으로 입력해야 합니다. 새 사용자 만들기에 대한 자세한 내용은 [사용자 추가 또는 삭제](add-users-azure-active-directory.md)를 참조하세요.

### <a name="to-permanently-delete-a-user"></a>사용자를 영구적으로 삭제하려면:

1. **사용자 - 삭제된 사용자** 페이지에서 사용할 수 있는 사용자 중 하나를 검색하고 선택합니다. _Rae Huff_를 예를 들겠습니다.

2. **영구적으로 삭제**를 선택합니다.

    ![사용자 - 사용자 복원 옵션이 강조 표시된 삭제된 사용자 페이지](media/active-directory-users-restore/users-deleted-users-permanent-delete-user.png)

## <a name="next-steps"></a>다음 단계
사용자를 복원하거나 삭제하면 다음 기본 프로세스를 수행할 수 있습니다.

- [사용자 추가 또는 삭제](add-users-azure-active-directory.md)

- [사용자에게 역할 할당](active-directory-users-assign-role-azure-portal.md)

- [프로필 정보 추가 또는 변경](active-directory-users-profile-azure-portal.md)

- [다른 조직에서 게스트 사용자 추가](../b2b/what-is-b2b.md)

다른 사용 가능한 사용자 관리 작업에 대 한 자세한 내용은 [Azure AD 사용자 관리 설명서](../users-groups-roles/index.yml)합니다.
