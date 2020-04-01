---
title: Azure HDInsight에 대한 NSG(네트워크 보안 그룹) 서비스 태그
description: HDInsight 서비스 태그를 사용하여 NSG에 IP 주소를 추가하지 않고도 상태 및 관리 서비스 노드에서 클러스터로의 인바운드 트래픽을 허용합니다.
author: hrasheed-msft
ms.author: hrasheed
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 03/10/2020
ms.openlocfilehash: 34ec05a8362f5947cb61924b19c6b1a52e5d91a4
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/31/2020
ms.locfileid: "80437666"
---
# <a name="nsg-service-tags-for-azure-hdinsight"></a>Azure HDInsight에 대한 NSG 서비스 태그

NSG(네트워크 보안 그룹)에 대한 Azure HDInsight 서비스 태그는 상태 및 관리 서비스에 대한 IP 주소 그룹입니다. 이러한 그룹은 보안 규칙 만들기의 복잡성을 최소화하는 데 도움이 됩니다. [서비스 태그는](../virtual-network/security-overview.md#service-tags) NSG의 각 [관리 IP 주소를](hdinsight-management-ip-addresses.md) 입력하지 않고 특정 IP 주소의 인바운드 트래픽을 허용하는 대체 방법을 제공합니다.

HDInsight 서비스는 이러한 서비스 태그를 관리합니다. 사용자 고유의 서비스 태그를 만들거나 기존 태그를 수정할 수 없습니다. Microsoft는 서비스 태그와 일치하는 주소 접두사를 관리하고 주소가 변경될 때 서비스 태그를 자동으로 업데이트합니다.

## <a name="get-started-with-service-tags"></a>서비스 태그 시작

네트워크 보안 그룹에서 서비스 태그를 사용하는 두 가지 옵션이 있습니다.

- **단일 글로벌 HDInsight 서비스 태그 사용**: 이 옵션은 HDInsight 서비스가 모든 지역에서 클러스터를 모니터링하는 데 사용하는 모든 IP 주소에 가상 네트워크를 엽니다. 이 옵션은 가장 간단한 방법이지만 제한적인 보안 요구 사항이 있는 경우 적절하지 않을 수 있습니다.

- **여러 지역 서비스 태그 사용**: 이 옵션은 HDInsight가 해당 지역에서 사용하는 IP 주소에만 가상 네트워크를 엽니다. 그러나 여러 지역을 사용하는 경우 가상 네트워크에 여러 서비스 태그를 추가해야 합니다.

## <a name="use-a-single-global-hdinsight-service-tag"></a>단일 글로벌 HDInsight 서비스 태그 사용

HDInsight 클러스터에서 서비스 태그를 사용하는 가장 쉬운 방법은 NSG `HDInsight` 규칙에 전역 태그를 추가하는 것입니다.

1. Azure [포털에서](https://portal.azure.com/)네트워크 보안 그룹을 선택합니다.

1. **설정에서** **인바운드 보안 규칙을**선택한 다음 **+ 추가**를 선택합니다.

1. **소스** 드롭다운 목록에서 서비스 **태그**를 선택합니다.

1. 소스 **서비스 태그** 드롭다운 목록에서 **HDInsight**를 선택합니다.

    ![Azure 포털에서 서비스 태그 추가](./media/hdinsight-service-tags/azure-portal-add-service-tag.png)

이 태그에는 HDInsight를 사용할 수 있는 모든 지역의 상태 및 관리 서비스의 IP 주소가 포함되어 있습니다. 태그는 클러스터가 생성된 위치에 관계없이 필요한 상태 및 관리 서비스와 통신할 수 있도록 합니다.

## <a name="use-regional-hdinsight-service-tags"></a>지역 HDInsight 서비스 태그 사용

더 제한적인 권한이 필요하기 때문에 전역 태그 옵션이 작동하지 않는 경우 해당 지역에 적용되는 서비스 태그만 허용할 수 있습니다. 클러스터가 생성되는 지역에 따라 1개, 2개 또는 3개의 해당 서비스 태그가 있을 수 있습니다.

해당 지역에 추가할 서비스 태그를 확인하려면 문서의 다음 섹션을 읽어보세요.

### <a name="use-a-single-regional-service-tag"></a>단일 지역 서비스 태그 사용

지역 서비스 태그를 사용하려는 경우 클러스터가 이 테이블에 나열된 지역 중 하나에 있는 경우 네트워크 보안 그룹에 단일 지역 서비스 태그를 추가하기만 하면 됩니다.

| 국가 | 지역 | 서비스 태그 |
| ---- | ---- | ---- |
| 오스트레일리아 | 오스트레일리아 동부 | HDInsight.오스트레일리아이스트 |
| &nbsp; | 오스트레일리아 남동부 | HDInsight.오스트레일리아남동부 |
| &nbsp; | 오스트레일리아 중부 | HDInsight.오스트레일리아센트럴 |
| 중국 | 중국 동부 2 | HDInsight.차이나이스트2 |
| &nbsp; | 중국 북부 2 | HDInsight.차이나노스2 |
| 미국 | 미국 중북부 | HDInsight.노스센트럴US |
| &nbsp; | 미국 서부 2 | HDInsight.WestUS2 |
| &nbsp; | 미국 중서부 | HDInsight.웨스트센트럴US |
| Canada | 캐나다 동부 | HDInsight.캐나다이스트 |
| 브라질 | 브라질 남부 | HDInsight.브라질사우스 |
| 한국 | 한국 중부 | HDInsight.한국중부 |
| &nbsp; | 한국 남부 | HDInsight.Korea한국 |
| 인도 | 인도 중부 | HDInsight.센트럴인디아 |
| &nbsp; | 인도 남부 | HDInsight.사우스인디아 |
| 일본 | 일본 서부 | HDInsight.재팬웨스트 |
| 프랑스 | 프랑스 중부| HDInsight.프랑스센트럴 |
| 영국 | 영국 남부 | HDInsight.UKSouth |
| Azure Government | USDoD 센트럴 | HDInsight.USDoDCentral |
| &nbsp; | USGov 텍사스 | HDInsight.USGov텍사스 |
| &nbsp; | 우스도드 이스트 | HDInsight.USDoDEast |
| &nbsp; | USGov 애리조나 | HDInsight.USGov애리조나 |

### <a name="use-multiple-regional-service-tags"></a>여러 지역 서비스 태그 사용

지역 서비스 태그를 사용하지만 클러스터가 생성된 지역이 이전 표에 나열되지 않은 경우 여러 지역 서비스 태그를 허용해야 합니다. 둘 이상의 사용을 해야 하는 이유는 다양한 지역에 대한 리소스 공급자의 배열의 차이 때문입니다.

나머지 지역은 사용하는 지역 서비스 태그를 기반으로 그룹으로 나뉩니다.

#### <a name="group-1"></a>그룹 1

다음 표의 지역 중 하나에서 클러스터를 만든 경우 서비스 `HDInsight.WestUS` 태그와 `HDInsight.EastUS` 나열된 지역 서비스 태그 외에 허용합니다. 이 섹션의 지역에는 세 개의 서비스 태그가 필요합니다.

예를 `East US 2` 들어, 지역에서 클러스터를 만든 경우 네트워크 보안 그룹에 다음 서비스 태그를 추가해야 합니다.

- `HDInsight.EastUS2`
- `HDInsight.WestUS`
- `HDInsight.EastUS`

| 국가 | 지역 | 서비스 태그 |
| ---- | ---- | ---- |
| 미국 | 미국 동부 2 | HDInsight.EastUS2 |
| &nbsp; | 미국 중부 | HDInsight.센트럴US |
| &nbsp; | 미국 중부 중부 | Hdinsight. 노스센트럴US |
| &nbsp; | 미국 중남부 | HDInsight.사우스센트럴US |
| &nbsp; | 미국 동부 | HDInsight.EastUS |
| &nbsp; | 미국 서부 | HDInsight.WestUS |
| 일본 | 일본 동부 | HDInsight.일본동 |
| 유럽 | 북유럽 | HDInsight.북유럽 |
| &nbsp; | 서유럽| HDInsight.웨스트유럽 |
| 아시아 | 동아시아 | HDInsight.이스트아시아 |
| &nbsp; | 동남아시아 | HDInsight.동남아시아 |
| 오스트레일리아 | 오스트레일리아 동부 | HDInsight.오스트레일리아이스트 |

#### <a name="group-2"></a>그룹 2

*중국 북부* 및 *중국 동부* 지역의 클러스터는 두 개의 `HDInsight.ChinaNorth` 서비스 `HDInsight.ChinaEast`태그를 허용해야 합니다.

#### <a name="group-3"></a>그룹 3

*미국 정부 아이오와* 및 미국 *정부 버지니아* 지역의 클러스터는 두 `HDInsight.USGovIowa` 개의 `HDInsight.USGovVirginia`서비스 태그를 허용해야 합니다.

#### <a name="group-4"></a>그룹 4

*독일 중부* 및 *독일 북동부* 지역의 클러스터는 두 개의 `HDInsight.GermanyCentral` 서비스 `HDInsight.GermanyNortheast`태그를 허용해야 합니다.

## <a name="next-steps"></a>다음 단계

- [네트워크 보안 그룹: 서비스 태그](../virtual-network/security-overview.md#security-rules)
- [Azure HDInsight 클러스터를 위한 가상 네트워크 만들기](hdinsight-create-virtual-network.md)
