---
title: IaaS 에이전트 확장으로 관리 작업 자동화
description: 이 문서에서는 특정 SQL Server 관리 작업을 자동화하는 SQL Server IaaS 에이전트 확장을 관리하는 방법을 설명합니다. 여기에는 자동화된 백업, 자동화된 패치 적용 및 Azure Key Vault 통합이 포함됩니다.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
editor: ''
tags: azure-resource-manager
ms.assetid: effe4e2f-35b5-490a-b5ef-b06746083da4
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/30/2019
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: 638a7fa96f4112f9805fd46618dc0acd1c2f382f
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87063238"
---
# <a name="automate-management-tasks-on-azure-virtual-machines-by-using-the-sql-server-iaas-agent-extension"></a>SQL Server IaaS 에이전트 확장을 사용하여 Azure 가상 머신에서 관리 작업 자동화
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]


SQL Server IaaS 에이전트 확장(SqlIaasExtension)은 관리 작업을 자동화하기 위해 Azure 가상 머신에서 실행됩니다. 이 문서에서는 확장이 지원하는 서비스에 대한 개요를 제공합니다. 또한 확장의 설치, 상태 및 제거에 대한 지침을 제공합니다.

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

이 문서의 클래식 버전을 보려면 [SQL Server VM에 대한 SQL Server IaaS 에이전트 확장(클래식)](../../../virtual-machines/windows/sqlclassic/virtual-machines-windows-classic-sql-server-agent-extension.md)을 참조하세요.


## <a name="supported-services"></a>지원되는 서비스
SQL Server IaaS 에이전트 확장은 다음 관리 작업을 지원합니다.

| 관리 기능 | Description |
| --- | --- |
| **SQL Server 자동화된 백업** |VM에 있는 SQL Server의 기본 인스턴스나 [제대로 설치된](frequently-asked-questions-faq.md#administration) 명명된 인스턴스에 대한 모든 데이터베이스 백업 예약을 자동화합니다. 자세한 내용은 [Azure 가상 머신에서 SQL Server에 대한 자동화된 백업(Resource Manager)](automated-backup-sql-2014.md)을 참조하세요. |
| **SQL Server 자동화된 패치 적용** |워크로드가 가장 많은 시간에 업데이트하지 않도록 VM에 대한 중요한 Windows 업데이트가 수행될 유지 관리 기간을 구성할 수 있습니다. 자세한 내용은 [Azure 가상 머신에서 SQL Server에 대한 자동화된 패치(Resource Manager)](automated-patching.md)를 참조하세요. |
| **Azure Key Vault 통합** |SQL Server VM에서 Azure Key Vault를 자동으로 설치하고 구성할 수 있습니다. 자세한 내용은 [Azure Virtual Machines에서 SQL Server에 대한 Azure Key Vault 통합 구성(Resource Manager)](azure-key-vault-integration-configure.md)을 참조하세요. |

SQL Server Iaas 에이전트 확장이 설치되고 실행되면 다음에서 관리 기능을 사용할 수 있습니다.

* Azure Portal에서 가상 머신의 SQL Server 패널에서 그리고 Azure Marketplace의 SQL Server 이미지에 대한 Azure PowerShell을 통해 사용
* 확장의 수동 설치에 대한 Azure PowerShell에서 사용 

## <a name="prerequisites"></a>필수 구성 요소
다음은 VM에서 SQL Server IaaS 에이전트 확장을 사용하기 위한 요구 사항입니다.

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
* SQL Server 2019

**Azure PowerShell**:

* [최신 Azure PowerShell 명령 다운로드 및 구성](/powershell/azure/)

[!INCLUDE [updated-for-az.md](../../../../includes/updated-for-az.md)]


##  <a name="installation"></a>설치
SQL Server IaaS 확장은 [SQL Server VM 리소스 공급자](sql-vm-resource-provider-register.md)를 사용 하 여 SQL Server VM를 등록할 때 설치 됩니다. 필요한 경우 아래 PowerShell 명령을 사용하여 SQL Server IaaS 에이전트를 수동으로 설치할 수 있습니다. 

  ```powershell-interactive
    Set-AzVMSqlServerExtension -VMName "sql2017" `
    -ResourceGroupName "LabsqlIAASagent" -Name "SQLIaasExtension" `
    -Version "2.0" -Location "Central US";  
  ```

> [!NOTE]
> 확장을 설치하면 SQL Server 서비스가 다시 시작됩니다. 


### <a name="install-on-a-vm-with-a-single-named-sql-server-instance"></a>단일 명명된 SQL Server 인스턴스로 VM에 설치
기본 인스턴스를 제거하고 IaaS 확장을 다시 설치하면 SQL Server IaaS 확장이 SQL Server의 명명된 인스턴스에서 작동합니다.

SQL Server의 명명된 인스턴스를 사용하려면 다음 단계를 수행합니다.
   1. Azure Marketplace에서 SQL Server VM을 배포합니다. 
   1. [Azure Portal](https://portal.azure.com)에서 IaaS 확장을 제거합니다.
   1. SQL Server VM 내에서 SQL Server를 완전히 제거합니다.
   1. SQL Server VM 내에 명명된 인스턴스를 사용하여 SQL Server를 설치합니다. 
   1. Azure Portal에서 IaaS 확장을 제거합니다.  


## <a name="get-the-status-of-the-sql-server-iaas-extension"></a>SQL Server IaaS 확장의 상태 확인
확장이 설치되어 있는지 확인하는 한 가지 방법은 Azure Portal에서 에이전트 상태를 확인하는 것입니다. 가상 머신 창에서 **모든 설정**을 선택하고 **확장**을 선택합니다. **SqlIaasExtension** 확장이 나열되어야 합니다.

![Azure Portal의 SQL Server IaaS 에이전트 확장 상태](./media/sql-server-iaas-agent-extension-automate-management/azure-rm-sql-server-iaas-agent-portal.png)

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

## <a name="removal"></a>제거
Azure Portal에서 가상 머신 속성의 **확장** 창에서 줄임표를 선택하여 확장을 제거할 수 있습니다. 그런 다음, **삭제**를 선택합니다.

![Azure Portal에서 SQL Server IaaS 에이전트 확장 제거](./media/sql-server-iaas-agent-extension-automate-management/azure-rm-sql-server-iaas-agent-uninstall.png)

**Remove-AzVMSqlServerExtension** PowerShell cmdlet을 사용할 수도 있습니다.

   ```powershell-interactive
    Remove-AzVMSqlServerExtension -ResourceGroupName "resourcegroupname" -VMName "vmname" -Name "SqlIaasExtension"
   ```

## <a name="next-steps"></a>다음 단계
확장에서 지원하는 서비스 중 하나를 사용하기 시작합니다. 자세한 내용은 이 문서의 [지원되는 서비스](#supported-services) 섹션에 나와 있는 문서를 참조하세요.

Azure Virtual Machines의 SQL Server 실행에 대한 자세한 내용은 [Azure Virtual Machines의 SQL Server란?](sql-server-on-azure-vm-iaas-what-is-overview.md)을 참조하세요.
