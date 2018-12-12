---
title: Azure에서 SQL VM의 라이선스 모델을 변경하는 방법 | Microsoft Docs
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
ms.openlocfilehash: 3bcbfc876ed27d16180ca03801f2054ad804e148
ms.sourcegitcommit: 345b96d564256bcd3115910e93220c4e4cf827b3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/28/2018
ms.locfileid: "52577062"
---
# <a name="how-to-change-the-licensing-model-for-a-sql-virtual-machine-in-azure"></a>Azure에서 SQL 가상 머신의 라이선스 모델을 변경하는 방법
이 문서에서는 새 SQL 리소스 공급자(**Microsoft.SqlVirtualMachine**)를 사용하여 Azure에서 SQL Server 가상 머신의 라이선스 모델을 변경하는 방법에 대해 설명합니다. SQL Server를 호스트하는 VM(가상 머신)의 두 가지 라이선스 모델은 사용당 지급 및 BYOL(사용자 라이선스 필요)입니다. 이제 Powershell 또는 Azure CLI를 사용하여 SQL VM에서 사용하는 라이선스 모델을 수정할 수 있습니다. 

**사용당 지급** 모델은 Azure VM의 초당 비용에 SQL Server 라이선스 비용이 포함됨을 의미합니다.

또한 **사용자 라이선스 필요** 모델은 [Azure 하이브리드 혜택](https://azure.microsoft.com/pricing/hybrid-benefit/)이라고도 하며 이 모델을 통해 SQL Server를 실행하는 VM과 함께 사용자의 SQL Server 라이선스를 사용할 수 있습니다. 가격에 대한 자세한 내용은 [SQL VM 가격 책정 가이드](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance)를 참조하세요.

두 라이선스 모델 간에 전환해도 **가동 중지 시간이 발생하지 않고**, VM이 다시 시작되지 않고, **추가 비용이 필요하지 않고**(실제로 AHB를 활성화하면 비용이 감소함), 전환은 **즉시 적용**됩니다. 


## <a name="register-legacy-sql-vm-with-new-resource-provider"></a>새 리소스 공급자에 레거시 SQL VM 등록
라이선스 모델 간에 전환하는 기능은 새 SQL VM 리소스 공급자(Microsoft.SqlVirtualMachine)에서 제공하는 기능입니다. 현재는 라이선스 모델을 전환하려면 먼저 새 공급자를 구독에 등록한 다음, 새 SQL VM 리소스 공급자에 레거시 VM을 등록해야 합니다. 

  >[!IMPORTANT]
  > SQL VM 리소스를 삭제하면 이미지의 하드 코드된 라이선스 설정으로 돌아갑니다. 

### <a name="powershell"></a>PowerShell

다음 코드는 먼저 새 SQL 리소스 공급자를 구독에 등록한 다음, 새 리소스 공급자에 레거시 SQL VM을 등록합니다. 

```powershell
# Connect to Azure
Connect-AzureRmAccount
Account: <account_name>

# Verify your subscription ID
Get-AzureRmContext

# Set the correct Azure Subscription ID
Set-AzureRmContext -SubscriptionId <Subscription_ID>

# Register the new SQL resource provider for your subscription
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.SqlVirtualMachine

# Register your legacy SQL VM with the new resource provider
# example: $vm=Get-AzureRmVm -ResourceGroupName AHBTest -Name AHBTest
$vm=Get-AzureRmVm -ResourceGroupName <ResourceGroupName> -Name <VMName>
New-AzureRmResource -ResourceName $vm.Name ResourceGroupName $vm.ResourceGroupName -Location $vm.Location -ResourceType Microsoft.SqlVirtualMachine/sqlVirtualMachines -Properties @{virtualMachineResourceId=$vm.Id}
```

### <a name="portal"></a>포털
포털을 사용하여 새 SQL VM 리소스 공급자를 등록할 수도 있습니다. 이렇게 하려면 다음 단계를 따르세요.
1. Azure Portal을 열고 **모든 서비스**로 이동합니다. 
1. **구독**으로 이동하고 관심 있는 구독을 선택합니다.  

  ![구독 선택](media/virtual-machines-windows-sql-ahb/open-subscriptions.png)

1. **구독** 블레이드에서 **리소스 공급자**로 이동합니다. 
1. 필터에 `sql`을 입력하여 SQL 관련 리소스 공급자를 표시합니다. 
1. 원하는 작업에 따라 **Microsoft.SqlVirtualMachine** 공급자에 대해 ‘등록’, ‘다시 등록’ 또는 ‘등록 해제’를 선택합니다. 

  ![공급자 수정](media/virtual-machines-windows-sql-ahb/select-resource-provider-sql.png)


## <a name="use-powershell"></a>PowerShell 사용 
Powershell을 사용하여 라이선스 모델을 변경할 수 있습니다.  라이선스 모델을 전환하기 전에 SQL VM이 새 SQL 리소스 공급자에 이미 등록되었는지 확인합니다. 

이 코드 조각은 사용당 지급 라이선스 모델을 BYOL(또는 Azure 하이브리드 혜택 사용)로 전환합니다. 
```powershell
#example: $SqlVm = Get-AzureRmResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName AHBTest -ResourceName AHBTest
$SqlVm = Get-AzureRmResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName <resource_group_name> -ResourceName <VM_name>
$SqlVm.Properties.sqlServerLicenseType="AHUB"
$SqlVm | Set-AzureRmResource -Force 
``` 

이 코드 조각은 BYOL 모델을 사용당 지급으로 전환합니다.
```powershell
#example: $SqlVm = Get-AzureRmResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName AHBTest -ResourceName AHBTest
$SqlVm = Get-AzureRmResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName <resource_group_name> -ResourceName <VM_name>
$SqlVm.Properties.sqlServerLicenseType="PAYG"
$SqlVm | Set-AzureRmResource -Force 
```

  >[!NOTE]
  > 라이선스 간에 전환하려면 새 SQL VM 리소스 공급자를 사용하고 있어야 합니다. SQL VM을 새 공급자에 등록하기 전에 이러한 명령을 실행하려고 하면 `Get-AzureRmResource : The Resource 'Microsoft.SqlVirtualMachine/SqlVirtualMachines/AHBTest' under resource group 'AHBTest' was not found. The property 'sqlServerLicenseType' cannot be found on this object. Verify that the property exists and can be set. ` 오류가 발생할 수 있습니다. 이 오류가 표시되면 [새 리소스 공급자에 SQL VM을 등록](#register-legacy-SQL-vm-with-new-resource-provider)하세요. 
 

## <a name="use-azure-cli"></a>Azure CLI 사용
Azure CLI를 사용하여 라이선스 모델을 변경할 수 있습니다.  라이선스 모델을 전환하기 전에 SQL VM이 새 SQL 리소스 공급자에 이미 등록되었는지 확인합니다. 

이 코드 조각은 사용당 지급 라이선스 모델을 BYOL(또는 Azure 하이브리드 혜택 사용)로 전환합니다.
```azurecli
az resource update -g <resource_group_name> -n <sql_virtual_machine_name> --resource-type "Microsoft.SqlVirtualMachine/SqlVirtualMachines" --set properties.sqlServerLicenseType=AHUB
# example: az resource update -g AHBTest -n AHBTest --resource-type "Microsoft.SqlVirtualMachine/SqlVirtualMachines" --set properties.sqlServerLicenseType=AHUB
```

이 코드 조각은 BYOL 모델을 사용당 지급으로 전환합니다. 
```azurecli
az resource update -g <resource_group_name> -n <sql_virtual_machine_name> --resource-type "Microsoft.SqlVirtualMachine/SqlVirtualMachines" --set properties.sqlServerLicenseType=PAYG
# example: az resource update -g AHBTest -n AHBTest --resource-type "Microsoft.SqlVirtualMachine/SqlVirtualMachines" --set properties.sqlServerLicenseType=PAYG
```

  >[!NOTE]
  >라이선스 간에 전환하려면 새 SQL VM 리소스 공급자를 사용하고 있어야 합니다. SQL VM을 새 공급자에 등록하기 전에 이러한 명령을 실행하려고 하면 `The Resource 'Microsoft.SqlVirtualMachine/SqlVirtualMachines/AHBTest' under resource group 'AHBTest' was not found. ` 오류가 발생할 수 있습니다. 이 오류가 표시되면 [새 리소스 공급자에 SQL VM을 등록](#register-legacy-SQL-vm-with-new-resource-provider)하세요. 

## <a name="view-current-licensing"></a>현재 라이선스 보기 

다음 코드 조각을 사용하여 SQL VM의 현재 라이선스 모델을 볼 수 있습니다. 

```powershell
# View current licensing model for your SQL VM
#example: $SqlVm = Get-AzureRmResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName <resource_group_name> -ResourceName <VM_name>
$SqlVm = Get-AzureRmResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName <resource_group_name> -ResourceName <VM_name>
$SqlVm.Properties.sqlServerLicenseType
```

## <a name="next-steps"></a>다음 단계

자세한 내용은 다음 문서를 참조하세요. 

* [Windows VM에서 SQL Server 개요](virtual-machines-windows-sql-server-iaas-overview.md)
* [Windows VM의 SQL Server FAQ](virtual-machines-windows-sql-server-iaas-faq.md)
* [Windows VM의 SQL Server 가격 책정 가이드](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Windows VM의 SQL Server 릴리스 정보](virtual-machines-windows-sql-server-iaas-release-notes.md)


