---
title: Azure Active Directory를 사용하여 그룹 멤버를 추가하거나 제거하는 방법 | Microsoft Docs
description: Azure Active Directory를 사용하여 그룹에서 사용자 및 장치를 추가하거나 제거하는 방법을 알아봅니다.
services: active-directory
author: eross-msft
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.component: fundamentals
ms.topic: conceptual
ms.date: 08/23/2018
ms.author: lizross
ms.custom: it-pro
ms.reviewer: krbain
ms.openlocfilehash: 7c1a83d83dcbf247550c66602a6f53d4ef0d7930
ms.sourcegitcommit: 1b561b77aa080416b094b6f41fce5b6a4721e7d5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/17/2018
ms.locfileid: "45733367"
---
# <a name="how-to-add-or-remove-group-members-using-azure-active-directory"></a>방법: Azure Active Directory를 사용하여 그룹 멤버 추가 또는 제거
Azure Active Directory를 사용하여 그룹 멤버를 계속 추가하거나 제거할 수 있습니다.

## <a name="to-add-group-members"></a>그룹 멤버를 추가하려면

1. 해당 디렉터리에 대한 글로벌 관리자 계정을 사용하여 [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. **Azure Active Directory**를 선택한 다음, **그룹**을 선택합니다.

3. **그룹 - 모든 그룹** 페이지에서 멤버를 추가할 그룹을 검색하여 선택합니다. 이 경우에 이전에 만든 그룹인 **MDM 정책 - 서부**를 사용합니다.

    ![그룹 - 그룹 이름이 강조 표시된 모든 그룹 페이지](media/active-directory-groups-members-azure-portal/group-all-groups-screen.png)

4. **MDM 정책 - 서부 개요** 페이지의 **관리** 영역에서 **멤버**를 선택합니다.

    ![멤버 옵션이 강조 표시된 MDM 정책 - 서부 개요 페이지](media/active-directory-groups-members-azure-portal/group-overview-blade.png)

5. **멤버 추가**를 선택하고, 그룹에 추가할 멤버를 각각 검색하고 선택한 다음, **선택**을 선택합니다.

    멤버를 성공적으로 추가했다는 메시지가 표시됩니다.

    ![멤버에 대한 검색이 표시된 멤버 추가 페이지](media/active-directory-groups-members-azure-portal/update-members.png)

6. 그룹에 추가된 모든 멤버 이름을 보려면 화면을 새로 고칩니다.

## <a name="to-remove-group-members"></a>그룹 멤버를 제거하려면

1. **그룹 - 모든 그룹** 페이지에서 멤버를 제거할 그룹을 검색하여 선택합니다. 다시 **MDM 정책 - 서부**를 사용합니다.

2. **관리** 영역에서 **멤버**를 선택하고, 제거할 멤버의 이름을 검색하여 선택한 다음, **제거**를 선택합니다.

    ![제거 옵션을 포함한 멤버 정보 페이지](media/active-directory-groups-members-azure-portal/remove-members-from-group.png)

## <a name="next-steps"></a>다음 단계

- [그룹 및 멤버 보기](active-directory-groups-view-azure-portal.md)

- [그룹 설정 편집](active-directory-groups-settings-azure-portal.md)

- [그룹을 사용하여 리소스에 대한 액세스 관리](active-directory-manage-groups.md)

- [그룹의 사용자에 대한 동적 규칙 관리](../users-groups-roles/groups-create-rule.md)

- [Azure Active Directory에 Azure 구독 연결 또는 추가](active-directory-how-subscriptions-associated-directory.md)
