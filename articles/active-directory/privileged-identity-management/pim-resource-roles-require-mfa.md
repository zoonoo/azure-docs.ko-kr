---
title: PIM에서 Azure 리소스 역할에 대한 다단계 인증 필요 | Microsoft Docs
description: Azure AD PIM(Privileged Identity Management)에서 Azure 리소스 역할에 대해 MFA(다단계 인증)를 요구하는 방법을 알아봅니다.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.component: pim
ms.date: 04/02/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 171d79856cf67dae9573dd1076c2ae4617cf86d1
ms.sourcegitcommit: 63613e4c7edf1b1875a2974a29ab2a8ce5d90e3b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/29/2018
ms.locfileid: "43190571"
---
# <a name="require-multi-factor-authentication-for-azure-resource-roles-in-pim"></a>PIM에서 Azure 리소스 역할에 대한 다단계 인증 필요

Azure 리소스 역할용 PIM(Privileged Identity Management)을 사용하면 리소스 관리자와 ID 관리자가 시간 제한 멤버 자격 및 Just-In-Time 액세스를 통해 중요한 Azure 인프라를 보호할 수 있습니다. 또한 PIM은 서로 다른 두 가지 시나리오에 대해 Azure Multi-Factor Authentication의 선택적 적용을 제공합니다.

## <a name="require-multi-factor-authentication-to-activate"></a>Multi-Factor Authentication 활성화 필요

리소스 관리자는 역할의 적격 멤버가 Azure Multi-Factor Authentication을 실행해야만 활성화할 수 있도록 설정할 수 있습니다. 이 프로세스를 통해 활성화를 요청한 사용자가 합당한 확신이 있는 당사자 본인임을 확인할 수 있습니다. 이 옵션을 적용하면 사용자의 계정이 손상되었을 수 있는 상황에서 중요한 리소스를 보호할 수 있습니다. 

이 요구 사항을 적용하려면 관리되는 리소스 목록에서 리소스를 선택합니다. [개요 대시보드](pim-resource-roles-overview-dashboards.md)의 화면 오른쪽 아래에 있는 역할 목록에서 역할을 선택합니다.

또한 왼쪽 창의 **역할** 또는 **역할 설정** 탭에서 역할 설정을 가져올 수 있습니다.

>[!Note]
>왼쪽 창의 옵션이 회색으로 표시되고 페이지 맨 위에 "활성화에 적합한 역할이 있습니다"라는 배너가 표시되면 현재 활성 관리자가 아니라는 뜻입니다. 이 경우 [활성화](pim-resource-roles-activate-your-roles.md)해야만 계속 진행할 수 있습니다.

!["역할" 및 "역할 설정" 탭 ](media/azure-pim-resource-rbac/aadpim_rbac_manage_a_role_v2.png)

역할의 멤버 자격을 보려면 화면 맨 위의 표시줄에서 **역할 설정**을 선택하여 **역할 설정 정보**를 엽니다.

역할 설정을 수정하려면 맨 위에서 **편집** 단추를 클릭합니다.

**활성화** 아래에 있는 섹션에서 **활성화 시 Multi-Factor Authentication 필요** 확인란을 선택합니다. 그런 다음 **저장**을 선택합니다.

![활성화 시 Multi-Factor Authentication 필요](media/azure-pim-resource-rbac/aadpim_rbac_require_mfa.png)

## <a name="require-multi-factor-authentication-on-assignment"></a>할당 시 Multi-Factor Authentication 필요

리소스 관리자가 멤버를 단기간(예: 하루) 동안 역할에 할당하려는 경우가 있습니다. 이 경우 할당된 멤버가 활성화를 요청하게 할 필요가 없습니다. 이 시나리오에서는 멤버가 자신의 역할 할당을 사용하는 경우 PIM이 Multi-Factor Authentication을 적용할 수 없습니다. 해당 멤버가 할당된 순간부터 역할에서 이미 활성 상태이기 때문입니다.

할당을 수행하는 리소스 관리자가 자신이 말하는 본인이 맞는지 확인하기 위해 할당 시 Multi-Factor Authentication을 적용할 수 있습니다.

동일한 역할 설정 세부 정보 화면에서 **직접 할당 시 Multi-Factor Authentication 필요** 확인란을 선택합니다.

![직접 할당 시 Multi-Factor Authentication 필요](media/azure-pim-resource-rbac/aadpim_rbac_require_mfa_on_assignment.png)

## <a name="next-steps"></a>다음 단계

- [PIM에서 Azure 리소스 역할 설정 구성](pim-resource-roles-configure-role-settings.md)
- [PIM에서 Azure 리소스에 대한 보안 경고 구성](pim-resource-roles-configure-alerts.md)


