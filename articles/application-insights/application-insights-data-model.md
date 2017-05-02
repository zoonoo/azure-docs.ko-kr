---
title: "Azure Application Insights 원격 분석 데이터 모델 | Microsoft Docs"
description: "Application Insights 데이터 모델 개요"
services: application-insights
documentationcenter: .net
author: SergeyKanzhelev
manager: azakonov-ms
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 04/17/2017
ms.author: sergkanz
translationtype: Human Translation
ms.sourcegitcommit: 9eafbc2ffc3319cbca9d8933235f87964a98f588
ms.openlocfilehash: 52f700bd18da87a9a38e0a791d0ec3496f201e0a
ms.lasthandoff: 04/22/2017


---
# <a name="application-insights-telemetry-data-model"></a>Application Insights 원격 분석 데이터 모델

Application Insights는 APM(응용 프로그램 성능 관리)을 위한 원격 분석 데이터 모델을 정의합니다. 이 모델은 데이터 수집을 표준화하고 플랫폼 및 언어 독립적 모니터링 시나리오를 만들 수 있도록 합니다. Application Insights에서 수집된 데이터로 일반적인 응용 프로그램 실행 패턴이 모델링됩니다.

![Application Insights 응용 프로그램 모델](./media/application-insights-data-model/application-insights-data-model.png)

응용 프로그램에는 두 가지 유형이 있습니다. 하나는 외부 ***요청***을 수신하는 끝점이 있는 응용 프로그램인 웹 응용 프로그램이고, 다른 하나는 웹 작업 또는 함수와 같은 위치에 저장된 데이터를 처리하기 위해 주기적으로 "웨이크업"되는 응용 프로그램입니다. 두 경우 모두에서 고유한 실행을 ***작업***이라고 지칭합니다. 작업은 ***예외***를 통해 성공 또는 실패하거나, 다른 서비스/저장소에 의존하여 비즈니스 논리를 전달할 수 있습니다. 이러한 개념을 반영하기 위해 Application Insights 데이터 모델은 3가지 원격 분석 유형인 [요청](./application-insights-data-model-request-telemetry.md), [예외](/application-insights-data-model-exception-telemetry.md) 및 [종속성](/application-insights-data-model-dependency-telemetry.md)을 정의합니다.

일반적으로 이러한 유형은 응용 프로그램 프레임워크에 의해 정의되며 SDK에서 자동으로 수집됩니다. `ASP.NET MVC`는 요청 실행 개념을 모델-뷰-컨트롤러 연결 방식으로 정의하며, 요청의 시작 및 중지를 표시합니다. SQL에 대한 종속성 호출은 `System.Data`로 정의됩니다. HTTP 끝점에 대한 호출은 `System.Net`으로 정의됩니다. 사용자 지정 속성 및 측정을 사용하여 특정 플랫폼 및 프레임워크에 의해 수집된 원격 분석 형식을 확장할 수 있습니다. 그러나 사용자 지정 원격 분석을 보고하려는 경우가 있습니다. 친숙한 계측 프레임워크(예: `Log4Net` 또는 `System.Diagnostics`)를 사용하여 진단 로깅을 구현하려고 할 수 있습니다. 또는 사용 패턴을 분석을 위해 서비스와의 사용자 상호 작용을 캡처해야 할 수도 있습니다. Application Insights에서는 3가지 추가 데이터 형식, 즉 [추적](/application-insights-data-model-trace-telemetry.md), [이벤트](/application-insights-data-model-event-telemetry.md) 및 [메트릭](/application-insights-data-model-metric-telemetry.md)을 인식하여 이러한 시나리오를 모델링합니다.

Application Insights 원격 분석 모델은 원격 분석과 해당 분석이 속하는 작업 간 [상관 관계를 분석](/correlation.md)하는 방법을 정의합니다. 예를 들어 요청은 SQL Database 호출을 수행하고 기록된 진단 정보를 작성할 수 있습니다. 해당 원격 분석 항목에 대한 상관 관계 컨텍스트를 설정한 후 요청 원격 분석에 다시 적용할 수 있습니다.

## <a name="schema-improvements"></a>향상된 스키마

Application Insights 데이터 모델은 응용 프로그램 원격 분석을 모델링하는 단순하고 기본적이면서도 강력한 방법입니다. 처음에는 기본 시나리오를 지원할 수 있게 단순하고 간단한 모델을 구현한 후 고급 사용을 위해 스키마를 확장할 수 있도록 합니다.

데이터 모델 또는 스키마 문제와 제안 사항을 보고하려면 GitHub [ApplicationInsights-Home](https://github.com/Microsoft/ApplicationInsights-Home/labels/schema) 리포지토리를 사용합니다.

## <a name="next-steps"></a>다음 단계

- Application Insights에서 지원되는 [플랫폼](/app-insights-platforms.md)을 확인합니다.
- [원격 분석을 확장 및 필터링](/app-insights-api-filtering-sampling.md)하는 방법을 알아봅니다.
- [샘플링](/app-insights-sampling.md)을 사용하여 데이터 모델을 기준으로 하는 원격 분석의 양을 최소화합니다.

