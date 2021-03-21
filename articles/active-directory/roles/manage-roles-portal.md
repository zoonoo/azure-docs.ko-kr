---
title: 사용자에 게 Azure AD 역할 할당-Azure Active Directory
description: Azure AD 역할을 할당 하 여 Azure Active Directory에서 사용자에 게 액세스 권한을 부여 하는 방법을 알아봅니다.
services: active-directory
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: how-to
ms.date: 03/07/2021
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 36ced586db1b4e417e623431c137c43dac8ba56f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103466647"
---
# <a name="assign-azure-ad-roles-to-users"></a>사용자에 게 Azure AD 역할 할당

이제 Azure AD 관리 센터에서 관리자 역할의 모든 멤버를 보고 관리할 수 있습니다. 역할 할당을 자주 관리하는 경우, 이 환경이 유용할 것입니다. 이 문서에서는 Azure AD 관리 센터를 사용 하 여 Azure AD 역할을 할당 하는 방법을 설명 합니다.

## <a name="assign-a-role"></a>역할 할당

1. 전역 관리자 또는 권한 있는 역할 관리자 권한으로 [AZURE AD 관리 센터](https://aad.portal.azure.com) 에 로그인 합니다.

1. **Azure Active Directory** 를 선택합니다.

1. **역할 및 관리자** 를 선택 하 여 사용 가능한 모든 역할 목록을 표시 합니다.

    ![역할 및 관리자 페이지의 스크린샷](./media/manage-roles-portal/roles-and-administrators.png)

1. 역할을 선택 하 여 해당 할당을 확인 합니다.

    필요한 역할을 찾을 수 있도록 Azure AD는 역할 범주에 따라 역할의 하위 집합을 표시할 수 있습니다. 선택한 유형의 역할만 표시 하도록 **유형** 필터를 체크 아웃 합니다.

1. **할당 추가** 를 선택한 다음이 역할에 할당 하려는 사용자를 선택 합니다.

    다음 그림과 다른 내용이 표시 되 면 pim [(Privileged Identity Management)](#privileged-identity-management-pim) 의 참고 사항을 읽어 pim을 사용 하 고 있는지 확인 합니다.

    ![관리 역할에 대한 권한 목록](./media/manage-roles-portal/add-assignments.png)

1. **추가** 를 선택 하 여 역할을 할당 합니다.

## <a name="privileged-identity-management-pim"></a>PIM(Privileged Identity Management)

[Azure AD Privileged Identity Management (pim)](../privileged-identity-management/pim-configure.md)를 사용 하 여 추가 관리 기능에 대해 **pim에서 관리** 를 선택할 수 있습니다. 권한 있는 역할 관리자는 “영구적”(역할에서 항상 활성 상태임) 할당을 “적격”(상승된 경우에만 역할에 포함)으로 변경할 수 있습니다. Privileged Identity Management 없는 경우에도 **PIM에서 관리** 를 선택 하 여 평가판에 등록할 수 있습니다. Privileged Identity Management에는 [Azure AD Premium P2 라이선스 계획](../privileged-identity-management/subscription-requirements.md)이 필요합니다.

!["PIM에서 관리" 작업이 선택 된 "사용자 관리자-할당" 페이지를 보여 주는 스크린샷](./media/manage-roles-portal/member-list-pim.png)

글로벌 관리자 또는 권한 있는 역할 관리자인 경우 멤버를 쉽게 추가 또는 제거하거나, 목록을 필터링하거나, 멤버를 선택하여 할당된 활성 역할을 확인할 수 있습니다.

> [!Note]
> Azure AD premium P2 라이선스가 있고 이미 Privileged Identity Management를 사용 하는 경우 모든 역할 관리 작업은 Azure AD가 아닌 권한 Id 관리에서 수행 됩니다.
>
> ![이미 PIM을 사용 하 고 프리미엄 P2 라이선스가 있는 사용자를 위해 PIM에서 관리 되는 Azure AD 역할](./media/manage-roles-portal/pim-manages-roles-for-p2.png)

## <a name="next-steps"></a>다음 단계

* [Azure AD 관리 역할 포럼](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032)에서 경험을 자유롭게 공유하세요.
* 역할에 대 한 자세한 내용은 [AZURE AD 기본 제공 역할](permissions-reference.md)을 참조 하세요.
* 기본 사용자 권한의 경우 [기본 게스트 및 멤버 사용자 권한 비교](../fundamentals/users-default-permissions.md)를 참조하세요.
