---
title: "PowerShell을 사용하여 영역이 설정된 공용 IP 주소 만들기 | Microsoft Docs"
description: "PowerShell을 사용하여 가용성 영역에서 공용 IP를 만듭니다."
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: 
tags: 
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: 
ms.workload: infrastructure
ms.date: 09/25/2017
ms.author: jdial
ms.custom: 
ms.translationtype: HT
ms.sourcegitcommit: 44e9d992de3126bf989e69e39c343de50d592792
ms.openlocfilehash: 701a8febb3f897fd7f1a81a00adb4635df153d3a
ms.contentlocale: ko-kr
ms.lasthandoff: 09/25/2017

---

# <a name="create-a-public-ip-address-in-an-availability-zone-with-powershell"></a>PowerShell을 사용하여 가용성 영역에서 공용 IP 주소 만들기

Azure 가용성 영역(미리 보기)에서 공용 IP 주소를 배포할 수 있습니다. 가용성 영역은 Azure 지역에서 물리적으로 별도 영역입니다. 방법 배우기:

> * 가용성 영역에서 공용 IP 주소 만들기
> * 가용성 영역에서 만든 관련 리소스 식별
  

Azure 구독이 아직 없는 경우 시작하기 전에 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 을 만듭니다.

이 문서를 사용하려 AzureRM 모듈 4.4.0 버전 이상이 설치되어 있어야 합니다. 버전을 확인하려면 `Get-Module -ListAvailable AzureRM`을 실행합니다. 설치 또는 업그레이드해야 할 경우 [PowerShell 갤러리](https://www.powershellgallery.com/packages/AzureRM)에서 최신 버전의 AzureRM 모듈을 설치합니다.

> [!NOTE]
> 가용성 영역은 미리 보기 상태이며 개발 및 테스트 시나리오에 대한 준비를 마쳤습니다. Azure 리소스, 지역 및 VM 크기 제품군을 선택하는 데 지원을 사용할 수 있습니다. 시작하는 방법에 대한 자세한 내용 및 가용성 영역을 사용해 볼 수 있는 Azure 리소스, 지역 및 VM 크기 제품군은 [가용성 영역 개요](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview)를 참조하세요. 지원을 위해 [StackOverflow](https://stackoverflow.com/questions/tagged/azure-availability-zones)에 연결하거나 [Azure 지원 티켓을 열](../azure-supportability/how-to-create-azure-support-request.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 수 있습니다.

## <a name="log-in-to-azure"></a>Azure에 로그인

`Login-AzureRmAccount` 명령으로 Azure 구독에 로그인하고 화면의 지시를 따릅니다.

```powershell
Login-AzureRmAccount
```
## <a name="create-resource-group"></a>리소스 그룹 만들기

[New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup)을 사용하여 Azure 리소스 그룹을 만듭니다. 리소스 그룹은 Azure 리소스가 배포 및 관리되는 논리적 컨테이너입니다. 이 예제에서는 *westeurope* 지역에 *myResourceGroup*이라는 리소스 그룹을 만듭니다. *westeurope*은 미리 보기에서 가용성 영역을 지원하는 Azure 지역 중 하나입니다.

```powershell
New-AzureRmResourceGroup -Name AzTest -Location westeurope
```

## <a name="create-a-zonal-public-ip-address"></a>영역 공용 IP 주소 만들기

다음 명령을 사용하여 가용성 영역에서 공용 IP 주소를 만듭니다.

```powershell
    New-AzureRmPublicIpAddress `
        -ResourceGroupName myResourceGroup `
        -Name myPublicIp `
        -Location westeurope `
        -AllocationMethod Static `
        -Zone 2
```

> [!NOTE]
> 가상 컴퓨터의 네트워크 인터페이스에 표준 SKU 공용 IP 주소를 할당할 때 [네트워크 보안 그룹](security-overview.md#network-security-groups)을 사용하여 원하는 트래픽을 명시적으로 허용해야 합니다. 네트워크 보안 그룹을 만들어 연결하고 원하는 트래픽을 명시적으로 허용해야 리소스와 통신할 수 있습니다.

## <a name="get-zone-information-about-a-public-ip-address"></a>공용 IP 주소에 대한 영역 정보 가져오기

다음 명령을 사용하여 공용 IP 주소의 영역 정보를 가져옵니다.

```powershell
Get-AzureRmPublicIpAddress ` 
    -ResourceGroup myResourceGroup `
    -Name myPublicIp
```

## <a name="next-steps"></a>다음 단계

- [가용성 영역](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview)에 대해 자세히 알아보기
- [공용 IP 주소](virtual-network-public-ip-address.md?toc=%2fazure%2fvirtual-network%2ftoc.json)에 대해 자세히 알아보기 
