<properties
	pageTitle="SQL Server IaaS 에이전트 확장 | Microsoft Azure"
	description="이 항목 클래식 배포 모델을 사용하여 만든 리소스를 사용하며, Azure에서 SQL Server를 실행하는 VM이 자동화 기능을 사용할 수 있게 해주는 SQL Server 에이전트 확장에 대해 설명합니다."
	services="virtual-machines"
	documentationCenter=""
	authors="jeffgoll"
	manager="jeffreyg"
   editor="monicar"    
   tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.workload="infrastructure-services"
	ms.date="10/02/2015"
	ms.author="jeffreyg"/>

# SQL Server IaaS 에이전트 확장

이 확장을 통해 Azure 가상 컴퓨터의 SQL Server에서 이 문서에 나열된 특정 서비스를 사용할 수 있습니다. 해당 서비스는 설치된 확장과 함께 사용해야 합니다. 이 확장은 Azure 포털의 SQL 서버 갤러리 이미지에 대해 자동으로 설치됩니다. 해당 확장은 Azure VM 게스트 에이전트가 설치된 모든 SQL Server VM에 설치할 수 있습니다.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]리소스 관리자 모델.


## 필수 조건
Powershell cmdlet 사용의 요구 사항:

- 최신 Azure 명령줄 SDK는 [여기에서 사용할 수 있습니다.](http://azure.microsoft.com/downloads/)

VM에서 확장을 사용하기 위한 요구 사항:

- Azure VM 게스트 에이전트
- Windows Server 2012, Windows Server 2012 R2 이상
- SQL Server 2012, SQL Server 2014 이상

## 확장과 함께 사용할 수 있는 서비스

- **SQL 자동화된 백업**: 이 서비스는 VM에 있는 SQL Server의 기본 인스턴스에 대한 모든 데이터베이스 백업 예약을 자동화합니다. 이 서비스에 대한 자세한 내용은 [Azure 가상 컴퓨터에서 SQL Server에 대한 자동화된 백업](virtual-machines-sql-server-automated-backup.md)을 참조하세요.
- **SQL 자동화된 패치**:이 서비스를 통해 작업량이 가장 많은 시간에 업데이트하지 않도록 VM에 대한 업데이트가 수행될 수 있는 유지 관리 기간을 구성할 수 있습니다. 이 서비스에 대한 자세한 내용은 [Azure 가상 컴퓨터에서 SQL Server에 대한 자동화된 패치](virtual-machines-sql-server-automated-patching.md)(영문)를 참조하세요.

## Powershell 사용하여 확장 추가
[Azure 포털](https://portal.azure.com/)을 사용하여 SQL 서버 VM을 프로비전하는 경우, 확장이 자동으로 설치됩니다. [Azure 클래식 포털](https://manage.windowsazure.com)을 사용하여 프로비전된 SQL Server VM 또는 고유한 SQL 라이선스를 가져온 VM의 경우, 다음 Azure PowerShell cmdlet을 사용하여 이 확장을 기존 VM에 추가할 수 있습니다.

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

<!---HONumber=AcomDC_1203_2015-->