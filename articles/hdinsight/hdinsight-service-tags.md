---
title: Azure HDInsight에 대 한 NSG (네트워크 보안 그룹) 서비스 태그
description: HDInsight 서비스 태그를 사용 하 여 네트워크 보안 그룹에 IP 주소를 명시적으로 추가 하지 않고도 HDInsight 상태 및 관리 서비스 노드에서 클러스터로의 인바운드 트래픽을 허용할 수 있습니다.
author: hrasheed-msft
ms.author: hrasheed
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 10/19/2019
ms.openlocfilehash: 7e3ce33bdf0773ababe5eb190877a9288c094c5c
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74187086"
---
# <a name="network-security-group-nsg-service-tags-for-azure-hdinsight"></a>Azure HDInsight에 대 한 NSG (네트워크 보안 그룹) 서비스 태그

NSGs (네트워크 보안 그룹)에 대 한 HDInsight 서비스 태그는 상태 및 관리 서비스에 대 한 IP 주소 그룹입니다. 이러한 그룹은 보안 규칙 생성의 복잡성을 최소화 하는 데 도움이 됩니다. [서비스 태그](../virtual-network/security-overview.md#service-tags) 는 네트워크 보안 그룹에 각 [관리 ip 주소](hdinsight-management-ip-addresses.md) 를 입력 하지 않고도 특정 IP 주소에서 인바운드 트래픽을 허용 하는 대체 방법을 제공 합니다.

이러한 서비스 태그는 HDInsight 서비스에서 만들고 관리 합니다. 사용자 고유의 서비스 태그를 만들거나 기존 태그를 수정할 수 없습니다. Microsoft는 서비스 태그와 일치 하는 주소 접두사를 관리 하 고 주소가 변경 되 면 서비스 태그를 자동으로 업데이트 합니다.

## <a name="getting-started-with-service-tags"></a>서비스 태그 시작

네트워크 보안 그룹에서 서비스 태그를 사용 하는 방법에는 두 가지가 있습니다.

1. 단일 HDInsight 서비스 태그 사용-이 옵션은 HDInsight 서비스가 모든 지역에서 클러스터를 모니터링 하는 데 사용 하는 모든 IP 주소에 대 한 가상 네트워크를 엽니다. 이 옵션은 가장 간단한 방법 이지만 보안 요구 사항을 제한 하는 경우에는 적합 하지 않을 수 있습니다.

1. 여러 지역 서비스 태그 사용-이 옵션은 HDInsight가 해당 특정 지역에서 사용 하는 IP 주소에 대해서만 가상 네트워크를 엽니다. 그러나 여러 지역을 사용 하는 경우에는 가상 네트워크에 여러 서비스 태그를 추가 해야 합니다.

## <a name="use-a-single-global-hdinsight-service-tag"></a>단일 글로벌 HDInsight 서비스 태그 사용

HDInsight 클러스터에서 서비스 태그 사용을 시작 하는 가장 쉬운 방법은 `HDInsight` 전역 태그를 네트워크 보안 그룹 규칙에 추가 하는 것입니다. 네트워크 보안 그룹에 서비스 태그를 추가 하는 방법에 대 한 지침은 [보안 그룹: 서비스 태그](../virtual-network/security-overview.md#service-tags)를 참조 하세요.

이 태그에는 HDInsight를 사용할 수 있는 모든 지역에 대 한 상태 및 관리 서비스의 IP 주소가 포함 되어 있으며, 클러스터가 만들어진 위치에 상관 없이 클러스터가 필요한 상태 및 관리 서비스와 통신할 수 있도록 합니다.

## <a name="use-regional-hdinsight-service-tags"></a>지역 HDInsight 서비스 태그 사용

더 제한적인 사용 권한이 필요 하기 때문에 옵션 하나가 작동 하지 않는 경우 해당 지역에 적용 되는 서비스 태그만 허용할 수 있습니다. 해당 서비스 태그는 클러스터가 생성 된 지역에 따라 1, 2 또는 3 개의 서비스 태그 일 수 있습니다.

영역에 추가할 서비스 태그를 확인 하려면 문서의 다음 섹션을 참조 하세요.

### <a name="use-a-single-regional-service-tag"></a>단일 지역 서비스 태그 사용

서비스 태그 옵션을 2로 설정 하 고 클러스터가이 표에 나열 된 지역 중 하나에 있는 경우 단일 지역 서비스 태그를 네트워크 보안 그룹에 추가 하기만 하면 됩니다.

| 국가 | Region | 서비스 태그 |
| ---- | ---- | ---- |
| 오스트레일리아 | 오스트레일리아 동부 | AustraliaEast |
| &nbsp; | 오스트레일리아 남동부 | AustraliaSoutheast |
| &nbsp; | 오스트레일리아 중부 | AustraliaCentral |
| 중국 | 중국 동부 2 | ChinaEast2 |
| &nbsp; | 중국 북부 2 | ChinaNorth2 |
| 미국 | 미국 중북부 | NorthCentralUS |
| &nbsp; | 미국 서부 2 | WestUS2 |
| &nbsp; | 미국 중서부 | WestCentralUS |
| 캐나다 | 캐나다 동부 | CanadaEast |
| 브라질 | 브라질 남부 | BrazilSouth |
| 한국 | 한국 중부 | KoreaCentral |
| &nbsp; | 한국 남부 | KoreaSouth |
| 인도 | 인도 중부 | CentralIndia |
| &nbsp; | 인도 남부 | SouthIndia |
| 일본 | 일본 서부 | JapanWest |
| 프랑스 | 프랑스 중부| FranceCentral |
| 영국 | 영국 남부 | Storage.westcentralus |
| Azure Government (Fairfax) | USDoD Central   | USDoDCentral |
| &nbsp; | USGov 텍사스 | HDInsight. USGovTexas |
| &nbsp; | UsDoD 동부 | HDInsight. USDoDEast |

### <a name="use-multiple-regional-service-tags"></a>여러 지역 서비스 태그 사용

서비스 태그 옵션을 2로 설정 하 고 클러스터를 만든 지역이 위에 나열 되지 않은 경우 여러 지역 서비스 태그를 허용 해야 합니다. 여러 영역에 대 한 리소스 공급자의 정렬에 차이가 있기 때문에 두 개 이상의를 사용 해야 합니다.

나머지 지역은 사용 하는 지역 서비스 태그에 따라 그룹으로 구분 됩니다.

#### <a name="group-1"></a>그룹 1

클러스터가 아래 표의 지역 중 하나에 생성 되는 경우 `HDInsight.WestUS` 서비스 태그를 허용 하 고 나열 된 지역 서비스 태그 외에 `HDInsight.EastUS` 합니다. 이 섹션의 영역에는 세 가지 서비스 태그가 필요 합니다.

예를 들어 `East US 2` 지역에서 클러스터를 만든 경우 네트워크 보안 그룹에 다음 서비스 태그를 추가 해야 합니다.

- `HDInsight.EastUS2`
- `HDInsight.WestUS`
- `HDInsight.EastUS`

| 국가 | Region | 서비스 태그 |
| ---- | ---- | ---- |
| 미국 | 미국 동부 2 | EastUS2 |
| &nbsp; | 미국 중부 | CentralUS |
| &nbsp; | 미국 NorthCentral | HDInsight. NorthCentralUS |
| &nbsp; | 미국 중남부 | SouthCentralUS |
| &nbsp; | 미국 동부 | HDInsight. EastUS |
| &nbsp; | 미국 서부 | WestUS |
| 일본 | 일본 동부 | JapanEast |
| Europe | 유럽 북부 | NorthEurope |
| &nbsp; | 서유럽| WestEurope |
| 아시아 | 동아시아 | HDInsight. E한글 |
| &nbsp; | 동남아시아 | SoutheastAsia |
| 오스트레일리아 | 오스트레일리아 동부 | AustraliaEast |

#### <a name="group-2"></a>그룹 2

**중국 북부** 및 **중국 동부**지역의 클러스터는 `HDInsight.ChinaNorth` 및 `HDInsight.ChinaEast`의 두 가지 서비스 태그를 허용 해야 합니다.

#### <a name="group-3"></a>그룹 3

**US Gov 아이오와** 및 **US Gov 버지니아**지역의 클러스터는 `HDInsight.USGovIowa` 및 `HDInsight.USGovVirginia`의 두 가지 서비스 태그를 허용 해야 합니다.

#### <a name="group-4"></a>그룹 4

**독일 중부** 및 **독일 북동쪽**지역의 클러스터는 `HDInsight.GermanyCentral` 및 `HDInsight.GermanyNorthEast`의 두 서비스 태그를 허용 해야 합니다.

## <a name="next-steps"></a>다음 단계

* [네트워크 보안 그룹-서비스 태그](../virtual-network/security-overview.md#security-rules)
* [Azure HDInsight 클러스터에 대 한 가상 네트워크 만들기](hdinsight-create-virtual-network.md)
