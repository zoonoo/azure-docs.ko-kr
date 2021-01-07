---
title: Azure DDoS Protection 참조 아키텍처
description: Azure DDoS protection 참조 아키텍처에 대해 알아봅니다.
services: ddos-protection
documentationcenter: na
author: yitoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/08/2020
ms.author: yitoh
ms.openlocfilehash: e5472620fe9b07d152a5325b0654044cb1505fd7
ms.sourcegitcommit: 9889a3983b88222c30275fd0cfe60807976fd65b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/20/2020
ms.locfileid: "94992440"
---
# <a name="ddos-protection-reference-architectures"></a>DDoS Protection 참조 아키텍처

DDoS Protection Standard는 [가상 네트워크에 배포 된 서비스를 위해](../virtual-network/virtual-network-for-azure-services.md)설계 되었습니다. 다른 서비스에는 기본 DDoS Protection 기본 서비스가 적용됩니다. 다음과 같은 참조 아키텍처는 시나리오별로 정렬되어 있으며, 아키텍처 패턴이 함께 그룹화되어 있습니다.

## <a name="virtual-machine-windowslinux-workloads"></a>가상 머신(Windows/Linux) 워크로드

### <a name="application-running-on-load-balanced-vms"></a>부하 분산된 VM에서 실행되는 애플리케이션

이 참조 아키텍처에서는 확장성과 가용성을 개선하기 위해 부하 분산 장치 뒤에서 확장 집합에 포함된 복수의 Windows VM을 실행하는 검증된 모범 사례를 보여줍니다. 이 아키텍처는 웹 서버 같은 상태 비저장 워크로드에 사용할 수 있습니다.

![부하 분산된 VM에서 실행 중인 애플리케이션에 대한 참조 아키텍처 다이어그램](./media/ddos-best-practices/image-9.png)

이 아키텍처에서 워크로드는 여러 VM 인스턴스에 배포됩니다. 공용 IP 주소는 한 개가 사용되며, 부하 분산 장치를 통해 인터넷 트래픽이 여러 VM으로 분산됩니다. DDoS Protection 표준은 공용 IP가 연결된 Azure(인터넷) 부하 분산 장치의 가상 네트워크에서 사용됩니다.

부하 분산 장치는 수신되는 인터넷 요청을 여러 VM 인스턴스로 분산합니다. 가상 머신 확장 집합을 사용하면 여러 VM을 수동 확장 또는 축소하거나 사전 정의된 규칙을 바탕으로 자동 확장 또는 축소할 수 있습니다. 이는 리소스가 DDoS 공격을 받고 있는 경우에 중요합니다. 이 참조 아키텍처에 대한 자세한 내용은 [이 아티클](/azure/architecture/reference-architectures/virtual-machines-windows/multi-vm)을 참조하세요.

### <a name="application-running-on-windows-n-tier"></a>Windows N 계층에서 실행 중인 애플리케이션

N 계층 아키텍처를 구현하는 방법은 여러 가지가 있습니다. 다음 다이어그램은 일반적인 3계층 웹 애플리케이션을 보여줍니다. 이 아키텍처는 [부하가 분산된 VM을 실행하여 확장성 및 가용성 확보](/azure/architecture/reference-architectures/virtual-machines-windows/multi-vm) 아티클을 기반으로 합니다. 웹 및 비즈니스 계층은 부하 분산된 VM을 사용합니다.

![Windows N 계층에서 실행 중인 애플리케이션에 대한 참조 아키텍처 다이어그램](./media/ddos-best-practices/image-10.png)

이 아키텍처에서 DDoS Protection 표준은 가상 네트워크에서 사용됩니다. 가상 네트워크의 모든 공용 IP는 레이어 3 및 4에 대해 DDoS 보호를 받습니다. 레이어 7 보호의 경우 WAF SKU에 Application Gateway를 배포합니다. 이 참조 아키텍처에 대한 자세한 내용은 [이 아티클](/azure/architecture/reference-architectures/virtual-machines-windows/n-tier)을 참조하세요.

> [!NOTE]
> 단일 VM이 공용 IP 뒤에서 실행 되는 시나리오는 지원 되지 않습니다.

### <a name="paas-web-application"></a>PaaS 웹 애플리케이션

이 참조 아키텍처는 단일 지역에서 Azure App Service 애플리케이션을 실행하는 방법을 보여줍니다. 이 아키텍처는 [Azure App Service](https://azure.microsoft.com/documentation/services/app-service/) 및 [Azure SQL Database](https://azure.microsoft.com/documentation/services/sql-database/)를 사용하는 웹 애플리케이션에 관한 일련의 검증된 사례를 보여줍니다.
대기 지역은 장애 조치(failover) 시나리오에 대해 설정되어 있습니다.

![PaaS 웹 애플리케이션에 대한 참조 아키텍처 다이어그램](./media/ddos-best-practices/image-11.png)

Azure Traffic Manager는 들어오는 요청을 한 지역의 Application Gateway로 라우팅합니다. 정상 작동 중에는 요청을 활성 지역의 Application Gateway로 라우팅합니다. 해당 지역을 사용할 수 없게 되면 Traffic Manager가 대기 지역의 Application Gateway로 장애 조치(failover) 됩니다.

웹 애플리케이션을 대상으로 하는 인터넷의 모든 트래픽이 Traffic Manager를 통해 [Application Gateway 공용 IP 주소](../application-gateway/application-gateway-web-app-overview.md)로 라우팅됩니다. 이 시나리오에서 앱 서비스(웹앱) 자체는 외부와 직접 연결되지 않고 Application Gateway를 통해 보호됩니다. 

레이어 7(HTTP/HTTPS/WebSocket) 공격에 대해 보호할 수 있도록 Application Gateway WAF SKU(금지 모드)를 구성하는 것이 좋습니다. 또한 웹앱은 [Application Gateway IP 주소에서 오는 트래픽만 수락](https://azure.microsoft.com/blog/ip-and-domain-restrictions-for-windows-azure-web-sites/)하도록 구성됩니다.

이 참조 아키텍처에 대한 자세한 내용은 [이 아티클](/azure/architecture/reference-architectures/app-service-web-app/multi-region)을 참조하세요.

## <a name="mitigation-for-non-web-paas-services"></a>비 웹 PaaS 서비스에 대한 완화

### <a name="hdinsight-on-azure"></a>Azure의 HDInsight

이 참조 아키텍처는 [Azure HDInsight 클러스터](../hdinsight/index.yml)에 대해 DDoS Protection 표준을 구성하는 방법을 보여줍니다. HDInsight 클러스터가 가상 네트워크에 연결되어 있고 DDoS Protection이 해당 가상 네트워크에서 사용되는지 확인해야 합니다.

![가상 네트워크 설정을 포함한 "HDInsight" 및 "고급 설정" 창](./media/ddos-best-practices/image-12.png)

![DDoS Protection을 사용하기 위한 선택](./media/ddos-best-practices/image-13.png)

이 아키텍처에서 인터넷의 HDInsight 클러스터를 대상으로 하는 트래픽은 HDInsight 게이트웨이 부하 분산 장치와 연결된 공용 IP 주소로 라우팅됩니다. 그러면 게이트웨이 부하 분산 장치가 트래픽을 헤드 노드 또는 작업자 노드로 직접 보냅니다. HDInsight 가상 네트워크에서 DDoS Protection 표준을 사용하기 때문에 가상 네트워크의 모든 공용 IP가 레이어3 및 4에 대해 DDoS Protection을 받습니다. 이 참조 아키텍처를 N 계층 및 다중 지역 참조 아키텍처와 결합할 수 있습니다.

이 참조 아키텍처에 대한 자세한 내용은 [Azure Virtual Network를 사용하여 Azure HDInsight 확장](../hdinsight/hdinsight-plan-virtual-network-deployment.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 설명서를 참조하세요.


> [!NOTE]
> 공용 IP를 사용한 가상 네트워크에서의 PowerApps 또는 API를 위한 Azure App Service 환경은 기본적으로 지원되지 않습니다.

## <a name="next-steps"></a>다음 단계

- [DDoS 보호 계획을 만드는](manage-ddos-protection.md)방법에 대해 알아봅니다.