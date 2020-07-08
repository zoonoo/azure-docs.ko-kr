---
title: Azure의 개인 IP 주소
titlesuffix: Azure Virtual Network
description: Azure의 개인 IP 주소에 대해 알아봅니다.
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/28/2020
ms.author: allensu
ms.openlocfilehash: 13688b73c0cd73e13e407f1b75beb8dda61a754f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84172258"
---
# <a name="private-ip-addresses"></a>개인 IP 주소
개인 Ip는 Azure의 리소스 간 통신을 허용 합니다. 

리소스는 다음과 같을 수 있습니다.
* Azure 서비스:
    * 가상 머신 네트워크 인터페이스
    * 내부 부하 분산 장치(ILB)
    * 애플리케이션 게이트웨이
* [가상 네트워크](virtual-networks-overview.md).
* VPN gateway 또는 Express 경로 회로를 통해 온-프레미스 네트워크

개인 Ip를 사용 하면 공용 IP 주소를 사용 하지 않고 이러한 리소스와 통신할 수 있습니다.

## <a name="allocation-method"></a>할당 방법

Azure는 리소스가 인 가상 네트워크 서브넷의 주소 범위에서 리소스에 개인 IP 주소를 할당 합니다.

Azure는 각 서브넷 주소 범위에서 처음 4 개 주소를 예약 합니다. 주소를 리소스에 할당할 수 없습니다. 예를 들어 서브넷의 주소 범위가 10.0.0.0/16 인 경우 주소 10.0.0.0-10.0.0.3 및 10.0.255.255을 사용할 수 없습니다. 서브넷 주소 범위 내의 IP 주소는 한 번에 하나의 리소스에만 할당할 수 있습니다. 

개인 IP 주소가 제공 되는 방법에는 다음 두 가지가 있습니다.

- **동적**: Azure는 사용 가능한 다음 할당되지 않은 또는 예약되지 않은 IP 주소를 서브넷의 주소 범위에 할당합니다. 예를 들어 Azure는 주소 10.0.0.4-10.0.0.9가 다른 리소스에 이미 할당된 경우 10.0.0.10를 새 리소스에 할당합니다. 

    동적이 기본 할당 방법입니다. 할당 되 면 네트워크 인터페이스가 다음과 같은 경우 동적 IP 주소가 해제 됩니다.
    
    * 삭제됨
    * 동일한 가상 네트워크 내의 다른 서브넷에 다시 할당 되었습니다.
    * 할당 방법이 정적으로 변경 되 고 다른 IP 주소가 지정 됩니다. 
    
    기본적으로 할당 메서드를 동적에서 고정으로 변경하는 경우 Azure는 이전에 동적으로 할당된 주소를 고정 주소로 할당합니다.

- **고정적**: Azure는 할당되지 않은 또는 예약되지 않은 IP 주소를 선택하고 서브넷의 주소 범위에 할당합니다. 

    예를 들어 서브넷의 주소 범위는 10.0.0.0/16 이며 주소 10.0.0.4-10.0.0.9가는 다른 리소스에 할당 됩니다. 10.0.0.10-10.0.255.254 간에 주소를 할당할 수 있습니다. 고정 주소는 네트워크 인터페이스가 삭제되는 경우에만 해제됩니다. 
    
    할당 방법이 변경 되 면 Azure에서 고정 IP를 동적 IP로 할당 합니다. 주소는 서브넷에서 사용할 수 있는 다음이 아닌 경우에도 마찬가지입니다. 네트워크 인터페이스가 다른 서브넷에 할당 되 면 주소가 변경 됩니다.
    
    네트워크 인터페이스를 다른 서브넷에 할당 하려면 할당 방법을 정적에서 동적으로 변경 합니다. 네트워크 인터페이스를 다른 서브넷에 할당 한 다음 할당 메서드를 다시 정적으로 변경 합니다. 새 서브넷의 주소 범위에서 IP 주소를 할당 합니다.
    
## <a name="virtual-machines"></a>가상 머신

하나 이상의 개인 IP 주소가 하나 이상의 **네트워크 인터페이스**에 할당 됩니다. 네트워크 인터페이스는 [Windows](../virtual-machines/windows/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 또는 [Linux](../virtual-machines/linux/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 가상 머신에 할당 됩니다. 각 개인 IP 주소에 대한 할당 방법을 동적 또는 고정으로 지정할 수 있습니다.

### <a name="internal-dns-hostname-resolution-for-virtual-machines"></a>내부 DNS 호스트 이름 확인(가상 머신)

Azure 가상 머신은 기본적으로 [azure 관리 DNS 서버](virtual-networks-name-resolution-for-vms-and-role-instances.md#azure-provided-name-resolution) 를 사용 하 여 구성 됩니다. 사용자 지정 DNS 서버를 명시적으로 구성할 수 있습니다. 이러한 DNS 서버는 동일한 가상 네트워크 내에 있는 가상 컴퓨터에 대 한 내부 이름 확인을 제공 합니다.

가상 컴퓨터의 개인 IP 주소에 대 한 호스트 이름에 대 한 매핑이 Azure에서 관리 되는 DNS 서버에 추가 됩니다. 

호스트 이름은 VM이 다음과 같은 경우 주 네트워크 인터페이스의 기본 IP에 매핑됩니다.

* 여러 네트워크 인터페이스
* 여러 IP 주소
* 모두

Azure 관리 DNS로 구성 된 Vm은 동일한 가상 네트워크 내에서 호스트 이름을 확인 합니다. 사용자 지정 DNS 서버를 사용 하 여 연결 된 가상 네트워크에서 Vm의 호스트 이름을 확인 합니다.

## <a name="internal-load-balancers-ilb--application-gateways"></a>ILB(내부 부하 분산 장치) 및 애플리케이션 게이트웨이

의 **프런트 엔드** 구성에 개인 IP 주소를 할당할 수 있습니다.

* Azure ilb ( [내부 부하 분산 장치](../load-balancer/load-balancer-internal-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) )
* [Azure Application Gateway](../application-gateway/application-gateway-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 

이 개인 IP 주소는 내부 끝점으로 사용 됩니다. 내부 끝점은 해당 가상 네트워크 내의 리소스와 연결 된 원격 네트워크에만 액세스할 수 있습니다. 동적 또는 고정 IP를 할당할 수 있습니다.

## <a name="at-a-glance"></a>요약
다음 표에서는 개인 IP를 리소스에 연결할 수 있는 속성을 보여 줍니다. 

사용할 수 있는 할당 방법도 다음과 같이 표시 됩니다.

* 동적
* 정적

| 최상위 리소스 | IP 주소 연결 | 동적 | 정적 |
| --- | --- | --- | --- |
| 가상 머신 |Linux |예 |예 |
| 부하 분산 장치 |프런트 엔드 구성 |예 |예 |
| 프런트 엔드 |프런트 엔드 구성 |예 |예 |

## <a name="limits"></a>제한
IP 주소 지정에 대 한 제한은 Azure에서 [네트워킹에 대 한 제한](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits) 의 전체 집합에 있습니다. 제한은 지역별, 구독별로 적용됩니다. 비즈니스 요구에 따라 기본 제한을 최대 제한까지 늘리려면 [지원 담당자에 게 문의 하세요](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade) .

## <a name="next-steps"></a>다음 단계
[Azure의 공용 IP 주소](public-ip-addresses.md) 에 대 한 자세한 정보
* [Azure Portal을 사용하여 고정 개인 IP 주소를 사용하는 VM 배포](virtual-networks-static-private-ip-arm-pportal.md)
