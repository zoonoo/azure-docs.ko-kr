---
title: Azure Active Directory를 사용하여 사용자에게 디렉터리 역할을 할당하는 방법 | Microsoft Docs
description: Azure Active Directory를 사용하여 사용자에게 디렉터리 역할을 할당하는 방법을 알아봅니다.
services: active-directory
author: eross-msft
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.component: fundamentals
ms.topic: conceptual
ms.date: 09/06/2018
ms.author: lizross
ms.reviewer: jeffsta
ms.openlocfilehash: b73df5ec0381e83c54c8cd9f8c0335448def0c6d
ms.sourcegitcommit: 1b561b77aa080416b094b6f41fce5b6a4721e7d5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/17/2018
ms.locfileid: "45733045"
---
# <a name="how-to-assign-roles-and-administrators-to-users-with-azure-active-directory"></a>방법: Azure Active Directory를 사용하여 사용자에게 역할 및 관리자 할당
조직의 사용자에게 Azure AD(Azure Active Directory) 리소스를 관리할 권한이 필요한 경우 사용자가 해당 권한으로 수행할 작업에 따라 Azure AD에서 사용자에게 적절한 역할을 할당해야 합니다.

사용 가능한 역할에 대한 자세한 내용은 [Azure Active Directory에서 관리자 역할 할당](../users-groups-roles/directory-assign-admin-roles.md)을 참조하세요. 사용자를 추가하는 방법에 대한 자세한 내용은 [Azure Active Directory에 새 사용자 추가](add-users-azure-active-directory.md)를 참조하세요.

## <a name="assign-roles"></a>역할 할당
Azure AD 역할을 사용자에게 할당하는 일반적인 방법은 사용자의 **디렉터리 역할** 페이지에 있습니다.

PIM(Privileged Identity Management)을 사용하여 역할을 할당할 수도 있습니다. PIM을 사용하는 방법에 대한 자세한 내용은 [Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management)를 참조하세요.

### <a name="to-assign-a-role-to-a-user"></a>사용자에게 역할을 할당하려면
1. 해당 디렉터리에 대한 글로벌 관리자 계정을 사용하여 [Azure Portal](https://portal.azure.com/)에 로그인합니다.

2. **Azure Active Directory**를 선택하고, **사용자**를 선택한 후, 역할 할당을 받을 사용자를 검색하여 선택합니다. 예를 들면 _Alain Charon_이 있습니다.

3. **Alain Charon - 프로필** 페이지에서 **디렉터리 역할**을 선택합니다.

    **Alain Charon - 디렉터리 역할** 페이지가 표시됩니다.

4. **역할 추가**를 선택하고, Alain에 할당할 역할(예: ‘응용 프로그램 관리자’)을 선택한 후, **선택**을 선택합니다.

    ![선택한 역할을 보여 주는 디렉터리 역할 페이지](media/active-directory-users-assign-role-azure-portal/directory-role-select-role.png)

    응용 프로그램 관리자 역할이 Alain Charon에 할당되고 **Alain Charon - 디렉터리 역할** 페이지에 표시됩니다.

## <a name="remove-a-role-assignment"></a>역할 할당 제거
사용자에서 역할 할당을 제거해야 할 경우 **Alain Charon - 디렉터리 역할** 페이지에서 제거할 수도 있습니다.

### <a name="to-remove-a-role-assignment-from-a-user"></a>사용자에서 역할 할당을 제거하려면

1. **Azure Active Directory**를 선택하고, **사용자**를 선택한 후, 역할 할당을 제거할 사용자를 검색하여 선택합니다. 예를 들면 _Alain Charon_이 있습니다.

2. **디렉터리 역할**을 선택하고, **응용 프로그램 관리자**를 선택한 후, **역할 제거**를 선택합니다.

    ![선택한 역할 및 제거 옵션을 보여 주는 디렉터리 역할 페이지](media/active-directory-users-assign-role-azure-portal/directory-role-remove-role.png)

    응용 프로그램 관리자 역할이 Alain Charon에서 제거되고 **Alain Charon - 디렉터리 역할** 페이지에 더 이상 표시되지 않습니다.

## <a name="next-steps"></a>다음 단계
- [사용자 추가 또는 삭제](add-users-azure-active-directory.md)

- [프로필 정보 추가 또는 변경](active-directory-users-profile-azure-portal.md)

- [다른 디렉터리에서 게스트 사용자 추가](../b2b/what-is-b2b.md)

또는 위임 할당, 정책 사용 및 사용자 계정 공유와 같은 다른 사용자 관리 작업을 수행할 수 있습니다. 사용 가능한 다른 작업에 대한 자세한 내용은 [Azure Active Directory 사용자 관리 설명서](../users-groups-roles/index.yml)를 참조하세요.


