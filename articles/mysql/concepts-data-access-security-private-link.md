---
title: Private Link - Azure Database for MySQL
description: Azure Database for MySQL에 대한 Private Link의 작동 방식에 관해 알아봅니다.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: 8a41832fdcc53820a9f7fe6436ec608b69f84126
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/30/2021
ms.locfileid: "122642207"
---
# <a name="private-link-for-azure-database-for-mysql"></a>Azure Database for MySQL에 대한 Private Link

[!INCLUDE[applies-to-mysql-single-server](includes/applies-to-mysql-single-server.md)]

Private Link를 사용하면 프라이빗 엔드포인트를 통해 Azure의 다양한 PaaS 서비스에 연결할 수 있습니다. Azure Private Link는 기본적으로 개인 VNet(Virtual Network) 내에 Azure 서비스를 제공합니다. PaaS 리소스는 VNet의 다른 리소스와 마찬가지로 개인 IP 주소를 사용하여 액세스할 수 있습니다.

Private Link 기능을 지원하는 PaaS 서비스의 목록을 보려면 Private Link [설명서](../private-link/index.yml)를 검토하세요. 프라이빗 엔드포인트는 특정 [VNet](../virtual-network/virtual-networks-overview.md) 및 서브넷 내의 개인 IP 주소입니다.

> [!NOTE]
> 프라이빗 링크 기능은 범용 또는 메모리 최적화 가격 책정 계층의 Azure Database for MySQL 서버에만 사용할 수 있습니다. 데이터베이스 서버가 이러한 가격 책정 계층 중 하나에 포함되어 있는지 확인하세요.

## <a name="data-exfiltration-prevention"></a>데이터 반출 방지

Azure Database for MySQL의 데이터 반출은 데이터베이스 관리자와 같은 권한 있는 사용자가 한 시스템에서 데이터를 추출하여 조직 외부의 다른 위치 또는 시스템으로 이동할 수 있는 경우입니다. 예를 들어 사용자는 데이터를 타사 소유의 스토리지 계정으로 이동합니다.

미국 서부에 프로비저닝된 Azure Database for MySQL 서버에 연결하는 Azure VM(가상 머신) 내에서 MySQL Workbench를 실행하는 사용자의 시나리오를 가정합니다. 아래 예에서는 네트워크 액세스 제어를 사용하여 Azure Database for MySQL에서 퍼블릭 엔드포인트를 통해 액세스를 제한하는 방법을 보여 줍니다.

* ‘Azure 서비스 허용’을 끄기로 설정하여 퍼블릭 엔드포인트를 통해 Azure Database for MySQL에 대한 모든 Azure 서비스 트래픽을 사용하지 않도록 설정합니다. [방화벽 규칙](./concepts-firewall-rules.md) 또는 [가상 네트워크 서비스 엔드포인트](./concepts-data-access-and-security-vnet.md)를 통해 서버에 액세스할 수 있는 IP 주소 또는 범위가 없어야 합니다.

* VM의 개인 IP 주소를 사용하는 Azure Database for MySQL로의 트래픽만 허용합니다. 자세한 내용은 [서비스 엔드포인트](concepts-data-access-and-security-vnet.md) 및 [VNet 방화벽 규칙](howto-manage-vnet-using-portal.md) 문서를 참조하세요.

* Azure VM에서 다음과 같이 NSG(네트워크 보안 그룹) 및 서비스 태그를 사용하여 나가는 연결의 범위를 좁힙니다.

    * ‘서비스 태그 = SQL.WestUs’에 대한 트래픽을 허용하는 NSG 규칙 지정 - 미국 서부에서는 Azure Database for MySQL에만 연결 허용
    * ‘서비스 태그 = SQL’에 대한 트래픽을 거부하는 NSG 규칙(우선 순위가 높음) 지정 - 모든 지역에서 Azure Database for MySQL에 대한 연결 거부</br></br>

이 설정이 완료되면 Azure VM에서 미국 서부 지역의 Azure Database for MySQL에만 연결할 수 있습니다. 그러나 연결은 단일 Azure Database for MySQL로 제한되지 않습니다. VM은 구독에 속하지 않은 데이터베이스를 포함하여 미국 서부 지역의 모든 Azure Database for MySQL에 계속 연결할 수 있습니다. 위의 시나리오에서 데이터 반출의 범위를 특정 지역으로 좁혔지만 완전히 제거하지는 않았습니다.</br>

프라이빗 링크를 사용하면 이제 NSG와 같은 네트워크 액세스 제어를 설정하여 프라이빗 엔드포인트에 대한 액세스를 제한할 수 있습니다. 그러면 개별 Azure PaaS 리소스가 특정 프라이빗 엔드포인트에 매핑됩니다. 악의적인 참가자는 매핑된 PaaS 리소스(예: Azure Database for MySQL)에만 액세스할 수 있으며 다른 리소스에는 액세스할 수 없습니다.

## <a name="on-premises-connectivity-over-private-peering"></a>프라이빗 피어링을 통한 온-프레미스 연결

고객이 온-프레미스 머신에서 퍼블릭 엔드포인트에 연결하는 경우 서버 수준 방화벽 규칙을 사용하여 IP 주소를 IP 기반 방화벽에 추가해야 합니다. 이 모델은 개발 또는 테스트 워크로드를 위해 개별 머신에 액세스할 수 있도록 하는 데 효과적이지만 프로덕션 환경에서는 관리하기가 어렵습니다.

프라이빗 링크를 사용하면 [기본 Route](https://azure.microsoft.com/services/expressroute/)(ER), 프라이빗 피어링 또는 [VPN 터널링](../vpn-gateway/index.yml)을 사용하여 프라이빗 엔드포인트에 대한 프레미스 간 액세스를 사용하도록 설정할 수 있습니다. 이후에는 퍼블릭 엔드포인트를 통한 모든 액세스를 사용하지 않도록 설정할 수 있고 IP 기반 방화벽은 사용할 수 없습니다.

> [!NOTE]
> Azure Database for MySQL 및 VNet 서브넷이 서로 다른 구독에 있는 경우도 있습니다. 이러한 경우에는 다음과 같은 구성을 확인해야 합니다.
> - **Microsoft.DBforMySQL** 리소스 공급자를 두 구독 모두에 등록해야 합니다. 자세한 내용은 [resource-manager-registration][resource-manager-portal]을 참조하세요.

## <a name="configure-private-link-for-azure-database-for-mysql"></a>Azure Database for MySQL에 대한 Private Link 구성

### <a name="creation-process"></a>만들기 프로세스

프라이빗 링크를 사용하도록 설정하려면 프라이빗 엔드포인트가 필요합니다. 이렇게 하려면 다음 방법 가이드를 사용합니다.

* [Azure Portal](./howto-configure-privatelink-portal.md)
* [CLI](./howto-configure-privatelink-cli.md)

### <a name="approval-process"></a>승인 프로세스
네트워크 관리자가 PE(프라이빗 엔드포인트)를 만들면 MySQL 관리자가 Azure Database for MySQL에 대한 PEC(프라이빗 엔드포인트 연결)를 관리할 수 있습니다. 네트워크 관리자와 DBA 간의 이러한 업무 분리는 Azure Database for MySQL 연결을 관리하는 데 유용합니다. 

* Azure Portal에서 Azure Database for MySQL 서버 리소스로 이동합니다. 
    * 왼쪽 창에서 프라이빗 엔드포인트 연결 선택
    * 모든 프라이빗 엔드포인트 연결(PEC)의 목록 표시
    * 해당 프라이빗 엔드포인트(PE) 만들어짐

:::image type="content" source="media/concepts-data-access-and-security-private-link/select-private-link-portal.png" alt-text="프라이빗 엔드포인트 포털 선택":::

* 목록에서 개별 PEC를 선택합니다.

:::image type="content" source="media/concepts-data-access-and-security-private-link/select-private-link.png" alt-text="프라이빗 엔드포인트 승인 보류 중 선택":::

* MySQL 서버 관리자는 PEC를 승인 또는 거부하도록 선택하고 필요에 따라 짧은 텍스트 응답을 추가할 수 있습니다.

:::image type="content" source="media/concepts-data-access-and-security-private-link/select-private-link-message.png" alt-text="프라이빗 엔드포인트 메시지 선택":::

* 승인되거나 거부되면 목록에 응답 텍스트와 함께 적절한 상태가 반영됩니다

:::image type="content" source="media/concepts-data-access-and-security-private-link/show-private-link-approved-connection.png" alt-text="프라이빗 엔드포인트 최종 상태 선택":::

## <a name="use-cases-of-private-link-for-azure-database-for-mysql"></a>Azure Database for MySQL에 대한 Private Link의 사용 사례

클라이언트는 동일한 VNet에서 프라이빗 엔드포인트에 연결하거나, 동일한 지역 또는 여러 지역의 [피어링된 VNet](../virtual-network/virtual-network-peering-overview.md)에서 연결하거나, 여러 지역의 [VNet 간 연결](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)을 통해 연결할 수 있습니다. 또한 클라이언트는 ExpressRoute, 프라이빗 피어링 또는 VPN 터널링을 사용하여 온-프레미스에서 연결할 수 있습니다. 다음은 일반적인 사용 사례를 보여 주는 간소화된 다이어그램입니다.

:::image type="content" source="media/concepts-data-access-and-security-private-link/show-private-link-overview.png" alt-text="프라이빗 엔드포인트 개요 선택":::

### <a name="connecting-from-an-azure-vm-in-peered-virtual-network-vnet"></a>피어링된 VNet(가상 네트워크)의 Azure VM에서 연결
피어링된 VNet의 Azure VM에서 Azure Database for MySQL에 대한 연결을 설정하도록 [VNet 피어링](../virtual-network/tutorial-connect-virtual-networks-powershell.md)을 구성합니다.

### <a name="connecting-from-an-azure-vm-in-vnet-to-vnet-environment"></a>VNet 간 환경의 Azure VM에서 연결
다른 지역 또는 구독의 Azure VM에서 Azure Database for MySQL에 대한 연결을 설정하도록 [VNet 간 VPN 게이트웨이 연결](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)을 구성합니다.

### <a name="connecting-from-an-on-premises-environment-over-vpn"></a>VPN을 통해 온-프레미스 환경에서 연결
온-프레미스 환경에서 Azure Database for MySQL에 대한 연결을 설정하려면 다음 옵션 중 하나를 선택하고 구현합니다.

* [지점 및 사이트 간 연결](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)
* [사이트 간 VPN 연결](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)
* [ExpressRoute 회로](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md)

## <a name="private-link-combined-with-firewall-rules"></a>방화벽 규칙과 결합된 Private Link

Private Link를 방화벽 규칙과 함께 사용하면 다음과 같은 상황 및 결과가 발생할 수 있습니다.

* 방화벽 규칙을 구성하지 않으면 기본적으로 트래픽이 Azure Database for MySQL에 액세스할 수 없게 됩니다.

* 공용 트래픽 또는 서비스 엔드포인트를 구성하고 프라이빗 엔드포인트를 만들면 해당 유형의 방화벽 규칙이 다양한 유형의 수신 트래픽 유형에 권한을 부여합니다.

* 퍼블릭 트래픽 또는 서비스 엔드포인트를 구성하지 않고 프라이빗 엔드포인트를 만들면 프라이빗 엔드포인트를 통해서만 Azure Database for MySQL에 액세스할 수 있습니다. 퍼블릭 트래픽 또는 서비스 엔드포인트를 구성하지 않으면 승인된 모든 프라이빗 엔드포인트가 거부되거나 삭제된 후에는 트래픽이 Azure Database for MySQL에 액세스할 수 없게 됩니다.

## <a name="deny-public-access-for-azure-database-for-mysql"></a>Azure Database for MySQL에 대한 퍼블릭 액세스 거부

Azure Database for MySQL에 액세스하기 위해 프라이빗 엔드포인트에만 의존하는 경우 데이터베이스 서버에서 **공용 네트워크 액세스 거부** 구성을 설정하여 모든 퍼블릭 엔드포인트([방화벽 규칙](concepts-firewall-rules.md) 및 [VNet 서비스 엔드포인트](concepts-data-access-and-security-vnet.md)) 설정을 사용하지 않도록 설정할 수 있습니다. 

이 설정이 *예* 로 설정되면 Azure Database for MySQL에 대해 프라이빗 엔드포인트를 통한 연결만 허용됩니다. 이 설정이 *아니요* 로 설정되면 클라이언트는 방화벽 또는 VNet 서비스 엔드포인트 설정에 따라 Azure Database for MySQL에 연결할 수 있습니다. 또한 프라이빗 네트워크 액세스의 값이 설정되면 고객은 기존 ‘방화벽 규칙’ 및 ‘VNet 서비스 엔드포인트 규칙’을 추가 및/또는 업데이트할 수 없습니다.

> [!Note]
> 이 기능은 Azure Database for MySQL - 단일 서버가 범용 및 메모리 최적화 가격 책정 계층을 지원하는 모든 Azure 지역에서 사용할 수 있습니다.
>
> 이 설정은 Azure Database for MySQL에 대한 SSL 및 TLS 구성에 영향을 주지 않습니다.

Azure Portal에서 Azure Database for MySQL에 대한 **공용 네트워크 액세스 거부** 를 설정하는 방법을 알아보려면 [공용 네트워크 액세스 거부를 구성하는 방법](howto-deny-public-network-access.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

Azure Database for MySQL 보안 기능에 관한 자세한 내용은 다음 문서를 참조하세요.

* Azure Database for MySQL에 대한 방화벽을 구성하려면 [방화벽 지원](./concepts-firewall-rules.md)을 참조하세요.

* Azure Database for MySQL에 대한 가상 네트워크 서비스 엔드포인트를 구성하는 방법에 관한 자세한 내용은 [가상 네트워크 액세스 구성](./concepts-data-access-and-security-vnet.md)을 참조하세요.

* Azure Database for MySQL 연결의 개요는 [Azure Database for MySQL 연결 아키텍처](./concepts-connectivity-architecture.md)를 참조하세요.

<!-- Link references, to text, Within this same GitHub repo. -->
[resource-manager-portal]: ../azure-resource-manager/management/resource-providers-and-types.md
