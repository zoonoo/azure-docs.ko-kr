---
title: "Azure Application Insights 원격 분석 데이터 모델 - 이벤트 원격 분석 | Microsoft 문서"
description: "이벤트 원격 분석을 위한 Azure Application Insights 데이터 모델"
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
ms.openlocfilehash: 5b8b803f26dca82b5112568f486e5c347602a409
ms.lasthandoff: 04/22/2017


---
# <a name="event-telemetry-application-insights-data-model"></a>이벤트 원격 분석: Application Insights 데이터 모델

이벤트는 응용 프로그램에서 발생한 지정 시간 작업을 나타냅니다. 일반적으로 이 작업은 단추 클릭 또는 주문 체크 아웃과 같은 사용자 조작입니다. 초기화 또는 구성 업데이트 같은 응용 프로그램 수명 주기 이벤트일 수도 있습니다. 이벤트 이름은 짧고 카디널리티가 낮은 문자열이어야 합니다. 

의미상 이벤트는 요청과 상호 연결되거나 연결되지 않을 수 있습니다. 하지만 제대로 사용될 경우 이벤트 원격 분석은 요청이나 추적보다 더 중요합니다. 이벤트는 비즈니스 원격 분석을 나타내며 별도의 덜 까다로운 샘플링이 적용되어야 합니다.

## <a name="name"></a>이름

이벤트 이름입니다. 적절한 그룹화 및 유용한 메트릭을 허용하려면 카디널리티를 낮게 유지합니다.

최대 길이: 512자

## <a name="custom-properties"></a>사용자 지정 속성

[!INCLUDE [application-insights-data-model-properties](../../includes/application-insights-data-model-properties.md)]

## <a name="custom-measurements"></a>사용자 지정 측정

[!INCLUDE [application-insights-data-model-measurements](../../includes/application-insights-data-model-measurements.md)]

## <a name="next-steps"></a>다음 단계

- Application Insights 형식 및 데이터 모델에 대한 자세한 내용은 [데이터 모델](/application-insights-data-model.md)을 참조하세요.
- [사용자 지정 이벤트 및 메트릭용 Application Insights API](/app-insights-asp-net-dependencies.md) 사용 방법을 알아봅니다.
- Application Insights에서 지원되는 [플랫폼](/app-insights-platforms.md)을 확인합니다.

