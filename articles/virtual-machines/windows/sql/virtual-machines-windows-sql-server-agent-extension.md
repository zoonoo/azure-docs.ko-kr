---
title: SQL VM에서 관리 작업 자동화(Resource Manager) | Microsoft 문서
description: 이 문서에서는 특정 SQL Server 관리 작업을 자동화하는 SQL Server 에이전트 확장을 관리하는 방법을 설명합니다. 여기에는 자동화된 Backup, 자동화된 패치 적용 및 Azure Key Vault 통합이 포함됩니다.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
manager: craigg
editor: ''
tags: azure-resource-manager
ms.assetid: effe4e2f-35b5-490a-b5ef-b06746083da4
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 07/12/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 2943a1501e1d81ff4884c21b5aa3861e16523bbb
ms.sourcegitcommit: be9fcaace62709cea55beb49a5bebf4f9701f7c6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/17/2019
ms.locfileid: "65827719"
---
# <a name="automate-management-tasks-on-azure-virtual-machines-with-the-sql-server-agent-extension-resource-manager"></a>SQL Server 에이전트 확장을 사용하여 Azure Virtual Machines에서 관리 작업 자동화(Resource Manager)
> [!div class="op_single_selector"]
> * [리소스 관리자](virtual-machines-windows-sql-server-agent-extension.md)
> * [클래식](../sqlclassic/virtual-machines-windows-classic-sql-server-agent-extension.md)

SQL Server IaaS 에이전트 확장(SqlIaasExtension)은 관리 작업을 자동화하기 위해 Azure 가상 머신에서 실행됩니다. 이 문서에서는 설치, 상태 및 제거에 대한 지침뿐만 아니라 확장에서 지원되는 서비스의 개요를 제공합니다.

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

이 문서의 클래식 버전을 보려면 [SQL Server VM에 대한 SQL Server 에이전트 확장 클래식](../sqlclassic/virtual-machines-windows-classic-sql-server-agent-extension.md)을 참조하세요.

## <a name="supported-services"></a>지원되는 서비스
SQL Server IaaS 에이전트 확장은 다음 관리 작업을 지원합니다.

| 관리 기능 | 설명 |
| --- | --- |
| **SQL 자동화된 Backup** |예약을 자동화 합니다 모든 데이터베이스에 대 한 백업에 대 한 기본 인스턴스 또는 [제대로 설치 된](virtual-machines-windows-sql-server-iaas-faq.md#administration) VM에서 SQL Server의 명명 된 인스턴스. 자세한 내용은 [Azure Virtual Machines에서 SQL Server에 대한 자동화된 백업(리소스 관리자)](virtual-machines-windows-sql-automated-backup.md)을 참조하세요. |
| **SQL 자동화된 패치** |워크로드가 가장 많은 시간에 업데이트하지 않도록 VM에 대한 중요한 Windows 업데이트가 수행될 유지 관리 기간을 구성할 수 있습니다. 자세한 내용은 [Azure Virtual Machines에서 SQL Server에 대한 자동화된 패치(리소스 관리자)](virtual-machines-windows-sql-automated-patching.md)를 참조하세요. |
| **Azure Key Vault 통합** |이 서비스를 통해 SQL Server VM에서 Azure Key Vault를 자동으로 설치 및 구성할 수 있습니다. 자세한 내용은 [Azure VM에서 SQL Server에 대한 Azure Key Vault 통합 구성(리소스 매니저)](virtual-machines-windows-ps-sql-keyvault.md)을 참조하세요. |

SQL Server IaaS 에이전트 확장을 설치하고 실행하면 Azure Portal에 있는 가상 머신의 SQL Server 패널, SQL Server Marketplace 이미지에 대한 Azure PowerShell, 확장 수동 설치를 위한 Azure PowerShell을 통해 이러한 관리 기능을 사용할 수 있습니다. 

## <a name="prerequisites"></a>필수 조건
VM에서 SQL Server IaaS 에이전트 확장을 사용하기 위한 요구 사항:

**운영 체제**:

* Windows Server 2008 R2
* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016

**SQL Server 버전**:

* SQL Server 2008 
* SQL Server 2008 R2
* SQL Server 2012
* SQL Server 2014
* SQL Server 2016

**Azure PowerShell**:

* [최신 Azure PowerShell 명령 다운로드 및 구성](/powershell/azure/overview)

[!INCLUDE [updated-for-az.md](../../../../includes/updated-for-az.md)]

> [!IMPORTANT]
> 이번에 [SQL Server IaaS 에이전트 확장](virtual-machines-windows-sql-server-agent-extension.md)은 Azure에서 SQL Server FCI에 대해 지원되지 않습니다. FCI에 참여하는 VM에서 확장을 제거하는 것이 좋습니다. 확장이 지원하는 기능은 에이전트를 제거한 후 SQL VM에 사용할 수 없습니다.

## <a name="installation"></a>설치
SQL Server IaaS 에이전트 확장은 SQL Server 가상 머신 갤러리 이미지 중 하나를 프로비전할 때 자동으로 설치됩니다. SQL IaaS 확장은 SQL Server VM에서 단일 인스턴스에 대 한 관리 효율성을 제공합니다. 기본 인스턴스의 경우 기본 인스턴스를 확장 한 다음 작동 하 고 다른 인스턴스 관리를 지원 하지 않습니다. 기본 인스턴스가 없는 하지만 명명 된 인스턴스를 하나만 있으면 명명된 된 인스턴스를 관리 합니다. 기본 인스턴스가 없는 경우 명명 된 인스턴스가 여러 개 확장 설치에 실패 합니다. 



이러한 SQL Server VM 중 하나에서 확장을 수동으로 다시 설치해야 하는 경우 다음 PowerShell 명령을 사용합니다.

```powershell
Set-AzVMSqlServerExtension -ResourceGroupName "resourcegroupname" -VMName "vmname" -Name "SqlIaasExtension" -Version "2.0" -Location "East US 2"
```

> [!WARNING]
> 확장 프로그램이 아직 설치되지 않은 경우 확장 프로그램을 설치하면 SQL Server 서비스가 다시 시작됩니다. 그러나 SQL IaaS 확장을 업데이트해도 SQL Server 서비스가 다시 시작되지 않습니다. 

> [!NOTE]
> 사용자 지정 SQL Server 이미지에 SQL Server IaaS 에이전트 확장을 설치할 수 있지만, 기능은 현재 제한 [라이선스 유형을 변경](virtual-machines-windows-sql-ahb.md)합니다. SQL IaaS 확장에서 제공 하는 다른 기능에만 작동 [SQL Server VM 갤러리 이미지](virtual-machines-windows-sql-server-iaas-overview.md#get-started-with-sql-vms) (종 량 제 또는 bring your-own license).

### <a name="use-a-single-named-instance"></a>명명 된 인스턴스는 단일을 사용 합니다.
SQL IaaS 확장 작동 명명된 된 인스턴스를 사용 하 여 SQL Server 이미지에는 기본 인스턴스를 제대로 제거할 경우 IaaS 확장을 다시 설치 하는 경우.

SQL Server의 명명된 된 인스턴스를 사용 하려면 다음을 수행 합니다.
   1. Marketplace에서 SQL Server VM을 배포 합니다. 
   1. 내에서 IaaS 확장을 제거 합니다 [Azure portal](https://portal.azure.com)합니다.
   1. SQL Server SQL Server VM 내에서 완전히 제거 합니다.
   1. SQL Server VM 내에서 명명된 된 인스턴스를 사용 하 여 SQL Server를 설치 합니다. 
   1. Azure portal 내에서 IaaS 확장을 설치 합니다.  

## <a name="status"></a>상태
확장이 설치되어 있는지 확인하는 한 가지 방법은 Azure Portal에서 에이전트 상태를 확인하는 것입니다. 가상 머신 창에서 **모든 설정**을 선택하고 **확장**을 클릭합니다. **SqlIaasExtension** 확장이 나열되어야 합니다.

![Azure Portal에서 SQL Server IaaS 에이전트 확장](./media/virtual-machines-windows-sql-server-agent-extension/azure-rm-sql-server-iaas-agent-portal.png)

**Get-AzVMSqlServerExtension** Azure PowerShell cmdlet을 사용할 수도 있습니다.

    Get-AzVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"

이전 명령은 에이전트가 설치되어 있는지 확인하고 일반적인 상태 정보를 제공합니다. 다음 명령을 사용하여 자동화된 Backup 및 패치에 대한 특정 상태 정보를 가져올 수도 있습니다.

    $sqlext = Get-AzVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"
    $sqlext.AutoPatchingSettings
    $sqlext.AutoBackupSettings

## <a name="removal"></a>제거
Azure portal에서 줄임표를 클릭 하 여 확장을 제거할 수 있습니다 합니다 **확장** 가상 머신 속성의 창. 그런 다음 **삭제**를 클릭합니다.

![Azure Portal에서 SQL Server IaaS 에이전트 확장 제거](./media/virtual-machines-windows-sql-server-agent-extension/azure-rm-sql-server-iaas-agent-uninstall.png)

**Remove-AzVMSqlServerExtension** PowerShell cmdlet을 사용할 수도 있습니다.

    Remove-AzVMSqlServerExtension -ResourceGroupName "resourcegroupname" -VMName "vmname" -Name "SqlIaasExtension"

## <a name="next-steps"></a>다음 단계
확장에 의해 지원되는 서비스 중 하나를 사용하기 시작합니다. 자세한 내용은 이 문서의 [지원되는 서비스](#supported-services) 섹션에서 참조된 문서를 참조하세요.

Azure Virtual Machines의 SQL Server 실행에 대한 자세한 내용은 [Azure Virtual Machines의 SQL Server 개요](virtual-machines-windows-sql-server-iaas-overview.md)를 참조하세요.

