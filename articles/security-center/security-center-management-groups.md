---
title: 구독을 관리 그룹으로 구성 하 고 Azure Security Center 사용자에 게 역할 할당
description: Azure Security Center에서 Azure 구독을 관리 그룹으로 구성 하 고 조직의 사용자에 게 역할을 할당 하는 방법에 대해 알아봅니다.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 03/04/2021
ms.author: memildin
ms.openlocfilehash: 3508d508a19d6ce7fba4f3ef3a4fa545a58a167d
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102099389"
---
# <a name="organize-subscriptions-into-management-groups-and-assign-roles-to-users"></a>구독을 관리 그룹으로 구성 하 고 사용자에 게 역할 할당

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
이 문서에서는 구독을 관리 그룹으로 구성 하 고 사용자에 게 역할을 할당 하는 방법을 배웠습니다. 관련 정보는 다음을 참조하세요.

- [Azure Security Center의 권한](security-center-permissions.md)