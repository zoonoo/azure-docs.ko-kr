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
ms.subservice: management
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 11/07/2020
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: fdff3f6144f7099f3f61cfe57186357e17136e9f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103225492"
---
# <a name="automate-management-with-the-sql-server-iaas-agent-extension"></a>SQL Server IaaS 에이전트 확장을 사용 하 여 관리 자동화
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]


SQL Server IaaS 에이전트 확장 (SqlIaasExtension)은 Azure Virtual Machines (Vm)의 SQL Server에서 실행 되어 관리 및 관리 작업을 자동화 합니다. 

이 문서에서는 확장에 대 한 개요를 제공 합니다. SQL Server IaaS 확장을 설치 하 여 Azure Vm에서 SQL Server 하려면 [자동 설치](sql-agent-extension-automatic-registration-all-vms.md), [단일 vm](sql-agent-extension-manually-register-single-vm.md)또는 [vm 대량으로](sql-agent-extension-manually-register-vms-bulk.md)문서를 참조 하세요. 

## <a name="overview"></a>개요

SQL Server IaaS 에이전트 확장을 사용 하면 Azure Portal와 통합할 수 있으며, 관리 모드에 따라 Azure Vm의 SQL Server에 대 한 다양 한 기능 혜택을 잠금 해제할 수 있습니다. 

- **기능 이점**: 확장이 포털 관리, 라이선스 유연성, 자동화 된 백업, 자동화 된 패치 등의 다양 한 자동화 기능 혜택을 해제 합니다. 자세한 내용은이 문서의 뒷부분에 나오는 [기능 이점](#feature-benefits) 을 참조 하세요. 

- **규정 준수**: 확장은 제품 약관에 지정 된 대로 Azure 하이브리드 혜택 사용 하도록 설정 되었음을 Microsoft에 알리기 위한 간단한 방법을 제공 합니다. 이 프로세스로 각 리소스마다 라이선스 등록 양식을 관리할 필요가 없습니다.  

- **무료**: 세 가지 관리 효율성 모드 모두에서 확장은 완전히 무료로 제공 됩니다. 확장과 관련 된 추가 비용이 나 관리 모드를 변경 하는 것은 없습니다. 

- **간소화 된 라이선스 관리**: 확장 SQL Server 라이선스 관리를 간소화 하 고 [Azure Portal](manage-sql-vm-portal.md), PowerShell 또는 Azure CLI를 사용 하 여 Azure 하이브리드 혜택 사용 하도록 설정 된 SQL Server vm을 신속 하 게 식별할 수 있습니다. 

   # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

   ```powershell-interactive
   Get-AzSqlVM | Where-Object {$_.LicenseType -eq 'AHUB'}
   ```

   # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

   ```azurecli-interactive
   $ az sql vm list --query "[?sqlServerLicenseType=='AHUB']"
   ```
   ---


> [!IMPORTANT]
> SQL IaaS 에이전트 확장은 Azure Virtual Machines 내에서 SQL Server를 사용 하는 경우 고객에 게 선택적 혜택을 제공 하기 위한 express 용도의 데이터를 수집 합니다. Microsoft는 고객의 사전 동의가 없는 라이선스 감사에는이 데이터를 사용 하지 않습니다. 자세한 내용은 [SQL Server 개인 정보 취급 방침](/sql/sql-server/sql-server-privacy#non-personal-data) 을 참조 하세요.


## <a name="feature-benefits"></a>기능 이점 

SQL Server IaaS 에이전트 확장은 SQL Server VM 관리에 대 한 다양 한 기능 혜택을 해제 합니다. 

다음 표에서는 이러한 이점에 대해 자세히 설명 합니다. 


| 기능 | Description |
| --- | --- |
| **포털 관리** | [포털에서 관리](manage-sql-vm-portal.md)의 잠금을 해제 하 여 모든 SQL Server vm을 한 곳에서 볼 수 있으므로 포털에서 직접 SQL 특정 기능을 사용 하거나 사용 하지 않도록 설정할 수 있습니다. <br/> 관리 모드: 경량 & full|  
| **자동화 된 백업** |VM에 있는 SQL Server의 기본 인스턴스나 [제대로 설치된](frequently-asked-questions-faq.md#administration) 명명된 인스턴스에 대한 모든 데이터베이스 백업 예약을 자동화합니다. 자세한 내용은 [Azure 가상 머신에서 SQL Server에 대한 자동화된 백업(Resource Manager)](automated-backup-sql-2014.md)을 참조하세요. <br/> 관리 모드: 전체|
| **자동화 된 패치** |VM에 대 한 중요 한 Windows 및 SQL Server 보안 업데이트가 수행 될 수 있는 유지 관리 기간을 구성 하 여 작업에 대 한 사용량이 많은 시간에 업데이트를 방지할 수 있습니다. 자세한 내용은 [Azure 가상 머신에서 SQL Server에 대한 자동화된 패치(Resource Manager)](automated-patching.md)를 참조하세요. <br/> 관리 모드: 전체|
| **Azure Key Vault 통합** |SQL Server VM에서 Azure Key Vault를 자동으로 설치하고 구성할 수 있습니다. 자세한 내용은 [Azure Virtual Machines에서 SQL Server에 대한 Azure Key Vault 통합 구성(Resource Manager)](azure-key-vault-integration-configure.md)을 참조하세요. <br/> 관리 모드: 전체|
| **포털에서 디스크 사용률 보기** | Azure Portal에서 SQL 데이터 파일의 디스크 사용률에 대 한 그래픽 표현을 볼 수 있습니다.  <br/> 관리 모드: 전체 | 
| **유연한 라이선스** | 사용자 고유 라이선스 (Azure 하이브리드 혜택 라고도 함)에서 종 량 제 라이선스 모델로 원활 하 게 전환 하 고 다시 [전환](licensing-model-azure-hybrid-benefit-ahb-change.md) 하 여 비용을 절감 하세요. <br/> 관리 모드: 경량 & full| 
| **유연한 버전/버전** | SQL Server [버전](change-sql-server-version.md) [또는 버전](change-sql-server-edition.md) 을 변경 하려는 경우 전체 SQL Server VM를 다시 배포 하지 않고도 Azure Portal 내에서 메타 데이터를 업데이트할 수 있습니다.  <br/> 관리 모드: 경량 & full| 


## <a name="management-modes"></a>관리 모드

SQL IaaS 확장을 세 가지 관리 모드로 등록 하도록 선택할 수 있습니다. 

- **경량** 모드는 확장 이진 파일을 VM에 복사 하지만 에이전트는 설치 하지 않으며 SQL Server 서비스를 다시 시작 하지 않습니다. 경량 모드는 SQL Server 라이선스 유형과 버전의 변경을 지원 하 고 제한 된 포털 관리를 제공 합니다. 여러 인스턴스 또는 FCI (장애 조치 (failover) 클러스터 인스턴스)에 참여 하는 Vm을 SQL Server 하는 경우이 옵션을 사용 합니다. [자동 등록](sql-agent-extension-automatic-registration-all-vms.md) 기능을 사용 하는 경우 또는 수동 등록 중에 관리 유형이 지정 되지 않은 경우에는 경량 모드가 기본 관리 모드입니다. 경량 모드를 사용하는 경우 메모리 또는 CPU에 영향을 주지 않으며 관련 비용이 없습니다. 먼저 SQL Server VM을 경량 모드로 등록한 다음, 예약된 유지 관리 기간 동안 전체 모드로 업그레이드하는 것이 좋습니다. 

- **전체** 모드에서는 모든 기능을 제공 하기 위해 SQL IaaS 에이전트를 VM에 설치 하지만 SQL Server 서비스 및 시스템 관리자 권한을 다시 시작 해야 합니다. 단일 인스턴스가 있는 SQL Server VM을 관리하는 데 이 옵션을 사용합니다. 전체 모드에서는 메모리 및 CPU에 최소한의 영향을 주는 두 개의 Windows 서비스가 설치됩니다. 이러한 서비스는 작업 관리자를 통해 모니터링할 수 있습니다. 전체 관리 모드를 사용하는 것과 관련된 비용은 없습니다. 

- **에이전트 없음** 모드는 Windows Server 2008에 설치된 SQL Server 2008 및 SQL Server 2008 R2 전용 모드입니다. 에이전트 없음 모드를 사용하는 경우 메모리 또는 CPU에는 영향을 주지 않습니다. NoAgent 관리 모드 사용과 관련 된 비용은 없으며, SQL Server 다시 시작 되지 않으며, 에이전트가 VM에 설치 되지 않습니다. 

Azure PowerShell를 사용 하 여 SQL Server IaaS 에이전트의 현재 모드를 볼 수 있습니다. 

  ```powershell-interactive
  # Get the SqlVirtualMachine
  $sqlvm = Get-AzSqlVM -Name $vm.Name  -ResourceGroupName $vm.ResourceGroupName
  $sqlvm.SqlManagementType
  ```


## <a name="installation"></a>설치

SQL Server IaaS 에이전트 확장을 사용 하 여 SQL Server VM를 등록 하 여 가상 컴퓨터 리소스와는 _별도의_ 리소스인 구독 내에서 **SQL 가상 컴퓨터** _리소스_ 를 만듭니다. 확장에서 SQL Server VM 등록을 취소 하면 **SQL 가상 컴퓨터** _리소스가_ 제거 되지만 실제 가상 컴퓨터는 삭제 되지 않습니다.

Azure Portal를 통해 SQL Server VM Azure Marketplace 이미지를 배포 하면 SQL Server VM 확장에 자동으로 등록 됩니다. 그러나 Azure 가상 머신에서 SQL Server를 자동으로 설치 하거나 사용자 지정 VHD에서 Azure 가상 머신을 프로 비전 하도록 선택 하는 경우 기능 혜택을 해제 하려면 SQL IaaS 확장에 SQL Server VM를 등록 해야 합니다. 

확장을 경량 모드로 등록 하면 이진 파일이 복사 되지만 에이전트는 VM에 설치 되지 않습니다. 확장이 전체 관리 모드로 업그레이드 될 때 에이전트가 VM에 설치 됩니다. 

확장에 등록 하는 방법에는 다음 세 가지가 있습니다. 
- [구독에서 모든 현재 및 미래의 Vm에 대해 자동으로](sql-agent-extension-automatic-registration-all-vms.md)
- [단일 VM에 대해 수동으로](sql-agent-extension-manually-register-single-vm.md)
- [대량으로 여러 Vm에 대 한 수동](sql-agent-extension-manually-register-vms-bulk.md)

### <a name="named-instance-support"></a>명명 된 인스턴스 지원

SQL Server IaaS 에이전트 확장은 가상 머신에서 사용할 수 있는 유일한 SQL Server 인스턴스인 경우 SQL Server의 명명 된 인스턴스에서 작동 합니다. VM에 기본 인스턴스가 없는 경우 명명 된 SQL Server 인스턴스가 여러 개 있는 Vm에 확장을 설치 하지 못합니다. 

SQL Server의 명명 된 인스턴스를 사용 하려면 Azure 가상 컴퓨터를 배포 하 고 명명 된 단일 SQL Server 인스턴스를 설치 하 고 [SQL IaaS 확장](sql-agent-extension-manually-register-single-vm.md)에 등록 합니다.

또는 Azure Marketplace SQL Server 이미지를 사용 하 여 명명 된 인스턴스를 사용 하려면 다음 단계를 수행 합니다. 

   1. Azure Marketplace에서 SQL Server VM을 배포합니다. 
   1. SQL IaaS 에이전트 확장에서 SQL Server VM [등록을 취소](sql-agent-extension-manually-register-single-vm.md#unregister-from-extension) 합니다. 
   1. SQL Server VM 내에서 SQL Server를 완전히 제거합니다.
   1. SQL Server VM 내에 명명된 인스턴스를 사용하여 SQL Server를 설치합니다. 
   1. [SQL IaaS 에이전트 확장을 사용 하 여 VM을 등록](sql-agent-extension-manually-register-single-vm.md#register-with-extension)합니다. 

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


## <a name="in-region-data-residency"></a>지역 내 데이터 보존
Azure SQL 가상 머신과 SQL IaaS 에이전트 확장은 고객 데이터를 배포 된 지역 외부로 이동 하거나 저장 하지 않습니다.

## <a name="next-steps"></a>다음 단계

SQL Server IaaS 확장을 설치 하 여 Azure Vm에서 SQL Server 하려면 [자동 설치](sql-agent-extension-automatic-registration-all-vms.md), [단일 vm](sql-agent-extension-manually-register-single-vm.md)또는 [vm 대량으로](sql-agent-extension-manually-register-vms-bulk.md)문서를 참조 하세요.

Azure Virtual Machines의 SQL Server 실행에 대한 자세한 내용은 [Azure Virtual Machines의 SQL Server란?](sql-server-on-azure-vm-iaas-what-is-overview.md)을 참조하세요.

자세히 알아보려면 질문과 [대답](frequently-asked-questions-faq.md)을 참조 하세요. 
