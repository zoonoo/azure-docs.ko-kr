---
title: 최근에 삭제된 사용자 복원 또는 영구 제거 - Azure Active Directory | Microsoft Docs
description: Azure Active Directory를 사용하여 복원 가능한 사용자를 보거나, 삭제된 사용자를 복원하거나, 사용자를 영구적으로 삭제하는 방법.
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.component: fundamentals
ms.topic: conceptual
ms.date: 12/17/2018
ms.author: lizross
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.openlocfilehash: 995a6a9da637c23663c51fe2f4b175741f5849b6
ms.sourcegitcommit: 9b6492fdcac18aa872ed771192a420d1d9551a33
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/22/2019
ms.locfileid: "54450325"
---
# <a name="restore-or-remove-a-recently-deleted-user-using-azure-active-directory"></a>Azure Active Directory를 사용하여 최근에 삭제된 사용자를 복원하거나 제거
사용자를 삭제하면 30일 동안 계정이 일시 중단된 상태로 유지됩니다. 30일이라는 기간 동안 사용자는 모든 속성과 함께 계정을 복원할 수 있습니다. 30일이라는 기간이 지나면 사용자가 자동으로 영구적으로 삭제됩니다.

Azure Portal에서 Azure AD(Azure Active Directory)를 사용하여 복원 가능한 사용자를 보거나, 삭제된 사용자를 복원하거나, 사용자를 영구적으로 삭제할 수 있습니다.

>[!Important]
>사용자나 Microsoft 고객 지원에서는 영구적으로 삭제된 사용자를 복원할 수 없습니다.

## <a name="required-permissions"></a>필요한 사용 권한
사용자를 복원하거나 영구적으로 삭제하려면 다음 역할 중 하나가 있어야 합니다.

- 회사 관리자

- 파트너 계층1 지원

- 파트너 계층2 지원

- 사용자 계정 관리자

## <a name="view-your-restorable-users"></a>복원 가능한 사용자 보기
30일 이내에 삭제된 모든 사용자를 볼 수 있습니다. 이러한 사용자를 복원할 수 있습니다.

### <a name="to-view-your-restorable-users"></a>복원 가능한 사용자를 보려면
1. 해당 디렉터리에 대한 글로벌 관리자 계정을 사용하여 [Azure Portal](https://portal.azure.com/)에 로그인합니다.

2. **Azure Active Directory**를 선택하고, **사용자**를 선택한 다음, **삭제된 사용자**를 선택합니다.

    복원에 사용할 수 있는 사용자 목록을 검토합니다.

    ![사용자 - 여전히 복원할 수 있는 사용자를 포함한 삭제된 사용자 페이지](media/active-directory-users-restore/users-deleted-users-view-restorable.png)

## <a name="restore-a-recently-deleted-user"></a>최근에 삭제된 사용자 복원
사용자의 계정이 일시 중단된 동안 관련된 모든 디렉터리 정보가 유지됩니다. 사용자를 복원할 때 이 디렉터리 정보도 복원됩니다.

### <a name="to-restore-a-user"></a>사용자를 복원하려면
1. **사용자 - 삭제된 사용자** 페이지에서 사용할 수 있는 사용자 중 하나를 검색하고 선택합니다. 예를 들어, _Mary Parker_입니다.

2. **사용자 복원**을 선택합니다.

    ![사용자 - 사용자 복원 옵션이 강조 표시된 삭제된 사용자 페이지](media/active-directory-users-restore/users-deleted-users-restore-user.png)

## <a name="permanently-delete-a-user"></a>사용자 영구 삭제
30일 동안 자동 삭제되기를 기다리지 않고 디렉터리에서 사용자를 영구적으로 삭제할 수 있습니다. 사용자나 다른 관리자 또는 Microsoft 고객 지원에서 영구적으로 삭제된 사용자를 복원할 수 없습니다.

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

- [다른 디렉터리에서 게스트 사용자 추가](../b2b/what-is-b2b.md) 

사용 가능한 다른 사용자 관리 작업에 대한 자세한 내용은 [Azure Active Directory 사용자 관리 설명서](../users-groups-roles/index.yml)를 참조하세요.
