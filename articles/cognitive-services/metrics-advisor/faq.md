---
title: 메트릭 관리자에 게 질문과 대답
titleSuffix: Azure Cognitive Services
description: 메트릭 관리자 서비스에 대 한 질문과 대답입니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 09/30/2020
ms.author: aahi
ms.openlocfilehash: e4a75bdd6147ee2189660c37062c5bec9d55d512
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91631745"
---
# <a name="metrics-advisor-frequently-asked-questions"></a>메트릭 관리자에 게 질문과 대답

### <a name="what-is-the-cost-of-my-instance"></a>내 인스턴스의 비용은 어떻습니까?

현재 미리 보기 중에는 인스턴스를 사용 하는 데 비용이 들지 않습니다.

### <a name="why-is-the-demo-website-readonly"></a>데모 웹 사이트가 readonly 인 이유는 무엇 인가요?

[데모 웹 사이트](https://anomaly-detector.azurewebsites.net/) 는 공개적으로 사용할 수 있습니다. 이 인스턴스는 데이터의 실수로 인 한 업로드를 방지 하기 위해 읽기 전용으로 설정 됩니다.

### <a name="why-cant-i-create-the-resource-the-pricing-tier-is-unavailable-and-it-says-you-have-already-created-1-s0-for-this-subscription"></a>리소스를 만들 수 없는 이유는 무엇입니까? "가격 책정 계층"을 사용할 수 없으며 "이미이 구독에 대해 S0 1 개를 만들었습니다." 라는 메시지가 표시 되나요?

:::image type="content" source="media/pricing.png" alt-text="F0 리소스가 이미 있는 경우의 메시지":::

공개 미리 보기 기간 중에는 한 지역의 한 구독에서 메트릭 관리자의 인스턴스를 하나만 만들 수 있습니다.

동일한 구독을 사용 하 여 동일한 지역에서 만든 인스턴스가 이미 있는 경우 다른 지역 또는 다른 구독을 시도 하 여 새 인스턴스를 만들 수 있습니다. 기존 인스턴스를 삭제 하 여 새 인스턴스를 만들 수도 있습니다.

기존 인스턴스를 이미 삭제 했지만 오류가 계속 표시 되는 경우 새 인스턴스를 만들기 전에 리소스를 삭제 한 후 20 분 정도 기다려 주세요.

## <a name="basic-concepts"></a>기본 개념

### <a name="what-is-multi-dimensional-time-series-data"></a>다차원 시계열 데이터는 무엇 인가요?

용어집에서 [다차원 메트릭](glossary.md#multi-dimensional-metric)  정의를 참조 하세요.

### <a name="how-much-data-is-needed-for-metrics-advisor-to-start-anomaly-detection"></a>메트릭 관리자에서 변칙 검색을 시작 하는 데 필요한 데이터의 양은 얼마 인가요?

적어도 하나의 데이터 요소는 변칙 검색을 트리거할 수 있습니다. 그러나 최상의 정확도는 아닙니다. 이 서비스는 데이터 피드를 만드는 동안 "채우기 간격" 규칙으로 지정한 값을 사용 하 여 이전 데이터 요소의 창을 가정 합니다.

검색 하려는 타임 스탬프 앞에 일부 데이터를 포함 하는 것이 좋습니다.
데이터의 세분성에 따라 권장 되는 데이터 양은 아래와 같습니다.

| 세분성 | 검색에 권장 되는 데이터 양 |
| ----------- | ------------------------------------- |
| 5 분 미만 | 4 일간의 데이터 |
| 5 분 ~ 1 일 | 데이터의 28 일 |
| 1 일 이상, 31 일 이상 | 4 년 분량의 데이터 |
| 31 일 초과 | 48 년 분량의 데이터 |

### <a name="why-metrics-advisor-doesnt-detect-anomalies-from-historical-data"></a>메트릭 관리자가 기록 데이터에서 변칙을 검색 하지 않는 이유는 무엇 인가요?

메트릭 관리자는 라이브 스트리밍 데이터를 검색 하기 위한 것입니다. 서비스가 다시 검색 하 고 변칙 검색을 실행 하는 기록 데이터의 최대 길이에 제한이 있습니다. 즉, 특정 한 가장 이른 타임 스탬프 이후의 데이터 요소만 변칙 검색 결과를 갖게 됩니다. 가장 이른 타임 스탬프는 데이터의 세분성에 따라 달라 집니다.

데이터의 세분성에 따라 변칙 검색 결과가 발생 하는 기록 데이터의 길이는 다음과 같습니다.

| 세분성 | 변칙 검색에 대 한 기록 데이터의 최대 길이 |
| ----------- | ------------------------------------- |
| 5 분 미만 | 온보드 시간-13 시간 |
| 1 시간 미만 5 분 | 등록 시간-4 일  |
| 1 시간-1 일 미만 | 등록 시간-14 일  |
| 1일 | 등록 시간-28 일  |
| 1 일 보다 크고 31 일 미만 | 온보드 시간-2 년  |
| 31 일 초과 | 온보드 시간-24 년   |

### <a name="more-concepts-and-technical-terms"></a>추가 개념 및 기술 조건

자세한 내용은 [용어집](glossary.md) 을 참조 하십시오.

###  <a name="how-do-i-write-a-valid-query-for-ingesting-my-data"></a>수집 내 데이터에 대 한 올바른 쿼리를 작성 어떻게 할까요?  

메트릭 관리자가 데이터를 수집 하려면 단일 타임 스탬프에서 데이터의 크기를 반환 하는 쿼리를 만들어야 합니다. 메트릭 관리자는이 쿼리를 여러 번 실행 하 여 각 타임 스탬프에서 데이터를 가져옵니다. 

쿼리는 지정 된 타임 스탬프에서 각 차원 조합에 대해 최대 하나의 레코드를 반환 해야 합니다. 반환 된 모든 레코드의 타임 스탬프는 동일 해야 합니다. 쿼리에서 반환 된 중복 레코드가 없어야 합니다.

예를 들어 일별 메트릭에 대해 아래 쿼리를 만든다고 가정 합니다. 
 
`select timestamp, city, category, revenue from sampledata where Timestamp >= @StartTime and Timestamp < dateadd(DAY, 1, @StartTime)`

시계열에 대해 정확한 세분성을 사용 해야 합니다. 시간별 메트릭에 대해 다음을 사용 합니다. 

`select timestamp, city, category, revenue from sampledata where Timestamp >= @StartTime and Timestamp < dateadd(hour, 1, @StartTime)`

이러한 쿼리는 단일 타임 스탬프로 데이터만 반환 하 고 메트릭 관리자가 수집 하는 모든 차원 조합을 포함 합니다. 

:::image type="content" source="media/query-result.png" alt-text="F0 리소스가 이미 있는 경우의 메시지" lightbox="media/query-result.png":::


### <a name="how-do-i-detect-spikes--dips-as-anomalies"></a>비정상 상태에서 급증 & dip를 검색 어떻게 할까요??

하드 임계값을 미리 정의한 경우 [변칙 검색 구성](how-tos/configure-metrics.md#anomaly-detection-methods)에서 "하드 임계값"을 수동으로 설정할 수 있습니다.
임계값이 없는 경우 AI로 구동 되는 "스마트 검색"을 사용할 수 있습니다. 자세한 내용은 [검색 구성 조정](how-tos/configure-metrics.md#tune-the-detecting-configuration) 을 참조 하세요.

### <a name="how-do-i-detect-inconformity-with-regular-seasonal-patterns-as-anomalies"></a>일반 (계절) 패턴을 비정상으로 감지 어떻게 할까요??

"스마트 감지"는 계절 패턴을 포함 하 여 데이터 패턴을 학습할 수 있습니다. 그런 다음 일반 패턴을 따르지 않는 데이터 요소를 변칙으로 검색 합니다. 자세한 내용은 [검색 구성 조정](how-tos/configure-metrics.md#tune-the-detecting-configuration) 을 참조 하세요.

### <a name="how-do-i-detect-flat-lines-as-anomalies"></a>평면을 비정상으로 감지 어떻게 할까요??

데이터가 정상적으로 안정적이 고 변동 되지 않으며, 너무 안정적으로 전환 되거나 평평한 선 일 때 경고를 표시 하려는 경우 변경이 너무 작은 경우 이러한 데이터 요소를 검색 하도록 "변경 임계값"을 구성할 수 있습니다.
자세한 내용은 [변칙 검색 구성](how-tos/configure-metrics.md#anomaly-detection-methods) 을 참조 하세요.

## <a name="next-steps"></a>다음 단계
- [Metrics Advisor 개요](overview.md)
- [데모 사이트 사용해 보기](quickstarts/explore-demo.md)
- [웹 포털 사용](quickstarts/web-portal.md)