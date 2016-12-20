---
title: "SQL Server VM의 자동화된 패치(클래식) | Microsoft Docs"
description: "클래식 배포 모드를 사용하여 Azure에서 실행 중인 SQL Server 가상 컴퓨터에 대한 자동화된 패치 기능에 대해 설명합니다."
services: virtual-machines-windows
documentationcenter: na
author: rothja
manager: jhubbard
editor: 
tags: azure-service-management
ms.assetid: 737b2f65-08b9-4f54-b867-e987730265a8
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: infrastructure-services
ms.date: 09/26/2016
ms.author: jroth
translationtype: Human Translation
ms.sourcegitcommit: ee34a7ebd48879448e126c1c9c46c751e477c406
ms.openlocfilehash: b10b1a5e50729963a758b2a5ba0c8856df0df848


---
# <a name="automated-patching-for-sql-server-in-azure-virtual-machines-classic"></a>Azure 가상 컴퓨터에서 SQL Server의 자동화된 패치(클래식)
> [!div class="op_single_selector"]
> * [리소스 관리자](virtual-machines-windows-sql-automated-patching.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
> * [클래식](virtual-machines-windows-classic-sql-automated-patching.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)
> 
> 

자동화된 패치는 SQL Server를 실행하는 Azure 가상 컴퓨터에 대한 유지 관리 기간을 설정합니다. 이 유지 관리 기간 동안만 자동화된 업데이트를 설치할 수 있습니다. SQL Server의 경우 이를 통해 시스템 업데이트 및 관련 재시작 작업이 데이터베이스에 대해 가장 적절한 시간에 수행되도록 할 수 있습니다. 자동화된 패치는 [SQL Server IaaS 에이전트 확장](virtual-machines-windows-classic-sql-server-agent-extension.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)에 따라 다릅니다.

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

이 문서의 Resource Manager 버전을 보려면 [Azure 가상 컴퓨터 Resource Manager에서 SQL Server의 자동화된 패치](virtual-machines-windows-sql-automated-patching.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)를 참조하세요.

## <a name="prerequisites"></a>필수 조건
자동화된 패치를 사용하려면 다음 필수 조건을 고려하세요.

**운영 체제**:

* Windows Server 2012
* Windows Server 2012 R2

**SQL Server 버전**:

* SQL Server 2012
* SQL Server 2014
* SQL Server 2016

**Azure PowerShell**:

* [최신 Azure PowerShell cmdlet을 설치합니다](../powershell-install-configure.md).

**SQL Server IaaS 확장**:

* [SQL Server IaaS 확장을 설치합니다](virtual-machines-windows-classic-sql-server-agent-extension.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

## <a name="settings"></a>설정
다음 표에서는 자동화된 패치에 대해 구성할 수 있는 옵션을 설명합니다. 클래식 VM의 경우 이러한 설정을 구성하려면 PowerShell을 사용해야 합니다.

| 설정 | 가능한 값 | 설명 |
| --- | --- | --- |
| **자동화된 패치** |사용/사용 안 함(사용 안 함) |Azure 가상 컴퓨터에 대한 자동화된 패치를 사용 또는 사용 안 함으로 설정합니다. |
| **유지 관리 일정** |매일, 월요일, 화요일, 수요일, 목요일, 금요일, 토요일, 일요일 |가상 컴퓨터에 대한 Windows, SQL Server 및 Microsoft 업데이트 다운로드 및 설치에 대한 일정입니다. |
| **유지 관리 시작 시간** |0-24 |가상 컴퓨터를 업데이트할 로컬 시작 시간입니다. |
| **유지 관리 기간** |30-180 |업데이트 다운로드 및 설치를 완료하는데 허용된 시간(분)입니다. |
| **패치 범주** |중요 |다운로드 및 설치할 업데이트의 범주입니다. |

## <a name="configuration-with-powershell"></a>PowerShell을 사용하여 구성
다음 예제에서는 PowerShell을 사용하여 기존 SQL Server VM에 대해 자동화된 패치를 구성합니다. **New-AzureVMSqlServerAutoPatchingConfig** 명령은 자동 업데이트에 대한 새 유지 관리 기간을 구성합니다.

    $aps = New-AzureVMSqlServerAutoPatchingConfig -Enable -DayOfWeek "Thursday" -MaintenanceWindowStartingHour 11 -MaintenanceWindowDuration 120  -PatchCategory "Important"

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -AutoPatchingSettings $aps | Update-AzureVM

이 예제를 바탕으로 다음 표에서는 대상 Azure VM에 미치는 실질적인 영향을 설명합니다.

| 매개 변수 | 결과 |
| --- | --- |
| **DayOfWeek** |매주 목요일마다 패치가 설치됩니다. |
| **MaintenanceWindowStartingHour** |오전 11시에 업데이트를 시작합니다. |
| **MaintenanceWindowsDuration** |120분 이내에 패치를 설치해야 합니다. 시작 시간을 기준으로 오후 1시까지 완료해야 합니다. |
| **PatchCategory** |이 매개 변수에 대해서는 "중요" 설정만 가능합니다. |

SQL Server IaaS 에이전트를 설치하고 구성하는 데는 몇 분 정도 걸릴 수 있습니다.

자동 패치를 사용하지 않으려면 동일한 스크립트를 New-AzureVMSqlServerAutoPatchingConfig에 대해 -Enable 매개 변수 없이 실행합니다. 설치와 마찬가지로 자동화된 패치를 사용하지 않도록 설정하는 데도 몇 분 정도 걸릴 수 있습니다.

## <a name="next-steps"></a>다음 단계
사용 가능한 다른 자동화 작업에 대한 내용은 [SQL Server IaaS 에이전트 확장](virtual-machines-windows-classic-sql-server-agent-extension.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)을 참조하세요.

Azure VM의 SQL Server 실행에 대한 자세한 내용은 [Azure 가상 컴퓨터의 SQL Server 개요](virtual-machines-windows-sql-server-iaas-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)를 참조하세요.




<!--HONumber=Nov16_HO3-->


