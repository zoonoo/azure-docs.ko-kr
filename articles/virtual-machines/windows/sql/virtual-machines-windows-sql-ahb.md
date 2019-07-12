---
title: Azure에서 SQL Server VM에 대 한 라이선스 모델을 변경 하는 방법
description: "' Bring your-라이선스 ' Azure Hybrid Benefit을 사용 하 여에 '종 량 제'에서 azure에서 SQL 가상 컴퓨터에 대 한 라이선스를 전환 하는 방법에 알아봅니다."
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
ms.date: 02/13/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 78ad784a45d2b0063932791daedc9b1ec1aafd72
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/10/2019
ms.locfileid: "67786755"
---
# <a name="how-to-change-licensing-model-for-a-sql-server-virtual-machine-in-azure"></a>Azure에서 SQL Server 가상 컴퓨터에 대 한 라이선스 모델을 변경 하는 방법
이 문서에서는 새 SQL VM 리소스 공급자(**Microsoft.SqlVirtualMachine**)를 사용하여 Azure에서 SQL Server 가상 머신의 라이선스 모델을 변경하는 방법에 대해 설명합니다.

두 가지 라이선스 모델을 SQL Server-호스팅 가상 머신 (VM)에 대 한 종 량 제 및 Azure 하이브리드 혜택 (AHB). 이제 Azure portal, Azure CLI 또는 PowerShell을 사용 하 여 수정할 수 있습니다 SQL Server VM의 라이선스 모델을 

합니다 **종 량 제** (PAYG) 모델은 Azure vm의 초당 비용은 SQL Server 라이선스 비용이 포함을 의미 합니다.
합니다 [Azure 하이브리드 혜택 (AHB)](https://azure.microsoft.com/pricing/hybrid-benefit/) SQL Server를 실행 하는 VM을 사용 하 여 사용자 고유의 SQL Server 라이선스를 사용할 수 있습니다. 

SQL Server 용 Microsoft Azure Hybrid Benefit Software assurance를 소유한 ("정규화 된 라이선스") Azure Virtual Machines에서 SQL Server 라이선스를 사용할 수 있습니다. SQL Server에 대 한 Azure Hybrid Benefit을 사용 하 여 VM에서 SQL Server 라이선스의 사용을 위해 고객에 게 부과 되지 않습니다 하지만 물론를 기본 클라우드 계산 (즉, 기본 요금), 저장소 및 백업, I/O가 u와 관련 된 비용 지불 계속 해야 합니다. se 된 서비스입니다.

Microsoft 제품 사용 약관에 따라 "고객은 Azure SQL Database (관리 되는 인스턴스, 탄력적 풀 및 단일 데이터베이스)를 사용 하는 것을 나타내야 Azure Data Factory, SQL Server Integration Services 또는 Azure 하이브리드에서 SQL Server 가상 머신 Azure에서 워크 로드를 구성 하는 경우 SQL Server 혜택. "

Azure VM에서 SQL Server에 대 한 Azure 하이브리드 혜택의 사용을 나타냅니다. 준수를 세 가지 옵션이 있습니다.

1. 기업 계약으로 고객에 대 한만 사용할 수 있는 Azure marketplace에서 BYOL SQL Server 이미지를 사용 하 여 가상 컴퓨터를 프로 비전 합니다.
1. 종 량 제 SQL Server 이미지를 사용 하 여 Azure marketplace에서 가상 머신 프로 비전 하 고 AHB를 활성화 합니다.
1. 자체 Azure VM에서 SQL Server를 수동으로 설치 [SQL Server VM을 등록](virtual-machines-windows-sql-register-with-resource-provider.md) AHB를 활성화 합니다.

SQL Server의 라이선스 유형 VM 프로 비전 되 고 나중에 언제 든 지 변경 될 때 설정 됩니다. 발생 라이선스 모델 간에 전환한 **가동 중지 시간 없이**VM 다시 시작 되지 않으면, 추가 **추가 비용 없이** (사실 AHB 활성화 *줄어듭니다* 비용) 이며 **즉시 적용**합니다. 

## <a name="prerequisites"></a>필수 구성 요소

SQL VM 리소스 공급자를 사용하려면 SQL IaaS 확장이 필요합니다. 따라서 SQL VM 리소스 공급자를 계속 사용하려면 다음이 필요합니다.
- [Azure 구독](https://azure.microsoft.com/free/).
- [Software assurance가 포함](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default)합니다. 
- A [SQL Server VM](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision) 등록 된 [SQL VM 리소스 공급자](virtual-machines-windows-sql-register-with-resource-provider.md) 설치 합니다. 


## <a name="change-license-for-vms-already-registered-with-resource-provider"></a>리소스 공급자를 사용 하 여 이미 등록 된 Vm에 대 한 라이선스 변경 

# <a name="azure-portaltabazure-portal"></a>[Azure Portal](#tab/azure-portal)

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

포털에서 직접 라이선스 모델을 수정할 수 있습니다. 

1. 열기는 [Azure portal](https://portal.azure.com) 시작 하 고는 [SQL 가상 머신 리소스](virtual-machines-windows-sql-manage-portal.md#access-sql-virtual-machine-resource) SQL Server VM에 대 한 합니다. 
1. 선택 **구성할** 아래에서 **설정**합니다. 
1. 선택 된 **Azure Hybrid Benefit** 옵션 및 Software Assurance를 사용 하 여 SQL Server 라이선스가 있는지 확인 하려면이 확인란을 선택 합니다. 
1. 선택 **Apply** 맨 아래에 **구성** 페이지입니다. 

![포털에 대 한 AHB](media/virtual-machines-windows-sql-ahb/ahb-in-portal.png)


# <a name="az-clitabbash"></a>[AZ CLI](#tab/bash)

Azure CLI를 사용하여 라이선스 모델을 변경할 수 있습니다.  

다음 코드 조각은 BYOL (또는 Azure Hybrid Benefit을 사용 하 여) 종 량 제 라이선스 모델을 전환 합니다.

```azurecli-interactive
# Switch your SQL Server VM license from pay-as-you-go to bring-your-own
# example: az sql vm update -n AHBTest -g AHBTest --license-type AHUB

az sql vm update -n <VMName> -g <ResourceGroupName> --license-type AHUB
```

다음 코드 조각은 종 량 제로 bring your-own license 모델을 전환합니다. 

```azurecli-interactive
# Switch your SQL Server VM license from bring-your-own to pay-as-you-go
# example: az sql vm update -n AHBTest -g AHBTest --license-type PAYG

az sql vm update -n <VMName> -g <ResourceGroupName> --license-type PAYG
```

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)
PowerShell을 사용하여 라이선스 모델을 변경할 수 있습니다.

다음 코드 조각은 BYOL (또는 Azure Hybrid Benefit을 사용 하 여) 종 량 제 라이선스 모델을 전환 합니다.

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

다음 코드 조각은 종 량 제로 BYOL 모델을 전환합니다.

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

## <a name="change-license-for-vms-not-registered-with-resource-provider"></a>리소스 공급자에 등록 되지 Vm에 대 한 라이선스 변경

Azure Marketplace PAYG 이미지에서 SQL Server VM을 프로 비전 한 경우 종 량 제 SQL 라이선스 형식은 있게 됩니다. BYOL 이미지를 사용 하 여 Azure Marketplace에서 SQL Server VM을 프로 비전 한 경우에 라이선스 형식은 AHUB 있게 됩니다. 기본 (PAYG)에서 모든 SQL Server Vm 프로 비전 또는 BYOL Azure Marketplace 이미지를 자동으로 등록할 SQL VM 리소스 공급자를 사용 하 여 변경할 수 있도록는 [라이선스 형식](#change-license-for-vms-already-registered-with-resource-provider)

자체 Azure Hybrid Benefit을 통해 Azure VM에서 SQL Server를 설치만 이용할 및 수행 해야 합니다 [SQL VM 리소스 공급자를 사용 하 여 이러한 Vm을 등록](virtual-machines-windows-sql-register-with-resource-provider.md) AHB 사용량에 따라 나타내려면 AHB으로 SQL Server 라이선스를 설정 하 여 Microsoft 제품 사용 약관입니다.

SQL VM을 SQL VM 리소스 공급자를 사용 하 여 등록 된 경우에 종 량 제 또는 AHB SQL Server VM의 라이선스 유형을 변경할 수 있습니다. 및 라이선스 준수에 대 한 모든 SQL Vm을 SQL VM RP에 등록 하면 됩니다.

## <a name="remarks"></a>설명

 - Azure 클라우드 솔루션 파트너 (CSP) 고객은 먼저 종 량 제 VM을 배포 하 고 활성 sa가 있는 경우--소유-라이선스를 변환 하 여 Azure Hybrid Benefit을 이용할 수 있습니다.
 - SQL Server VM 리소스를 삭제 하면 이동 하 게 다시 이미지의 하드 코드 된 라이선스 설정 합니다. 
  - 라이선스 모델을 변경 하는 기능에는 SQL VM 리소스 공급자의 기능입니다. Azure portal 통해 marketplace 이미지를 자동으로 배포 리소스 공급자를 사용 하 여 SQL Server VM을 등록 합니다. 그러나 SQL Server 설치 하는 자체 고객 해야 수동으로 [SQL Server VM을 등록](virtual-machines-windows-sql-register-with-resource-provider.md)합니다. 
- SQL Server VM을 가용성 집합에 추가 VM을 다시 필요 합니다. 이러한 모든 vm이 가용성에 추가 집합은 기본 종 량 제 라이선스 형식을 돌아가서 AHB 다시 사용 하도록 설정 해야 합니다. 


## <a name="limitations"></a>제한 사항

 - 만 라이선스 모델 변경 software assurance가 있는 고객에 게 제공 됩니다.
 - 라이선스 모델 변경 SQL Server의 standard 및 enterprise edition 에서만 지원 됩니다. Express, 웹 및 개발자에 대 한 라이선스 변경 지원 되지 않습니다. 
 - Resource Manager 모델을 사용 하 여 배포 된 가상 컴퓨터는 라이선스 모델 변경만 지원 됩니다. 클래식 모델을 사용 하 여 배포 된 Vm 지원 되지 않습니다. 
 - 공용 클라우드 설치의 경우는 라이선싱 모델 변경만 활성화 됩니다.
 - 라이선스 모델 변경 단일 NIC (네트워크 인터페이스)를 가진 가상 컴퓨터 에서만 지원 됩니다. 둘 이상의 NIC가 있는 가상 컴퓨터에서 먼저 제거 해야 Nic 중 하나 (Azure portal을 사용) 하는 절차를 시도 하기 전에 합니다. 그렇지 않은 경우 다음과 유사한 오류를 실행 합니다. `The virtual machine '\<vmname\>' has more than one NIC associated.` 자동 패치 및 백업 처럼 Azure portal에서 SQL 구성 페이지를 통해 수행 된 작업은 더 이상 라이선스 모드를 변경한 후 NIC를 VM에 다시 추가할 수 없습니다, 있지만 지원 합니다.


## <a name="known-errors"></a>알려진 오류

### <a name="the-resource-microsoftsqlvirtualmachinesqlvirtualmachinesresource-group-under-resource-group-resource-group-was-not-found-the-property-sqlserverlicensetype-cannot-be-found-on-this-object-verify-that-the-property-exists-and-can-be-set"></a>리소스 ' Microsoft.SqlVirtualMachine/SqlVirtualMachines/\<리소스 그룹 >' 리소스 그룹 '\<리소스 그룹 >'를 찾을 수 없습니다. 이 개체의 'sqlServerLicenseType' 속성을 찾을 수 없습니다. 속성이 있고 설정할 수 있는지 확인 합니다.
이 오류는 SQL VM 리소스 공급자로 등록 되지 않은 SQL Server VM의 라이선스 모델을 변경 하려고 할 때 발생 합니다. 리소스 공급자를 등록 해야 하 [구독](virtual-machines-windows-sql-register-with-resource-provider.md#register-sql-vm-resource-provider-with-subscription), 다음 SQL을 사용 하 여 SQL Server VM을 등록 [리소스 공급자](virtual-machines-windows-sql-register-with-resource-provider.md)합니다. 

### <a name="cannot-validate-argument-on-parameter-sku"></a>'Sku' 매개 변수의 인수가 유효한지 확인할 수 없음
Azure PowerShell을 사용 하는 경우 SQL Server VM 라이선스 모델을 변경 하려고 할 때이 오류가 발생할 수 있습니다 > 4.0: `Set-AzResource: Cannot validate argument on parameter 'Sku'. The argument is null or empty. Provide an argument that is not null or empty, and then try the command again.`

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


