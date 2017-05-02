---
title: "Azure Application Insights 원격 분석 데이터 모델 - 예외 원격 분석 | Microsoft 문서"
description: "예외 원격 분석을 위한 Azure Application Insights 데이터 모델"
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
ms.openlocfilehash: 17a39660fce598610ff9a95e886282e6b3faffe4
ms.lasthandoff: 04/22/2017


---
# <a name="exception-telemetry-application-insights-data-model"></a>예외 원격 분석: Application Insights 데이터 모델

예외 인스턴스는 모니터링된 응용 프로그램을 실행하는 동안 발생한 처리된 예외 또는 처리되지 않은 예외를 나타냅니다.

## <a name="problem-id"></a>문제 ID

코드에서 throw된 예외의 식별자입니다. 예외 그룹화에 사용됩니다. 일반적으로 예외 형식 및 호출 스택 기반 함수의 조합입니다.

최대 길이: 1024자

## <a name="severity-level"></a>심각도 수준

추적 심각도 수준입니다. 값은 `Verbose`, `Information`, `Warning`, `Error`, `Critical`일 수 있습니다.

## <a name="exception-details"></a>예외 세부 정보

이 섹션은 진행 중인 작업입니다...

## <a name="custom-properties"></a>사용자 지정 속성

[!INCLUDE [application-insights-data-model-properties](../../includes/application-insights-data-model-properties.md)]

## <a name="custom-measurements"></a>사용자 지정 측정

[!INCLUDE [application-insights-data-model-measurements](../../includes/application-insights-data-model-measurements.md)]

## <a name="next-steps"></a>다음 단계

- Application Insights 형식 및 데이터 모델에 대한 자세한 내용은 [데이터 모델](/application-insights-data-model.md)을 참조하세요.
- [Application Insights를 사용하여 웹앱에서 예외를 진단](/app-insights-asp-net-exceptions.md)하는 방법을 알아봅니다.
- Application Insights에서 지원되는 [플랫폼](/app-insights-platforms.md)을 확인합니다.

