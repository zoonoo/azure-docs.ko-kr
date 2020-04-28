---
title: PIM - Azure Active Directory에서 Azure AD 역할 보안 마법사 | 마이크로 소프트 문서
description: Azure AD PIM(Privileged Identity Management)을 사용하여 영구 권한 있는 Azure AD 역할 할당을 적격 상태로 변환하는 데 사용할 수 있는 보안 마법사를 설명합니다.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 04/21/2020
ms.author: curtand
ms.custom: pim ; H1Hack27Feb2017
ms.collection: M365-identity-device-management
ms.openlocfilehash: a9f12b2b31da4c7fe67eef9674d96b517d4e2bfa
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81867745"
---
# <a name="azure-ad-roles-security-wizard-in-privileged-identity-management"></a>권한 있는 ID 관리에서 Azure AD 역할 보안 마법사

Azure Active Directory(Azure AD) 조직에서 PIM(권한 있는 ID 관리)을 처음 사용하는 사용자인 경우 시작할 마법사가 표시됩니다. 마법사는 권한 있는 ID의 보안 위험과 권한 있는 ID 관리를 사용하여 이러한 위험을 줄이는 방법을 이해하는 데 도움이 됩니다. 나중에 작업하려는 경우 마법사에서 기존 역할 할당을 변경할 필요가 없습니다.

> [!Important]
> 보안 마법사를 일시적으로 사용할 수 없습니다. 기다려 주셔서 감사합니다.

## <a name="wizard-overview"></a>마법사 개요

조직에서 권한 ID 관리를 사용하기 전에 모든 역할 할당은 영구적입니다. 마법사의 첫 번째 단계에서는 권한이 높은 역할의 목록과 얼마나 많은 사용자가 현재 해당 역할에 있는지를 보여 줍니다. 특정 역할을 자세히 알아보면 익숙하지 않은 사용자에 대해 더 잘 알 수 있습니다.

마법사의 두 번째 단계는 관리자의 역할 할당을 변경할 기회를 제공합니다.  

> [!WARNING]
> 하나 이상의 글로벌 관리자와 Microsoft 계정이 아닌 직장 또는 학교 계정이 있는 권한 있는 역할 관리자가 두 명 이상 있어야 합니다. 권한 있는 역할 관리자가 한 명만 있는 경우 해당 계정이 삭제된 경우 조직은 권한 있는 ID 관리를 관리할 수 없습니다.
> 또한 사용자가 Microsoft 계정(즉, Skype 및 Outlook.com 같은 Microsoft 서비스에 로그인하는 데 사용하는 계정)이 있는 경우 역할 할당을 영구적으로 유지합니다. 해당 역할의 활성화에 대해 다단계 인증을 요구하려는 경우 해당 사용자는 잠깁니다.

## <a name="run-the-wizard"></a>마법사 실행

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.

1. Azure **AD 권한 ID 관리**를 엽니다.

1. **Azure AD 역할을** 선택한 다음 **마법사를**선택합니다.

    ![Azure AD 역할 - 마법사를 실행하는 3단계를 보여주는 마법사 페이지](./media/pim-security-wizard/wizard-start.png)

1. **1 권한 있는 역할 검색.**

1. 권한 있는 역할 목록을 검토하여 어떤 사용자가 영구 또는 적격인지 확인합니다.

    ![권한 있는 역할 검색 - 영구 및 적격 회원을 표시하는 역할 창](./media/pim-security-wizard/discover-privileged-roles-users.png)

1. **다음을** 선택하여 적격으로 만들 사용자 또는 그룹을 선택합니다.

    ![역할을 수행할 구성원을 선택할 수 있는 옵션을 사용하여 멤버를 적격 페이지로 변환](./media/pim-security-wizard/convert-members-eligible.png)

1. 사용자 또는 그룹을 선택한 후 **다음**을 선택합니다.

    ![전환될 영구 역할 할당이 있는 구성원을 보여주는 변경 사항 페이지 검토](./media/pim-security-wizard/review-changes.png)

1. 영구 과제를 적격으로 변환하려면 **확인을** 선택합니다.

    변환이 완료되면 알림이 표시됩니다.

    ![전환 상태를 표시하는 알림](./media/pim-security-wizard/notification-completion.png)

다른 권한 있는 역할 할당을 적격으로 변환하려면 마법사를 다시 실행하면 됩니다. 마법사 대신 권한 ID 관리 인터페이스를 사용하려면 [권한 있는 ID 관리에서 Azure AD 역할 할당을](pim-how-to-add-role-to-user.md)참조하세요.

## <a name="next-steps"></a>다음 단계

- [권한 있는 ID 관리에서 Azure AD 역할 할당](pim-how-to-add-role-to-user.md)
- [권한 있는 ID 관리를 관리 하기 위해 다른 관리자에 대 한 액세스 권한을 부여 합니다.](pim-how-to-give-access-to-pim.md)
