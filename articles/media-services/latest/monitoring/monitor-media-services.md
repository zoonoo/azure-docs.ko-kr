---
title: Media Services 모니터링
description: Media Services를 모니터링하는 방법을 알아보려면 여기서 시작합니다.
author: IngridAtMicrosoft
ms.author: inhenkel
manager: femilia
ms.topic: how-to
ms.service: media-services
ms.custom: subject-monitoring
ms.date: 03/17/2021
ms.openlocfilehash: 90ca92dc19c588d0b19adf009301cf844e0cdbde
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105609051"
---
# <a name="monitor-media-services"></a>Media Services 모니터링

Azure 리소스를 사용하는 중요한 애플리케이션 및 비즈니스 프로세스가 있는 경우 이와 같은 리소스의 가용성, 성능 및 작업을 모니터링할 수 있습니다. 이 문서에서는 Media Services에서 생성되는 모니터링 데이터와 Azure Monitor 기능을 사용하여 이 데이터에 대해 분석하고 경고하는 방법을 설명합니다.

## <a name="metrics-are-useful"></a>유용한 메트릭

다음은 Media Services 메트릭을 모니터링하여 앱 성능을 이해하는 경우의 예입니다. Media Services 메트릭을 사용하여 해결할 수 있는 몇 가지 질문은 다음과 같습니다.

- 표준 스트리밍 엔드포인트를 모니터링하여 한도를 초과하는 경우를 확인하려면 어떻게 하나요?
- 프리미엄 스트리밍 엔드포인트 스케일링 단위가 충분한지를 어떻게 알 수 있나요?
- 내 스트리밍 엔드포인트를 스케일링해야 하는 경우를 알기 위한 경고를 설정하려면 어떻게 해야 하나요?
- 계정에 구성된 최대 송신에 도달했을 때를 알기 위한 경고를 설정하려면 어떻게 해야 하나요?
- 실패한 요청에 대한 분석 및 실패 원인을 어떻게 확인할 수 있나요?
- 패키지 작성 도구에서 끌어올 수 있는 HLS 또는 DASH 요청 수를 알려면 어떻게 해야 하나요?
- 실패한 요청의 임계값에 도달했을 때를 알기 위한 경고를 설정하려면 어떻게 해야 하나요?

<!--THIS DOESN'T BELONG HERE Concurrency becomes a concern for the number of Streaming Endpoints used in a single account over time. You need to keep in mind the relationship between the number of concurrent streams with complex publishing parameters like dynamic packaging to multiple protocols, multiple DRM encryptions etc. Each additional published live stream adds to the CPU and output bandwidth on the Streaming Endpoint. With that in mind, you should use Azure Monitor to closely watch the Streaming Endpoint's utilization (CPU and Egress capacity) to make certain that you are scaling it appropriately (or split traffic out between multiple Streaming Endpoints if you are getting into very high concurrency).-->

<!-- Optional diagram showing monitoring for your service. If you need help creating one, contact 
robb@microsoft.com -->

## <a name="what-is-azure-monitor"></a>Azure Monitor란?

Media Services는 Azure의 전체 스택 모니터링 서비스인 [Azure Monitor](../../../azure-monitor/overview.md)를 사용하여 모니터링 데이터를 만듭니다. 이를 통해 다른 클라우드 및 온-프레미스의 리소스 이외에도 Azure 리소스를 모니터링할 수 있는 완전한 기능 세트를 제공합니다.

다음 개념을 설명하는 [Azure Monitor를 사용하여 Azure 리소스 모니터링](../../../azure-monitor/essentials/monitor-azure-resource.md) 문서를 읽어보세요.

- Azure Monitor란?
- 모니터링과 관련된 비용
- Azure에서 수집된 데이터 모니터링
- 데이터 수집 구성
- 모니터링 데이터를 분석하고 경고하는 Azure의 표준 도구

## <a name="monitoring-data"></a>데이터 모니터링

Media Services는 [Azure 리소스의 모니터링 데이터](../../../azure-monitor/essentials/monitor-azure-resource.md#monitoring-data)에 설명된 다른 Azure 리소스와 동일한 종류의 모니터링 데이터를 수집합니다.

Azure Monitor가 수집하는 모든 데이터는 두 가지 기본 유형인 메트릭 및 로그 중 하나에 해당합니다. 이러한 두 가지 유형으로 다음을 수행할 수 있습니다.

- 메트릭 탐색기를 사용하여 메트릭 데이터를 시각화하고 분석합니다.
- Media Services 진단 로그를 모니터링하고 경고 및 알림을 만듭니다.
- 로그를 사용하여 다음을 수행할 수 있습니다.
  - Azure Storage로 보내기
  - Azure Event Hubs에 스트리밍
  - Log Analytics로 내보내기
  - 타사 서비스 사용

Media Services에서 만든 메트릭 및 로그 메트릭에 대한 자세한 내용은 [Media Services 모니터링 데이터 참조](monitor-media-services-data-reference.md) 문서를 참조하세요.

## <a name="collection-and-routing"></a>수집 및 라우팅

*플랫폼 메트릭* 및 *활동 로그* 는 자동으로 수집되고 저장되지만 진단 설정을 사용하여 다른 위치로 라우팅할 수 있습니다.  

*리소스 로그* 는 진단 설정을 만들고 하나 이상의 위치로 라우팅할 때까지 수집 및 저장되지 **않습니다**.

Azure Portal, CLI 또는 PowerShell을 사용한 진단 설정 만들기의 자세한 프로세스는 [Azure에서 플랫폼 로그 및 메트릭을 수집하는 진단 설정 만들기](../../../azure-monitor/essentials/diagnostic-settings.md) 문서를 참조하세요.

진단 설정을 만들 때 수집할 로그 범주를 지정합니다. Media Services 범주는 [Media Services 모니터링 데이터 참조](monitor-media-services-data-reference.md)에 나열되어 있습니다.

## <a name="analyzing-metrics"></a>메트릭 분석

메트릭 탐색기를 사용하여 다른 Azure 서비스의 메트릭으로 Media Services에 대한 메트릭을 분석하려면 **Azure Monitor** 메뉴에서 **메트릭** 을 엽니다. 이 도구 사용에 대한 자세한 내용은 [Azure 메트릭 탐색기 시작](../../../azure-monitor/essentials/metrics-getting-started.md)을 참조하세요.

Media Services에 대해 수집되는 메트릭의 목록은 [Media Services 모니터링 데이터 참조](monitor-media-services-data-reference.md)를 참조하세요.

## <a name="analyzing-logs"></a>로그 분석

Azure Monitor Logs의 데이터는 테이블마다 고유한 자체 속성 집합이 있는 테이블에 저장됩니다.  

Azure Monitor의 모든 리소스 로그에는 동일한 필드와 그 뒤에 오는 서비스별 필드가 있습니다. 공용 스키마는 [Azure Monitor 리소스 로그 스키마](../../../azure-monitor/essentials/resource-logs-schema.md#top-level-common-schema)에서 설명합니다.

Media Services 리소스 로그의 스키마는 [Media Services 모니터링 데이터 참조](monitor-media-services-data-reference.md)에서 찾을 수 있습니다.

[활동 로그](../../../azure-monitor/essentials/activity-log.md)는 구독 수준의 이벤트에 대한 인사이트를 제공하는 Azure의 플랫폼 로그입니다. 활동 로그는 독립적으로 보거나 Azure Monitor Logs로 라우팅할 수 있습니다. 여기서 Log Analytics를 사용하여 훨씬 더 복잡한 쿼리를 수행할 수 있습니다.

Media Services에 대해 수집되는 리소스 로그 유형 목록은 [Media Services 모니터링 데이터 참조](monitor-media-services-data-reference.md)를 참조하세요.

### <a name="why-would-i-want-to-use-diagnostic-logs"></a>진단 로그를 사용하려는 이유는 무엇인가요?

진단 로그를 사용하여 검사할 수 있는 몇 가지 작업은 다음과 같습니다.

- DRM 유형에서 제공하는 라이선스의 수
- 정책에서 제공하는 라이선스의 수
- DRM 또는 정책 유형별 오류
- 클라이언트의 허가되지 않은 라이선스 요청 수

## <a name="alerts"></a>경고

Azure Monitor 경고는 모니터링 데이터에서 중요한 조건이 발견될 때 사용자에게 사전에 알립니다. 이를 통해 고객이 알기 전에 시스템 문제를 식별하고 해결할 수 있습니다. [메트릭](../../../azure-monitor/alerts/alerts-metric-overview.md), [로그](../../../azure-monitor/alerts/alerts-unified-log.md) 및 [활동 로그](../../../azure-monitor/alerts/activity-log-alerts.md)에서 경고를 설정할 수 있습니다.

Media Services 메트릭은 값이 변경되었는지 여부와 상관 없이 정기적으로 수집됩니다. 자주 샘플링될 수 있으므로 경고하는 데 유용합니다. 비교적 간단한 논리를 사용하여 경고를 신속하게 발생시킬 수 있습니다.

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
