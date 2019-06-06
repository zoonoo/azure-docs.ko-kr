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
ms.date: 03/13/2019
ms.author: lizross
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: d2889af6000e77fba7a91392c0adb227588b5306
ms.sourcegitcommit: 087ee51483b7180f9e897431e83f37b08ec890ae
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/31/2019
ms.locfileid: "66430785"
---
# <a name="associate-or-add-an-azure-subscription-to-your-azure-active-directory-tenant"></a>Azure Active Directory 테넌트에 Azure 구독 연결 또는 추가

Azure 구독을 구독 사용자, 서비스 및 장치를 인증 하려면 Azure AD를 신뢰 하는 Azure Active Directory (Azure AD)를 사용 하 여 트러스트 관계를 있습니다. 여러 구독이 동일한 Azure AD 디렉터리를 신뢰할 수는 있지만 각 구독은 디렉터리를 하나만 신뢰할 수 있습니다.

구독이 만료되면 구독과 연결된 기타 모든 리소스에 대한 액세스 권한도 손실됩니다. 그러나 Azure AD 디렉터리는 Azure에 남아 있으므로 다른 Azure 구독을 사용하여 디렉터리를 연결하고 관리할 수 있습니다.

모든 사용자가 단일 *홈* 인증에 대 한 디렉터리입니다. 하지만 사용자는 다른 디렉터리의 게스트일 수도 있습니다. Azure AD에서 각 사용자의 홈 디렉터리와 게스트 디렉터리를 모두 확인할 수 있습니다.

> [!Important]
> 다른 디렉터리 역할을 사용 하 여 할당 된 사용자에 대 한 구독을 연결할 때 [역할 기반 액세스 제어 (RBAC)](../../role-based-access-control/role-assignments-portal.md) 액세스가 손실 됩니다. 클래식 구독 관리자 (서비스 관리자 및 공동 관리자)에 액세스할 수 없게 됩니다.
> 
> 또한 Azure Kubernetes Service (AKS) 클러스터를 다른 구독으로 이동 하거나 새 테 넌 트를 클러스터 소유 구독을 이동 하면 클러스터가 손실된 역할 할당 및 서비스 보안 주체 사용 권한으로 인해 기능이 손실 합니다. AKS에 대 한 자세한 내용은 참조 하세요. [Azure Kubernetes Service (AKS)](https://docs.microsoft.com/azure/aks/)합니다.

## <a name="before-you-begin"></a>시작하기 전에

구독을 연결하거나 추가하려면 먼저 다음 작업을 수행해야 합니다.

1. 변경 되 고 영향 어떻게 다음 목록을 검토 합니다.

    - RBAC를 사용 하 여 역할 할당 된 사용자에 대 한 액세스를 잃게 됩니다.
    - 서비스 관리자 및 공동 관리자에 액세스할 수 없게 됩니다.
    - 모든 주요 자격 증명 모음에 있는 경우 이러한에서는 액세스할 수 없게 및 연결 후 수정 해야 합니다.
    - Virtual Machines 또는 Logic Apps와 같은 리소스에 대 한 모든 관리 되는 id를 설정한 경우 다시 사용 하도록 설정 하거나 연결 후 다시
    - 연결 후 다시 등록 해야는 등록 된 Azure Stack에 있는 경우

1. 다음 조건을 충족하는 계정을 사용하여 로그인해야 합니다.
    - 에 [소유자](../../role-based-access-control/built-in-roles.md#owner) 구독에 대 한 역할 할당 합니다. 소유자 역할을 할당 하는 방법에 대 한 정보를 참조 하세요 [RBAC 및 Azure portal을 사용 하 여 Azure 리소스에 대 한 액세스 관리](../../role-based-access-control/role-assignments-portal.md)합니다.
    - 구독과 연결되어 있는 현재 디렉터리 및 이후 구독을 연결하려는 새 디렉터리에 모두 포함되어 있는 계정. 다른 디렉터리에 대한 액세스 권한을 얻는 방법에 대한 자세한 내용은 [Azure Active Directory 관리자가 B2B 협업 사용자를 추가하는 방법](../b2b/add-users-administrator.md)을 참조하세요.

1. Azure CSP(클라우드 서비스 공급자) 구독(MS-AZR-0145P, MS-AZR-0146P, MS-AZR-159P), Microsoft 내부 구독(MS-AZR-0015P) 또는 Microsoft Imagine 구독(MS-AZR-0144P)을 사용하고 있지 않은지 확인합니다.
    
## <a name="to-associate-an-existing-subscription-to-your-azure-ad-directory"></a>Azure AD 디렉터리에 기존 구독을 연결하려면

1. 로그인하여 [Azure Portal의 구독 페이지](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)에서 사용하려는 구독을 선택합니다.

2. **디렉터리 변경**을 선택합니다.

    ![디렉터리 변경 옵션이 강조 표시된 구독 페이지](media/active-directory-how-subscriptions-associated-directory/change-directory-button.png)

3. 표시되는 경고를 검토한 다음 **변경**을 선택합니다.

    ![디렉터리 페이지를 변경하여 변경 대상 디렉터리 표시](media/active-directory-how-subscriptions-associated-directory/edit-directory-ui.png)

    구독의 디렉터리가 변경되며 성공 메시지가 표시됩니다.

    ![디렉터리 변경에 대 한 성공 메시지](media/active-directory-how-subscriptions-associated-directory/edit-directory-success.png)
4. 사용 된 **디렉터리 전환기** 새 디렉터리로 이동 합니다. 모든 항목이 제대로 표시 되는 데 몇 시간이 걸릴 수 있습니다. 경우 오랜 시간이 걸리는 것 했는지 확인 합니다 **전역 구독 필터** 이동된 구독에 대 한 되도록 하지 숨겨져 있습니다.

    ![샘플 정보를 사용 하 여 디렉터리 전환기 페이지](media/active-directory-how-subscriptions-associated-directory/directory-switcher.png)

구독 디렉터리 변경은 서비스 수준 작업이므로 구독 청구 소유권에 영향을 주지 않습니다. 계정 관리자는 [계정 센터](https://account.azure.com/subscriptions)에서 서비스 관리자를 계속 변경할 수 있습니다. 원래 디렉터리를 삭제하려면 구독 청구 소유권을 새 계정 관리자로 이전해야 합니다. 청구 소유권 이전에 대해 자세히 알아보려면 [Azure 구독의 소유권을 다른 계정으로 이전](../../billing/billing-subscription-transfer.md)을 참조하세요.

## <a name="post-association-steps"></a>사후 연결 단계
다른 디렉터리에 대 한 구독을 연결한 후 작업을 다시 시작할 때 수행 해야 하는 추가 단계가 있을 수 있습니다.

1. 키 자격 증명 모음 테 넌 트 ID를 변경 해야 모든 키 자격 증명 모음에 있는 경우 자세한 내용은 [구독 이동 후 주요 자격 증명 모음 테 넌 트 ID 변경](../../key-vault/key-vault-subscription-move-fix.md)합니다.

2. 리소스에 대 한 관리 되는 시스템 할당 Id를 사용한 경우 이러한 다시 활성화 해야 있습니다. 관리 되는 사용자 할당 Id를 사용 하는 경우 다시 만들어야 이러한 합니다. 를 다시 사용 하도록 설정 하거나 관리 되는 Id를 다시 만드는 후 해당 id에 할당 된 권한을 다시 설정 해야 합니다. 자세한 내용은 참조 [Azure 리소스에 대 한 관리 되는 id 란?](../managed-identities-azure-resources/overview.md)합니다.

3. 이 구독을 사용 하 여 Azure Stack에 등록 하는 경우 다시 등록 해야 합니다. 자세한 내용은 [Azure 사용 하 여 Azure Stack 등록](/azure-stack/operator/azure-stack-registration)합니다.



## <a name="next-steps"></a>다음 단계

- 새 Azure AD 테넌트를 만들려면 [Azure Active Directory에 액세스하여 새 테넌트 만들기](active-directory-access-create-new-tenant.md)를 참조하세요.

- Microsoft Azure에서 리소스 액세스를 제어하는 방법을 자세히 알아보려면 [Azure에서 리소스 액세스 이해](../../role-based-access-control/rbac-and-directory-admin-roles.md)

- Azure AD에서 역할을 할당하는 방법에 대해 자세히 알아보려면 [Azure Active Directory를 통해 사용자에게 디렉터리 역할을 할당하는 방법](active-directory-users-assign-role-azure-portal.md)을 참조하세요.
