---
title: Azure Security Center에서 테 넌 트 전체 사용 권한 부여 및 요청
description: Azure Security Center에서 테 넌 트 전체 사용 권한을 관리 하는 방법을 알아봅니다.
author: memildin
ms.author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 03/11/2021
ms.openlocfilehash: 0a24546579df020dcb7c7a9b01ee3d181226d2df
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102617491"
---
# <a name="grant-and-request-tenant-wide-visibility"></a>테 넌 트 전체 표시 권한 부여 및 요청

**전역 관리자** 의 AD (Azure Active Directory) 역할이 있는 사용자는 테 넌 트 전반에 걸친 책임이 있지만 Azure Security Center에서 조직 차원의 정보를 볼 수 있는 Azure 권한은 없습니다. Azure AD 역할 할당은 Azure 리소스에 대 한 액세스 권한을 부여 하지 않으므로 권한 상승이 필요 합니다. 

## <a name="grant-tenant-wide-permissions-to-yourself"></a>자신에 게 테 넌 트 전체 사용 권한 부여

자신에 게 테 넌 트 수준 권한을 할당 하려면:

1. 조직에서 [pim (Azure AD Privileged Identity Management](../active-directory/privileged-identity-management/pim-configure.md)) 또는 다른 pim 도구를 사용 하 여 리소스 액세스를 관리 하는 경우 다음 절차를 수행 하 여 사용자에 대해 전역 관리자 역할이 활성화 되어 있어야 합니다.

1. 테 넌 트의 루트 관리 그룹에 대 한 할당이 없는 전역 관리자 사용자로 서 Security Center의 **개요** 페이지를 열고 배너에서 **테 넌 트 전체 표시 유형** 링크를 선택 합니다. 

    :::image type="content" source="media/security-center-management-groups/enable-tenant-level-permissions-banner.png" alt-text="Azure Security Center에서 테 넌 트 수준 권한 사용":::

1. 할당할 새 Azure 역할을 선택 합니다. 

    :::image type="content" source="media/security-center-management-groups/enable-tenant-level-permissions-form.png" alt-text="사용자에 게 할당 되는 테 넌 트 수준 권한을 정의 하기 위한 양식":::

    > [!TIP]
    > 일반적으로 보안 관리자 역할은 루트 수준에서 정책을 적용해야 하며, 보안 읽기 권한자는 테넌트 수준 가시성을 제공하는 데 충분합니다. 이러한 역할로 부여되는 권한에 대한 자세한 내용은 [보안 관리자 기본 제공 역할 설명](../role-based-access-control/built-in-roles.md#security-admin) 또는 [보안 읽기 권한자 기본 제공 역할 설명](../role-based-access-control/built-in-roles.md#security-reader)을 참조하세요.
    >
    > Security Center와 관련 된 이러한 역할 간의 차이점에 대해서는 [역할 및 허용 되는 작업](security-center-permissions.md#roles-and-allowed-actions)의 표를 참조 하세요.

    조직 전체 보기는 테 넌 트의 루트 관리 그룹 수준에서 역할을 부여 하 여 수행 됩니다.  

1. Azure Portal 로그 아웃 한 다음 다시 로그인 합니다.

1. 액세스 권한을 승격하면 Azure Security Center를 열거나 새로 고쳐 Azure AD 테넌트 아래의 모든 구독에 대한 가시성이 있는지 확인합니다. 

위의 간단한 프로세스는 자동으로 많은 작업을 수행 합니다.

1. 사용자의 사용 권한이 일시적으로 상승 되었습니다.
1. 새 사용 권한을 사용 하 여 사용자는 루트 관리 그룹의 원하는 Azure RBAC 역할에 할당 됩니다.
1. 승격 된 권한이 제거 됩니다.

Azure AD 권한 상승 프로세스에 대 한 자세한 내용은 [모든 azure 구독 및 관리 그룹을 관리 하기 위해 액세스 권한 상승](../role-based-access-control/elevate-access-global-admin.md)을 참조 하세요.


## <a name="request-tenant-wide-permissions-when-yours-are-insufficient"></a>권한이 충분 하지 않은 경우 테 넌 트 전체 사용 권한 요청

Security Center에 로그인 하 여 보기가 제한적 이라고 알려주는 배너를 표시 하는 경우에는를 클릭 하 여 조직의 전역 관리자에 게 요청을 보낼 수 있습니다. 요청에서 할당 하려는 역할을 포함할 수 있으며 전역 관리자가 부여할 역할을 결정 합니다. 

이러한 요청을 수락 하거나 거부할지를 결정 하는 것이 전역 관리자의 결정입니다. 

> [!IMPORTANT]
> 7 일 마다 한 요청을 제출할 수 있습니다.

전역 관리자에 게 높은 권한을 요청 하려면:

1. Azure Portal에서 Azure Security Center를 엽니다.

1. "제한 된 정보를 볼 수 있습니다." 라는 배너가 표시 됩니다. 선택 합니다.

    :::image type="content" source="media/security-center-management-groups/request-tenant-permissions.png" alt-text="사용자에게 테넌트 전체의 사용 권한을 요청할 수 있음을 알리는 배너입니다.":::

1. 자세한 요청 양식에서 원하는 역할 및 이러한 권한이 필요한 이유에 대 한 근거를 선택 합니다.

    :::image type="content" source="media/security-center-management-groups/request-tenant-permissions-details.png" alt-text="Azure 전역 관리자의 테 넌 트 전체 사용 권한 요청에 대 한 세부 정보 페이지":::

1. **액세스 요청** 을 선택합니다.

    전역 관리자에 게 전자 메일을 보냅니다. 전자 메일에는 요청을 승인 하거나 거부할 수 있는 Security Center에 대 한 링크가 포함 되어 있습니다.

    :::image type="content" source="media/security-center-management-groups/request-tenant-permissions-email.png" alt-text="전역 관리자에 게 새 사용 권한을 전자 메일로 보내기":::

    전역 관리자가 **요청 검토** 를 선택 하 고 프로세스를 완료 한 후에는 요청 하는 사용자에 게 전자 메일이 발송 됩니다. 

## <a name="next-steps"></a>다음 단계

다음 관련 페이지에서 Security Center 권한에 대해 자세히 알아보세요.

- [Azure Security Center의 권한](security-center-permissions.md)