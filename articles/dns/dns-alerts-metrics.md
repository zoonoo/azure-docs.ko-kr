---
title: 메트릭 및 경고 - Azure DNS
description: 이 학습 경로를 사용하여 Azure DNS 메트릭 및 경고를 시작하세요.
services: dns
documentationcenter: na
author: rohinkoul
manager: kumudD
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/26/2021
ms.author: rohink
ms.openlocfilehash: f2eaac432673682b075763c6ce9fe9426ed77a70
ms.sourcegitcommit: 2f322df43fb3854d07a69bcdf56c6b1f7e6f3333
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/27/2021
ms.locfileid: "108017576"
---
# <a name="azure-dns-metrics-and-alerts"></a>Azure DNS 메트릭 및 경고

Azure DNS는 Microsoft Azure 인프라를 사용하여 이름 확인을 제공하는 DNS 도메인에 대한 호스팅 서비스입니다. 이 문서에서는 Azure DNS 서비스에 대한 메트릭 및 경고를 설명합니다.

## <a name="azure-dns-metrics"></a>Azure DNS 메트릭

Azure DNS는 DNS 영역의 특정 측면을 모니터링하기 위한 메트릭을 제공합니다. Azure DNS의 메트릭을 사용하여 충족되는 조건에 따라 경고를 구성할 수 있습니다. 제공된 메트릭은 [Azure Monitor 서비스](../azure-monitor/index.yml)를 사용하여 데이터를 표시합니다. 메트릭 탐색기 환경 및 차트에 대한 자세한 내용은 [Azure Monitor 메트릭 탐색기](../azure-monitor/essentials/metrics-charts.md)를 참조하세요. 
 
Azure DNS는 Azure Monitor에 DNS 영역에 대한 다음 메트릭을 제공합니다.

-   QueryVolume
-   RecordSetCount
-   RecordSetCapacityUtilization

자세한 내용은 [메트릭 정의](../azure-monitor/essentials/metrics-supported.md#microsoftnetworkdnszones)를 참조하세요.

>[!NOTE]
> 이때 이러한 메트릭은 Azure DNS에서 호스트되는 공용 DNS 영역에만 사용할 수 있습니다. Azure DNS에서 호스트되는 프라이빗 영역을 사용하는 경우 이러한 메트릭은 해당 영역에 대한 데이터를 제공하지 않습니다. 또한 메트릭 및 경고 기능은 Azure 퍼블릭 클라우드에서만 지원됩니다. 소버린 클라우드에 대한 지원은 나중에 제공될 예정입니다. 

메트릭을 볼 수 있는 가장 세분화된 요소는 DNS 영역입니다. 현재 영역 내 개별 리소스 레코드에 대한 메트릭을 볼 수 없습니다.

### <a name="query-volume"></a>쿼리 볼륨

*쿼리 볼륨* 메트릭은 DNS 영역에 대해 Azure DNS가 받는 DNS 쿼리 수를 보여줍니다. 측정 단위는 `Count`이고 집계는 일정 기간 동안 받는 모든 쿼리의 `Sum`입니다.

이 메트릭을 보려면 Azure Portal의 **모니터** 페이지에서 **메트릭** 탐색기 환경을 선택합니다. DNS 영역으로 범위를 지정한 다음, **적용** 을 선택합니다. *메트릭* 드롭다운에서 `Query Volume`을 선택한 다음, *집계* 드롭다운에서 `Sum`을 선택합니다.

![쿼리 볼륨](./media/dns-alerts-metrics/dns-metrics-query-volume.png)

*그림: Azure DNS 쿼리 볼륨 메트릭*

### <a name="record-set-count"></a>레코드 집합 수

*레코드 집합 수* 메트릭은 DNS 영역에 대한 Azure DNS에서 레코드 집합의 수를 표시합니다. 영역에서 정의된 모든 레코드 집합이 계산됩니다. 측정 단위는 `Count`이며 집계는 모든 레코드 집합의 `Maximum`입니다.

이 메트릭을 보려면 Azure Portal의 **모니터** 탭에서 **메트릭** 탐색기 환경을 선택합니다. DNS 영역으로 범위를 지정한 다음, **적용** 을 선택합니다. *메트릭* 드롭다운에서 `Query Volume`을 선택한 다음, *집계* 드롭다운에서 `Sum`을 선택합니다.

**리소스** 드롭다운에서 DNS 영역을 선택하고, **레코드 집합 수** 메트릭을 선택한 다음, **집계** 로 **최대** 를 선택합니다. 

![레코드 집합 수](./media/dns-alerts-metrics/dns-metrics-record-set-count.png)

*그림: Azure DNS 레코드 집합 수 메트릭*

### <a name="record-set-capacity-utilization"></a>레코드 집합 용량 사용률

*레코드 집합 용량 사용률* 메트릭은 DNS 영역에 대한 레코드 집합 용량의 사용률을 보여줍니다. 각 Azure DNS 영역에는 영역에 허용되는 최대 레코드 집합 수를 정의하는 레코드 집합 제한이 있습니다. 자세한 내용은 [DNS 제한](dns-zones-records.md#limits) 섹션을 참조하세요. 측정 단위는 `Percentage`이며 집계 형식은 `Maximum`입니다.

예를 들어 DNS 영역에 구성된 500개의 레코드 집합이 있고, 영역의 기본 레코드 집합 제한이 5000개인 경우 RecordSetCapacityUtilization 메트릭은 500을 5000으로 나눈 값인 10%를 표시합니다. 

이 메트릭을 보려면 Azure Portal의 **모니터** 탭에서 **메트릭** 탐색기 환경을 선택합니다. DNS 영역으로 범위를 지정한 다음, **적용** 을 선택합니다. *메트릭* 드롭다운에서 `Record Set Capacity Utilization`을 선택한 다음, *집계* 드롭다운에서 `Sum`을 선택합니다. 

![메트릭을 보는 방법에 대한 예제를 보여 주는 스크린샷.](./media/dns-alerts-metrics/dns-metrics-record-set-capacity-uitlization.png)

*그림: Azure DNS 레코드 집합 용량 사용률 메트릭*

## <a name="alerts-in-azure-dns"></a>Azure DNS의 경고

Azure Monitor에는 사용 가능한 각 메트릭 값에 대해 구성할 수 있는 경고가 있습니다. 자세한 내용은 [Azure Monitor 경고](../azure-monitor/alerts/alerts-metric.md)를 참조하세요.

1. Azure DNS 영역에 대한 경고를 구성하려면 Azure Portal의 *모니터* 페이지에서 **경고** 를 선택합니다. 그런 다음, **+ 새 경고 규칙** 을 선택합니다.

    :::image type="content" source="./media/dns-alerts-metrics/alert-metrics.png" alt-text="모니터 페이지의 경고 단추 스크린샷.":::


1. 범위 섹션에서 **리소스 선택** 링크를 클릭하여 *리소스 선택* 페이지를 엽니다. **DNS 영역** 을 기준으로 필터링한 다음, 대상 리소스로 원하는 Azure DNS 영역을 선택합니다. 영역을 선택하면 **완료** 를 선택합니다.

    :::image type="content" source="./media/dns-alerts-metrics/select-resource.png" alt-text="경고 구성의 리소스 선택 페이지 스크린샷.":::

1. 그런 다음, 조건 섹션에서 **조건 추가** 링크를 클릭하여 *신호 선택* 페이지를 엽니다. 경고를 구성하려는 3가지 *메트릭* 신호 형식 중 하나를 선택합니다.

    :::image type="content" source="./media/dns-alerts-metrics/select-signal.png" alt-text="신호 선택 페이지의 사용 가능한 메트릭 스크린샷.":::

1. *신호 논리 구성* 페이지에서 선택한 메트릭에 대한 임계값과 평가 빈도를 구성합니다.

    :::image type="content" source="./media/dns-alerts-metrics/configure-signal-logic.png" alt-text="신호 논리 구성 페이지의 스크린샷.":::

1. 알림을 보내거나 경고에 의해 트리거되는 작업을 호출하려면 **작업 그룹 추가** 를 클릭합니다. *작업 그룹 추가* 페이지에서 **+ 작업 그룹 만들기** 를 선택합니다. 자세한 내용은 [작업 그룹](../azure-monitor/alerts/action-groups.md)을 참조하세요.

1. *경고 규칙 이름* 을 입력하고 **경고 규칙 만들기** 를 선택하여 구성을 저장합니다.

    :::image type="content" source="./media/dns-alerts-metrics/create-alert-rule.png" alt-text="경고 규칙 만들기 페이지의 스크린샷.":::

Azure Monitor 메트릭에 대한 경고를 구성하는 방법에 대한 자세한 내용은 [Azure Monitor를 사용하여 경고 만들기, 보기 및 관리](../azure-monitor/alerts/alerts-metric.md)를 참조하세요. 

## <a name="next-steps"></a>다음 단계

- [Azure DNS](dns-overview.md)에 대해 자세히 알아봅니다.
