---
title: 사용자의 프로필 정보 추가 또는 업데이트 - Azure Active Directory | Microsoft Docs
description: 사진 및 작업 세부 정보를 포함하여 Azure Active Directory에서 사용자의 프로필에 정보를 추가하는 방법에 대한 지침입니다.
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 04/11/2019
ms.author: lizross
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8d710a86bb63765ea8a1a777818ca5f99e38d3a7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60248126"
---
# <a name="add-or-update-a-users-profile-information-using-azure-active-directory"></a>Azure Active Directory를 사용하여 사용자 프로필 정보 추가 또는 업데이트
프로필 사진, 작업 관련 정보 및 Azure AD(Azure Active Directory)를 사용하는 일부 설정을 비롯하여 사용자 프로필 정보를 추가합니다. 새 사용자를 추가하는 방법에 대한 자세한 내용은 [Azure Active Directory에서 사용자를 추가 또는 삭제하는 방법](add-users-azure-active-directory.md)을 참조하세요.

## <a name="add-or-change-profile-information"></a>프로필 정보 추가 또는 변경
앞으로 살펴보겠지만 사용자를 만드는 동안 추가할 수 있는 것보다 많은 정보를 사용자 프로필에서 사용할 수 있습니다. 이 추가 정보는 모두 선택 사항이며 조직에서 필요에 따라 추가할 수 있습니다.

## <a name="to-add-or-change-profile-information"></a>프로필 정보를 추가 또는 변경하려면
1. 에 로그인 합니다 [Azure portal](https://portal.azure.com/) 조직에 대 한 사용자 관리자입니다.

2. **Azure Active Directory**를 선택하고, **사용자**를 선택한 다음, 사용자를 선택합니다. 예를 들면 _Alain Charon_이 있습니다.

    **Alain Charon - 프로필** 페이지가 표시됩니다.

    ![편집 가능한 정보가 포함된 사용자 프로필 페이지](media/active-directory-users-profile-azure-portal/user-profile-all-blade.png)

3. **편집**을 선택하여 사용 가능한 각 섹션에 포함된 정보를 선택적으로 추가하거나 업데이트합니다.

    ![편집 가능한 영역을 보여 주는 사용자 프로필 페이지](media/active-directory-users-profile-azure-portal/user-profile-edit.png)

    - **프로필 사진.** 사용자 계정의 썸네일 이미지를 선택합니다. 이 사진은 Azure Active Directory 및 사용자의 개인 페이지(예: myapps.microsoft.com 페이지)에 나타납니다.

    - **ID.** 추가 하거나 마지막으로 이름과 같은 사용자에 대 한 추가 id 값을 업데이트 합니다. 첫 번째 이름과 마지막 이름 값에서이 이름은 독립적으로 설정할 수 있습니다. 예를 들어, 회사 이름, 이니셜을 포함 하거나 표시 하는 이름의 시퀀스를 변경 하려면를 사용할 수 있습니다. 또 다른 예로, ' Chris Green' 이름은 두 사용자가 사용 하 여 Id 문자열 ' Chris B. Green' 'Chris R. 녹색 (Contoso)입니다.'를 해당 이름을 설정 하려면

    - **작업 정보.** 사용자의 직책, 부서 또는 관리자와 같은 작업 관련 정보를 추가합니다.

    - **설정.** 사용자가 Azure Active Directory 테넌트에 로그인할 수 있는지 여부를 결정합니다. 사용자의 전역 위치를 지정할 수도 있습니다.

    - **연락처 정보.** 사용자의 관련 연락처 정보를 추가합니다. 예를 들면 주소 또는 휴대폰 번호가 있습니다.

    - **인증 연락처 정보.** 이 정보를 확인하여 사용자의 활성 전화 번호와 메일 주소가 있는지 확인합니다. 이 정보는 Azure Active Directory에서 로그인 중인 사용자가 실제로 사용자인지 확인하는 데 사용됩니다. 인증 연락처 정보는 전역 관리자만 업데이트할 수 있습니다.

4. **저장**을 선택합니다.

    사용자에 대한 모든 변경 내용이 저장됩니다.

    >[!Note]
    >Windows Server Active Directory를 사용하여 인증 원본이 Windows Server Active Directory인 사용자의 ID, 연락처 정보 또는 작업 정보를 업데이트해야 합니다. 업데이트를 완료하면 변경 내용이 표시되기 전에 다음 동기화 주기가 완료될 때까지 기다려야 합니다.

## <a name="next-steps"></a>다음 단계
사용자 프로필을 업데이트한 후 다음 기본 프로세스를 수행할 수 있습니다.

- [사용자 추가 또는 삭제](add-users-azure-active-directory.md)

- [사용자에게 역할 할당](active-directory-users-assign-role-azure-portal.md)

- [기본 그룹 만들기 및 멤버 추가](active-directory-groups-create-azure-portal.md)

또는 위임 할당, 정책 사용 및 사용자 계정 공유와 같은 다른 사용자 관리 작업을 수행할 수 있습니다. 사용 가능한 다른 작업에 대한 자세한 내용은 [Azure Active Directory 사용자 관리 설명서](../users-groups-roles/index.yml)를 참조하세요.
