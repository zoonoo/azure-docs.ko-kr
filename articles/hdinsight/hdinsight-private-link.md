---
title: Private Link를 사용하여 Azure HDInsight 클러스터 보호 및 격리(미리 보기)
description: Azure Private Link를 사용하여 가상 네트워크에서 Azure HDInsight 클러스터를 격리하는 방법을 알아봅니다.
ms.service: hdinsight
ms.topic: conceptual
ms.date: 10/15/2020
ms.openlocfilehash: fe3b9617db20f445d4139c006c283bbfe537d544
ms.sourcegitcommit: 6323442dbe8effb3cbfc76ffdd6db417eab0cef7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/28/2021
ms.locfileid: "110616482"
---
# <a name="secure-and-isolate-azure-hdinsight-clusters-with-private-link-preview"></a>Private Link를 사용하여 Azure HDInsight 클러스터 보호 및 격리(미리 보기)

Azure HDInsight의 [기본 가상 네트워크 아키텍처](./hdinsight-virtual-network-architecture.md)에서 HDInsight RP(리소스 공급자)는 공용 IP 주소를 사용하여 클러스터와 통신합니다. 일부 시나리오에서는 공용 IP 주소를 사용하지 않는 완전한 네트워크 격리가 필요합니다. 이 문서에서는 프라이빗 HDInsight 클러스터를 만드는 데 사용할 수 있는 고급 컨트롤에 대해 알아봅니다. 전체 네트워크 격리 없이 클러스터에서 트래픽을 제한하는 방법에 대한 자세한 내용은 [Azure HDInsight에서 네트워크 트래픽 제어](./control-network-traffic.md)를 참조하세요.

ARM(Azure Resource Manager) 템플릿에서 특정 네트워크 속성을 구성하여 프라이빗 HDInsight 클러스터를 만들 수 있습니다. 프라이빗 HDInsight 클러스터를 만드는 데 사용하는 두 가지 속성이 있습니다.

* `resourceProviderConnection`을 아웃바운드로 설정하여 공용 IP 주소를 제거합니다.
* `privateLink`를 사용하도록 설정하여 Azure Private Link를 사용하도록 설정하고 [프라이빗 엔드포인트](../private-link/private-endpoint-overview.md)를 사용합니다.

## <a name="remove-public-ip-addresses"></a>공용 IP 주소 제거

기본적으로 HDInsight RP는 공용 IP를 사용하여 클러스터에 대한 ‘인바운드’ 연결을 사용합니다. `resourceProviderConnection` 네트워크 속성이 ‘아웃바운드’로 설정된 경우 클러스터 내부에서 RP로 연결이 항상 시작되도록 HDInsight RP에 대한 연결을 반전시킵니다. 인바운드 연결이 없으면 인바운드 서비스 태그 또는 공용 IP 주소가 필요하지 않습니다.

기본 가상 네트워크 아키텍처에 사용되는 기본 부하 분산 장치는 HDInsight RP와 같이 필요한 아웃바운드 의존성 액세스에 대한 퍼블릭 NAT(Network Address Translation)를 자동으로 제공합니다. 퍼블릭 인터넷에 대한 아웃바운드 연결을 제한하려는 경우 [방화벽을 구성](./hdinsight-restrict-outbound-traffic.md)해도 되지만 필수는 아닙니다.

`resourceProviderConnection`을 아웃바운드로 구성하면 프라이빗 엔드포인트를 사용하여 클러스터 관련 리소스(예: Azure Data Lake Storage Gen2 또는 외부 메타스토어)에 액세스할 수도 있습니다. 해당 리소스에 프라이빗 엔드포인트를 사용하는 것이 필수는 아니지만 해당 리소스에 대한 프라이빗 엔드포인트를 사용하려는 경우 HDInsight 클러스터를 만들기 전에 프라이빗 엔드포인트 및 DNS 항목을 구성해야 합니다.`before` 클러스터를 만들 때 필요한 모든 외부 SQL 데이터베이스(예: Apache Ranger, Ambari, Oozie 및 Hive 메타스토어)를 만들고 제공하는 것이 좋습니다. 모든 관련 리소스가 클러스터 서브넷 내에서 자체 프라이빗 엔드포인트를 통해 또는 다른 프라이빗 엔드포인트를 통해 액세스할 수 있어야 합니다.

프라이빗 엔드포인트를 통해 Azure Data Lake Storage Gen2에 연결할 때에는 Gen2 스토리지 계정에 ‘blob’ 및 ‘dfs’ 모두에 대한 엔드포인트 세트가 있어야 합니다. 자세한 내용은 [프라이빗 엔드포인트 만들기](../storage/common/storage-private-endpoints.md)를 참조하세요.

다음 다이어그램에서는 `resourceProviderConnection`이 아웃바운드로 설정된 경우 HDInsight 가상 네트워크 아키텍처가 어떤 형태가 될 수 있는지를 보여 줍니다.

:::image type="content" source="media/hdinsight-private-link/outbound-resource-provider-connection-only.png" alt-text="아웃바운드 리소스 공급자 연결을 사용하는 HDInsight 아키텍처의 다이어그램":::

클러스터를 만든 후 적절한 DNS 확인을 설정해야 합니다. `azurehdinsight.net`이라는 CNAME(Canonical Name) DNS 레코드가 Azure 관리형 퍼블릭 DNS 영역에 만들어집니다.

```dns
<clustername>    CNAME    <clustername>-int
```

클러스터 FQDN을 사용하여 클러스터에 액세스하려면 내부 부하 분산 장치의 개인 IP를 직접 사용하거나 고유한 프라이빗 DNS 영역을 사용하여 필요에 따라 클러스터 엔드포인트를 재정의할 수 있습니다. 예를 들어 `azurehdinsight.net`이라는 프라이빗 DNS 영역을 사용할 수 있습니다. 그리고 필요에 따라 개인 IP를 추가합니다.

```dns
<clustername>        A   10.0.0.1
<clustername-ssh>    A   10.0.0.2
```

## <a name="enable-private-link"></a>Private Link 사용

기본적으로 사용하지 않도록 설정되는 Private Link는 클러스터를 만들기 전에 UDR(사용자 정의 경로) 및 방화벽 규칙을 제대로 설정하기 위한 방대한 네트워킹 지식을 필요로 합니다. 이 설정을 사용하는 것은 선택 사항이지만 이전 섹션에서 설명한 대로 `resourceProviderConnection` 네트워크 속성이 ‘아웃바운드’로 설정된 경우에만 사용할 수 있습니다.

`privateLink`를 ‘사용하도록 설정’하면 내부 [SLB](../load-balancer/load-balancer-overview.md)(표준 부하 분산 장치)가 생성되고 SLB마다 Azure Private Link 서비스가 프로비저닝됩니다. Private Link 서비스를 사용하면 프라이빗 엔드포인트에서 HDInsight 클러스터에 액세스할 수 있습니다.

표준 부하 분산 장치는 기본 부하 분산 장치와 마찬가지로 [퍼블릭 아웃바운드 NAT](../load-balancer/load-balancer-outbound-connections.md)를 자동으로 제공하지 않습니다. 아웃바운드 종속성을 위해 [Virtual Network NAT](../virtual-network/nat-overview.md) 또는 [방화벽](./hdinsight-restrict-outbound-traffic.md)과 같은 자체 NAT 솔루션을 제공해야 합니다. HDInsight 클러스터는 여전히 아웃바운드 종속성에 액세스해야 합니다. 이러한 아웃바운드 종속성이 허용되지 않으면 클러스터 만들기가 실패할 수 있습니다.

### <a name="prepare-your-environment"></a>환경 준비

프라이빗 링크 서비스를 성공적으로 만들려면 [프라이빗 링크 서비스에 대한 네트워크 정책을 명시적으로 사용하지 않도록 설정](../private-link/disable-private-link-service-network-policy.md)해야 합니다.

다음 다이어그램에서는 클러스터를 만들기 전에 필요한 네트워킹 구성의 예를 보여 줍니다. 이 예제에서 모든 아웃바운드 트래픽은 UDR을 사용하여 Azure Firewall로 [강제](../firewall/forced-tunneling.md)되며 클러스터를 만들기 전에 방화벽에서 필요한 아웃바운드 종속성을 “허용”해야 합니다. Enterprise Security Package 클러스터의 경우 VNet 피어링을 통해 Azure Active Directory Domain Services 대한 네트워크 연결을 제공할 수 있습니다.

:::image type="content" source="media/hdinsight-private-link/before-cluster-creation.png" alt-text="클러스터를 만들기 전의 프라이빗 링크 환경 다이어그램":::

네트워킹을 설정한 후에는 다음 그림과 같이 아웃바운드 리소스 공급자 연결 및 프라이빗 링크가 활성화된 클러스터를 만들 수 있습니다. 이 구성에는 공용 IP가 없으며 표준 부하 분산 장치마다 Private Link 서비스가 프로비저닝됩니다.

:::image type="content" source="media/hdinsight-private-link/after-cluster-creation.png" alt-text="클러스터를 만든 후의 프라이빗 링크 환경 다이어그램":::

### <a name="access-a-private-cluster"></a>프라이빗 클러스터에 액세스

프라이빗 클러스터에 액세스하려면 내부 부하 분산 장치를 직접 사용하거나 Private Link DNS 확장 및 프라이빗 엔드포인트를 사용하면 됩니다. `privateLink` 설정이 사용으로 설정된 경우 고유한 프라이빗 엔드포인트를 만들고 프라이빗 DNS 영역을 통해 DNS 확인을 구성할 수 있습니다.

`azurehdinsight.net`이라는 Azure 관리형 퍼블릭 DNS 영역에서 만든 Private Link 항목은 다음과 같습니다.

```dns
<clustername>        CNAME    <clustername>.privatelink
<clustername>-int    CNAME    <clustername>-int.privatelink
<clustername>-ssh    CNAME    <clustername>-ssh.privatelink
```

다음 이미지는 피어링되지 않았거나 클러스터 부하 분산 장치에 직접 연결되지 않은 가상 네트워크에서 클러스터에 액세스하는 데 필요한 프라이빗 DNS 항목의 예를 보여 줍니다. Azure 프라이빗 영역을 사용하여 `*.privatelink.azurehdinsight.net` FQDN을 재정의하고 고유한 프라이빗 엔드포인트 IP 주소로 확인할 수 있습니다.

:::image type="content" source="media/hdinsight-private-link/access-private-clusters.png" alt-text="프라이빗 링크 아키텍처 다이어그램":::

## <a name="how-to-create-clusters"></a>클러스터를 만드는 방법
### <a name="use-arm-template-properties"></a>ARM 템플릿 속성 사용

다음 JSON 코드 조각에는 프라이빗 HDInsight 클러스터를 만들기 위해 ARM 템플릿에서 구성해야 하는 두 가지 네트워크 속성이 있습니다.

```json
networkProperties: {
    "resourceProviderConnection": "Inbound" | "Outbound",
    "privateLink": "Enabled" | "Disabled"
}
```

Private Link를 포함하여 많은 HDInsight 엔터프라이즈 보안 기능이 있는 전체 템플릿은 [HDInsight 엔터프라이즈 보안 템플릿](https://github.com/Azure-Samples/hdinsight-enterprise-security/tree/main/ESP-HIB-PL-Template)을 참조하세요.

### <a name="use-azure-powershell"></a>Azure PowerShell 사용

PowerShell을 사용하려면 [여기](/powershell/module/az.hdinsight/new-azhdinsightcluster#example-4--create-an-azure-hdinsight-cluster-with-relay-outbound-and-private-link-feature)에서 예제를 참조하세요.

### <a name="use-azure-cli"></a>Azure CLI 사용
Azure CLI를 사용하려면 [여기](/cli/azure/hdinsight#az_hdinsight_create-examples)에서 예제를 참조하세요.

## <a name="next-steps"></a>다음 단계

* [Azure HDInsight용 Enterprise Security Package](enterprise-security-package.md)
* [Azure HDInsight의 엔터프라이즈 보안 일반 정보 및 지침](./domain-joined/general-guidelines.md)
