<properties 
   pageTitle="Azure 가상 컴퓨터에서 SQL Server의 자동화된 백업"
   description="Azure 가상 컴퓨터에서 실행 중인 SQL Server에 대한 자동화된 백업 기능에 대해 설명합니다."
   services="virtual-machines"
   documentationCenter="na"
   authors="rothja"
   manager="jeffreyg"
   editor="monicar" />
<tags 
   ms.service="virtual-machines"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows-sql-server"
   ms.workload="infrastructure-services"
   ms.date="08/05/2015"
   ms.author="jroth" />

# Azure 가상 컴퓨터에서 SQL Server의 자동화된 백업

자동화된 백업에서는 SQL Server 2014 Standard 또는 Enterprise를 실행하는 Azure VM의 모든 기존 및 새 데이터베이스에 대해 [Microsoft Azure에 대한 관리되는 백업](https://msdn.microsoft.com/library/dn449496.aspx)을 자동으로 구성합니다. 이를 통해 지속형 Azure Blob 저장소를 활용하는 일반 데이터베이스 백업을 구성할 수 있습니다.

>[AZURE.NOTE]자동화된 백업은 SQL Server IaaS 에이전트에 의존합니다. 에이전트를 설치하고 구성하려면 대상 가상 컴퓨터에서 실행 중인 Azure VM 에이전트가 있어야 합니다. 최신 가상 컴퓨터 갤러리 이미지는 기본적으로 이 옵션을 사용하도록 설정되어 있지만 Azure VM 에이전트가 기존 VM에서 누락될 수 있습니다. 사용자 고유의 VM 이미지를 사용하는 경우에는 SQL Server IaaS 에이전트를 설치해야 합니다. 자세한 내용은 [VM 에이전트 및 확장](http://azure.microsoft.com/blog/2014/04/15/vm-agent-and-extensions-part-2/)을 참조하세요.

## 자동화된 백업 설정

다음 표에서는 자동화된 백업에 대해 구성할 수 있는 옵션을 설명합니다. 실제 구성 단계는 Azure 포털 또는 Azure Windows PowerShell 명령 사용 여부에 따라 달라집니다.

|설정|범위(기본값)|설명|
|---|---|---|
|**자동화된 백업**|사용/사용 안 함(사용 안 함)|SQL Server 2014 Standard 또는 Enterprise를 실행하는 Azure VM에 대해 자동화된 백업을 사용하거나 사용하지 않도록 설정합니다.|
|**보존 기간**|1-30일(30일)|백업 보존 기간(일 수)입니다.|
|**저장소 계정**|Azure 저장소 계정(지정된 VM에 대해 만든 저장소 계정)|Blob 저장소에 자동화된 백업 파일을 저장하기 위해 사용하여 Azure 저장소 계정입니다. 모든 백업 파일을 저장하려면 컨테이너를 이 위치에 만듭니다. 백업 파일 명명 규칙에는 날짜, 시간 및 컴퓨터 이름이 포함됩니다.|
|**암호화**|사용/사용 안 함(사용 안 함)|암호화 사용 여부를 설정합니다. 암호화 기능을 사용하면 백업을 복원하는 데 사용되는 인증서가 동일한 명명 규칙을 사용하여 동일한 자동 백업 컨테이너에 지정한 저장소 계정에 배치됩니다. 암호가 변경되면 해당 암호를 사용하여 새 인증서가 생성되지만 이전 인증서도 이전 백업의 복원을 위해 유지됩니다.|
|**암호**|암호 텍스트(없음)|암호화 키의 암호입니다. 암호화를 사용하는 경우에만 필요합니다. 암호화된 백업을 복원하기 위해서는 올바른 암호 및 백업을 수행할 때 사용한 인증서가 있어야 합니다.|

## 포털에서 자동화된 백업 구성

[Azure 미리 보기 포털](http://go.microsoft.com/fwlink/?LinkID=525040&clcid=0x409)을 사용하면 새 SQL Server 2014 가상 컴퓨터를 만들 때 자동화된 백업을 구성할 수 있습니다. 다음 스크린샷에는 **선택적 구성**|**SQL 자동화된 백업** 아래에 이러한 옵션이 나와 있습니다.

![Azure 포털에서 SQL 자동 백업 구성](./media/virtual-machines-sql-server-automated-backup/IC778483.jpg)

기존 SQL Server 2014 가상 컴퓨터의 경우, 가상 컴퓨터 속성의 **구성** 섹션에서 **자동 백업** 설정을 선택합니다. **자동화된 백업** 창에서 기능을 활성화하고, 보존 기간을 설정하고, 저장소 계정을 선택하고 암호화를 설정할 수 있습니다. 다음 스크린샷에 이 내용이 나와 있습니다.

![Azure 포털에서 자동화된 백업 구성](./media/virtual-machines-sql-server-automated-backup/IC792133.jpg)

>[AZURE.NOTE]처음으로 자동화된 백업을 사용 설정하면 Azure에서 백그라운드로 SQL Server IaaS 에이전트를 구성합니다. 이 시간 동안에는 포털에 자동화된 백업이 구성되었다고 표시되지 않습니다. 에이전트가 설치 및 구성될 때까지 몇 분 정도 기다리세요. 그 후 포털에 새로운 설정이 반영됩니다.

## PowerShell을 사용하여 자동화된 백업 구성

다음 PowerShell 예제에서는 기존 SQL Server 2014 VM에 대해 자동화된 백업이 구성됩니다. **New-AzureVMSqlServerAutoBackupConfig** 명령은 $storageaccount 변수로 지정한 Azure 저장소 계정에 백업을 저장하는 자동화된 백업 설정을 구성합니다. 이러한 백업은 10일 동안 보존됩니다. **Set-AzureVMSqlServerExtension** 명령은 지정된 Azure VM을 이러한 설정으로 업데이트합니다.

    $storageaccount = "<storageaccountname>"
    $storageaccountkey = (Get-AzureStorageKey -StorageAccountName $storageaccount).Primary
    $storagecontext = New-AzureStorageContext -StorageAccountName $storageaccount -StorageAccountKey $storageaccountkey
    $autobackupconfig = New-AzureVMSqlServerAutoBackupConfig -StorageContext $storagecontext -Enable -RetentionPeriod 10
    
    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -AutoBackupSettings $autobackupconfig | Update-AzureVM

SQL Server IaaS 에이전트를 설치하고 구성하는 데는 몇 분 정도 걸릴 수 있습니다.

암호화를 사용하려면 CertificatePassword 매개 변수에 대 한 암호(보안 문자열)와 함께 EnableEncryption 매개 변수를 전달 하도록 이전 스크립트를 수정합니다. 다음 스크립트를 사용하면 이전 예제의 자동화된 백업 설정을 사용하고 암호화를 추가할 수 있습니다.

    $storageaccount = "<storageaccountname>"
    $storageaccountkey = (Get-AzureStorageKey -StorageAccountName $storageaccount).Primary
    $storagecontext = New-AzureStorageContext -StorageAccountName $storageaccount -StorageAccountKey $storageaccountkey
    $password = "P@ssw0rd"
    $encryptionpassword = $password | ConvertTo-SecureString -AsPlainText -Force  
    $autobackupconfig = New-AzureVMSqlServerAutoBackupConfig -StorageContext $storagecontext -Enable -RetentionPeriod 10 -EnableEncryption -CertificatePassword $encryptionpassword
    
    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -AutoBackupSettings $autobackupconfig | Update-AzureVM 

자동 백업을 사용하지 않으려면 동일한 스크립트를 **New-AzureVMSqlServerAutoBackupConfig**에 대해 **-Enable** 매개 변수 없이 실행합니다. 설치와 마찬가지로 자동화된 백업을 사용하지 않도록 설정하는 데도 몇 분 정도 걸릴 수 있습니다.

## SQL Server IaaS 에이전트 비활성화 및 제거

자동화된 백업 및 패치에 SQL Server IaaS 에이전트를 사용하지 않으려면 다음 명령을 사용합니다.

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -Disable | Update-AzureVM

SQL Server IaaS 에이전트를 제거하려면 다음 구문을 사용합니다.

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension –Uninstall | Update-AzureVM

**Remove-AzureVMSqlServerExtension** 명령을 사용하여 확장을 제거할 수도 있습니다.

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Remove-AzureVMSqlServerExtension | Update-AzureVM

>[AZURE.NOTE]SQL Server IaaS 에이전트를 비활성화하고 제거해도 이전에 구성한 관리된 백업 설정은 제거되지 않습니다. SQL Server IaaS 에이전트를 비활성화 또는 제거하기 전에 자동화된 백업을 사용하지 않도록 설정해야 합니다.

## 호환성

다음 제품은 자동화된 백업에 대해 SQL Server IaaS 에이전트 기능과 호환됩니다.

- Windows Server 2012

- Windows Server 2012 R2

- SQL Server 2014 Standard

- SQL Server 2014 Enterprise

## 다음 단계

자동화된 백업은 Azure VM에서 관리되는 백업을 구성합니다. 따라서 [관리되는 백업 설명서를 검토](https://msdn.microsoft.com/library/dn449496.aspx)하여 동작 및 의미를 이해해야 합니다.

Azure VM에서 SQL Server에 대한 추가적인 백업 및 복원 지침은 [Azure 가상 컴퓨터에서 SQL Server의 백업 및 복원](virtual-machines-sql-server-backup-and-restore.md) 항목을 참조하세요.

Azure에서 SQL Server VM 관련 기능은 [Azure 가상 컴퓨터의 SQL Server에 대한 자동화된 패치](virtual-machines-sql-server-automated-patching.md)입니다.

그 밖에 [Azure 가상 컴퓨터에서 SQL Server 실행과 관련된 리소스](virtual-machines-sql-server-infrastructure-services.md)를 검토하세요.

<!---HONumber=August15_HO7-->