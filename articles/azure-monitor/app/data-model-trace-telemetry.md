---
title: Azure 애플리케이션 Insights 데이터 모델-원격 분석 추적
description: 추적 원격 분석을 위한 Azure Application Insights 데이터 모델
ms.topic: conceptual
ms.date: 04/25/2017
ms.reviewer: sergkanz
ms.openlocfilehash: 31958b26cdb8a7897cf0051af6600014c07949fd
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "77671956"
---
# <a name="trace-telemetry-application-insights-data-model"></a>추적 원격 분석: Application Insights 데이터 모델

[Application Insights](../../azure-monitor/app/app-insights-overview.md)에서 추적 원격 분석은 텍스트를 검색하는 `printf` 스타일 추적 문을 나타냅니다. `Log4Net`, `NLog` 및 기타 텍스트 기반 로그 파일 항목이 이 형식의 인스턴스로 변환됩니다. 추적에는 확장 기능으로 사용할 측정이 없습니다.

## <a name="message"></a>메시지

추적 메시지입니다.

최대 길이: 32768자

## <a name="severity-level"></a>심각도 수준

추적 심각도 수준입니다. 값은 `Verbose`, `Information`, `Warning`, `Error`, `Critical`일 수 있습니다.

## <a name="custom-properties"></a>사용자 지정 속성

[!INCLUDE [application-insights-data-model-properties](../../../includes/application-insights-data-model-properties.md)]

## <a name="next-steps"></a>다음 단계

- [Application Insights에서 .NET 추적 로그 탐색](../../azure-monitor/app/asp-net-trace-logs.md).
- [Application Insights에서 Java 추적 로그를 탐색](../../azure-monitor/app/java-trace-logs.md)합니다.
- Application Insights 형식 및 데이터 모델에 대한 자세한 내용은 [데이터 모델](data-model.md)을 참조하세요.
- [사용자 지정 추적 원격 분석을 작성합니다](../../azure-monitor/app/api-custom-events-metrics.md#tracktrace).
- Application Insights에서 지원되는 [플랫폼](../../azure-monitor/app/platforms.md)을 확인합니다.
