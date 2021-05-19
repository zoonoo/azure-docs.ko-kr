---
title: Microsoft 고객 계약 청구 계정에서 테넌트 관리 - Azure
description: 이 문서에서는 Microsoft 고객 계약 청구 계정과 연결된 테넌트를 파악하고 관리할 수 있도록 도와줍니다.
author: bandersmsft
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 05/05/2021
ms.author: banders
ms.reviewer: baolcsva
ms.openlocfilehash: a1d66e1a28717feb66bc223d10ae44e8d5457728
ms.sourcegitcommit: 02d443532c4d2e9e449025908a05fb9c84eba039
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/06/2021
ms.locfileid: "108747866"
---
# <a name="manage-tenants-in-your-microsoft-customer-agreement-billing-account"></a>Microsoft 고객 계약 청구 계정에서 테넌트 관리

이 문서에서는 Microsoft 고객 계약 청구 계정과 연결된 테넌트를 파악하고 관리할 수 있도록 도와줍니다. 이 문서의 정보를 사용하여 테넌트를 관리하고, 구독을 양도하고, 청구 소유권을 관리하는 한편 청구 환경에 대한 보안 액세스를 보장할 수 있습니다.

## <a name="whats-a-tenant"></a>테넌트란?

테넌트는 조직을 디지털로 표현한 것으로, 주로 Microsoft.com과 같은 도메인과 연결됩니다. Azure 리소스 및 청구 관리 권한을 사용자에게 할당할 수 있는, Azure Active Directory를 통해 관리되는 환경입니다.

각 테넌트는 서로 다르며 다른 테넌트와 별개이지만, 다른 테넌트의 게스트 사용자가 내 테넌트에 액세스하여 내 비용을 추적하고 청구를 관리하도록 허용할 수 있습니다.

## <a name="how-tenants-and-subscriptions-relate-to-billing-account"></a>테넌트 및 구독과 청구 계정 간 관련성

Microsoft 고객 계약(청구 계정)에서 비용을 추적하고 청구를 관리합니다. 각 청구 계정에는 적어도 하나의 청구 프로필이 있습니다. 이러한 청구 프로필을 통해 청구서 및 결제 방법을 관리할 수 있습니다. 각 청구 프로필에는 기본적으로 하나의 청구서 섹션이 포함됩니다. 필요한 경우 더 많은 청구서 섹션을 만들어 더 세분화된 수준으로 비용을 그룹화, 추적 및 관리할 수 있습니다.

- 청구 계정은 단일 테넌트와 연결되어 있습니다. 즉, 해당 테넌트에 속한 사용자만 청구 계정에 액세스할 수 있습니다.
- 청구 계정에 새 Azure 구독을 만들면 항상 청구 계정 테넌트에 생성됩니다. 하지만 구독을 다른 테넌트로 이동할 수는 있습니다. 다른 테넌트의 기존 구독을 내 청구 계정에 연결할 수도 있습니다. 필요에 따라 한 테넌트에 리소스와 구독을 유지하면서 다른 테넌트를 통해 중앙에서 청구를 관리할 수 있습니다.

다음 다이어그램에서는 청구 계정 및 구독과 테넌트의 연결 방식을 보여줍니다. Contoso MCA 청구 계정은 테넌트 1과 연결된 반면, Contoso PAYG 계정은 테넌트 2에 연결되어 있습니다. Contoso가 MCA 청구 계정을 통해 PAYG 구독 요금을 지불하려고 한다고 가정해보겠습니다. 이를 위해서는 청구 소유권을 양도하여 구독을 MCA 청구 계정에 연결하면 됩니다. 구독과 리소스는 여전히 테넌트 2와 연결되어 있지만 관련 요금이 MCA 청구 계정을 통해 지불됩니다.

:::image type="content" source="./media/manage-tenants/billing-hierarchy-example.png" alt-text="예제 청구 계층을 보여주는 다이어그램." border="false" lightbox="./media/manage-tenants/billing-hierarchy-example.png":::

## <a name="manage-subscriptions-under-multiple-tenants-in-a-single-microsoft-customer-agreement"></a>단일 Microsoft 고객 계약의 여러 테넌트에서 구독 관리

청구 계정에 대한 [적절한 사용 권한](../manage/understand-mca-roles.md#subscription-billing-roles-and-tasks)이 있는 청구 소유자는 구독을 만들 수 있습니다. 기본적으로 Microsoft 고객 계약에서 생성된 모든 새 구독은 Microsoft 고객 계약 테넌트에 있습니다.

- 다른 테넌트의 구독을 내 Microsoft 고객 계약 청구 계정에 연결할 수 있습니다. 구독에 대한 청구 소유권을 획득하면 청구 정보만 변경됩니다. 서비스 테넌트 또는 Azure RBAC 역할에는 영향을 주지 않습니다.
- 서비스 테넌트에서 구독 소유자를 변경하려면 [다른 Azure Active Directory 디렉터리로 구독](../../role-based-access-control/transfer-subscription.md)을 양도해야 합니다.

MCA 청구 계정은 단일 테넌트/디렉터리에서 관리됩니다. 청구 계정은 테넌트의 구독에 대한 청구서만 통제합니다. 그러나 청구 소유권 이전을 사용하여 다른 테넌트의 청구 계정에 구독을 연결할 수 있습니다.

### <a name="billing-ownership-transfer"></a>청구 소유권 이전

청구 소유권 이전은 단일 구독에 대한 청구서 배열만 변경합니다. 구독에 대한 사용자 및 자원 관리는 변경되지 않습니다.

청구 소유권 이전은 다음과 같은 두 가지 영향을 미칩니다.

- 구독의 원래 청구 소유권이 제거됩니다.
- 구독 청구 소유권은 다른 테넌트/디렉터리에 있을 수 있는 대상 청구 계정에 *링크됩니다*.

청구 소유권 이전은 다음에 영향을 주지 않습니다.

- 사용자
- 리소스
- Azure RBAC 권한


## <a name="add-guest-users-to-your-microsoft-customer-agreement-tenant"></a>Microsoft 고객 계약 테넌트에 게스트 사용자 추가

특정 Microsoft 고객 계약 청구 테넌트에 추가된 사용자가 다른 테넌트의 청구 업무를 관리하려면 게스트로 초대받아야 합니다.

사용자를 게스트로 초대하려면 해당 사용자에게 내 Azure AD(Active Directory) 도메인과 다른 도메인의 이메일 주소가 기존에 있어야만 합니다. Azure AD는 인증 링크가 포함된 이메일을 게스트 사용자에게 보냅니다.

:::image type="content" source="./media/manage-tenants/guest-invitation-email.png" alt-text="예제 이메일 초대장을 보여주는 스크린샷" lightbox="./media/manage-tenants/guest-invitation-email.png" :::

사용자가 Microsoft 고객 계약 테넌트에 추가되면 [초대를 수락](../../active-directory/external-identities/b2b-quickstart-add-guest-users-portal.md#accept-the-invitation)해야 합니다.

**초대 수락** 링크를 선택하면 Azure에 인증하라는 메시지가 표시됩니다.

:::image type="content" source="./media/manage-tenants/authenticate.png" alt-text="Azure에 인증하라는 메시지를 보여주는 스크린샷" lightbox="./media/manage-tenants/authenticate.png" :::

그런 다음, **수락** 을 선택합니다.

:::image type="content" source="./media/manage-tenants/accept-invitation.png" alt-text="초대를 수락하라는 메시지를 보여주는 스크린샷" lightbox="./media/manage-tenants/accept-invitation.png" :::

초대를 수락하고 나면 [Cost Management + Billing 아래에 Microsoft 고객 계약 청구 계정이 나타납니다](../understand/mca-overview.md#check-access-to-a-microsoft-customer-agreement).

:::image type="content" source="./media/manage-tenants/billing-microsoft-customer-agreement-in-list.png" alt-text="청구 계정 목록에서 Microsoft 고객 계약을 보여주는 스크린샷" lightbox="./media/manage-tenants/billing-microsoft-customer-agreement-in-list.png" :::

게스트 사용자를 초대할 수 있는 권한 부여는 Azure AD 설정을 통해 제어됩니다. 설정 값은 **조직 관계** 페이지의 **설정** 아래에 표시됩니다. 설정이 선택되어 있는지 확인하세요. 그렇지 않으면 초대에 실패합니다. 자세한 내용은 [게스트 사용자 액세스 권한 제한](../../active-directory/enterprise-users/users-restrict-guest-permissions.md)을 참조하세요.

:::image type="content" source="./media/manage-tenants/external-collaboration-settings.png" alt-text="외부 협업 설정을 보여주는 스크린샷" lightbox="./media/manage-tenants/external-collaboration-settings.png" :::

> [!IMPORTANT]
> 단, 이렇게 하면 게스트 사용자가 Microsoft 고객 계약 테넌트에 액세스할 수 있게 되므로 잠재적인 보안 문제가 발생할 수 있습니다. 자세한 내용은 [게스트 사용자의 기본 사용 권한을 제한하는 방법 알아보기](../../active-directory/fundamentals/users-default-permissions.md#restrict-member-users-default-permissions)를 참조하세요.

## <a name="manage-multiple-microsoft-cloud-services-under-an-azure-ad-tenant"></a>Azure AD 테넌트에서 여러 Microsoft 클라우드 서비스 관리

단일 Azure AD 테넌트에서 조직의 여러 클라우드 서비스를 관리할 수 있습니다. Microsoft 클라우드 제품에 대한 사용자 계정은 모두 사용자 계정 및 그룹이 포함된 Azure AD 테넌트에 저장됩니다. 다음 다이어그램에서는 계정이 포함된 공통 Azure AD 테넌트에서 여러 서비스를 사용하는 조직의 예를 보여줍니다. 각 서비스에는 사용자가 자신의 서비스를 관리하는 고유한 포털이 파란색 텍스트로 표시됩니다.

:::image type="content" source="./media/manage-tenants/diagram-multiple-services-common-azure-ad-tenant-accounts.png" alt-text="계정이 포함된 공통 Azure AD 테넌트에서 여러 서비스를 사용하는 조직의 예를 보여주는 다이어그램." border="false" lightbox="./media/manage-tenants/diagram-multiple-services-common-azure-ad-tenant-accounts.png":::

## <a name="next-steps"></a>다음 단계

가변형 청구 소유권을 관리하고 Microsoft 고객 계약에 안전하게 액세스하는 방법을 알아보려면 다음 문서를 참조하세요.

- [테넌트를 설정하는 방법](../../active-directory/develop/quickstart-create-new-tenant.md)
- [Azure 기본 제공 역할](../../role-based-access-control/built-in-roles.md)
- [다른 Azure AD 디렉터리로 Azure 구독 양도](../../role-based-access-control/transfer-subscription.md)
- [Azure Active Directory에서 게스트 액세스 권한 제한(미리 보기)](../../active-directory/enterprise-users/users-restrict-guest-permissions.md)
- [Azure Portal에서 디렉터리에 게스트 사용자를 추가](../../active-directory/external-identities/b2b-quickstart-add-guest-users-portal.md#accept-the-invitation)
- [Azure Active Directory의 기본 사용자 권한이란?](../../active-directory/external-identities/b2b-quickstart-add-guest-users-portal.md#accept-the-invitation)
- [Azure Active Directory란?](../../active-directory/fundamentals/active-directory-whatis.md)