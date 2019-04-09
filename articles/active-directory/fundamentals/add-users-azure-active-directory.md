---
title: 사용자 추가 또는 삭제 - Azure Active Directory | Microsoft Docs
description: Azure Active Directory를 사용하여 새 사용자를 추가하거나 기존 사용자를 삭제하는 방법에 대한 지침입니다.
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 04/01/2019
ms.author: lizross
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: c1bac4d2c0f236b8fca611c7391846abdb782796
ms.sourcegitcommit: e43ea344c52b3a99235660960c1e747b9d6c990e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/04/2019
ms.locfileid: "59009600"
---
# <a name="add-or-delete-users-using-azure-active-directory"></a>Azure Active Directory를 사용한 사용자 추가 또는 삭제
새 사용자를 추가 하거나 Azure Active Directory (Azure AD) 조직에서 기존 사용자를 삭제 합니다.

## <a name="add-a-new-user"></a>새 사용자 추가
Azure Active Directory 포털을 사용하여 새 사용자를 만들 수 있습니다.

### <a name="to-add-a-new-user"></a>새 사용자를 추가하려면
1. 에 로그인 합니다 [Azure portal](https://portal.azure.com/) 조직에 대 한 사용자 관리자입니다.

2. **Azure Active Directory**를 선택하고, **사용자**를 선택한 다음, **새 사용자**를 선택합니다.

    ![사용자 - 새 사용자가 강조 표시된 모든 사용자 페이지](media/add-users-azure-active-directory/new-user-all-users-blade.png)

3. **사용자** 페이지에서 필요한 정보를 입력합니다.

    ![새 사용자 추가, 사용자 정보가 있는 사용자 페이지](media/add-users-azure-active-directory/new-user-user-blade.png)

   - **이름 (필수)입니다.** 새 사용자의 이름 및 성입니다. 예를 들어, Mary Parker입니다.

   - **사용자 이름 (필수)입니다.** 새 사용자의 사용자 이름입니다. 예: mary@contoso.com
    
       사용자 이름의 도메인 이름 부분은 초기 기본 도메인 이름 <_yourdomainname_>.onmicrosoft.com이거나 contoso.com과 같은 사용자 지정 도메인 이름을 사용해야 합니다. 사용자 지정 도메인 이름 작성 방법에 대한 자세한 내용은 [Azure Active Directory에 사용자 지정 도메인 이름을 추가하는 방법](add-custom-domain.md)을 참조하세요.

   - **프로필입니다.** 원할 경우 사용자에 대한 자세한 정보를 추가할 수 있습니다. 사용자 정보를 나중에 추가할 수도 있습니다. 사용자 정보 추가에 대한 자세한 내용은 [사용자 프로필 정보를 추가하거나 변경하는 방법](active-directory-users-profile-azure-portal.md)을 참조하세요.

   - **그룹입니다.** 원할 경우, 하나 이상의 기존 그룹에 사용자를 추가할 수 있습니다. 사용자를 나중에 그룹에 추가할 수도 있습니다. 사용자를 그룹에 추가하는 방법에 대한 자세한 내용은 [기본 그룹을 만들고 멤버를 추가하는 방법](active-directory-groups-create-azure-portal.md)을 참조하세요.

   - **디렉터리 역할입니다.** 필요에 따라 Azure AD 관리자 역할에 사용자를 추가할 수 있습니다. 사용자가 제한 된 관리자 역할의 전역 관리자 또는 하나 이상의 Azure AD에서 할당할 수 있습니다. 역할 할당에 대한 자세한 내용은 [사용자에게 역할을 할당하는 방법](active-directory-users-assign-role-azure-portal.md)을 참조하세요.

4. **암호** 상자에 제공된 자동 생성된 암호를 복사합니다. 초기 로그인 프로세스를 수행하려면 사용자에게 이 암호를 제공해야 합니다.

5. **만들기**를 선택합니다.

    사용자가 만들어지고 Azure AD 테넌트에 추가됩니다.

## <a name="add-a-new-user-within-a-hybrid-environment"></a>하이브리드 환경 내에 새 사용자 추가
Azure Active Directory(클라우드)와 Windows Server Active Directory(온-프레미스)를 모두 사용하는 환경이 있는 경우 기존 사용자 계정 데이터를 동기화하여 새 사용자를 추가할 수 있습니다. 하이브리드 환경에 대한 자세한 내용은 [Azure Active Directory와 온-프레미스 디렉터리 통합](../hybrid/whatis-hybrid-identity.md)을 참조하세요.

## <a name="delete-a-user"></a>사용자 삭제
Azure Active Directory 포털을 사용하여 기존 사용자를 삭제할 수 있습니다.

### <a name="to-delete-a-user"></a>사용자를 삭제하려면
1. 에 로그인 합니다 [Azure portal](https://portal.azure.com/) 조직에 대 한 사용자 관리자 계정을 사용 합니다.

2. **Azure Active Directory**를 선택하고 **사용자**를 선택한 다음, Azure AD 테넌트에서 삭제할 사용자를 검색하여 선택합니다. 예를 들어, _Mary Parker_입니다.

3. **사용자 삭제**를 선택합니다.

    ![사용자 - 사용자 삭제가 강조 표시된 모든 사용자 페이지](media/add-users-azure-active-directory/delete-user-all-users-blade.png)

    사용자가 삭제되고 **사용자 - 모든 사용자** 페이지에 더 이상 표시되지 않습니다. 이 사용자는 다음 30일 동안 **삭제된 사용자** 페이지에서 볼 수 있으며 해당 기간 복원할 수 있습니다. 사용자 복원에 대한 자세한 내용은 [How to restore or permanently remove a recently deleted user](active-directory-users-restore.md)(최근에 삭제된 사용자를 복원하거나 영구적으로 제거하는 방법)를 참조하세요. 사용자 삭제 되 면 사용자에 의해 소비 되는 모든 라이선스를 사용할 수 있는 다른 사용자에 대 한 제공 됩니다.

    >[!Note]
    >Windows Server Active Directory를 사용하여 인증 원본이 Windows Server Active Directory인 사용자의 ID, 연락처 정보 또는 작업 정보를 업데이트해야 합니다. 업데이트를 완료하면 변경 내용이 표시되기 전에 다음 동기화 주기가 완료될 때까지 기다려야 합니다.

## <a name="next-steps"></a>다음 단계

사용자를 추가한 후 다음 기본 프로세스를 수행할 수 있습니다.

- [프로필 정보 추가 또는 변경](active-directory-users-profile-azure-portal.md)

- [사용자에게 역할 할당](active-directory-users-assign-role-azure-portal.md)

- [기본 그룹 만들기 및 멤버 추가](active-directory-groups-create-azure-portal.md)

- [동적 그룹 및 사용자 작업](../users-groups-roles/groups-create-rule.md)

또는 [adding guest users from another directory](../b2b/what-is-b2b.md)(다른 디렉터리의 게스트 사용자 추가) 또는 [restoring a deleted user](active-directory-users-restore.md)(삭제된 사용자 복원)와 같은 다른 사용자 관리 작업을 수행할 수 있습니다. 사용 가능한 다른 작업에 대한 자세한 내용은 [Azure Active Directory 사용자 관리 설명서](../users-groups-roles/index.yml)를 참조하세요.
