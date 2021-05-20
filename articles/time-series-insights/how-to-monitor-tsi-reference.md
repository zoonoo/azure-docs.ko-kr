---
title: Azure Time Series Insights 데이터 참조 모니터링 | Microsoft Docs
description: Azure Time Series Insights 모니터링에 대한 참조 설명서입니다.
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 12/10/2020
ms.custom: lyrana
ms.openlocfilehash: 20d376ee501edf16711617847c685dfd531f2c80
ms.sourcegitcommit: c2a41648315a95aa6340e67e600a52801af69ec7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106504586"
---
# <a name="monitoring-azure-time-series-insights-data-reference"></a>Azure Time Series Insights 데이터 참조 모니터링

Azure Time Series Insights 환경에서 Azure Monitor가 수집한 데이터 및 리소스에 대해 알아봅니다. 모니터링 데이터의 수집 및 분석에 대한 자세한 내용은 [Time Series Insights 모니터링]( ./how-to-monitor-tsi.md)을 참조하세요.

## <a name="metrics"></a>메트릭

이 섹션에는 Azure Time Series Insights에 대해 수집되는 모든 자동 수집 플랫폼 메트릭이 나열되어 있습니다. Azure Time Series Insights 포함하여 모든 Azure Monitor 지원 메트릭의 목록은 [Azure Monitor 지원 메트릭](../azure-monitor/essentials/metrics-supported.md)을 참조하세요.
이러한 메트릭에 대한 리소스 공급자는 [Microsoft.TimeSeriesInsights/environments/eventsources](../azure-monitor/essentials/metrics-supported.md#microsofttimeseriesinsightsenvironmentseventsources) 및 [Microsoft.TimeSeriesInsights/environments](../azure-monitor/essentials/metrics-supported.md#microsofttimeseriesinsightsenvironments)입니다.


### <a name="ingress"></a>수신

|메트릭|메트릭 표시 이름|단위|집계 형식|설명|
|---|---|---|---|---|
|IngressReceivedBytes|수신된 바이트|바이트|합계|이벤트 원본에서 읽은 바이트 수|
|IngressReceivedInvalidMessages|수신된 잘못된 메시지|개수|합계|이벤트 원본에서 읽은 잘못된 메시지 수|
|IngressReceivedMessages|수신된 메시지|개수|합계|이벤트 원본에서 읽은 메시지 수|
|IngressReceivedMessagesCountLag|수신된 메시지 수 지연|개수|평균|이벤트 원본 파티션에서 마지막 큐에 대기된 메시지의 시퀀스 번호와 수신 처리되는 메시지의 시퀀스 번호 간의 차이|
|IngressReceivedMessagesTimeLag|수신된 메시지 시간 지연|초|최대|메시지가 이벤트 원본의 큐에 대기되는 시간과 수신 처리되는 시간 간의 차이입니다.|
|IngressStoredBytes|저장된 수신 바이트|바이트|합계|성공적으로 처리되어 쿼리에 사용할 수 있는 총 이벤트 크기|
|IngressStoredEvents|저장된 수신 이벤트|개수|합계|성공적으로 처리되어 쿼리에 사용할 수 있는 총 평면화된 이벤트 크기|

### <a name="storage"></a>스토리지

|메트릭|메트릭 표시 이름|단위|집계 형식|설명|
|---|---|---|---|---|
|WarmStorageMaxProperties|웜 스토리지 최대 속성|개수|최대|S1/S2 SKU 환경에서 허용하는 최대 속성 수 및 PAYG SKU용 웜 저장소에서 허용하는 최대 속성 수|
|WarmStorageUsedProperties|웜 스토리지 사용 속성 |개수|최대|S1/S2 SKU 환경에서 사용하는 속성 수 및 PAYG SKU용 웜 저장소에서 사용하는 속성 수|

## <a name="resource-logs"></a>리소스 로그

이 섹션에는 Azure Time Series Insights 환경에 대해 수집할 수 있는 리소스 로그 유형이 나열되어 있습니다.

| 범주 | 표시 이름 | 설명 |
|----- |----- |----- |
| 수신 | TSIIngress | 수신 범주는 수신 파이프라인에서 발생하는 오류를 추적합니다. 이 범주에는 이벤트를 받을 때 발생하는 오류(예: 이벤트 원본에 연결하지 못함) 및 이벤트를 처리할 때 발생하는 오류(예: 이벤트 페이로드를 구문 분석할 때 발생하는 오류)가 포함됩니다. |

## <a name="schemas"></a>스키마
Azure Time Series Insights에서 다음 스키마를 사용하고 있습니다.

### <a name="tsiingress-table"></a>TSIIngress 테이블

| 속성 | Description |
|----- |----- |
| TimeGenerated | 이 이벤트가 생성되는 시간(UTC)입니다. |
| 위치 | 리소스의 위치입니다. |
| 범주 | 로그 이벤트의 범주입니다. |
| OperationName | 이벤트의 작업 이름입니다. |
| CorrelationId | 요청의 상관 관계 ID입니다. |
| Level | 이벤트의 심각도 수준입니다. |
| ResultDescription | '수신 금지 오류'와 같은 작업 결과에 대한 설명입니다. |
| 메시지 | 오류와 관련된 메시지입니다. 잘못된 부분과 오류를 완화하는 방법에 대한 세부 정보를 포함합니다. |
| 오류 코드 | 오류와 관련된 코드 |
| EventSourceType | 이벤트 원본의 유형입니다. 이벤트 허브 또는 IoT 허브일 수 있습니다. |
| EventSourceProperties | 이벤트 원본과 관련된 속성의 컬렉션입니다. 소비자 그룹 및 액세스 키 이름과 같은 세부 정보를 포함합니다. |
