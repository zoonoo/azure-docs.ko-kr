---
title: 개인 링크 - MySQL용 Azure 데이터베이스
description: MySQL용 Azure 데이터베이스에 대해 개인 링크가 작동하는 방식에 대해 알아봅니다.
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: c2cc4986542404281424286882c046dec39f5daf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79371293"
---
# <a name="private-link-for-azure-database-for-mysql"></a>MySQL용 Azure 데이터베이스에 대한 개인 링크

Private Link를 사용하면 프라이빗 엔드포인트를 통해 Azure의 다양한 PaaS 서비스에 연결할 수 있습니다. Azure 개인 링크는 기본적으로 VNet(개인 가상 네트워크) 내에서 Azure 서비스를 제공합니다. PaaS 리소스는 VNet의 다른 리소스와 마찬가지로 개인 IP 주소를 사용하여 액세스할 수 있습니다.

개인 링크 기능을 지원하는 PaaS 서비스에 대한 목록을 보려면 개인 링크 [설명서를 검토하십시오.](https://docs.microsoft.com/azure/private-link/index) 프라이빗 엔드포인트는 특정 [VNet](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) 및 서브넷 내의 개인 IP 주소입니다.

> [!NOTE]
> 이 기능은 MySQL용 Azure 데이터베이스가 범용 및 메모리 최적화가격 책정 계층을 지원하는 모든 Azure 리전에서 사용할 수 있습니다.

## <a name="data-exfiltration-prevention"></a>데이터 반출 방지

MySQL용 Azure 데이터베이스의 데이터 필터링은 데이터베이스 관리자와 같은 권한이 있는 사용자가 한 시스템에서 데이터를 추출하여 조직 외부의 다른 위치 또는 시스템으로 이동할 수 있는 경우입니다. 예를 들어 사용자는 데이터를 타사 소유의 스토리지 계정으로 이동합니다.

미국 서부에서 프로비저닝된 MySQL 서버용 Azure 데이터베이스에 연결하는 Azure 가상 시스템(VM) 내에서 MySQL 워크벤치를 실행하는 사용자가 있는 시나리오를 생각해 보십시오. 아래 예제에서는 네트워크 액세스 제어를 사용하여 MySQL용 Azure Database의 공용 끝점으로 액세스를 제한하는 방법을 보여 주습니다.

* 공용 끝점을 통해 공용 끝점을 통해 모든 Azure 서비스 트래픽을 Azure 데이터베이스로 비활성화하려면 *Azure 서비스 허용을* OFF로 설정합니다. [방화벽 규칙](https://docs.microsoft.com/azure/mysql/concepts-firewall-rules) 또는 [가상 네트워크 서비스 끝점을](https://docs.microsoft.com/azure/mysql/concepts-data-access-and-security-vnet)통해 서버에 액세스할 수 있는 IP 주소 또는 범위가 없는지 확인합니다.

* VM의 개인 IP 주소를 사용하여 MySQL용 Azure 데이터베이스에 대한 트래픽만 허용합니다. 자세한 내용은 [서비스 엔드포인트](concepts-data-access-and-security-vnet.md) 및 [VNet 방화벽 규칙](howto-manage-vnet-using-portal.md) 문서를 참조하세요.

* Azure VM에서 다음과 같이 NSG(네트워크 보안 그룹) 및 서비스 태그를 사용하여 나가는 연결의 범위를 좁힙니다.

    * 서비스 태그 = SQL에 대한 트래픽을 허용하도록 NSG 규칙을 *지정합니다. WestUs* - 미국 서부의 MySQL용 Azure 데이터베이스에만 연결 허용
    * *서비스 태그 = SQL에* 대한 트래픽을 거부하도록 NSG 규칙(우선 순위가 높은)을 지정 - 모든 리전에서 MySQL에 대한 Azure 데이터베이스에 대한 업데이트에 대한 연결을 거부합니다.</br></br>

이 설정이 끝나면 Azure VM은 미국 서부 지역의 MySQL용 Azure 데이터베이스에만 연결할 수 있습니다. 그러나 연결은 MySQL에 대한 단일 Azure 데이터베이스로 제한되지 않습니다. VM은 구독의 일부가 아닌 데이터베이스를 포함하여 미국 서부 지역의 MySQL용 모든 Azure 데이터베이스에 계속 연결할 수 있습니다. 위의 시나리오에서 데이터 반출의 범위를 특정 지역으로 좁혔지만 완전히 제거하지는 않았습니다.</br>

개인 링크를 사용하면 NSG와 같은 네트워크 액세스 제어를 설정하여 개인 끝점에 대한 액세스를 제한할 수 있습니다. 그러면 개별 Azure PaaS 리소스가 특정 프라이빗 엔드포인트에 매핑됩니다. 악의적인 내부자는 매핑된 PaaS 리소스(예: MySQL용 Azure 데이터베이스)와 다른 리소스에만 액세스할 수 있습니다.

## <a name="on-premises-connectivity-over-private-peering"></a>프라이빗 피어링을 통한 온-프레미스 연결

온-프레미스 컴퓨터에서 공용 끝점에 연결할 때 서버 수준 방화벽 규칙을 사용하여 IP 주소를 IP 기반 방화벽에 추가해야 합니다. 이 모델은 개발 또는 테스트 워크로드를 위해 개별 머신에 액세스할 수 있도록 하는 데 효과적이지만 프로덕션 환경에서는 관리하기가 어렵습니다.

프라이빗 링크를 사용하면 [ER(익스프레스 루트),](https://azure.microsoft.com/services/expressroute/) 프라이빗 피어링 또는 [VPN 터널을](https://docs.microsoft.com/azure/vpn-gateway/)사용하여 개인 엔드포인트에 대한 크로스-프레미스 액세스를 활성화할 수 있습니다. 이후에 공용 끝점을 통해 모든 액세스를 비활성화하고 IP 기반 방화벽을 사용하지 않을 수 있습니다.

## <a name="configure-private-link-for-azure-database-for-mysql"></a>MySQL용 Azure 데이터베이스에 대한 개인 링크 구성

### <a name="creation-process"></a>만들기 프로세스

비공개 링크를 사용하려면 비공개 끝점이 필요합니다. 이 작업은 다음 방법 가이드를 사용하여 수행할 수 있습니다.

* [Azure 포털](https://docs.microsoft.com/azure/mysql/howto-configure-privatelink-portal)
* [Cli](https://docs.microsoft.com/azure/mysql/howto-configure-privatelink-cli)

### <a name="approval-process"></a>승인 프로세스
네트워크 관리자가 개인 끝점(PE)을 만들면 MySQL 관리자는 MYSQL용 Azure 데이터베이스에 대한 개인 끝점 연결(PEC)을 관리할 수 있습니다. 네트워크 관리자와 DBA 간의 이러한 업무 분리는 MySQL 연결에 대한 Azure 데이터베이스 관리에 유용합니다. 

* Azure 포털에서 MySQL 서버 리소스에 대한 Azure 데이터베이스로 이동합니다. 
    * 왼쪽 창에서 전용 끝점 연결을 선택합니다.
    * 모든 개인 끝점 연결(NIC) 목록 표시
    * 해당 개인 끝점(PE) 생성

![개인 끝점 포털 선택](media/concepts-data-access-and-security-private-link/select-private-link-portal.png)

* 목록에서 개별 PEC를 선택합니다.

![승인 대기 중인 개인 끝점 선택](media/concepts-data-access-and-security-private-link/select-private-link.png)

* MySQL 서버 관리자는 PEC를 승인 또는 거부하고 선택적으로 짧은 텍스트 응답을 추가할 수 있습니다.

![개인 끝점 메시지 선택](media/concepts-data-access-and-security-private-link/select-private-link-message.png)

* 승인 또는 거부 후 목록에는 응답 텍스트와 함께 적절한 상태가 반영됩니다.

![개인 끝점 최종 상태 선택](media/concepts-data-access-and-security-private-link/show-private-link-approved-connection.png)

## <a name="use-cases-of-private-link-for-azure-database-for-mysql"></a>MySQL에 대 한 Azure 데이터베이스에 대 한 개인 링크의 사용 사례

클라이언트는 동일한 VNet에서 개인 엔드포인트에 연결할 수 있으며, 동일한 리전에서 피어된 VNet 또는 지역 간에 VNet-VNet 연결을 통해 연결할 수 있습니다. 또한 클라이언트는 ExpressRoute, 프라이빗 피어링 또는 VPN 터널링을 사용하여 온-프레미스에서 연결할 수 있습니다. 다음은 일반적인 사용 사례를 보여 주는 간소화된 다이어그램입니다.

![프라이빗 엔드포인트 개요 선택](media/concepts-data-access-and-security-private-link/show-private-link-overview.png)

### <a name="connecting-from-an-azure-vm-in-peered-virtual-network-vnet"></a>피어링된 VNet(가상 네트워크)의 Azure VM에서 연결
[피어링된 VNet의 Azure](https://docs.microsoft.com/azure/virtual-network/tutorial-connect-virtual-networks-powershell) VM에서 MySQL용 Azure 데이터베이스에 대한 연결을 설정하도록 VNet 피어링을 구성합니다.

### <a name="connecting-from-an-azure-vm-in-vnet-to-vnet-environment"></a>VNet 간 환경의 Azure VM에서 연결
[VNet-VNet VPN 게이트웨이 연결을](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal) 구성하여 다른 지역 또는 구독의 Azure VM에서 MySQL용 Azure 데이터베이스에 대한 연결을 설정합니다.

### <a name="connecting-from-an-on-premises-environment-over-vpn"></a>VPN을 통해 온-프레미스 환경에서 연결
온-프레미스 환경에서 MySQL용 Azure 데이터베이스에 대한 연결을 설정하려면 다음 옵션 중 하나를 선택하고 구현합니다.

* [지점 간 연결](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps)
* [사이트 간 VPN 연결](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell)
* [익스프레스루트 서킷](https://docs.microsoft.com/azure/expressroute/expressroute-howto-linkvnet-portal-resource-manager)

## <a name="private-link-combined-with-firewall-rules"></a>방화벽 규칙과 결합된 비공개 링크

방화벽 규칙과 함께 비공개 링크를 사용할 때 다음과 같은 상황과 결과가 발생할 수 있습니다.

* 방화벽 규칙을 구성하지 않으면 기본적으로 트래픽이 MySQL용 Azure 데이터베이스에 액세스할 수 없습니다.

* 공용 트래픽 또는 서비스 끝점을 구성하고 개인 끝점을 만드는 경우 다른 유형의 들어오는 트래픽은 해당 유형의 방화벽 규칙에 의해 승인됩니다.

* 공용 트래픽 또는 서비스 끝점을 구성하지 않고 개인 끝점을 만드는 경우 MySQL용 Azure 데이터베이스는 개인 끝점을 통해서만 액세스할 수 있습니다. 공용 트래픽 또는 서비스 끝점을 구성하지 않으면 승인된 모든 개인 끝점이 거부되거나 삭제된 후에는 트래픽이 MySQL용 Azure 데이터베이스에 액세스할 수 없습니다.

## <a name="deny-public-access-for-azure-database-for-mysql"></a>MySQL에 대 한 Azure 데이터베이스에 대 한 공용 액세스 거부

MySQL에 대한 Azure 데이터베이스에 액세스하기 위해 개인 끝점에만 의존하려는 경우 데이터베이스 서버에서 공용 네트워크 액세스 거부 구성을 설정하여 모든 공용 끝점(예: [방화벽 규칙](concepts-firewall-rules.md) 및 [VNet 서비스 끝점)을](concepts-data-access-and-security-vnet.md) **설정하지** 않도록 설정할 수 있습니다. 

이 설정이 *YES로*설정된 경우 개인 끝점을 통한 연결만 MySQL용 Azure 데이터베이스에 허용됩니다. 이 설정이 *NO로*설정되면 클라이언트는 방화벽 또는 VNet 서비스 끝점 설정에 따라 MySQL용 Azure 데이터베이스에 연결할 수 있습니다. 또한 개인 네트워크 액세스 의 값이 설정되면 기존 방화벽 및 VNet 서비스 끝점 규칙을 추가 및/또는 업데이트할 수 없습니다.

> [!Note]
> 이 기능은 PostgreSQL - 단일 서버가 범용 및 메모리 최적화 가격 책정 계층을 지원하는 모든 Azure 지역에서 사용할 수 있습니다.
>
> 이 설정은 MySQL용 Azure 데이터베이스에 대한 SSL 및 TLS 구성에 영향을 주지 않습니다.

Azure 포털에서 MySQL에 대한 Azure 데이터베이스에 대한 **공용 네트워크 액세스 거부를** 설정하는 방법을 알아보려면 [공용 네트워크 액세스 거부를 구성하는 방법을](howto-deny-public-network-access.md)참조하십시오.

## <a name="next-steps"></a>다음 단계

MySQL 보안 기능에 대한 Azure 데이터베이스에 대해 자세히 알아보려면 다음 문서를 참조하세요.

* MySQL용 Azure 데이터베이스에 대한 방화벽을 구성하려면 [방화벽 지원을](https://docs.microsoft.com/azure/mysql/concepts-firewall-rules)참조하십시오.

* MySQL용 Azure 데이터베이스에 대한 가상 네트워크 서비스 끝점을 구성하는 방법을 알아보려면 [가상 네트워크에서 액세스 구성 을](https://docs.microsoft.com/azure/mysql/concepts-data-access-and-security-vnet)참조하십시오.

* MySQL 연결에 대한 Azure 데이터베이스에 대한 개요는 [MySQL 연결 아키텍처용 Azure 데이터베이스를](https://docs.microsoft.com/azure/mysql/concepts-connectivity-architecture) 참조하십시오.
