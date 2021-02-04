---
title: Azure Security Center에 대한 테넌트 수준 가시성 얻기 | Microsoft Docs
description: 이 문서에서는 Azure Active Directory 테 넌 트에 연결 된 모든 구독에 정책을 적용 하 여 규모에 따라 보안 환경을 관리 하는 방법을 설명 합니다.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: b85c0e93-9982-48ad-b23f-53b367f22b10
ms.service: security-center
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/04/2021
ms.author: memildin
ms.openlocfilehash: 13cbc2e9451221fef951eb6fac4c6b2772275122
ms.sourcegitcommit: f82e290076298b25a85e979a101753f9f16b720c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/04/2021
ms.locfileid: "99556420"
---
# <a name="organize-management-groups-subscriptions-and-tenant-wide-visibility"></a>관리 그룹, 구독 및 테 넌 트 전체 표시 유형 구성

이 문서에서는 Azure Active Directory 테 넌 트에 연결 된 모든 Azure 구독에 보안 정책을 적용 하 여 조직의 보안 상태를 대규모로 관리 하는 방법을 설명 합니다.

Azure AD 테 넌 트에 등록 된 모든 구독의 보안 상태를 파악 하려면 루트 관리 그룹에서 충분 한 읽기 권한이 있는 Azure 역할을 할당 해야 합니다.


## <a name="organize-your-subscriptions-into-management-groups"></a>구독을 관리 그룹으로 구성

### <a name="introduction-to-management-groups"></a>관리 그룹 소개

Azure 관리 그룹은 액세스, 정책 및 구독 그룹에 대한 보고를 효율적으로 관리하는 기능을 제공하고 루트 관리 그룹에서 작업을 수행하여 전체 Azure 공간을 효율적으로 관리합니다. 관리 그룹에 구독을 구성하고 거버넌스 정책을 관리 그룹에 적용합니다. 관리 그룹에 속하는 모든 구독은 관리 그룹에 적용되는 정책을 자동으로 상속합니다. 

각 Azure AD 테 넌 트에는 **루트 관리 그룹** 이라는 단일 최상위 관리 그룹이 제공 됩니다. 이 루트 관리 그룹은 모든 관리 그룹과 구독이 루트 관리 그룹까지 접히도록 만들어집니다. 이 그룹을 사용 하면 글로벌 정책과 Azure 역할 할당을 디렉터리 수준에서 적용할 수 있습니다. 

루트 관리 그룹은 다음 작업 중 하나를 실행할 때 자동으로 만들어집니다. 
- [Azure Portal](https://portal.azure.com)에서 **관리 그룹** 를 엽니다.
- API 호출을 사용 하 여 관리 그룹을 만듭니다.
- PowerShell을 사용하여 관리 그룹을 만듭니다. PowerShell 지침은 [리소스 및 조직 관리를 위한 관리 그룹 만들기](../governance/management-groups/create-management-group-portal.md)를 참조 하세요.

Security Center를 등록 하는 데 관리 그룹은 필요 하지 않지만 루트 관리 그룹이 만들어지도록 하나 이상의를 만드는 것이 좋습니다. 그룹이 만들어진 후 Azure AD 테넌트 아래의 모든 하위 구독이 연결됩니다. 


관리 그룹의 자세한 개요는 [Azure 관리 그룹으로 리소스 구성](../governance/management-groups/overview.md) 문서를 참조하세요.

### <a name="view-and-create-management-groups-in-the-azure-portal"></a>Azure Portal에서 관리 그룹 보기 및 만들기

1. [Azure Portal](https://portal.azure.com)에서 위쪽 표시줄의 검색 상자를 사용 하 여 **관리 그룹** 을 찾아 엽니다.

    :::image type="content" source="./media/security-center-management-groups/open-management-groups-service.png" alt-text="관리 그룹 액세스":::

    관리 그룹 목록이 표시 됩니다.

1. 관리 그룹을 만들려면 **관리 그룹 추가** 를 선택 하 고, 관련 세부 정보를 입력 한 다음, **저장** 을 선택 합니다.

    :::image type="content" source="media/security-center-management-groups/add-management-group.png" alt-text="Azure에 관리 그룹 추가":::

    - **관리 그룹 ID** 는 이 관리 그룹에 명령을 전송하는 데 사용되는 디렉터리 고유 식별자입니다. 이 식별자는 Azure 시스템 전체에서 이 그룹을 식별하는 데 사용되므로 만든 후에 편집할 수 없습니다. 
    - 표시 이름 필드는 Azure Portal 내에 표시되는 이름을 포함합니다. 별도 표시 이름은 관리 그룹을 만들 때 사용되는 선택적 필드로, 언제든지 변경할 수 있습니다.  


### <a name="add-subscriptions-to-a-management-group"></a>관리 그룹에 구독 추가
만든 관리 그룹에 구독을 추가할 수 있습니다.

1. **관리 그룹** 아래에서 구독에 대 한 관리 그룹을 선택 합니다.

    :::image type="content" source="./media/security-center-management-groups/management-group-subscriptions.png" alt-text="구독에 대 한 관리 그룹을 선택 합니다.":::

1. 그룹 페이지가 열리면 **자세히** 를 선택 합니다.

    :::image type="content" source="./media/security-center-management-groups/management-group-details-page.png" alt-text="관리 그룹의 세부 정보 페이지 열기":::

1. 그룹 세부 정보 페이지에서 **구독 추가** 를 선택한 다음 구독을 선택 하 고 **저장** 을 선택 합니다. 범위에 모든 구독을 추가할 때까지 반복 합니다.

    :::image type="content" source="./media/security-center-management-groups/management-group-add-subscriptions.png" alt-text="관리 그룹에 구독 추가":::
   > [!IMPORTANT]
   > 관리 그룹은 구독 및 자식 관리 그룹 모두를 포함할 수 있습니다. 사용자를 부모 관리 그룹에 할당 하면 자식 관리 그룹의 구독에 의해 액세스 권한이 상속 됩니다. 부모 관리 그룹에서 설정된 정책도 자식에서 상속됩니다. 


## <a name="grant-tenant-wide-permissions-to-yourself"></a>자신에 게 테 넌 트 전체 사용 권한 부여

**전역 관리자** 의 AD (Azure Active Directory) 역할이 있는 사용자는 테 넌 트 전반에 걸친 책임이 있지만 Azure Security Center에서 조직 차원의 정보를 볼 수 있는 Azure 권한은 없습니다. Azure AD 역할 할당은 Azure 리소스에 대 한 액세스 권한을 부여 하지 않으므로 권한 상승이 필요 합니다. 

> [!TIP]
> [모든 Azure 구독 및 관리 그룹을 관리 하기 위해 액세스 권한 상승](../role-based-access-control/elevate-access-global-admin.md)의 전역 관리자 역할에 대 한 권한 상승에 대해 자세히 알아보세요.

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

## <a name="assign-azure-roles-to-other-users"></a>다른 사용자에 게 Azure 역할 할당

### <a name="assign-azure-roles-to-users-through-the-azure-portal"></a>Azure Portal를 통해 사용자에 게 Azure 역할을 할당 합니다. 
1. [Azure Portal](https://portal.azure.com)에 로그인합니다. 
1. 관리 그룹을 보려면 Azure 주 메뉴에서 **모든 서비스** 를 선택한 다음, **관리 그룹** 을 선택합니다.
1.  관리 그룹을 선택 하 고 **세부 정보** 를 선택 합니다.

    :::image type="content" source="./media/security-center-management-groups/management-group-details.PNG" alt-text="관리 그룹 세부 정보 스크린샷":::

1. **액세스 제어 (IAM)** , **역할 할당** 을 차례로 선택 합니다.
1. **역할 할당 추가** 를 선택합니다.
1. 할당할 역할을 선택 하 고 사용자를 선택한 다음 **저장** 을 선택 합니다.  
   
   ![보안 읽기 권한자 역할 추가 스크린샷](./media/security-center-management-groups/asc-security-reader.png)

### <a name="assign-azure-roles-to-users-with-powershell"></a>PowerShell을 사용 하 여 사용자에 게 Azure 역할을 할당 합니다. 
1. [Azure PowerShell](/powershell/azure/install-az-ps)을 설치합니다.
2. 다음 명령을 실행합니다. 

    ```azurepowershell
    # Login to Azure as a Global Administrator user
    Connect-AzAccount
    ```

3. 메시지가 표시되면 전역 관리자 자격 증명으로 로그인합니다. 

    ![로그인 프롬프트 스크린샷](./media/security-center-management-groups/azurerm-sign-in.PNG)

4. 다음 명령을 실행하여 reader 역할 권한을 부여합니다.

    ```azurepowershell
    # Add Reader role to the required user on the Root Management Group
    # Replace "user@domian.com” with the user to grant access to
    New-AzRoleAssignment -SignInName "user@domain.com" -RoleDefinitionName "Reader" -Scope "/"
    ```
5. 역할을 제거하려면 다음 명령을 사용합니다. 

    ```azurepowershell
    Remove-AzRoleAssignment -SignInName "user@domain.com" -RoleDefinitionName "Reader" -Scope "/" 
    ```

## <a name="remove-elevated-access"></a>상승된 액세스 제거 

Azure 역할이 사용자에 게 할당 된 후에는 테 넌 트 관리자가 사용자 액세스 관리자 역할에서 자신을 제거 해야 합니다.

1. [Azure Portal](https://portal.azure.com) 또는 [Azure Active Directory 관리 센터](https://aad.portal.azure.com)에 로그인합니다.

2. 탐색 목록에서 **Azure Active Directory** 을 선택 하 고 **속성** 을 선택 합니다.

3. **Azure 리소스에 대 한 액세스 관리** 에서 스위치를 **아니요** 로 설정 합니다.

4. 설정을 저장 하려면 **저장** 을 선택 합니다.



## <a name="next-steps"></a>다음 단계
이 문서에서는 Azure Security Center에 대한 테넌트 수준 가시성을 얻는 방법을 배웠습니다. 관련 정보는 다음을 참조하세요.

- [Azure Security Center의 권한](security-center-permissions.md)