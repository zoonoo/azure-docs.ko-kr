---
title: SQL VM 리소스 공급자를 사용 하 여 Azure에서 가상 머신 SQL Server 등록 | Microsoft Docs
description: 관리 효율성을 개선 하려면 SQL VM 리소스 공급자에 SQL Server VM를 등록 합니다.
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
ms.openlocfilehash: 95a167cbc8fde4488e3f46ffd850f0619cf1b651
ms.sourcegitcommit: 770b060438122f090ab90d81e3ff2f023455213b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/17/2019
ms.locfileid: "68305863"
---
# <a name="register-sql-server-virtual-machine-in-azure-with-the-sql-vm-resource-provider"></a>SQL VM 리소스 공급자를 사용 하 여 Azure에 SQL Server 가상 머신 등록

이 문서에서는 SQL VM 리소스 공급자를 사용 하 여 Azure SQL Server VM (가상 머신)을 등록 하는 방법을 설명 합니다. 

Azure Portal를 통해 SQL Server VM marketplace 이미지를 배포 하면 리소스 공급자에 SQL Server VM 자동으로 등록 됩니다. 그러나 Azure Marketplace에서 이미지를 선택 하는 대신 Azure 가상 컴퓨터에서 SQL Server를 자동으로 설치 하도록 선택 하는 경우에는 현재 다음에 대 한 리소스 공급자에 SQL Server VM를 등록 해야 합니다.

-  **규정 준수** - [Azure 하이브리드 혜택](https://azure.microsoft.com/pricing/hybrid-benefit/) 를 사용 하는 고객은 Azure 하이브리드 혜택를 사용 하는 경우 microsoft에 표시 해야 하며, 이렇게 하려면 SQL VM 리소스 공급자에 등록 해야 합니다. 

-  **기능 이점** -리소스 공급자를 사용 하 여 SQL Server VM를 등록 하면 [자동 패치](virtual-machines-windows-sql-automated-patching.md), [자동 백업](virtual-machines-windows-sql-automated-backup-v2.md), 모니터링 및 관리 효율성 기능 뿐 아니라 [라이선스](virtual-machines-windows-sql-ahb.md) 및 [버전](virtual-machines-windows-sql-change-edition.md) 유연성이 잠금 해제 됩니다. 이전에는 Azure Marketplace에서 이미지를 SQL Server VM만 사용할 수 있었습니다.

Azure VM에 SQL Server를 자동으로 설치 하거나 SQL Server를 사용 하 여 사용자 지정 VHD에서 Azure VM을 프로 비전 하는 것은 고객이 SQL VM 리소스에 등록 하 여 Microsoft에 사용 함을 나타내는 조건에 따라 SQL Server에 대 한 Azure 하이브리드 혜택를 준수 합니다. 공급자별. 

SQL VM 리소스 공급자를 활용 하려면 SQL VM 리소스 공급자를 구독과 함께 등록 해야 합니다. Azure Portal, Azure CLI 및 PowerShell을 사용 하 여이를 수행할 수 있습니다. 

## <a name="prerequisites"></a>전제 조건

리소스 공급자에 SQL Server VM을 등록 하려면 다음이 필요 합니다. 

- [Azure 구독](https://azure.microsoft.com/free/).
- [SQL Server VM](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision)입니다. 
- [Azure CLI](/cli/azure/install-azure-cli) 및 [PowerShell](/powershell/azure/new-azureps-module-az). 

## <a name="register-with-sql-vm-resource-provider"></a>SQL VM 리소스 공급자에 등록
[Sql IaaS 확장이](virtual-machines-windows-sql-server-agent-extension.md) VM에 이미 설치 되어 있는 경우 sql vm 리소스 공급자에 등록 하는 것은 단순히 SqlVirtualMachine/SqlVirtualMachines 형식의 메타 데이터 리소스를 만드는 것입니다. 다음은 SQL IaaS 확장이 VM에 이미 설치 되어 있는 경우 SQL VM 리소스 공급자에 등록 하는 코드 조각입니다. SQL VM 리소스 공급자를 ' PAYG ' 또는 ' AHUB '로 등록할 때 원하는 SQL Server 라이선스 유형을 제공 해야 합니다. 

다음 코드 조각과 함께 PowerShell을 사용 하 여 SQL Server VM를 등록 합니다.

  ```powershell-interactive
     # Get the existing  Compute VM
     $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
     # Register with SQL VM resource provider
     New-AzResource -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
        -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines `
        -Properties @{virtualMachineResourceId=$vm.Id;SqlServerLicenseType='AHUB'}  
  
  ```

SQL IaaS 확장이 VM에 설치 되어 있지 않은 경우 경량 SQL 관리 모드를 지정 하 여 SQL VM 리소스 공급자에 등록할 수 있습니다. 경량 SQL 관리 모드에서 SQL VM 리소스 공급자는 SQL IaaS 확장을 [경량 모드로](virtual-machines-windows-sql-server-agent-extension.md#install-in-lightweight-mode) 자동 설치 하 고 SQL Server 인스턴스 메타 데이터를 확인 합니다. 이는 SQL Server 서비스를 다시 시작 하지 않습니다. SQL VM 리소스 공급자를 ' PAYG ' 또는 ' AHUB '로 등록할 때 원하는 SQL Server 라이선스 유형을 제공 해야 합니다. 

다음 코드 조각을 사용 하 여 PowerShell을 사용 하 여 경량 SQL 관리 모드로 SQL Server VM를 등록 합니다.

  ```powershell-interactive
     # Get the existing  Compute VM
     $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
     # Register SQL VM with 'Lightweight' SQL IaaS agent
     New-AzResource -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
        -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines `
        -Properties @{virtualMachineResourceId=$vm.Id;SqlServerLicenseType='AHUB';sqlManagement='LightWeight'}  
  
  ```

[경량 모드](virtual-machines-windows-sql-server-agent-extension.md#install-in-lightweight-mode) 에서 SQL VM 리소스 공급자를 등록 하면 준수를 보장 하 고 유연한 라이선스 뿐만 아니라 전체 SQL Server 버전 업데이트를 사용할 수 있습니다. 장애 조치 (Failover) 클러스터 인스턴스 및 다중 인스턴스 배포는 경량 모드 에서만 SQL VM 리소스 공급자를 사용 하 여 등록할 수 있습니다. Azure Portal에 있는 지침에 따라 [전체 모드로](virtual-machines-windows-sql-server-agent-extension.md#full-mode-installation) 업그레이드 하 고 언제 든 지 SQL Server를 사용 하 여 포괄적인 관리 기능 집합을 사용할 수 있습니다. 

## <a name="register-sql-server-2008r2-on-windows-server-2008-vms"></a>Windows Server 2008 Vm에 SQL Server 2008/R2 등록

[Noagent](virtual-machines-windows-sql-server-agent-extension.md) 모드에서 제공 하는 SQL VM 리소스에는 Windows Server 2008에 설치 된 SQL Server 2008 및 2008 R2를 등록할 수 있습니다. 이 옵션은 규정 준수를 보장 하 고 기능이 제한 된 Azure Portal에서 SQL Server VM를 모니터링할 수 있도록 합니다.

다음 표에서는 등록 중에 제공 되는 매개 변수에 대해 허용 되는 값을 자세히 설명 합니다.

| 매개 변수 | 허용 되는 값                                 |
| :------------------| :--------------------------------------- |
| **sqlLicenseType** | `'AHUB'`또는 `'PAYG'`                    |
| **sqlImageOffer**  | `'SQL2008-WS2008'` 또는 `'SQL2008R2-WS2008`|
| &nbsp;             | &nbsp;                                   |


Windows Server 2008 인스턴스에 SQL Server 2008 또는 2008 r 2를 등록 하려면 다음 Powershell 코드 조각을 사용 합니다.  

  ```powershell-interactive
     # Get the existing  Compute VM
     $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
    New-AzResource -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
      -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines `
      -Properties @{virtualMachineResourceId=$vm.Id;SqlServerLicenseType='AHUB'; `
       sqlManagement='NoAgent';sqlImageSku='Standard';sqlImageOffer='SQL2008R2-WS2008'}
  ```

## <a name="verify-registration-status"></a>등록 상태 확인
Azure Portal, Azure CLI 또는 PowerShell을 사용 하 여 SQL Server SQL VM 리소스 공급자에 이미 등록 되어 있는지 확인할 수 있습니다. 

### <a name="azure-portal"></a>Azure Portal 
Azure Portal를 사용 하 여 등록 상태를 확인 하려면 다음을 수행 합니다.

1. [Azure 포털](https://portal.azure.com)할 수 있습니다. 
1. [SQL 가상 머신으로](virtual-machines-windows-sql-manage-portal.md)이동 합니다.
1. 목록에서 SQL Server VM을 선택 합니다. SQL Server VM 여기에 나열 되지 않은 경우 SQL Server VM SQL VM 리소스 공급자에 등록 되지 않았을 수 있습니다. 
1. 아래에서 `Status`값을 봅니다. 이면 `Status = Succeeded`SQL Server VM SQL VM 리소스 공급자에 등록 된 것입니다. 

    ![SQL RP 등록을 사용 하 여 상태 확인](media/virtual-machines-windows-sql-register-with-rp/verify-registration-status.png)

### <a name="az-cli"></a>Az CLI

다음 AZ CLI 명령을 사용 하 여 현재 SQL Server VM 등록 상태를 확인 합니다. `ProvisioningState`등록이 성공 `Succeeded` 했는지 여부를 표시 합니다. 

  ```azurecli-interactive
  az sql vm show -n <vm_name> -g <resource_group>
  ```


### <a name="powershell"></a>PowerShell

다음 PowerShell cmdlet을 사용 하 여 현재 SQL Server VM 등록 상태를 확인 합니다. `ProvisioningState`등록이 성공 `Succeeded` 했는지 여부를 표시 합니다. 

  ```powershell-interactive
  Get-AzResource -ResourceName <vm_name> -ResourceGroupName <resource_group> -ResourceType Microsoft.SqlVirtualMachine/sqlVirtualMachines
  ```
오류는 SQL Server VM 리소스 공급자에 등록 되지 않았음을 나타냅니다. 

---

## <a name="register-sql-vm-resource-provider-with-subscription"></a>구독에 SQL VM 리소스 공급자 등록 

SQL VM 리소스 공급자에 SQL Server VM를 등록 하려면 구독에 리소스 공급자를 등록 해야 합니다. Azure Portal 또는 Azure CLI를 사용 하 여이 작업을 수행할 수 있습니다.

### <a name="azure-portal"></a>Azure Portal

다음 단계는 Azure Portal 사용 하 여 Azure 구독에 SQL VM 리소스 공급자를 등록 합니다. 

1. Azure Portal을 열고 **모든 서비스**로 이동합니다. 
1. **구독**으로 이동하고 관심 있는 구독을 선택합니다.  
1. **구독** 페이지에서 **리소스 공급자**로 이동 합니다. 
1. 필터에 `sql`을 입력하여 SQL 관련 리소스 공급자를 표시합니다. 
1. 원하는 작업에 따라 **Microsoft.SqlVirtualMachine** 공급자에 대해 ‘등록’, ‘다시 등록’ 또는 ‘등록 해제’를 선택합니다.    

   ![공급자 수정](media/virtual-machines-windows-sql-ahb/select-resource-provider-sql.png)

### <a name="az-cli"></a>Az CLI
다음 코드 조각에서는 Azure 구독에 SQL VM 리소스 공급자를 등록 합니다. 

```azurecli-interactive
# Register the new SQL VM resource provider to your subscription 
az provider register --namespace Microsoft.SqlVirtualMachine 
```

### <a name="powershell"></a>PowerShell

다음 PowerShell 코드 조각은 Azure 구독에 SQL VM 리소스 공급자를 등록 합니다.

```powershell-interactive
# Register the new SQL VM resource provider to your subscription
Register-AzResourceProvider -ProviderNamespace Microsoft.SqlVirtualMachine
```
---

## <a name="remarks"></a>설명

 - SQL VM 리소스 공급자는 ' 리소스 관리자 '를 사용 하 여 배포 된 SQL Server Vm만 지원 합니다. ' 클래식 모델 '을 사용 하 여 배포 된 SQL Server Vm은 지원 되지 않습니다. 
 - SQL VM 리소스 공급자는 공용 클라우드에 배포 된 SQL Server Vm만 지원 합니다. 개인 또는 정부 클라우드에 배포 하는 것은 지원 되지 않습니다. 
 
## <a name="frequently-asked-questions"></a>질문과 대답 

**Azure Marketplace의 SQL 이미지에서 프로 비전 된 SQL Server VM를 등록 해야 하나요?**

아니요. Microsoft autoregisters는 Azure Marketplace의 SQL 이미지에서 프로 비전 된 Vm을 제공 합니다. VM이 Azure marketplace의 SQL 이미지에서 프로 비전 되지 않고 SQL Server 자체 설치 된 경우에만 SQL VM 리소스 공급자를 사용 하 여 등록 해야 합니다.

**SQL VM 리소스 공급자를 모든 고객이 사용할 수 있나요?** 

예. 고객은 Azure marketplace에서 SQL Server 이미지를 사용 하지 않거나 자체 설치 된 SQL Server를 사용 하거나 사용자 지정 VHD를 제공 하지 않은 경우 SQL VM 리소스 공급자에 SQL Server VM을 등록 해야 합니다. 모든 유형의 구독 (Direct, EA 및 CSP)에서 소유 하는 Vm은 SQL VM 리소스 공급자에 등록할 수 있습니다.

**Sql IaaS 확장이 이미 설치 되어 있는 SQL Server VM 경우 SQL VM 리소스 공급자에 등록 해야 하나요?**

SQL Server VM 자체 설치 된 경우 Azure marketplace의 SQL 이미지에서 프로 비전 되지 않은 경우 SQL IaaS 확장을 설치한 경우에도 SQL VM 리소스 공급자에 등록 해야 합니다. SQL VM 리소스 공급자를 사용 하 여 등록 하면 SqlVirtualMachines 형식의 새 리소스가 생성 됩니다. SQL IaaS 확장을 설치 하면 해당 리소스가 생성 되지 않습니다.

**SQL VM 리소스 공급자를 사용 하 여 등록할 때 기본 SQL 관리 모드는 무엇입니까?**

SQL VM RP에 등록할 때 기본 SQL 관리 모드가 _꽉 찼습니다_. SQL VM 리소스 공급자를 사용 하 여 등록할 때 SQL 관리 속성을 설정 하지 않으면 모드가 전체 관리 효율성으로 설정 됩니다. Sql IaaS 확장을 VM에 설치 하는 것은 완전 한 관리 모드로 SQL VM 리소스 공급자를 등록 하는 필수 구성 요소입니다.

**SQL VM 리소스 공급자에 등록 하기 위한 필수 구성 요소는 무엇 인가요?**

경량 모드 또는 에이전트 없음 모드에서 SQL VM 리소스 공급자에 등록 하기 위한 필수 구성 요소는 없습니다. SQL VM 리소스 공급자를 전체 모드로 등록 하기 위한 필수 구성 요소는 SQL IaaS 확장이 VM에 설치 되어 있어야 합니다.

**Sql IaaS 확장이 VM에 설치 되어 있지 않은 경우 SQL VM 리소스 공급자에 등록할 수 있나요?**

예, SQL IaaS 확장이 VM에 설치 되어 있지 않은 경우 경량 관리 모드에서 SQL VM 리소스 공급자를 사용 하 여 등록할 수 있습니다. 경량 모드에서 SQL VM 리소스 공급자는 콘솔 앱을 사용 하 여 SQL 인스턴스의 버전 및 버전을 확인 합니다. VM에서 실행 중인 SQL 인스턴스가 하나 이상 있는지 확인 한 후 콘솔 앱이 종료 됩니다. 경량 모드에서 SQL VM 리소스 공급자를 등록 하면 SQL Server 다시 시작 되지 않고 VM에서 에이전트가 생성 되지 않습니다.

**SQL VM 리소스 공급자에 등록 하 여 내 VM에 에이전트를 설치 하나요?**

아니요. SQL VM 리소스 공급자를 사용 하 여 등록 하면 새 메타 데이터 리소스만 생성 되 고 VM에 에이전트가 설치 되지 않습니다. SQL IaaS 확장은 완전 한 관리 기능을 사용 하는 경우에만 필요 하므로, 관리 효율성 모드를 경량에서 전체로 업그레이드 하면 SQL IaaS 확장을 설치 하 고 SQL Server를 다시 시작 합니다.

**내 VM에서 SQL VM 리소스 공급자를 다시 시작 SQL Server를 등록 하 시겠습니까?**

아니요. SQL VM 리소스 공급자를 사용 하 여 등록 하면 새 메타 데이터 리소스만 생성 되며 VM에서 SQL Server 다시 시작 되지 않습니다. SQL IaaS 확장을 설치 하려면 SQL Server를 다시 시작 해야 합니다. 및 SQL IaaS 확장은 전체 관리 기능을 사용 하도록 설정 하는 데 필요 합니다. 관리 효율성 모드를 경량에서 전체로 업그레이드 하면 SQL IaaS 확장을 설치 하 고 SQL Server를 다시 시작 합니다.

**SQL VM 리소스 공급자를 사용 하 여 등록할 때 간단 하 고 에이전트 없는 관리 모드의 차이점은 무엇 인가요?** 

아니요-에이전트 관리 모드는 Windows Server 2008의 SQL Server 2008/r 2 에서만 사용할 수 있습니다. 그리고 이러한 버전에 대해 유일 하 게 사용할 수 있는 관리 모드입니다. 다른 모든 버전의 SQL Server에 대해 사용 가능한 두 가지 관리 효율성 모드는 간단 하 고 완전 합니다. 비-에이전트 모드를 사용 하려면 고객이 SQL Server 버전 및 버전 속성을 설정 해야 합니다. 경량 모드는 VM을 쿼리하여 SQL 인스턴스의 버전 및 버전을 찾습니다.

**Azure CLI를 사용 하 여 경량 또는 비 에이전트 모드로 SQL VM 리소스 공급자에 등록할 수 있나요?**

아니요. SQL 관리 모드 속성은 Azure PowerShell를 사용 하 여 SQL VM 리소스 공급자에 등록 하는 경우에만 사용할 수 있습니다. Azure CLI는 SQL 관리 효율성 속성을 설정 하는 것을 지원 하지 않으며 항상 기본 모드 (전체 관리 효율성)로 SQL VM 리소스 공급자에 등록 합니다.

**SQL 라이선스 유형을 지정 하지 않고 SQL VM 리소스 공급자에 등록할 수 있나요?**

아니요. Sql VM RP에 등록할 때 SQL 라이선스 유형이 선택적 속성이 아닙니다. AZ CLI 및 PowerShell을 사용 하 여 모든 관리 효율성 모드 (에이전트 없음, 경량 및 전체)에 SQL VM 리소스 공급자를 등록할 때 SQL 라이선스 유형을 PAYG 또는 AHUB로 설정 해야 합니다.

**SQL IaaS 확장을 에이전트 없음 모드에서 전체 모드로 업그레이드할 수 있나요?**

아니요. 에이전트 없는 모드에서는 SQL 관리 효율성 모드를 Full 또는 Lightweight로 업그레이드할 수 없습니다. 이는 Windows Server 2008에 대 한 기술적 제한 사항입니다.

**SQL IaaS 확장을 경량 모드에서 전체 모드로 업그레이드할 수 있나요?**

예. PowerShell 또는 Azure Portal를 통해 SQL 관리 모드를 경량에서 Full로 업그레이드 하는 것이 지원 됩니다. SQL Server를 다시 시작 해야 합니다.

**SQL IaaS 확장을 전체 모드에서 에이전트 또는 경량 관리 모드로 다운 그레이드할 수 있나요?**

아니요. SQL IaaS 확장 관리 효율성 모드의 다운 그레이드는 지원 되지 않습니다. SQL 관리 효율성 모드를 전체 모드에서 경량 또는 에이전트 없음 모드로 다운 그레이드할 수 없습니다. 경량 모드에서 에이전트 없음 모드로 다운 그레이드할 수 없습니다. 관리 효율성 모드를 전체 관리 기능으로 변경 하려면 SQL IaaS 확장을 제거 합니다. Micorsoft SqlVirtualMachine 리소스를 삭제 하 고 SQL VM 리소스 공급자를 사용 하 여 SQL Server VM를 다시 등록 합니다.

**Azure Portal에서 SQL VM 리소스 공급자에 등록할 수 있나요?**

아니요. Azure Portal에서는 SQL VM 리소스 공급자를 등록할 수 없습니다. Azure CLI 또는 PowerShell에서 전체 관리 모드로 SQL VM 리소스 공급자를 등록 하는 것이 지원 됩니다. 그리고 경량 또는 에이전트 없는 관리 모드에서 SQL VM 리소스 공급자에 등록 하는 것은 Azure PowerShell Api 에서만 지원 됩니다.

**SQL Server 설치 하기 전에 SQL VM 리소스 공급자를 사용 하 여 VM을 등록할 수 있나요?**

아니요. SQL VM 리소스 공급자에 성공적으로 등록 하려면 VM에 SQL 인스턴스가 하나 이상 있어야 합니다. VM에 SQL 인스턴스가 없는 경우 새 Micosoft. SqlVirtualMachine 리소스는 실패 상태가 됩니다.

**SQL 인스턴스를 여러 개 사용 하는 경우 SQL VM 리소스를 사용 하 여 VM을 등록할 수 있나요?**

예. SQL VM 리소스 공급자는 하나의 SQL 인스턴스만 등록 합니다. SQL VM 리소스 공급자는 여러 인스턴스의 경우 기본 SQL 인스턴스를 등록 합니다. 기본 인스턴스가 없으면 경량 모드의 등록만 지원 됩니다. 경량에서 전체 관리 모드로 업그레이드 하려면 기본 SQL 인스턴스가 있어야 합니다. 그렇지 않으면 VM에 명명 된 SQL 인스턴스가 하나만 있어야 합니다.

**SQL VM 리소스 공급자를 사용 하 여 SQL Server 장애 조치 (Failover) 클러스터 인스턴스를 등록할 수 있나요?**

예. Azure VM의 SQL FCI 인스턴스는 경량 모드의 SQL VM 리소스 공급자에 등록 될 수 있습니다. 그러나 SQL FCI 인스턴스를 완전 한 관리 효율성 모드로 업그레이드할 수는 없습니다.

**Always ON 가용성 그룹이 구성 된 경우 VM을 SQL VM RP에 등록할 수 있나요?**

예. Always ON 가용성 그룹 구성에 참여 하는 경우 SQL VM 리소스 공급자를 사용 하 여 Azure VM에 SQL Server 인스턴스를 등록 하는 데 제한 사항이 없습니다.

## <a name="next-steps"></a>다음 단계

자세한 내용은 다음 문서를 참조하세요. 

* [Windows VM에서 SQL Server 개요](virtual-machines-windows-sql-server-iaas-overview.md)
* [Windows VM의 SQL Server FAQ](virtual-machines-windows-sql-server-iaas-faq.md)
* [Windows VM의 SQL Server 가격 책정 가이드](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Windows VM의 SQL Server 릴리스 정보](virtual-machines-windows-sql-server-iaas-release-notes.md)
