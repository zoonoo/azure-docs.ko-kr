---
title: 보안 끝점을 공개 하는 관리 되는 인스턴스-Azure SQL Database 관리 되는 인스턴스 | Microsoft Docs
description: 관리 되는 인스턴스를 사용 하 여 Azure에서 공용 끝점을 안전 하 게 사용
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: vanto, carlrab
manager: craigg
ms.date: 05/08/2019
ms.openlocfilehash: f06677b66c8f6586fec8cc5dfe97b1515b741e9c
ms.sourcegitcommit: 399db0671f58c879c1a729230254f12bc4ebff59
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/09/2019
ms.locfileid: "65470299"
---
# <a name="use-an-azure-sql-database-managed-instance-securely-with-public-endpoints"></a>공용 끝점을 사용 하 여 Azure SQL Database 관리 되는 인스턴스를 안전 하 게 사용

Azure SQL Database 관리 되는 인스턴스에 대해 사용자 연결을 제공할 수 있습니다 [공용 끝점](../virtual-network/virtual-network-service-endpoints-overview.md)합니다. 이 문서에서는이 구성을 더욱 안전 하 게 하는 방법에 설명 합니다.

## <a name="scenarios"></a>시나리오

SQL Database 관리 되는 인스턴스는 해당 가상 네트워크 내에서 연결을 허용 하도록 개인 끝점을 제공 합니다. 기본 옵션을 최대 격리를 제공 하는 것입니다. 그러나 가지 공용 끝점 연결을 제공 해야 하는 시나리오가 있습니다.

- 관리 되는 인스턴스는 다중 테 넌 트 전용 플랫폼-as a service (PaaS) 제품으로 통합 해야 합니다.
- 더 높은 처리량의 VPN을 사용 하는 경우 가능한 것 보다 데이터 교환 해야 합니다.
- 회사 정책을 회사 네트워크 내에서 PaaS를 금지 합니다.

## <a name="deploy-a-managed-instance-for-public-endpoint-access"></a>공용 끝점 액세스를 위한 관리 되는 인스턴스를 배포 합니다.

하지만 필수 사항은 아니지만, 공용 끝점 액세스를 사용 하 여 관리 되는 인스턴스에 대 한 일반 배포 모델을 격리 된 전용된 가상 네트워크의 인스턴스를 만드는 것입니다. 이 구성에서는 가상 네트워크는 가상 클러스터 격리에만 사용 됩니다. 회사 네트워크의 IP 주소 공간을 사용 하 여 관리 되는 인스턴스의 IP 주소 공간이 겹치면 중요 하지 않습니다.

## <a name="secure-data-in-motion"></a>동작 중인 데이터 보호

관리 되는 인스턴스 데이터 트래픽이 암호화 클라이언트 드라이버를 지 원하는 경우에 항상 암호화 됩니다. 관리 되는 인스턴스 및 다른 Azure 가상 머신 또는 Azure 서비스 간에 전송 된 데이터를 Azure의 백본을 벗어나지 않습니다. 관리 되는 인스턴스 및 온-프레미스 네트워크 간의 연결 인 경우에 Azure ExpressRoute를 사용 하 여 Microsoft 피어 링 하는 것이 좋습니다. ExpressRoute를 사용 하면 공용 인터넷을 통해 데이터를 이동 하지 않습니다. 관리 되는 인스턴스 개인 연결에 대 한 개인 피어 링만 사용할 수 있습니다.

## <a name="lock-down-inbound-and-outbound-connectivity"></a>인바운드 및 아웃 바운드 연결을 잠그려면

다음 다이어그램은 권장 되는 보안 구성을 보여 줍니다.

![인바운드 및 아웃 바운드 연결을 잠그기 위한 보안 구성](media/sql-database-managed-instance-public-endpoint-securely/managed-instance-vnet.png)

관리 되는 인스턴스를 [전용 공용 끝점 주소가](sql-database-managed-instance-find-management-endpoint-ip-address.md)합니다. 클라이언트 쪽 아웃 바운드 방화벽 및 네트워크 보안 그룹 규칙에서 아웃 바운드 연결을 제한 하려면이 공용 끝점 IP 주소를 설정 합니다.

관리 되는 인스턴스로 트래픽을 신뢰할 수 있는 출처에서 가져온을 위해 잘 알려진 IP 주소를 사용 하 여 원본에서 연결 하는 것이 좋습니다. 3342 포트에서 관리 되는 인스턴스 공용 끝점에 대 한 액세스를 제한 하려면 네트워크 보안 그룹을 사용 합니다.

클라이언트를 온-프레미스 네트워크에서 연결을 시작 해야 하는 경우 원래 주소는 IP 주소의 잘 알려진 집합으로 변환 해야 합니다. 사용 좋습니다 (예: 일반적인 시나리오는 중인 모바일 작업자) 하도록 할 수 없는 경우 [지점-사이트 간 VPN 연결 및 개인 끝점을](sql-database-managed-instance-configure-p2s.md)입니다.

연결 된 Azure에서 시작 하는 경우 트래픽이 시작에서 잘 알려진 할당 되는 것이 좋습니다 [가상 IP 주소](../virtual-network/virtual-networks-reserved-public-ip.md) (예: 가상 머신). 관리를 위해 쉽게 가상 IP (VIP) 주소를 사용 하려는 [공용 IP 주소 접두사](../virtual-network/public-ip-address-prefix.md)합니다.

## <a name="next-steps"></a>다음 단계

- 관리 인스턴스에 대 한 공용 끝점을 구성 하는 방법에 알아봅니다. [공용 끝점을 구성 합니다.](sql-database-managed-instance-public-endpoint-configure.md)
