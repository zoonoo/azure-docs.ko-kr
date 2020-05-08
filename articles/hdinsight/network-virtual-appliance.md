---
title: Azure HDInsight에서 네트워크 가상 어플라이언스 구성
description: Azure HDInsight에서 네트워크 가상 어플라이언스에 대 한 다양 한 추가 기능을 구성 하는 방법에 대해 알아봅니다.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/06/2020
ms.openlocfilehash: cbc2104ae3c55ae3670867b7a253d812f3a4be0e
ms.sourcegitcommit: 602e6db62069d568a91981a1117244ffd757f1c2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/06/2020
ms.locfileid: "82864709"
---
# <a name="configure-network-virtual-appliance-in-azure-hdinsight"></a>Azure HDInsight에서 네트워크 가상 어플라이언스 구성

> [!Important]
> 다음 정보는 Azure 방화벽 이외의 NVA (네트워크 가상 어플라이언스)를 구성 하려는 경우에 **만** 필요 합니다.

Azure 방화벽은 몇 가지 일반적인 중요 시나리오에 대 한 트래픽을 허용 하도록 자동으로 구성 됩니다. 다른 네트워크 가상 어플라이언스를 사용 하려면 몇 가지 추가 기능을 구성 해야 합니다. 네트워크 가상 어플라이언스를 구성할 때 다음 요소를 염두에 두어야 합니다.

* 서비스 엔드포인트 지원 서비스는 서비스 엔드포인트로 구성되어야 합니다.
* IP 주소 종속성은 비 HTTP/S 트래픽 (TCP 및 UDP 트래픽 모두)을 위한 것입니다.
* NVA 장치에 FQDN HTTP/HTTPS 끝점을 배치할 수 있습니다.
* 와일드 카드 HTTP/HTTPS 끝점은 여러 한정자에 따라 달라질 수 있는 종속성입니다.
* 만든 경로 테이블을 HDInsight 서브넷에 할당 합니다.

## <a name="service-endpoint-capable-dependencies"></a>서비스 끝점 지원 종속성

| **엔드포인트** |
|---|
| Azure SQL |
| Azure Storage |
| Azure Active Directory |

### <a name="ip-address-dependencies"></a>IP 주소 종속성

| **엔드포인트** | **세부 정보** |
|---|---|
| \*:123 | NTP 클록 확인. 트래픽이 포트 123의 여러 엔드포인트에서 확인됩니다. |
| [여기](hdinsight-management-ip-addresses.md) 에 게시 된 ip | 이러한 Ip는 HDInsight 서비스입니다. |
| ESP 클러스터에 대 한 AAD-DS 개인 Ip |
| \*: KMS Windows 정품 인증의 경우 16800 |
| \*12000 Log Analytics |

### <a name="fqdn-httphttps-dependencies"></a>FQDN HTTP/HTTPS 종속성

> [!Important]
> 아래 목록은 가장 중요 한 Fqdn 중 일부를 제공 합니다. [이 파일에서](https://github.com/Azure-Samples/hdinsight-fqdn-lists/blob/master/HDInsightFQDNTags.json)nva를 구성 하기 위해 추가 fqdn (대부분 Azure Storage 및 Azure Service Bus)을 가져올 수 있습니다.

| **엔드포인트**                                                          |
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

* [아웃바운드 트래픽을 제한하는 방화벽 사용](./hdinsight-restrict-outbound-traffic.md)
* [Azure HDInsight 가상 네트워크 아키텍처](hdinsight-virtual-network-architecture.md)
