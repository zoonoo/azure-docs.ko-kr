---
title: 관리 그룹을 만들어 Azure 리소스 구성
description: Azure 관리 그룹을 만들어 여러 리소스를 관리하는 방법을 알아봅니다.
author: rthorn17
manager: rithorn
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/10/2018
ms.author: rithorn
ms.openlocfilehash: 6a45ffa13ead40b72fd1a0a3c2696a6e6829a4d5
ms.sourcegitcommit: 5c00e98c0d825f7005cb0f07d62052aff0bc0ca8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/24/2018
ms.locfileid: "49956411"
---
# <a name="create-management-groups-for-resource-organization-and-management"></a>리소스 조직 및 관리에 대한 관리 그룹 만들기

관리 그룹은 여러 구독에서 액세스, 정책 및 규정 준수를 관리하는 데 도움이 되는 컨테이너입니다. 이러한 컨테이너를 만들어 [Azure 정책](../policy/overview.md) 및 [Azure 역할 기반 액세스 제어](../../role-based-access-control/overview.md)와 함께 사용할 수 있는 효과적이고 효율적인 계층을 구축합니다. 관리 그룹에 대한 자세한 내용은 [Azure 관리 그룹으로 리소스 구성](overview.md)을 참조하세요.

디렉터리에서 만드는 첫 번째 관리 그룹을 완료하려면 최대 15분이 소요될 수 있습니다. 디렉터리에 대해 Azure 내의 관리 그룹 서비스를 설정하기 위해 처음으로 실행되는 프로세스가 있습니다. 프로세스가 완료되면 알림이 수신됩니다.

## <a name="create-a-management-group"></a>관리 그룹 만들기

포털, PowerShell 또는 Azure CLI를 사용하여 관리 그룹을 만들 수 있습니다. 현재 Resource Manager 템플릿을 사용하여 관리 그룹을 만들 수 없습니다.

### <a name="create-in-portal"></a>포털에서 만들기

1. [Azure Portal](http://portal.azure.com)에 로그인합니다.

1. **모든 서비스** > **관리 그룹**을 선택합니다.

1. 주 페이지에서 **새 관리 그룹**을 선택합니다.

   ![기본 그룹](./media/main.png)

1. 관리 그룹 ID 필드를 채웁니다.

   - **관리 그룹 ID**는 이 관리 그룹에 명령을 전송하는 데 사용되는 디렉터리 고유 식별자입니다. 이 식별자는 Azure 시스템 전체에서 이 그룹을 식별하는 데 사용되므로 만든 후에 편집할 수 없습니다.
   - 표시 이름 필드는 Azure Portal 내에 표시되는 이름을 포함합니다. 별도 표시 이름은 관리 그룹을 만들 때 사용되는 선택적 필드로, 언제든지 변경할 수 있습니다.  

   ![생성](./media/create_context_menu.png)  

1. **저장**을 선택합니다.

### <a name="create-in-powershell"></a>PowerShell에서 열기

PowerShell 내에서 New-AzureRmManagementGroup cmdlet을 사용합니다.

```azurepowershell-interactive
New-AzureRmManagementGroup -GroupName 'Contoso'
```

**GroupName**은 생성되는 고유 식별자입니다. 이 ID는 다른 명령에서 이 그룹을 참조하는 데 사용되며 나중에 변경할 수 없습니다.

관리 그룹이 Azure Portal 내에서 다른 이름을 표시하도록 하려면 해당 문자열의 **DisplayName** 매개 변수를 추가합니다. 예를 들어, GroupName이 Contoso이고 표시 이름이 "Contoso Group"인 관리 그룹을 만들려면 다음 cmdlet을 사용합니다.

```azurepowershell-interactive
New-AzureRmManagementGroup -GroupName 'Contoso' -DisplayName 'Contoso Group' -ParentId 'ContosoTenant'
```

이 관리 그룹을 다른 관리 아래에 만들려면 **ParentId** 매개 변수를 사용합니다.

### <a name="create-in-azure-cli"></a>Azure CLI에서 만들기

Azure CLI에서 az account management-group create 명령을 사용합니다.

```azurecli-interactive
az account management-group create --name 'Contoso'
```

## <a name="next-steps"></a>다음 단계

관리 그룹에 대해 자세히 알아보려면 다음 항목을 참조하세요.

- [Azure 관리 그룹으로 리소스 구성](overview.md)
- [관리 그룹을 변경, 삭제 또는 관리하는 방법](manage.md)
- [Azure PowerShell 모듈 설치](https://www.powershellgallery.com/packages/AzureRM.ManagementGroups)
- [REST API 사양 검토](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/managementgroups/resource-manager/Microsoft.Management/preview)
- [Azure CLI 확장 설치](/cli/azure/extension?view=azure-cli-latest#az-extension-list-available)
