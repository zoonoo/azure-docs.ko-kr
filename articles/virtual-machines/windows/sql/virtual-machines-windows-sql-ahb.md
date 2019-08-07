---
title: Azure에서 SQL Server VM에 대 한 라이선스 모델을 변경 하는 방법
description: Azure 하이브리드 혜택를 사용 하 여 Azure에서 SQL 가상 머신의 라이선스를 ' 종 량 제 '에서 ' 사용자의 라이선스 가져오기 '로 전환 하는 방법을 알아봅니다.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: jroth
tags: azure-resource-manager
ms.assetid: aa5bf144-37a3-4781-892d-e0e300913d03
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/05/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 37457d8ce1189f9282f4763633e944e3c2d639c9
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/06/2019
ms.locfileid: "68816728"
---
# <a name="how-to-change-licensing-model-for-a-sql-server-virtual-machine-in-azure"></a>Azure에서 SQL Server 가상 컴퓨터에 대 한 라이선스 모델을 변경 하는 방법
이 문서에서는 새 SQL VM 리소스 공급자(**Microsoft.SqlVirtualMachine**)를 사용하여 Azure에서 SQL Server 가상 머신의 라이선스 모델을 변경하는 방법에 대해 설명합니다.

VM (가상 컴퓨터) 호스팅 SQL Server 종 량 제 및 Azure 하이브리드 혜택 (AHB)에 대 한 두 가지 라이선스 모델이 있습니다. 이제 Azure Portal, Azure CLI 또는 PowerShell을 사용 하 여 SQL Server VM 라이선스 모델을 수정할 수 있습니다. 

PAYG ( **종 량** 제) 모델은 Azure VM을 실행 하는 초당 비용에 SQL Server 라이선스 비용이 포함 됨을 의미 합니다.
[Azure 하이브리드 혜택 (AHB)](https://azure.microsoft.com/pricing/hybrid-benefit/) 를 사용 하면 SQL Server를 실행 하는 VM에서 사용자 고유의 SQL Server 라이선스를 사용할 수 있습니다. 

Microsoft Azure 하이브리드 혜택을 통해 Azure Virtual Machines에서 소프트웨어 보증 ("적격 라이선스")이 포함 된 SQL Server 라이선스를 사용할 수 SQL Server. SQL Server에 대 한 Azure 하이브리드 혜택를 사용 하는 경우 VM에 대 한 SQL Server 라이선스 사용에 대 한 요금은 청구 되지 않지만 기본 클라우드 계산 (즉, 기본 요금), 저장소 및 백업 비용에 대 한 비용을 지불 해야 합니다. 서비스의 se (해당 하는 경우).

Microsoft 제품 약관에 따라 고객은 Azure 하이브리드에서 Azure SQL Database (Managed Instance, Elastic Pool 및 Single Database), Azure Data Factory, SQL Server Integration Services 또는 SQL Server Virtual Machines를 사용 하 고 있음을 나타내야 합니다. Azure에서 워크 로드를 구성할 때 SQL Server 혜택

Azure VM의 SQL Server에 대 한 Azure 하이브리드 혜택를 사용 하 고 정책을 준수 하도록 지정 하려면 다음 세 가지 옵션을 사용 합니다.

1. Azure marketplace의 BYOL SQL Server 이미지를 사용 하 여 가상 컴퓨터를 프로 비전 합니다. 기업계약 있는 고객만 사용할 수 있습니다.
1. Azure marketplace에서 PAYG SQL Server 이미지를 사용 하 여 가상 머신을 프로 비전 하 고 AHB를 활성화 합니다.
1. 자동 설치 SQL Server Azure VM에서 수동으로 [SQL Server VM를 등록](virtual-machines-windows-sql-register-with-resource-provider.md) 하 고 AHB를 활성화 합니다.

SQL Server 라이선스 유형은 VM이 프로 비전 될 때 설정 되며 나중에 언제 든 지 변경할 수 있습니다. 라이선스 모델 간을 전환 하면 **가동 중지 시간이**발생 하지 않으며, VM을 다시 시작 하지 않고 **추가 비용 없이 추가 비용** 을 *절감* 하 고 (실제로 AHB를 활성화 하 여 비용 절감) **즉시 적용**됩니다. 

## <a name="prerequisites"></a>필수 구성 요소

SQL VM 리소스 공급자를 사용하려면 SQL IaaS 확장이 필요합니다. 따라서 SQL VM 리소스 공급자를 계속 사용하려면 다음이 필요합니다.
- [Azure 구독](https://azure.microsoft.com/free/).
- [소프트웨어 보증](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default). 
- 설치 된 [SQL Server VM](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision) [SQL VM 리소스 공급자](virtual-machines-windows-sql-register-with-resource-provider.md) 에 등록 되어 있습니다. 


## <a name="change-license-for-vms-already-registered-with-resource-provider"></a>리소스 공급자에 이미 등록 된 Vm에 대 한 라이선스 변경 

# <a name="azure-portaltabazure-portal"></a>[Azure Portal](#tab/azure-portal)

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

포털에서 직접 라이선스 모델을 수정할 수 있습니다. 

1. [Azure Portal](https://portal.azure.com) 를 열고 SQL Server VM에 대 한 [SQL 가상 머신 리소스](virtual-machines-windows-sql-manage-portal.md#access-sql-virtual-machine-resource) 를 시작 합니다. 
1. **설정**아래에서 **구성** 을 선택 합니다. 
1. **Azure 하이브리드 혜택** 옵션을 선택 하 고 확인란을 선택 하 여 소프트웨어 보증이 포함 된 SQL Server 라이선스가 있는지 확인 합니다. 
1. **구성** 페이지의 아래쪽에서 **적용** 을 선택 합니다. 

![포털의 AHB](media/virtual-machines-windows-sql-ahb/ahb-in-portal.png)


# <a name="az-clitabbash"></a>[AZ CLI](#tab/bash)

Azure CLI를 사용하여 라이선스 모델을 변경할 수 있습니다.  

다음 코드 조각은 종 량 제 라이선스 모델을 BYOL로 전환 하거나 Azure 하이브리드 혜택를 사용 하 여 전환 합니다.

```azurecli-interactive
# Switch your SQL Server VM license from pay-as-you-go to bring-your-own
# example: az sql vm update -n AHBTest -g AHBTest --license-type AHUB

az sql vm update -n <VMName> -g <ResourceGroupName> --license-type AHUB
```

다음 코드 조각은 사용자 자신의 라이선스 모델을 종 량 제로 전환 합니다. 

```azurecli-interactive
# Switch your SQL Server VM license from bring-your-own to pay-as-you-go
# example: az sql vm update -n AHBTest -g AHBTest --license-type PAYG

az sql vm update -n <VMName> -g <ResourceGroupName> --license-type PAYG
```

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)
PowerShell을 사용하여 라이선스 모델을 변경할 수 있습니다.

다음 코드 조각은 종 량 제 라이선스 모델을 BYOL로 전환 하거나 Azure 하이브리드 혜택를 사용 하 여 전환 합니다.

```powershell-interactive
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

다음 코드 조각은 BYOL 모델을 종 량 제로로 전환 합니다.

```powershell-interactive
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
---

## <a name="change-license-for-vms-not-registered-with-resource-provider"></a>리소스 공급자에 등록 되지 않은 Vm에 대 한 라이선스 변경

PAYG Azure Marketplace 이미지에서 SQL Server VM를 프로 비전 한 경우 SQL 라이선스 유형이 PAYG 됩니다. Azure Marketplace에서 BYOL 이미지를 사용 하 여 SQL Server VM를 프로 비전 한 경우 라이선스 유형은 AHUB가 됩니다. 기본 (PAYG) 또는 BYOL Azure Marketplace 이미지에서 프로 비전 된 모든 SQL Server Vm은 SQL VM 리소스 공급자에 자동으로 등록 되므로 [라이선스 유형을](#change-license-for-vms-already-registered-with-resource-provider) 변경할 수 있습니다.

Azure 하이브리드 혜택를 통해 Azure VM에 SQL Server 자체 설치만 가능 하며, Microsoft 제품 약관에 따라 AHB 사용을 나타내는 AHB로 SQL Server 라이선스를 설정 하 여 [이러한 vm을 SQL vm 리소스 공급자에 등록](virtual-machines-windows-sql-register-with-resource-provider.md) 해야 합니다.

Sql VM이 SQL VM 리소스 공급자에 등록 되어 있는 경우에만 SQL Server VM 라이선스 유형을 PAYG 또는 AHB로 변경할 수 있습니다. 라이선스 준수를 위해 모든 SQL vm을 SQL VM RP에 등록 해야 합니다.

## <a name="remarks"></a>설명

 - Azure CSP (클라우드 솔루션 파트너) 고객은 먼저 종 량 제 VM을 배포 하 고 활성 SA가 있는 경우 자신의 라이선스 가져오기로 전환 하 여 Azure 하이브리드 혜택 활용할 수 있습니다.
 - SQL Server VM 리소스를 삭제 하는 경우 이미지의 하드 코드 된 라이선스 설정으로 돌아갑니다. 
  - 라이선스 모델을 변경 하는 기능은 SQL VM 리소스 공급자의 기능입니다. Azure Portal를 통해 marketplace 이미지를 배포 하면 SQL Server VM 리소스 공급자와 함께 자동으로 등록 됩니다. 그러나 SQL Server를 자동으로 설치 하는 고객은 SQL Server VM을 수동으로 [등록](virtual-machines-windows-sql-register-with-resource-provider.md)해야 합니다. 
- 가용성 집합에 SQL Server VM를 추가 하려면 VM을 다시 만들어야 합니다. 따라서 가용성 집합에 추가 된 모든 Vm은 기본 종 량 제 라이선스 형식으로 돌아가서 AHB를 다시 사용 하도록 설정 해야 합니다. 


## <a name="limitations"></a>제한 사항

 - 라이선스 모델 변경은 소프트웨어 보증이 있는 고객만 사용할 수 있습니다.
 - 라이선스 모델 변경은 standard 및 enterprise edition SQL Server 에서만 지원 됩니다. Express, Web 및 Developer의 라이선스 변경은 지원 되지 않습니다. 
 - 라이선스 모델 변경은 리소스 관리자 모델을 사용 하 여 배포 된 가상 컴퓨터에 대해서만 지원 됩니다. 클래식 모델을 사용 하 여 배포 된 Vm은 지원 되지 않습니다. 클래식에서 리소스 관리자 (ARM) 모델로 VM을 마이그레이션하고 SQL VM 리소스 공급자에 등록할 수 있습니다. VM이 SQL VM 리소스 공급자에 등록 되 면 VM에서 라이선스 모델 변경 내용이 제공 됩니다. 
 - 라이선스 모델 변경은 공용 클라우드 설치에만 사용할 수 있습니다.
 - 라이선스 모델 변경은 단일 NIC (네트워크 인터페이스)가 있는 가상 컴퓨터 에서만 지원 됩니다. NIC가 둘 이상 있는 가상 머신에서는 먼저 Azure Portal를 사용 하 여 Nic 중 하나를 제거한 후 절차를 시도 합니다. 그렇지 않으면 다음과 유사한 오류가 발생 합니다. `The virtual machine '\<vmname\>' has more than one NIC associated.`라이선스 모드를 변경한 후에는 VM에 NIC를 다시 추가할 수 있지만 자동 패치 및 백업과 같은 Azure Portal의 SQL 구성 페이지에서 수행 되는 작업은 더 이상 지원 되지 않는 것으로 간주 됩니다.


## <a name="known-errors"></a>알려진 오류

### <a name="the-resource-microsoftsqlvirtualmachinesqlvirtualmachinesresource-group-under-resource-group-resource-group-was-not-found-the-property-sqlserverlicensetype-cannot-be-found-on-this-object-verify-that-the-property-exists-and-can-be-set"></a>리소스 그룹 '\<리소스 그룹 > '에서 '\<SqlVirtualMachine/SqlVirtualMachines/resource-group > ' 리소스를 찾을 수 없습니다. 이 개체에서 ' sqlServerLicenseType ' 속성을 찾을 수 없습니다. 속성이 존재 하며 설정할 수 있는지 확인 하십시오.
이 오류는 SQL VM 리소스 공급자에 등록 되지 않은 SQL Server VM에서 라이선스 모델을 변경 하려고 할 때 발생 합니다. [구독](virtual-machines-windows-sql-register-with-resource-provider.md#register-sql-vm-resource-provider-with-subscription)에 리소스 공급자를 등록 한 다음 SQL [리소스 공급자](virtual-machines-windows-sql-register-with-resource-provider.md)에 SQL Server VM을 등록 해야 합니다. 

### <a name="cannot-validate-argument-on-parameter-sku"></a>'Sku' 매개 변수의 인수가 유효한지 확인할 수 없음
Azure PowerShell > 4.0를 사용할 때 SQL Server VM 라이선스 모델을 변경 하려고 하면이 오류가 발생할 수 있습니다.`Set-AzResource: Cannot validate argument on parameter 'Sku'. The argument is null or empty. Provide an argument that is not null or empty, and then try the command again.`

이 오류를 해결하려면 라이선스 모델을 전환할 때 앞서 언급한 PowerShell 코드 조각에서 이러한 줄의 주석 처리를 제거합니다.

  ```powershell-interactive
  # the following code snippet is necessary if using Azure Powershell version > 4
  $SqlVm.Kind= "LicenseChange"
  $SqlVm.Plan= [Microsoft.Azure.Management.ResourceManager.Models.Plan]::new()
  $SqlVm.Sku= [Microsoft.Azure.Management.ResourceManager.Models.Sku]::new()
  ```
  
다음 코드를 사용하여 Azure PowerShell 버전을 확인합니다.
  
  ```powershell-interactive
  Get-Module -ListAvailable -Name Azure -Refresh
  ```

## <a name="next-steps"></a>다음 단계

자세한 내용은 다음 문서를 참조하세요. 

* [Windows VM에서 SQL Server 개요](virtual-machines-windows-sql-server-iaas-overview.md)
* [Windows VM의 SQL Server FAQ](virtual-machines-windows-sql-server-iaas-faq.md)
* [Windows VM의 SQL Server 가격 책정 가이드](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Windows VM의 SQL Server 릴리스 정보](virtual-machines-windows-sql-server-iaas-release-notes.md)


