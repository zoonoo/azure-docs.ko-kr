---
title: SQL VM 리소스 공급자를 사용 하 여 Azure에서 SQL Server 가상 컴퓨터를 등록 | Microsoft Docs
description: 관리 효율성을 개선 하기 위해 SQL VM 리소스 공급자를 사용 하 여 SQL Server VM을 등록 합니다.
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
ms.date: 06/24/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: c78b5d71fffbf579b15f747c048bd65259039749
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/10/2019
ms.locfileid: "67786747"
---
# <a name="register-sql-server-virtual-machine-in-azure-with-the-sql-vm-resource-provider"></a>SQL VM 리소스 공급자를 사용 하 여 Azure에서 SQL Server 가상 컴퓨터 등록

이 문서에서는 SQL VM 리소스 공급자를 사용 하 여 Azure SQL Server 가상 컴퓨터 (VM)를 등록 하는 방법을 설명 합니다. 

Azure portal 통해 SQL Server VM marketplace 이미지를 자동으로 배포 리소스 공급자를 사용 하 여 SQL Server VM을 등록 합니다. 그러나 자체 Azure Marketplace에서 이미지를 선택 하는 대신 Azure Virtual Machine에서 SQL Server를 설치 하려는 경우 다음 등록 해야 SQL Server VM 위해 현재 보유 한 리소스 공급자를 사용 하 여:

-  **규정 준수** – Microsoft 제품 사용 약관을 사용 하는 고객 마다 합니다 [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/) SQL VM 리소스 공급자를 사용 하 여 등록 해야이 수행 하려면 Azure Hybrid Benefit-를 사용 하는 경우 Microsoft를 나타내야 합니다. 

-  **혜택 기능** -리소스 공급자를 사용 하 여 SQL Server VM을 등록의 잠금을 해제 [자동 패치](virtual-machines-windows-sql-automated-patching.md), [자동 백업](virtual-machines-windows-sql-automated-backup-v2.md), 모니터링 및 관리 효율성 기능 뿐만 아니라 [라이선싱](virtual-machines-windows-sql-ahb.md) 하 고 [edition](virtual-machines-windows-sql-change-edition.md) 유연성. 이전에 이러한 되었습니다만 사용할 수 있는 SQL Server VM 이미지를 Azure Marketplace에서.

고객에 게는 Microsoft 사용 하는 SQL VM 리소스를 사용 하 여 등록 나타냅니다 조건을 사용 하 여 SQL Server에 대 한 Azure 하이브리드 혜택을 통해 준수는 자체 Azure VM에서 SQL Server를 설치 하거나 SQL Server를 사용 하 여 사용자 지정 VHD에서 Azure VM을 프로 비전 공급자입니다. 

SQL VM 리소스 공급자를 활용 하려면 또한 구독을 사용 하 여 SQL VM 리소스 공급자를 등록 해야 합니다. Azure portal, Azure CLI 및 PowerShell을 사용 하 여이 수행할 수 있습니다. 

## <a name="prerequisites"></a>전제 조건

SQL Server VM에 리소스 공급자를 등록 하려면 다음이 필요 합니다. 

- [Azure 구독](https://azure.microsoft.com/free/).
- A [SQL Server VM](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision)합니다. 
- [Azure CLI](/cli/azure/install-azure-cli) 하 고 [PowerShell](/powershell/azure/new-azureps-module-az)합니다. 

## <a name="register-with-sql-vm-resource-provider"></a>SQL VM 리소스 공급자 등록
경우는 [SQL IaaS 확장](virtual-machines-windows-sql-server-agent-extension.md) Microsoft.SqlVirtualMachine/SqlVirtualMachines 형식의 메타 데이터 리소스 단순히 만드는 SQL VM 리소스 공급자를 사용 하 여 등록 한 다음 VM에 이미 설치 되어 있습니다. 다음은 SQL IaaS 확장을 VM에 이미 설치 되어 있으면 SQL VM 리소스 공급자를 등록 하려면 코드 조각입니다. 유형으로 SQL VM 리소스 공급자를 등록 하는 경우 SQL Server 라이선스를 제공 해야 ' 종 량 제 ' 또는 'AHUB'. 

다음 코드 조각을 사용 하 여 PowerShell을 사용 하 여 SQL Server VM을 등록 합니다.

  ```powershell-interactive
     // Get the existing  Compute VM
     $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
     // Register with SQL VM resource provider
     New-AzResource -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
        -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines `
        -Properties @{virtualMachineResourceId=$vm.Id;sqlLicenseType='AHUB'}  
  
  ```

SQL IaaS 확장을 VM에 설치 되어 있지 않으면, 다음 등록할 수 있습니다 SQL VM 리소스 공급자를 사용 하 여 간단한 SQL 관리 모드를 지정 하 여. 간단한 SQL 관리 모드에서 리소스 공급자가 자동으로 SQL VM에서 SQL IaaS 확장 설치 [경량 모드](virtual-machines-windows-sql-server-agent-extension.md#install-in-lightweight-mode) SQL Server를 확인 하 고 인스턴스 메타 데이터, SQL Server 서비스를 다시 시작 하지이 그러면 합니다. 유형으로 SQL VM 리소스 공급자를 등록 하는 경우 SQL Server 라이선스를 제공 해야 ' 종 량 제 ' 또는 'AHUB'. 

다음 코드 조각을 사용 하 여 PowerShell을 사용 하는 간단한 SQL 관리 모드에 SQL Server VM을 등록 합니다.

  ```powershell-interactive
     // Get the existing  Compute VM
     $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
     // Register SQL VM with 'Lightweight' SQL IaaS agent
     New-AzResource -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
        -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines `
        -Properties @{virtualMachineResourceId=$vm.Id;sqlLicenseType='AHUB';sqlManagement='LightWeight'}  
  
  ```
SQL VM 리소스 공급자에 등록 [경량 모드](virtual-machines-windows-sql-server-agent-extension.md#install-in-lightweight-mode) 는 규정 준수를 보장 하 고 융통성 있는 라이선스를 사용 하도록 설정 뿐만 아니라 내부 SQL Server 버전 업데이트 합니다. 경량 모드 에서만에서 SQL VM 리소스 공급자를 사용 하 여 장애 조치 클러스터 인스턴스 및 다중 인스턴스 배포를 등록할 수 있습니다. 업그레이드 하려면 Azure portal에서 찾을 지침을 따를 수 있습니다 [전체 모드](virtual-machines-windows-sql-server-agent-extension.md#full-mode-installation) 언제 든 지 SQL Server 다시 시작 하 여 포괄적인 관리 효율성 기능 집합을 사용 하도록 설정 합니다. 

## <a name="register-sql-server-2008r2-on-windows-server-2008-vms"></a>Windows Server 2008 Vm에서 SQL Server 2008/R2에 등록

SQL Server 2008 및 2008 R2 Windows Server 2008에 설치 된 리소스에서 제공 하는 SQL VM을 등록할 수는 [NoAgent](virtual-machines-windows-sql-server-agent-extension.md) 모드입니다. 이 옵션 규정 준수를 보장 하 고 SQL Server VM이 제한 된 기능을 사용 하 여 Azure portal에서 모니터링할 수 있도록 합니다.

다음 표에서 등록 중에 제공 된 매개 변수에 대해 허용 되는 값을 보여 줍니다.

| 매개 변수 | 허용 되는 값                                 |
| :------------------| :--------------------------------------- |
| **sqlLicenseType** | `'AHUB'`또는 `'PAYG'`                    |
| **sqlImageOffer**  | `'SQL2008-WS2008'` 또는 `'SQL2008R2-WS2008`|
| &nbsp;             | &nbsp;                                   |


에 SQL Server 2008 또는 2008 R2에서 Windows Server 2008 인스턴스를 등록 하려면 다음 Powershell 코드 조각을 사용 합니다.  

  ```powershell-interactive
     // Get the existing  Compute VM
     $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
    New-AzResource -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
      -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines `
      -Properties @{virtualMachineResourceId=$vm.Id;sqlLicenseType='AHUB'; `
       sqlManagement='NoAgent';sqlImageSku='Standard';sqlImageOffer='SQL2008R2-WS2008'}
  ```



## <a name="verify-registration-status"></a>등록 상태 확인
Azure portal, Azure CLI 또는 PowerShell을 사용 하 여 SQL VM 리소스 공급자를 사용 하 여 SQL Server에 이미 등록 되었습니다 경우 확인할 수 있습니다. 

# <a name="azure-portaltabazure-portal"></a>[Azure Portal](#tab/azure-portal)
Azure portal을 사용 하 여 등록 상태를 확인 하려면 다음을 수행 합니다.

1. [Azure 포털](https://portal.azure.com)할 수 있습니다. 
1. 로 이동 하 [SQL 가상 머신](virtual-machines-windows-sql-manage-portal.md)합니다.
1. 목록에서 SQL Server VM을 선택 합니다. SQL Server VM 여기 나열 되지 않으면, 가능성이 SQL VM 리소스 공급자를 사용 하 여 SQL Server VM에 등록 되지 않았습니다. 
1. 아래에 있는 값을 보려면 `Status`합니다. 경우 `Status = Succeeded`를 성공적으로 SQL VM 리소스 공급자를 사용 하 여 등록 된 SQL Server VM을 합니다. 

    ![SQL RP 등록을 사용 하 여 상태를 확인 합니다.](media/virtual-machines-windows-sql-register-with-rp/verify-registration-status.png)

# <a name="az-clitabbash"></a>[AZ CLI](#tab/bash)

다음 AZ CLI 명령 사용 하 여 현재 SQL Server VM 등록 상태를 확인 합니다. `ProvisioningState` 나타납니다 `Succeeded` 등록에 성공 합니다. 

  ```azurecli-interactive
  az sql vm show -n <vm_name> -g <resource_group>
  ```


# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)

다음 PowerShell cmdlet 사용 하 여 현재 SQL Server VM 등록 상태를 확인 합니다. `ProvisioningState` 나타납니다 `Succeeded` 등록에 성공 합니다. 

  ```powershell-interactive
  Get-AzResource -ResourceName <vm_name> -ResourceGroupName <resource_group> -ResourceType Microsoft.SqlVirtualMachine/sqlVirtualMachines
  ```
오류는 SQL Server VM 리소스 공급자에 등록 되지 않은 것을 나타냅니다. 

---

## <a name="register-sql-vm-resource-provider-with-subscription"></a>구독을 사용 하 여 SQL VM 리소스 공급자 등록 

SQL Server VM에 SQL VM 리소스 공급자를 등록 하려면 구독에 리소스 공급자를 등록 해야 합니다. Azure portal 또는 Azure CLI를 사용 하 여이 수행할 수 있습니다.

# <a name="azure-portaltabazure-portal"></a>[Azure Portal](#tab/azure-portal)


다음 단계는 Azure portal을 사용 하 여 Azure 구독에 SQL VM 리소스 공급자를 등록 됩니다. 

1. Azure Portal을 열고 **모든 서비스**로 이동합니다. 
1. **구독**으로 이동하고 관심 있는 구독을 선택합니다.  
1. 에 **구독** 페이지에서 이동할 **리소스 공급자**합니다. 
1. 필터에 `sql`을 입력하여 SQL 관련 리소스 공급자를 표시합니다. 
1. 원하는 작업에 따라 **Microsoft.SqlVirtualMachine** 공급자에 대해 ‘등록’, ‘다시 등록’ 또는 ‘등록 해제’를 선택합니다.    

   ![공급자 수정](media/virtual-machines-windows-sql-ahb/select-resource-provider-sql.png)

# <a name="az-clitabbash"></a>[AZ CLI](#tab/bash)

다음 코드 조각은 Azure 구독에 SQL VM 리소스 공급자를 등록 됩니다. 

```azurecli-interactive
# Register the new SQL VM resource provider to your subscription 
az provider register --namespace Microsoft.SqlVirtualMachine 
```

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)

다음 PowerShell 코드 조각은 Azure 구독에 SQL VM 리소스 공급자를 등록 됩니다.

```powershell-interactive
# Register the new SQL VM resource provider to your subscription
Register-AzResourceProvider -ProviderNamespace Microsoft.SqlVirtualMachine
```
---

## <a name="remarks"></a>설명

 - SQL VM 리소스 공급자는 ' 리소스 관리자 '를 사용 하 여 배포 하는 SQL Server Vm만 지원 합니다. SQL Server Vm '클래식 모델' 되지를 사용 하 여 배포 합니다. 
 - SQL VM 리소스 공급자는 공용 클라우드로 배포 하는 SQL Server Vm만 지원 합니다. 개인 또는 정부 클라우드에 대 한 배포가 지원 되지 않습니다. 

## <a name="next-steps"></a>다음 단계

자세한 내용은 다음 문서를 참조하세요. 

* [Windows VM에서 SQL Server 개요](virtual-machines-windows-sql-server-iaas-overview.md)
* [Windows VM의 SQL Server FAQ](virtual-machines-windows-sql-server-iaas-faq.md)
* [Windows VM의 SQL Server 가격 책정 가이드](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Windows VM의 SQL Server 릴리스 정보](virtual-machines-windows-sql-server-iaas-release-notes.md)


