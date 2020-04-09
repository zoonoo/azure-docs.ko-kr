---
title: SQL VM 리소스 공급자에 등록
description: Azure SQL Server 가상 컴퓨터를 SQL VM 리소스 공급자에 등록하여 Azure Marketplace 외부에 배포된 SQL Server VM에 대한 기능을 활성화하고 규정 준수 및 관리 용이성을 개선합니다.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 11/13/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: d9c1cff53d5d0f0385d3d61938c7fb6309efb7b1
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/09/2020
ms.locfileid: "80985391"
---
# <a name="register-a-sql-server-virtual-machine-in-azure-with-the-sql-vm-resource-provider"></a>SQL VM 리소스 공급자와 Azure에서 SQL Server 가상 컴퓨터를 등록합니다.

이 문서에서는 Azure에서 SQL VM 리소스 공급자와 함께 SQL Server 가상 컴퓨터(VM)를 등록하는 방법을 설명합니다. 리소스 공급자에 등록하면 구독 내에서 **SQL 가상 시스템** _리소스가_ 생성되며, 이 리소스는 가상 시스템 리소스와 는 별도의 리소스입니다. 리소스 공급자에서 SQL Server VM을 등록 취소하면 **SQL 가상 시스템** _리소스가_ 제거되지만 실제 가상 컴퓨터는 삭제되지 않습니다. 

Azure 포털을 통해 SQL Server VM Azure Marketplace 이미지를 배포하는 경우 리소스 공급자에 SQL Server VM이 자동으로 등록됩니다. 그러나 Azure 가상 컴퓨터에 SQL Server를 자체 설치하거나 사용자 지정 VHD에서 Azure 가상 컴퓨터를 프로비전하도록 선택한 경우 다음을 위해 SQL Server VM을 리소스 공급자에 등록해야 합니다.

- **기능 이점**: 리소스 공급자와 SQL Server VM을 등록하면 [자동화된 패치,](virtual-machines-windows-sql-automated-patching.md) [자동화된 백업,](virtual-machines-windows-sql-automated-backup-v2.md)모니터링 및 관리 기능의 잠금을 해제할 수 있습니다. 또한 [라이선싱](virtual-machines-windows-sql-ahb.md) 및 [에디션](virtual-machines-windows-sql-change-edition.md) 유연성을 잠금 해제합니다. 이전에는 이러한 기능을 Azure Marketplace에서 배포한 SQL Server VM 이미지에서만 사용할 수 있었습니다. 

- **규정 준수**: SQL VM 리소스 공급자에 등록하면 제품 용어에 지정된 대로 Azure 하이브리드 혜택이 활성화되었음을 Microsoft에 알리는 요구 사항을 충족하는 간소화된 방법을 제공합니다. 이 프로세스는 각 리소스에 대한 라이선스 등록 양식을 관리해야 하는 경우 무효화됩니다.  

- **무료 관리**: 세 가지 관리 용이성 모드에서 SQL VM 리소스 공급자에 등록하는 것은 완전히 무료입니다. 리소스 공급자또는 변경 관리 모드와 관련된 추가 비용은 없습니다. 

- **간소화된 라이선스 관리**: SQL VM 리소스 공급자에 등록하면 SQL Server 라이센스 관리가 간소화되고 [Azure 포털,](virtual-machines-windows-sql-manage-portal.md)Az CLI 또는 PowerShell을 사용하여 사용하도록 설정된 Azure 하이브리드 혜택으로 SQL Server VM을 빠르게 식별할 수 있습니다. 

   # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

   ```azurecli-interactive
   $vms = az sql vm list | ConvertFrom-Json
   $vms | Where-Object {$_.sqlServerLicenseType -eq "AHUB"}
   ```

   # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

   ```powershell-interactive
   Get-AzSqlVM | Where-Object {$_.LicenseType -eq 'AHUB'}
   ```

   ---

SQL VM 리소스 공급자를 활용하려면 먼저 [리소스 공급자에 구독을 등록해야](#register-subscription-with-rp)하며, 이 경우 리소스 공급자는 해당 특정 구독 내에서 리소스를 만들 수 있습니다.

SQL VM 리소스 공급자사용의 이점에 대한 자세한 내용은 [다음 channel9](https://channel9.msdn.com/Shows/Data-Exposed/Benefit-from-SQL-VM-Resource-Provider-when-self-installing-SQL-Server-on-Azure?WT.mc_id=dataexposed-c9-niner) 비디오를 시청하십시오. 

<iframe src="https://channel9.msdn.com/Shows/Data-Exposed/Benefit-from-SQL-VM-Resource-Provider-when-self-installing-SQL-Server-on-Azure/player" width="960" height="540" allowFullScreen frameBorder="0" title="Azure에 SQL Server를 자체 설치할 때 SQL VM 리소스 공급자의 이점 - Microsoft 채널 9 비디오"></iframe>


## <a name="prerequisites"></a>사전 요구 사항

SQL Server VM을 리소스 공급자에 등록하려면 다음이 필요합니다. 

- [Azure 구독](https://azure.microsoft.com/free/).
- 공용 또는 Azure 정부 클라우드에 배포된 Azure 리소스 모델 [SQL Server VM입니다.](virtual-machines-windows-portal-sql-server-provision.md) 
- [Azure CLI](/cli/azure/install-azure-cli) 또는 [PowerShell의](/powershell/azure/new-azureps-module-az)최신 버전입니다. 

## <a name="management-modes"></a>관리 모드

SQL [IaaS 확장이](virtual-machines-windows-sql-server-agent-extension.md) 아직 설치되지 않은 경우 SQL VM 리소스 공급자에 등록하면 등록 프로세스 중에 지정된 세 가지 관리 모드 중 하나에 SQL Server IaaS 확장을 자동으로 설치합니다. 관리 모드를 지정하지 않는 경우 SQL IaaS 확장이 전체 관리 모드로 설치됩니다.  

SQL IaaS 확장이 이미 수동으로 설치되어 있는 경우 이미 전체 관리 모드에 있으며 전체 모드의 리소스 공급자에 등록해도 SQL Server 서비스가 다시 시작되지 않습니다.

세 가지 관리 모드는 다음과 같습니다.

- **경량** 모드는 SQL Server를 다시 시작할 필요가 없지만 SQL Server의 라이센스 유형 및 버전 변경만 지원합니다. 여러 인스턴스가 있는 SQL Server VM에 이 옵션을 사용하거나 FCI(장애 조치 클러스터 인스턴스)에 참여합니다. 경량 모드를 사용할 때 메모리 나 CPU에 영향을 미치지 않으며 관련 비용이 없습니다. 먼저 경량 모드로 SQL Server VM을 등록한 다음 예약된 유지 관리 기간 동안 전체 모드로 업그레이드하는 것이 좋습니다.  

- **전체** 모드는 모든 기능을 제공하지만 SQL Server 및 시스템 관리자 권한을 다시 시작해야 합니다. 이 옵션은 SQL IaaS 확장을 수동으로 설치할 때 기본적으로 설치되는 옵션입니다. 단일 인스턴스로 SQL Server VM을 관리하는 데 사용합니다. 전체 모드는 메모리와 CPU에 최소한의 영향을 미치는 두 개의 Windows 서비스를 설치합니다 . 전체 관리 용이성 모드를 사용하는 데 드는 비용은 없습니다. 

- **NoAgent** 모드는 Windows Server 2008에 설치된 SQL Server 2008 및 SQL Server 2008 R2 전용입니다. NoAgent 모드를 사용할 때 메모리 또는 CPU에 영향을 미치지 않습니다. NoAgent 관리 용이성 모드를 사용하는 데 드는 비용은 없습니다. 

PowerShell을 사용하여 SQL Server IaaS 에이전트의 현재 모드를 볼 수 있습니다. 

  ```powershell-interactive
  # Get the SqlVirtualMachine
  $sqlvm = Get-AzSqlVM -Name $vm.Name  -ResourceGroupName $vm.ResourceGroupName
  $sqlvm.SqlManagementType
  ```

## <a name="register-subscription-with-rp"></a>RP로 구독 등록

SQL VM 리소스 공급자에 SQL Server VM을 등록하려면 먼저 리소스 공급자에 구독을 등록해야 합니다. 이렇게 하면 SQL VM 리소스 공급자가 구독 내에서 리소스를 만들 수 있습니다.  Azure 포털, Azure CLI 또는 PowerShell을 사용하여 이 작업을 수행할 수 있습니다.

### <a name="azure-portal"></a>Azure portal

1. Azure 포털을 열고 **모든 서비스로**이동합니다. 
1. **구독으로** 이동하여 관심 구독을 선택합니다.  
1. 구독 페이지에서 **리소스 공급자로** **이동합니다.** 
1. SQL 관련 리소스 공급자를 불러오기 위해 필터에 **sql을** 입력합니다. 
1. 원하는 작업에 따라 **등록,** **다시 등록**또는 **Microsoft.SqlVirtualMachine** 공급자에 대한 **등록 취소를** 선택합니다. 

![공급자 수정](media/virtual-machines-windows-sql-ahb/select-resource-provider-sql.png)


### <a name="command-line"></a>명령 줄

Az CLI 또는 PowerShell을 사용하여 SQL VM 리소스 공급자를 Azure 구독에 등록합니다. 

# <a name="az-cli"></a>[AZ CLI](#tab/bash)

```azurecli-interactive
# Register the SQL VM resource provider to your subscription 
az provider register --namespace Microsoft.SqlVirtualMachine 
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell-interactive
# Register the SQL VM resource provider to your subscription
Register-AzResourceProvider -ProviderNamespace Microsoft.SqlVirtualMachine
```

---

## <a name="register-sql-vm-with-rp"></a>RP로 SQL VM 등록 

### <a name="lightweight-management-mode"></a>경량 관리 모드

SQL [Server IaaS 에이전트 확장이](virtual-machines-windows-sql-server-agent-extension.md) VM에 설치되지 않은 경우 경량 모드에서 SQL VM 리소스 공급자에 등록하는 것이 좋습니다. 이렇게 하면 SQL IaaS 확장이 [경량 모드로](#management-modes) 설치되고 SQL Server 서비스가 다시 시작되지 않습니다. 그런 다음 언제든지 전체 모드로 업그레이드할 수 있지만 이렇게 하면 SQL Server 서비스가 다시 시작되므로 예약된 유지 관리 기간까지 기다리는 것이 좋습니다. 

SQL Server 라이선스 유형을 사용량당`PAYG`지불하는 종량제(종량제) 또는 자체`AHUB`라이선스를 사용하는 Azure 하이브리드 혜택() 또는 [무료 DR 복제본 라이선스를](virtual-machines-windows-sql-high-availability-dr.md#free-dr-replica-in-azure)활성화하는 재해 복구()로`DR`제공합니다.

장애 조치 클러스터 인스턴스 및 다중 인스턴스 배포는 경량 모드에서 SQL VM 리소스 공급자에만 등록할 수 있습니다. 

# <a name="az-cli"></a>[AZ CLI](#tab/bash)

Az CLI를 사용하면 경량 모드로 SQL Server VM을 등록합니다. 

  ```azurecli-interactive
  # Register Enterprise or Standard self-installed VM in Lightweight mode
  az sql vm create --name <vm_name> --resource-group <resource_group_name> --location <vm_location> --license-type PAYG 
  ```


# <a name="powershell"></a>[PowerShell](#tab/powershell)

PowerShell을 사용하면 경량 모드에서 SQL Server VM을 등록합니다.  


  ```powershell-interactive
  # Get the existing compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
  # Register SQL VM with 'Lightweight' SQL IaaS agent
  New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
    -LicenseType PAYG -SqlManagementType LightWeight  
  ```

---

### <a name="full-management-mode"></a>전체 관리 모드


SQL IaaS 확장이 이미 VM에 수동으로 설치되어 있는 경우 SQL Server 서비스를 다시 시작하지 않고 전체 모드로 SQL Server VM을 등록할 수 있습니다. **그러나 SQL IaaS 확장이 설치되지 않은 경우 전체 모드로 등록하면 SQL IaaS 확장을 전체 모드로 설치하고 SQL Server 서비스를 다시 시작합니다. 신중하게 진행하십시오.**


SQL Server VM을 전체 모드로 직접 등록하고 SQL Server 서비스를 다시 시작하려면 다음 PowerShell 명령을 사용합니다. 

  ```powershell-interactive
  # Get the existing  Compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
        
  # Register with SQL VM resource provider in full mode
  New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -SqlManagementType Full
  ```

### <a name="noagent-management-mode"></a>노에이전트 관리 모드 

Windows Server 2008(R2_아님)에_설치된 SQL Server 2008 및 2008 R2는 [NoAgent 모드에서](#management-modes)SQL VM 리소스 공급자에 등록할 수 있습니다. 이 옵션은 규정 준수를 보장하고 제한된 기능이 있는 Azure 포털에서 SQL Server VM을 모니터링할 수 있도록 합니다.

`AHUB`을 `PAYG` `DR` 을 지정하거나 **sqlLicenseType**로 `SQL2008-WS2008` `SQL2008R2-WS2008`지정하거나 **sqlImageOffer**로 . 

Windows Server 2008 인스턴스에서 SQL Server 2008 또는 2008 R2를 등록하려면 다음 Az CLI 또는 PowerShell 코드 조각을 사용합니다. 


# <a name="az-cli"></a>[AZ CLI](#tab/bash)

Az CLI를 통해 NoAgent 모드에서 SQL Server 2008 VM을 등록합니다. 

  ```azurecli-interactive
   az sql vm create -n sqlvm -g myresourcegroup -l eastus |
   --license-type PAYG --sql-mgmt-type NoAgent 
   --image-sku Enterprise --image-offer SQL2008-WS2008
 ```
 
 
Az CLI를 통해 노에이전트 모드에서 SQL Server 2008 R2 VM을 등록합니다. 

  ```azurecli-interactive
   az sql vm create -n sqlvm -g myresourcegroup -l eastus |
   --license-type PAYG --sql-mgmt-type NoAgent 
   --image-sku Enterprise --image-offer SQL2008R2-WS2008
 ```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

PowerShell을 사용하면 NoAgent 모드에서 SQL Server 2008 VM을 등록하십시오. 


  ```powershell-interactive
  # Get the existing compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
  New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
    -LicenseType PAYG -SqlManagementType NoAgent -Sku Standard -Offer SQL2008-WS2008
  ```
  
  PowerShell을 사용하면 NoAgent 모드에서 SQL Server 2008 R2 VM을 등록합니다. 


  ```powershell-interactive
  # Get the existing compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
  New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
    -LicenseType PAYG -SqlManagementType NoAgent -Sku Standard -Offer SQL2008R2-WS2008
  ```

---

## <a name="upgrade-to-full-management-mode"></a>전체 관리 모드로 업그레이드 

*간단한* IaaS 확장이 설치된 SQL Server VM은 Azure 포털, Az CLI 또는 PowerShell을 사용하여 모드를 _완전히_ 업그레이드할 수 있습니다. _NOAgent_ 모드의 SQL Server VM은 OS가 Windows 2008 R2 이상으로 업그레이드된 후 _전체로_ 업그레이드할 수 있습니다. 다운그레이드할 수 없으므로 SQL VM 리소스 공급자에서 SQL Server VM을 [등록 취소해야](#unregister-vm-from-rp) 합니다. 이렇게 하면 **SQL 가상 컴퓨터** _리소스가_제거되지만 실제 가상 컴퓨터는 삭제되지 않습니다. 

PowerShell을 사용하여 SQL Server IaaS 에이전트의 현재 모드를 볼 수 있습니다. 

  ```powershell-interactive
  # Get the SqlVirtualMachine
  $sqlvm = Get-AzSqlVM -Name $vm.Name  -ResourceGroupName $vm.ResourceGroupName
  $sqlvm.SqlManagementType
  ```

에이전트 모드를 전체로 업그레이드하려면 다음을 수행하십시오. 


### <a name="azure-portal"></a>Azure portal

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. [SQL 가상 컴퓨터](virtual-machines-windows-sql-manage-portal.md#access-the-sql-virtual-machines-resource) 리소스로 이동합니다. 
1. SQL Server 가상 컴퓨터를 선택하고 **개요를**선택합니다. 
1. NoAgent 또는 경량 IaaS 모드가 있는 SQL Server VM의 경우 유일한 라이센스 유형을 선택하고 **SQL IaaS 확장** 메시지와 함께 에디션 업데이트를 사용할 수 있습니다.

   ![포털에서 모드를 변경하기 위한 선택](media/virtual-machines-windows-sql-server-agent-extension/change-sql-iaas-mode-portal.png)

1. 가상 **컴퓨터 확인란에서 SQL Server 서비스를 다시 시작하기로 동의함을** 선택한 다음 IaaS 모드를 완전히 업그레이드하려면 **확인을** 선택합니다. 

    ![가상 컴퓨터에서 SQL Server 서비스를 다시 시작하기로 동의하는 확인란](media/virtual-machines-windows-sql-server-agent-extension/enable-full-mode-iaas.png)

### <a name="command-line"></a>명령 줄

# <a name="az-cli"></a>[AZ CLI](#tab/bash)

다음 Az CLI 코드 조각을 실행합니다.

  ```azurecli-interactive
  # Update to full mode
  az sql vm update --name <vm_name> --resource-group <resource_group_name> --sql-mgmt-type full  
  ```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

다음 PowerShell 코드 조각을 실행합니다.

  ```powershell-interactive
  # Get the existing  Compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
        
  # Register with SQL VM resource provider in full mode
  Update-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -SqlManagementType Full
  ```

---

## <a name="verify-registration-status"></a>등록 상태 확인
Azure 포털, Azure CLI 또는 PowerShell을 사용하여 SQL Server VM이 SQL VM 리소스 공급자에 이미 등록되었는지 확인할 수 있습니다. 

### <a name="azure-portal"></a>Azure portal 

1. [Azure Portal](https://portal.azure.com)에 로그인합니다. 
1. [SQL Server 가상 컴퓨터로](virtual-machines-windows-sql-manage-portal.md)이동합니다.
1. 목록에서 SQL Server VM을 선택합니다. SQL Server VM이 여기에 나열되지 않은 경우 SQL VM 리소스 공급자에 등록되지 않았을 수 있습니다. 
1. **상태**에서 값을 봅니다. **상태가** **성공하면**SQL Server VM이 SQL VM 리소스 공급자에 성공적으로 등록되었습니다. 

![SQL RP 등록으로 상태 확인](media/virtual-machines-windows-sql-register-with-rp/verify-registration-status.png)

### <a name="command-line"></a>명령 줄

Az CLI 또는 PowerShell을 사용하여 현재 SQL Server VM 등록 상태를 확인합니다. `ProvisioningState`등록에 `Succeeded` 성공했는지 표시됩니다. 

# <a name="az-cli"></a>[AZ CLI](#tab/bash)


  ```azurecli-interactive
  az sql vm show -n <vm_name> -g <resource_group>
 ```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

  ```powershell-interactive
  Get-AzSqlVM -Name <vm_name> -ResourceGroupName <resource_group>
  ```

---

오류가 SQL Server VM이 리소스 공급자에 등록되지 되었음을 나타냅니다. 


## <a name="unregister-vm-from-rp"></a>RP에서 VM 등록 취소

SQL VM 리소스 공급자에 SQL Server VM을 등록 취소하려면 Azure 포털 또는 Azure CLI를 사용하여 SQL 가상 시스템 *리소스를* 삭제합니다. SQL 가상 시스템 *리소스를* 삭제해도 SQL Server VM은 삭제되지 않습니다. 그러나 *리소스를*제거하려고 할 때 실수로 가상 컴퓨터를 삭제할 수 있으므로 주의하고 신중하게 단계를 따르십시오. 

SQL VM 리소스 공급자에 SQL VM을 등록 취소하는 것은 관리 모드를 완전히 다운그레이드해야 합니다. 

### <a name="azure-portal"></a>Azure portal

Azure 포털을 사용하여 리소스 공급자에 SQL Server VM을 등록 취소하려면 다음 단계를 따르십시오.

1. [Azure 포털에](https://portal.azure.com)로그인합니다.
1. SQL Server VM 리소스로 이동합니다. 
  
   ![SQL 가상 시스템 리소스](media/virtual-machines-windows-sql-manage-portal/sql-vm-manage.png)

1. **삭제를 선택합니다.** 

   ![SQL VM 리소스 공급자 삭제](media/virtual-machines-windows-sql-register-with-rp/delete-sql-vm-resource-provider.png)

1. SQL 가상 컴퓨터의 이름을 입력하고 **가상 컴퓨터 옆에 있는 확인란을 지웁니다.**

   ![SQL VM 리소스 공급자 삭제](media/virtual-machines-windows-sql-register-with-rp/confirm-delete-of-resource-uncheck-box.png)

   >[!WARNING]
   > 가상 시스템 이름 옆에 있는 확인란을 지우지 않으면 가상 시스템이 완전히 *삭제됩니다.* 리소스 공급자에서 SQL Server VM을 등록 취소하지만 *실제 가상 컴퓨터를 삭제하지 않도록 확인란을*지웁니까? 

1. SQL Server 가상 컴퓨터가 아닌 SQL 가상 시스템 *리소스의*삭제를 확인하려면 **삭제를** 선택합니다. 

### <a name="command-line"></a>명령 줄

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
Azure CLI를 사용하여 리소스 공급자에서 SQL Server 가상 컴퓨터를 등록 취소하려면 [az sql vm delete](/cli/azure/sql/vm?view=azure-cli-latest#az-sql-vm-delete) 명령을 사용합니다. 이렇게 하면 SQL Server 가상 시스템 *리소스가* 제거되지만 가상 컴퓨터는 삭제되지 않습니다. 


```azurecli-interactive
az sql vm delete 
  --name <SQL VM resource name> |
  --resource-group <Resource group name> |
  --yes 
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Azure CLI를 사용 하 여 리소스 공급자에서 SQL Server 가상 컴퓨터를 등록 취소 하려면 [New-AzSqlVM](/powershell/module/az.sqlvirtualmachine/new-azsqlvm)명령을 사용 합니다. 이렇게 하면 SQL Server 가상 시스템 *리소스가* 제거되지만 가상 컴퓨터는 삭제되지 않습니다. 

```powershell-interactive
Remove-AzSqlVM -ResourceGroupName <resource_group_name> -Name <VM_name>
```

---

## <a name="limitations"></a>제한 사항

SQL VM 리소스 공급자는 다음을 지원합니다.
- Azure 리소스 관리자를 통해 배포된 SQL 서버 VM입니다. 클래식 모델을 통해 배포된 SQL Server VM은 지원되지 않습니다. 
- 공용 또는 Azure 정부 클라우드에 배포된 SQL Server VM입니다. 다른 개인 또는 정부 클라우드에 대한 배포는 지원되지 않습니다. 


## <a name="frequently-asked-questions"></a>질문과 대답 

**Azure 마켓플레이스의 SQL Server 이미지에서 프로비전된 SQL Server VM을 등록해야 합니까?**

아니요. Microsoft는 Azure 마켓플레이스의 SQL Server 이미지에서 프로비전된 VM을 자동으로 등록합니다. Azure Marketplace의 SQL Server 이미지에서 VM이 *프로비전되지 않았고* SQL Server가 자체 설치되어 있는 경우에만 SQL VM 리소스 공급자에 등록해야 합니다.

**SQL VM 리소스 공급자를 모든 고객이 사용할 수 있나요?** 

예. 고객은 Azure Marketplace에서 SQL Server 이미지를 사용하지 않고 자체 설치된 SQL Server를 사용하지 않거나 사용자 지정 VHD를 가져온 경우 SQL VM 리소스 공급자에 SQL Server VM을 등록해야 합니다. 모든 유형의 구독(직접, 엔터프라이즈 계약 및 클라우드 솔루션 공급자)이 소유한 VM은 SQL VM 리소스 공급자에 등록할 수 있습니다.

**SQL Server VM에 SQL Server IaaS 확장이 이미 설치되어 있는 경우 SQL VM 리소스 공급자에 등록해야 합니까?**

SQL Server VM이 자체 설치되어 있고 Azure Marketplace의 SQL Server 이미지에서 프로비전되지 않은 경우 SQL Server IaaS 확장을 설치한 경우에도 SQL VM 리소스 공급자에 등록해야 합니다. SQL VM 리소스 공급자에 등록하면 Microsoft.SqlVirtualMachine 형식의 새 리소스가 만들어집니다. SQL Server IaaS 확장을 설치해도 해당 리소스가 생성되지 않습니다.

**SQL VM 리소스 공급자에 등록할 때 기본 관리 모드는 무엇입니까?**

SQL VM 리소스 공급자에 등록할 때기본 관리 모드가 *가득 찼습니다.* SQL VM 리소스 공급자에 등록할 때 SQL Server 관리 속성이 설정되지 않으면 모드가 완전 관리 용이성으로 설정되고 SQL Server 서비스가 다시 시작됩니다. 먼저 경량 모드로 SQL VM 리소스 공급자에 등록한 다음 유지 관리 기간 동안 전체로 업그레이드하는 것이 좋습니다. 

**SQL VM 리소스 공급자에 등록하기 위한 필수 구성 조건은 무엇입니까?**

SQL VM 리소스 공급자를 경량 모드 또는 에이전트 없음 모드에서 등록하기 위한 전제 조건은 없습니다. 전체 모드에서 SQL VM 리소스 공급자에 등록하기 위한 전제 조건은 SQL Server 서비스가 다시 시작되기 때문에 SQL Server IaaS 확장을 VM에 설치하는 것입니다. 

**SQL Server IaaS 확장이 VM에 설치되어 있지 않은 경우 SQL VM 리소스 공급자에 등록할 수 있습니까?**

예. VM에 SQL Server IaaS 확장이 설치되어 있지 않은 경우 간단한 관리 모드에서 SQL VM 리소스 공급자에 등록할 수 있습니다. 경량 모드에서SQL VM 리소스 공급자는 콘솔 앱을 사용하여 SQL Server 인스턴스의 버전과 버전을 확인합니다. 

SQL VM 리소스 공급자에 등록할 때 기본 SQL 관리 모드는 _전체_입니다. SQL VM 리소스 공급자에 등록할 때 SQL Management 속성이 설정되지 않은 경우 모드가 전체 관리 용이성으로 설정됩니다. 먼저 경량 모드로 SQL VM 리소스 공급자에 등록한 다음 유지 관리 기간 동안 전체로 업그레이드하는 것이 좋습니다. 

**SQL VM 리소스 공급자에 등록하면 내 VM에 에이전트가 설치됩니까?**

아니요. SQL VM 리소스 공급자에 등록하면 새 메타데이터 리소스만 생성됩니다. VM에 에이전트를 설치하지 않습니다.

SQL Server IaaS 확장은 전체 관리 용이성을 활성화하는 데만 필요합니다. 관리 용이성 모드를 경량에서 전체로 업그레이드하면 SQL Server IaaS 확장이 설치되고 SQL Server가 다시 시작됩니다.

**SQL Server VM 리소스 공급자에 등록하면 VM에서 SQL Server가 다시 시작됩니까?**

등록 하는 동안 지정 된 모드에 따라 달라 집니다. 경량 또는 NoAgent 모드를 지정하면 SQL Server 서비스가 다시 시작되지 않습니다. 그러나 관리 모드를 완전히 지정하거나 관리 모드를 비워 두면 SQL IaaS 확장을 전체 관리 모드로 설치하여 SQL Server 서비스가 다시 시작됩니다. 

**SQL VM 리소스 공급자에 등록할 때 경량 및 에이전트 없음 관리 모드의 차이점은 무엇입니까?** 

에이전트 없음 관리 모드는 Windows Server 2008의 SQL Server 2008 및 SQL Server 2008 R2에서만 사용할 수 있습니다. 이러한 버전에 사용할 수 있는 유일한 관리 모드입니다. 다른 모든 SQL Server 버전의 경우 사용 가능한 두 관리 용이성 모드는 가볍고 가득 차 있습니다. 

에이전트 없음 모드를 사용하려면 고객이 SQL Server 버전 및 에디션 속성을 설정해야 합니다. 경량 모드는 VM을 쿼리하여 SQL Server 인스턴스의 버전과 버전을 찾습니다.

**SQL Server 라이센스 유형을 지정하지 않고 SQL VM 리소스 공급자에 등록할 수 있습니까?**

아니요. SQL Server 라이센스 형식은 SQL VM 리소스 공급자에 등록할 때 선택적 속성이 아닙니다. 모든 관리 용이성 모드(에이전트 없음, 경량 및 전체)에서 SQL VM 리소스 공급자에 등록할 때 SQL Server 라이선스 유형을 종량제 또는 Azure 하이브리드 혜택으로 설정해야 합니다.

**SQL Server IaaS 확장을 에이전트 없음 모드에서 전체 모드로 업그레이드할 수 있습니까?**

아니요. 관리 용이성 모드를 완전 또는 경량으로 업그레이드하는 것은 에이전트 가 없는 모드에서는 사용할 수 없습니다. 이는 Windows Server 2008의 기술적 제한 사항입니다. 먼저 OS를 Windows Server 2008 R2 이상으로 업그레이드한 다음 전체 관리 모드로 업그레이드할 수 있습니다. 

**SQL Server IaaS 확장을 경량 모드에서 전체 모드로 업그레이드할 수 있습니까?**

예. PowerShell 또는 Azure 포털을 통해 관리 용이성 모드를 경량에서 전체로 업그레이드하는 것이 지원됩니다. SQL Server 서비스를 다시 시작해야 합니다.

**SQL Server IaaS 확장을 전체 모드에서 에이전트 없음 또는 경량 관리 모드로 다운그레이드할 수 있습니까?**

아니요. SQL Server IaaS 확장 관리 용이성 모드를 다운그레이드하는 것은 지원되지 않습니다. 관리 용이성 모드는 전체 모드에서 경량 또는 무에이전트 모드로 다운그레이드할 수 없으며, 경량 모드에서 에이전트 없음 모드로 다운그레이드할 수 없습니다. 

관리 용이성 모드를 전체 관리 가능성에서 변경하려면 SQL Server *리소스를* 삭제하여 SQL Server 리소스 공급자에서 SQL Server 가상 컴퓨터를 [등록 취소하고](#unregister-vm-from-rp) SQL Server VM을 다른 관리 모드에서 SQL VM 리소스 공급자에 다시 등록합니다.

**Azure 포털에서 SQL VM 리소스 공급자에 등록할 수 있습니까?**

아니요. Azure 포털에서 SQL VM 리소스 공급자에 등록할 수 없습니다. SQL VM 리소스 공급자에 등록하는 것은 Azure CLI 또는 PowerShell에서만 지원됩니다. 

**SQL Server를 설치하기 전에 SQL VM 리소스 공급자에 VM을 등록할 수 있습니까?**

아니요. VM에는 SQL VM 리소스 공급자에 성공적으로 등록하려면 하나 이상의 SQL Server(데이터베이스 엔진) 인스턴스가 있어야 합니다. VM에 SQL Server 인스턴스가 없는 경우 새 Microsoft.SqlVirtualMachine 리소스가 실패한 상태가 됩니다.

**SQL Server 인스턴스가 여러 개인 경우 SQL VM 리소스 공급자에 VM을 등록할 수 있습니까?**

예. SQL VM 리소스 공급자는 하나의 SQL Server(데이터베이스 엔진) 인스턴스만 등록합니다. SQL VM 리소스 공급자는 여러 인스턴스의 경우 기본 SQL Server 인스턴스를 등록합니다. 기본 인스턴스가 없는 경우 경량 모드로만 등록이 지원됩니다. 경량에서 전체 관리 용이성 모드로 업그레이드하려면 기본 SQL Server 인스턴스가 있어야 하거나 VM에 명명된 SQL Server 인스턴스가 하나만 있어야 합니다.

**SQL VM 리소스 공급자에 SQL Server 장애 조치 클러스터 인스턴스를 등록할 수 있습니까?**

예. Azure VM의 SQL Server 장애 조치 클러스터 인스턴스는 경량 모드에서 SQL VM 리소스 공급자에 등록할 수 있습니다. 그러나 SQL Server 장애 조치 클러스터 인스턴스는 전체 관리 용이성 모드로 업그레이드할 수 없습니다.

**Always On 가용성 그룹이 구성된 경우 VM을 SQL VM 리소스 공급자에 등록할 수 있습니까?**

예. Always On 가용성 그룹 구성에 참여하는 경우 Azure VM에 SQL VM 리소스 공급자를 등록하는 데는 제한이 없습니다.

**SQL VM 리소스 공급자에 등록하거나 전체 관리 용이성 모드로 업그레이드하는 데 드는 비용은 얼마입니까?**
없음 SQL VM 리소스 공급자에 등록하거나 세 가지 관리 용이성 모드 중 어느 것을 사용하는 것과 관련된 수수료는 없습니다. 리소스 공급자와 SQL Server VM을 관리하는 것은 완전히 무료입니다. 

**다양한 관리 용이성 모드를 사용할 때 성능에 미치는 영향은 무엇입니까?**
*NoAgent* 및 *경량* 관리 용이성 모드를 사용할 때는 영향을 미치지 않습니다. OS에 설치된 두 서비스에서 *전체* 관리 용이성 모드를 사용할 때 최소한의 영향이 있습니다. 작업 관리자를 통해 모니터링하고 기본 제공 Windows 서비스 콘솔에서 볼 수 있습니다. 

두 서비스 이름은 다음과 같습니다.
- `SqlIaaSExtensionQuery`(표시 이름 `Microsoft SQL Server IaaS Query Service`- )
- `SQLIaaSExtension`(표시 이름 `Microsoft SQL Server IaaS Agent`- )


## <a name="next-steps"></a>다음 단계

자세한 내용은 다음 문서를 참조하세요. 

* [Windows VM의 SQL 서버 개요](virtual-machines-windows-sql-server-iaas-overview.md)
* [Windows VM의 SQL 서버에 대한 자주 묻는 질문](virtual-machines-windows-sql-server-iaas-faq.md)
* [Windows VM의 SQL 서버에 대한 가격 지침](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Windows VM에서 SQL 서버에 대한 릴리스 정보](virtual-machines-windows-sql-server-iaas-release-notes.md)
