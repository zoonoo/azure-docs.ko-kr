---
title: "Azure Application Insights 원격 분석 데이터 모델 - 메트릭 원격 분석 | Microsoft Docs"
description: "메트릭 원격 분석을 위한 Azure Application Insights 데이터 모델"
services: application-insights
documentationcenter: .net
author: SergeyKanzhelev
manager: carmonm
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 04/25/2017
ms.author: bwren
ms.openlocfilehash: 42e55db4f932de85ee1a71c408b889e0ff9fe3e1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="metric-telemetry-application-insights-data-model"></a>메트릭 원격 분석: Application Insights 데이터 모델

[Application Insights](app-insights-overview.md)에서 지원하는 메트릭 원격 분석에는 두 가지 유형, 즉 단일 측정 및 미리 집계된 메트릭이 있습니다. 단일 측정은 이름 및 값만 포함합니다. 미리 집계된 메트릭은 집계 간격에서 메트릭의 최소값 및 최대값과 해당 표준 편차를 지정합니다.

미리 집계된 메트릭 원격 분석은 집계 기간을 1분으로 가정합니다.

Application Insights에서는 잘 알려진 몇 가지 메트릭 이름을 지원합니다. 

시스템 및 프로세스 카운터를 나타내는 메트릭:

| **.NET 이름**             | **플랫폼 독립적 이름** | **REST API 이름** | **설명**
| ------------------------- | -------------------------- | ----------------- | ---------------- 
| `\Processor(_Total)\% Processor Time` | 진행 중인 작업... | [processorCpuPercentage](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FprocessorCpuPercentage) | 총 컴퓨터 CPU
| `\Memory\Available Bytes`                 | 진행 중인 작업... | [memoryAvailableBytes](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FmemoryAvailableBytes) | 디스크의 사용 가능한 메모리
| `\Process(??APP_WIN32_PROC??)\% Processor Time` | 진행 중인 작업... | [processCpuPercentage](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FprocessCpuPercentage) | 응용 프로그램을 호스트하는 프로세스의 CPU
| `\Process(??APP_WIN32_PROC??)\Private Bytes`      | 진행 중인 작업... | [processPrivateBytes](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FprocessPrivateBytes) | 응용 프로그램을 호스트하는 프로세스에 사용되는 메모리
| `\Process(??APP_WIN32_PROC??)\IO Data Bytes/sec` | 진행 중인 작업... | [processIOBytesPerSecond](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FprocessIOBytesPerSecond) | 응용 프로그램을 호스트하는 프로세스의 I/O 작업 실행 속도
| `\ASP.NET Applications(??APP_W3SVC_PROC??)\Requests/Sec`             | 진행 중인 작업... | [requestsPerSecond](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FrequestsPerSecond) | 응용 프로그램에서 처리되는 요청 속도 
| `\.NET CLR Exceptions(??APP_CLR_PROC??)\# of Exceps Thrown / sec`    | 진행 중인 작업... | [exceptionsPerSecond](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FexceptionsPerSecond) | 응용 프로그램에서 throw하는 예외 속도
| `\ASP.NET Applications(??APP_W3SVC_PROC??)\Request Execution Time`   | 진행 중인 작업... | [requestExecutionTime](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FrequestExecutionTime) | 평균 요청 실행 시간
| `\ASP.NET Applications(??APP_W3SVC_PROC??)\Requests In Application Queue` | 진행 중인 작업... | [requestsInQueue](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FrequestsInQueue) | 큐에서 처리를 대기 중인 요청 수

## <a name="name"></a>이름

Application Insights 포털 및 UI에서 참조하려는 메트릭의 이름입니다. 

## <a name="value"></a>값

단일 측정 값입니다. 집계의 개별 측정값의 합계입니다.

## <a name="count"></a>개수

집계된 메트릭의 메트릭 가중치입니다. 측정값에 대해서는 설정하지 않아야 합니다.

## <a name="min"></a>Min

집계된 메트릭의 최소값입니다. 측정값에 대해서는 설정하지 않아야 합니다.

## <a name="max"></a>max

집계된 메트릭의 최대값입니다. 측정값에 대해서는 설정하지 않아야 합니다.

## <a name="standard-deviation"></a>표준 편차

집계된 메트릭 표준 편차입니다. 측정값에 대해서는 설정하지 않아야 합니다.

## <a name="custom-properties"></a>사용자 지정 속성

[!INCLUDE [application-insights-data-model-properties](../../includes/application-insights-data-model-properties.md)]

## <a name="next-steps"></a>다음 단계

- [사용자 지정 이벤트 및 메트릭용 Application Insights API](app-insights-api-custom-events-metrics.md#trackmetric) 사용 방법을 알아봅니다.
- Application Insights 형식 및 데이터 모델에 대한 자세한 내용은 [데이터 모델](application-insights-data-model.md)을 참조하세요.
- Application Insights에서 지원되는 [플랫폼](app-insights-platforms.md)을 확인합니다.
