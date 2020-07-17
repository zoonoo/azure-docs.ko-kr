---
title: Azure SQL Managed Instance 공용 끝점 보안
description: Azure SQL Managed Instance에서 공용 엔드포인트를 안전 하 게 사용
services: sql-database
ms.service: sql-managed-instance
ms.subservice: security
ms.custom: sqldbrb=1
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: vanto, carlrab
ms.date: 05/08/2019
ms.openlocfilehash: a3339d03607c2286dabbac73fd0b683c61552dc0
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84708606"
---
# <a name="use-azure-sql-managed-instance-securely-with-public-endpoints"></a>공용 끝점과 안전 하 게 Azure SQL Managed Instance 사용
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Azure SQL Managed Instance는 [공용 끝점](../../virtual-network/virtual-network-service-endpoints-overview.md)에 대 한 사용자 연결을 제공할 수 있습니다. 이 문서에서는이 구성을 보다 안전 하 게 보호 하는 방법을 설명 합니다.

## <a name="scenarios"></a>시나리오

Azure SQL Managed Instance은 가상 네트워크 내부에서의 연결을 허용 하는 개인 끝점을 제공 합니다. 기본 옵션은 최대 격리를 제공 하는 것입니다. 그러나 공용 끝점 연결을 제공 해야 하는 시나리오는 다음과 같습니다.

- 관리 되는 인스턴스는 다중 테 넌 트 전용 PaaS (platform as a service) 제품과 통합 되어야 합니다.
- VPN을 사용 하는 경우 보다 높은 처리량의 데이터 교환이 필요 합니다.
- 회사 정책은 회사 네트워크 내에서 PaaS를 금지 합니다.

## <a name="deploy-a-managed-instance-for-public-endpoint-access"></a>공용 끝점 액세스를 위한 관리 되는 인스턴스 배포

필수는 아니지만 공용 끝점 액세스 권한이 있는 관리 되는 인스턴스의 일반적인 배포 모델은 전용 격리 된 가상 네트워크에서 인스턴스를 만드는 것입니다. 이 구성에서 가상 네트워크는 가상 클러스터 격리에만 사용 됩니다. 관리 되는 인스턴스의 IP 주소 공간이 회사 네트워크의 IP 주소 공간과 겹치면 상관 없습니다.

## <a name="secure-data-in-motion"></a>동작에서 데이터 보호

클라이언트 드라이버에서 암호화를 지 원하는 경우 SQL Managed Instance 데이터 트래픽은 항상 암호화 됩니다. 관리 되는 인스턴스와 다른 Azure virtual machines 또는 Azure 서비스 간에 전송 되는 데이터는 Azure의 백본을 유지 하지 않습니다. 관리 되는 인스턴스와 온-프레미스 네트워크 간 연결이 있는 경우 Azure Express 경로를 사용 하는 것이 좋습니다. Express 경로를 사용 하면 공용 인터넷을 통해 데이터를 이동 하지 않아도 됩니다. 관리 되는 인스턴스 개인 연결의 경우 개인 피어 링만 사용할 수 있습니다.

## <a name="lock-down-inbound-and-outbound-connectivity"></a>인바운드 및 아웃 바운드 연결 잠금

다음 다이어그램에서는 권장 되는 보안 구성을 보여 줍니다.

![인바운드 및 아웃 바운드 연결을 잠그기 위한 보안 구성](./media/public-endpoint-overview/managed-instance-vnet.png)

관리 되는 인스턴스에는 [전용 공용 끝점 주소가](management-endpoint-find-ip-address.md)있습니다. 클라이언트 쪽 아웃 바운드 방화벽과 네트워크 보안 그룹 규칙에서이 공용 끝점 IP 주소를 설정 하 여 아웃 바운드 연결을 제한 합니다.

관리 되는 인스턴스에 대 한 트래픽을 신뢰할 수 있는 원본에서 가져오도록 하려면 잘 알려진 IP 주소를 사용 하 여 원본에서 연결 하는 것이 좋습니다. 네트워크 보안 그룹을 사용 하 여 포트 3342에서 관리 되는 인스턴스 공용 끝점에 대 한 액세스를 제한 합니다.

클라이언트가 온-프레미스 네트워크에서 연결을 시작 해야 하는 경우 원래 주소가 잘 알려진 IP 주소 집합으로 변환 되었는지 확인 합니다. 이러한 작업을 수행할 수 없는 경우 (예: 일반적인 시나리오에 대 한 모바일 직원) [지점 및 사이트 간 VPN 연결 및 개인 끝점](point-to-site-p2s-configure.md)을 사용 하는 것이 좋습니다.

Azure에서 연결을 시작한 경우에는 잘 알려진 할당 된 [가상 IP 주소](/previous-versions/azure/virtual-network/virtual-networks-reserved-public-ip) (예: 가상 머신)에서 트래픽을 제공 하는 것이 좋습니다. VIP (가상 IP) 주소를 보다 쉽게 관리할 수 있도록 [공용 ip 주소 접두사](../../virtual-network/public-ip-address-prefix.md)를 사용 하는 것이 좋습니다.

## <a name="next-steps"></a>다음 단계

- 인스턴스 관리에 대 한 공용 끝점을 구성 하는 방법에 대해 알아봅니다. [공용 끝점 구성](public-endpoint-configure.md)
