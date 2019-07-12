---
title: SQL Server IaaS 에이전트 확장을 사용 하 여 Azure Virtual Machines에서 관리 작업 자동화 | Microsoft Docs
description: 이 문서에서는 특정 SQL Server 관리 작업을 자동화하는 SQL Server 에이전트 확장을 관리하는 방법을 설명합니다. 여기에는 자동화된 Backup, 자동화된 패치 적용 및 Azure Key Vault 통합이 포함됩니다.
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
ms.openlocfilehash: 41023103dc30d16f599e847f9d324bc7bb4be11c
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67798047"
---
# <a name="automate-management-tasks-on-azure-virtual-machines-with-the-sql-server-iaas-agent-extension"></a>SQL Server IaaS 에이전트 확장을 사용 하 여 Azure Virtual Machines에서 관리 작업 자동화
> [!div class="op_single_selector"]
> * [리소스 관리자](virtual-machines-windows-sql-server-agent-extension.md)
> * [클래식](../sqlclassic/virtual-machines-windows-classic-sql-server-agent-extension.md)

SQL Server IaaS 에이전트 확장(SqlIaasExtension)은 관리 작업을 자동화하기 위해 Azure 가상 머신에서 실행됩니다. 이 문서에서는 개요 및 설치, 상태 및 제거에 대 한 지침 뿐만 아니라 확장에서 지 원하는 서비스를 제공 합니다.

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

이 문서의 클래식 버전을 보려면 [SQL Server VM에 대한 SQL Server 에이전트 확장 클래식](../sqlclassic/virtual-machines-windows-classic-sql-server-agent-extension.md)을 참조하세요.

SQL IaaS 확장에 대 한 SQL 관리 효율성 모드가 세 가지가 있습니다. **전체**하십시오 **경량**, 및 **NoAgent**합니다. 

- **전체** 모드에서는 모든 기능을 제공 하지만 SQL Server 권한과 SA를 다시 시작 해야 합니다. 기본적으로 설치 되 고 단일 인스턴스를 사용 하 여 SQL Server VM을 관리 하기 위한 사용 해야 하는 옵션입니다. 

- **경량** SQL Server를 다시 시작 필요 하지 않지만 라이선스 유형 및 버전의 SQL Server 변경만 지원 합니다. 이 옵션에 여러 인스턴스를 사용 하 여 SQL Server Vm에 사용 되는 또는 장애 조치 클러스터 인스턴스 (FCI)에 참여 해야 합니다. 

- **NoAgent** SQL Server 2008 및 Windows Server 2008에 설치 된 SQL Server 2008 R2에 대 한 전용입니다. 활용 하는 방법은 `NoAgent` 모드를 Windows Server 2008 이미지를 참조 하세요 [Windows Server 2008 등록](virtual-machines-windows-sql-register-with-resource-provider.md#register-sql-server-2008r2-on-windows-server-2008-vms)합니다. 

## <a name="supported-services"></a>지원되는 서비스
SQL Server IaaS 에이전트 확장은 다음 관리 작업을 지원합니다.

| 관리 기능 | 설명 |
| --- | --- |
| **SQL 자동화된 Backup** |예약을 자동화 합니다 모든 데이터베이스에 대 한 백업에 대 한 기본 인스턴스 또는 [제대로 설치 된](virtual-machines-windows-sql-server-iaas-faq.md#administration) VM에서 SQL Server의 명명 된 인스턴스. 자세한 내용은 [Azure Virtual Machines에서 SQL Server에 대한 자동화된 백업(리소스 관리자)](virtual-machines-windows-sql-automated-backup.md)을 참조하세요. |
| **SQL 자동화된 패치** |워크로드가 가장 많은 시간에 업데이트하지 않도록 VM에 대한 중요한 Windows 업데이트가 수행될 유지 관리 기간을 구성할 수 있습니다. 자세한 내용은 [Azure Virtual Machines에서 SQL Server에 대한 자동화된 패치(리소스 관리자)](virtual-machines-windows-sql-automated-patching.md)를 참조하세요. |
| **Azure Key Vault 통합** |이 서비스를 통해 SQL Server VM에서 Azure Key Vault를 자동으로 설치 및 구성할 수 있습니다. 자세한 내용은 [Azure VM에서 SQL Server에 대한 Azure Key Vault 통합 구성(리소스 매니저)](virtual-machines-windows-ps-sql-keyvault.md)을 참조하세요. |

SQL Server IaaS 에이전트 확장을 설치하고 실행하면 Azure Portal에 있는 가상 머신의 SQL Server 패널, SQL Server Marketplace 이미지에 대한 Azure PowerShell, 확장 수동 설치를 위한 Azure PowerShell을 통해 이러한 관리 기능을 사용할 수 있습니다. 

## <a name="prerequisites"></a>필수 구성 요소
VM에서 SQL Server IaaS 에이전트 확장을 사용하기 위한 요구 사항:

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


## <a name="change-management-modes"></a>관리 모드를 변경 합니다.

PowerShell을 사용 하 여 SQL IaaS 에이전트의 현재 모드를 볼 수 있습니다. 

  ```powershell-interactive
     //Get the SqlVirtualMachine
     $sqlvm = Get-AzResource -Name $vm.Name  -ResourceGroupName $vm.ResourceGroupName  -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines
     $sqlvm.Properties.sqlManagement
  ```

있는 SQL Server Vm에 대 한 합니다 *NoAgent* 또는 *경량* IaaS 확장을 설치한 상태 모드를 업그레이드할 수 있습니다 *전체* Azure portal을 사용 합니다. 다운 그레이드-이렇게 하려면 완전히 SQL IaaS 확장을 제거 하 고 다시 설치 해야 하는 것이 불가능 합니다. 

에이전트 업그레이드 모드로 *전체*, 다음을 수행 합니다. 

1. [Azure 포털](https://portal.azure.com)할 수 있습니다.
1. 로 이동 하 [SQL 가상 머신](virtual-machines-windows-sql-manage-portal.md#access-sql-virtual-machine-resource) 리소스입니다. 
1. SQL Server 가상 머신을 선택 및 선택 **개요**합니다. 
1. 사용 하 여 SQL Vm에 대 한 합니다 *NoAgent* 또는 *경량* IaaS 모드에 대 한 메시지를 선택 합니다. **만 라이선스 유형 및 버전 업데이트는 SQL IaaS 확장을 사용 하 여 사용할 수 있는**합니다.

    ![포털에서 모드 변경이 시작](media/virtual-machines-windows-sql-server-agent-extension/change-sql-iaas-mode-portal.png)

1. 에 동의 **SQL Server 서비스를 다시 시작** 확인란을 선택 하 여 선택한 후 **확인** 를 'full' IaaS 모드를 업그레이드 합니다. 

    ![IaaS 확장에 대 한 전체 관리를 사용 하도록 설정](media/virtual-machines-windows-sql-server-agent-extension/enable-full-mode-iaas.png)

##  <a name="installation"></a>설치
SQL IaaS 확장은 사용 하 여 SQL Server VM을 등록할 때에 설치 합니다 [SQL VM 리소스 공급자](virtual-machines-windows-sql-register-with-resource-provider.md#register-with-sql-vm-resource-provider)합니다. 그러나 필요한 경우 SQL IaaS 에이전트를 설치할 수도 있습니다 사용 하 여 수동으로 *전체* 하거나 *경량* 모드 설치 합니다. 

합니다 *전체* SQL Server IaaS 에이전트 확장은 Azure portal을 사용 하 여 SQL Server 가상 머신 갤러리 이미지 중 하나를 프로 비전 하는 경우 자동으로 설치 됩니다. 

### <a name="full-mode-installation"></a>전체 모드 설치
합니다 *전체* SQL IaaS 확장은 SQL Server VM에서 단일 인스턴스에 대 한 전체 관리 효율성을 제공 합니다. 기본 인스턴스의 경우 기본 인스턴스를 확장 한 다음 작동 하 고 다른 인스턴스 관리를 지원 하지 않습니다. 기본 인스턴스가 없는 하지만 명명 된 인스턴스를 하나만 있으면 명명된 된 인스턴스를 관리 합니다. 기본 인스턴스가 없는 경우 명명 된 인스턴스가 여러 개 확장 설치에 실패 합니다. 

설치 합니다 *전체* 모드 SQL IaaS의 SQL Server 서비스를 다시 시작 됩니다. SQL Server 서비스를 다시 시작을 방지 하려면 설치 합니다 *경량* 제한 된 관리 효율성을 대신 사용 하 여 모드입니다. 

SQL IaaS 에이전트를 설치 *전체* PowerShell을 사용 하 여 모드:

  ```powershell-interactive
     // Get the existing  Compute VM
     $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
     // Register SQL VM with 'Full' SQL IaaS agent
     New-AzResource -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
        -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines `
        -Properties @{virtualMachineResourceId=$vm.Id;sqlServerLicenseType='AHUB';sqlManagement='Full'}  
  
  ```

| 매개 변수 | 허용 되는 값                        |
| :------------------| :-------------------------------|
| **sqlServerLicenseType** | `'AHUB'`또는 `'PAYG'`     |
| &nbsp;             | &nbsp;                          |


> [!WARNING]
> - 확장이 이미 설치 되어 있지 않으면를 설치 합니다 **전체** 확장 SQL Server 서비스를 다시 시작 합니다. 사용 하 여 **경량** 모드를 SQL Server 서비스를 다시 시작 되지 않도록 합니다. 
> - SQL IaaS 확장을 업데이트 해도 SQL Server 서비스를 다시 시작 하지 않습니다. 

#### <a name="install-on-a-vm-with-a-single-named-sql-server-instance"></a>단일 명명 된 SQL Server 인스턴스를 사용 하 여 VM에 설치
SQL IaaS 확장 작동 명명된 된 인스턴스를 사용 하 여 SQL Server의 기본 인스턴스가 제거 되 고 IaaS 확장을 다시 설치 하는 경우.

SQL Server의 명명된 된 인스턴스를 사용 하려면 다음을 수행 합니다.
   1. Marketplace에서 SQL Server VM을 배포 합니다. 
   1. 내에서 IaaS 확장을 제거 합니다 [Azure portal](https://portal.azure.com)합니다.
   1. SQL Server SQL Server VM 내에서 완전히 제거 합니다.
   1. SQL Server VM 내에서 명명된 된 인스턴스를 사용 하 여 SQL Server를 설치 합니다. 
   1. Azure portal 내에서 IaaS 확장을 설치 합니다.  


### <a name="install-in-lightweight-mode"></a>경량 모드에서 설치
경량 모드에 SQL Server 서비스를 다시 시작 되지 않습니다 하지만 제한 된 기능을 제공 합니다. 

SQL IaaS 에이전트를 설치 *경량* PowerShell을 사용 하 여 모드:


  ```powershell-interactive
     // Get the existing  Compute VM
     $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
     // Register SQL VM with 'Lightweight' SQL IaaS agent
     New-AzResource -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
        -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines `
        -Properties @{virtualMachineResourceId=$vm.Id;sqlServerLicenseType='AHUB';sqlManagement='LightWeight'}  
  
  ```

| 매개 변수 | 허용 되는 값                        |
| :------------------| :-------------------------------|
| **sqlServerLicenseType** | `'AHUB'`또는 `'PAYG'`     |
| &nbsp;             | &nbsp;                          |


## <a name="get-status-of-sql-iaas-extension"></a>SQL IaaS 확장의 상태를 가져옵니다.
확장이 설치되어 있는지 확인하는 한 가지 방법은 Azure Portal에서 에이전트 상태를 확인하는 것입니다. 가상 머신 창에서 **모든 설정**을 선택하고 **확장**을 클릭합니다. **SqlIaasExtension** 확장이 나열되어야 합니다.

![Azure Portal에서 SQL Server IaaS 에이전트 확장](./media/virtual-machines-windows-sql-server-agent-extension/azure-rm-sql-server-iaas-agent-portal.png)

**Get-AzVMSqlServerExtension** Azure PowerShell cmdlet을 사용할 수도 있습니다.

   ```powershell-interactive
   Get-AzVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"
   ```

이전 명령은 에이전트가 설치되어 있는지 확인하고 일반적인 상태 정보를 제공합니다. 다음 명령을 사용하여 자동화된 Backup 및 패치에 대한 특정 상태 정보를 가져올 수도 있습니다.

   ```powershell-interactive
    $sqlext = Get-AzVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"
    $sqlext.AutoPatchingSettings
    $sqlext.AutoBackupSettings
   ```

## <a name="removal"></a>제거
Azure portal에서 줄임표를 클릭 하 여 확장을 제거할 수 있습니다 합니다 **확장** 가상 머신 속성의 창. 그런 다음 **삭제**를 클릭합니다.

![Azure Portal에서 SQL Server IaaS 에이전트 확장 제거](./media/virtual-machines-windows-sql-server-agent-extension/azure-rm-sql-server-iaas-agent-uninstall.png)

**Remove-AzVMSqlServerExtension** PowerShell cmdlet을 사용할 수도 있습니다.

   ```powershell-interactive
    Remove-AzVMSqlServerExtension -ResourceGroupName "resourcegroupname" -VMName "vmname" -Name "SqlIaasExtension"
   ```

## <a name="next-steps"></a>다음 단계
확장에 의해 지원되는 서비스 중 하나를 사용하기 시작합니다. 자세한 내용은 참조에서 참조 된 문서를 [지원 되는 서비스](#supported-services) 이 문서의 섹션입니다.

Azure Virtual Machines의 SQL Server 실행에 대한 자세한 내용은 [Azure Virtual Machines의 SQL Server 개요](virtual-machines-windows-sql-server-iaas-overview.md)를 참조하세요.

