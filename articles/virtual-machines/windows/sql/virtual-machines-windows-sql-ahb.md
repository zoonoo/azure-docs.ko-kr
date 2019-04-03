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
ms.date: 02/13/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: bc3e2955049188b0794367d5391762f5eb50b1c0
ms.sourcegitcommit: 04716e13cc2ab69da57d61819da6cd5508f8c422
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/02/2019
ms.locfileid: "58850181"
---
# <a name="how-to-change-the-licensing-model-for-a-sql-server-virtual-machine-in-azure"></a>Azure에서 SQL Server 가상 머신의 라이선스 모델을 변경하는 방법
이 문서에서는 새 SQL VM 리소스 공급자(**Microsoft.SqlVirtualMachine**)를 사용하여 Azure에서 SQL Server 가상 머신의 라이선스 모델을 변경하는 방법에 대해 설명합니다. 두 개의 SQL Server-종 량 제를 호스팅하는 가상 머신 (VM)에 대 한 모델 라이선스 및 라이선스 (BYOL). 이제 PowerShell 또는 Azure CLI를 사용하여 SQL Server VM에서 사용하는 라이선스 모델을 수정할 수 있습니다. 

합니다 **종 량 제** (PAYG) 모델은 Azure vm의 초당 비용은 SQL Server 라이선스 비용이 포함을 의미 합니다.

**bring your-own license** (BYOL) 모델은 라고도 합니다 [Azure 하이브리드 혜택 (AHB)](https://azure.microsoft.com/pricing/hybrid-benefit/), SQL Server를 실행 하는 VM을 사용 하 여 사용자 고유의 SQL Server 라이선스를 사용할 수 있습니다. 가격에 대한 자세한 내용은 [SQL Server VM 가격 책정 가이드](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance)를 참조하세요.

두 라이선스 모델 간에 전환해도 **가동 중지 시간이 발생하지 않고**, VM이 다시 시작되지 않고, **추가 비용이 필요하지 않고**(실제로 AHB를 활성화하면 비용이 *감소함*), 전환은 **즉시 적용**됩니다. 

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

## <a name="remarks"></a>설명

 - 현재 라이선스 모델을 변환하는 기능은 종량제 SQL Server VM 이미지를 시작할 때만 사용할 수 있습니다. 포털에서 사용자 라이선스 필요 이미지로 시작하는 경우 해당 이미지를 종량제로 변환할 수 없습니다.
 - CSP 고객은 먼저 종량제 VM을 배포한 후 사용자 라이선스 필요로 변환하여 AHB 혜택을 활용할 수 있습니다. 
 - 현재이 기능은 공용 클라우드 설치에만 활성화 됩니다.
 - SQL Server VM 이미지를 사용자 지정 리소스 공급자에 등록 하는 경우 'AHUB' = 라이선스 유형을 지정 합니다. 라이선스 종료 빈 값으로 입력 하거나 '종 량 제'를 지정 하면 등록이 실패 합니다. 

## <a name="prerequisites"></a>필수 조건
SQL VM 리소스 공급자를 사용하려면 SQL IaaS 확장이 필요합니다. 따라서 SQL VM 리소스 공급자를 계속 사용하려면 다음이 필요합니다.
- [Azure 구독](https://azure.microsoft.com/free/).
- [Software assurance가 포함](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default)합니다. 
- A *종 량 제* [SQL Server VM](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision) 사용 하 여 합니다 [SQL IaaS 확장](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-agent-extension) 설치 합니다. 


## <a name="register-sql-resource-provider-to-your-subscription"></a>구독에 SQL 리소스 공급자 등록 

라이선스 모델 간에 전환하는 기능은 새 SQL VM 리소스 공급자(Microsoft.SqlVirtualMachine)에서 제공하는 기능입니다. 2018년 12월 이후에 배포한 SQL Server VM은 새로운 리소스 공급자에 자동으로 등록됩니다. 그러나 이 날짜 이전에 배포한 기존 VM이 해당 라이선스 모델을 전환할 수 있으려면 수동으로 리소스 공급자에 등록해야 합니다. 

  > [!NOTE] 
  > SQL Server VM 리소스를 삭제하면 이미지의 하드 코드된 라이선스 설정으로 돌아갑니다. 

SQL Server VM을 SQL 리소스 공급자에 등록하려면 리소스 공급자를 구독에 등록해야 합니다. Azure portal, Azure CLI 또는 PowerShell을 사용 하 여이 수행할 수 있습니다. 

### <a name="with-the-azure-portal"></a>Azure Portal 사용
다음 단계는 Azure portal을 사용 하 여 Azure 구독에 SQL 리소스 공급자를 등록 됩니다. 

1. Azure Portal을 열고 **모든 서비스**로 이동합니다. 
1. **구독**으로 이동하고 관심 있는 구독을 선택합니다.  
1. **구독** 블레이드에서 **리소스 공급자**로 이동합니다. 
1. 필터에 `sql`을 입력하여 SQL 관련 리소스 공급자를 표시합니다. 
1. 원하는 작업에 따라 **Microsoft.SqlVirtualMachine** 공급자에 대해 ‘등록’, ‘다시 등록’ 또는 ‘등록 해제’를 선택합니다. 

   ![공급자 수정](media/virtual-machines-windows-sql-ahb/select-resource-provider-sql.png)

### <a name="with-azure-cli"></a>Azure CLI 사용
다음 코드 조각은 Azure 구독에 SQL 리소스 공급자를 등록 됩니다. 

```azurecli
# Register the new SQL resource provider to your subscription 
az provider register --namespace Microsoft.SqlVirtualMachine 
```

### <a name="with-powershell"></a>PowerShell 사용
다음 코드 조각은 Azure 구독에 SQL 리소스 공급자를 등록 됩니다. 

```powershell
# Register the new SQL resource provider to your subscription
Register-AzResourceProvider -ProviderNamespace Microsoft.SqlVirtualMachine
```


## <a name="register-sql-server-vm-with-sql-resource-provider"></a>SQL Server VM을 SQL 리소스 공급자에 등록
SQL 리소스 공급자 구독에 등록 되 면 리소스 공급자를 사용 하 여 다음 SQL Server VM을 등록할 수 있습니다. 따라서 Azure CLI 및 PowerShell을 사용 하 여 수행할 수 있습니다. 

### <a name="with-azure-cli"></a>Azure CLI 사용

다음 코드 조각을 사용 하 여 Azure CLI를 사용 하 여 SQL Server VM을 등록 합니다. 

```azurecli
# Register your existing SQL Server VM with the new resource provider
az sql vm create -n <VMName> -g <ResourceGroupName> -l <VMLocation>
```

### <a name="with-powershell"></a>PowerShell 사용

다음 코드 조각을 사용 하 여 PowerShell을 사용 하 여 SQL Server VM을 등록 합니다. 

```powershell
# Register your existing SQL Server VM with the new resource provider
# example: $vm=Get-AzVm -ResourceGroupName AHBTest -Name AHBTest
$vm=Get-AzVm -ResourceGroupName <ResourceGroupName> -Name <VMName>
New-AzResource -ResourceName $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location -ResourceType Microsoft.SqlVirtualMachine/sqlVirtualMachines -Properties @{virtualMachineResourceId=$vm.Id}
```

## <a name="change-licensing-model"></a>라이선스 모델 변경

리소스 공급자를 SQL Server VM에 등록 되 면 Azure portal, Azure CLI 또는 PowerShell을 사용 하 여 라이선스 모델을 변경할 수 있습니다. 

### <a name="with-the-azure-portal"></a>Azure Portal 사용

포털에서 직접 라이선스 모델을 수정할 수 있습니다. 

1. 내에서 SQL Server VM으로 이동 합니다 [Azure portal](https://portal.azure.com)합니다. 
1. 선택 **SQL Server 구성** 에 **설정** 창입니다. 
1. 선택 **편집할** 에 **SQL Server 라이선스** 라이선스를 수정 하는 창입니다. 

![포털에 대 한 AHB](media/virtual-machines-windows-sql-ahb/ahb-in-portal.png)

  >[!NOTE]
  > 이 옵션은 bring your-라이선스 이미지에 사용할 수 없습니다. 

### <a name="with-azure-cli"></a>Azure CLI 사용

Azure CLI를 사용하여 라이선스 모델을 변경할 수 있습니다.  

다음 코드 조각은 BYOL (또는 Azure Hybrid Benefit을 사용 하 여) 종 량 제 라이선스 모델을 전환 합니다.

```azurecli
# Switch your SQL Server VM license from pay-as-you-go to bring-your-own
# example: az sql vm update -n AHBTest -g AHBTest --license-type AHUB

az sql vm update -n <VMName> -g <ResourceGroupName> --license-type AHUB
```

다음 코드 조각은 종 량 제로 BYOL 모델을 전환합니다. 

```azurecli
# Switch your SQL Server VM license from bring-your-own to pay-as-you-go
# example: az sql vm update -n AHBTest -g AHBTest --license-type PAYG

az sql vm update -n <VMName> -g <ResourceGroupName> --license-type PAYG
```

### <a name="with-powershell"></a>PowerShell 사용 

PowerShell을 사용하여 라이선스 모델을 변경할 수 있습니다. 

다음 코드 조각은 BYOL (또는 Azure Hybrid Benefit을 사용 하 여) 종 량 제 라이선스 모델을 전환 합니다. 

```PowerShell
# Switch your SQL Server VM license from pay-as-you-go to bring-your-own
#example: $SqlVm = Get-AzResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName AHBTest -ResourceName AHBTest

$SqlVm = Get-AzResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName <resource_group_name> -ResourceName <VM_name>
$SqlVm.Properties.sqlServerLicenseType="AHUB"
<# the following code snippet is only necessary if using Azure Powershell version > 4
$SqlVm.Kind= "LicenseChange"
$SqlVm.Plan= [Microsoft.Azure.Management.ResourceManager.Models.Plan]::new()
$SqlVm.Sku= [Microsoft.Azure.Management.ResourceManager.Models.Sku]::new() #>
$SqlVm | Set-AzResource -Force 
```

다음 코드 조각은 종 량 제로 BYOL 모델을 전환합니다.

```PowerShell
# Switch your SQL Server VM license from bring-your-own to pay-as-you-go
#example: $SqlVm = Get-AzResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName AHBTest -ResourceName AHBTest

$SqlVm = Get-AzResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName <resource_group_name> -ResourceName <VM_name>
$SqlVm.Properties.sqlServerLicenseType="PAYG"
<# the following code snippet is only necessary if using Azure Powershell version > 4
$SqlVm.Kind= "LicenseChange"
$SqlVm.Plan= [Microsoft.Azure.Management.ResourceManager.Models.Plan]::new()
$SqlVm.Sku= [Microsoft.Azure.Management.ResourceManager.Models.Sku]::new() #>
$SqlVm | Set-AzResource -Force 
``` 


## <a name="view-current-licensing"></a>현재 라이선스 보기 

다음 코드 조각을 사용하여 SQL Server VM의 현재 라이선스 모델을 볼 수 있습니다. 

```powershell
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
```powershell
# the following code snippet is necessary if using Azure Powershell version > 4
$SqlVm.Kind= "LicenseChange"
$SqlVm.Plan= [Microsoft.Azure.Management.ResourceManager.Models.Plan]::new()
$SqlVm.Sku= [Microsoft.Azure.Management.ResourceManager.Models.Sku]::new()
```

다음 코드를 사용하여 Azure PowerShell 버전을 확인합니다.

```powershell
Get-Module -ListAvailable -Name Azure -Refresh
```

### <a name="the-resource-microsoftsqlvirtualmachinesqlvirtualmachinesresource-group-under-resource-group-resource-group-was-not-found-the-property-sqlserverlicensetype-cannot-be-found-on-this-object-verify-that-the-property-exists-and-can-be-set"></a>리소스 'Microsoft.SqlVirtualMachine/SqlVirtualMachines/ < 리소스 그룹 >' 리소스 그룹 ' < 리소스 그룹 >'을 찾을 수 없습니다. 이 개체의 'sqlServerLicenseType' 속성을 찾을 수 없습니다. 속성이 있고 설정할 수 있는지 확인 합니다.
이 오류는 SQL 리소스 공급자를 사용 하 여 등록 되지 않은 SQL Server VM의 라이선스 모델을 변경 하려고 할 때 발생 합니다. 리소스 공급자를 등록 해야 하 [구독](#register-sql-resource-provider-to-your-subscription), 다음 SQL을 사용 하 여 SQL Server VM을 등록 [리소스 공급자](#register-sql-server-vm-with-sql-resource-provider)합니다. 

## <a name="next-steps"></a>다음 단계

자세한 내용은 다음 문서를 참조하세요. 

* [Windows VM에서 SQL Server 개요](virtual-machines-windows-sql-server-iaas-overview.md)
* [Windows VM의 SQL Server FAQ](virtual-machines-windows-sql-server-iaas-faq.md)
* [Windows VM의 SQL Server 가격 책정 가이드](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Windows VM의 SQL Server 릴리스 정보](virtual-machines-windows-sql-server-iaas-release-notes.md)


