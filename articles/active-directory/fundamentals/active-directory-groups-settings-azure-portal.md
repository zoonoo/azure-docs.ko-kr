---
title: Azure Active Directory를 사용하여 그룹 정보를 편집하는 방법 | Microsoft Docs
description: Azure Active Directory를 사용하여 그룹 정보를 편집하는 방법을 알아봅니다.
services: active-directory
author: eross-msft
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.component: fundamentals
ms.topic: conceptual
ms.date: 08/27/2018
ms.author: lizross
ms.reviewer: krbain
ms.custom: it-pro
ms.openlocfilehash: a02987fdce3a15cd5d416234e3717df6d33622ec
ms.sourcegitcommit: 1b561b77aa080416b094b6f41fce5b6a4721e7d5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/17/2018
ms.locfileid: "45731345"
---
# <a name="how-to-edit-your-group-information-using-azure-active-directory"></a>방법: Azure Active Directory를 사용하여 그룹 정보 편집

Azure Active Directory를 사용하여 이름, 설명 또는 멤버 자격 유형을 업데이트할 뿐 아니라 그룹 설정을 편집할 수 있습니다.

## <a name="to-edit-your-group-settings"></a>그룹 설정을 편집하려면
1. 해당 디렉터리에 대한 글로벌 관리자 계정을 사용하여 [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. **Azure Active Directory**를 선택한 후 **그룹**을 선택합니다.

    모든 활성 그룹을 보여 주는 **그룹 - 모든 그룹** 페이지가 나타납니다.

3. **그룹 - 모든 그룹** 페이지에서 **검색** 상자에 가능한 한 많은 그룹 이름을 입력합니다. 이 문서에서는 **MDM policy - West** 그룹을 검색하고 있습니다.

    검색 결과가 **검색** 상자 아래에 표시되고 추가 문자를 입력할 때 업데이트됩니다.

    ![검색 상자에 검색 텍스트가 있는 모든 그룹 페이지](media/active-directory-groups-settings-azure-portal/search-for-specific-group.png)

4. **MDM policy - West** 그룹을 선택한 후 **관리** 영역에서 **속성**을 선택합니다.

    ![멤버 수 및 멤버 옵션이 강조 표시된 그룹 개요 페이지](media/active-directory-groups-settings-azure-portal/group-overview-blade.png)

5. 다음을 포함하여 필요에 따라 **일반 설정** 정보를 업데이트합니다.

    ![그룹의 속성 설정](media/active-directory-groups-settings-azure-portal/group-properties-settings.png)

    - **그룹 이름.** 기존 그룹 이름을 편집합니다.
    
    - **그룹 설명.** 기존 그룹 설명을 편집합니다.

    - **그룹 유형.** 그룹이 생성된 후에는 그룹 유형을 변경할 수 없습니다. **그룹 유형**을 변경하려면 그룹을 삭제하고 새 그룹을 만들어야 합니다.
    
    - **멤버 자격 유형.** 멤버 자격 유형을 변경합니다. 다양한 사용 가능한 멤버 자격 유형에 대한 자세한 내용은 [방법: Azure Active Directory 포털을 사용하여 기본 그룹 만들기 및 멤버 추가](active-directory-groups-create-azure-portal.md)를 참조하세요.
    
    - **개체 ID.** 개체 ID를 변경할 수는 없지만, 개체 ID를 복사하여 그룹용 PowerShell 명령에서 사용할 수 있습니다. PowerShell cmdlet 사용에 대한 자세한 내용은 [그룹 설정 구성을 위한 Azure Active Directory cmdlet](../users-groups-roles/groups-settings-v2-cmdlets.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계
이러한 문서는 Azure Active Directory에 대한 추가 정보를 제공합니다.

- [그룹 및 멤버 보기](active-directory-groups-view-azure-portal.md)

- [기본 그룹 만들기 및 멤버 추가](active-directory-groups-create-azure-portal.md)

- [그룹에서 멤버를 추가 또는 제거하는 방법](active-directory-groups-members-azure-portal.md)

- [그룹의 사용자에 대한 동적 규칙 관리](../users-groups-roles/groups-create-rule.md)

- [그룹의 멤버 자격 관리](active-directory-groups-membership-azure-portal.md)

- [그룹을 사용하여 리소스에 대한 액세스 관리](active-directory-manage-groups.md)

- [Azure Active Directory에 Azure 구독 연결 또는 추가](active-directory-how-subscriptions-associated-directory.md)
