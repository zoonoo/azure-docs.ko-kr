---
title: SQL VM 리소스 공급자를 사용 하 여 Azure에 SQL Server 가상 머신 등록 | Microsoft Docs
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
ms.openlocfilehash: 87db36936ee4aee45b7e8d83e1512d22c2a49eee
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/08/2019
ms.locfileid: "68846142"
---
# <a name="register-a-sql-server-virtual-machine-in-azure-with-the-sql-vm-resource-provider"></a>SQL VM 리소스 공급자를 사용 하 여 Azure에 SQL Server 가상 머신 등록

이 문서에서는 SQL VM 리소스 공급자를 사용 하 여 Azure에서 SQL Server VM (가상 머신)을 등록 하는 방법을 설명 합니다. 

Azure Portal를 통해 SQL Server VM Azure Marketplace 이미지를 배포 하면 리소스 공급자와 SQL Server VM 자동으로 등록 됩니다. Azure Marketplace에서 이미지를 선택 하는 대신 Azure 가상 컴퓨터에서 SQL Server를 자동으로 설치 하도록 선택 하거나 SQL Server를 사용 하 여 사용자 지정 VHD에서 Azure VM을 프로 비전 하는 경우에는 리소스 공급자를 사용 하 여 SQL Server VM를 등록 해야 합니다. :

- **규정 준수**: Microsoft 제품 약관에 따라 고객은 [Azure 하이브리드 혜택](https://azure.microsoft.com/pricing/hybrid-benefit/)를 사용 하는 경우 microsoft에 알려 주어 야 합니다. 이렇게 하려면 SQL VM 리소스 공급자에 등록 해야 합니다. 

- **기능 이점**: 리소스 공급자를 사용 하 여 SQL Server VM를 등록 하면 [자동화 된 패치](virtual-machines-windows-sql-automated-patching.md), [자동화 된 백업](virtual-machines-windows-sql-automated-backup-v2.md), 모니터링 및 관리 효율성 기능이 잠금 해제 됩니다. 또한 [라이선스](virtual-machines-windows-sql-ahb.md) 및 [버전](virtual-machines-windows-sql-change-edition.md) 유연성을 잠금 해제 합니다. 이전에는 이러한 기능을 Azure Marketplace의 SQL Server VM 이미지만 사용할 수 있었습니다.

SQL VM 리소스 공급자를 활용 하려면 SQL VM 리소스 공급자를 구독과 함께 등록 해야 합니다. Azure Portal, Azure CLI 또는 PowerShell을 사용 하 여이를 수행할 수 있습니다. 

## <a name="prerequisites"></a>전제 조건

리소스 공급자에 SQL Server VM을 등록 하려면 다음이 필요 합니다. 

- [Azure 구독](https://azure.microsoft.com/free/).
- [SQL Server VM](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision)입니다. 
- [Azure CLI](/cli/azure/install-azure-cli) 및 [PowerShell](/powershell/azure/new-azureps-module-az)입니다. 

## <a name="register-with-the-sql-vm-resource-provider"></a>SQL VM 리소스 공급자에 등록
[SQL Server IaaS 에이전트 확장이](virtual-machines-windows-sql-server-agent-extension.md) VM에 이미 설치 되어 있는 경우 SQL vm 리소스 공급자를 사용 하 여 등록 하면 단순히 SqlVirtualMachine/SqlVirtualMachines 형식의 메타 데이터 리소스를 만듭니다. 

SQL Server IaaS 확장이 VM에 이미 설치 되어 있는 경우 다음 코드 조각을 사용 하 여 SQL VM 리소스 공급자에 등록 합니다. SQL VM 리소스 공급자를 사용 하 여 등록할 때 원하는 SQL Server 라이선스 유형을 제공 해야 합니다. 종 량 제 (`PAYG`) 또는 Azure 하이브리드 혜택 (`AHUB`)입니다. 

다음 PowerShell 코드 조각을 사용 하 여 SQL Server VM를 등록 합니다.

  ```powershell-interactive
     # Get the existing compute VM
     $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
     # Register with the SQL VM resource provider
     New-AzResource -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
        -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines `
        -Properties @{virtualMachineResourceId=$vm.Id;SqlServerLicenseType='AHUB'}  
  
  ```

SQL Server IaaS 확장이 VM에 설치 되어 있지 않은 경우 경량 관리 모드를 지정 하 여 SQL VM 리소스 공급자에 등록할 수 있습니다. 경량 관리 모드에서 SQL VM 리소스 공급자는 SQL Server IaaS 확장을 [경량 모드로](virtual-machines-windows-sql-server-agent-extension.md#install-in-lightweight-mode) 자동으로 설치 하 고 SQL Server 인스턴스 메타 데이터를 확인 합니다. 이는 SQL Server 서비스를 다시 시작 하지 않습니다. SQL VM 리소스 공급자를 사용 하 여 등록할 때 원하는 SQL Server 라이선스 유형을 제공 해야 합니다. 종 량 제 (`PAYG`) 또는 Azure 하이브리드 혜택 (`AHUB`)입니다. 

다음 PowerShell 코드 조각을 사용 하 여 경량 관리 모드로 SQL Server VM를 등록 합니다.

  ```powershell-interactive
     # Get the existing compute VM
     $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
     # Register the SQL VM with the lightweight SQL IaaS agent
     New-AzResource -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
        -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines `
        -Properties @{virtualMachineResourceId=$vm.Id;SqlServerLicenseType='AHUB';sqlManagement='LightWeight'}  
  
  ```

[경량 모드](virtual-machines-windows-sql-server-agent-extension.md#install-in-lightweight-mode) 에서 SQL VM 리소스 공급자를 등록 하면 준수를 보장 하 고 유연한 라이선스 뿐만 아니라 전체 SQL Server 버전 업데이트를 사용할 수 있습니다. 장애 조치 (Failover) 클러스터 인스턴스 및 다중 인스턴스 배포는 경량 모드 에서만 SQL VM 리소스 공급자를 사용 하 여 등록할 수 있습니다. 언제 든 지 Azure Portal에 있는 지침에 따라 [전체 모드로](virtual-machines-windows-sql-server-agent-extension.md#install-in-full-mode) 업그레이드 하 고 SQL Server 다시 시작 하 여 포괄적인 관리 효율성 기능 집합을 사용할 수 있습니다. 

## <a name="register-sql-server-2008-or-2008-r2-on-windows-server-2008-vms"></a>Windows Server 2008 Vm에 SQL Server 2008 또는 2008 R2 등록

Windows Server 2008에 설치 된 SQL Server 2008 및 2008 R2는 ([에이전트 없음](virtual-machines-windows-sql-server-agent-extension.md)) 모드의 SQL VM 리소스 공급자에 등록할 수 있습니다. 이 옵션은 규정 준수를 보장 하 고 기능이 제한 된 Azure Portal에서 SQL Server VM를 모니터링할 수 있도록 합니다.

다음 표에서는 등록 중에 제공 되는 매개 변수에 대해 허용 되는 값을 자세히 설명 합니다.

| 매개 변수 | 허용 되는 값                                 |
| :------------------| :--------------------------------------- |
| **sqlLicenseType** | `AHUB` 또는 `PAYG`                    |
| **sqlImageOffer**  | `SQL2008-WS2008` 또는 `SQL2008R2-WS2008`|
| &nbsp;             | &nbsp;                                   |


Windows Server 2008에 SQL Server 2008 또는 2008 R2 인스턴스를 등록 하려면 다음 PowerShell 코드 조각을 사용 합니다.  

  ```powershell-interactive
     # Get the existing compute VM
     $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
    New-AzResource -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
      -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines `
      -Properties @{virtualMachineResourceId=$vm.Id;SqlServerLicenseType='AHUB'; `
       sqlManagement='NoAgent';sqlImageSku='Standard';sqlImageOffer='SQL2008R2-WS2008'}
  ```

## <a name="verify-registration-status"></a>등록 상태 확인
Azure Portal, Azure CLI 또는 PowerShell을 사용 하 여 SQL Server VM SQL VM 리소스 공급자에 이미 등록 되어 있는지 확인할 수 있습니다. 

### <a name="azure-portal"></a>Azure Portal 

1. [Azure Portal](https://portal.azure.com)에 로그인합니다. 
1. [SQL Server virtual machines](virtual-machines-windows-sql-manage-portal.md)로 이동 합니다.
1. 목록에서 SQL Server VM을 선택 합니다. SQL Server VM 여기에 나열 되지 않은 경우 SQL VM 리소스 공급자에 등록 되지 않았을 수 있습니다. 
1. **상태**에서 값을 확인 합니다. **상태가** **성공**인 경우 SQL Server VM SQL VM 리소스 공급자에 등록 되었습니다. 

    ![SQL RP 등록을 사용 하 여 상태 확인](media/virtual-machines-windows-sql-register-with-rp/verify-registration-status.png)

### <a name="azure-cli"></a>Azure CLI

  ```azurecli-interactive
  az sql vm show -n <vm_name> -g <resource_group>
  ```
`ProvisioningState`등록이 성공 `Succeeded` 했는지 여부를 표시 합니다. 

### <a name="powershell"></a>PowerShell

  ```powershell-interactive
  Get-AzResource -ResourceName <vm_name> -ResourceGroupName <resource_group> -ResourceType Microsoft.SqlVirtualMachine/sqlVirtualMachines
  ```
`ProvisioningState`등록이 성공 `Succeeded` 했는지 여부를 표시 합니다.

오류는 SQL Server VM 리소스 공급자에 등록 되지 않았음을 나타냅니다. 

## <a name="register-the-sql-vm-resource-provider-with-a-subscription"></a>구독에 SQL VM 리소스 공급자 등록 

SQL VM 리소스 공급자에 SQL Server VM를 등록 하려면 구독에 리소스 공급자를 등록 해야 합니다. Azure Portal, Azure CLI 또는 PowerShell을 사용 하 여이 작업을 수행할 수 있습니다.

### <a name="azure-portal"></a>Azure Portal

1. Azure Portal를 열고 **모든 서비스**로 이동 합니다. 
1. **구독** 으로 이동 하 고 원하는 구독을 선택 합니다.  
1. **구독** 페이지에서 **리소스 공급자**로 이동 합니다. 
1. Sql 관련 리소스 공급자를 가져오려면 필터에 **sql** 을 입력 합니다. 
1. 원하는 작업에 따라 **SqlVirtualMachine** 공급자에 대 한 **등록**, **다시 등록**또는 등록 **취소** 를 선택 합니다. 

![공급자 수정](media/virtual-machines-windows-sql-ahb/select-resource-provider-sql.png)

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
# Register the new SQL VM resource provider to your subscription 
az provider register --namespace Microsoft.SqlVirtualMachine 
```

### <a name="powershell"></a>PowerShell

```powershell-interactive
# Register the new SQL VM resource provider to your subscription
Register-AzResourceProvider -ProviderNamespace Microsoft.SqlVirtualMachine
```

## <a name="remarks"></a>설명

- SQL VM 리소스 공급자는 Azure Resource Manager를 통해 배포 된 SQL Server Vm만 지원 합니다. 클래식 모델을 통해 배포 된 SQL Server Vm은 지원 되지 않습니다. 
- SQL VM 리소스 공급자는 공용 클라우드에 배포 된 SQL Server Vm만 지원 합니다. 개인 또는 정부 클라우드로의 배포는 지원 되지 않습니다. 
 
## <a name="frequently-asked-questions"></a>질문과 대답 

**Azure Marketplace의 SQL Server 이미지에서 프로 비전 된 SQL Server VM를 등록 해야 하나요?**

아니요. Microsoft에서는 Azure Marketplace의 SQL Server 이미지에서 프로 비전 된 Vm을 자동으로 등록 합니다. VM이 Azure Marketplace의 SQL Server 이미지에서 프로 비전 *되지 않아* SQL Server 자체 설치 된 경우에만 SQL vm 리소스 공급자를 사용 하 여 등록 해야 합니다.

**SQL VM 리소스 공급자를 모든 고객이 사용할 수 있나요?** 

예. 사용자가 Azure Marketplace의 SQL Server 이미지를 사용 하지 않고 대신 자체 설치 된 SQL Server를 사용 하거나 사용자 지정 VHD를 사용 하는 경우 SQL Server Vm을 SQL VM 리소스 공급자와 함께 등록 해야 합니다. 모든 유형의 구독 (직접, 기업계약 및 클라우드 솔루션 공급자)이 소유한 Vm은 SQL VM 리소스 공급자에 등록할 수 있습니다.

**내 SQL Server VM에 SQL Server IaaS 확장이 이미 설치 되어 있는 경우 SQL VM 리소스 공급자에 등록 해야 하나요?**

SQL Server VM 자체 설치 되 고 Azure Marketplace의 SQL Server 이미지에서 프로 비전 되지 않은 경우 SQL Server IaaS 확장을 설치한 경우에도 SQL VM 리소스 공급자에 등록 해야 합니다. SQL VM 리소스 공급자를 사용 하 여 등록 하면 SqlVirtualMachines 형식의 새 리소스가 생성 됩니다. SQL Server IaaS 확장을 설치 하면 해당 리소스가 생성 되지 않습니다.

**SQL VM 리소스 공급자를 사용 하 여 등록할 때 기본 관리 모드는 무엇입니까?**

SQL VM 리소스 공급자를 사용 하 여 등록할 때 기본 관리 모드가 *꽉 찼습니다*. SQL VM 리소스 공급자를 사용 하 여 등록할 때 SQL Server 관리 속성이 설정 되지 않은 경우 모드가 전체 관리 효율성으로 설정 됩니다. VM에 SQL Server IaaS 확장을 설치 하는 것은 완전 한 관리 효율성 모드로 SQL VM 리소스 공급자를 등록 하는 필수 구성 요소입니다.

**SQL VM 리소스 공급자에 등록 하기 위한 필수 구성 요소는 무엇 인가요?**

경량 모드 또는 에이전트 없음 모드에서 SQL VM 리소스 공급자에 등록 하기 위한 필수 구성 요소는 없습니다. 전체 모드에서 SQL VM 리소스 공급자에 등록 하기 위한 필수 구성 요소는 SQL Server IaaS 확장이 VM에 설치 되어 있어야 합니다.

**SQL Server IaaS 확장이 VM에 설치 되어 있지 않으면 SQL VM 리소스 공급자에 등록할 수 있나요?**

예, SQL Server IaaS 확장이 VM에 설치 되어 있지 않은 경우 경량 관리 모드로 SQL VM 리소스 공급자에 등록할 수 있습니다. 경량 모드에서 SQL VM 리소스 공급자는 콘솔 앱을 사용 하 여 SQL Server 인스턴스의 버전 및 버전을 확인 합니다. 

VM에서 실행 중인 SQL Server 인스턴스가 하나 이상 있는지 확인 한 후 콘솔 앱이 종료 됩니다. 경량 모드에서 SQL VM 리소스 공급자를 등록 하면 SQL Server 다시 시작 되지 않고 VM에서 에이전트가 생성 되지 않습니다.

**SQL VM 리소스 공급자에 등록 하 여 내 VM에 에이전트를 설치 하나요?**

아니요. SQL VM 리소스 공급자를 사용 하 여 등록 하면 새 메타 데이터 리소스만 생성 됩니다. VM에 에이전트를 설치 하지 않습니다.

SQL Server IaaS 확장은 완전 한 관리 기능을 사용 하도록 설정 하는 데만 필요 합니다. 관리 효율성 모드를 경량에서 전체로 업그레이드 하면 SQL Server IaaS 확장이 설치 되 고 SQL Server 다시 시작 됩니다.

**VM에서 SQL Server VM 리소스 공급자 다시 시작 SQL Server에 등록 됩니까?**

아니요. SQL VM 리소스 공급자를 사용 하 여 등록 하면 새 메타 데이터 리소스만 생성 됩니다. VM에서 SQL Server을 다시 시작 하지 않습니다. 

SQL Server IaaS 확장을 설치 하는 경우에만 SQL Server를 다시 시작 해야 합니다. 및 SQL Server IaaS 확장은 완전 한 관리 기능을 사용 하도록 설정 하는 데 필요 합니다. 관리 효율성 모드를 경량에서 전체로 업그레이드 하면 SQL Server IaaS 확장이 설치 되 고 SQL Server 다시 시작 됩니다.

**SQL VM 리소스 공급자를 사용 하 여 등록할 때 간단 하 고 에이전트 없는 관리 모드의 차이점은 무엇 인가요?** 

아니요-에이전트 관리 모드는 SQL Server 2008 및 SQL Server 2008 R2 on Windows Server 2008에만 사용할 수 있습니다. 이러한 버전에는 유일 하 게 사용할 수 있는 관리 모드입니다. 다른 모든 버전 SQL Server의 경우 사용 가능한 두 가지 관리 효율성 모드가 간단 하 고 완전 합니다. 

비-에이전트 모드를 사용 하려면 고객이 SQL Server 버전 및 버전 속성을 설정 해야 합니다. 경량 모드는 VM을 쿼리하여 SQL Server 인스턴스의 버전 및 에디션을 찾습니다.

**Azure CLI를 사용 하 여 SQL VM 리소스 공급자를 경량 또는 비 에이전트 모드로 등록할 수 있나요?**

아니요. 관리 모드 속성은 Azure PowerShell를 사용 하 여 SQL VM 리소스 공급자에 등록 하는 경우에만 사용할 수 있습니다. Azure CLI는 SQL Server 관리 효율성 속성의 설정을 지원 하지 않습니다. 항상 기본 모드인 전체 관리 효율성으로 SQL VM 리소스 공급자를 등록 합니다.

**SQL Server 라이선스 유형을 지정 하지 않고 SQL VM 리소스 공급자에 등록할 수 있나요?**

아니요. SQL VM 리소스 공급자를 사용 하 여 등록 하는 경우 SQL Server 라이선스 형식은 선택적 속성이 아닙니다. Azure CLI 및 PowerShell을 모두 사용 하 여 SQL Server 라이선스 유형을 종 량 제로 설정 하거나, 모든 관리 효율성 모드 (에이전트 없음, 경량 및 전체)에서 SQL VM 리소스 공급자에 등록할 때 Azure 하이브리드 혜택 해야 합니다.

**SQL Server IaaS 확장을 에이전트 없음 모드에서 전체 모드로 업그레이드할 수 있나요?**

아니요. 에이전트 없는 모드에서는 관리 효율성 모드를 full 또는 lightweight로 업그레이드할 수 없습니다. 이는 Windows Server 2008에 대 한 기술적 제한 사항입니다.

**SQL Server IaaS 확장을 경량 모드에서 전체 모드로 업그레이드할 수 있나요?**

예. 관리 효율성 모드를 경량에서 full로 업그레이드 하는 것은 PowerShell 또는 Azure Portal를 통해 지원 됩니다. SQL Server를 다시 시작 해야 합니다.

**SQL Server IaaS 확장을 전체 모드에서 에이전트 없음 또는 경량 관리 모드로 다운 그레이드할 수 있나요?**

아니요. SQL Server IaaS 확장 관리 효율성 모드의 다운 그레이드는 지원 되지 않습니다. 관리 효율성 모드는 전체 모드에서 경량 모드로 또는 에이전트 없음 모드로 다운 그레이드할 수 없으며 경량 모드에서 에이전트 없음 모드로 다운 그레이드할 수 없습니다. 

관리 효율성 모드를 전체 관리 기능으로 변경 하려면 SQL Server IaaS 확장을 제거 합니다. 그런 다음 Micorsoft SqlVirtualMachine 리소스를 삭제 하 고 SQL VM 리소스 공급자를 사용 하 여 SQL Server VM를 다시 등록 합니다.

**Azure Portal에서 SQL VM 리소스 공급자에 등록할 수 있나요?**

아니요. Azure Portal에서는 SQL VM 리소스 공급자에 등록할 수 없습니다. Azure CLI 또는 PowerShell에서 완전 한 관리 모드로 SQL VM 리소스 공급자를 등록 하는 것이 지원 됩니다. 경량 또는 에이전트 없는 관리 모드로 SQL VM 리소스 공급자를 등록 하는 것은 Azure PowerShell Api 에서만 지원 됩니다.

**SQL Server 설치 하기 전에 SQL VM 리소스 공급자를 사용 하 여 VM을 등록할 수 있나요?**

아니요. VM에는 SQL VM 리소스 공급자에 성공적으로 등록 하려면 하나 이상의 SQL Server 인스턴스가 있어야 합니다. VM에 SQL Server 인스턴스가 없으면 새 SqlVirtualMachine 리소스가 실패 한 상태가 됩니다.

**SQL Server 인스턴스가 여러 개 있는 경우 SQL VM 리소스 공급자를 사용 하 여 VM을 등록할 수 있나요?**

예. SQL VM 리소스 공급자는 SQL Server 인스턴스를 하나만 등록 합니다. SQL VM 리소스 공급자는 여러 인스턴스의 경우 기본 SQL Server 인스턴스를 등록 합니다. 기본 인스턴스가 없으면 경량 모드의 등록만 지원 됩니다. 경량에서 전체 관리 모드로 업그레이드 하려면 기본 SQL Server 인스턴스가 있어야 합니다. 그렇지 않으면 VM에 명명 된 SQL Server 인스턴스가 하나만 있어야 합니다.

**SQL VM 리소스 공급자를 사용 하 여 SQL Server 장애 조치 (failover) 클러스터 인스턴스를 등록할 수 있나요?**

예. Azure VM의 SQL Server 장애 조치 (failover) 클러스터 인스턴스는 경량 모드의 SQL VM 리소스 공급자에 등록 될 수 있습니다. 그러나 SQL Server 장애 조치 (failover) 클러스터 인스턴스를 완전 한 관리 효율성 모드로 업그레이드할 수는 없습니다.

**Always On 가용성 그룹이 구성 된 경우에는 SQL VM 리소스 공급자를 사용 하 여 VM을 등록할 수 있나요?**

예. Always On 가용성 그룹 구성에 참여 하는 경우 SQL VM 리소스 공급자를 사용 하 여 Azure VM에 SQL Server 인스턴스를 등록 하는 데 제한 사항이 없습니다.

## <a name="next-steps"></a>다음 단계

자세한 내용은 다음 문서를 참조하세요. 

* [Windows VM에서 SQL Server 개요](virtual-machines-windows-sql-server-iaas-overview.md)
* [Windows VM의 SQL Server에 대 한 FAQ](virtual-machines-windows-sql-server-iaas-faq.md)
* [Windows VM의 SQL Server에 대 한 가격 책정 지침](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Windows VM의 SQL Server에 대 한 릴리스 정보](virtual-machines-windows-sql-server-iaas-release-notes.md)
