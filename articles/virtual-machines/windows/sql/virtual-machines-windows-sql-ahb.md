---
title: Azure에서 SQL Server VM의 라이선스 모델을 변경하는 방법 | Microsoft Docs
description: Azure에서 SQL 가상 머신의 라이선스를 전환하는 방법을 알아봅니다.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-resource-manager
ms.assetid: aa5bf144-37a3-4781-892d-e0e300913d03
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 11/14/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 8526716b299d26d8d70c9c5e5cdace34e188d019
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2019
ms.locfileid: "56111070"
---
# <a name="how-to-change-the-licensing-model-for-a-sql-server-virtual-machine-in-azure"></a>Azure에서 SQL Server 가상 머신의 라이선스 모델을 변경하는 방법
이 문서에서는 새 SQL VM 리소스 공급자(**Microsoft.SqlVirtualMachine**)를 사용하여 Azure에서 SQL Server 가상 머신의 라이선스 모델을 변경하는 방법에 대해 설명합니다. SQL Server를 호스트하는 VM(가상 머신)의 두 가지 라이선스 모델은 사용당 지급 및 BYOL(사용자 라이선스 필요)입니다. 이제 PowerShell 또는 Azure CLI를 사용하여 SQL Server VM에서 사용하는 라이선스 모델을 수정할 수 있습니다. 

**PAYG(종량제)** 모델은 Azure VM을 실행하는 초당 비용에 SQL Server 라이선스 비용이 포함됩니다.

**BYOL(사용자 라이선스 필요)** 모델은 [Azure 하이브리드 혜택](https://azure.microsoft.com/pricing/hybrid-benefit/)이라고도 하며, SQL Server를 실행하는 VM에 사용자의 SQL Server 라이선스를 사용할 수 있습니다. 가격에 대한 자세한 내용은 [SQL Server VM 가격 책정 가이드](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance)를 참조하세요.

두 라이선스 모델 간에 전환해도 **가동 중지 시간이 발생하지 않고**, VM이 다시 시작되지 않고, **추가 비용이 필요하지 않고**(실제로 AHB를 활성화하면 비용이 *감소함*), 전환은 **즉시 적용**됩니다. 

  >[!NOTE]
  > - 현재 라이선스 모델을 변환하는 기능은 종량제 SQL Server VM 이미지를 시작할 때만 사용할 수 있습니다. 포털에서 사용자 라이선스 필요 이미지로 시작하는 경우 해당 이미지를 종량제로 변환할 수 없습니다. 
  > - CSP 고객은 먼저 종량제 VM을 배포한 후 사용자 라이선스 필요로 변환하여 AHB 혜택을 활용할 수 있습니다. 


## <a name="prerequisites"></a>필수 조건
SQL VM 리소스 공급자를 사용하려면 SQL IaaS 확장이 필요합니다. 따라서 SQL VM 리소스 공급자를 계속 사용하려면 다음이 필요합니다.
- [Azure 구독](https://azure.microsoft.com/free/).
- [SQL IaaS 확장](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-agent-extension)이 설치된 [SQL Server VM](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision) 


## <a name="register-existing-sql-server-vm-with-sql-resource-provider"></a>기존 SQL Server VM을 SQL 리소스 공급자에 등록
라이선스 모델 간에 전환하는 기능은 새 SQL VM 리소스 공급자(Microsoft.SqlVirtualMachine)에서 제공하는 기능입니다. 2018년 12월 이후에 배포한 SQL Server VM은 새로운 리소스 공급자에 자동으로 등록됩니다. 그러나 이 날짜 이전에 배포한 기존 VM이 해당 라이선스 모델을 전환할 수 있으려면 수동으로 리소스 공급자에 등록해야 합니다. 

  > [!NOTE] 
  > SQL VM 리소스를 삭제하면 이미지의 하드 코드된 라이선스 설정으로 돌아갑니다. 

### <a name="register-sql-resource-provider-with-your-subscription"></a>SQL 리소스 공급자를 구독에 등록 

SQL Server VM을 SQL 리소스 공급자에 등록하려면 리소스 공급자를 구독에 등록해야 합니다. 이 작업은 PowerShell 또는 Azure Portal을 사용하여 수행할 수 있습니다. 

#### <a name="using-powershell"></a>PowerShell 사용
다음 코드 조각은 SQL 리소스 공급자를 Azure 구독에 등록합니다. 

```powershell
# Register the new SQL resource provider for your subscription
Register-AzResourceProvider -ProviderNamespace Microsoft.SqlVirtualMachine
```

#### <a name="using-azure-portal"></a>Azure Portal 사용
다음은 Azure Portal을 사용하여 SQL 리소스 공급자를 Azure 구독에 등록하는 단계입니다. 

1. Azure Portal을 열고 **모든 서비스**로 이동합니다. 
1. **구독**으로 이동하고 관심 있는 구독을 선택합니다.  
1. **구독** 블레이드에서 **리소스 공급자**로 이동합니다. 
1. 필터에 `sql`을 입력하여 SQL 관련 리소스 공급자를 표시합니다. 
1. 원하는 작업에 따라 **Microsoft.SqlVirtualMachine** 공급자에 대해 ‘등록’, ‘다시 등록’ 또는 ‘등록 해제’를 선택합니다. 

  ![공급자 수정](media/virtual-machines-windows-sql-ahb/select-resource-provider-sql.png)

### <a name="register-sql-server-vm-with-sql-resource-provider"></a>SQL Server VM을 SQL 리소스 공급자에 등록
SQL 리소스 공급자가 구독에 등록되면 PowerShell을 사용하여 SQL Server VM을 SQL 리소스 공급자에 등록할 수 있습니다. 


```powershell
# Register your existing SQL Server VM with the new resource provider
# example: $vm=Get-AzureRmVm -ResourceGroupName AHBTest -Name AHBTest
$vm=Get-AzureRmVm -ResourceGroupName <ResourceGroupName> -Name <VMName>
New-AzureRmResource -ResourceName $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location -ResourceType Microsoft.SqlVirtualMachine/sqlVirtualMachines -Properties @{virtualMachineResourceId=$vm.Id}
```


## <a name="use-powershell"></a>PowerShell 사용 
PowerShell을 사용하여 라이선스 모델을 변경할 수 있습니다.  라이선스 모델을 전환하기 전에 SQL Server VM이 새 SQL 리소스 공급자에 이미 등록되었는지 확인합니다. 

다음 코드 조각은 사용당 지급 라이선스 모델을 BYOL(또는 Azure 하이브리드 혜택 사용)로 전환합니다. 
```PowerShell
#example: $SqlVm = Get-AzResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName AHBTest -ResourceName AHBTest
$SqlVm = Get-AzResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName <resource_group_name> -ResourceName <VM_name>
$SqlVm.Properties.sqlServerLicenseType="AHUB"
<# the following code snippet is only necessary if using Azure Powershell version > 4
$SqlVm.Kind= "LicenseChange"
$SqlVm.Plan= [Microsoft.Azure.Management.ResourceManager.Models.Plan]::new()
$SqlVm.Sku= [Microsoft.Azure.Management.ResourceManager.Models.Sku]::new() #>
$SqlVm | Set-AzResource -Force 
``` 

다음 코드 조각은 BYOL 모델을 사용당 지급으로 전환합니다.
```PowerShell
#example: $SqlVm = Get-AzResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName AHBTest -ResourceName AHBTest
$SqlVm = Get-AzResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName <resource_group_name> -ResourceName <VM_name>
$SqlVm.Properties.sqlServerLicenseType="PAYG"
<# the following code snippet is only necessary if using Azure Powershell version > 4
$SqlVm.Kind= "LicenseChange"
$SqlVm.Plan= [Microsoft.Azure.Management.ResourceManager.Models.Plan]::new()
$SqlVm.Sku= [Microsoft.Azure.Management.ResourceManager.Models.Sku]::new() #>
$SqlVm | Set-AzResource -Force 
```

  >[!NOTE]
  > 라이선스 간에 전환하려면 새 SQL VM 리소스 공급자를 사용하고 있어야 합니다. SQL Server VM을 새 공급자에 등록하기 전에 이러한 명령을 실행하려고 하면 다음 오류가 발생할 수 있습니다. `Get-AzResource : The Resource 'Microsoft.SqlVirtualMachine/SqlVirtualMachines/AHBTest' under resource group 'AHBTest' was not found. The property 'sqlServerLicenseType' cannot be found on this object. Verify that the property exists and can be set. ` 이 오류가 표시되면 새 리소스 공급자에 SQL Server VM을 등록하세요. 

 

## <a name="use-azure-cli"></a>Azure CLI 사용
Azure CLI를 사용하여 라이선스 모델을 변경할 수 있습니다.  라이선스 모델을 전환하기 전에 SQL Server VM이 새 SQL 리소스 공급자에 이미 등록되었는지 확인합니다. 

다음 코드 조각은 사용당 지급 라이선스 모델을 BYOL(또는 Azure 하이브리드 혜택 사용)로 전환합니다.
```azurecli
az resource update -g <resource_group_name> -n <sql_virtual_machine_name> --resource-type "Microsoft.SqlVirtualMachine/SqlVirtualMachines" --set properties.sqlServerLicenseType=AHUB
# example: az resource update -g AHBTest -n AHBTest --resource-type "Microsoft.SqlVirtualMachine/SqlVirtualMachines" --set properties.sqlServerLicenseType=AHUB
```

다음 코드 조각은 BYOL 모델을 사용당 지급으로 전환합니다. 
```azurecli
az resource update -g <resource_group_name> -n <sql_virtual_machine_name> --resource-type "Microsoft.SqlVirtualMachine/SqlVirtualMachines" --set properties.sqlServerLicenseType=PAYG
# example: az resource update -g AHBTest -n AHBTest --resource-type "Microsoft.SqlVirtualMachine/SqlVirtualMachines" --set properties.sqlServerLicenseType=PAYG
```

  >[!NOTE]
  >라이선스 간에 전환하려면 새 SQL VM 리소스 공급자를 사용하고 있어야 합니다. SQL Server VM을 새 공급자에 등록하기 전에 이러한 명령을 실행하려고 하면 다음 오류가 발생할 수 있습니다. `The Resource 'Microsoft.SqlVirtualMachine/SqlVirtualMachines/AHBTest' under resource group 'AHBTest' was not found. ` 이 오류가 표시되면 [새 리소스 공급자에 SQL Server VM을 등록](#register-existing-sql-server-vm-with-sql-resource-provider)하세요. 

## <a name="view-current-licensing"></a>현재 라이선스 보기 

다음 코드 조각을 사용하여 SQL Server VM의 현재 라이선스 모델을 볼 수 있습니다. 

```PowerShell
# View current licensing model for your SQL Server VM
#example: $SqlVm = Get-AzResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName <resource_group_name> -ResourceName <VM_name>
$SqlVm = Get-AzResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName <resource_group_name> -ResourceName <VM_name>
$SqlVm.Properties.sqlServerLicenseType
```

## <a name="known-errors"></a>알려진 오류

### <a name="sql-iaas-extension-is-not-installed-on-virtual-machine"></a>Sql IaaS 확장이 가상 머신에 설치되지 않음
SQL IaaS 확장은 SQL VM 리소스 공급자에 SQL Server VM을 등록하는 데 필요한 필수 구성 요소입니다. SQL IaaS 확장을 설치하기 전에 SQL Server VM을 등록하려고 하면 다음 오류가 발생합니다.

`Sql IaaS Extension is not installed on Virtual Machine: '{0}'. Please make sure it is installed and in running state and try again later.`

이 문제를 해결하려면 SQL Server VM을 등록하기 전에 SQL IaaS 확장을 설치합니다. 

  > [!NOTE]
  > SQL IaaS 확장을 설치하면 SQL Server 서비스가 다시 시작되고, 유지 관리 기간 동안에만 이러한 설치를 수행해야 합니다. 자세한 내용은 [SQL IaaS 확장 설치](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-agent-extension#installation)를 참조하세요. 

### <a name="cannot-validate-argument-on-parameter-sku"></a>'Sku' 매개 변수의 인수가 유효한지 확인할 수 없음
Azure PowerShell 4.0 이후 버전을 사용하는 경우 SQL Server VM 라이선스 모델을 변경하려고 하면 이 오류가 발생할 수 있습니다.

`Set-AzResource : Cannot validate argument on parameter 'Sku'. The argument is null or empty. Provide an argument that is not null or empty, and then try the command again.`

이 오류를 해결하려면 라이선스 모델을 전환할 때 앞서 언급한 PowerShell 코드 조각에서 이러한 줄의 주석 처리를 제거합니다. 
```PowerShell
# the following code snippet is necessary if using Azure Powershell version > 4
$SqlVm.Kind= "LicenseChange"
$SqlVm.Plan= [Microsoft.Azure.Management.ResourceManager.Models.Plan]::new()
$SqlVm.Sku= [Microsoft.Azure.Management.ResourceManager.Models.Sku]::new()
```

다음 코드를 사용하여 Azure PowerShell 버전을 확인합니다.

```PowerShell
Get-Module -ListAvailable -Name Azure -Refresh
```

## <a name="next-steps"></a>다음 단계

자세한 내용은 다음 문서를 참조하세요. 

* [Windows VM에서 SQL Server 개요](virtual-machines-windows-sql-server-iaas-overview.md)
* [Windows VM의 SQL Server FAQ](virtual-machines-windows-sql-server-iaas-faq.md)
* [Windows VM의 SQL Server 가격 책정 가이드](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Windows VM의 SQL Server 릴리스 정보](virtual-machines-windows-sql-server-iaas-release-notes.md)


