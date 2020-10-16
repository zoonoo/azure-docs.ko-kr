---
title: 개인 링크를 사용 하 여 Azure HDInsight 클러스터 보호 및 격리 (미리 보기)
description: Azure 개인 링크를 사용 하 여 가상 네트워크에서 Azure HDInsight 클러스터를 격리 하는 방법을 알아봅니다.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 10/15/2020
ms.openlocfilehash: c51b99ed04357cdebaabbde2b2bd0400adcfef30
ms.sourcegitcommit: 33368ca1684106cb0e215e3280b828b54f7e73e8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/16/2020
ms.locfileid: "92134183"
---
# <a name="secure-and-isolate-azure-hdinsight-clusters-with-private-link-preview"></a>개인 링크를 사용 하 여 Azure HDInsight 클러스터 보호 및 격리 (미리 보기)

Azure HDInsight의 [기본 virtual network 아키텍처](./hdinsight-virtual-network-architecture.md)에서 RP (hdinsight 리소스 공급자)는 공용 IP 주소를 사용 하 여 클러스터와 통신 합니다. 일부 시나리오에서는 공용 IP 주소를 사용 하지 않고 네트워크 격리가 완료 되어야 합니다. 이 문서에서는 사설 HDInsight 클러스터를 만드는 데 사용할 수 있는 고급 컨트롤에 대해 알아봅니다. 완전 한 네트워크 격리 없이 클러스터에서 들어오고 나가는 트래픽을 제한 하는 방법에 대 한 자세한 내용은 [Azure HDInsight에서 네트워크 트래픽 제어](./control-network-traffic.md)를 참조 하세요.

ARM (Azure Resource Manager) 템플릿에서 특정 네트워크 속성을 구성 하 여 사설 HDInsight 클러스터를 만들 수 있습니다. 사설 HDInsight 클러스터를 만드는 데 사용 하는 두 가지 속성이 있습니다.

* 을 (를) 아웃 바운드로 설정 하 여 공용 IP 주소 `resourceProviderConnection` 를 제거 합니다.
* Azure 개인 링크를 사용 하도록 설정 하 고을 사용으로 설정 하 여 [개인 끝점](../private-link/private-endpoint-overview.md) `privateLink` 을 사용 합니다.

## <a name="remove-public-ip-addresses"></a>공용 IP 주소 제거

기본적으로 HDInsight RP는 공용 Ip를 사용 하 여 클러스터에 대 한 *인바운드* 연결을 사용 합니다. `resourceProviderConnection`네트워크 속성이 *아웃 바운드*로 설정 되 면 연결이 항상 클러스터 내부에서 RP로 시작 되도록 HDInsight RP에 대 한 연결을 반대로 바꿉니다. 인바운드 연결이 없으면 인바운드 서비스 태그 또는 공용 IP 주소가 필요 하지 않습니다.

기본 가상 네트워크 아키텍처에서 사용 되는 기본 부하 분산 장치는 자동으로 공용 NAT (네트워크 주소 변환)를 제공 하 여 HDInsight RP와 같은 필요한 아웃 바운드 종속성에 액세스 합니다. 공용 인터넷에 대 한 아웃 바운드 연결을 제한 하려는 경우 방화벽을 [구성할](./hdinsight-restrict-outbound-traffic.md)수 있지만 반드시 필요한 것은 아닙니다.

`resourceProviderConnection`아웃 바운드를 구성 하면 전용 끝점을 사용 하 여 Azure Data Lake Storage Gen2 또는 외부 metastore 같은 클러스터 특정 리소스에도 액세스할 수 있습니다. HDInsight 클러스터를 만들기 전에 개인 끝점과 DNS 항목을 구성 해야 합니다. 클러스터를 만드는 동안 Apache 레인저, Ambari, Oozie 및 Hive metastore와 같은 필요한 모든 외부 SQL 데이터베이스를 만들고 제공 하는 것이 좋습니다.

Azure Key Vault에 대 한 전용 끝점은 지원 되지 않습니다. 미사용 CMK 암호화에 Azure Key Vault를 사용 하는 경우 개인 끝점이 없는 HDInsight 서브넷 내에서 Azure Key Vault 끝점에 액세스할 수 있어야 합니다.

다음 다이어그램에서는 `resourceProviderConnection` 이 아웃 바운드로 설정 된 경우 잠재적 HDInsight 가상 네트워크 아키텍처의 모양을 보여 줍니다.

:::image type="content" source="media/hdinsight-private-link/outbound-resource-provider-connection-only.png" alt-text="아웃 바운드 리소스 공급자 연결을 사용한 HDInsight 아키텍처 다이어그램":::

클러스터를 만든 후 적절 한 DNS 확인을 설정 해야 합니다. 다음 정식 이름 DNS 레코드 (CNAME)는 Azure에서 관리 되는 공용 DNS 영역에 생성 됩니다 `azurehdinsight.net` .

```dns
<clustername>    CNAME    <clustername>-int
```

클러스터 Fqdn을 사용 하 여 클러스터에 액세스 하려면 직접 내부 부하 분산 장치 개인 Ip를 사용 하거나 사용자 고유의 사설 DNS 영역을 사용 하 여 클러스터 끝점을 요구 사항에 맞게 재정의할 수 있습니다. 예를 들어 사설 DNS 영역를 사용할 수 있습니다 `azurehdinsight.net` . 필요에 따라 개인 Ip를 추가 합니다.

```dns
<clustername>        A   10.0.0.1
<clustername-ssh>    A   10.0.0.2
```

## <a name="enable-private-link"></a>개인 링크 사용

기본적으로 사용 하지 않도록 설정 된 개인 링크에는 클러스터를 만들기 전에 UDR (사용자 정의 경로) 및 방화벽 규칙을 제대로 설정 하는 데 필요한 광범위 한 네트워킹 정보가 필요 합니다. 클러스터에 대 한 개인 링크 액세스는 `resourceProviderConnection` 이전 섹션에서 설명한 대로 네트워크 속성이 *아웃 바운드* 로 설정 된 경우에만 사용할 수 있습니다.

`privateLink`이 *사용*으로 설정 되 면 내부 [표준 부하 분산 장치](../load-balancer/load-balancer-overview.md) (SLB)가 만들어지고 각 Slb에 대해 Azure 개인 링크 서비스가 프로 비전 됩니다. 개인 링크 서비스를 사용 하면 개인 끝점에서 HDInsight 클러스터에 액세스할 수 있습니다.

표준 부하 분산 장치는 기본 부하 분산 장치 처럼 공용 아웃 바운드 NAT를 자동으로 제공 하지 않습니다. 아웃 바운드 종속성을 위해 NAT 또는 [방화벽과](./hdinsight-restrict-outbound-traffic.md)같은 [VIRTUAL NETWORK](../virtual-network/nat-overview.md) 사용자 고유의 nat 솔루션을 제공 해야 합니다. HDInsight 클러스터는 여전히 아웃 바운드 종속성에 액세스할 수 있어야 합니다. 이러한 아웃 바운드 종속성을 사용할 수 없는 경우 클러스터 만들기가 실패할 수 있습니다.

### <a name="prepare-your-environment"></a>환경 준비

다음 다이어그램에서는 클러스터를 만들기 전에 필요한 네트워킹 구성의 예를 보여 줍니다. 이 예제에서 모든 아웃 바운드 트래픽은 UDR을 사용 하 여 Azure 방화벽에 [강제](../firewall/forced-tunneling.md) 적용 되며 클러스터를 만들기 전에 방화벽에서 필요한 아웃 바운드 종속성이 "허용" 이어야 합니다. Enterprise Security Package 클러스터의 경우 VNet 피어 링에서 Azure Active Directory Domain Services에 대 한 네트워크 연결을 제공할 수 있습니다.

:::image type="content" source="media/hdinsight-private-link/before-cluster-creation.png" alt-text="아웃 바운드 리소스 공급자 연결을 사용한 HDInsight 아키텍처 다이어그램":::

네트워킹을 설정한 후에는 다음 그림에 표시 된 것 처럼 아웃 바운드 리소스 공급자 연결 및 개인 링크를 사용 하는 클러스터를 만들 수 있습니다. 이 구성에서는 공용 Ip가 없으며 각 표준 부하 분산 장치에 대해 개인 링크 서비스가 프로 비전 됩니다.

:::image type="content" source="media/hdinsight-private-link/after-cluster-creation.png" alt-text="아웃 바운드 리소스 공급자 연결을 사용한 HDInsight 아키텍처 다이어그램":::

### <a name="access-a-private-cluster"></a>개인 클러스터에 액세스

개인 클러스터에 액세스 하려면 내부 부하 분산 장치 개인 Ip를 직접 사용 하거나 개인 링크 DNS 확장 및 개인 끝점을 사용할 수 있습니다. `privateLink`설정이 사용으로 설정 되 면 개인 끝점을 만들고 개인 dns 영역을 통해 dns 확인을 구성할 수 있습니다.

Azure에서 관리 되는 공용 DNS 영역에서 만든 개인 링크 항목은 다음과 같습니다 `azurehdinsight.net` .

```dns
<clustername>        CNAME    <clustername>.privatelink
<clustername>-int    CNAME    <clustername>-int.privatelink
<clustername>-ssh    CNAME    <clustername>-ssh.privatelink
```

다음 이미지는 피어 링 되지 않거나 클러스터 부하 분산 장치에 직접적인 시야를 갖지 않는 가상 네트워크에서 클러스터에 액세스 하는 데 필요한 개인 DNS 항목의 예를 보여 줍니다. Azure 개인 영역을 사용 하 여 fqdn을 재정의 하 `*.privatelink.azurehdinsight.net` 고 사용자 고유의 개인 끝점 IP 주소로 확인할 수 있습니다.

:::image type="content" source="media/hdinsight-private-link/access-private-clusters.png" alt-text="아웃 바운드 리소스 공급자 연결을 사용한 HDInsight 아키텍처 다이어그램":::

## <a name="arm-template-properties"></a>ARM 템플릿 속성

다음 JSON 코드 조각에는 개인 HDInsight 클러스터를 만들기 위해 ARM 템플릿에서 구성 해야 하는 두 가지 네트워크 속성이 포함 되어 있습니다.

```json
networkProperties: {
    "resourceProviderConnection": "Inbound" | "Outbound",
    "privateLink": "Enabled" | "Disabled"
}
```

개인 링크를 비롯 한 다양 한 HDInsight 엔터프라이즈 보안 기능이 포함 된 전체 템플릿은 [hdinsight enterprise security template (hdinsight enterprise security 템플릿](https://github.com/Azure-Samples/hdinsight-enterprise-security/tree/main/ESP-HIB-PL-Template))을 참조 하세요.

## <a name="next-steps"></a>다음 단계

* [Azure HDInsight에 대 한 Enterprise Security Package](enterprise-security-package.md)
* [Azure HDInsight의 엔터프라이즈 보안 일반 정보 및 지침](./domain-joined/general-guidelines.md)
