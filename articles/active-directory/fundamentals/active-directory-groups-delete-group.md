---
title: 그룹 삭제 - Azure Active Directory | Microsoft Docs
description: Azure Active Directory를 사용하여 그룹을 삭제하는 방법에 대한 지침입니다.
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 08/29/2018
ms.author: lizross
ms.reviewer: krbain
ms.custom: it-pro, seodec18
ms.openlocfilehash: 61f6d68e873b6c8b8b901e5e065065ed42513572
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/28/2019
ms.locfileid: "55100210"
---
# <a name="delete-a-group-using-azure-active-directory"></a>Azure Active Directory를 사용하여 그룹 삭제
여러 가지 이유로 Azure AD(Azure Active Directory)그룹을 삭제할 수 있지만, 일반적으로 이유는 다음과 같습니다.

- **그룹 유형**을 잘못된 옵션으로 설정함

- 실수로 잘못된 그룹 또는 중복 그룹을 생성함 

- 그룹이 더 이상 필요하지 않음

## <a name="to-delete-a-group"></a>그룹 삭제
1. 해당 디렉터리에 대한 글로벌 관리자 계정을 사용하여 [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. **Azure Active Directory**를 선택한 후 **그룹**을 선택합니다.

3. **그룹 - 모든 그룹** 페이지에서 삭제할 그룹을 검색하여 선택합니다. 이 단계에 **MDM policy - East**를 사용하겠습니다.

    ![그룹-모든 그룹 페이지, 그룹 이름 강조 표시됨](media/active-directory-groups-delete-group/group-all-groups-screen.png)

4. **MDM policy - East 개요** 페이지에서 **삭제**를 선택합니다.

    그룹이 Azure Active Directory 테넌트에서 삭제됩니다.

    ![MDM policy - East 개요 페이지, 삭제 옵션 강조 표시됨](media/active-directory-groups-delete-group/group-overview-blade.png)

## <a name="next-steps"></a>다음 단계

- 그룹을 실수로 삭제한 경우에는 다시 만들 수 있습니다. 자세한 내용은 [기본 그룹을 만들고 멤버를 추가하는 방법](active-directory-groups-create-azure-portal.md)을 참조하세요.

- Office 365 그룹을 실수로 삭제한 경우에는 복원할 수 있습니다. 자세한 내용은 [삭제된 Office 365 그룹 복원](../users-groups-roles/groups-restore-deleted.md)을 참조하세요.
