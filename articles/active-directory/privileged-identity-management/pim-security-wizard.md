---
title: PIM의 Azure AD 역할 보안 마법사 | Microsoft Docs
description: Azure AD PIM(Privileged Identity Management)을 사용하여 영구 권한 있는 Azure AD 역할 할당을 적격 상태로 변환하는 데 사용할 수 있는 보안 마법사를 설명합니다.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 11/09/2018
ms.author: rolyon
ms.custom: pim ; H1Hack27Feb2017
ms.openlocfilehash: 337e4ee0c0b60e4b505fa9e4a2654b1a30b4c71f
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55194130"
---
# <a name="azure-ad-roles-security-wizard-in-pim"></a>PIM의 Azure AD 역할 보안 마법사

조직에서 Azure AD PIM(Privileged Identity Management)을 처음 실행하는 사용자에게는 마법사가 표시됩니다. 마법사는 권한 있는 ID에 대한 보안 위험을 이해하고 위험을 줄이도록 PIM을 사용하는 방법을 이해하도록 도와줍니다. 나중에 작업하려는 경우 마법사에서 기존 역할 할당을 변경할 필요가 없습니다.

## <a name="wizard-overview"></a>마법사 개요

조직에서 PIM 사용을 시작하기 전에 모든 역할 할당은 영구적입니다. 현재 해당 권한이 필요하지 않는 경우에도 사용자는 항상 이러한 역할에 할당되어 있습니다. 마법사의 첫 번째 단계에서는 권한이 높은 역할의 목록과 얼마나 많은 사용자가 현재 해당 역할에 있는지를 보여 줍니다. 특정 역할을 자세히 알아보면 익숙하지 않은 사용자에 대해 더 잘 알 수 있습니다.

마법사의 두 번째 단계는 관리자의 역할 할당을 변경할 기회를 제공합니다.  

> [!WARNING]
> 조직 계정(Microsoft 계정이 아닌 )을 사용하는 글로벌 관리자 한 명 이상, 권한 있는 역할 관리자 두 명 이상이 있는 것이 중요합니다. 권한 있는 역할 관리자가 한 명뿐인 경우 해당 계정이 삭제되면 조직은 PIM을 관리할 수 없습니다.
> 또한 사용자가 Microsoft 계정 (Skype나 Outlook.com과 같은 Microsoft 서비스에 로그인 하는 데 사용하는 계정)을 가진 경우 역할 할당을 영구적으로 유지합니다. 해당 역할의 활성화에 대해 MFA를 요구하려는 경우 해당 사용자가 잠기게 됩니다.

## <a name="run-the-wizard"></a>마법사 실행

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.

1. **Azure AD Privileged Identity Management**를 엽니다.

1. **Azure AD 역할**을 클릭한 다음, **마법사**를 클릭합니다.

    ![Azure AD 역할 - 마법사](./media/pim-security-wizard/wizard-start.png)

1. **1 권한 있는 역할 검색**을 클릭합니다.

1. 권한 있는 역할 목록을 검토하여 어떤 사용자가 영구 또는 적격인지 확인합니다.

    ![권한 있는 역할 사용자 검색](./media/pim-security-wizard/discover-privileged-roles-users.png)

1. **다음**을 클릭하여 적격으로 설정할 멤버를 선택합니다.

    ![멤버를 적격으로 변환](./media/pim-security-wizard/convert-members-eligible.png)

1. 멤버를 선택했으면 **확인**을 클릭합니다.

    ![변경 내용 검토](./media/pim-security-wizard/review-changes.png)

1. **확인**을 클릭하여 영구 할당을 적격으로 변환합니다.

    변환이 완료되면 알림이 표시됩니다.

    ![공지](./media/pim-security-wizard/notification-completion.png)

다른 권한 있는 역할 할당을 적격으로 변환하려면 마법사를 다시 실행하면 됩니다. 마법사 대신 PIM 인터페이스를 사용하려면 [PIM에서 Azure AD 디렉터리 역할 할당](pim-how-to-add-role-to-user.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

- [PIM에서 Azure AD 디렉터리 역할 할당](pim-how-to-add-role-to-user.md)
- [PIM 관리를 위해 다른 관리자에게 액세스 권한 부여](pim-how-to-give-access-to-pim.md)
