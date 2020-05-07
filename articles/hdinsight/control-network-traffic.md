---
title: Azure HDInsight에서 네트워크 트래픽 제어
description: Azure HDInsight 클러스터에 대 한 인바운드 및 아웃 바운드 트래픽을 제어 하는 기술에 대해 알아봅니다.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/04/2020
ms.openlocfilehash: 031dbb0e8c9b9fb8dc37b264f9ba8e1186efc832
ms.sourcegitcommit: 31236e3de7f1933be246d1bfeb9a517644eacd61
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82783592"
---
# <a name="control-network-traffic-in-azure-hdinsight"></a>Azure HDInsight에서 네트워크 트래픽 제어

Azure Virtual Networks의 네트워크 트래픽은 다음 방법을 사용하여 제어할 수 있습니다.

* **NSG(네트워크 보안 그룹)** 를 통해 네트워크로의 인바운드 및 아웃바운드 트래픽을 필터링할 수 있습니다. 자세한 내용은 [네트워크 보안 그룹을 사용하여 네트워크 트래픽 필터링](../virtual-network/security-overview.md) 문서를 참조하세요.

* Nva ( **네트워크 가상 어플라이언스** )는 아웃 바운드 트래픽에만 사용할 수 있습니다. Nva는 방화벽 및 라우터와 같은 장치의 기능을 복제 합니다. 자세한 내용은 [네트워크 어플라이언스](https://azure.microsoft.com/solutions/network-appliances) 문서를 참조하세요.

관리 서비스인 HDInsight는 VNET에서 들어오고 나가는 트래픽에 대해 HDInsight 상태 및 관리 서비스에 대 한 무제한 액세스가 필요 합니다. NSGs를 사용 하는 경우 이러한 서비스가 HDInsight 클러스터와 계속 통신할 수 있는지 확인 해야 합니다.

![Azure 사용자 지정 VNET에서 만든 HDInsight 엔터티 다이어그램](./media/control-network-traffic/hdinsight-vnet-diagram.png)

## <a name="hdinsight-with-network-security-groups"></a>네트워크 보안 그룹을 사용 하는 HDInsight

네트워크 **보안 그룹** 을 사용 하 여 네트워크 트래픽을 제어 하려는 경우 HDInsight를 설치 하기 전에 다음 작업을 수행 합니다.

1. HDInsight에 대해 사용할 Azure 지역을 식별합니다.

2. 해당 지역에서 HDInsight에 필요한 서비스 태그를 확인 합니다. 자세한 내용은 [Azure HDInsight에 대 한 NSG (네트워크 보안 그룹) 서비스 태그](hdinsight-service-tags.md)를 참조 하세요.

3. HDInsight를 설치 하려는 서브넷의 네트워크 보안 그룹을 만들거나 수정 합니다.

    * __네트워크 보안 그룹__: IP 주소에서 포트 __443__에 __인바운드__ 트래픽을 허용합니다. 이렇게 하면 HDInsight management services가 가상 네트워크 외부에서 클러스터에 연결할 수 있습니다.

네트워크 보안 그룹에 대 한 자세한 내용은 [네트워크 보안 그룹 개요](../virtual-network/security-overview.md)를 참조 하세요.

## <a name="controlling-outbound-traffic-from-hdinsight-clusters"></a>HDInsight 클러스터에서 아웃 바운드 트래픽 제어

HDInsight 클러스터에서 아웃 바운드 트래픽을 제어 하는 방법에 대 한 자세한 내용은 [Azure hdinsight 클러스터에 대 한 아웃 바운드 네트워크 트래픽 제한 구성](hdinsight-restrict-outbound-traffic.md)을 참조 하세요.

### <a name="forced-tunneling-to-on-premises"></a>온-프레미스로 강제 터널링

강제 터널링은 서브넷의 모든 트래픽이 온-프레미스 네트워크 또는 방화벽과 같은 특정 네트워크 또는 위치에 강제 적용 되는 사용자 정의 라우팅 구성입니다. 대량 데이터 전송 및 잠재적인 성능 영향 때문에 온-프레미스로 다시 모든 데이터 전송의 강제 터널링은 권장 _되지 않습니다_ .

강제 터널링을 설정 하려는 고객은 [사용자 지정 metastore](./hdinsight-use-external-metadata-stores.md) 을 사용 하 고 클러스터 서브넷 또는 온-프레미스 네트워크에서 이러한 사용자 지정 metastore 연결을 적절 하 게 설정 해야 합니다.

Azure 방화벽을 사용 하 여 UDR 설정의 예를 보려면 [Azure HDInsight 클러스터에 대 한 아웃 바운드 네트워크 트래픽 제한 구성](hdinsight-restrict-outbound-traffic.md)을 참조 하세요.

## <a name="required-ip-addresses"></a> 필수 IP 주소

네트워크 보안 그룹 또는 사용자 정의 경로를 사용 하 여 트래픽을 제어 하는 경우 [HDInsight 관리 IP 주소](hdinsight-management-ip-addresses.md)를 참조 하세요.

## <a name="required-ports"></a>필요한 포트

**방화벽**을 사용하여 특정 포트 외부에서 클러스터에 액세스하려는 경우 시나리오에 필요한 포트에서 트래픽을 허용해야 합니다. 이전 섹션에서 설명한 Azure 관리 트래픽이 포트 443의 클러스터에 도달할 수 있는 한, 기본적으로 포트의 특별 한 허용 목록 필요 하지 않습니다.

특정 서비스에 대한 포트 목록은 [HDInsight의 Apache Hadoop 서비스에서 사용되는 포트](hdinsight-hadoop-port-settings-for-services.md) 문서를 참조하세요.

가상 어플라이언스의 방화벽 규칙에 대한 자세한 내용은 [가상 어플라이언스 시나리오](../virtual-network/virtual-network-scenario-udr-gw-nva.md) 문서를 참조하세요.

## <a name="next-steps"></a>다음 단계

* Azure 가상 네트워크를 만드는 코드 예제 및 예제는 [Azure HDInsight 클러스터용 가상 네트워크 만들기](hdinsight-create-virtual-network.md)를 참조 하세요.
* 온-프레미스 네트워크에 연결하기 위해 HDInsight를 구성하는 엔드투엔드 예제는 [HDInsight를 온-프레미스 네트워크에 연결](./connect-on-premises-network.md)을 참조하세요.
* Azure 가상 네트워크에 대한 자세한 내용은 [Azure Virtual Network 개요](../virtual-network/virtual-networks-overview.md)를 참조하세요.
* 네트워크 보안 그룹에 대한 자세한 내용은 [네트워크 보안 그룹](../virtual-network/security-overview.md)을 참조하세요.
* 사용자 정의 경로에 대 한 자세한 내용은 [사용자 정의 경로 및 IP 전달](../virtual-network/virtual-networks-udr-overview.md)을 참조 하세요.
* 가상 네트워크에 대 한 자세한 내용은 [Plan vnet For HDInsight](./hdinsight-plan-virtual-network-deployment.md)항목을 참조 하세요.
