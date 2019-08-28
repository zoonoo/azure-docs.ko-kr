---
title: SQL Server IaaS 에이전트 확장을 사용 하 여 Azure virtual machines에서 관리 작업 자동화 | Microsoft Docs
description: 이 문서에서는 특정 SQL Server 관리 작업을 자동화 하는 SQL Server IaaS 에이전트 확장을 관리 하는 방법을 설명 합니다. 여기에는 자동화 된 백업, 자동화 된 패치 및 Azure Key Vault 통합이 포함 됩니다.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
manager: jroth
editor: ''
tags: azure-resource-manager
ms.assetid: effe4e2f-35b5-490a-b5ef-b06746083da4
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/24/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 59b5138950e0fb94ea0051fa9cfe9aa75cd7d770
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/21/2019
ms.locfileid: "69877800"
---
# <a name="automate-management-tasks-on-azure-virtual-machines-by-using-the-sql-server-iaas-agent-extension"></a>SQL Server IaaS 에이전트 확장을 사용 하 여 Azure virtual machines에서 관리 작업 자동화
> [!div class="op_single_selector"]
> * [리소스 관리자](virtual-machines-windows-sql-server-agent-extension.md)
> * [클래식](../sqlclassic/virtual-machines-windows-classic-sql-server-agent-extension.md)

SQL Server IaaS 에이전트 확장(SqlIaasExtension)은 관리 작업을 자동화하기 위해 Azure 가상 머신에서 실행됩니다. 이 문서에서는 확장이 지 원하는 서비스에 대 한 개요를 제공 합니다. 또한이 문서에서는 확장의 설치, 상태 및 제거에 대 한 지침을 제공 합니다.

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

이 문서의 클래식 버전을 보려면 [SQL Server vm에 대 한 SQL Server IaaS 에이전트 확장 (클래식)](../sqlclassic/virtual-machines-windows-classic-sql-server-agent-extension.md)을 참조 하세요.

SQL Server IaaS 확장에는 다음과 같은 세 가지 관리 효율성 모드가 있습니다. 

- **전체** 모드에서는 모든 기능을 제공 하지만 SQL Server 및 시스템 관리자 권한이 다시 시작 되어야 합니다. 이 옵션은 기본적으로 설치 되는 옵션입니다. 단일 인스턴스를 사용 하 여 SQL Server VM를 관리 하는 데 사용 합니다. 

- **경량** 를 사용 하는 경우에는 SQL Server를 다시 시작할 필요가 없지만 라이선스 유형과 SQL Server 버전의 변경만 지원 합니다. 여러 인스턴스를 사용 하 여 Vm을 SQL Server 하거나 FCI (장애 조치 (failover) 클러스터 인스턴스)에 참여 하는 경우이 옵션을 사용 합니다. 

- **Noagent** 는 Windows Server 2008에 설치 된 SQL Server 2008 및 SQL Server 2008 R2 전용입니다. Windows Server 2008 이미지에이 모드를 사용 하는 방법에 대 한 자세한 내용은 [Windows server 2008 등록](virtual-machines-windows-sql-register-with-resource-provider.md#register-sql-server-2008-or-2008-r2-on-windows-server-2008-vms)을 참조 하세요. 

## <a name="supported-services"></a>지원되는 서비스
SQL Server IaaS 에이전트 확장은 다음 관리 작업을 지원합니다.

| 관리 기능 | 설명 |
| --- | --- |
| **자동화 된 백업 SQL Server** |는 VM에 기본 인스턴스 또는 [올바르게 설치](virtual-machines-windows-sql-server-iaas-faq.md#administration) 된 명명 된 SQL Server 인스턴스에 대 한 모든 데이터베이스의 백업 일정을 자동화 합니다. 자세한 내용은 [Azure virtual machines의 SQL Server에 대 한 자동화 된 백업 (리소스 관리자)](virtual-machines-windows-sql-automated-backup.md)을 참조 하세요. |
| **자동화 된 패치 SQL Server** |워크로드가 가장 많은 시간에 업데이트하지 않도록 VM에 대한 중요한 Windows 업데이트가 수행될 유지 관리 기간을 구성할 수 있습니다. 자세한 내용은 [Azure virtual machines의 SQL Server에 대 한 자동화 된 패치 (리소스 관리자)](virtual-machines-windows-sql-automated-patching.md)를 참조 하세요. |
| **Azure Key Vault 통합** |SQL Server VM에서 Azure Key Vault를 자동으로 설치하고 구성할 수 있습니다. 자세한 내용은 [Azure Virtual Machines의 SQL Server에 대 한 Azure Key Vault 통합 구성 (리소스 관리자)](virtual-machines-windows-ps-sql-keyvault.md)을 참조 하세요. |

SQL Server Iaas 에이전트 확장이 설치 되어 실행 되 면 관리 기능을 사용할 수 있습니다.

* Azure Portal에서 가상 컴퓨터의 SQL Server 패널에서 Azure Marketplace의 SQL Server 이미지에 Azure PowerShell 합니다.
* 확장의 수동 설치에 대 한 Azure PowerShell. 

## <a name="prerequisites"></a>필수 구성 요소
VM에서 SQL Server IaaS 에이전트 확장을 사용 하기 위한 요구 사항은 다음과 같습니다.

**운영 체제**:

* Windows Server 2008 R2
* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016
* Windows Server 2019 

**SQL Server 버전**:

* SQL Server 2008 
* SQL Server 2008 R2
* SQL Server 2012
* SQL Server 2014
* SQL Server 2016
* SQL Server 2017

**Azure PowerShell**:

* [최신 Azure PowerShell 명령 다운로드 및 구성](/powershell/azure/overview)

[!INCLUDE [updated-for-az.md](../../../../includes/updated-for-az.md)]


## <a name="change-management-modes"></a>변경 관리 모드

PowerShell을 사용 하 여 SQL Server IaaS 에이전트의 현재 모드를 볼 수 있습니다. 

  ```powershell-interactive
     #Get the SqlVirtualMachine
     $sqlvm = Get-AzResource -Name $vm.Name  -ResourceGroupName $vm.ResourceGroupName  -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines
     $sqlvm.Properties.sqlManagement
  ```

*경량* IaaS 확장이 설치 된 SQL Server vm은 Azure Portal를 사용 하 여 모드를 _full_ 로 업그레이드할 수 있습니다. _에이전트가 없는_ SQL Server Vm은 Windows 2008 R2 이상으로 업그레이드 한 후에 _전체_ 로 업그레이드할 수 있습니다. 다운 그레이드할 수는 없습니다. 이렇게 하려면 SQL IaaS 확장을 완전히 제거 하 고 다시 설치 해야 합니다. 

에이전트 모드를 full로 업그레이드 하려면 다음을 수행 합니다. 


# <a name="azure-portaltabazure-portal"></a>[Azure Portal](#tab/azure-portal)

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. [SQL 가상 컴퓨터](virtual-machines-windows-sql-manage-portal.md#access-the-sql-virtual-machines-resource) 리소스로 이동 합니다. 
1. SQL Server 가상 컴퓨터를 선택 하 고 **개요**를 선택 합니다. 
1. NoAgent 또는 lightweight IaaS 모드를 사용 하는 SQL Server Vm의 경우 **SQL IaaS 확장 메시지에서 유일한 라이선스 유형 및 버전 업데이트를 사용할 수** 있습니다 .를 선택 합니다.

   ![포털에서 모드를 변경 하기 위한 선택 항목](media/virtual-machines-windows-sql-server-agent-extension/change-sql-iaas-mode-portal.png)

1. **가상 컴퓨터에서 SQL Server 서비스를 다시 시작** 합니다. 확인란을 선택 하 고 **확인** 을 선택 하 여 IaaS 모드를 전체로 업그레이드 합니다. 

    ![가상 머신에서 SQL Server 서비스를 다시 시작 하는 것에 동의 하는 확인란](media/virtual-machines-windows-sql-server-agent-extension/enable-full-mode-iaas.png)

# <a name="az-clitabbash"></a>[AZ CLI](#tab/bash)

다음 Az CLI 코드 조각을 실행 합니다.

  ```azurecli-interactive
  # Update to full mode

  az sql vm update --name <vm_name> --resource-group <resource_group_name> --sql-mgmt-type full  
  ```

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)

다음 PowerShell 코드 조각을 실행 합니다.

  ```powershell-interactive
  # Update to full mode

  $SqlVm = Get-AzResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName <resource_group_name> -ResourceName <VM_name>
  $SqlVm.Properties.sqlManagement="Full"
  $SqlVm | Set-AzResource -Force
  ```

---


##  <a name="installation"></a>설치
SQL Server IaaS 확장은 [SQL VM 리소스 공급자](virtual-machines-windows-sql-register-with-resource-provider.md)를 사용 하 여 SQL Server VM를 등록할 때 설치 됩니다. 필요한 경우 전체 또는 경량 모드를 사용 하 여 SQL Server IaaS 에이전트를 수동으로 설치할 수 있습니다. 

Azure Portal를 사용 하 여 SQL Server 가상 머신 Azure Marketplace 이미지 중 하나를 프로 비전 할 때 전체 모드의 SQL Server IaaS 에이전트 확장이 자동으로 설치 됩니다. 

### <a name="install-in-full-mode"></a>전체 모드로 설치
SQL Server IaaS 확장의 전체 모드는 SQL Server VM의 단일 인스턴스에 대 한 완전 한 관리 기능을 제공 합니다. 기본 인스턴스가 있는 경우 확장은 기본 인스턴스와 함께 작동 하며 다른 인스턴스 관리를 지원 하지 않습니다. 기본 인스턴스가 없지만 명명 된 인스턴스는 하나만 있는 경우 명명 된 인스턴스를 관리 합니다. 기본 인스턴스가 없고 명명 된 인스턴스가 여러 개 있는 경우 확장을 설치 하지 못합니다. 

PowerShell을 사용 하 여 전체 모드로 SQL Server IaaS 에이전트를 설치 합니다.

  ```powershell-interactive
     #Get the existing compute VM
     $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
     #Register the SQL Server VM with 'Full' SQL Server IaaS agent
     New-AzResource -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
        -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines `
        -Properties @{virtualMachineResourceId=$vm.Id;sqlServerLicenseType='AHUB';sqlManagement='Full'}  
  
  ```

| 매개 변수 | 허용 되는 값                        |
| :------------------| :-------------------------------|
| **sqlServerLicenseType** | `AHUB` 또는 `PAYG`     |
| &nbsp;             | &nbsp;                          |


> [!NOTE]
> 확장이 아직 설치 되지 않은 경우 전체 확장을 설치 하면 SQL Server 서비스가 다시 시작 됩니다. SQL Server 서비스를 다시 시작 하지 않도록 하려면 제한 된 관리 기능을 사용 하 여 간단한 모드를 대신 설치 합니다.
> 
> SQL Server IaaS 확장을 업데이트 해도 SQL Server 서비스가 다시 시작 되지 않습니다. 

### <a name="install-on-a-vm-with-a-single-named-sql-server-instance"></a>단일 명명 된 SQL Server 인스턴스를 사용 하 여 VM에 설치
SQL Server IaaS 확장은 기본 인스턴스가 제거 되 고 IaaS 확장을 다시 설치 하는 경우 SQL Server의 명명 된 인스턴스에서 작동 합니다.

SQL Server의 명명 된 인스턴스를 사용 하려면 다음을 수행 합니다.
   1. Azure Marketplace에서 SQL Server VM를 배포 합니다. 
   1. [Azure Portal](https://portal.azure.com)에서 IaaS 확장을 제거 합니다.
   1. SQL Server VM 내에서 SQL Server 완전히 제거 합니다.
   1. SQL Server VM 내에 명명 된 인스턴스를 사용 하 여 SQL Server를 설치 합니다. 
   1. Azure Portal에서 IaaS 확장을 설치 합니다.  


### <a name="install-in-lightweight-mode"></a>경량 모드에서 설치
경량 모드는 SQL Server 서비스를 다시 시작 하지 않지만 제한 된 기능을 제공 합니다. 

PowerShell을 사용 하 여 경량 모드로 SQL Server IaaS 에이전트를 설치 합니다.


  ```powershell-interactive
     /#Get the existing  Compute VM
     $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
     #Register the SQL Server VM with the 'Lightweight' SQL IaaS agent
     New-AzResource -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
        -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines `
        -Properties @{virtualMachineResourceId=$vm.Id;sqlServerLicenseType='AHUB';sqlManagement='LightWeight'}  
  
  ```

| 매개 변수 | 허용 되는 값                        |
| :------------------| :-------------------------------|
| **sqlServerLicenseType** | `AHUB` 또는 `PAYG`     |
| &nbsp;             | &nbsp;                          |


## <a name="get-the-status-of-the-sql-server-iaas-extension"></a>SQL Server IaaS 확장의 상태를 가져옵니다.
확장이 설치되어 있는지 확인하는 한 가지 방법은 Azure Portal에서 에이전트 상태를 확인하는 것입니다. 가상 컴퓨터 창에서 **모든 설정** 을 선택 하 고 **확장**을 선택 합니다. **SqlIaasExtension** 확장이 나열되어야 합니다.

![Azure Portal에서 SQL Server IaaS 에이전트 확장의 상태](./media/virtual-machines-windows-sql-server-agent-extension/azure-rm-sql-server-iaas-agent-portal.png)

**AzVMSqlServerExtension** Azure PowerShell cmdlet을 사용할 수도 있습니다.

   ```powershell-interactive
   Get-AzVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"
   ```

이전 명령은 에이전트가 설치 되어 있는지 확인 하 고 일반 상태 정보를 제공 합니다. 다음 명령을 사용 하 여 자동화 된 백업 및 패치 적용에 대 한 특정 상태 정보를 가져올 수 있습니다.

   ```powershell-interactive
    $sqlext = Get-AzVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"
    $sqlext.AutoPatchingSettings
    $sqlext.AutoBackupSettings
   ```

## <a name="removal"></a>제거
Azure Portal에서 가상 컴퓨터 속성의 **확장** 창에 있는 줄임표를 선택 하 여 확장을 제거할 수 있습니다. 그런 다음, **삭제**를 선택합니다.

![Azure Portal에서 SQL Server IaaS 에이전트 확장 제거](./media/virtual-machines-windows-sql-server-agent-extension/azure-rm-sql-server-iaas-agent-uninstall.png)

**AzVMSqlServerExtension** PowerShell cmdlet을 사용할 수도 있습니다.

   ```powershell-interactive
    Remove-AzVMSqlServerExtension -ResourceGroupName "resourcegroupname" -VMName "vmname" -Name "SqlIaasExtension"
   ```

## <a name="next-steps"></a>다음 단계
확장에서 지 원하는 서비스 중 하나를 사용 하 여 시작 합니다. 자세한 내용은이 문서의 [지원 되는 서비스](#supported-services) 섹션에서 참조 하는 문서를 참조 하세요.

Azure Virtual Machines에서 SQL Server를 실행 하는 방법에 대 한 자세한 내용은 [azure Virtual Machines에서 SQL Server 이란?](virtual-machines-windows-sql-server-iaas-overview.md)를 참조 하세요.

