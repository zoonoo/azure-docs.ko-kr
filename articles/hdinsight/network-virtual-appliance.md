---
title: Azure HDInsight에서 네트워크 가상 어플라이언스 구성
description: Azure HDInsight에서 네트워크 가상 어플라이언스에 대 한 다양 한 추가 기능을 구성 하는 방법에 대해 알아봅니다.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.date: 06/30/2020
ms.openlocfilehash: 557f0a6a37747d3a461ced8de16fd1fcf0d1abab
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2020
ms.locfileid: "95524116"
---
# <a name="configure-network-virtual-appliance-in-azure-hdinsight"></a>Azure HDInsight에서 네트워크 가상 어플라이언스 구성

> [!Important]
> 다음 정보는 [Azure 방화벽](./hdinsight-restrict-outbound-traffic.md)이외의 nva (네트워크 가상 어플라이언스)를 구성 하려는 경우에 **만** 필요 합니다.

Azure 방화벽 FQDN 태그는 여러 가지 중요 한 Fqdn에 대 한 트래픽을 허용 하도록 자동으로 구성 됩니다. 다른 네트워크 가상 어플라이언스를 사용 하려면 몇 가지 추가 기능을 구성 해야 합니다. 네트워크 가상 어플라이언스를 구성할 때 다음 요소를 염두에 두어야 합니다.

* 서비스 끝점을 사용 하 여 서비스 끝점을 구성할 수 있습니다. 이러한 서비스 끝점은 일반적으로 비용 또는 성능 고려 사항에 대해 NVA를 우회 합니다.
* ResourceProviderConnection이 *아웃 바운드* 로 설정 된 경우 metastore에 대 한 저장소 및 SQL server에 대 한 개인 끝점을 사용할 수 있으며 nva에 추가 하지 않아도 됩니다.
* IP 주소 종속성은 비 HTTP/S 트래픽 (TCP 및 UDP 트래픽 모두)을 위한 것입니다.
* NVA 장치에서 FQDN HTTP/HTTPS 끝점을 승인할 수 있습니다.
* 만든 경로 테이블을 HDInsight 서브넷에 할당 합니다.

## <a name="service-endpoint-capable-dependencies"></a>서비스 끝점 지원 종속성

필요에 따라 NVA를 우회 하는 다음 서비스 끝점을 하나 이상 사용할 수 있습니다. 이 옵션은 비용을 절약 하 고 성능 최적화를 위해 많은 양의 데이터를 전송 하는 데 유용할 수 있습니다. 

| **엔드포인트** |
|---|
| Azure SQL |
| Azure Storage |
| Azure Active Directory |

### <a name="ip-address-dependencies"></a>IP 주소 종속성

| **엔드포인트** | **세부 정보** |
|---|---|
| [여기](hdinsight-management-ip-addresses.md) 에 게시 된 ip | 이러한 Ip는 HDInsight 리소스 공급자를 위한 것 이며 비대칭 라우팅을 방지 하려면 UDR에 포함 되어야 합니다. 이 규칙은 ResourceProviderConnection이 *Inbound* 로 설정 된 경우에만 필요 합니다. ResourceProviderConnection이 *아웃 바운드* 로 설정 된 경우 이러한 IP는 udr에 필요 하지 않습니다.  |
| AAD-DS 개인 Ip | Vnet이 피어 링가 아닌 경우에만 ESP 클러스터에 필요 합니다.|


### <a name="fqdn-httphttps-dependencies"></a>FQDN HTTP/HTTPS 종속성

[이](https://github.com/Azure-Samples/hdinsight-fqdn-lists/)리포지토리에서 nva를 구성 하기 위해 fqdn 종속성 목록을 가져올 수 있습니다 (대부분 Azure Storage 및 Azure Service Bus). 지역 목록에 대해서는 [여기](https://github.com/Azure-Samples/hdinsight-fqdn-lists/tree/master/Regional)를 참조 하세요. 이러한 종속성은 RP (HDInsight 리소스 공급자)에서 클러스터를 성공적으로 만들고 모니터링/관리 하는 데 사용 됩니다. 여기에는 원격 분석/진단 로그, 프로 비전 메타 데이터, 클러스터 관련 구성, 스크립트 등이 포함 됩니다. 이 FQDN 종속성 목록은 향후 HDInsight 업데이트 해제로 변경 될 수 있습니다.

아래 목록에는 클러스터를 만든 *후* 클러스터 작업 수명 동안 OS 및 보안 패치 또는 인증서 유효성 검사에 필요할 수 있는 몇 가지 fqdn이 있습니다.

| **런타임 종속성 Fqdn**                                                          |
|---|
| azure.archive.ubuntu.com:80                                           |
| security.ubuntu.com:80                                                |
| ocsp.msocsp.com:80                                                    |
| ocsp.digicert.com:80                                                  |
| microsoft.com/pki/mscorp/cps/default.htm:443                                      |
| microsoft.com:80                                                      |
|login.windows.net:443                                                  |
|login.microsoftonline.com:443                                          |

## <a name="next-steps"></a>다음 단계

* [아웃바운드 트래픽을 제한하는 방화벽 사용](./hdinsight-restrict-outbound-traffic.md)
* [Azure HDInsight 가상 네트워크 아키텍처](hdinsight-virtual-network-architecture.md)
