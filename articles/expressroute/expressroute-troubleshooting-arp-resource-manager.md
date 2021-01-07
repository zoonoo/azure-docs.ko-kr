---
title: 'Azure Express 경로: ARP 테이블-문제 해결'
description: 이 페이지에서는 Express 경로 회로에 대 한 ARP (주소 확인 프로토콜) 테이블을 가져오는 방법에 대 한 지침을 제공 합니다.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: troubleshooting
ms.date: 12/15/2020
ms.author: duau
ms.custom: seodec18
ms.openlocfilehash: 7d8ae2c58979c66ebbbab366d172179bdeee4253
ms.sourcegitcommit: 77ab078e255034bd1a8db499eec6fe9b093a8e4f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/16/2020
ms.locfileid: "97561582"
---
# <a name="getting-arp-tables-in-the-resource-manager-deployment-model"></a>Resource Manager 배포 모델에서 ARP 테이블 가져오기
> [!div class="op_single_selector"]
> * [PowerShell - Resource Manager](expressroute-troubleshooting-arp-resource-manager.md)
> * [PowerShell - 클래식](expressroute-troubleshooting-arp-classic.md)
> 
> 

이 문서는 ExpressRoute 회로의 ARP 테이블을 배우는 단계를 안내합니다.

> [!IMPORTANT]
> 이 문서는 간단한 문제를 진단하고 수정하는 데 사용됩니다. 이 문서는 Microsoft 기술 지원 서비스를 대체하지 않습니다. 아래 설명한 지침으로 문제를 해결할 수 없다면 [Microsoft 지원](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) 으로 지원 티켓을 열어야 합니다.
> 
> 

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

## <a name="address-resolution-protocol-arp-and-arp-tables"></a>주소 확인 프로토콜(ARP) 및 ARP 테이블
주소 확인 프로토콜(ARP)은 [RFC 826](https://tools.ietf.org/html/rfc826)에 정의된 계층 2 프로토콜입니다. ARP는 IP 주소로 이더넷 주소(MAC 주소)를 매핑하는 데 사용합니다.

ARP 테이블은 각 피어 링 유형에 대 한 기본 및 보조 인터페이스에 대해 다음과 같은 정보를 제공 합니다.

1. 온-프레미스 라우터 인터페이스 IP 주소를 MAC 주소에 매핑
2. ExpressRoute 라우터 인터페이스 IP 주소를 MAC 주소에 매핑
3. 매핑 사용 기간

ARP 테이블은 계층 2 구성의 유효성을 검사하고 기본적인 계층 2 연결 문제를 해결하는 데 도움을 줍니다. 

ARP 테이블의 예: 

```output
Age InterfaceProperty IpAddress  MacAddress    
--- ----------------- ---------  ----------    
 10 On-Prem           10.0.0.1   ffff.eeee.dddd
  0 Microsoft         10.0.0.2   aaaa.bbbb.cccc
```


다음 섹션은 ExpressRoute 에지 라우터가 나타내는 ARP 테이블을 보는 방법을 알려줍니다. 

## <a name="prerequisites-for-learning-arp-tables"></a>ARP 테이블을 학습하기 위한 필수 조건
더 진행 하기 전에 아래 정보가 true 인지 확인 합니다.

* 하나 이상의 피어 링으로 구성 된 유효한 Express 경로 회로입니다. 연결 공급자가 완벽히 구성한 회로여야 합니다. 사용자 또는 연결 공급자는이 회로에 대해 최소한 Azure 개인, Azure 공용 또는 Microsoft 피어 링을 구성 해야 합니다.
* 피어 링을 구성 하는 데 사용 되는 IP 주소 범위입니다. Ip 주소가 인터페이스에 매핑되는 방식을 이해 하려면 [express 경로 라우팅 요구 사항 페이지](expressroute-routing.md) 에서 ip 주소 할당 예제를 검토 합니다. [ExpressRoute 피어링 구성 페이지](expressroute-howto-routing-arm.md)를 검토하면 피어링 구성에 관한 정보를 얻을 수 있습니다.
* 네트워킹 팀/연결 공급자가 제공한 해당 IP 주소에서 사용하는 인터페이스 MAC 주소 정보.
* 최신 Azure용 PowerShell 모듈(1.50 버전 이상)이 있어야 합니다.

> [!NOTE]
> 서비스 공급자가 계층 3을 제공 하 고 아래 포털/출력에서 ARP 테이블이 비어 있는 경우 포털의 새로 고침 단추를 사용 하 여 회로 구성을 새로 고칩니다. 이 작업은 회로에 올바른 라우팅 구성을 적용합니다. 
>
>

## <a name="getting-the-arp-tables-for-your-expressroute-circuit"></a>ExpressRoute 회로의 ARP 테이블 가져오기
이 섹션은 PowerShell을 사용하여 피어링당 ARP 테이블을 보는 방법을 설명합니다. 사용자 또는 연결 공급자는 더 진행하기 전에 피어링을 구성해야 합니다. 각 회로는 두 가지 경로(기본 및 보조)가 있습니다. 각 경로의 ARP 테이블을 독립적으로 확인할 수 있습니다.

### <a name="arp-tables-for-azure-private-peering"></a>Azure 프라이빗 피어링용 ARP 테이블
다음 cmdlet은 Azure 프라이빗 피어링용 ARP 테이블을 제공합니다.

```azurepowershell
# Required Variables
$RG = "<Your Resource Group Name Here>"
$Name = "<Your ExpressRoute Circuit Name Here>"

# ARP table for Azure private peering - Primary path
Get-AzExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType AzurePrivatePeering -DevicePath Primary

# ARP table for Azure private peering - Secondary path
Get-AzExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType AzurePrivatePeering -DevicePath Secondary 
```

아래의 샘플 출력은 그 경로 중 하나를 보여줍니다.

```output
Age InterfaceProperty IpAddress  MacAddress    
--- ----------------- ---------  ----------    
 10 On-Prem           10.0.0.1   ffff.eeee.dddd
  0 Microsoft         10.0.0.2   aaaa.bbbb.cccc
```


### <a name="arp-tables-for-azure-public-peering"></a>Azure 공용 피어링용 ARP 테이블
다음 cmdlet은 Azure 공용 피어링용 ARP 테이블을 제공합니다.

```azurepowershell
# Required Variables
$RG = "<Your Resource Group Name Here>"
$Name = "<Your ExpressRoute Circuit Name Here>"

# ARP table for Azure public peering - Primary path
Get-AzExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType AzurePublicPeering -DevicePath Primary

# ARP table for Azure public peering - Secondary path
Get-AzExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType AzurePublicPeering -DevicePath Secondary 
```


아래의 샘플 출력은 그 경로 중 하나를 보여줍니다.

```output
Age InterfaceProperty IpAddress  MacAddress    
--- ----------------- ---------  ----------    
 10 On-Prem           64.0.0.1   ffff.eeee.dddd
  0 Microsoft         64.0.0.2   aaaa.bbbb.cccc
```


### <a name="arp-tables-for-microsoft-peering"></a>Microsoft 피어링용 ARP 테이블
다음 cmdlet은 Microsoft 피어링용 ARP 테이블을 제공합니다.

```azurepowershell
# Required Variables
$RG = "<Your Resource Group Name Here>"
$Name = "<Your ExpressRoute Circuit Name Here>"

# ARP table for Microsoft peering - Primary path
Get-AzExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType MicrosoftPeering -DevicePath Primary

# ARP table for Microsoft peering - Secondary path
Get-AzExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType MicrosoftPeering -DevicePath Secondary 
```


아래의 샘플 출력은 그 경로 중 하나를 보여줍니다.

```output
Age InterfaceProperty IpAddress  MacAddress    
--- ----------------- ---------  ----------    
 10 On-Prem           65.0.0.1   ffff.eeee.dddd
  0 Microsoft         65.0.0.2   aaaa.bbbb.cccc
```


## <a name="how-to-use-this-information"></a>이 정보를 사용하는 방법
피어링의 ARP 테이블을 사용하여 계층 2 구성과 연결의 유효성을 검사할 수 있습니다. 이 섹션은 다양한 시나리오에서 ARP 테이블이 표시되는 모습의 개요를 보여줍니다.

### <a name="arp-table-when-a-circuit-is-in-operational-state-expected-state"></a>회로가 작동 상태일 때 ARP 테이블(예상 상태)
* ARP 테이블은 유효한 IP 주소 및 MAC 주소를 사용 하 여 온-프레미스 측에 대 한 항목을 포함 합니다. Microsoft side에도 동일 하 게 표시 됩니다. 
* 온-프레미스 IP 주소의 마지막 옥텟은 항상 홀수입니다.
* Microsoft IP 주소의 마지막 옥텟은 항상 짝수입니다.
* 세 피어 링 (기본/보조) 모두에 대해 동일한 MAC 주소가 Microsoft 쪽에 표시 됩니다. 

```output
Age InterfaceProperty IpAddress  MacAddress    
--- ----------------- ---------  ----------    
 10 On-Prem           65.0.0.1   ffff.eeee.dddd
  0 Microsoft         65.0.0.2   aaaa.bbbb.cccc
```

### <a name="arp-table-when-on-premises--connectivity-provider-side-has-problems"></a>온-프레미스/연결 공급자 측에 문제가 있을 때 ARP 테이블
온-프레미스 또는 연결 공급자에 대 한 문제가 발생 하는 경우 ARP 테이블은 두 가지 중 하나를 표시 합니다. 온-프레미스 MAC 주소가 불완전 하 게 표시 되거나 ARP 테이블의 Microsoft 항목만 표시 됩니다.
  
```output
Age InterfaceProperty IpAddress  MacAddress    
--- ----------------- ---------  ----------   
  0 On-Prem           65.0.0.1   Incomplete
  0 Microsoft         65.0.0.2   aaaa.bbbb.cccc
```
또는
   
```output
Age InterfaceProperty IpAddress  MacAddress    
--- ----------------- ---------  ----------    
  0 Microsoft         65.0.0.2   aaaa.bbbb.cccc
```  

> [!NOTE]
> 이 문제를 디버그하려면 연결 공급자로 지원 요청을 엽니다. ARP 테이블에 MAC 주소에 매핑된 인터페이스의 IP 주소가 없으면 다음 정보를 검토합니다.
> 
> 1. MSEE-PR과 MSEE 사이의 링크에 할당된 /30 서브넷의 첫 번째 IP 주소가 MSEE-PR의 인터페이스에서 사용되는 경우 Azure에서 항상 두 번째 IP 주소를 MSEE에 사용합니다.
> 2. 고객(C-Tag) 및 서비스(S-Tag) VLAN 태그가 MSEE-PR 및 MSEE 쌍 모두와 일치하는지 확인합니다.
> 

### <a name="arp-table-when-microsoft-side-has-problems"></a>Microsoft 측에 문제가 있을 때 ARP 테이블
* Microsoft 측에 문제가 있는 경우 피어 링에 대해 표시 된 ARP 테이블이 표시 되지 않습니다. 
* [Microsoft 지원](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)으로 지원 티켓을 엽니다. 계층 2 연결 문제가 있다고 표시합니다. 

## <a name="next-steps"></a>다음 단계
* Express 경로 회로에 대 한 계층 3 구성의 유효성을 검사 합니다.
  * BGP 세션의 상태를 확인 하려면 경로 요약을 가져옵니다.
  * 경로 테이블을 가져와 Express 경로에서 보급 되는 접두사를 확인 합니다.
* 바이트 in/out을 검토 하 여 데이터 전송의 유효성을 검사 합니다.
* 여전히 문제가 발생 하는 경우 [Microsoft 지원](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) 으로 지원 티켓을 엽니다.

