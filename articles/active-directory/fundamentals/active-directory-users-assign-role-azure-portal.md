---
title: 사용자에게 Azure AD 역할 할당 - Azure Active Directory | Microsoft Docs
description: Azure Active Directory를 사용하여 사용자에게 관리자 및 비관리자 역할을 할당하는 방법에 대한 지침입니다.
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: how-to
ms.date: 08/31/2020
ms.author: ajburnle
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: fe0c7eec5c023fcba3152ad6329d318210a0c2ab
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92370936"
---
# <a name="assign-administrator-and-non-administrator-roles-to-users-with-azure-active-directory"></a>Azure Active Directory를 사용하여 사용자에게 관리자 및 비관리자 역할 할당

Azure AD(Azure Active Directory)에서 사용자 중 한 명에게 Azure AD 리소스를 관리할 수 있는 권한이 필요한 경우 필요한 권한을 제공하는 역할에 할당해야 합니다. Azure 리소스를 관리하는 역할 및 Azure AD 리소스를 관리하는 역할에 대한 자세한 내용은 [클래식 구독 관리자 역할, Azure 역할, Azure AD 역할](../../role-based-access-control/rbac-and-directory-admin-roles.md)을 참조하세요.

사용 가능한 Azure AD 역할에 대한 자세한 내용은 [Azure Active Directory에서 관리자 역할 할당](../roles/permissions-reference.md)을 참조하세요. 사용자를 추가하려면 [Azure Active Directory에 새 사용자 추가](add-users-azure-active-directory.md)를 참조하세요.

## <a name="assign-roles"></a>역할 할당

사용자에게 Azure AD 역할을 할당하는 일반적인 방법은 사용자에게 **할당된 역할** 페이지를 사용하는 것입니다. 또한 PIM(Privileged Identity Management)을 사용하여 사용자 자격이 Just-In-Time 역할로 승격되도록 구성할 수 있습니다. PIM을 사용하는 방법에 대한 자세한 내용은 [Privileged Identity Management](../privileged-identity-management/index.yml)를 참조하세요.

> [!Note]
> Azure AD Premium P2 라이선스 플랜이 있고 PIM을 이미 사용하는 경우 모든 역할 관리 작업은 [Privileged Identity Management 환경](../roles/manage-roles-portal.md)에서 수행됩니다. 이 기능은 현재 한 번에 하나의 역할만 할당하도록 제한되어 있습니다. 현재는 여러 역할을 선택하여 한 사용자에게 한 번에 할당할 수 없습니다.
>
> ![PIM을 이미 사용하고 Premium P2 라이선스가 있는 사용자를 위해 PIM에서 관리되는 Azure AD 역할](./media/active-directory-users-assign-role-azure-portal/pim-manages-roles-for-p2.png)

## <a name="assign-a-role-to-a-user"></a>사용자에 역할 할당

1. [Azure Portal](https://portal.azure.com/)로 이동한 후 해당 디렉터리에 대한 전역 관리자 계정을 사용하여 로그인합니다.

2. **Azure Active Directory** 를 검색하고 선택합니다.

      ![Azure Active Directory에 대한 Azure Portal 검색](media/active-directory-users-assign-role-azure-portal/search-azure-active-directory.png)

3. **사용자** 를 선택합니다.

4. 역할 할당을 받은 사용자를 검색하고 선택합니다. 예를 들어 _Alain Charon_ 입니다.

      ![모든 사용자 페이지 - 사용자 선택](media/active-directory-users-assign-role-azure-portal/directory-role-select-user.png)

5. **Alain Charon - 프로필** 페이지에서 **할당된 역할** 을 선택합니다.

    **Alain Charon - 관리 역할** 페이지가 나타납니다.

6. **할당 추가** 를 선택하고, Alain에게 할당할 역할(예: _애플리케이션 관리자_)을 선택한 다음, **선택** 을 선택합니다.

    ![선택한 역할을 보여 주는 할당된 역할 페이지](media/active-directory-users-assign-role-azure-portal/directory-role-select-role.png)

    애플리케이션 관리자 역할이 Alain Charon에 할당되고 **Alain Charon - 관리 역할** 페이지에 표시됩니다.

## <a name="remove-a-role-assignment"></a>역할 할당 제거

사용자에서 역할 할당을 제거해야 할 경우 **Alain Charon - 관리 역할** 페이지에서 제거할 수도 있습니다.

### <a name="to-remove-a-role-assignment-from-a-user"></a>사용자에서 역할 할당을 제거하려면

1. **Azure Active Directory** 를 선택하고, **사용자** 를 선택한 후, 역할 할당을 제거할 사용자를 검색하여 선택합니다. 예를 들어 _Alain Charon_ 입니다.

2. **할당된 역할** 을 선택하고 **애플리케이션 관리자** 를 선택한 다음, **할당 제거** 를 선택합니다.

    ![선택된 역할과 제거 옵션을 보여 주는 디렉터리 역할 페이지](media/active-directory-users-assign-role-azure-portal/directory-role-remove-role.png)

    애플리케이션 관리자 역할이 Alain Charon에서 제거되고 **Alain Charon - 관리 역할** 페이지에 더 이상 표시되지 않습니다.

## <a name="next-steps"></a>다음 단계

- [사용자 추가 또는 삭제](add-users-azure-active-directory.md)

- [프로필 정보 추가 또는 변경](active-directory-users-profile-azure-portal.md)

- [다른 디렉터리에서 게스트 사용자 추가](../external-identities/what-is-b2b.md)

체크 아웃할 수 있는 다른 사용자 관리 작업은 [Azure Active Directory 사용자 관리 설명서](../enterprise-users/index.yml)에서 확인할 수 있습니다.