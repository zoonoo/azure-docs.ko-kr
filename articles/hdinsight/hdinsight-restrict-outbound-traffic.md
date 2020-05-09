---
title: 아웃 바운드 네트워크 트래픽 제한 구성-Azure HDInsight
description: Azure HDInsight 클러스터에 대 한 아웃 바운드 네트워크 트래픽 제한을 구성 하는 방법에 대해 알아봅니다.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: seoapr2020
ms.date: 04/17/2020
ms.openlocfilehash: eaf51f6778d38d236808c3fd809082bc3b2d54b2
ms.sourcegitcommit: 602e6db62069d568a91981a1117244ffd757f1c2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/06/2020
ms.locfileid: "82863436"
---
# <a name="configure-outbound-network-traffic-for-azure-hdinsight-clusters-using-firewall"></a>방화벽을 사용 하 여 Azure HDInsight 클러스터에 대 한 아웃 바운드 네트워크 트래픽 구성

이 문서에서는 Azure 방화벽을 사용 하 여 HDInsight 클러스터에서 아웃 바운드 트래픽을 보호 하는 단계를 제공 합니다. 아래 단계에서는 기존 클러스터에 대 한 Azure 방화벽을 구성 하는 것으로 가정 합니다. 방화벽 뒤에 새 클러스터를 배포 하는 경우 먼저 HDInsight 클러스터 및 서브넷을 만듭니다. 그런 다음이 가이드의 단계를 따릅니다.

## <a name="background"></a>배경

HDInsight 클러스터는 일반적으로 가상 네트워크에 배포 됩니다. 클러스터에는 해당 가상 네트워크 외부의 서비스에 대 한 종속성이 있습니다.

인바운드 트래픽이 필요한 몇 가지 종속성이 있습니다. 인바운드 관리 트래픽은 방화벽 장치를 통해 보낼 수 없습니다. 이 트래픽에 대 한 원본 주소가 알려져 있으며 [여기](hdinsight-management-ip-addresses.md)에 게시 됩니다. 이 정보를 사용 하 여 클러스터에 대 한 인바운드 트래픽을 보호 하는 NSG (네트워크 보안 그룹) 규칙을 만들 수도 있습니다.

HDInsight 아웃 바운드 트래픽 종속성은 Fqdn으로 거의 완전히 정의 되어 있습니다. 여기에는 고정 IP 주소가 없습니다. 정적 주소가 없는 경우 NSGs (네트워크 보안 그룹)에서 클러스터의 아웃 바운드 트래픽을 잠글 수 없습니다. 주소가 변경 되 면 현재 이름 확인 및 사용에 따라 규칙을 설정할 수 없는 경우가 많습니다.

도메인 이름에 따라 아웃 바운드 트래픽을 제어할 수 있는 방화벽으로 아웃 바운드 주소를 보호 합니다. Azure 방화벽은 대상 또는 [fqdn 태그](../firewall/fqdn-tags.md)의 fqdn을 기준으로 아웃 바운드 트래픽을 제한 합니다.

## <a name="configuring-azure-firewall-with-hdinsight"></a>HDInsight를 사용 하 여 Azure 방화벽 구성

Azure 방화벽을 사용 하 여 기존 HDInsight에서 송신을 잠그는 단계에 대 한 요약은 다음과 같습니다.

1. 서브넷을 만듭니다.
1. 방화벽을 만듭니다.
1. 방화벽에 응용 프로그램 규칙 추가
1. 방화벽에 네트워크 규칙을 추가 합니다.
1. 라우팅 테이블을 만듭니다.

### <a name="create-new-subnet"></a>새 서브넷 만들기

클러스터가 있는 가상 네트워크에서 **AzureFirewallSubnet** 이라는 서브넷을 만듭니다.

### <a name="create-a-new-firewall-for-your-cluster"></a>클러스터에 대 한 새 방화벽 만들기

자습서의 **방화벽 배포** [: Azure Portal을 사용 하 여 Azure 방화벽 배포 및 구성](../firewall/tutorial-firewall-deploy-portal.md#deploy-the-firewall)의 단계를 사용 하 여 **FW01** 이라는 방화벽을 만듭니다.

### <a name="configure-the-firewall-with-application-rules"></a>응용 프로그램 규칙을 사용 하 여 방화벽 구성

클러스터가 중요 한 통신을 보내고 받을 수 있도록 하는 응용 프로그램 규칙 컬렉션을 만듭니다.

1. Azure Portal에서 새 방화벽 **FW01** 을 선택 합니다.

1. **설정** > **Rules**규칙 > **응용 프로그램 규칙 컬렉션** > **+ 응용 프로그램 규칙 컬렉션 추가**로 이동 합니다.

    ![제목: 응용 프로그램 규칙 컬렉션 추가](./media/hdinsight-restrict-outbound-traffic/hdinsight-restrict-outbound-traffic-add-app-rule-collection.png)

1. **응용 프로그램 규칙 컬렉션 추가** 화면에서 다음 정보를 제공 합니다.

    **위쪽 섹션**

    | 속성|  값|
    |---|---|
    |속성| FwAppRule|
    |우선 순위|200|
    |작업|Allow|

    **FQDN 태그 섹션**

    | Name | 소스 주소 | FQDN 태그 | 참고 |
    | --- | --- | --- | --- |
    | Rule_1 | * | Windowsupdate.log 및 HDInsight | HDI 서비스에 필요 합니다. |

    **대상 Fqdn 섹션**

    | Name | 원본 주소 | `Protocol:Port` | 대상 FQDN | 참고 |
    | --- | --- | --- | --- | --- |
    | Rule_2 | * | https: 443 | login.windows.net | Windows 로그인 작업을 허용 합니다. |
    | Rule_3 | * | https: 443 | login.microsoftonline.com | Windows 로그인 작업을 허용 합니다. |
    | Rule_4 | * | https: 443, http: 80 | storage_account_name. windows .net | 을 `storage_account_name` 실제 저장소 계정 이름으로 바꿉니다. 클러스터가 WASB에서 지원 되는 경우 WASB에 대 한 규칙을 추가 합니다. Https 연결만 사용 하려면 저장소 계정에 ["보안 전송 필요"](../storage/common/storage-require-secure-transfer.md) 가 설정 되어 있는지 확인 합니다. |

   ![제목: 응용 프로그램 규칙 컬렉션 정보 입력](./media/hdinsight-restrict-outbound-traffic/hdinsight-restrict-outbound-traffic-add-app-rule-collection-details.png)

1. **추가**를 선택합니다.

### <a name="configure-the-firewall-with-network-rules"></a>네트워크 규칙을 사용 하 여 방화벽 구성

HDInsight 클러스터를 올바르게 구성 하는 네트워크 규칙을 만듭니다.

1. 이전 단계를 계속 진행 하 여 **네트워크 규칙 컬렉션** > **+ 네트워크 규칙 컬렉션 추가**로 이동 합니다.

1. **네트워크 규칙 컬렉션 추가** 화면에서 다음 정보를 제공 합니다.

    **위쪽 섹션**

    | 속성|  값|
    |---|---|
    |속성| FwNetRule|
    |우선 순위|200|
    |작업|Allow|

    **IP 주소 섹션**

    | Name | 프로토콜 | 원본 주소 | 대상 주소 | 대상 포트 | 참고 |
    | --- | --- | --- | --- | --- | --- |
    | Rule_1 | UDP | * | * | 123 | 시간 서비스 |
    | Rule_2 | 모두 | * | DC_IP_Address_1, DC_IP_Address_2 | * | Enterprise Security Package (ESP)를 사용 하는 경우 ESP 클러스터에 대해 AAD와 통신할 수 있도록 하는 IP 주소 섹션에서 네트워크 규칙을 추가 합니다. 도메인 컨트롤러의 IP 주소는 포털의 AAD DS 섹션에서 찾을 수 있습니다. |
    | Rule_3 | TCP | * | Data Lake Storage 계정의 IP 주소 | * | Azure Data Lake Storage를 사용 하는 경우 IP 주소 섹션에서 네트워크 규칙을 추가 하 여 ADLS Gen1 및 Gen2의 SNI 문제를 해결할 수 있습니다. 이 옵션은 트래픽을 방화벽으로 라우팅합니다. 이로 인해 큰 데이터 로드 비용이 더 많이 들 수 있지만 방화벽 로그에서 트래픽이 기록 되 고 감사 될 수 있습니다. Data Lake Storage 계정에 대 한 IP 주소를 확인 합니다. 와 `[System.Net.DNS]::GetHostAddresses("STORAGEACCOUNTNAME.blob.core.windows.net")` 같은 PowerShell 명령을 사용 하 여 IP 주소에 대 한 FQDN을 확인할 수 있습니다.|
    | Rule_4 | TCP | * | * | 12000 | 필드 Log Analytics 사용 하는 경우 IP 주소 섹션에서 네트워크 규칙을 만들어 Log Analytics 작업 영역과 통신할 수 있도록 합니다. |

    **서비스 태그 섹션**

    | Name | 프로토콜 | 원본 주소 | 서비스 태그 | 대상 포트 | 참고 |
    | --- | --- | --- | --- | --- | --- |
    | Rule_7 | TCP | * | SQL | 1433 | Sql 트래픽을 기록 하 고 감사 하는 데 사용할 수 있는 SQL에 대 한 서비스 태그 섹션에서 네트워크 규칙을 구성 합니다. HDInsight 서브넷에서 SQL Server에 대 한 서비스 끝점을 구성 하지 않은 경우 방화벽을 무시 합니다. |

   ![제목: 응용 프로그램 규칙 컬렉션 입력](./media/hdinsight-restrict-outbound-traffic/hdinsight-restrict-outbound-traffic-add-network-rule-collection.png)

1. **추가**를 선택합니다.

### <a name="create-and-configure-a-route-table"></a>경로 테이블 만들기 및 구성

다음 항목을 사용 하 여 경로 테이블을 만듭니다.

* [상태 및 관리 서비스](../hdinsight/hdinsight-management-ip-addresses.md#health-and-management-services-all-regions) 의 모든 IP 주소: 다음 홉 유형이 **인터넷**인 모든 지역입니다.

* [상태 및 관리 서비스](../hdinsight/hdinsight-management-ip-addresses.md#health-and-management-services-specific-regions) 에서 클러스터가 생성 되는 지역에 대 한 두 개의 IP 주소: 다음 홉 유형이 **인터넷**인 특정 지역.

* IP 주소 0.0.0.0/0에 대 한 가상 어플라이언스 경로 하나는 Azure 방화벽 개인 IP 주소를 사용 하는 다음 홉입니다.

예를 들어 "미국 동부"의 미국 지역에서 만든 클러스터에 대 한 경로 테이블을 구성 하려면 다음 단계를 사용 합니다.

1. Azure 방화벽 **테스트-FW01**를 선택 합니다. **개요** 페이지에 나열 된 **개인 IP 주소** 를 복사 합니다. 이 예에서는 **10.0.2.4의 샘플 주소**를 사용 합니다.

1. 그런 다음 **모든 서비스** > **네트워킹** > **경로 테이블로** 이동 하 여 **경로 테이블을 만듭니다**.

1. 새 경로에서 **설정** > **경로** > **+ 추가**로 이동 합니다. 다음 경로를 추가 합니다.

| 경로 이름 | 주소 접두사 | 다음 홉 유형 | 다음 홉 주소 |
|---|---|---|---|
| 168.61.49.99 | 168.61.49.99/32 | 인터넷 | 해당 없음 |
| 23.99.5.239 | 23.99.5.239/32 | 인터넷 | 해당 없음 |
| 168.61.48.131 | 168.61.48.131/32 | 인터넷 | 해당 없음 |
| 138.91.141.162 | 138.91.141.162/32 | 인터넷 | 해당 없음 |
| 13.82.225.233 | 13.82.225.233/32 | 인터넷 | 해당 없음 |
| 40.71.175.99 | 40.71.175.99/32 | 인터넷 | 해당 없음 |
| 0.0.0.0 | 0.0.0.0/0 | 가상 어플라이언스 | 10.0.2.4 |

경로 테이블 구성을 완료 합니다.

1. **설정**아래에서 **서브넷** 을 선택 하 여 만든 경로 테이블을 HDInsight 서브넷에 할당 합니다.

1. **+ 연결**을 선택 합니다.

1. **서브넷 연결** 화면에서 클러스터가 만들어진 가상 네트워크를 선택 합니다. 그리고 HDInsight 클러스터에 사용 하는 **서브넷** 입니다.

1. **확인**을 선택합니다.

## <a name="edge-node-or-custom-application-traffic"></a>에 지 노드 또는 사용자 지정 응용 프로그램 트래픽

위의 단계를 수행 하면 클러스터에서 문제 없이 작동할 수 있습니다. 해당 하는 경우에는에 지 노드에서 실행 중인 사용자 지정 응용 프로그램을 수용 하기 위해 종속성을 구성 해야 합니다.

응용 프로그램 종속성을 식별 하 고 Azure 방화벽 또는 경로 테이블에 추가 해야 합니다.

비대칭 라우팅 문제를 방지 하려면 응용 프로그램 트래픽에 대 한 경로를 만들어야 합니다.

응용 프로그램에 다른 종속성이 있는 경우 Azure 방화벽에 추가 해야 합니다. 다른 모든 항목에 대해 HTTP/HTTPS 트래픽 및 네트워크 규칙을 허용하는 애플리케이션 규칙을 만듭니다.

## <a name="logging-and-scale"></a>로깅 및 크기 조정

Azure 방화벽은 몇 가지 다른 저장소 시스템에 로그를 보낼 수 있습니다. 방화벽에 대 한 로깅을 구성 하는 방법에 대 한 지침은 [자습서: Azure 방화벽 로그 및 메트릭 모니터링](../firewall/tutorial-diagnostics.md)의 단계를 따르세요.

로깅 설정을 완료 한 후 Log Analytics를 사용 하는 경우 다음과 같은 쿼리를 사용 하 여 차단 된 트래픽을 볼 수 있습니다.

```Kusto
AzureDiagnostics | where msg_s contains "Deny" | where TimeGenerated >= ago(1h)
```

Azure 방화벽을 Azure Monitor 로그와 통합 하는 것은 응용 프로그램을 처음 사용할 때 유용 합니다. 특히 모든 응용 프로그램 종속성을 인식 하지 못하는 경우입니다. Azure Monitor 로그에 대한 자세한 내용은 [Azure Monitor에서 로그 데이터 분석](../azure-monitor/log-query/log-query-overview.md)을 참조하세요.

Azure 방화벽의 규모 제한과 요청이 늘어남에 대 한 자세한 내용은 [이](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-firewall-limits) 문서를 참조 하거나 [faq](../firewall/firewall-faq.md)를 참조 하세요.

## <a name="access-to-the-cluster"></a>클러스터에 대 한 액세스

방화벽이 성공적으로 설정 되 면 내부 끝점 (`https://CLUSTERNAME-int.azurehdinsight.net`)을 사용 하 여 가상 네트워크 내에서 Ambari에 액세스할 수 있습니다.

공용 끝점 (`https://CLUSTERNAME.azurehdinsight.net`) 또는 ssh 끝점 (`CLUSTERNAME-ssh.azurehdinsight.net`)을 사용 하려면 [여기](../firewall/integrate-lb.md)에 설명 된 비대칭 라우팅 문제를 방지 하기 위해 경로 테이블 및 nsg 규칙에 올바른 경로가 있는지 확인 합니다. 특히이 경우 인바운드 NSG 규칙의 클라이언트 IP 주소를 허용 하 고 다음 홉이로 `internet`설정 된 사용자 정의 경로 테이블에도 추가 해야 합니다. 라우팅이 올바르게 설정 되지 않은 경우 시간 초과 오류가 표시 됩니다.

## <a name="next-steps"></a>다음 단계

* [Azure HDInsight 가상 네트워크 아키텍처](hdinsight-virtual-network-architecture.md)
* [네트워크 가상 어플라이언스 구성](./network-virtual-appliance.md)