---
title: 관리자 역할 사용 권한 보기 및 할당 - Azure Active Directory | Microsoft Docs
description: 이제 포털에서 Azure AD 관리자 역할의 멤버를 보고 관리할 수 있습니다. 역할 할당을 자주 관리하는 사용자를 위한 것입니다.
services: active-directory
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 01/31/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 20842c8c644a351b59322569661abf82bb524449
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60472592"
---
# <a name="view-and-assign-administrator-roles-in-azure-active-directory"></a>Azure Active Directory에서 관리자 역할 보기 및 할당

이제 Azure Active Directory 포털에서 관리자 역할의 모든 멤버를 확인하고 관리할 수 있습니다. 역할 할당을 자주 관리하는 경우, 이 환경이 유용할 것입니다. 그리고 "이러한 역할이 정말로 무엇을 수행할까?"라고 궁금하다면 Azure AD 관리자 역할에 대한 자세한 권한 목록을 확인할 수 있습니다.

## <a name="view-all-roles"></a>모든 역할 보기

Azure Active Directory에서 **역할 및 관리자**를 선택하여 사용 가능한 역할 목록을 봅니다. 

각 행의 오른쪽에 있는 줄임표를 클릭하여 역할에 대한 자세한 설명을 엽니다.

![Azure AD 포털의 역할 목록](./media/directory-manage-roles-portal/role-list.png)

## <a name="view-my-roles"></a>내 역할 보기

자신의 권한도 쉽게 확인할 수 있습니다. **역할 및 관리자** 페이지에서 **내 역할**을 선택하면 현재 나에게 할당된 역할을 볼 수 있습니다.

## <a name="view-assignments-for-a-role"></a>역할 할당 보기

역할을 클릭하면 역할에 할당된 사용자를 볼 수 있습니다. 추가 관리 기능을 위해 **PIM에서 관리**를 선택할 수 있습니다. 권한 있는 역할 관리자는 “영구적”(역할에서 항상 활성 상태임) 할당을 “적격”(상승된 경우에만 역할에 포함)으로 변경할 수 있습니다. PIM이 없는 경우, **PIM에서 관리**를 선택하여 평가판에 등록할 수 있습니다. Privileged Identity Management에는 [Azure AD Premium P2 라이선스 계획](../privileged-identity-management/subscription-requirements.md)이 필요합니다.

![관리자 역할의 멤버 목록](./media/directory-manage-roles-portal/member-list.png)

글로벌 관리자 또는 권한 있는 역할 관리자인 경우 멤버를 쉽게 추가 또는 제거하거나, 목록을 필터링하거나, 멤버를 선택하여 할당된 활성 역할을 확인할 수 있습니다.

## <a name="view-a-users-role-permissions"></a>사용자의 역할 권한 보기

역할의 멤버를 보고 있는 경우 **설명**을 선택하여 역할 할당에서 부여된 권한의 전체 목록을 확인합니다. 이 페이지에는 디렉터리 역할을 관리하는 데 도움이 되는 관련 설명서에 대한 링크가 포함되어 있습니다.

![관리 역할에 대한 권한 목록](./media/directory-manage-roles-portal/role-description.png)


## <a name="next-steps"></a>다음 단계

* [Azure AD 관리 역할 포럼](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032)에서 경험을 자유롭게 공유하세요.
* 역할 및 관리자 역할 할당에 대한 자세한 내용은 [관리자 역할 할당](directory-assign-admin-roles.md)을 참조하세요.
* 기본 사용자 권한의 경우 [기본 게스트 및 멤버 사용자 권한 비교](../fundamentals/users-default-permissions.md)를 참조하세요.
