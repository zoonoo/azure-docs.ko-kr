---
title: Azure 표준 Load Balancer에 Azure Firewall 통합
description: Azure 표준 Load Balancer에 Azure Firewall을 통합하는 방법 알아보기
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 4/1/2019
ms.author: victorh
ms.openlocfilehash: 7ee92a7508918635849caafab4632bbba81ee628
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60193778"
---
# <a name="integrate-azure-firewall-with-azure-standard-load-balancer"></a>Azure 표준 Load Balancer에 Azure Firewall 통합

Azure 표준 Load Balancer(공용 또는 내부)를 사용하여 가상 네트워크에 Azure Firewall을 통합할 수 있습니다. 

것이 좋습니다 Azure 방화벽을 사용 하 여 내부 부하 분산 장치를 통합 하는 훨씬 간단 하 게 디자인 이라고 합니다. 이미 배포 된 곳에 보관 하려는 경우에 공용 부하 분산 장치를 사용할 수 있습니다. 그러나 공용 부하 분산 장치 시나리오에서 기능에 영향을 줄 수 있는 비대칭 라우팅 문제에 주의해야 합니다.

Azure Load Balancer에 대한 자세한 내용은 [Azure Load Balancer란?](../load-balancer/load-balancer-overview.md)를 참조하세요.

## <a name="public-load-balancer"></a>공용 부하 분산 장치

공용 부하 분산 장치를 사용할 경우 부하 분산 장치는 공용 프런트 엔드 IP 주소를 사용하여 배포됩니다.

### <a name="asymmetric-routing"></a>비대칭 라우팅

비대칭 라우팅은 패킷이 대상에 대한 하나의 경로를 사용하고, 원본으로 돌아갈 때는 다른 경로를 사용하는 문제입니다. 이 문제는 서브넷에 방화벽의 개인 IP 주소로 가는 기본 경로가 있을 때 사용자가 공용 부하 분산 장치를 사용하는 경우에 발생합니다. 이 경우 들어오는 부하 분산 장치 트래픽은 해당 공용 IP 주소를 통해 수신되지만 반환 경로는 방화벽의 개인 IP 주소를 거칩니다. 방화벽은 상태를 저장하고 이러한 연결된 세션을 인식하지 않으므로 반환 패킷을 삭제합니다.

### <a name="fix-the-routing-issue"></a>라우팅 문제 해결

Azure Firewall을 서브넷에 배포할 때 한 가지 단계는 AzureFirewallSubnet에 있는 방화벽의 개인 IP 주소를 통해 패킷을 전달하는 서브넷의 기본 경로를 만드는 것입니다. 자세한 내용은 [자습서: Azure Portal을 사용하여 Azure Firewall 배포 및 구성](tutorial-firewall-deploy-portal.md#create-a-default-route)을 참조하세요.

부하 분산 장치 시나리오에 방화벽을 도입하는 경우 인터넷 트래픽이 방화벽의 공용 IP 주소를 통해 들어오도록 할 수 있습니다. 여기에서 방화벽은 해당 방화벽 규칙을 적용하고, 패킷을 부하 분산 장치의 공용 IP 주소로 NAT합니다. 여기서 문제가 발생합니다. 패킷은 방화벽의 공용 IP 주소에 도착하지만 개인 IP 주소(기본 경로 사용)를 통해 방화벽으로 돌아갑니다.
이 문제를 방지하려면 방화벽의 공용 IP 주소에 대해 추가 호스트 경로를 만듭니다. 방화벽의 공용 IP 주소로 이동하는 패킷은 인터넷을 통해 라우팅됩니다. 이 경우 방화벽의 개인 IP 주소로의 기본 경로가 사용되지 않게 됩니다.

![비대칭 라우팅](media/integrate-lb/Firewall-LB-asymmetric.png)

예를 들어, 다음 경로는 공용 IP 주소 13.86.122.41 및 개인 IP 주소 10.3.1.4에서 방화벽용으로 사용됩니다.

![경로 테이블](media/integrate-lb/route-table.png)

## <a name="internal-load-balancer"></a>내부 부하 분산 장치

내부 부하 분산 장치를 사용할 경우 부하 분산 장치는 프라이빗 프런트 엔드 IP 주소를 사용하여 배포됩니다.

이 시나리오에서는 비대칭 라우팅 문제가 없습니다. 수신 패킷은 방화벽의 공용 IP 주소에 도착하고, 부하 분산 장치의 개인 IP 주소로 변환된 후 같은 반환 경로를 사용하여 방화벽의 개인 IP 주소로 돌아갑니다.

따라서 공용 부하 분산 장치 시나리오와 비슷하게 이 시나리오를 배포할 수 있지만 방화벽 공용 IP 주소 호스트 경로는 필요하지 않습니다.

## <a name="additional-security"></a>추가 보안

부하 분산 시나리오의 보안을 강화하려면 NSG(네트워크 보안 그룹)를 사용할 수 있습니다.

예를 들어, 부하 분산된 가상 머신이 있는 백 엔드 서브넷에 NSG를 만들 수 있습니다. 방화벽 IP 주소/포트에서 발생하는 수신 트래픽을 허용합니다.

NSG에 대한 자세한 내용은 [보안 그룹](../virtual-network/security-overview.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

- [Azure Azure Firewall을 배포 및 구성](tutorial-firewall-deploy-portal.md)하는 방법에 대해 알아봅니다.