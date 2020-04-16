---
title: 아웃바운드 네트워크 트래픽 제한 구성 - Azure HDInsight
description: Azure HDInsight 클러스터에 대한 아웃바운드 네트워크 트래픽 제한을 구성하는 방법을 알아봅니다.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 03/11/2020
ms.openlocfilehash: 3432f981df3f666d6276eee4564ef33000faa6b1
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81410887"
---
# <a name="configure-outbound-network-traffic-for-azure-hdinsight-clusters-using-firewall"></a>방화벽을 사용하여 Azure HDInsight 클러스터에 대한 아웃바운드 네트워크 트래픽 구성

이 문서에서는 Azure 방화벽을 사용하여 HDInsight 클러스터에서 아웃바운드 트래픽을 보호하는 단계를 제공합니다. 아래 단계는 기존 클러스터에 대한 Azure 방화벽을 구성한다고 가정합니다. 방화벽 뒤에 새 클러스터를 배포하는 경우 먼저 HDInsight 클러스터와 서브넷을 만든 다음 이 가이드의 단계를 따릅니다.

## <a name="background"></a>배경

Azure HDInsight 클러스터는 일반적으로 자체 가상 네트워크에 배포됩니다. 클러스터는 제대로 작동하기 위해 네트워크 액세스가 필요한 가상 네트워크 외부의 서비스에 대한 종속성을 가지고 있습니다.

인바운드 트래픽이 필요한 몇 가지 종속성이 있습니다. 인바운드 관리 트래픽은 방화벽 장치를 통해 전송할 수 없습니다. 이 트래픽의 소스 주소는 알려져 있으며 [여기에](hdinsight-management-ip-addresses.md)게시됩니다. 이 정보가 있는 NSG(네트워크 보안 그룹) 규칙을 만들어 클러스터에 대한 인바운드 트래픽을 보호할 수도 있습니다.

HDInsight 아웃바운드 트래픽 종속성은 거의 전적으로 FQDN으로 정의되며, 그 뒤에는 정적 IP 주소가 없습니다. 정적 주소가 부족하면 NSG(네트워크 보안 그룹)를 사용하여 클러스터에서 아웃바운드 트래픽을 잠글 수 없습니다. 주소는 현재 이름 확인에 따라 규칙을 설정할 수 없을 정도로 자주 변경되고 NSG 규칙을 설정하는 데 사용할 수 없습니다.

아웃바운드 주소를 보호하는 솔루션은 도메인 이름을 기반으로 아웃바운드 트래픽을 제어할 수 있는 방화벽 장치를 사용하는 것입니다. Azure 방화벽은 대상 또는 [FQDN 태그의](../firewall/fqdn-tags.md)FQDN을 기반으로 아웃바운드 HTTP 및 HTTPS 트래픽을 제한할 수 있습니다.

## <a name="configuring-azure-firewall-with-hdinsight"></a>HDInsight를 사용하는 Azure 방화벽 구성

Azure 방화벽을 사용하여 기존 HDInsight에서 송신을 잠그는 단계의 요약은 다음과 같습니다.

1. 서브넷을 만듭니다.
1. 방화벽을 만듭니다.
1. 방화벽에 응용 프로그램 규칙 추가
1. 방화벽에 네트워크 규칙을 추가합니다.
1. 라우팅 테이블을 만듭니다.

### <a name="create-new-subnet"></a>새 서브넷 만들기

클러스터가 있는 가상 네트워크에서 **AzureFirewallSubnet이라는** 서브넷을 만듭니다.

### <a name="create-a-new-firewall-for-your-cluster"></a>클러스터에 대한 새 방화벽 만들기

자습서에서 **방화벽 배포:** [Azure 포털을 사용하여 Azure 방화벽 배포 및 구성](../firewall/tutorial-firewall-deploy-portal.md#deploy-the-firewall)단계를 사용하여 **Test-FW01이라는** 방화벽을 만듭니다.

### <a name="configure-the-firewall-with-application-rules"></a>응용 프로그램 규칙으로 방화벽 구성

클러스터가 중요한 통신을 보내고 받을 수 있도록 하는 응용 프로그램 규칙 컬렉션을 만듭니다.

1. Azure 포털에서 새 방화벽 **테스트-FW01을** 선택합니다.

1. **설정** > **규칙** > **응용 프로그램 규칙 모음으로** > 이동 + 응용 프로그램 규칙 컬렉션**추가.**

    ![제목: 응용 프로그램 규칙 컬렉션 추가](./media/hdinsight-restrict-outbound-traffic/hdinsight-restrict-outbound-traffic-add-app-rule-collection.png)

1. 응용 **프로그램 규칙 컬렉션 추가** 화면에서 다음 정보를 제공합니다.

    **맨 위 섹션**

    | 속성|  값|
    |---|---|
    |속성| FwAppRule|
    |우선 순위|200|
    |작업|Allow|

    **FQDN 태그 섹션**

    | 속성 | 소스 주소 | FQDN 태그 | 메모 |
    | --- | --- | --- | --- |
    | Rule_1 | * | 윈도우업데이트 및 HD인사이트 | HDI 서비스에 필요 |

    **대상 FQDNs 섹션**

    | 속성 | 소스 주소 | 프로토콜:포트 | 대상 FQDNS | 메모 |
    | --- | --- | --- | --- | --- |
    | Rule_2 | * | https:443 | login.windows.net | Windows 로그인 작업 허용 |
    | Rule_3 | * | https:443 | login.microsoftonline.com | Windows 로그인 작업 허용 |
    | Rule_4 | * | https:443,http:80 | storage_account_name.blob.core.windows.net | 실제 `storage_account_name` 저장소 계정 이름으로 바꿉니다. 클러스터가 WASB로 지원되는 경우 WASB에 대한 규칙을 추가합니다. https 연결만 사용하려면 저장소 계정에서 ["보안 전송 필요"가](../storage/common/storage-require-secure-transfer.md) 활성화되어 있는지 확인합니다. |

   ![제목: 응용 프로그램 규칙 컬렉션 세부 정보를 입력합니다.](./media/hdinsight-restrict-outbound-traffic/hdinsight-restrict-outbound-traffic-add-app-rule-collection-details.png)

1. **추가**를 선택합니다.

### <a name="configure-the-firewall-with-network-rules"></a>네트워크 규칙으로 방화벽 구성

네트워크 규칙을 만들어 HDInsight 클러스터를 올바르게 구성합니다.

1. 이전 단계에서 계속, **네트워크 규칙 컬렉션으로** > 이동 **+ 네트워크 규칙 컬렉션 추가**.

1. 네트워크 **규칙 수집 추가** 화면에서 다음 정보를 제공합니다.

    **맨 위 섹션**

    | 속성|  값|
    |---|---|
    |속성| FwNetRule|
    |우선 순위|200|
    |작업|Allow|

    **IP 주소 섹션**

    | 속성 | 프로토콜 | 소스 주소 | 목적지 주소 | 대상 포트 | 메모 |
    | --- | --- | --- | --- | --- | --- |
    | Rule_1 | UDP | * | * | 123 | 시간 서비스 |
    | Rule_2 | 모두 | * | DC_IP_Address_1, DC_IP_Address_2 | * | ESP(엔터프라이즈 보안 패키지)를 사용하는 경우 IP 주소 섹션에 ESP 클러스터에 대한 AAD-DS와의 통신을 허용하는 네트워크 규칙을 추가합니다. 포털의 AAD-DS 섹션에서 도메인 컨트롤러의 IP 주소를 찾을 수 있습니다. |
    | Rule_3 | TCP | * | 데이터 레이크 스토리지 계정의 IP 주소 | * | Azure Data Lake Storage를 사용하는 경우 IP 주소 섹션에 네트워크 규칙을 추가하여 ADLS Gen1 및 Gen2의 SNI 문제를 해결할 수 있습니다. 이 옵션은 트래픽을 방화벽으로 라우팅하여 대규모 데이터 로드에 대한 비용이 증가할 수 있지만 트래픽은 방화벽 로그에 기록되고 감사할 수 있습니다. 데이터 레이크 스토리지 계정의 IP 주소를 확인합니다. FQDN을 IP 주소로 해결하는 것과 같은 `[System.Net.DNS]::GetHostAddresses("STORAGEACCOUNTNAME.blob.core.windows.net")` powershell 명령을 사용할 수 있습니다.|
    | Rule_4 | TCP | * | * | 12000 | (선택 사항) 로그 애널리틱스를 사용하는 경우 IP 주소 섹션에서 네트워크 규칙을 만들어 Log Analytics 작업 영역과의 통신을 활성화합니다. |

    **서비스 태그 섹션**

    | 속성 | 프로토콜 | 원본 주소 | 서비스 태그 | 대상 포트 | 메모 |
    | --- | --- | --- | --- | --- | --- |
    | Rule_7 | TCP | * | SQL | 1433 | 방화벽을 우회하는 HDInsight 서브넷에서 SQL Server용 서비스 엔드포인트를 구성하지 않는 한 SQL 트래픽에 대한 서비스 태그 섹션을 구성하여 SQL 트래픽을 기록하고 감사할 수 있도록 하는 네트워크 규칙을 구성합니다. |

   ![제목: 응용 프로그램 규칙 컬렉션 입력](./media/hdinsight-restrict-outbound-traffic/hdinsight-restrict-outbound-traffic-add-network-rule-collection.png)

1. **추가**를 선택합니다.

### <a name="create-and-configure-a-route-table"></a>경로 테이블 만들기 및 구성

다음 항목으로 경로 테이블을 만듭니다.

* 상태 및 관리 서비스의 모든 IP [주소:](../hdinsight/hdinsight-management-ip-addresses.md#health-and-management-services-all-regions) 다음 홉 유형의 **인터넷이**있는 모든 지역 .

* [클러스터가 상태 및 관리 서비스에서](../hdinsight/hdinsight-management-ip-addresses.md#health-and-management-services-specific-regions) 생성되는 리전의 두 IP 주소: 다음 홉 유형의 **인터넷이**있는 특정 지역.

* IP 주소 0.0.0.0/0에 대한 하나의 가상 어플라이언스 경로로 다음 홉이 Azure 방화벽 개인 IP 주소입니다.

예를 들어 미국 "미국 동부"지역에서 만든 클러스터에 대한 경로 테이블을 구성하려면 다음 단계를 사용합니다.

1. Azure 방화벽 **테스트-FW01을**선택합니다. 개요 페이지에 나열된 **비공개 IP 주소를** **복사합니다.** 이 예제에서는 **10.0.2.4 의 샘플 주소를**사용합니다.

1. 그런 다음 **모든 서비스** > **네트워킹** > **경로 테이블** 및 경로 테이블 **만들기로**이동합니다.

1. 새 경로에서 **설정** > **경로로** > 이동 **+ 추가**. 다음 경로를 추가합니다.

| 경로 이름 | 주소 접두사 | 다음 홉 유형 | 다음 홉 주소 |
|---|---|---|---|
| 168.61.49.99 | 168.61.49.99/32 | 인터넷 | 해당 없음 |
| 23.99.5.239 | 23.99.5.239/32 | 인터넷 | 해당 없음 |
| 168.61.48.131 | 168.61.48.131/32 | 인터넷 | 해당 없음 |
| 138.91.141.162 | 138.91.141.162/32 | 인터넷 | 해당 없음 |
| 13.82.225.233 | 13.82.225.233/32 | 인터넷 | 해당 없음 |
| 40.71.175.99 | 40.71.175.99/32 | 인터넷 | 해당 없음 |
| 0.0.0.0 | 0.0.0.0/0 | 가상 어플라이언스 | 10.0.2.4 |

경로 테이블 구성완료:

1. **설정에서** **서브넷을** 선택하여 HDInsight 서브넷에 만든 경로 테이블을 할당합니다.

1. **+ 연결**을 선택합니다.

1. **어소시에이트 서브넷** 화면에서 클러스터가 생성된 가상 네트워크와 HDInsight 클러스터에 사용한 **서브넷을** 선택합니다.

1. **확인**을 선택합니다.

## <a name="edge-node-or-custom-application-traffic"></a>에지 노드 또는 사용자 지정 응용 프로그램 트래픽

위의 단계를 통해 클러스터가 문제 없이 작동할 수 있습니다. 해당하는 경우 에지 노드에서 실행되는 사용자 지정 응용 프로그램을 수용하도록 종속성을 구성해야 합니다.

응용 프로그램 종속성을 식별하고 Azure 방화벽 또는 경로 테이블에 추가해야 합니다.

비대칭 라우팅 문제를 방지하려면 응용 프로그램 트래픽에 대해 경로를 만들어야 합니다.

응용 프로그램에 다른 종속성이 있는 경우 Azure 방화벽에 추가해야 합니다. 다른 모든 항목에 대해 HTTP/HTTPS 트래픽 및 네트워크 규칙을 허용하는 애플리케이션 규칙을 만듭니다.

## <a name="logging-and-scale"></a>로깅 및 배율 조정

Azure 방화벽은 몇 가지 다른 저장소 시스템에 로그를 보낼 수 있습니다. 방화벽에 대한 로깅 구성에 대한 지침은 [자습서: Azure 방화벽 로그 및 메트릭 모니터링의 단계를 따릅니다.](../firewall/tutorial-diagnostics.md)

로깅 설정을 완료한 후 로그 애널리틱스에 데이터를 로깅하는 경우 다음과 같은 쿼리를 사용하여 차단된 트래픽을 볼 수 있습니다.

```Kusto
AzureDiagnostics | where msg_s contains "Deny" | where TimeGenerated >= ago(1h)
```

Azure 방화벽을 Azure Monitor 로그와 통합하면 모든 응용 프로그램 종속성을 인식하지 못하는 경우 응용 프로그램이 처음 작동할 때 유용합니다. Azure Monitor 로그에 대한 자세한 내용은 [Azure Monitor에서 로그 데이터 분석](../azure-monitor/log-query/log-query-overview.md)을 참조하세요.

Azure 방화벽의 확장 제한 및 요청 증가에 대해 알아보려면 [이](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-firewall-limits) 문서를 참조하거나 [FAQ를](../firewall/firewall-faq.md)참조하십시오.

## <a name="access-to-the-cluster"></a>클러스터에 대한 액세스

방화벽을 성공적으로 설정한 후 내부 끝점()을`https://CLUSTERNAME-int.azurehdinsight.net`사용하여 가상 네트워크 내부에서 Ambari에 액세스할 수 있습니다.

공용 끝점 ()`https://CLUSTERNAME.azurehdinsight.net`또는 ssh 끝점 (ssh`CLUSTERNAME-ssh.azurehdinsight.net`endpoint)을 사용하려면 [여기에](../firewall/integrate-lb.md)설명된 비대칭 라우팅 문제를 피하기 위해 경로 테이블및 NSG 규칙에 올바른 경로가 있는지 확인하십시오. 특히 이 경우 인바운드 NSG 규칙의 클라이언트 IP 주소를 허용하고 다음 홉 집합을 으로 `internet`설정하여 사용자 정의 경로 테이블에 추가해야 합니다. 올바르게 설정되지 않으면 시간 지정 오류가 표시됩니다.

## <a name="configure-another-network-virtual-appliance"></a>다른 네트워크 가상 어플라이언스 구성

> [!Important]
> 다음 **정보는** Azure 방화벽 이외 NVA(네트워크 가상 어플라이언스)를 구성하려는 경우에만 필요합니다.

이전 지침은 HDInsight 클러스터에서 아웃바운드 트래픽을 제한하기 위해 Azure 방화벽을 구성하는 데 도움이 됩니다. Azure 방화벽은 많은 일반적인 중요한 시나리오에 대한 트래픽을 허용하도록 자동으로 구성됩니다. 다른 네트워크 가상 어플라이언스를 사용하려면 여러 가지 추가 기능을 수동으로 구성해야 합니다. 네트워크 가상 어플라이언스를 구성할 때 다음 사항을 염두에 두십시오.

* 서비스 엔드포인트 지원 서비스는 서비스 엔드포인트로 구성되어야 합니다.
* IP 주소 종속성은 HTTP/S가 아닌 트래픽(TCP 및 UDP 트래픽 모두)에 대한 것입니다.
* FQDN HTTP/HTTPS 엔드포인트는 NVA 장치에 배치할 수 있습니다.
* 와일드카드 HTTP/HTTPS 끝점은 여러 한정자에 따라 달라질 수 있는 종속성입니다.
* HDInsight 서브넷에 만든 경로 테이블을 할당합니다.

### <a name="service-endpoint-capable-dependencies"></a>서비스 엔드포인트 가능 종속성

| **끝점** |
|---|
| Azure SQL |
| Azure Storage |
| Azure Active Directory |

#### <a name="ip-address-dependencies"></a>IP 주소 종속성

| **끝점** | **세부 정보** |
|---|---|
| \*:123 | NTP 클록 확인. 트래픽이 포트 123의 여러 엔드포인트에서 확인됩니다. |
| [여기에](hdinsight-management-ip-addresses.md) 게시된 IP | 이들은 HDInsight 서비스입니다 |
| ESP 클러스터를 위한 AAD-DS 개인 IP |
| \*:16800 KMS 윈도우 활성화용 |
| \*로그 분석용 12000 |

#### <a name="fqdn-httphttps-dependencies"></a>FQDN HTTP/HTTPS 종속성

> [!Important]
> 아래 목록은 가장 중요한 FQDNs 중 몇 개만 제공합니다. [이 파일에서](https://github.com/Azure-Samples/hdinsight-fqdn-lists/blob/master/HDInsightFQDNTags.json)NVA를 구성하기 위한 추가 FQDNs(주로 Azure 저장소 및 Azure Service Bus)를 얻을 수 있습니다.

| **끝점**                                                          |
|---|
| azure.archive.ubuntu.com:80                                           |
| security.ubuntu.com:80                                                |
| ocsp.msocsp.com:80                                                    |
| ocsp.digicert.com:80                                                  |
| wawsinfraprodbay063.blob.core.windows.net:443                         |
| registry-1.docker.io:443                                              |
| auth.docker.io:443                                                    |
| production.cloudflare.docker.com:443                                  |
| download.docker.com:443                                               |
| us.archive.ubuntu.com:80                                              |
| download.mono-project.com:80                                          |
| packages.treasuredata.com:80                                          |
| security.ubuntu.com:80                                                |
| azure.archive.ubuntu.com:80                                           |
| ocsp.msocsp.com:80                                                    |
| ocsp.digicert.com:80                                                  |

## <a name="next-steps"></a>다음 단계

* [Azure HDInsight 가상 네트워크 아키텍처](hdinsight-virtual-network-architecture.md)
