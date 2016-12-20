---
title: "Azure VM에서 SQL Server에 대한 Azure 키 자격 증명 모음 통합 구성(클래식)"
description: "Azure 키 자격 증명 모음과 함께 사용하도록 SQL Server 암호화 구성을 자동화하는 방법에 대해 알아보세요. 이 항목에서는 Azure 주요 자격 증명 모음 통합을 클래식 배포 모델에서 만든 SQL Server 가상 컴퓨터와 함께 사용하는 방법에 대해 설명합니다."
services: virtual-machines-windows
documentationcenter: 
author: rothja
manager: jhubbard
editor: 
tags: azure-service-management
ms.assetid: ab8d41a7-1971-4032-ab71-eb435c455dc1
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: infrastructure-services
ms.date: 09/26/2016
ms.author: jroth
translationtype: Human Translation
ms.sourcegitcommit: ee34a7ebd48879448e126c1c9c46c751e477c406
ms.openlocfilehash: 53120cbdbee8463f579d72af6fb29ed17b261dde


---
# <a name="configure-azure-key-vault-integration-for-sql-server-on-azure-vms-classic"></a>Azure VM에서 SQL Server에 대한 Azure 키 자격 증명 모음 통합 구성(클래식)
> [!div class="op_single_selector"]
> * [리소스 관리자](virtual-machines-windows-ps-sql-keyvault.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
> * [클래식](virtual-machines-windows-classic-ps-sql-keyvault.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)
> 
> 

## <a name="overview"></a>개요
[TDE(투명한 데이터 암호화)](https://msdn.microsoft.com/library/bb934049.aspx), [CLE(열 수준 암호화)](https://msdn.microsoft.com/library/ms173744.aspx) [백업 암호화](https://msdn.microsoft.com/library/dn449489.aspx) 등 여러 SQL Server 암호화 기능이 있습니다. 이러한 형태의 암호화는 암호화에 사용되는 암호화 키를 관리 및 저장해야 합니다. AKV(Azure 키 자격 증명 모음) 서비스는 안전하고 가용성이 높은 위치에서 이러한 키의 보안 및 관리를 개선하도록 설계되었습니다. [SQL Server 커넥터](http://www.microsoft.com/download/details.aspx?id=45344) 는 SQL Server가 Azure 주요 자격 증명 모음의 주요 항목을 사용할 수 있게 해줍니다.

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

온-프레미스 컴퓨터로 SQL Server를 실행하는 경우 [온-프레미스 SQL Server 컴퓨터에서 Azure 키 자격 증명 모음에 액세스할 수 있는 단계](https://msdn.microsoft.com/library/dn198405.aspx)가 있습니다. 하지만 Azure VM의 SQL Server에서는 *Azure 주요 자격 증명 모음 통합* 기능을 사용하여 시간을 절약할 수 있습니다. 이 기능을 지원하는 Azure PowerShell cmdlet 몇 개만 있으면 SQL VM이 키 자격 증명 모음에 액세스하는 데 필요한 구성을 자동화할 수 있습니다.

이 기능은 활성화되면 자동으로 SQL Server 커넥터를 설치하고, Azure 키 자격 증명 모음에 액세스하도록 EKM 공급자를 구성하고, 사용자가 자격 증명 모음에 액세스할 수 있도록 자격 증명을 만듭니다. 앞에서 언급한 온-프레미스 설명서의 단계를 살펴보셨다면 이 기능이 2 및 3단계를 자동화한다는 것을 알 수 있습니다. 사용자가 수동으로 해야 하는 유일한 일은 키 자격 증명 모음 및 키를 만드는 작업입니다. 그 이후에 SQL VM을 설정하는 전체 과정이 자동화됩니다. 이 기능이 설정을 완료하면 사용자는 T-SQL 문을 실행하여 평소와 같이 데이터베이스 암호화 또는 백업을 시작할 수 있습니다.

[!INCLUDE [AKV Integration Prepare](../../includes/virtual-machines-sql-server-akv-prepare.md)]

## <a name="configure-akv-integration"></a>AKV 통합 구성
PowerShell을 사용하여 Azure 키 자격 증명 모음 통합을 구성합니다. 다음 섹션에서는 필수 매개 변수 및 샘플 PowerShell 스크립트의 개요를 제공합니다.

### <a name="install-the-sql-server-iaas-extension"></a>SQL Server IaaS 확장 설치
먼저 [SQL Server IaaS 확장을 설치](virtual-machines-windows-classic-sql-server-agent-extension.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)합니다.

### <a name="understand-the-input-parameters"></a>입력 매개 변수 이해
다음 표에는 다음 섹션에는 PowerShell 스크립트를 실행하는 데 필요한 매개 변수가 나열되어 있습니다.

| 매개 변수 | 설명 | 예 |
| --- | --- | --- |
| **$akvURL** |**키 자격 증명 모음 URL** |"https://contosokeyvault.vault.azure.net/" |
| **$spName** |**서비스 주체 이름** |"fde2b411-33d5-4e11-af04eb07b669ccf2" |
| **$spSecret** |**서비스 주체 암호** |"9VTJSQwzlFepD8XODnzy8n2V01Jd8dAjwm/azF1XDKM=" |
| **$credName** |**자격 증명 이름**: AKV 통합은 VM이 주요 자격 증명 모음에 액세스할 수 있도록 SQL Server 내에 자격 증명을 만듭니다. 이 자격 증명의 이름을 선택하세요. |"mycred1" |
| **$vmName** |**가상 컴퓨터 이름**: 이전에 만든 SQL VM의 이름입니다. |"myvmname" |
| **$serviceName** |**서비스 이름**: SQL VM과 연결된 클라우드 서비스 이름입니다. |"mycloudservicename" |

### <a name="enable-akv-integration-with-powershell"></a>PowerShell과 AKV 통합 설정
**New-AzureVMSqlServerKeyVaultCredentialConfig** cmdlet은 Azure Key Vault Integration 기능에 대한 구성 개체를 만듭니다. **Set-AzureVMSqlServerExtension**은 **KeyVaultCredentialSettings** 매개 변수와의 통합을 구성합니다. 다음 단계에서는 이러한 명령을 사용하는 방법을 보여줍니다.

1. 이 항목의 이전 섹션에서 설명한 대로, 먼저 Azure PowerShell에서 특정 값을 사용하여 입력 매개 변수를 구성합니다. 다음은 스크립트 예입니다.
   
        $akvURL = "https://contosokeyvault.vault.azure.net/"
        $spName = "fde2b411-33d5-4e11-af04eb07b669ccf2"
        $spSecret = "9VTJSQwzlFepD8XODnzy8n2V01Jd8dAjwm/azF1XDKM="
        $credName = "mycred1"
        $vmName = "myvmname"
        $serviceName = "mycloudservicename"
2. 그런 후 다음 스크립트를 사용하여 AKV 통합을 구성 및 설정합니다.
   
     $secureakv =  $spSecret | ConvertTo-SecureString -AsPlainText -Force   $akvs = New-AzureVMSqlServerKeyVaultCredentialConfig -Enable -CredentialName $credname -AzureKeyVaultUrl $akvURL -ServicePrincipalName $spName -ServicePrincipalSecret $secureakv   Get-AzureVM -ServiceName $serviceName -Name $vmName | Set-AzureVMSqlServerExtension -KeyVaultCredentialSettings $akvs | Update-AzureVM

SQL IaaS 에이전트 확장에서 이 새로운 구성을 사용하여 SQL VM을 업데이트할 것입니다.

[!INCLUDE [AKV Integration Next Steps](../../includes/virtual-machines-sql-server-akv-next-steps.md)]




<!--HONumber=Nov16_HO3-->


