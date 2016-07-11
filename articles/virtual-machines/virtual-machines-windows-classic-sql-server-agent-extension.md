<properties
	pageTitle="SQL Server VM에 대한 SQL Server 에이전트 확장(클래식) | Microsoft Azure"
	description="이 항목에서는 특정 SQL Server 관리 작업을 자동화하는 SQL Server 에이전트 확장 관리 방법에 대해 설명합니다. 여기에는 자동화된 백업, 자동화된 패치 및 Azure 주요 자격 증명 모음 통합이 포함됩니다. 이 항목에서는 클래식 배포 모드를 사용합니다."
	services="virtual-machines-windows"
	documentationCenter=""
	authors="rothja"
	manager="jhubbard"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.workload="infrastructure-services"
	ms.date="05/16/2016"
	ms.author="jroth"/>

# SQL Server VM에 대한 SQL Server 에이전트 확장(클래식)

> [AZURE.SELECTOR]
- [리소스 관리자](virtual-machines-windows-sql-server-agent-extension.md)
- [클래식](virtual-machines-windows-classic-sql-server-agent-extension.md)

관리 작업을 자동화하기 위해 Azure 가상 컴퓨터에서 SQL Server IaaS 에이전트 확장(SQLIaaSAgent)을 실행합니다. 이 항목에서는 설치, 상태 및 제거에 대한 지침뿐만 아니라 확장에 의해 지원되는 서비스의 개요를 제공합니다.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)] 
이 문서의 리소스 관리자 버전을 보려면 [SQL Server VM 리소스 관리자에 대한 SQL Server 에이전트 확장](virtual-machines-windows-sql-server-agent-extension.md)을 참조하세요.

## 지원되는 서비스

SQL Server IaaS 에이전트 확장은 다음 관리 작업을 지원합니다.

| 관리 기능 | 설명 |
|---------------------|-------------------------------|
| **SQL 자동화된 백업** | VM에 있는 SQL Server의 기본 인스턴스에 대한 모든 데이터베이스 백업 예약을 자동화합니다. 자세한 내용은 [Azure 가상 컴퓨터에서 SQL Server에 대한 자동화된 백업(클래식)](virtual-machines-windows-classic-sql-automated-backup.md)을 참조하세요.|
| **SQL 자동화된 패치** | 워크로드가 가장 많은 시간에 업데이트하지 않도록 VM에 대한 업데이트가 수행될 수 있는 유지 관리 기간을 구성할 수 있습니다. 자세한 내용은 [Azure 가상 컴퓨터에서 SQL Server에 대한 자동화된 패치(클래식)](virtual-machines-windows-classic-sql-automated-patching.md)를 참조하세요.|
| **Azure 주요 자격 증명 모음 통합** | SQL Server VM에서 Azure 주요 자격 증명 모음을 자동으로 설치하고 구성할 수 있습니다. 자세한 내용은 [Azure VM에서 SQL Server에 대한 Azure 주요 자격 증명 모음 통합 구성(클래식)](virtual-machines-windows-classic-ps-sql-keyvault.md)을 참조하세요.|

## 필수 조건

VM에서 SQL Server IaaS 에이전트 확장을 사용하기 위한 요구 사항:

**운영 체제**:

- Windows Server 2012
- Windows Server 2012 R2

**SQL Server 버전**:

- SQL Server 2012
- SQL Server 2014
- SQL Server 2016

**Azure PowerShell**:

- [최신 Azure PowerShell 명령 다운로드 및 구성](../powershell-install-configure.md)

**가상 컴퓨터 게스트 에이전트**:

- BGInfo 확장은 새 Azure VM에 자동으로 설치됩니다.

## 설치

SQL Server IaaS 에이전트 확장은 SQL Server 가상 컴퓨터 갤러리 이미지 중 하나를 프로비전할 때 자동으로 설치됩니다.

OS 전용 Windows Server 가상 컴퓨터를 만드는 경우 **Set-AzureVMSqlServerExtension** PowerShell cmdlet을 사용하여 확장을 수동으로 설치할 수 있습니다. 이 명령을 사용하여 자동화된 패치와 같은 에이전트 서비스 중 하나를 구성합니다. 에이전트가 설치되지 않은 경우 VM이 설치합니다. **Set-AzureVMSqlServerExtension** PowerShell 사용에 대한 지침은 이 문서의 [지원되는 서비스](#supported-services) 섹션에 있는 개별 항목을 참조하세요.

SQL IaaS 에이전트 확장의 최신 버전으로 업데이트하는 경우 확장을 업데이트한 후 가상 컴퓨터를 다시 시작해야 합니다.

>[AZURE.NOTE] VM에서 SQL Server IaaS 에이전트 확장을 수동으로 설치하는 경우 PowerShell 명령을 사용하여 해당 기능을 사용 및 관리해야 합니다. 이 시나리오에서 포털 인터페이스는 사용할 수 없습니다.

## 상태

확장이 설치되어 있는지 확인하는 한 가지 방법은 Azure 포털에서 에이전트 상태를 확인하는 것입니다. 가상 컴퓨터 블레이드에서 **모든 설정**을 선택하고 **확장**을 클릭합니다. 목록에 **SQLIaaSAgent** 확장이 표시되어야 합니다.

![Azure 포털에서 SQL Server IaaS 에이전트 확장](./media/virtual-machines-windows-classic-sql-server-agent-extension/azure-sql-server-iaas-agent-portal.png)

**Get-AzureVMSqlServerExtension** Azure Powershell cmdlet을 사용할 수도 있습니다.

	Get-AzureVM –ServiceName "service" –Name "vmname" | Get-AzureVMSqlServerExtension

## 제거   

Azure 포털에서 가상 컴퓨터 속성의 **확장** 블레이드에서 줄임표를 클릭하여 확장을 제거할 수 있습니다. 그런 다음 **삭제**를 클릭합니다.

![Azure 포털에서 SQL Server IaaS 에이전트 확장 제거](./media/virtual-machines-windows-classic-sql-server-agent-extension/azure-sql-server-iaas-agent-uninstall.png)

**Remove-AzureVMSqlServerExtension** Powershell cmdlet을 사용할 수도 있습니다.

	Get-AzureVM –ServiceName "service" –Name "vmname" | Remove-AzureVMSqlServerExtension | Update-AzureVM

## 다음 단계

확장에 의해 지원되는 서비스 중 하나를 사용하기 시작합니다. 자세한 내용은 이 문서의 [지원되는 서비스](#supported-services) 섹션에 참조된 항목을 참조하세요.

Azure 가상 컴퓨터의 SQL Server 실행에 대한 자세한 내용은 [Azure 가상 컴퓨터의 SQL Server 개요](virtual-machines-windows-sql-server-iaas-overview.md)를 참조하세요.

<!---HONumber=AcomDC_0629_2016-->