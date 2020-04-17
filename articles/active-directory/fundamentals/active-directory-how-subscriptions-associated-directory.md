---
title: 테넌트 - Azure AD에 기존 Azure 구독 추가
description: Azure Active Directory 테넌트에 기존 Azure 구독을 추가하는 방법에 대한 지침입니다.
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: ajburnle
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 104bf51fb03d88ab0e5efd25ebebb0e3060bc264
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81457929"
---
# <a name="associate-or-add-an-azure-subscription-to-your-azure-active-directory-tenant"></a>Azure Active Directory 테넌트에 Azure 구독 연결 또는 추가

Azure 구독에는 Azure Active Directory(Azure AD)와 트러스트 관계가 있습니다. 구독은 Azure AD를 신뢰하여 사용자, 서비스 및 장치를 인증합니다.

여러 구독은 동일한 Azure AD 디렉터리를 신뢰할 수 있습니다. 각 구독은 단일 디렉터리만 신뢰할 수 있습니다.

구독이 만료되면 구독과 연결된 기타 모든 리소스에 대한 액세스 권한도 손실됩니다. 그러나 Azure AD 디렉터리Azure에 남아 있습니다. 다른 Azure 구독을 사용하여 디렉터리를 연결하고 관리할 수 있습니다.

모든 사용자에게 인증을 위한 단일 *홈* 디렉터리가 있습니다. 사용자는 다른 디렉터리에서 게스트가 될 수도 있습니다. Azure AD에서 각 사용자의 홈 디렉터리와 게스트 디렉터리를 모두 확인할 수 있습니다.

> [!Important]
> 구독을 다른 디렉터리에 연결하면 [RBAC(역할 기반 액세스 제어)를](../../role-based-access-control/role-assignments-portal.md) 사용하여 역할이 할당된 사용자는 액세스 권한을 잃게 됩니다. 서비스 관리자 및 공동 관리자를 비롯한 클래식 구독 관리자도 액세스 권한을 잃게 됩니다.
>
> 구독을 다른 디렉터리와 연결하는 경우에도 구독에서 정책 할당이 제거됩니다.
>
> Azure Kubernetes 서비스(AKS) 클러스터를 다른 구독으로 이동하거나 클러스터 소유 구독을 새 테넌트로 이동하면 역할 할당 및 서비스 주체의 권한이 손실되어 클러스터가 기능을 잃게 됩니다. AKS에 대한 자세한 내용은 [AZURE Kubernetes 서비스(AKS)를](https://docs.microsoft.com/azure/aks/)참조하십시오.


## <a name="before-you-begin"></a>시작하기 전 주의 사항

구독을 연결하거나 추가하려면 다음 작업을 수행합니다.

- 다음 변경 사항 목록과 영향을 받을 수 있는 방법을 검토합니다.

  - RBAC를 사용하여 역할이 할당된 사용자는 액세스 권한을 잃게 됩니다.
  - 서비스 관리자 및 공동 관리자는 액세스 권한을 잃게 됩니다.
  - 키 자격 증명 모음이 있는 경우 액세스할 수 없으며 연결 후 수정해야 합니다.
  - 가상 컴퓨터 또는 논리 앱과 같은 리소스에 대해 관리되는 ID가 있는 경우 연결 후 다시 활성화하거나 다시 만들어야 합니다.
  - 등록된 Azure 스택이 있는 경우 연결 후 다시 등록해야 합니다.

- 다음 조건을 충족하는 계정을 사용하여 로그인해야 합니다.

  - 구독에 대한 [소유자](../../role-based-access-control/built-in-roles.md#owner) 역할 할당이 있습니다. 소유자 역할을 할당하는 방법에 대한 자세한 내용은 [RBAC 및 Azure 포털을 사용하여 Azure 리소스에 대한 액세스 관리를](../../role-based-access-control/role-assignments-portal.md)참조하십시오.
  - 현재 디렉터리와 새 디렉터리모두에 존재합니다. 현재 디렉터리구독과 연결됩니다. 새 디렉터리를 구독과 연결합니다. 다른 디렉터리에 대한 액세스 권한에 대한 자세한 내용은 [Azure 포털의 Azure Active Directory B2B 공동 작업 사용자 추가를](../b2b/add-users-administrator.md)참조하십시오.

- Azure CSP(클라우드 서비스 공급자) 구독(MS-AZR-0145P, MS-AZR-0146P, MS-AZR-159P), Microsoft 내부 구독(MS-AZR-0015P) 또는 Microsoft Imagine 구독(MS-AZR-0144P)을 사용하고 있지 않은지 확인합니다.

## <a name="associate-a-subscription-to-a-directory"></a>구독을 디렉터리로 연결합니다.<a name="to-associate-an-existing-subscription-to-your-azure-ad-directory"></a>

기존 구독을 Azure AD 디렉터리에 연결하려면 다음 단계를 따르십시오.

1. 로그인하여 [Azure Portal의 구독 페이지](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)에서 사용하려는 구독을 선택합니다.

1. **디렉터리 변경**을 선택합니다.

    ![디렉터리 변경 옵션이 강조 표시된 구독 페이지](media/active-directory-how-subscriptions-associated-directory/change-directory-in-azure-subscriptions.png)

1. 표시되는 경고를 검토한 다음 **변경**을 선택합니다.

    ![디렉터리 페이지를 변경하여 변경 대상 디렉터리 표시](media/active-directory-how-subscriptions-associated-directory/edit-directory-ui.png)

    구독의 디렉터리가 변경되며 성공 메시지가 표시됩니다.

    ![디렉터리 변경에 대한 성공 메시지](media/active-directory-how-subscriptions-associated-directory/edit-directory-success.png)

**스위치 디렉토리를** 사용하여 새 디렉터리로 이동합니다. 모든 것이 제대로 표시되기까지 몇 시간이 걸릴 수 있습니다. 시간이 너무 오래 걸리는 것 같으면 **글로벌 구독 필터를**확인하십시오. 이동된 구독이 숨겨져 있지 않은지 확인합니다. 새 디렉터리를 보려면 Azure 포털에서 로그아웃하고 다시 로그인해야 할 수 있습니다.

![샘플 정보가 있는 디렉토리 스위처 페이지](media/active-directory-how-subscriptions-associated-directory/directory-switcher.png)

구독 디렉터리 변경은 서비스 수준 작업이므로 구독 청구 소유권에 영향을 주지 않습니다. 계정 관리자는 [계정 센터](https://account.azure.com/subscriptions)에서 서비스 관리자를 계속 변경할 수 있습니다. 원래 디렉터리를 삭제하려면 구독 청구 소유권을 새 계정 관리자에게 이전해야 합니다. 청구 소유권 이전에 대한 자세한 내용은 [Azure 구독의 소유권을 다른 계정으로 이전합니다.](../../cost-management-billing/manage/billing-subscription-transfer.md)

## <a name="post-association-steps"></a>연결 후 단계

구독을 다른 디렉터리에 연결한 후 작업을 다시 시작하려면 다음 작업을 수행해야 할 수 있습니다.

- 키 자격 증명 모음이 있는 경우 키 자격 증명 모음 테넌트 ID를 변경해야 합니다. 자세한 내용은 [구독 이동 후 키 자격 증명 모음 테넌트 ID 변경을](../../key-vault/general/subscription-move-fix.md)참조하십시오.

- 리소스에 대해 시스템 할당된 관리 ID를 사용한 경우 이러한 ID를 다시 활성화해야 합니다. 사용자가 할당한 관리 ID를 사용한 경우 이러한 ID를 다시 만들어야 합니다. 관리되는 ID를 다시 사용하거나 다시 만든 후에는 해당 ID에 할당된 권한을 다시 설정해야 합니다. 자세한 내용은 [Azure 리소스에 대한 관리ID란 무엇입니까?](../managed-identities-azure-resources/overview.md)

- 이 구독을 사용하여 Azure 스택을 등록한 경우 다시 등록해야 합니다. 자세한 내용은 [Azure를 사용 하 여 Azure 스택 등록을](/azure-stack/operator/azure-stack-registration)참조 하십시오.

## <a name="next-steps"></a>다음 단계

- 새 Azure AD 테넌트를 만들려면 [빠른 시작: Azure Active Directory에서 새 테넌트 만들기를](active-directory-access-create-new-tenant.md)참조하십시오.

- Microsoft Azure에서 리소스 액세스를 제어하는 방법에 대한 자세한 내용은 [클래식 구독 관리자 역할, Azure RBAC 역할 및 Azure AD 관리자 역할을](../../role-based-access-control/rbac-and-directory-admin-roles.md)참조하십시오.

- Azure AD에서 역할을 할당하는 방법에 대한 자세한 내용은 [Azure Active Directory를 사용 하 여 사용자에 게 관리자 및 비 관리자 역할 할당](active-directory-users-assign-role-azure-portal.md)을 참조 합니다.
