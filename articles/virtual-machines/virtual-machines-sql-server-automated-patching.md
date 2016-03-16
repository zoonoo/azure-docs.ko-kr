<properties
	pageTitle="VM의 자동화된 SQL Server 패치 | Microsoft Azure"
	description="Azure에서 실행 중인 SQL Server 가상 컴퓨터에 대한 자동화된 패치 기능에 대해 설명합니다."
	services="virtual-machines"
	documentationCenter="na"
	authors="rothja"
	manager="jeffreyg"
	editor="monicar"
	tags="azure-resource-manager" />
<tags
	ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.workload="infrastructure-services"
	ms.date="02/03/2016"
	ms.author="jroth" />

# Azure 가상 컴퓨터에서 SQL Server의 자동화된 패치

자동화된 패치는 SQL Server 2012 또는 2014를 실행하는 Azure 가상 컴퓨터에 대한 유지 관리 기간을 설정합니다. 이 유지 관리 기간 동안만 자동화된 업데이트를 설치할 수 있습니다. SQL Server의 경우 이를 통해 시스템 업데이트 및 관련 재시작 작업이 데이터베이스에 대해 가장 적절한 시간에 수행되도록 할 수 있습니다. 이 작업에는 SQL Server IaaS 에이전트가 필요합니다.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]리소스 관리자 모델.

## Azure 포털에서 자동화된 패치 구성

[Azure 포털](http://go.microsoft.com/fwlink/?LinkID=525040&clcid=0x409)을 사용하여 새 SQL Server 가상 컴퓨터를 만들 때 자동 패치를 구성할 수 있습니다.

>[AZURE.NOTE] 자동화된 패치는 SQL Server IaaS 에이전트에 의존합니다. 에이전트를 설치하고 구성하려면 대상 가상 컴퓨터에서 실행 중인 Azure VM 에이전트가 있어야 합니다. 최신 가상 컴퓨터 갤러리 이미지는 기본적으로 이 옵션을 사용하도록 설정되어 있지만 Azure VM 에이전트가 기존 VM에서 누락될 수 있습니다. 사용자 고유의 VM 이미지를 사용하는 경우에는 SQL Server IaaS 에이전트를 설치해야 합니다. 자세한 내용은 [VM 에이전트 및 확장](https://azure.microsoft.com/blog/2014/04/15/vm-agent-and-extensions-part-2/)을 참조하세요.

다음 Azure 포털 스크린샷은 **옵션 구성** l **SQL 자동 패치**의 옵션입니다.

![Azure 포털에서 SQL 자동 패치](./media/virtual-machines-sql-server-automated-patching/IC778484.jpg)

기존 SQL Server 2012 또는 2014 가상 컴퓨터의 경우 가상 컴퓨터 속성의 **구성** 섹션에서 **자동 패치** 설정을 선택합니다. **자동 패치** 창에서 기능을 사용하도록 설정하고, 유지 관리 일정 및 시작 시간을 설정하고, 유지 관리 기간을 선택할 수 있습니다. 다음 스크린샷에 이 내용이 나와 있습니다.

![Azure 포털에서 자동화된 패치 구성](./media/virtual-machines-sql-server-automated-patching/IC792132.jpg)

>[AZURE.NOTE] 처음으로 자동화된 패치를 사용 설정하면 Azure에서 백그라운드로 SQL Server IaaS 에이전트를 구성합니다. 이 시간 동안에는 구성된 자동 패치가 Azure 포털에 표시되지 않습니다. 에이전트가 설치 및 구성될 때까지 몇 분 정도 기다리세요. 그 후 Azure 포털에는 새 설정이 반영됩니다.

## PowerShell을 사용하여 자동화된 패치 구성

PowerShell을 사용하여 자동화된 패치를 구성할 수도 있습니다.

다음 예제에서는 PowerShell을 사용하여 기존 SQL Server VM에 대해 자동화된 패치를 구성합니다. **New-AzureVMSqlServerAutoPatchingConfig** 명령은 자동 업데이트에 대한 새 유지 관리 기간을 구성합니다.

    $aps = New-AzureVMSqlServerAutoPatchingConfig -Enable -DayOfWeek "Thursday" -MaintenanceWindowStartingHour 11 -MaintenanceWindowDuration 120  -PatchCategory "Important"

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -AutoPatchingSettings $aps | Update-AzureVM

이 예제를 바탕으로 다음 표에서는 대상 Azure VM에 미치는 실질적인 영향을 설명합니다.

|매개 변수|결과|
|---|---|
|**DayOfWeek**|매주 목요일마다 패치가 설치됩니다.|
|**MaintenanceWindowStartingHour**|오전 11시에 업데이트를 시작합니다.|
|**MaintenanceWindowsDuration**|120분 이내에 패치를 설치해야 합니다. 시작 시간을 기준으로 오후 1시까지 완료해야 합니다.|
|**PatchCategory**|이 매개 변수에 대해서는 "중요" 설정만 가능합니다.|

SQL Server IaaS 에이전트를 설치하고 구성하는 데는 몇 분 정도 걸릴 수 있습니다.

자동 패치를 사용하지 않으려면 동일한 스크립트를 New-AzureVMSqlServerAutoPatchingConfig에 대해 -Enable 매개 변수 없이 실행합니다. 설치와 마찬가지로 자동화된 패치를 사용하지 않도록 설정하는 데도 몇 분 정도 걸릴 수 있습니다.

## SQL Server IaaS 에이전트 비활성화 및 제거

자동화된 백업 및 패치에 SQL Server IaaS 에이전트를 사용하지 않으려면 다음 명령을 사용합니다.

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -Disable | Update-AzureVM

SQL Server IaaS 에이전트를 제거하려면 다음 구문을 사용합니다.

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension –Uninstall | Update-AzureVM

**Remove-AzureVMSqlServerExtension** 명령을 사용하여 확장을 제거할 수도 있습니다.

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Remove-AzureVMSqlServerExtension | Update-AzureVM

## 호환성

다음 제품은 자동화된 패치에 대해 SQL Server IaaS 에이전트 기능과 호환됩니다.

- Windows Server 2012

- Windows Server 2012 R2

- SQL Server 2012

- SQL Server 2014

## 다음 단계

Azure에서 SQL Server VM 관련 기능은 [Azure 가상 컴퓨터의 SQL Server에 대한 자동화된 백업](virtual-machines-sql-server-automated-backup.md)입니다.

그 밖에 [Azure 가상 컴퓨터에서 SQL Server 실행과 관련된 리소스](virtual-machines-sql-server-infrastructure-services.md)를 검토하세요.

<!---HONumber=AcomDC_0204_2016-->