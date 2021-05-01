---
title: 최근에 삭제된 사용자 복원 또는 영구 제거 - Azure AD
description: Azure Active Directory를 사용하여 복원 가능한 사용자를 보거나, 삭제된 사용자를 복원하거나, 사용자를 영구적으로 삭제하는 방법.
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: how-to
ms.date: 10/23/2020
ms.author: ajburnle
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: bd4d42e1cafa58aaf6a47188c9e86c24ac175047
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92514392"
---
# <a name="restore-or-remove-a-recently-deleted-user-using-azure-active-directory"></a>Azure Active Directory를 사용하여 최근에 삭제된 사용자를 복원하거나 제거
사용자를 삭제하면 30일 동안 계정이 일시 중단된 상태로 유지됩니다. 30일이라는 기간 동안 사용자는 모든 속성과 함께 계정을 복원할 수 있습니다. 30일 기간이 지나면 영구 삭제 프로세스가 자동으로 시작됩니다.

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
1. 조직에 대한 글로벌 관리자 계정을 사용하여 [Azure Portal](https://portal.azure.com/)에 로그인합니다.

2. **Azure Active Directory** 를 선택하고, **사용자** 를 선택한 다음, **삭제된 사용자** 를 선택합니다.

    복원에 사용할 수 있는 사용자 목록을 검토합니다.

    ![사용자 - 여전히 복원할 수 있는 사용자를 포함한 삭제된 사용자 페이지](media/active-directory-users-restore/users-deleted-users-view-restorable.png)

## <a name="restore-a-recently-deleted-user"></a>최근에 삭제된 사용자 복원

조직에서 사용자 계정을 삭제하면 해당 계정이 일시 중단된 상태가 되며 관련된 모든 조직 정보가 보존됩니다. 사용자를 복원하면 이 조직 정보도 복원됩니다.

> [!Note]
> 사용자가 복원되면 해당 라이선스에 사용 가능한 사용자가 없는 경우에도 삭제 시 사용자에게 할당된 라이선스가 복원됩니다. 구매한 것보다 더 많은 라이선스를 사용하는 경우 조직은 일시적으로 라이선스 사용 규정 준수를 위반할 수 있습니다.

### <a name="to-restore-a-user"></a>사용자를 복원하려면
1. **사용자 - 삭제된 사용자** 페이지에서 사용할 수 있는 사용자 중 하나를 검색하고 선택합니다. 예를 들어, _Mary Parker_ 입니다.

2. **사용자 복원** 을 선택합니다.

    ![사용자 - 사용자 복원 옵션이 강조 표시된 삭제된 사용자 페이지](media/active-directory-users-restore/users-deleted-users-restore-user.png)

## <a name="permanently-delete-a-user"></a>사용자 영구 삭제
30일 동안 자동 삭제되기를 기다리지 않고 조직에서 사용자를 영구적으로 삭제할 수 있습니다. 사용자나 다른 관리자 또는 Microsoft 고객 지원에서 영구적으로 삭제된 사용자를 복원할 수 없습니다.

>[!Note]
>사용자를 실수로 영구적으로 삭제하면 새 사용자를 만들고 모든 이전 정보를 수동으로 입력해야 합니다. 새 사용자 만들기에 대한 자세한 내용은 [사용자 추가 또는 삭제](add-users-azure-active-directory.md)를 참조하세요.

### <a name="to-permanently-delete-a-user"></a>사용자를 영구적으로 삭제하려면:

1. **사용자 - 삭제된 사용자** 페이지에서 사용할 수 있는 사용자 중 하나를 검색하고 선택합니다. _Rae Huff_ 를 예를 들겠습니다.

2. **영구적으로 삭제** 를 선택합니다.

    ![사용자 - 사용자 삭제 옵션이 강조 표시된 삭제된 사용자 페이지](media/active-directory-users-restore/users-deleted-users-permanent-delete-user.png)

## <a name="next-steps"></a>다음 단계
사용자를 복원하거나 삭제하면 다음을 수행할 수 있습니다.

- [사용자 추가 또는 삭제](add-users-azure-active-directory.md)

- [사용자에게 역할 할당](active-directory-users-assign-role-azure-portal.md)

- [프로필 정보 추가 또는 변경](active-directory-users-profile-azure-portal.md)

- [다른 조직에서 게스트 사용자 추가](../external-identities/what-is-b2b.md)

사용 가능한 다른 사용자 관리 작업에 대한 자세한 내용은 [Azure AD 사용자 관리 설명서](../enterprise-users/index.yml)를 참조하세요.
