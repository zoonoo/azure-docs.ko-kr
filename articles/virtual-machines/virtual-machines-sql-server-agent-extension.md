<properties 
	pageTitle="Azure 가상 컴퓨터의 SQL Server" 
	description="SQL Server 에이전트 확장 및 아직 자동으로 설치되지 않은 에이전트를 설치하는 방법을 설명합니다. 에이전트 확장을 통해 Azure의 클라우드에서 SQL Server를 실행하는 가상 컴퓨터에서 자동화 기능을 사용할 수 있습니다." 
	services="virtual-machines" 
	documentationCenter="" 
	authors="jeffgoll" 
	manager="jeffreyg"
	editor=""/>

<tags
	ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.workload="infrastructure-services" 
	ms.date="06/17/2015"
	ms.author="jeffreyg"/>

# SQL Server IaaS 에이전트 확장

이 확장을 통해 Azure 가상 컴퓨터의 SQL Server에서 이 문서에 나열된 특정 서비스를 사용할 수 있습니다. 해당 서비스는 설치된 확장과 함께 사용해야 합니다. 이 확장은 Azure Preview 포털에서 SQL Server 갤러리 이미지에 대해 자동으로 설치됩니다. 해당 확장은 Azure VM 게스트 에이전트가 설치된 모든 SQL Server VM에 설치할 수 있습니다.
 
## 필수 조건
Powershell cmdlet 사용의 요구 사항:

- 최신 Azure 명령줄 SDK는 [여기에서 사용할 수 있습니다.](http://azure.microsoft.com/downloads/)

VM에서 확장을 사용하기 위한 요구 사항:

- Azure VM 게스트 에이전트
- Windows Server 2012, Windows Server 2012 R2 이상
- SQL Server 2012, SQL Server 2014 이상
 
## 확장과 함께 사용할 수 있는 서비스

- **SQL 자동화된 백업**: 이 서비스는 VM에 있는 SQL Server의 기본 인스턴스에 대한 모든 데이터베이스 백업 예약을 자동화합니다. 이 서비스에 대한 자세한 내용은 [Azure 가상 컴퓨터에서 SQL Server에 대한 자동화된 백업](https://msdn.microsoft.com/library/azure/dn906091.aspx)을 참조하세요.
- **SQL 자동화된 패치**:이 서비스를 통해 작업량이 가장 많은 시간에 업데이트하지 않도록 VM에 대한 업데이트가 수행될 수 있는 유지 관리 기간을 구성할 수 있습니다. 이 서비스에 대한 자세한 내용은 [Azure 가상 컴퓨터에서 SQL Server에 대한 자동화된 패치](https://msdn.microsoft.com/library/azure/dn961166.aspx)(영문)를 참조하세요.

## Powershell 사용하여 확장 추가
[Azure Preview 포털](https://portal.azure.com/)을 사용하여 SQL Server VM을 프로비전하는 경우 확장이 자동으로 설치됩니다. [Azure 관리 포털](https://manage.windowsazure.com)과 함께 프로비전된 SQL Server VM의 경우 또는 고유한 SQL 라이선스를 가져오는 VM의 경우, 다음 Azure PowerShell cmdlet을 사용하여 기존 VM에 이 확장을 추가할 수 있습니다.

**Set-AzureVMSqlServerExtension**

### 구문

Set-AzureVMSqlServerExtension [-VM] <IPersistentVM> [[-Version] <string>] [-AutoBackupSettings <AutoBackupSettings>] [-AutoPatchingSetttings <AutoPatchingSetttings>] [-Confirm] [-WhatIf] [<CommonParameters>]

> [AZURE.NOTE]–Version 매개 변수는 생략하는 것이 좋습니다. 이 매개 변수가 없으면 기본값은 확장의 최신 버전입니다.

### 예
	Get-AzureVM –ServiceName serviceName –Name vmName | Set-AzureVMSqlServerExtension –AutoBackupSettings $abs | Update-AzureVM**

## 확장의 상태를 확인합니다.
이 확장 및 해당 확장과 연결된 서비스의 상태를 확인하려는 경우 두 포털 중 어떤 포털이든 사용할 수 있습니다. 기존 VM의 세부 정보에서 **설정** 아래에 **확장**을 찾습니다.

다음 Azure PowerShell cmdlet을 사용할 수도 있습니다.

**Get-AzureVMSqlServerExtension**

### 구문

Get-AzureVMSqlServerExtension [[-VM] <IPersistentVM>] [[-Version] <string>] [<CommonParameters>]

> [AZURE.NOTE]–Version 매개 변수는 생략할 수 있습니다. 이 매개 변수가 없으면 기본값은 확장의 최신 버전입니다.

### 예
	Get-AzureVM –ServiceName "service" –Name "vmname" | Get-AzureVMSqlServerExtension

## Powershell을 사용하여 확장 제거   
VM에서 이 확장을 제거하려는 경우 다음 Azure Powershell cmdlet을 사용할 수 있습니다.

**Remove-AzureVMSqlServerExtension**

### 구문
Remove-AzureVMSqlServerExtension -VM <IPersistentVM> [<CommonParameters>]

<!---HONumber=62-->