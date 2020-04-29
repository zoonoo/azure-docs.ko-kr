---
title: Azure 표준 Load Balancer에 Azure Firewall 통합
description: Azure 표준 Load Balancer(공용 또는 내부)를 사용하여 가상 네트워크에 Azure Firewall을 통합할 수 있습니다.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 02/28/2020
ms.author: victorh
ms.openlocfilehash: ab9a500d9535b55702b8baff15f8cc47e6ac2c86
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "78196721"
---
# <a name="integrate-azure-firewall-with-azure-standard-load-balancer"></a>Azure 표준 Load Balancer에 Azure Firewall 통합

Azure 표준 Load Balancer(공용 또는 내부)를 사용하여 가상 네트워크에 Azure Firewall을 통합할 수 있습니다. 

기본 디자인은 Azure 방화벽과 내부 부하 분산 장치를 통합 하는 것입니다 .이는 훨씬 간단한 디자인입니다. 이미 배포 된 부하 분산 장치를 보유 하 고 있는 경우 공용 부하 분산 장치를 사용할 수 있습니다. 그러나 공용 부하 분산 장치 시나리오에서 기능에 영향을 줄 수 있는 비대칭 라우팅 문제에 주의해야 합니다.

Azure Load Balancer에 대한 자세한 내용은 [Azure Load Balancer란?](../load-balancer/load-balancer-overview.md)를 참조하세요.

## <a name="public-load-balancer"></a>공용 부하 분산 장치

공용 부하 분산 장치를 사용할 경우 부하 분산 장치는 공용 프런트 엔드 IP 주소를 사용하여 배포됩니다.

### <a name="asymmetric-routing"></a>비대칭 라우팅

비대칭 라우팅은 패킷이 대상에 대한 하나의 경로를 사용하고, 원본으로 돌아갈 때는 다른 경로를 사용하는 문제입니다. 이 문제는 서브넷에 방화벽의 개인 IP 주소로 가는 기본 경로가 있을 때 사용자가 공용 부하 분산 장치를 사용하는 경우에 발생합니다. 이 경우 들어오는 부하 분산 장치 트래픽은 해당 공용 IP 주소를 통해 수신되지만 반환 경로는 방화벽의 개인 IP 주소를 거칩니다. 방화벽은 상태 저장 이므로 방화벽에서 설정 된 세션을 인식 하지 못하기 때문에 반환 하는 패킷을 삭제 합니다.

### <a name="fix-the-routing-issue"></a>라우팅 문제 해결

Azure Firewall을 서브넷에 배포할 때 한 가지 단계는 AzureFirewallSubnet에 있는 방화벽의 개인 IP 주소를 통해 패킷을 전달하는 서브넷의 기본 경로를 만드는 것입니다. 자세한 내용은 [자습서: Azure Portal 사용 하 여 Azure 방화벽 배포 및 구성](tutorial-firewall-deploy-portal.md#create-a-default-route)을 참조 하세요.

부하 분산 장치 시나리오에 방화벽을 도입하는 경우 인터넷 트래픽이 방화벽의 공용 IP 주소를 통해 들어오도록 할 수 있습니다. 여기에서 방화벽은 해당 방화벽 규칙을 적용하고, 패킷을 부하 분산 장치의 공용 IP 주소로 NAT합니다. 여기서 문제가 발생합니다. 패킷은 방화벽의 공용 IP 주소에 도착하지만 개인 IP 주소(기본 경로 사용)를 통해 방화벽으로 돌아갑니다.
이 문제를 방지하려면 방화벽의 공용 IP 주소에 대해 추가 호스트 경로를 만듭니다. 방화벽의 공용 IP 주소로 이동하는 패킷은 인터넷을 통해 라우팅됩니다. 이 경우 방화벽의 개인 IP 주소로의 기본 경로가 사용되지 않게 됩니다.

![비대칭 라우팅](media/integrate-lb/Firewall-LB-asymmetric.png)

### <a name="route-table-example"></a>경로 테이블 예

예를 들어, 공용 IP 주소 20.185.97.136 및 개인 IP 주소 10.0.1.4에서 방화벽에 대 한 경로는 다음과 같습니다.

> [!div class="mx-imgBorder"]
> ![경로 테이블](media/integrate-lb/route-table.png)

### <a name="nat-rule-example"></a>NAT 규칙 예제

다음 예제에서 NAT 규칙은 20.185.97.136에 대 한 RDP 트래픽을 20.42.98.220의 부하 분산 장치에 대 한 방화벽으로 변환 합니다.

> [!div class="mx-imgBorder"]
> ![NAT 규칙](media/integrate-lb/nat-rule-02.png)

### <a name="health-probes"></a>상태 프로브

포트 80 또는 HTTP/HTTPS 프로브에 TCP 상태 프로브를 사용 하는 경우 부하 분산 장치 풀의 호스트에서 웹 서비스를 실행 해야 합니다.

## <a name="internal-load-balancer"></a>내부 부하 분산 장치

내부 부하 분산 장치를 사용할 경우 부하 분산 장치는 프라이빗 프런트 엔드 IP 주소를 사용하여 배포됩니다.

이 시나리오에서는 비대칭 라우팅 문제가 없습니다. 수신 패킷은 방화벽의 공용 IP 주소에 도착하고, 부하 분산 장치의 개인 IP 주소로 변환된 후 같은 반환 경로를 사용하여 방화벽의 개인 IP 주소로 돌아갑니다.

따라서 공용 부하 분산 장치 시나리오와 비슷하게 이 시나리오를 배포할 수 있지만 방화벽 공용 IP 주소 호스트 경로는 필요하지 않습니다.

## <a name="additional-security"></a>추가 보안

부하 분산 시나리오의 보안을 강화하려면 NSG(네트워크 보안 그룹)를 사용할 수 있습니다.

예를 들어, 부하 분산된 가상 머신이 있는 백 엔드 서브넷에 NSG를 만들 수 있습니다. 방화벽 IP 주소/포트에서 발생하는 수신 트래픽을 허용합니다.

![네트워크 보안 그룹](media/integrate-lb/nsg-01.png)

NSGs에 대 한 자세한 내용은 [보안 그룹](../virtual-network/security-overview.md)을 참조 하세요.

## <a name="next-steps"></a>다음 단계

- [Azure Azure Firewall을 배포 및 구성](tutorial-firewall-deploy-portal.md)하는 방법에 대해 알아봅니다.