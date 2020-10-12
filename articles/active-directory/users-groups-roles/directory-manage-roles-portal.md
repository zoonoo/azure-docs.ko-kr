---
title: 관리자 역할 권한 보기 및 할당-Azure AD | Microsoft Docs
description: 이제 포털에서 Azure AD 관리자 역할의 멤버를 보고 관리할 수 있습니다. 역할 할당을 자주 관리하는 사용자를 위한 것입니다.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: how-to
ms.date: 06/15/2020
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: e548e99cc60d67b477fd087b993764bf7f223592
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91541189"
---
# <a name="view-and-assign-administrator-roles-in-azure-active-directory"></a>Azure Active Directory에서 관리자 역할 보기 및 할당

이제 Azure Active Directory 포털에서 관리자 역할의 모든 멤버를 확인하고 관리할 수 있습니다. 역할 할당을 자주 관리하는 경우, 이 환경이 유용할 것입니다. 그리고 "이러한 역할이 정말로 무엇을 수행할까?"라고 궁금하다면 Azure AD 관리자 역할에 대한 자세한 권한 목록을 확인할 수 있습니다.

## <a name="view-all-roles"></a>모든 역할 보기

1. [Azure Portal](https://portal.azure.com) 에 로그인 하 고 **Azure Active Directory**을 선택 합니다.

1. **역할 및 관리자** 를 선택 하 여 사용 가능한 모든 역할 목록을 표시 합니다.

1. 각 행의 오른쪽에 있는 줄임표 (...)를 선택 하 여 역할에 대 한 사용 권한을 확인 합니다. 역할에 할당 된 사용자를 보려면 역할을 선택 합니다. 다음 그림과 다른 내용이 표시 되는 경우 [권한 있는 역할에 대 한 할당 보기](#view-assignments-for-privileged-roles) 의 메모를 읽고 PRIVILEGED IDENTITY MANAGEMENT (PIM)에 있는지 여부를 확인 합니다.

    ![Azure AD 포털의 역할 목록](./media/directory-manage-roles-portal/view-roles-in-azure-active-directory.png)

## <a name="view-my-roles"></a>내 역할 보기

자신의 권한도 쉽게 확인할 수 있습니다. **역할 및 관리자** 페이지에서 **내 역할**을 선택하면 현재 나에게 할당된 역할을 볼 수 있습니다.

## <a name="view-assignments-for-privileged-roles"></a>권한 있는 역할에 대 한 할당 보기

추가 관리 기능을 위해 **PIM에서 관리**를 선택할 수 있습니다. 권한 있는 역할 관리자는 “영구적”(역할에서 항상 활성 상태임) 할당을 “적격”(상승된 경우에만 역할에 포함)으로 변경할 수 있습니다. Privileged Identity Management 없는 경우에도 **PIM에서 관리** 를 선택 하 여 평가판에 등록할 수 있습니다. Privileged Identity Management에는 [Azure AD Premium P2 라이선스 계획](../privileged-identity-management/subscription-requirements.md)이 필요합니다.

![관리자 역할의 멤버 목록](./media/directory-manage-roles-portal/member-list.png)

글로벌 관리자 또는 권한 있는 역할 관리자인 경우 멤버를 쉽게 추가 또는 제거하거나, 목록을 필터링하거나, 멤버를 선택하여 할당된 활성 역할을 확인할 수 있습니다.

> [!Note]
> Azure AD premium P2 라이선스가 있고 이미 Privileged Identity Management를 사용 하는 경우 모든 역할 관리 작업은 Azure AD가 아닌 권한 Id 관리에서 수행 됩니다.
>
> ![이미 PIM을 사용 하 고 프리미엄 P2 라이선스가 있는 사용자를 위해 PIM에서 관리 되는 Azure AD 역할](./media/directory-manage-roles-portal/pim-manages-roles-for-p2.png)

## <a name="view-a-users-role-permissions"></a>사용자의 역할 권한 보기

역할의 멤버를 보고 있는 경우 **설명**을 선택하여 역할 할당에서 부여된 권한의 전체 목록을 확인합니다. 이 페이지에는 디렉터리 역할을 관리하는 데 도움이 되는 관련 설명서에 대한 링크가 포함되어 있습니다.

!["전역 관리자-설명" 페이지를 보여 주는 스크린샷](./media/directory-manage-roles-portal/role-description.png)

## <a name="download-role-assignments"></a>역할 할당 다운로드

특정 역할에 대 한 모든 할당을 다운로드 하려면 역할 **및 관리자** 페이지에서 역할을 선택한 다음 **역할 할당 다운로드**를 선택 합니다. 해당 역할의 모든 범위에서 할당을 나열 하는 CSV 파일이 다운로드 됩니다.

![역할에 대 한 모든 할당 다운로드](./media/directory-manage-roles-portal/download-role-assignments.png)

## <a name="assign-a-role"></a>역할 할당

1. 전역 관리자 또는 권한 있는 역할 관리자 권한으로 [Azure Portal](https://portal.azure.com) 에 로그인 하 고 **Azure Active Directory**을 선택 합니다.

1. **역할 및 관리자** 를 선택 하 여 사용 가능한 모든 역할 목록을 표시 합니다.

1. 역할을 선택 하 여 해당 할당을 확인 합니다.

    !["PIM에서 관리" 작업이 선택 된 "사용자 관리자-할당" 페이지를 보여 주는 스크린샷](./media/directory-manage-roles-portal/member-list.png)

1. 할당 **추가** 를 선택 하 고 할당 하려는 역할을 선택 합니다. 추가 관리 기능을 위해 **PIM에서 관리**를 선택할 수 있습니다. 다음 그림과 다른 내용이 표시 되는 경우 [권한 있는 역할에 대 한 할당 보기](#view-assignments-for-privileged-roles) 에서 참고 사항을 읽어 PIM에 있는지 확인 합니다.

    ![관리 역할에 대한 권한 목록](./media/directory-manage-roles-portal/directory-role-select-role.png)

## <a name="next-steps"></a>다음 단계

* [Azure AD 관리 역할 포럼](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032)에서 경험을 자유롭게 공유하세요.
* 역할 및 관리자 역할 할당에 대한 자세한 내용은 [관리자 역할 할당](directory-assign-admin-roles.md)을 참조하세요.
* 기본 사용자 권한의 경우 [기본 게스트 및 멤버 사용자 권한 비교](../fundamentals/users-default-permissions.md)를 참조하세요.
