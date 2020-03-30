---
title: IaaS 에이전트 확장으로 관리 작업 자동화
description: 이 문서에서는 특정 SQL Server 관리 작업을 자동화하는 SQL Server IaaS 에이전트 확장을 관리하는 방법에 대해 설명합니다. 여기에는 자동화된 백업, 자동 패치 및 Azure Key Vault 통합이 포함됩니다.
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
ms.date: 08/30/2019
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: 3d16c1950cbae0bcc7dd858e5520eb8bfc6e496d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77030781"
---
# <a name="automate-management-tasks-on-azure-virtual-machines-by-using-the-sql-server-iaas-agent-extension"></a>SQL Server IaaS 에이전트 확장을 사용하여 Azure 가상 시스템의 관리 작업 자동화
> [!div class="op_single_selector"]
> * [리소스 관리자](virtual-machines-windows-sql-server-agent-extension.md)
> * [고전적인](../sqlclassic/virtual-machines-windows-classic-sql-server-agent-extension.md)

SQL Server IaaS 에이전트 확장(SqlIaasExtension)은 관리 작업을 자동화하기 위해 Azure 가상 머신에서 실행됩니다. 이 문서에서는 확장이 지원하는 서비스에 대한 개요를 제공합니다. 이 문서에서는 확장의 설치, 상태 및 제거에 대한 지침도 제공합니다.

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

이 문서의 클래식 버전을 보려면 [SQL Server VM(클래식)에 대한 SQL Server IaaS 에이전트 확장을](../sqlclassic/virtual-machines-windows-classic-sql-server-agent-extension.md)참조하십시오.


## <a name="supported-services"></a>지원되는 서비스
SQL Server IaaS 에이전트 확장은 다음 관리 작업을 지원합니다.

| 관리 기능 | 설명 |
| --- | --- |
| **SQL Server 자동 백업** |기본 인스턴스 또는 VM에 SQL Server의 [올바르게 설치된](virtual-machines-windows-sql-server-iaas-faq.md#administration) 명명된 인스턴스에 대한 모든 데이터베이스에 대한 백업 일정을 자동화합니다. 자세한 내용은 [Azure 가상 컴퓨터(리소스 관리자)에서 SQL Server에 대한 자동화된 백업을](virtual-machines-windows-sql-automated-backup.md)참조하십시오. |
| **SQL Server 자동 패치** |워크로드가 가장 많은 시간에 업데이트하지 않도록 VM에 대한 중요한 Windows 업데이트가 수행될 유지 관리 기간을 구성할 수 있습니다. 자세한 내용은 [Azure 가상 컴퓨터(리소스 관리자)의 SQL Server에 대한 자동 패치를](virtual-machines-windows-sql-automated-patching.md)참조하십시오. |
| **Azure 키 볼트 통합** |SQL Server VM에서 Azure Key Vault를 자동으로 설치하고 구성할 수 있습니다. 자세한 내용은 [Azure 가상 시스템(리소스 관리자)에서 SQL Server에 대한 Azure 키 자격 증명 모음 통합 구성을](virtual-machines-windows-ps-sql-keyvault.md)참조하십시오. |

SQL Server Iaas 에이전트 확장을 설치하고 실행한 후에는 관리 기능을 사용할 수 있습니다.

* Azure 포털의 가상 컴퓨터의 SQL Server 패널과 Azure 마켓플레이스의 SQL Server 이미지에 대한 Azure PowerShell을 통해
* 확장의 수동 설치에 대 한 Azure PowerShell을 통해. 

## <a name="prerequisites"></a>사전 요구 사항
다음은 VM에서 SQL Server IaaS 에이전트 확장을 사용하는 요구 사항입니다.

**운영 체제**:

* Windows Server 2008 R2
* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016
* Windows Server 2019 

**SQL 서버 버전**:

* SQL Server 2008 
* SQL Server 2008 R2
* SQL Server 2012
* SQL Server 2014
* SQL Server 2016
* SQL Server 2017
* SQL Server 2019

**Azure 파워쉘**:

* [최신 Azure PowerShell 명령 다운로드 및 구성](/powershell/azure/overview)

[!INCLUDE [updated-for-az.md](../../../../includes/updated-for-az.md)]


##  <a name="installation"></a>설치
SQL Server IaaS 확장은 [SQL VM 리소스 공급자에](virtual-machines-windows-sql-register-with-resource-provider.md)SQL Server VM을 등록할 때 설치됩니다. 필요한 경우 아래 PowerShell 명령을 사용하여 SQL Server IaaS 에이전트를 수동으로 설치할 수 있습니다. 

  ```powershell-interactive
    Set-AzVMSqlServerExtension -VMName "sql2017" `
    -ResourceGroupName "LabsqlIAASagent" -Name "SQLIaasExtension" `
    -Version "2.0" -Location "Central US";  
  ```

> [!NOTE]
> 확장을 설치하면 SQL Server 서비스가 다시 시작됩니다. 


### <a name="install-on-a-vm-with-a-single-named-sql-server-instance"></a>명명된 단일 SQL Server 인스턴스를 통해 VM에 설치
SQL Server IaaS 확장은 기본 인스턴스가 제거되고 IaaS 확장이 다시 설치된 경우 SQL Server에서 명명된 인스턴스와 함께 작동합니다.

SQL Server의 명명된 인스턴스를 사용하려면 다음 단계를 따르십시오.
   1. Azure 마켓플레이스에서 SQL 서버 VM을 배포합니다. 
   1. [Azure 포털에서](https://portal.azure.com)IaaS 확장을 제거합니다.
   1. SQL Server VM 내에서 SQL 서버를 완전히 제거합니다.
   1. SQL Server VM 내에 명명된 인스턴스를 가진 SQL Server를 설치합니다. 
   1. Azure 포털에서 IaaS 확장을 설치합니다.  


## <a name="get-the-status-of-the-sql-server-iaas-extension"></a>SQL 서버 IaaS 확장의 상태 가져옵니다.
확장이 설치되어 있는지 확인하는 한 가지 방법은 Azure Portal에서 에이전트 상태를 확인하는 것입니다. 가상 시스템 창에서 **모든 설정을** 선택한 다음 **확장을 선택합니다.** **SqlIaasExtension** 확장이 나열되어야 합니다.

![Azure 포털에서 SQL 서버 IaaS 에이전트 확장 상태](./media/virtual-machines-windows-sql-server-agent-extension/azure-rm-sql-server-iaas-agent-portal.png)

**Get-AzVMSqlServerExtension** Azure PowerShell cmdlet을 사용할 수도 있습니다.

   ```powershell-interactive
   Get-AzVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"
   ```

이전 명령은 에이전트가 설치되어 있는지 확인하고 일반 상태 정보를 제공합니다. 다음 명령을 사용하여 자동화된 백업 및 패치에 대한 특정 상태 정보를 얻을 수 있습니다.

   ```powershell-interactive
    $sqlext = Get-AzVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"
    $sqlext.AutoPatchingSettings
    $sqlext.AutoBackupSettings
   ```

## <a name="removal"></a>제거
Azure 포털에서 가상 컴퓨터 속성의 **확장** 창에서 타원을 선택하여 확장을 제거할 수 있습니다. 그런 다음, **삭제**를 선택합니다.

![Azure 포털에서 SQL 서버 IaaS 에이전트 확장 제거](./media/virtual-machines-windows-sql-server-agent-extension/azure-rm-sql-server-iaas-agent-uninstall.png)

또한 **제거-AzVMSqlServer내선 익스텐션** 파워쉘 cmdlet을 사용할 수 있습니다.

   ```powershell-interactive
    Remove-AzVMSqlServerExtension -ResourceGroupName "resourcegroupname" -VMName "vmname" -Name "SqlIaasExtension"
   ```

## <a name="next-steps"></a>다음 단계
확장이 지원하는 서비스 중 하나를 사용하기 시작합니다. 자세한 내용은 이 문서의 [지원되는 서비스](#supported-services) 섹션에서 참조하는 문서를 참조하세요.

Azure 가상 컴퓨터에서 SQL 서버를 실행하는 방법에 대한 자세한 내용은 [Azure 가상 컴퓨터의 SQL 서버란 무엇입니까?](virtual-machines-windows-sql-server-iaas-overview.md)
