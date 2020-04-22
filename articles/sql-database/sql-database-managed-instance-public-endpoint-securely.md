---
title: 안전한 관리형 인스턴스 공용 끝점
description: 관리되는 인스턴스를 사용하여 Azure에서 공용 끝점을 안전하게 사용
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: vanto, carlrab
ms.date: 05/08/2019
ms.openlocfilehash: f11c19ba33ee2fbae0fef265371bedad2fe29cb7
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81684947"
---
# <a name="use-an-azure-sql-database-managed-instance-securely-with-public-endpoints"></a>공용 끝점을 사용하여 Azure SQL Database 관리 인스턴스를 안전하게 사용

Azure SQL Database 관리형 인스턴스는 [공용 끝점을](../virtual-network/virtual-network-service-endpoints-overview.md)통해 사용자 연결을 제공할 수 있습니다. 이 문서에서는 이 구성을 보다 안전하게 만드는 방법에 대해 설명합니다.

## <a name="scenarios"></a>시나리오

SQL Database 관리 인스턴스는 가상 네트워크 내부에서 연결을 허용하는 개인 끝점을 제공합니다. 기본 옵션은 최대 격리를 제공하는 것입니다. 그러나 공용 끝점 연결을 제공해야 하는 시나리오가 있습니다.

- 관리되는 인스턴스는 다중 테넌트 전용 플랫폼-a-a-service(PaaS) 오퍼링과 통합되어야 합니다.
- VPN을 사용할 때보다 데이터 교환 처리량이 더 많이 필요합니다.
- 회사 정책은 회사 네트워크 내에서 PaaS를 금지합니다.

## <a name="deploy-a-managed-instance-for-public-endpoint-access"></a>공용 엔드포인트 액세스를 위해 관리되는 인스턴스 배포

필수는 아니지만 공용 끝점 액세스가 있는 관리되는 인스턴스의 일반적인 배포 모델은 전용 격리된 가상 네트워크에서 인스턴스를 만드는 것입니다. 이 구성에서 가상 네트워크는 가상 클러스터 격리에만 사용됩니다. 관리되는 인스턴스의 IP 주소 공간이 회사 네트워크의 IP 주소 공간과 겹치는지는 중요하지 않습니다.

## <a name="secure-data-in-motion"></a>이동 중인 데이터 보안

클라이언트 드라이버가 암호화를 지원하는 경우 관리되는 인스턴스 데이터 트래픽은 항상 암호화됩니다. 관리되는 인스턴스와 다른 Azure 가상 시스템 또는 Azure 서비스 간에 전송된 데이터는 Azure의 백본을 벗어나지 않습니다. 관리되는 인스턴스와 온-프레미스 네트워크 간에 연결이 있는 경우 Azure ExpressRoute를 사용하는 것이 좋습니다. ExpressRoute는 공용 인터넷을 통해 데이터를 이동하지 않도록 도와줍니다. 관리형 인스턴스 개인 연결의 경우 개인 피어링만 사용할 수 있습니다.

## <a name="lock-down-inbound-and-outbound-connectivity"></a>인바운드 및 아웃바운드 연결 잠금

다음 다이어그램에서는 권장되는 보안 구성을 보여 주며 다음과 같은 구성을 보여 주며 다음과 같은

![인바운드 및 아웃바운드 연결을 잠그는 보안 구성](media/sql-database-managed-instance-public-endpoint-securely/managed-instance-vnet.png)

관리되는 인스턴스에는 [전용 공용 끝점 주소가](sql-database-managed-instance-find-management-endpoint-ip-address.md)있습니다. 클라이언트 측 아웃바운드 방화벽 및 네트워크 보안 그룹 규칙에서 이 공용 끝점 IP 주소를 설정하여 아웃바운드 연결을 제한합니다.

관리되는 인스턴스에 대한 트래픽이 신뢰할 수 있는 원본에서 유입되도록 하려면 잘 알려진 IP 주소가 있는 원본에서 연결하는 것이 좋습니다. 네트워크 보안 그룹을 사용하여 port 3342에서 관리되는 인스턴스 공용 끝점에 대한 액세스를 제한합니다.

클라이언트가 온-프레미스 네트워크에서 연결을 시작해야 하는 경우 원래 주소가 잘 알려진 IP 주소 집합으로 변환되는지 확인합니다. 이렇게 할 수 없는 경우(예: 모바일 인력이 일반적인 시나리오인 경우) [지점 간 VPN 연결 및 개인 엔드포인트를](sql-database-managed-instance-configure-p2s.md)사용하는 것이 좋습니다.

Azure에서 연결이 시작되는 경우 잘 알려진 할당된 가상 [IP](../virtual-network/virtual-networks-reserved-public-ip.md) 주소(예: 가상 시스템)에서 트래픽을 받는 것이 좋습니다. 가상 IP(VIP) 주소를 보다 쉽게 관리하려면 [공용 IP 주소 접두사를](../virtual-network/public-ip-address-prefix.md)사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- 인스턴스 관리를 위한 공용 끝점을 구성하는 방법 알아보기: [공용 끝점 구성](sql-database-managed-instance-public-endpoint-configure.md)
