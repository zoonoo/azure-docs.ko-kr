---
title: 모니터링 Media Services
description: 여기에서 시작 하 여 Media Services를 모니터링 하는 방법을 알아보세요.
author: IngridAtMicrosoft
ms.author: inhenkel
manager: femilia
ms.topic: how-to
ms.service: media-services
ms.date: 03/12/2021
ms.openlocfilehash: 895c674fdbe5c413b68fe12862e7846f1eb7bd0b
ms.sourcegitcommit: afb9e9d0b0c7e37166b9d1de6b71cd0e2fb9abf5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/14/2021
ms.locfileid: "103464500"
---
# <a name="monitor-media-services"></a>모니터 Media Services

Azure 리소스를 사용하는 중요한 애플리케이션 및 비즈니스 프로세스가 있는 경우 이와 같은 리소스의 가용성, 성능 및 작업을 모니터링할 수 있습니다. 이 문서에서는 Media Services에서 생성 되는 모니터링 데이터 및 Azure Monitor의 기능을 사용 하 여이 데이터를 분석 하 고 경고 하는 방법에 대해 설명 합니다.

## <a name="metrics-are-useful"></a>메트릭이 유용 합니다.

다음은 응용 프로그램의 작동 방식을 이해 하는 데 Media Services 메트릭을 모니터링 하는 방법에 대 한 예입니다. Media Services 메트릭을 사용 하 여 해결할 수 있는 몇 가지 질문은 다음과 같습니다.

- 표준 스트리밍 끝점을 모니터링 하 여 한도를 초과 하는 경우를 확인 어떻게 할까요??
- 어떻게 할까요? 프리미엄 스트리밍 끝점 배율 단위가 충분 한지 확인
- 내 스트리밍 끝점을 확장할 시기를 알 수 있도록 경고를 설정 하려면 어떻게 해야 하나요?
- 계정에 구성 된 최대 송신에 도달 했을 때 알 수 있도록 경고를 설정 어떻게 할까요??
- 실패 한 요청에 대 한 분석 및 실패 원인을 어떻게 확인할 수 있나요?
- 패키지에서 얼마나 많은 HLS 또는 대시 요청을 가져올 수 있나요?
- 실패 한 요청의 임계값에 도달 했을 때를 알 수 있도록 경고를 설정 어떻게 할까요??

<!--THIS DOESN'T BELONG HERE Concurrency becomes a concern for the number of Streaming Endpoints used in a single account over time. You need to keep in mind the relationship between the number of concurrent streams with complex publishing parameters like dynamic packaging to multiple protocols, multiple DRM encryptions etc. Each additional published live stream adds to the CPU and output bandwidth on the Streaming Endpoint. With that in mind, you should use Azure Monitor to closely watch the Streaming Endpoint's utilization (CPU and Egress capacity) to make certain that you are scaling it appropriately (or split traffic out between multiple Streaming Endpoints if you are getting into very high concurrency).-->

<!-- Optional diagram showing monitoring for your service. If you need help creating one, contact 
robb@microsoft.com -->

## <a name="what-is-azure-monitor"></a>Azure Monitor란?

Media Services는 Azure에서 전체 stack 모니터링 서비스인 [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview)를 사용 하 여 모니터링 데이터를 만들며,이를 통해 다른 클라우드 및 온-프레미스의 리소스 외에도 azure 리소스를 모니터링할 수 있는 완전 한 기능 집합을 제공 합니다.

다음 개념을 설명 하는 [Azure Monitor을 사용 하 여 Azure 리소스 모니터링](https://docs.microsoft.com/azure/azure-monitor/insights/monitor-azure-resource)문서를 읽어 보십시오.

- Azure Monitor란?
- 모니터링과 관련된 비용
- Azure에서 수집된 데이터 모니터링
- 데이터 수집 구성
- 모니터링 데이터를 분석하고 경고하는 Azure의 표준 도구

## <a name="monitoring-data"></a>데이터 모니터링

Media Services는 [azure 리소스의 데이터 모니터링](https://docs.microsoft.com/azure/azure-monitor/insights/monitor-azure-resource#monitoring-data-from-Azure-resources)에 설명 된 다른 azure 리소스와 동일한 종류의 모니터링 데이터를 수집 합니다.

Azure Monitor가 수집하는 모든 데이터는 두 가지 기본 유형인 메트릭 및 로그 중 하나에 해당합니다. 이러한 두 가지 유형으로 다음을 수행할 수 있습니다.

- 메트릭 탐색기를 사용 하 여 메트릭 데이터를 시각화 하 고 분석 합니다.
- Media Services 진단 로그를 모니터링 하 고 경고 및 알림을 만듭니다.
- 로그를 사용 하 여 다음을 수행할 수 있습니다.
  - Azure Storage로 보내기
  - Azure Event Hubs로 스트리밍
  - Log Analytics로 내보내기
  - 타사 서비스 사용

Media Services에서 만든 메트릭 및 로그 메트릭에 대 한 자세한 내용은 [모니터링 Media Services 데이터 참조](monitor-media-services-data-reference.md) 문서를 참조 하세요.

## <a name="collection-and-routing"></a>수집 및 라우팅

*플랫폼 메트릭* 및 *활동 로그* 는 자동으로 수집 되 고 저장 되지만 진단 설정을 사용 하 여 다른 위치로 라우팅될 수 있습니다.  

*리소스 로그* 는 진단 설정을 만들고 하나 이상의 위치로 라우팅할 때까지 수집 및 저장 **되지 않습니다** .

Azure Portal, CLI 또는 PowerShell을 사용 하 여 진단 설정을 만드는 자세한 프로세스는 [Azure에서 플랫폼 로그 및 메트릭을 수집 하는 진단 설정 만들기](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings) 문서를 참조 하세요.

진단 설정을 만들 때 수집할 로그 범주를 지정합니다. Media Services 범주는 [Media Services 모니터링 데이터 참조](monitor-media-services-data-reference.md)에 나열 되어 있습니다.

## <a name="analyzing-metrics"></a>메트릭 분석

**Azure Monitor** 메뉴에서 **메트릭을 열어 메트릭 탐색기를 사용** 하 여 다른 Azure 서비스의 메트릭과 함께 Media Services에 대 한 메트릭을 분석할 수 있습니다. 이 도구 사용에 대한 자세한 내용은 [Azure 메트릭 탐색기 시작](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-getting-started)을 참조하세요.

Media Services에 대해 수집 되는 메트릭의 목록은 [모니터링 Media Services 데이터 참조](monitor-media-services-data-reference.md)를 참조 하세요.

## <a name="analyzing-logs"></a>로그 분석

Azure Monitor Logs의 데이터는 테이블마다 고유한 자체 속성 집합이 있는 테이블에 저장됩니다.  

Azure Monitor의 모든 리소스 로그에는 동일한 필드와 그 뒤에 오는 서비스별 필드가 있습니다. 공용 스키마는 [Azure Monitor 리소스 로그 스키마](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-logs-schema#top-level-resource-logs-schema)에 설명 되어 있습니다.

Media Services 리소스 로그의 스키마는 [모니터링 Media Services 데이터 참조](monitor-media-services-data-reference.md)에서 찾을 수 있습니다.

[활동 로그](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log) 는 구독 수준 이벤트에 대 한 통찰력을 제공 하는 Azure의 플랫폼 로그입니다. 활동 로그는 독립적으로 보거나 Azure Monitor Logs로 라우팅할 수 있습니다. 여기서 Log Analytics를 사용하여 훨씬 더 복잡한 쿼리를 수행할 수 있습니다.

Media Services에 대해 수집 되는 리소스 로그의 유형 목록은 [모니터링 Media Services 데이터 참조](monitor-media-services-data-reference.md)를 참조 하세요.

### <a name="why-would-i-want-to-use-diagnostic-logs"></a>진단 로그를 사용 하려면 어떻게 해야 하나요?

진단 로그를 사용 하 여 검사할 수 있는 몇 가지 작업은 다음과 같습니다.

- DRM 유형에 의해 제공 된 라이선스의 수입니다.
- 정책에서 제공 하는 라이선스의 수입니다.
- DRM 또는 정책 유형별로 오류가 발생 했습니다.
- 클라이언트의 허가 되지 않은 라이선스 요청 수입니다.

## <a name="alerts"></a>경고

Azure Monitor 경고는 모니터링 데이터에서 중요한 조건이 발견될 때 사용자에게 사전에 알립니다. 이를 통해 고객이 알기 전에 시스템 문제를 식별하고 해결할 수 있습니다. [메트릭](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-overview), [로그](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-unified-log) 및 [활동 로그](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-alerts)에서 경고를 설정할 수 있습니다.

Media Services 메트릭은 값이 변경 되었는지 여부에 관계 없이 정기적으로 수집 됩니다. 자주 샘플링 될 수 있으므로 경고 하는 데 유용 합니다. 비교적 간단한 논리를 사용 하 여 경고를 신속 하 게 발생 시킬 수 있습니다.

<!--
The following table lists common and recommended alert rules for Media Services.

<!-- Fill in the table with metric and log alerts that would be valuable for your service. Change the format as necessary to make it more readable
**PLACEHOLDER** table

| Alert type | Condition | Description  |
|:---|:---|:---|
| | | |
| | | |
-->

## <a name="next-steps"></a>다음 단계

[!INCLUDE [monitoring-next-steps](../includes/monitoring-next-steps.md)]
