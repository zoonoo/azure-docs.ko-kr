---
title: 테넌트에 기존 Azure 구독 추가 - Azure Active Directory | Microsoft Docs
description: Azure Active Directory 테넌트에 기존 Azure 구독을 추가하는 방법에 대한 지침입니다.
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: lizross
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0a116355c8140d30f8297cde067a82f37f72e02a
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56165861"
---
# <a name="associate-or-add-an-azure-subscription-to-your-azure-active-directory-tenant"></a>Azure Active Directory 테넌트에 Azure 구독 연결 또는 추가
Azure 구독에는 Azure AD(Active Directory)와의 트러스트 관계가 설정되어 있습니다. 즉, 구독은 Azure AD의 사용자/서비스/디바이스 인증을 신뢰합니다. 여러 구독이 동일한 Azure AD 디렉터리를 신뢰할 수는 있지만 각 구독은 디렉터리를 하나만 신뢰할 수 있습니다.

구독이 만료되면 구독과 연결된 기타 모든 리소스에 대한 액세스 권한도 손실됩니다. 그러나 Azure AD 디렉터리는 Azure에 남아 있으므로 다른 Azure 구독을 사용하여 디렉터리를 연결하고 관리할 수 있습니다.

모든 사용자에게는 인증을 위한 “홈” 디렉터리 하나가 있습니다. 하지만 사용자는 다른 디렉터리의 게스트일 수도 있습니다. Azure AD에서 각 사용자의 홈 디렉터리와 게스트 디렉터리를 모두 확인할 수 있습니다.

>[!Important]
>구독 디렉터리가 변경되고 나면 액세스 권한이 할당된 모든 [RBAC(역할 기반 액세스 제어)](../../role-based-access-control/role-assignments-portal.md) 사용자 및 모든 구독 관리자의 액세스 권한이 손실됩니다. 또한 키 자격 증명 모음(있는 경우) 역시 구독 이동의 영향을 받습니다. 이 문제를 해결하려면 작업을 다시 시작하기 전에 [키 자격 증명 모음 테넌트 ID를 변경](../../key-vault/key-vault-subscription-move-fix.md)해야 합니다.


## <a name="before-you-begin"></a>시작하기 전에
구독을 연결하거나 추가하려면 먼저 다음 작업을 수행해야 합니다.

- 다음 조건을 충족하는 계정을 사용하여 로그인해야 합니다.
    - 구독에 대한 **RBAC 소유자** 권한이 있는 계정

    - 구독과 연결되어 있는 현재 디렉터리 및 이후 구독을 연결하려는 새 디렉터리에 모두 포함되어 있는 계정. 다른 디렉터리에 대한 액세스 권한을 얻는 방법에 대한 자세한 내용은 [Azure Active Directory 관리자가 B2B 공동 작업 사용자를 추가하는 방법](../b2b/add-users-administrator.md)을 참조하세요.

- Azure CSP(클라우드 서비스 공급자) 구독(MS-AZR-0145P, MS-AZR-0146P, MS-AZR-159P), Microsoft 내부 구독(MS-AZR-0015P) 또는 Microsoft Imagine 구독(MS-AZR-0144P)을 사용하고 있지 않은지 확인합니다.
    
## <a name="to-associate-an-existing-subscription-to-your-azure-ad-directory"></a>Azure AD 디렉터리에 기존 구독을 연결하려면
1. 로그인하여 [Azure Portal의 구독 페이지](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)에서 사용하려는 구독을 선택합니다.

2. **디렉터리 변경**을 선택합니다.

    ![디렉터리 변경 옵션이 강조 표시된 구독 페이지](media/active-directory-how-subscriptions-associated-directory/change-directory-button.png)

3. 표시되는 경고를 검토한 다음 **변경**을 선택합니다.

    ![디렉터리 페이지를 변경하여 변경 대상 디렉터리 표시](media/active-directory-how-subscriptions-associated-directory/edit-directory-ui.png)

    구독의 디렉터리가 변경되며 성공 메시지가 표시됩니다.

    ![성공 메시지](media/active-directory-how-subscriptions-associated-directory/edit-directory-success.png)    

4. 디렉터리 전환기를 사용하여 새 디렉터리로 이동합니다. 모든 항목이 제대로 표시되는 데 최대 10분이 걸릴 수 있습니다.

    ![디렉터리 전환기 페이지](media/active-directory-how-subscriptions-associated-directory/directory-switcher.png)

구독 디렉터리 변경은 서비스 수준 작업이므로 구독 청구 소유권에 영향을 주지 않습니다. 계정 관리자는 [계정 센터](https://account.azure.com/subscriptions)에서 서비스 관리자를 계속 변경할 수 있습니다. 원래 디렉터리를 삭제하려면 구독 청구 소유권을 새 계정 관리자로 이전해야 합니다. 청구 소유권 이전에 대해 자세히 알아보려면 [Azure 구독의 소유권을 다른 계정으로 이전](../../billing/billing-subscription-transfer.md)을 참조하세요. 

## <a name="next-steps"></a>다음 단계

- 새 Azure AD 테넌트를 만들려면 [Azure Active Directory에 액세스하여 새 테넌트 만들기](active-directory-access-create-new-tenant.md)를 참조하세요.

- Microsoft Azure에서 리소스 액세스를 제어하는 방법을 자세히 알아보려면 [Azure에서 리소스 액세스 이해](../../role-based-access-control/rbac-and-directory-admin-roles.md)

- Azure AD에서 역할을 할당하는 방법에 대해 자세히 알아보려면 [Azure Active Directory를 통해 사용자에게 디렉터리 역할을 할당하는 방법](active-directory-users-assign-role-azure-portal.md)을 참조하세요.
