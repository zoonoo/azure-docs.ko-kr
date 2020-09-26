---
title: Azure 방화벽 통합 문서를 사용 하 여 로그 모니터링
description: Azure 방화벽 통합 문서는 Azure 방화벽 데이터 분석을 위한 유연한 캔버스와 Azure Portal 내에서 풍부한 시각적 보고서를 만드는 기능을 제공 합니다.
services: firewall
author: gopimsft
ms.service: firewall
ms.topic: how-to
ms.date: 09/22/2020
ms.author: victorh
ms.openlocfilehash: dabe154535d2bd3ae5550d4bbbafa75adc5ae2c0
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91347376"
---
# <a name="monitor-logs-using-azure-firewall-workbook"></a>Azure 방화벽 통합 문서를 사용 하 여 로그 모니터링

Azure 방화벽 통합 문서는 Azure 방화벽 데이터 분석을 위한 유연한 캔버스를 제공 합니다. 이를 사용 하 여 Azure Portal 내에서 풍부한 시각적 보고서를 만들 수 있습니다. Azure에 배포 된 여러 방화벽을 탭 하 여 통합 된 대화형 환경에 통합할 수 있습니다.

Azure 방화벽 이벤트에 대 한 통찰력을 얻고, 응용 프로그램 및 네트워크 규칙에 대해 알아보고, Url, 포트 및 주소에서 방화벽 작업에 대 한 통계를 확인할 수 있습니다. Azure 방화벽 통합 문서를 사용 하면 방화벽과 리소스 그룹을 필터링 할 수 있으며, 로그의 문제를 조사할 때 데이터 집합을 쉽게 읽을 수 있도록 범주별로 동적 필터링 할 수 있습니다. 

## <a name="prerequisites"></a>사전 요구 사항

시작 하기 전에 Azure Portal에서 [진단 로깅을 사용 하도록 설정](firewall-diagnostics.md#enable-diagnostic-logging-through-the-azure-portal) 해야 합니다. 또한 azure 방화벽에 사용할 수 있는 진단 로그 및 메트릭에 대 한 개요는 [Azure 방화벽 로그 및 메트릭을](logs-and-metrics.md) 읽어 보세요.

## <a name="get-started"></a>시작

통합 문서를 배포 하려면 [Azure 방화벽에 대 한 Azure Monitor 통합 문서](https://github.com/Azure/Azure-Network-Security/tree/master/Azure%20Firewall/Azure%20Monitor%20Workbook) 로 이동 하 고 페이지의 지침을 따릅니다. Azure 방화벽 통합 문서는 다중 테 넌 트 및 다중 구독에서 작동 하도록 설계 되었으며 여러 방화벽으로 필터링 가능 합니다.

## <a name="overview-page"></a>개요 페이지

개요 페이지에서는 작업 영역, 시간 및 방화벽에 대해 필터링 하는 방법을 제공 합니다. 방화벽 및 로그 유형 (응용 프로그램, 네트워크, 위협 intel, DNS 프록시)에서 시간별로 이벤트를 표시 합니다.

:::image type="content" source="./media/firewall-workbook/firewall-workbook-overview.png" alt-text="Azure 방화벽 통합 문서 개요":::

## <a name="application-rule-log-statistics"></a>응용 프로그램 규칙 로그 통계

이 페이지에는 시간에 따른 IP 주소의 고유한 원본, 응용 프로그램 규칙 수 사용량, 시간당 거부/허용 된 FQDN 및 필터링 된 데이터가 표시 됩니다. IP 주소를 기준으로 데이터를 필터링 할 수 있습니다.

:::image type="content" source="./media/firewall-workbook/firewall-workbook-application-rule.png" alt-text="Azure 방화벽 통합 문서 응용 프로그램 규칙 로그":::

## <a name="network-rule-log-statistics"></a>네트워크 규칙 로그 통계

이 페이지에서는 시간에 따라 IP 및 DNAT 규칙 작업-허용/거부, 대상 포트를 기준으로 보기를 제공 합니다. 작업, 포트 및 대상 유형을 기준으로 필터링 할 수도 있습니다.

:::image type="content" source="./media/firewall-workbook/firewall-workbook-network-rule.png" alt-text="Azure 방화벽 통합 문서 네트워크 규칙 로그":::

기간을 기준으로 로그를 필터링 할 수도 있습니다.

:::image type="content" source="./media/firewall-workbook/firewall-workbook-network-rule-time.png" alt-text="Azure 방화벽 통합 문서 네트워크 규칙 로그 시간 창":::

## <a name="investigations"></a>조사

로그를 확인 하 고 원본 IP 주소에 따라 리소스에 대해 자세히 알아볼 수 있습니다. 가상 컴퓨터 이름 및 네트워크 인터페이스 이름과 같은 정보를 얻을 수 있습니다. 로그에서 리소스를 필터링 하는 것은 간단 합니다.

:::image type="content" source="./media/firewall-workbook/firewall-workbook-investigation.png" alt-text="Azure 방화벽 통합 문서 조사":::

## <a name="next-steps"></a>다음 단계

- [Azure 방화벽 진단](firewall-diagnostics.md) 에 대 한 자세한 정보