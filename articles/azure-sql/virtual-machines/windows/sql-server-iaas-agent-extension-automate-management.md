---
title: SQL Server IaaS 에이전트 확장은 무엇 인가요?
description: 이 문서에서는 SQL Server IaaS 에이전트 확장을 사용 하 여 Azure Vm에서 SQL Server의 관리 관련 관리 작업을 자동화 하는 방법을 설명 합니다. 이러한 기능에는 자동화 된 백업, 자동화 된 패치, Azure Key Vault 통합, 라이선스 관리, 저장소 구성, 모든 SQL Server VM 인스턴스의 중앙 관리 등이 포함 됩니다.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
editor: ''
tags: azure-resource-manager
ms.assetid: effe4e2f-35b5-490a-b5ef-b06746083da4
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 10/30/2020
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: 070058eae36bf4f8546cfcf4beb85ac5023e9c79
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2020
ms.locfileid: "93286171"
---
# <a name="what-is-the-sql-server-iaas-agent-extension"></a>SQL Server IaaS 에이전트 확장은 무엇 인가요?
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]


SQL Server IaaS 에이전트 확장 (SqlIaasExtension)은 Azure Virtual Machines (Vm)의 SQL Server에서 실행 되어 관리 및 관리 작업을 자동화 합니다. 

이 문서에서는 확장에 대 한 개요를 제공 합니다. SQL Server IaaS 확장을 설치 하 여 Azure Vm에서 SQL Server 하려면 [자동 설치](sql-vm-resource-provider-automatic-registration.md), [단일 vm](sql-vm-resource-provider-register.md)또는 [vm 대량으로](sql-vm-resource-provider-bulk-register.md)문서를 참조 하세요. 

## <a name="overview"></a>개요

SQL Server IaaS 에이전트 확장은 Azure Vm의 SQL Server에 대 한 다양 한 이점을 제공 합니다. 

- **기능 이점** : 확장이 포털 관리, 라이선스 유연성, 자동화 된 백업, 자동화 된 패치 등의 다양 한 자동화 기능 혜택을 해제 합니다. 자세한 내용은이 문서의 뒷부분에 나오는 [기능 이점](#feature-benefits) 을 참조 하세요. 

- **규정 준수** : 확장은 제품 약관에 지정 된 대로 Azure 하이브리드 혜택 사용 하도록 설정 되었음을 Microsoft에 알리기 위한 간단한 방법을 제공 합니다. 이 프로세스로 각 리소스마다 라이선스 등록 양식을 관리할 필요가 없습니다.  

- **무료** : 세 가지 관리 효율성 모드 모두에서 확장은 완전히 무료로 제공 됩니다. 리소스 공급자 또는 관리 모드 변경과 관련된 추가 비용은 없습니다. 

- **간소화 된 라이선스 관리** : 확장 SQL Server 라이선스 관리를 간소화 하 고 [Azure Portal](manage-sql-vm-portal.md), Azure CLI 또는 PowerShell을 사용 하 여 Azure 하이브리드 혜택 사용 하도록 설정 된 SQL Server vm을 빠르게 식별할 수 있습니다. 

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


> [!IMPORTANT]
> SQL IaaS 에이전트 확장은 Azure Virtual Machines 내에서 SQL Server를 사용 하는 경우 고객에 게 선택적 혜택을 제공 하기 위한 express 용도의 데이터를 수집 합니다. Microsoft는 고객의 사전 동의가 없는 라이선스 감사에는이 데이터를 사용 하지 않습니다. 자세한 내용은 [SQL Server 개인 정보 취급 방침](/sql/sql-server/sql-server-privacy#non-personal-data) 을 참조 하세요.


## <a name="feature-benefits"></a>기능 이점 

SQL Server IaaS 에이전트 확장은 SQL Server VM 관리에 대 한 다양 한 기능 혜택을 해제 합니다. 

다음 표에서는 이러한 이점에 대해 자세히 설명 합니다. 


| 기능 | 설명 |
| --- | --- |
| **포털 관리** | [포털에서 관리](manage-sql-vm-portal.md)의 잠금을 해제 하 여 모든 SQL Server vm을 한 곳에서 볼 수 있으므로 포털에서 직접 SQL 특정 기능을 사용 하거나 사용 하지 않도록 설정할 수 있습니다. 
| **자동화 된 백업** |VM에 있는 SQL Server의 기본 인스턴스나 [제대로 설치된](frequently-asked-questions-faq.md#administration) 명명된 인스턴스에 대한 모든 데이터베이스 백업 예약을 자동화합니다. 자세한 내용은 [Azure 가상 머신에서 SQL Server에 대한 자동화된 백업(Resource Manager)](automated-backup-sql-2014.md)을 참조하세요. |
| **자동화 된 패치** |VM에 대 한 중요 한 Windows 및 SQL Server 보안 업데이트가 수행 될 수 있는 유지 관리 기간을 구성 하 여 작업에 대 한 사용량이 많은 시간에 업데이트를 방지할 수 있습니다. 자세한 내용은 [Azure 가상 머신에서 SQL Server에 대한 자동화된 패치(Resource Manager)](automated-patching.md)를 참조하세요. |
| **Azure Key Vault 통합** |SQL Server VM에서 Azure Key Vault를 자동으로 설치하고 구성할 수 있습니다. 자세한 내용은 [Azure Virtual Machines에서 SQL Server에 대한 Azure Key Vault 통합 구성(Resource Manager)](azure-key-vault-integration-configure.md)을 참조하세요. |
| **유연한 라이선스** | 사용자 고유 라이선스 (Azure 하이브리드 혜택 라고도 함)에서 종 량 제 라이선스 모델로 원활 하 게 전환 하 고 다시 [전환](licensing-model-azure-hybrid-benefit-ahb-change.md) 하 여 비용을 절감 하세요. | 
| **유연한 버전/버전** | SQL Server [버전](change-sql-server-version.md) [또는 버전](change-sql-server-edition.md) 을 변경 하려는 경우 전체 SQL Server VM를 다시 배포 하지 않고도 Azure Portal 내에서 메타 데이터를 업데이트할 수 있습니다.  | 


## <a name="management-modes"></a>관리 모드

SQL IaaS 확장에는 다음과 같은 세 가지 관리 모드가 있습니다.

- **경량** 모드에서는 SQL Server를 다시 시작할 필요가 없지만 SQL Server 버전 및 라이선스 유형의 변경만 지원합니다. 여러 인스턴스가 있는 SQL Server VM에서 또는 FCI(장애 조치(failover) 클러스터 인스턴스)에 참여하는 경우 이 옵션을 사용합니다. 이 관리 모드는 컴퓨터에 SQL IaaS 에이전트 확장 이진 파일을 보관 하지만 에이전트는 설치 하지 않습니다. [자동 등록](sql-vm-resource-provider-automatic-registration.md) 기능을 사용 하는 경우 또는 수동 등록 중에 관리 유형이 지정 되지 않은 경우에는 경량 모드가 기본 관리 모드입니다. 경량 모드를 사용하는 경우 메모리 또는 CPU에 영향을 주지 않으며 관련 비용이 없습니다. 먼저 SQL Server VM을 경량 모드로 등록한 다음, 예약된 유지 관리 기간 동안 전체 모드로 업그레이드하는 것이 좋습니다.

- **전체** 모드에서는 모든 기능을 제공하지만 SQL Server 재시작과 시스템 관리자 권한이 필요합니다. 단일 인스턴스가 있는 SQL Server VM을 관리하는 데 이 옵션을 사용합니다. 전체 모드에서는 메모리 및 CPU에 최소한의 영향을 주는 두 개의 Windows 서비스가 설치됩니다. 이러한 서비스는 작업 관리자를 통해 모니터링할 수 있습니다. 전체 관리 모드를 사용하는 것과 관련된 비용은 없습니다. 

- **에이전트 없음** 모드는 Windows Server 2008에 설치된 SQL Server 2008 및 SQL Server 2008 R2 전용 모드입니다. 에이전트 없음 모드를 사용하는 경우 메모리 또는 CPU에는 영향을 주지 않습니다. 에이전트 없음 관리 모드 사용과 관련된 비용은 없습니다. 

Azure PowerShell를 사용 하 여 SQL Server IaaS 에이전트의 현재 모드를 볼 수 있습니다. 

  ```powershell-interactive
  # Get the SqlVirtualMachine
  $sqlvm = Get-AzSqlVM -Name $vm.Name  -ResourceGroupName $vm.ResourceGroupName
  $sqlvm.SqlManagementType
  ```

## <a name="installation"></a>설치

SQL Server IaaS 에이전트 확장은 SQL VM 리소스 공급자를 사용 하 여 SQL Server Vm을 등록할 때 설치 됩니다. 리소스 공급자를 사용 하 여 등록 하면 가상 컴퓨터 리소스의 _개별_ 리소스인 구독 내에서 **SQL 가상 컴퓨터** _리소스가_ 생성 됩니다. 리소스 공급자에서 SQL Server VM 등록을 취소하면 **SQL 가상 머신** _리소스_ 가 제거되지만 실제 가상 머신은 삭제되지 않습니다.

Azure Portal을 통해 SQL Server VM Azure Marketplace 이미지를 배포하면 SQL Server VM이 자동으로 리소스 공급자에 등록됩니다. 그러나 Azure 가상 머신에서 SQL Server를 자동으로 설치 하거나 사용자 지정 VHD에서 Azure 가상 머신을 프로 비전 하도록 선택 하는 경우 sql IaaS 에이전트 확장을 설치 하려면 SQL VM 리소스 공급자를 사용 하 여 SQL Server VM을 등록 해야 합니다. 

확장을 설치 하려면 리소스 공급자를 사용 하 여 SQL Server VM를 등록 합니다.
- [구독에서 모든 현재 및 미래의 Vm에 대해 자동으로](sql-vm-resource-provider-automatic-registration.md)
- [단일 VM의 경우](sql-vm-resource-provider-register.md)
- [대량으로 여러 Vm](sql-vm-resource-provider-bulk-register.md)

### <a name="named-instance-support"></a>명명 된 인스턴스 지원

SQL Server IaaS 확장은 가상 머신에서 사용할 수 있는 유일한 SQL Server 인스턴스인 SQL Server의 명명 된 인스턴스에서 작동 합니다. 확장은 여러 SQL Server 인스턴스가 있는 Vm에 설치 되지 않습니다. 

SQL Server의 명명 된 인스턴스를 사용 하려면 Azure 가상 컴퓨터를 배포 하 고 명명 된 단일 SQL Server 인스턴스를 설치한 다음 [SQL VM 리소스 공급자](sql-vm-resource-provider-register.md) 에 등록 하 여 확장을 설치 합니다.

또는 Azure Marketplace SQL Server 이미지를 사용 하 여 명명 된 인스턴스를 사용 하려면 다음 단계를 수행 합니다. 

   1. Azure Marketplace에서 SQL Server VM을 배포합니다. 
   1. SQL VM 리소스 공급자에서 SQL Server VM [등록을 취소](sql-vm-resource-provider-register.md#unregister-from-rp) 합니다. 
   1. SQL Server VM 내에서 SQL Server를 완전히 제거합니다.
   1. SQL Server VM 내에 명명된 인스턴스를 사용하여 SQL Server를 설치합니다. 
   1. [SQL VM 리소스 공급자에 SQL Server VM를 등록](sql-vm-resource-provider-register.md#register-with-rp)하 여 Sql IaaS 에이전트 확장을 설치 합니다. 

## <a name="verify-status-of-extension"></a>확장의 상태 확인

Azure Portal 또는 Azure PowerShell를 사용 하 여 확장의 상태를 확인 합니다. 


### <a name="azure-portal"></a>Azure portal

확장이 Azure Portal에 설치 되어 있는지 확인 합니다. 

가상 컴퓨터 창에서 **모든 설정** 을 선택 하 고 **확장** 을 선택 합니다. **SqlIaasExtension** 확장이 나열되어야 합니다.

![Azure Portal에서 SQL Server IaaS 에이전트 확장의 상태](./media/sql-server-iaas-agent-extension-automate-management/azure-rm-sql-server-iaas-agent-portal.png)


### <a name="azure-powershell"></a>Azure PowerShell

**Get-AzVMSqlServerExtension** Azure PowerShell cmdlet을 사용할 수도 있습니다.

   ```powershell-interactive
   Get-AzVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"
   ```

이전 명령은 에이전트가 설치되어 있는지 확인하고 일반적인 상태 정보를 제공합니다. 다음 명령을 사용하여 자동화된 Backup 및 패치에 대한 특정 상태 정보를 가져올 수 있습니다.

   ```powershell-interactive
    $sqlext = Get-AzVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"
    $sqlext.AutoPatchingSettings
    $sqlext.AutoBackupSettings
   ```


## <a name="limitations"></a>제한 사항

SQL IaaS 에이전트 확장은 다음을 지원 합니다. 

- Azure Resource Manager를 통해 배포된 SQL Server VM. 클래식 모델을 통해 배포된 SQL Server VM은 지원되지 않습니다. 
- 공용 또는 Azure Government 클라우드에 배포된 SQL Server VM. 다른 프라이빗 또는 정부 클라우드로의 배포는 지원되지 않습니다. 


## <a name="frequently-asked-questions"></a>질문과 대답 

**Azure Marketplace의 SQL Server 이미지에서 프로비저닝된 SQL Server VM을 등록해야 하나요?**

아니요. Microsoft는 Azure Marketplace의 SQL Server 이미지에서 프로비저닝된 VM을 자동으로 등록합니다. VM이 Azure Marketplace의 SQL Server 이미지에서 프로비저닝되지 *않고* SQL Server가 자체 설치된 경우에만 SQL VM 리소스 공급자 등록이 필요합니다.

**SQL VM 리소스 공급자를 모든 고객이 사용할 수 있나요?** 

예. 고객이 Azure Marketplace의 SQL Server 이미지를 사용하지 않고 대신 자체 설치된 SQL Server를 사용하거나 사용자 지정 VHD를 사용하는 경우 SQL Server VM을 SQL VM 리소스 공급자에 등록해야 합니다. 모든 유형의 구독(직접, 기업계약 및 클라우드 솔루션 공급자)이 소유한 VM은 SQL VM 리소스 공급자에 등록할 수 있습니다.

**SQL VM 리소스 공급자에 등록할 때 기본 관리 모드는 무엇입니까?**

SQL VM 리소스 공급자를 사용 하 여 등록할 때 기본 관리 모드는 *간단* 합니다. SQL VM 리소스 공급자를 사용 하 여 등록할 때 SQL Server 관리 속성이 설정 되지 않은 경우 모드가 lightweight로 설정 되 고 SQL Server 서비스가 다시 시작 되지 않습니다. 먼저 경량 모드에서 SQL VM 리소스 공급자에 등록한 다음, 유지 관리 기간 중에 전체로 업그레이드하는 것이 좋습니다. 마찬가지로, [자동 등록 기능](sql-vm-resource-provider-automatic-registration.md)을 사용 하는 경우 기본 관리도 간단 합니다.

**SQL VM 리소스 공급자에 등록하기 위한 필수 구성 요소는 무엇인가요?**

SQL Server VM에 설치 하는 것 외에는 SQL VM 리소스 공급자에 등록 하기 위한 필수 구성 요소가 없습니다. SQL IaaS 에이전트 확장이 전체 모드로 설치 된 경우에는 SQL Server 서비스가 다시 시작 되므로 유지 관리 기간 동안이 작업을 수행 하는 것이 좋습니다.

**SQL VM 리소스 공급자에 등록하면 내 VM에 에이전트를 설치하나요?**

예, 전체 관리 효율성 모드로 SQL VM 리소스 공급자를 등록 하면 에이전트가 VM에 설치 됩니다. 경량 또는 NoAgent 모드에서 등록 하는 것은 아닙니다. 

경량 모드에서 SQL VM 리소스 공급자를 등록 하면 SQL IaaS 에이전트 확장 *이진* 파일만 VM에 복사 되 고 에이전트는 설치 되지 않습니다. 이러한 이진 파일은 관리 모드가 전체로 업그레이드 될 때 에이전트를 설치 하는 데 사용 됩니다.


**내 VM에서 SQL VM 리소스 공급자를 다시 시작 SQL Server를 등록 하 시겠습니까?**

등록 중 지정한 모드에 따라 달라집니다. Lightweight 또는 NoAgent 모드가 지정 된 경우 SQL Server 서비스는 다시 시작 되지 않습니다. 그러나 관리 모드를 full로 지정 하면 SQL Server 서비스가 다시 시작 됩니다. Windows Server 버전 2008이 아닌 경우 자동 등록 기능은 SQL Server Vm을 경량 모드로 등록 합니다 .이 경우 SQL Server VM NoAgent 모드로 등록 됩니다. 

**SQL VM 리소스 공급자를 사용 하 여 등록할 때 경량 및 NoAgent 관리 모드의 차이점은 무엇 인가요?** 

NoAgent 관리 모드는 SQL Server 2008 및 SQL Server 2008 R2 on Windows Server 2008에 대해 유일 하 게 사용할 수 있는 관리 모드입니다. 이후 버전의 Windows Server에서는 사용 가능한 두 가지 관리 효율성 모드가 간단 하 고 완전 합니다. 

NoAgent 모드에서는 고객이 SQL Server 버전 및 버전 속성을 설정 해야 합니다. 경량 모드는 VM을 쿼리하여 SQL Server 인스턴스의 버전 및 에디션을 찾습니다.

**SQL Server 라이선스 유형을 지정하지 않고 SQL VM 리소스 공급자에 등록할 수 있나요?**

아니요. SQL VM 리소스 공급자에 등록하는 경우 SQL Server 라이선스 형식은 선택적 속성이 아닙니다. 모든 관리 효율성 모드 (NoAgent, 경량 및 full)에서 SQL VM 리소스 공급자를 사용 하 여 등록할 때 SQL Server 라이선스 유형을 종 량 제로 설정 하거나 Azure 하이브리드 혜택 해야 합니다. Developer 또는 Evaluation edition과 같은 SQL Server의 무료 버전이 설치 되어 있는 경우 종 량 제 라이선스로 등록 해야 합니다. Azure 하이브리드 혜택는 Enterprise 및 Standard edition과 같은 유료 버전의 SQL Server 에서만 사용할 수 있습니다.

**SQL Server IaaS 확장을 NoAgent 모드에서 full 모드로 업그레이드할 수 있나요?**

아니요. NoAgent 모드에서는 관리 효율성 모드를 full 또는 lightweight로 업그레이드할 수 없습니다. 이는 Windows Server 2008의 기술적 제한 사항입니다. 먼저 OS를 Windows Server 2008 R2 이상으로 업그레이드한 후에는 전체 관리 모드로 업그레이드할 수 있습니다. 

**SQL Server IaaS 확장을 경량 모드에서 전체 모드로 업그레이드할 수 있나요?**

예. 관리 효율성 모드를 경량에서 full로 업그레이드 하는 것은 Azure PowerShell 또는 Azure Portal를 통해 지원 됩니다. 이렇게 하면 SQL Server 서비스가 다시 시작 됩니다.

**SQL Server IaaS 확장을 전체 모드에서 NoAgent 또는 경량 관리 모드로 다운 그레이드할 수 있나요?**

아니요. SQL Server IaaS 확장 관리 모드는 다운그레이드가 지원되지 않습니다. 관리 효율성 모드는 전체 모드에서 경량 또는 NoAgent 모드로 다운 그레이드할 수 없으며 경량 모드에서 NoAgent 모드로 다운 그레이드할 수 없습니다. 

관리 효율성 모드를 전체 관리 기능으로 변경 하려면 sql 가상 컴퓨터 _리소스_ 를 삭제 하 여 sql vm 리소스 공급자에서 SQL Server VM [등록을 취소](sql-vm-resource-provider-register.md#unregister-from-rp) 하 고 다른 관리 모드에서 다시 sql vm 리소스 공급자를 사용 하 여 SQL Server VM를 다시 등록 합니다.

**Azure Portal에서 SQL VM 리소스 공급자에 등록할 수 있나요?**

아니요. Azure Portal에서는 SQL VM 리소스 공급자에 등록할 수 없습니다. SQL VM 리소스 공급자에 등록 하는 것은 Azure CLI 또는 Azure PowerShell 에서만 지원 됩니다. 

**SQL Server를 설치하기 전에 VM을 SQL VM 리소스 공급자에 등록할 수 있나요?**

아니요. VM에는 SQL VM 리소스 공급자에 성공적으로 등록 하려면 하나 이상의 SQL Server (데이터베이스 엔진) 인스턴스가 있어야 합니다. VM에 SQL Server 인스턴스가 없으면 새 Microsoft.SqlVirtualMachine 리소스가 실패 상태가 됩니다.

**SQL Server 인스턴스가 여러 개 있는 경우 VM을 SQL VM 리소스 공급자에 등록할 수 있나요?**

예. SQL VM 리소스 공급자는 SQL Server(데이터베이스 엔진) 인스턴스를 하나만 등록합니다. SQL VM 리소스 공급자는 인스턴스가 여러 개인 경우 기본 SQL Server 인스턴스를 등록합니다. 기본 인스턴스가 없으면 경량 모드 등록만 지원됩니다. 경량에서 전체 관리 모드로 업그레이드하려면 기본 SQL Server 인스턴스가 있어야 합니다. 그렇지 않으면 VM에 명명된 SQL Server 인스턴스가 하나만 있어야 합니다.

**SQL Server 장애 조치(failover) 클러스터 인스턴스를 SQL VM 리소스 공급자에 등록할 수 있나요?**

예. Azure VM의 SQL Server 장애 조치(failover) 클러스터 인스턴스는 경량 모드로 SQL VM 리소스 공급자에 등록할 수 있습니다. 그러나 SQL Server 장애 조치(failover) 클러스터 인스턴스를 전체 관리 모드로 업그레이드할 수는 없습니다.

**Always On 가용성 그룹이 구성된 경우에는 VM을 SQL VM 리소스 공급자에 등록할 수 있나요?**

예. Always On 가용성 그룹 구성에 참여하더라도 Azure VM의 SQL Server 인스턴스를 SQL VM 리소스 공급자에 등록하는 데 제한이 없습니다.

**SQL VM 리소스 공급자에 등록하거나 전체 관리 모드로 업그레이드하는 경우 비용은 어떻게 되나요?**

없음 SQL VM 리소스 공급자에 등록하거나 세 가지 관리 모드 중 하나를 사용하는 것과 관련된 요금은 없습니다. 리소스 공급자를 사용한 SQL Server VM 관리는 완전히 무료입니다. 

**각 관리 모드를 사용할 때 성능에 미치는 영향은 무엇입니까?**

*에이전트 없음* 및 *경량* 관리 모드를 사용하는 경우에는 영향을 미치지 않습니다. OS에 설치된 두 서비스로부터 *전체* 관리 모드를 사용하는 경우 최소한의 영향만 미칩니다. 이러한 서비스는 작업 관리자를 통해 모니터링할 수 있으며 기본 제공 Windows 서비스 콘솔에 표시됩니다. 

두 서비스 이름은 다음과 같습니다.
- `SqlIaaSExtensionQuery`(표시 이름 - `Microsoft SQL Server IaaS Query Service`)
- `SQLIaaSExtension`(표시 이름 - `Microsoft SQL Server IaaS Agent`)

**확장 어떻게 할까요? 제거 하 시겠습니까?**

SQL VM 리소스 공급자에서 SQL Server VM [등록을 취소](sql-vm-resource-provider-register.md#unregister-from-rp) 하 여 확장을 제거 합니다. 

## <a name="next-steps"></a>다음 단계

SQL Server IaaS 확장을 설치 하 여 Azure Vm에서 SQL Server 하려면 [자동 설치](sql-vm-resource-provider-automatic-registration.md), [단일 vm](sql-vm-resource-provider-register.md)또는 [vm 대량으로](sql-vm-resource-provider-bulk-register.md)문서를 참조 하세요.

Azure Virtual Machines의 SQL Server 실행에 대한 자세한 내용은 [Azure Virtual Machines의 SQL Server란?](sql-server-on-azure-vm-iaas-what-is-overview.md)을 참조하세요.
