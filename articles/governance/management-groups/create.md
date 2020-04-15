---
title: 리소스를 구성하는 관리 그룹 만들기 - Azure 거버넌스
description: Azure 관리 그룹을 만들어 포털, Azure PowerShell 및 Azure CLI를 통해 여러 리소스를 관리하는 방법을 알아봅니다.
ms.date: 04/15/2020
ms.topic: conceptual
ms.openlocfilehash: 34815089367512c4aa54f148c118a669625d0ea3
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81381595"
---
# <a name="create-management-groups-for-resource-organization-and-management"></a>리소스 조직 및 관리에 대한 관리 그룹 만들기

관리 그룹은 여러 구독에서 액세스, 정책 및 규정 준수를 관리하는 데 도움이 되는 컨테이너입니다. 이러한 컨테이너를 만들어 [Azure 정책](../policy/overview.md) 및 [Azure 역할 기반 액세스 제어](../../role-based-access-control/overview.md)와 함께 사용할 수 있는 효과적이고 효율적인 계층을 구축합니다. 관리 그룹에 대한 자세한 내용은 [Azure 관리 그룹으로 리소스 구성](overview.md)을 참조하세요.

디렉터리에서 만드는 첫 번째 관리 그룹을 완료하려면 최대 15분이 소요될 수 있습니다. 디렉터리에 대해 Azure 내의 관리 그룹 서비스를 설정하기 위해 처음으로 실행되는 프로세스가 있습니다. 프로세스가 완료되면 알림이 수신됩니다. 자세한 내용은 [관리 그룹의 초기 설정을](./overview.md#initial-setup-of-management-groups)참조하십시오.

## <a name="create-a-management-group"></a>관리 그룹 만들기

테넌트의 모든 Azure AD 사용자는 해당 사용자에게 할당된 관리 그룹 쓰기 권한 없이 관리 그룹을 만들 수 있습니다. 이 새 관리 그룹은 루트 관리 그룹의 자식이 되며 작성자는 "소유자" 역할 할당을 받게 됩니다. 관리 그룹 서비스를 사용하면 루트 수준에서 역할 할당이 필요하지 않도록 이 기능을 사용할 수 있습니다. 사용자가 만들 때 루트 관리 그룹에 액세스할 수 없습니다. 관리 그룹을 사용하기 위해 Azure AD 글로벌 관리자를 찾는 데 어려움을 피하기 위해 루트에서 초기 관리 그룹을 만들 수 있습니다.  
수준.

포털, [리소스 관리자 템플릿,](../../azure-resource-manager/templates/deploy-to-tenant.md#create-management-group)PowerShell 또는 Azure CLI를 사용하여 관리 그룹을 만들 수 있습니다.

### <a name="create-in-portal"></a>포털에서 만들기

1. [Azure 포털에](https://portal.azure.com)로그인합니다.

1. **모든 서비스** > **관리 + 거버넌스를 선택합니다.**

1. **비용 관리 + 청구** 선택

1. 비용 관리 + 청구 - 관리 그룹 페이지에서 **관리 그룹을 선택합니다.**

1. **+ 관리 그룹 추가를**선택합니다.

   :::image type="content" source="./media/main.png" alt-text="관리 그룹 작업 페이지" border="false":::

1. 관리 그룹 ID 필드를 채웁니다.

   - **관리 그룹 ID**는 이 관리 그룹에 명령을 전송하는 데 사용되는 디렉터리 고유 식별자입니다. 이 식별자는 Azure 시스템 전체에서 이 그룹을 식별하는 데 사용되므로 만든 후에 편집할 수 없습니다. [루트 관리 그룹은](overview.md#root-management-group-for-each-directory) Azure Active Directory ID인 ID로 자동으로 만들어집니다. 다른 모든 관리 그룹에 대해 고유 ID를 할당합니다.
   - 표시 이름 필드는 Azure Portal 내에 표시되는 이름을 포함합니다. 별도의 표시 이름은 관리 그룹을 만들 때 선택적 필드이며 모든 경우 변경할 수 있습니다.  
     유용합니다.

   :::image type="content" source="./media/create_context_menu.png" alt-text="새 관리 그룹을 만들기 위한 옵션 창" border="false":::

1. **저장**을 선택합니다.

### <a name="create-in-powershell"></a>PowerShell에서 열기

PowerShell의 경우 [New-AzManagementGroup](/powershell/module/az.resources/new-azmanagementgroup) cmdlet을 사용하여 새 관리 그룹을 만듭니다.

```azurepowershell-interactive
New-AzManagementGroup -GroupName 'Contoso'
```

**GroupName**은 생성되는 고유 식별자입니다. 이 ID는 다른 명령에서 이 그룹을 참조하는 데 사용되며 나중에 변경할 수 없습니다.

관리 그룹이 Azure 포털 내에 다른 이름을 표시하도록 하려면 **DisplayName** 매개 변수를 추가합니다. 예를 들어 Contoso의 GroupName 및 "Contoso Group"의 표시 이름을 사용하여 관리 그룹을 만들려면 다음 cmdlet을 사용합니다.

```azurepowershell-interactive
New-AzManagementGroup -GroupName 'Contoso' -DisplayName 'Contoso Group'
```

앞의 예제에서 새 관리 그룹은 루트 관리 그룹 아래에 만들어집니다. 다른 관리 그룹을 부모로 지정하려면 **ParentId** 매개 변수를 사용합니다.

```azurepowershell-interactive
$parentGroup = Get-AzManagementGroup -GroupName Contoso
New-AzManagementGroup -GroupName 'ContosoSubGroup' -ParentId $parentGroup.id
```

### <a name="create-in-azure-cli"></a>Azure CLI에서 만들기

Azure CLI의 경우 [az 계정 관리 그룹 만들기](/cli/azure/account/management-group?view=azure-cli-latest#az-account-management-group-create) 명령을 사용하여 새 관리 그룹을 만듭니다.

```azurecli-interactive
az account management-group create --name Contoso
```

**이름은** 생성되는 고유 식별자입니다. 이 ID는 다른 명령에서 이 그룹을 참조하는 데 사용되며 나중에 변경할 수 없습니다.

관리 그룹이 Azure 포털 내에 다른 이름을 표시하도록 하려면 **표시 이름** 매개 변수를 추가합니다. 예를 들어 Contoso의 GroupName 및 "Contoso Group"의 표시 이름을 사용하여 관리 그룹을 만들려면 다음 명령을 사용합니다.

```azurecli-interactive
az account management-group create --name Contoso --display-name 'Contoso Group'
```

앞의 예제에서 새 관리 그룹은 루트 관리 그룹 아래에 만들어집니다. 다른 관리 그룹을 부모로 지정하려면 **부모** 매개 변수를 사용하고 상위 그룹의 이름을 제공합니다.

```azurecli-interactive
az account management-group create --name ContosoSubGroup --parent Contoso
```

## <a name="next-steps"></a>다음 단계

관리 그룹에 대해 자세히 알아보려면 다음 항목을 참조하세요.

- [관리 그룹을 만들어 Azure 리소스 구성](./create.md)
- [관리 그룹을 변경, 삭제 또는 관리하는 방법](./manage.md)
- [Azure PowerShell 리소스 모듈에서 관리 그룹 검토](/powershell/module/az.resources#resources)
- [REST API에서 관리 그룹 검토](/rest/api/resources/managementgroups)
- [Azure CLI에서 관리 그룹 검토](/cli/azure/account/management-group)
