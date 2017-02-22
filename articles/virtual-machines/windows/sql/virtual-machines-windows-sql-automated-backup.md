---
title: "SQL Server 2014 Azure Virtual Machines의 자동화된 백업 | Microsoft Docs"
description: "Azure에서 실행되는 SQL Server 2014 VMs의 자동화된 백업 기능에 대해 설명합니다. 이 문서는 Resource Manager를 사용하는 VMs에만 적용됩니다."
services: virtual-machines-windows
documentationcenter: na
author: rothja
manager: jhubbard
editor: 
tags: azure-resource-manager
ms.assetid: bdc63fd1-db49-4e76-87d5-b5c6a890e53c
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/30/2017
ms.author: jroth
translationtype: Human Translation
ms.sourcegitcommit: 253c504fa433c7ca37c0065ebf01d13dafc76231
ms.openlocfilehash: c4cf6ab29ebf5b3397017cf754ee04bf57ab1555


---
# <a name="automated-backup-for-sql-server-2014-virtual-machines-resource-manager"></a>SQL Server 2014 Virtual Machines의 자동화된 백업(Resource Manager)
> [!div class="op_single_selector"]
> * [리소스 관리자](virtual-machines-windows-sql-automated-backup.md)
> * [클래식](../sqlclassic/virtual-machines-windows-classic-sql-automated-backup.md)
> 
> 

자동화된 백업에서는 SQL Server 2014 Standard 또는 Enterprise를 실행하는 Azure VM의 모든 기존 및 새 데이터베이스에 대해 [Microsoft Azure에 대한 관리되는 백업](https://msdn.microsoft.com/library/dn449496.aspx) 을 자동으로 구성합니다. 이를 통해 지속형 Azure Blob 저장소를 활용하는 일반 데이터베이스 백업을 구성할 수 있습니다. 자동화된 백업은 [SQL Server IaaS 에이전트 확장](virtual-machines-windows-sql-server-agent-extension.md)에 따라 다릅니다.

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

이 문서의 클래식 버전을 보려면 [Azure Virtual Machines에서 SQL Server의 자동화된 백업(클래식)](../sqlclassic/virtual-machines-windows-classic-sql-automated-backup.md)을 참조하세요.

## <a name="prerequisites"></a>필수 조건
자동화된 백업을 사용하려면 다음 필수 조건을 고려하세요.

**운영 체제**:

- Windows Server 2012
- Windows Server 2012 R2

**SQL Server 버전**:

- SQL Server 2014 Standard
- SQL Server 2014 Enterprise

> [!IMPORTANT]
> 자동화된 백업은 SQL Server 2014에서 작동합니다. SQL Server 2016을 사용하는 경우 자동화된 백업 v2를 사용하여 데이터베이스를 백업할 수 있습니다. 자세한 내용은 [SQL Server 2016 Virtual Machines의 자동화된 백업 v2](virtual-machines-windows-sql-automated-backup-v2.md)를 참조하세요.

**데이터베이스 구성**:

- 대상 데이터베이스는 전체 복구 모델을 사용해야 합니다.

전체 복구 모델이 백업에 미치는 영향에 대한 자세한 내용은 [전체 복구 모델에서 백업](https://technet.microsoft.com/library/ms190217.aspx)을 참조하세요.

**Azure 배포 모델**:

- 리소스 관리자

**Azure PowerShell**:

- [최신 Azure PowerShell 명령을 설치합니다](/powershell/azureps-cmdlets-docs) .

> [!NOTE]
> 자동화된 백업은 SQL Server IaaS 에이전트 확장에 의존합니다. 현재 SQL 가상 컴퓨터 갤러리 이미지는 기본적으로 이 확장을 추가합니다. 자세한 내용은 [SQL Server IaaS 에이전트 확장](virtual-machines-windows-sql-server-agent-extension.md)을 참조하세요.

## <a name="settings"></a>설정
다음 표에서는 자동화된 백업에 대해 구성할 수 있는 옵션을 설명합니다. 실제 구성 단계는 Azure 포털 또는 Azure Windows PowerShell 명령 사용 여부에 따라 달라집니다.

| 설정 | 범위(기본값) | 설명 |
| --- | --- | --- |
| **자동화된 백업** | 사용/사용 안 함(사용 안 함) | SQL Server 2014 Standard 또는 Enterprise를 실행하는 Azure VM에 대해 자동화된 백업을 사용하거나 사용하지 않도록 설정합니다. |
| **보존 기간** | 1-30일(30일) | 백업 보존 기간(일 수)입니다. |
| **저장소 계정** | Azure 저장소 계정 | Blob 저장소에 자동화된 백업 파일을 저장하기 위해 사용하여 Azure 저장소 계정입니다. 모든 백업 파일을 저장하려면 컨테이너를 이 위치에 만듭니다. 백업 파일 명명 규칙에는 날짜, 시간 및 컴퓨터 이름이 포함됩니다. |
| **암호화** | 사용/사용 안 함(사용 안 함) | 암호화 사용 여부를 설정합니다. 암호화를 사용하면 백업을 복원하는 데 사용되는 인증서가 동일한 명명 규칙을 사용하여 동일한 `automaticbackup` 컨테이너에 지정한 저장소 계정에 배치됩니다. 암호가 변경되면 해당 암호를 사용하여 새 인증서가 생성되지만 이전 인증서도 이전 백업의 복원을 위해 유지됩니다. |
| **암호** | 암호 텍스트 | 암호화 키의 암호입니다. 암호화를 사용하는 경우에만 필요합니다. 암호화된 백업을 복원하기 위해서는 올바른 암호 및 백업을 수행할 때 사용한 인증서가 있어야 합니다. |

## <a name="configuration-in-the-portal"></a>포털에서 구성
Azure Portal을 사용하여 프로비전 중에 또는 기존 SQL Server 2014 VMs에 대해 자동화된 백업을 구성할 수 있습니다.

### <a name="new-vms"></a>새 VM
Azure Portal을 사용하여 Resource Manager 배포 모델에서 새 SQL Server 2014 Virtual Machine을 만들 때 자동화된 백업을 구성합니다.

**SQL Server 설정** 블레이드에서 **자동화된 백업**을 선택합니다. 다음 Azure 포털 스크린샷은 **SQL 자동화된 백업** 블레이드를 보여 줍니다.

![Azure 포털에서 SQL 자동화된 백업 구성](./media/virtual-machines-windows-sql-automated-backup/azure-sql-arm-autobackup.png)

컨텍스트의 경우 [Azure에서 SQL Server 가상 컴퓨터 프로비전](virtual-machines-windows-portal-sql-server-provision.md)의 전체 항목을 참조하세요.

### <a name="existing-vms"></a>기존 VM
기존 SQL Server 가상 컴퓨터에 대한 해당 SQL Server 가상 컴퓨터를 선택합니다. 그런 다음 **설정** 블레이드의 **SQL Server 구성** 섹션을 선택합니다.

![기존 VM에 대한 SQL 자동화된 백업](./media/virtual-machines-windows-sql-automated-backup/azure-sql-rm-autobackup-existing-vms.png)

**SQL Server 구성** 블레이드에서 자동화된 백업 섹션의 **편집** 단추를 클릭합니다.

![기존 VM에 대한 SQL 자동화된 백업 구성](./media/virtual-machines-windows-sql-automated-backup/azure-sql-rm-autobackup-configuration.png)

완료되면 **SQL Server 구성** 블레이드 아래쪽의 **확인** 단추를 클릭하여 변경 내용을 저장합니다.

처음으로 자동화된 백업을 사용 설정할 경우 Azure에서 백그라운드로 SQL Server IaaS 에이전트를 구성합니다. 이 시간 동안에는 구성된 자동화된 백업이 Azure 포털에 표시되지 않을 수 있습니다. 에이전트가 설치 및 구성될 때까지 몇 분 정도 기다리세요. 그 후 Azure 포털에는 새 설정이 반영됩니다.

> [!NOTE]
> 또한 템플릿을 사용하여 자동화된 백업을 구성할 수 있습니다. 자세한 내용은 [자동화된 백업에 대한 Azure 빠른 시작 템플릿](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-sql-existing-autobackup-update)을 참조하세요.
> 
> 

## <a name="configuration-with-powershell"></a>PowerShell을 사용하여 구성
SQL VM을 프로비전한 후 자동화된 백업을 구성하려면 PowerShell을 사용합니다. 시작하기 전에 다음을 수행해야 합니다.

- [최신 Azure PowerShell을 다운로드하여 설치합니다](http://aka.ms/webpi-azps).
- Windows PowerShell을 열고 계정에 연결합니다. 이렇게 하려면 프로비전 관련 문서의 [구독 구성](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-ps-sql-create#configure-your-subscription) 섹션에서 설명하는 단계를 수행하면 됩니다.

다음 PowerShell 예제에서는 기존 SQL Server 2014 VM에 대해 자동화된 백업이 구성됩니다. **AzureRM.Compute\New-AzureVMSqlServerAutoBackupConfig** 명령은 가상 컴퓨터와 연결된 Azure 저장소 계정에 백업을 저장하도록 자동화된 백업 설정을 구성합니다. 이러한 백업은 10일 동안 보존됩니다. **Set-AzureRmVMSqlServerExtension** 명령은 지정된 Azure VM을 이러한 설정으로 업데이트합니다.

    $vmname = "vmname"
    $resourcegroupname = "resourcegroupname"
    $autobackupconfig = AzureRM.Compute\New-AzureVMSqlServerAutoBackupConfig -Enable -RetentionPeriodInDays 10 -ResourceGroupName $resourcegroupname

    Set-AzureRmVMSqlServerExtension -AutoBackupSettings $autobackupconfig -VMName $vmname -ResourceGroupName $resourcegroupname

SQL Server IaaS 에이전트를 설치하고 구성하는 데는 몇 분 정도 걸릴 수 있습니다.

암호화를 사용하려면 **CertificatePassword** 매개 변수에 대 한 암호(보안 문자열)와 함께 **EnableEncryption** 매개 변수를 전달 하도록 이전 스크립트를 수정합니다. 다음 스크립트를 사용하면 이전 예제의 자동화된 백업 설정을 사용하고 암호화를 추가할 수 있습니다.

    $vmname = "vmname"
    $resourcegroupname = "resourcegroupname"
    $password = "P@ssw0rd"
    $encryptionpassword = $password | ConvertTo-SecureString -AsPlainText -Force  
    $autobackupconfig = AzureRM.Compute\New-AzureVMSqlServerAutoBackupConfig -Enable -RetentionPeriod 10 -EnableEncryption -CertificatePassword $encryptionpassword -ResourceGroupName $resourcegroupname

    Set-AzureRmVMSqlServerExtension -AutoBackupSettings $autobackupconfig -VMName $vmname -ResourceGroupName $resourcegroupname

자동 백업을 사용하지 않으려면 동일한 스크립트를 **AzureRM.Compute\New-AzureVMSqlServerAutoBackupConfig** 명령에 대해 **-Enable** 매개 변수 없이 실행합니다. **-Enable** 매개 변수가 없는 경우 기능을 해제하는 명령을 신호로 보냅니다. 설치와 마찬가지로 자동화된 백업을 사용하지 않도록 설정하는 데도 몇 분 정도 걸릴 수 있습니다.

> [!NOTE]
> SQL Server IaaS 에이전트를 제거해도 이전에 구성한 자동화된 백업 설정은 제거되지 않습니다. SQL Server IaaS 에이전트를 비활성화 또는 제거하기 전에 자동화된 백업을 사용하지 않도록 설정해야 합니다.
> 
> 

## <a name="next-steps"></a>다음 단계
자동화된 백업은 Azure VM에서 관리되는 백업을 구성합니다. 따라서 [관리되는 백업 설명서를 검토](https://msdn.microsoft.com/library/dn449496.aspx) 하여 동작 및 의미를 이해해야 합니다.

Azure VM의 SQL Server에 대한 추가적인 백업 및 복원 지침은 [Azure 가상 컴퓨터의 SQL Server 백업 및 복원](virtual-machines-windows-sql-backup-recovery.md)항목을 참조하세요.

사용 가능한 다른 자동화 작업에 대한 내용은 [SQL Server IaaS 에이전트 확장](virtual-machines-windows-sql-server-agent-extension.md)을 참조하세요.

Azure VM의 SQL Server 실행에 대한 자세한 내용은 [Azure 가상 컴퓨터의 SQL Server 개요](virtual-machines-windows-sql-server-iaas-overview.md)를 참조하세요.




<!--HONumber=Jan17_HO5-->


