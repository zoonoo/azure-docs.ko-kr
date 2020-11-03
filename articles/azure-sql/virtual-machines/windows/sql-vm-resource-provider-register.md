---
title: IaaS 에이전트 확장 SQL Server 설치
description: SQL VM 리소스 공급자를 사용 하 여 Azure SQL Server 가상 컴퓨터를 등록 하면 Azure Marketplace 외부에 배포 된 SQL Server 가상 컴퓨터에 대 한 기능을 사용 하도록 설정 하 고, 규정 준수 및 관리 효율성을 향상 시킬 수 있습니다.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 10/30/2020
ms.author: mathoma
ms.reviewer: jroth
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 8099bf87410e8359894d8b40d7637451891ad664
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2020
ms.locfileid: "93286146"
---
# <a name="install-sql-server-iaas-agent-extension"></a>IaaS 에이전트 확장 SQL Server 설치
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

SQL VM 리소스 공급자를 사용 하 여 SQL Server VM를 등록 하면 [SQL Server IaaS 에이전트 확장이](sql-server-iaas-agent-extension-automate-management.md)설치 됩니다. 

이 문서에서는 SQL VM 리소스 공급자를 사용 하 여 단일 SQL Server VM를 등록 하는 방법을 설명 합니다. 또는 모든 SQL Server Vm을 [자동으로](sql-vm-resource-provider-automatic-registration.md) 등록 하거나 [대량으로 스크립팅할](sql-vm-resource-provider-bulk-register.md)수 있습니다.


## <a name="overview"></a>개요

리소스 공급자를 사용 하 여 등록 하면 [SQL Server IaaS 에이전트 확장이](sql-server-iaas-agent-extension-automate-management.md) 설치 되 고, 가상 머신 리소스와 _별도의_ 리소스인 구독 내에서 **SQL 가상 머신** _리소스_ 도 만들어집니다. 리소스 공급자에서 SQL Server VM 등록을 취소하면 **SQL 가상 머신** _리소스_ 가 제거되지만 실제 가상 머신은 삭제되지 않습니다.

Azure Portal을 통해 SQL Server VM Azure Marketplace 이미지를 배포하면 SQL Server VM이 자동으로 리소스 공급자에 등록됩니다. 그러나 Azure 가상 머신에서 SQL Server를 자동으로 설치 하거나 사용자 지정 VHD에서 Azure 가상 머신을 프로 비전 하도록 선택 하는 경우 sql IaaS 에이전트 확장을 설치 하려면 SQL VM 리소스 공급자를 사용 하 여 SQL Server VM을 등록 해야 합니다. 

SQL VM 리소스 공급자를 활용 하려면 먼저 리소스 공급자에 [구독을 등록](#register-subscription-with-rp)해야 합니다. 그러면 리소스 공급자는 해당 특정 구독 내에서 리소스를 만들 수 있습니다.

> [!IMPORTANT]
> SQL IaaS 에이전트 확장은 Azure Virtual Machines 내에서 SQL Server를 사용 하는 경우 고객에 게 선택적 혜택을 제공 하기 위한 express 용도의 데이터를 수집 합니다. Microsoft는 고객의 사전 동의가 없는 라이선스 감사에는이 데이터를 사용 하지 않습니다. 자세한 내용은 [SQL Server 개인 정보 취급 방침](/sql/sql-server/sql-server-privacy#non-personal-data) 을 참조 하세요.

## <a name="prerequisites"></a>필수 구성 요소

리소스 공급자에 SQL Server VM을 등록하려면 다음이 필요합니다. 

- [Azure 구독](https://azure.microsoft.com/free/).
- [SQL Server 2008](https://www.microsoft.com/sql-server/sql-server-downloads) 이상이 있는 Azure 리소스 모델 [Windows Server 2008 이상 가상 컴퓨터](../../../virtual-machines/windows/quick-create-portal.md) (또는 그 이상)가 공용 또는 Azure Government 클라우드에 배포 되었습니다. 
- [Azure CLI](/cli/azure/install-azure-cli) 또는 Azure PowerShell의 최신 버전 [(5.0 이상)](/powershell/azure/install-az-ps) 


## <a name="register-subscription-with-rp"></a>RP에 구독 등록

SQL Server VM을 SQL 리소스 공급자에 등록하려면 먼저 구독을 리소스 공급자에 등록해야 합니다. 그러면 SQL VM 리소스 공급자가 구독 내에서 리소스를 만들 수 있습니다.  Azure Portal, Azure CLI 또는 Azure PowerShell를 사용 하 여이 작업을 수행할 수 있습니다.

### <a name="azure-portal"></a>Azure portal

1. Azure Portal을 열고 **모든 서비스** 로 이동합니다. 
1. **구독** 으로 이동하여 원하는 구독을 선택합니다.  
1. **구독** 페이지에서 **리소스 공급자** 로 이동합니다. 
1. 필터에 **sql** 을 입력하여 SQL 관련 리소스 공급자를 표시합니다. 
1. 원하는 작업에 따라 **Microsoft.SqlVirtualMachine** 공급자에 대해 **등록** , **다시 등록** 또는 **등록 해제** 를 선택합니다. 

   ![공급자 수정](./media/sql-vm-resource-provider-register/select-resource-provider-sql.png)


### <a name="command-line"></a>명령 줄

Azure CLI 또는 Azure PowerShell를 사용 하 여 Azure 구독에 SQL VM 리소스 공급자를 등록 합니다. 

# <a name="azure-cli"></a>[Azure CLI](#tab/bash)

```azurecli-interactive
# Register the SQL VM resource provider to your subscription 
az provider register --namespace Microsoft.SqlVirtualMachine 
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

```powershell-interactive
# Register the SQL VM resource provider to your subscription
Register-AzResourceProvider -ProviderNamespace Microsoft.SqlVirtualMachine
```

---

## <a name="register-with-rp"></a>RP로 등록

[SQL Server IaaS 에이전트 확장](sql-server-iaas-agent-extension-automate-management.md#management-modes)에 대 한 세 가지 관리 모드가 있습니다. 

전체 관리 모드로 확장을 설치 하면 SQL Server 서비스가 다시 시작 되므로 먼저 경량 모드로 확장을 설치한 후 유지 관리 기간 동안 [전체로 업그레이드](#upgrade-to-full) 하는 것이 좋습니다. 

### <a name="lightweight-management-mode"></a>경량 관리 모드

Azure CLI 또는 Azure PowerShell를 사용 하 여 리소스 공급자에 SQL Server VM를 등록 하 고 SQL IaaS 확장을 경량 모드로 설치 합니다. 이는 SQL Server 서비스를 다시 시작 하지 않습니다. 그런 다음, 언제든지 전체 모드로 업그레이드할 수 있지만 이렇게 하면 SQL Server 서비스가 다시 시작되므로 예약된 유지 관리 기간이 도래할 때까지 기다리는 것이 좋습니다. 

SQL Server 라이선스 유형을 다음과 같이 제공합니다. 사용량에 따라 지불하려면 종량제(`PAYG`), 자체 라이선스를 사용하려면 Azure 하이브리드 혜택(`AHUB`), [무료 DR 복제본 라이선스](business-continuity-high-availability-disaster-recovery-hadr-overview.md#free-dr-replica-in-azure)를 활성화하려면 재해 복구(`DR`)입니다.

장애 조치 (Failover) 클러스터 인스턴스 및 다중 인스턴스 배포는 경량 모드의 SQL VM 리소스 공급자에만 등록할 수 있습니다. 

# <a name="azure-cli"></a>[Azure CLI](#tab/bash)

Azure CLI를 사용 하 여 SQL Server VM를 경량 모드로 등록 합니다. 

  ```azurecli-interactive
  # Register Enterprise or Standard self-installed VM in Lightweight mode
  az sql vm create --name <vm_name> --resource-group <resource_group_name> --location <vm_location> --license-type PAYG 
  ```


# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Azure PowerShell를 사용 하 여 SQL Server VM를 경량 모드로 등록 합니다.  


  ```powershell-interactive
  # Get the existing compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
  # Register SQL VM with 'Lightweight' SQL IaaS agent
  New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
    -LicenseType PAYG -SqlManagementType LightWeight  
  ```

---

### <a name="full-management-mode"></a>전체 관리 모드

SQL Server VM를 전체 모드로 등록 하면 SQL Server 서비스가 다시 시작 됩니다. 주의 하 여 계속 하세요. 

SQL Server VM를 전체 모드로 직접 등록 하 고 (SQL Server 서비스를 다시 시작 하는 경우) 다음 Azure PowerShell 명령을 사용 합니다. 

  ```powershell-interactive
  # Get the existing  Compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
        
  # Register with SQL VM resource provider in full mode
  New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -SqlManagementType Full
  ```

### <a name="noagent-management-mode"></a>에이전트 없음 관리 모드 

Windows Server 2008( _R2_ 가 아님)에 설치된 SQL Server 2008 및 2008 R2는 SQL VM 리소스 공급자에 [에이전트 없음 모드](sql-server-iaas-agent-extension-automate-management.md#management-modes)로 등록할 수 있습니다. 이 옵션은 규정 준수를 보장하고 제한된 기능으로 Azure Portal에서 SQL Server VM을 모니터링할 수 있도록 합니다.

**sqlLicenseType** 으로 `AHUB`, `PAYG`는 `DR`지정하고 **sqlImageOffer** 로 `SQL2008-WS2008` 또는 `SQL2008R2-WS2008`을 지정합니다. 

Windows Server 2008 인스턴스에 SQL Server 2008 또는 2008 r 2를 등록 하려면 다음 Azure CLI 또는 Azure PowerShell 코드 조각을 사용 합니다. 


# <a name="azure-cli"></a>[Azure CLI](#tab/bash)

Azure CLI를 사용 하 여 NoAgent 모드에서 SQL Server 2008 가상 컴퓨터를 등록 합니다. 

  ```azurecli-interactive
   az sql vm create -n sqlvm -g myresourcegroup -l eastus |
   --license-type PAYG --sql-mgmt-type NoAgent 
   --image-sku Enterprise --image-offer SQL2008-WS2008
 ```
 
 
Azure CLI를 사용 하 여 NoAgent 모드에서 SQL Server 2008 R2 가상 머신을 등록 합니다. 

  ```azurecli-interactive
   az sql vm create -n sqlvm -g myresourcegroup -l eastus |
   --license-type PAYG --sql-mgmt-type NoAgent 
   --image-sku Enterprise --image-offer SQL2008R2-WS2008
 ```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Azure PowerShell를 사용 하 여 NoAgent 모드에서 SQL Server 2008 가상 컴퓨터를 등록 합니다. 

  ```powershell-interactive
  # Get the existing compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
  New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
    -LicenseType PAYG -SqlManagementType NoAgent -Sku Standard -Offer SQL2008-WS2008
  ```
  

Azure PowerShell를 사용 하 여 NoAgent 모드에서 SQL Server 2008 R2 가상 머신을 등록 합니다. 

  ```powershell-interactive
  # Get the existing compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
  New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
    -LicenseType PAYG -SqlManagementType NoAgent -Sku Standard -Offer SQL2008R2-WS2008
  ```

---

## <a name="verify-mode"></a>확인 모드

Azure PowerShell를 사용 하 여 SQL Server IaaS 에이전트의 현재 모드를 볼 수 있습니다. 

```powershell-interactive
# Get the SqlVirtualMachine
$sqlvm = Get-AzSqlVM -Name $vm.Name  -ResourceGroupName $vm.ResourceGroupName
$sqlvm.SqlManagementType
```

## <a name="upgrade-to-full"></a>전체로 업그레이드  

*경량* IaaS 확장이 설치 된 SQL Server vm은 Azure Portal, Azure CLI 또는 Azure PowerShell를 사용 하 여 모드를 _full_ 로 업그레이드할 수 있습니다. _에이전트 없음_ 모드의 SQL Server VM은 OS를 Windows 2008 R2 이상으로 업그레이드한 후 _전체_ 모드로 업그레이드할 수 있습니다. 다운그레이드할 수는 없습니다. 그러려면 SQL Server VM을 SQL VM 리소스 공급자에서 [등록 취소](#unregister-from-rp)해야 합니다. 이렇게 하면 **SQL 가상 머신** _리소스_ 가 제거되지만 실제 가상 머신은 삭제되지 않습니다. 


### <a name="azure-portal"></a>Azure portal

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. [SQL 가상 머신](manage-sql-vm-portal.md#access-the-sql-virtual-machines-resource) 리소스로 이동합니다. 
1. SQL Server VM를 선택 하 고 **개요** 를 선택 합니다. 
1. SQL Server VM이 에이전트 없음 또는 경량 IaaS 모드인 경우 **SQL IaaS 확장에서는 라이선스 유형 및 버전 업데이트만 사용할 수 있습니다** 메시지를 선택합니다.

   ![포털에서 모드를 변경하기 위한 선택 항목](./media/sql-vm-resource-provider-register/change-sql-iaas-mode-portal.png)

1. **가상 머신에서 SQL Server 서비스를 다시 시작하는 데 동의합니다.** 확인란을 선택한 다음, **확인** 을 선택하여 IaaS 모드를 전체로 업그레이드합니다. 

    ![가상 머신에서 SQL Server 서비스를 다시 시작하는 데 동의하는 확인란](./media/sql-vm-resource-provider-register/enable-full-mode-iaas.png)

### <a name="command-line"></a>명령 줄

# <a name="azure-cli"></a>[Azure CLI](#tab/bash)

다음 Azure CLI 코드 조각을 실행합니다.

  ```azurecli-interactive
  # Update to full mode
  az sql vm update --name <vm_name> --resource-group <resource_group_name> --sql-mgmt-type full  
  ```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

다음 Azure PowerShell 코드 조각을 실행 합니다.

  ```powershell-interactive
  # Get the existing  Compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
        
  # Register with SQL VM resource provider in full mode
  Update-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -SqlManagementType Full
  ```

---

## <a name="verify-registration-status"></a>등록 상태 확인
Azure Portal, Azure CLI 또는 Azure PowerShell를 사용 하 여 SQL Server VM SQL VM 리소스 공급자에 이미 등록 되어 있는지 확인할 수 있습니다. 

### <a name="azure-portal"></a>Azure portal 

1. [Azure Portal](https://portal.azure.com)에 로그인합니다. 
1. [SQL Server vm](manage-sql-vm-portal.md)으로 이동 합니다.
1. 목록에서 SQL Server VM을 선택합니다. SQL Server VM이 여기에 나열되지 않은 경우 SQL VM 리소스 공급자에 등록되지 않았을 수 있습니다. 
1. **상태** 에서 값을 확인합니다. **상태** 가 **성공** 인 경우 SQL Server VM이 SQL VM 리소스 공급자에 등록되어 있는 것입니다. 

   ![SQL RP 등록 상태 확인](./media/sql-vm-resource-provider-register/verify-registration-status.png)

### <a name="command-line"></a>명령 줄

Azure CLI 또는 Azure PowerShell 중 하나를 사용 하 여 현재 SQL Server VM 등록 상태를 확인 합니다. 등록이 성공하면 `ProvisioningState`가 `Succeeded`로 표시됩니다. 

# <a name="azure-cli"></a>[Azure CLI](#tab/bash)


  ```azurecli-interactive
  az sql vm show -n <vm_name> -g <resource_group>
 ```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

  ```powershell-interactive
  Get-AzSqlVM -Name <vm_name> -ResourceGroupName <resource_group>
  ```

---

오류는 SQL Server VM이 리소스 공급자에 등록되지 않은 것을 나타냅니다. 


## <a name="unregister-from-rp"></a>RP에서 등록 취소

SQL VM 리소스 공급자를 사용 하 여 SQL Server VM 등록을 취소 하려면 Azure Portal 또는 Azure CLI를 사용 하 여 SQL 가상 머신 *리소스* 를 삭제 합니다. SQL 가상 컴퓨터 *리소스* 를 삭제 해도 SQL Server VM는 삭제 되지 않습니다. 그러나 *리소스* 를 제거하려고 할 때 실수로 가상 머신을 삭제할 수 있으므로 신중하게 단계를 수행해야 합니다. 

Sql VM 리소스 공급자를 사용 하 여 SQL 가상 머신의 등록을 취소 하려면 관리 모드를 전체로 다운 그레이드 해야 합니다. 

### <a name="azure-portal"></a>Azure portal

Azure Portal을 사용하여 SQL Server VM을 리소스 공급자에서 등록 취소하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. SQL VM 리소스로 이동 합니다. 
  
   ![SQL 가상 머신 리소스](./media/sql-vm-resource-provider-register/sql-vm-manage.png)

1. **삭제** 를 선택합니다. 

   ![위쪽 탐색에서 삭제를 선택 합니다.](./media/sql-vm-resource-provider-register/delete-sql-vm-resource-provider.png)

1. SQL 가상 컴퓨터의 이름을 입력 하 고 **가상 컴퓨터 옆에 있는 확인란의 선택을 취소** 합니다.

   ![실제 가상 컴퓨터를 삭제 하지 않도록 VM을 선택 취소 하 고 삭제를 선택 하 여 SQL VM 리소스 삭제를 계속 합니다.](./media/sql-vm-resource-provider-register/confirm-delete-of-resource-uncheck-box.png)

   >[!WARNING]
   > 가상 머신 이름 옆의 확인란 선택을 취소하지 않으면 가상 머신이 완전히 *삭제* 됩니다. 이 확인란 선택을 취소하여 SQL Server VM을 리소스 공급자에서 등록 취소하되 *실제 가상 머신은 삭제하지 않습니다*. 

1. **삭제** 를 선택 하 여 SQL SERVER VM 아닌 SQL 가상 컴퓨터 *리소스* 삭제를 확인 합니다. 

### <a name="command-line"></a>명령 줄

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
Azure CLI를 사용 하 여 리소스 공급자에서 SQL Server VM 등록을 취소 하려면 [az SQL VM delete](/cli/azure/sql/vm?view=azure-cli-latest&preserve-view=true#az-sql-vm-delete) 명령을 사용 합니다. 그러면 SQL Server VM *리소스가* 제거 되지만 가상 컴퓨터는 삭제 되지 않습니다. 


```azurecli-interactive
az sql vm delete 
  --name <SQL VM resource name> |
  --resource-group <Resource group name> |
  --yes 
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Azure PowerShell를 사용 하 여 리소스 공급자에서 SQL Server VM 등록을 취소 하려면 [AzSqlVM](/powershell/module/az.sqlvirtualmachine/remove-azsqlvm)명령을 사용 합니다. 그러면 SQL Server VM *리소스가* 제거 되지만 가상 컴퓨터는 삭제 되지 않습니다. 

```powershell-interactive
Remove-AzSqlVM -ResourceGroupName <resource_group_name> -Name <VM_name>
```

---



## <a name="next-steps"></a>다음 단계

자세한 내용은 다음 문서를 참조하세요. 

* [Windows VM에서 SQL Server 개요](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [Windows VM의 SQL Server FAQ](frequently-asked-questions-faq.md)  
* [Windows VM의 SQL Server 가격 책정 가이드](pricing-guidance.md)
* [Windows VM의 SQL Server 릴리스 정보](../../database/doc-changes-updates-release-notes.md)
