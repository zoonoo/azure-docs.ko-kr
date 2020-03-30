---
title: Azure에서 SQL 서버 VM에 대한 라이센스 모델 변경
description: Azure 하이브리드 혜택을 사용하여 Azure의 SQL Server 가상 컴퓨터에 대한 라이선스를 종량제에서 사용자 고유의 라이선스로 전환하는 방법을 알아봅니다.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: jroth
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 11/13/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 502d1fe599accb29ccc99c9e527f8d1c8e1d52b8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77201828"
---
# <a name="change-the-license-model-for-a-sql-server-virtual-machine-in-azure"></a>Azure에서 SQL Server 가상 시스템에 대한 라이선스 모델 변경
이 문서에서는 새 SQL VM 리소스 공급자인 **Microsoft.SqlVirtualMachine**을 사용하여 Azure의 SQL Server 가상 컴퓨터(VM)에 대한 라이센스 모델을 변경하는 방법에 대해 설명합니다.

SQL Server를 호스팅하는 VM에는 종량제, Azure 하이브리드 혜택 및 DR(재해 복구)의 세 가지 라이선스 모델이 있습니다. Azure 포털, Azure CLI 또는 PowerShell을 사용하여 SQL Server VM의 라이선스 모델을 수정할 수 있습니다. 

- **종량제** 모델은 Azure VM을 실행하는 초당 비용에 SQL Server 라이선스 비용이 포함됩니다.
- [Azure 하이브리드 혜택을](https://azure.microsoft.com/pricing/hybrid-benefit/) 사용하면 SQL Server를 실행하는 VM을 사용하여 고유한 SQL Server 라이선스를 사용할 수 있습니다. 
- **재해 복구** 라이센스 유형은 Azure의 무료 [DR 복제본에](virtual-machines-windows-sql-high-availability-dr.md#free-dr-replica-in-azure) 사용됩니다. 

Azure 하이브리드 혜택을 사용하면 Azure 가상 컴퓨터에서 소프트웨어 보증("적격 라이선스")을 사용하여 SQL Server 라이선스를 사용할 수 있습니다. Azure 하이브리드 혜택의 경우 고객은 VM에서 SQL Server 라이선스 사용에 대한 요금이 청구되지 않습니다. 그러나 기본 클라우드 계산(즉, 기본 요금), 저장소 및 백업 비용을 지불해야 합니다. 또한 서비스 사용과 관련된 I/O에 대해 비용을 지불해야 합니다(해당되는 경우).

Microsoft 제품 약관에 따르면 " 고객은 Azure SQL 데이터베이스(관리되는 인스턴스, 탄력적 풀 및 단일 데이터베이스), Azure 데이터 팩터리, SQL Server 통합 서비스 또는 Azure 에서 SQL 서버 가상 컴퓨터를 사용하고 있음을 나타내야 합니다. Azure에서 워크로드를 구성할 때 SQL Server에 대한 하이브리드 이점입니다."

Azure VM에서 SQL Server에 대한 Azure 하이브리드 혜택의 사용을 나타내고 준수하려면 세 가지 옵션이 있습니다.

- Azure Marketplace에서 사용자 고유의 라이선스 SQL Server 이미지를 사용하여 가상 컴퓨터를 프로비전합니다. 이 옵션은 기업 계약을 체결한 고객만 사용할 수 있습니다.
- Azure Marketplace에서 종량제 SQL Server 이미지를 사용하여 가상 컴퓨터를 프로비전하고 Azure 하이브리드 혜택을 활성화합니다.
- Azure VM에 SQL Server를 자체 설치하고 [SQL VM 리소스 공급자에](virtual-machines-windows-sql-register-with-resource-provider.md)수동으로 등록하고 Azure 하이브리드 혜택을 활성화합니다.

SQL Server의 라이센스 유형은 VM이 프로비전될 때 설정됩니다. 나중에 언제든지 변경할 수 있습니다. 라이센스 모델 간에 전환하면 가동 중지 시간이 발생하지 않으며 VM 또는 SQL Server 서비스가 다시 시작되지 않으며 추가 비용이 추가되지 않으며 즉시 적용됩니다. 실제로 Azure 하이브리드 혜택을 활성화하면 비용이 *절감됩니다.*

## <a name="prerequisites"></a>사전 요구 사항

SQL Server VM의 라이선싱 모델을 변경하면 다음과 같은 요구 사항이 있습니다. 

- [Azure 구독](https://azure.microsoft.com/free/).
- [SQL VM 리소스 공급자에](virtual-machines-windows-sql-register-with-resource-provider.md)등록된 SQL [서버 VM](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision) .
- [소프트웨어 보증은](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default) [Azure 하이브리드 이점을](https://azure.microsoft.com/pricing/hybrid-benefit/)활용하기 위한 요구 사항입니다. 


## <a name="vms-already-registered-with-the-resource-provider"></a>리소스 공급자에 이미 등록된 VM 

# <a name="portal"></a>[포털](#tab/azure-portal)

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

포털에서 직접 라이선스 모델을 수정할 수 있습니다. 

1. Azure [포털을](https://portal.azure.com) 열고 SQL Server VM에 대한 [SQL 가상 시스템 리소스를](virtual-machines-windows-sql-manage-portal.md#access-the-sql-virtual-machines-resource) 엽니다. 
1. **설정에서** **구성을** 선택합니다. 
1. Azure **하이브리드 혜택** 옵션을 선택하고 확인란을 선택하여 소프트웨어 보증이 있는 SQL Server 라이선스가 있는지 확인합니다. 
1. **구성** 페이지 하단에서 **적용을** 선택합니다. 

![포털의 Azure 하이브리드 혜택](media/virtual-machines-windows-sql-ahb/ahb-in-portal.png)


# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI를 사용하여 라이센스 모델을 변경할 수 있습니다.  


**Azure 하이브리드 혜택**

```azurecli-interactive
# Switch your SQL Server VM license from pay-as-you-go to bring-your-own
# example: az sql vm update -n AHBTest -g AHBTest --license-type AHUB

az sql vm update -n <VMName> -g <ResourceGroupName> --license-type AHUB
```

**당신이 가서 지불**: 

```azurecli-interactive
# Switch your SQL Server VM license from bring-your-own to pay-as-you-go
# example: az sql vm update -n AHBTest -g AHBTest --license-type PAYG

az sql vm update -n <VMName> -g <ResourceGroupName> --license-type PAYG
```

**재해 복구(DR)**

```azurecli-interactive
# Switch your SQL Server VM license from bring-your-own to pay-as-you-go
# example: az sql vm update -n AHBTest -g AHBTest --license-type DR

az sql vm update -n <VMName> -g <ResourceGroupName> --license-type DR
```

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

PowerShell을 사용하여 라이센스 모델을 변경할 수 있습니다.

**Azure 하이브리드 혜택**

```powershell-interactive
# Switch your SQL Server VM license from pay-as-you-go to bring-your-own
Update-AzSqlVM -ResourceGroupName <resource_group_name> -Name <VM_name> -LicenseType AHUB
```

**당신이 가서 지불**

```powershell-interactive
# Switch your SQL Server VM license from bring-your-own to pay-as-you-go
Update-AzSqlVM -ResourceGroupName <resource_group_name> -Name <VM_name> -LicenseType PAYG
```

**재해 복구** 

```powershell-interactive
# Switch your SQL Server VM license from bring-your-own to pay-as-you-go
Update-AzSqlVM -ResourceGroupName <resource_group_name> -Name <VM_name> -LicenseType DR
```

---

## <a name="vms-not-registered-with-the-resource-provider"></a>리소스 공급자에 등록되지 않은 VM

종량제 Azure 마켓플레이스 이미지에서 SQL Server VM을 프로비전한 경우 SQL Server 라이선스 유형은 종량제입니다. Azure Marketplace에서 사용자 라이선스 가져오기 이미지를 사용하여 SQL Server VM을 프로비전한 경우 라이선스 유형은 AHUB가 됩니다. 기본값(종량제) 또는 사용자 소유라이선스Azure 마켓플레이스 이미지가져오기로 프로비전된 모든 SQL Server VM은 SQL VM 리소스 공급자에 자동으로 등록되므로 [라이센스 유형을](#vms-already-registered-with-the-resource-provider)변경할 수 있습니다.

Azure 하이브리드 혜택을 통해 Azure VM에 SQL Server를 자체 설치할 수 있습니다. 이러한 [VM을 SQL VM 리소스 공급자에 등록하려면](virtual-machines-windows-sql-register-with-resource-provider.md) SQL Server 라이선스를 Azure 하이브리드 혜택으로 설정하여 Microsoft 제품 약관에 따라 Azure 하이브리드 혜택 사용량을 나타내야 합니다.

SQL Server VM이 SQL VM 리소스 공급자에 등록된 경우에만 SQL Server VM의 라이선스 유형을 종량제 또는 Azure 하이브리드 혜택으로 변경할 수 있습니다.

## <a name="remarks"></a>설명

- CSP(Azure 클라우드 솔루션 공급자) 고객은 먼저 종량제 VM을 배포한 다음 활성 소프트웨어 보증이 있는 경우 사용자 고유의 라이선스로 변환하여 Azure 하이브리드 혜택을 사용할 수 있습니다.
- SQL Server VM 리소스를 삭제하면 이미지의 하드 코딩된 라이센스 설정으로 돌아갑니다. 
- 라이센스 모델을 변경하는 기능은 SQL VM 리소스 공급자의 기능입니다. Azure 포털을 통해 Azure Marketplace 이미지를 배포하는 것은 리소스 공급자와 SQL Server VM을 자동으로 등록합니다. 그러나 SQL Server를 자체 설치하는 고객은 SQL [Server VM을](virtual-machines-windows-sql-register-with-resource-provider.md)수동으로 등록해야 합니다. 
- SQL Server VM을 가용성 집합에 추가하려면 VM을 다시 만들어야 합니다. 따라서 가용성 집합에 추가된 모든 VM은 기본 종량제 라이선스 유형으로 돌아갑니다. Azure 하이브리드 혜택을 다시 활성화해야 합니다. 


## <a name="limitations"></a>제한 사항

라이센스 모델을 변경하는 것은 다음과 같은 것입니다.
   - [소프트웨어 보증을](https://www.microsoft.com/en-us/licensing/licensing-programs/software-assurance-overview)가진 고객만 사용할 수 있습니다.
   - SQL Server의 표준 및 엔터프라이즈 버전에대해서만 지원됩니다. 익스프레스, 웹 및 개발자에 대한 라이선스 변경은 지원되지 않습니다. 
   - Azure 리소스 관리자 모델을 통해 배포된 가상 시스템에 대해서만 지원됩니다. 클래식 모델을 통해 배포된 가상 컴퓨터는 지원되지 않습니다. 
   - 공용 또는 Azure 정부 클라우드에서만 사용할 수 있습니다. 
   - 단일 네트워크 인터페이스(NIC)가 있는 가상 컴퓨터에서만 지원됩니다. 


## <a name="known-errors"></a>알려진 오류

### <a name="the-resource-microsoftsqlvirtualmachinesqlvirtualmachinesresource-group-under-resource-group-resource-group-was-not-found"></a>리소스 그룹\<\<'리소스 그룹>'에서 리소스 'Microsoft.SqlVirtualMachine/SqlVirtualMachine/리소스 그룹>'을 찾을 수 없습니다.

이 오류는 SQL VM 리소스 공급자에 등록되지 않은 SQL Server VM의 라이센스 모델을 변경하려고 할 때 발생합니다.

`The Resource 'Microsoft.SqlVirtualMachine/SqlVirtualMachines/\<resource-group>' under resource group '\<resource-group>' was not found. The property 'sqlServerLicenseType' cannot be found on this object. Verify that the property exists and can be set.`

리소스 공급자에 구독을 등록한 다음 SQL [Server VM을 리소스 공급자에 등록해야](virtual-machines-windows-sql-register-with-resource-provider.md)합니다. 


### <a name="the-virtual-machine-vmname-has-more-than-one-nic-associated"></a>가상 컴퓨터\<'vmname'에는\>두 개 이상의 NIC가 연결되어 있습니다.

이 오류는 NIC가 두 개 이상있는 가상 컴퓨터에서 발생합니다. 라이선스 모델을 변경하기 전에 NIC 중 하나를 제거합니다. 라이선스 모델을 변경한 후 NIC를 VM에 다시 추가할 수 있지만 자동 백업 및 패치와 같은 Azure 포털의 작업은 더 이상 지원되지 않습니다. 


## <a name="next-steps"></a>다음 단계

자세한 내용은 다음 문서를 참조하세요. 

* [Windows VM의 SQL 서버 개요](virtual-machines-windows-sql-server-iaas-overview.md)
* [Windows VM의 SQL 서버에 대한 자주 묻는 질문](virtual-machines-windows-sql-server-iaas-faq.md)
* [Windows VM의 SQL 서버에 대한 가격 지침](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Windows VM에서 SQL 서버에 대한 릴리스 정보](virtual-machines-windows-sql-server-iaas-release-notes.md)


