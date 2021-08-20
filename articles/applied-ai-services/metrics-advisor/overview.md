---
title: Azure Metrics Advisor 서비스란?
titleSuffix: Azure Applied AI Services
description: Metrics Advisor란?
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: applied-ai-services
ms.subservice: metrics-advisor
ms.topic: overview
ms.date: 07/06/2021
ms.author: mbullwin
ms.openlocfilehash: 295c166ec80eba638798dd452e27de945e41e9a6
ms.sourcegitcommit: 192444210a0bd040008ef01babd140b23a95541b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/15/2021
ms.locfileid: "114342523"
---
# <a name="what-is-azure-metrics-advisor"></a>Azure Metrics Advisor란? 

Metrics Advisor는 AI를 사용하여 시계열 데이터에서 데이터 모니터링과 변칙 검색을 수행하는 [Azure Applied AI Services](../../applied-ai-services/what-are-applied-ai-services.md)의 일부입니다. 이 서비스는 데이터에 모델을 적용하는 프로세스를 자동화하고 데이터 수집, 변칙 검색 및 진단용 API 및 웹 기반 작업 영역 세트를 제공하므로 기계 학습을 몰라도 상관없습니다. 개발자는 서비스 위에 AIOps, 예측 유지 관리 및 비즈니스 모니터 애플리케이션을 빌드할 수 있습니다. Metrics Advisor를 사용하여 다음을 수행합니다.

* 여러 데이터 원본에서 다차원 데이터 분석
* 변칙 식별 및 상관 관계 지정
* 데이터에서 사용되는 변칙 검색 모델을 구성하고 미세 조정
* 변칙 진단 및 근본 원인 분석 지원

:::image type="content" source="media/metrics-advisor-overview.png" alt-text="Metrics Advisor 개요":::

이 설명서에는 다음과 같은 유형의 문서가 포함되어 있습니다.
* [빠른 시작](./Quickstarts/web-portal.md)은 서비스를 호출하고 짧은 시간 내에 결과를 얻을 수 있는 단계별 지침입니다. 
* [방법 가이드](./how-tos/onboard-your-data.md)에는 보다 구체적이거나 사용자 지정된 방식으로 서비스를 사용하기 위한 지침이 포함되어 있습니다.
* [개념 문서](glossary.md)에서는 서비스의 기능 및 기능에 대한 자세한 설명을 제공합니다.

## <a name="connect-to-a-variety-of-data-sources"></a>다양한 데이터 원본에 연결

Metrics Advisor는 다음을 포함한 여러 데이터 저장소의 [다차원 메트릭 데이터에 연결하고 수집](how-tos/onboard-your-data.md)할 수 있습니다. SQL Server, Azure Blob Storage, MongoDB 등

## <a name="easy-to-use-and-customizable-anomaly-detection"></a>사용하기 쉽고 사용자 지정이 가능한 변칙 검색

* Metrics Advisor는 데이터에 가장 적합한 모델을 자동으로 선택하므로 기계 학습을 몰라도 상관없습니다.
* [다차원 메트릭](glossary.md#multi-dimensional-metric) 내의 모든 시계열을 자동으로 모니터링합니다.
* [매개 변수 튜닝](how-tos/configure-metrics.md) 및 [대화형 피드백](how-tos/anomaly-feedback.md)을 사용하여 데이터에 적용된 모델 및 향후 변칙 검색 결과를 사용자 지정합니다.

## <a name="real-time-notification-through-multiple-channels"></a>여러 채널을 통한 실시간 알림

변칙이 검색될 때마다 Metrics Advisor는 이메일 후크, 웹후크, Teams 후크 및 Azure DevOps 후크와 같은 후크를 사용하여 여러 채널을 통해 [실시간 알림을 보낼](how-tos/alerts.md) 수 있습니다. 유연한 경고 구성을 통해 알림을 보낼 시기와 위치를 사용자 지정할 수 있습니다.

## <a name="smart-diagnostic-insights-by-analyzing-anomalies"></a>변칙을 분석하는 스마트 분석 인사이트

### <a name="analyze-root-cause-into-specific-dimension"></a>특정 차원으로 근본 원인 분석 

Metrics Advisor는 동일한 다차원 메트릭에서 검색된 변칙을 진단 트리에 결합하여 근본 원인을 특정 차원으로 분석하는 데 도움을 줍니다. 또한 각 차원의 가장 큰 기여도를 분석하여 사용할 수 있는 자동화된 분석 인사이트도 있습니다. 

### <a name="cross-metrics-analysis-using-metrics-graph"></a>메트릭 그래프를 사용한 교차 메트릭 분석

[메트릭 그래프는 메트릭](./how-tos/metrics-graph.md) 간의 관계를 나타냅니다. 전체적인 보기에서 모든 관련 메트릭 중 비정상적인 상태를 파악하는 데 도움이 되도록 교차 메트릭 분석을 사용하도록 설정할 수 있습니다. 최종 근본 원인을 찾습니다.

자세한 내용은 [인시던트 진단 방법](./how-tos/diagnose-an-incident.md)을 참조합니다.

## <a name="typical-workflow"></a>일반적인 워크플로

워크플로는 간단합니다. 데이터를 온보딩한 후 변칙 검색을 미세 조정하고 시나리오에 맞게 구성을 만들 수 있습니다.

1. Metrics Advisor용 [Azure 리소스를 만듭니다](https://go.microsoft.com/fwlink/?linkid=2142156). 
2. 웹 포털을 사용하여 첫 번째 모니터를 빌드합니다.
    1. [데이터 온보딩](./how-tos/onboard-your-data.md)
    2. [변칙 검색 구성 미세 조정](./how-tos/configure-metrics.md)
    3. [알림에 대한 변칙 구독](./how-tos/alerts.md)
    4. [진단 인사이트 보기](./how-tos/diagnose-an-incident.md)
3. REST API를 사용하여 인스턴스를 사용자 지정합니다.

## <a name="video"></a>동영상
* [Metrics Advisor 소개](https://www.youtube.com/watch?v=0Y26cJqZMIM)
* [Cognitive Services를 처음 사용하는 경우](https://www.youtube.com/watch?v=7tCLJHdBZgM)

## <a name="data-retention--limitation"></a>데이터 보존 및 제한: 

Metrics Advisor는 사용 가능한 데이터가 있는지 여부에 관계없이 현재 타임스탬프를 기준으로 최대 **10,000** 시간 간격([간격 이란?](tutorials/write-a-valid-query.md#what-is-an-interval))을 유지합니다. 데이터가 창 밖으로 벗어나면 삭제됩니다.  다양한 메트릭 세분화에 따른 일 수에 대한 데이터 보존 매핑: 

| 세분성(분) |    보존(일) |
|------------------| ------------------|
|  1 | 6.94 |
|  5 | 34.72|
| 15 | 104.1|
| 60(시간당) | 416.67 |
| 1440(=매일)|10000.00|

추가 제한 사항도 있습니다. 자세한 내용은 [FAQ](faq.yml#what-are-the-data-retention-and-limitations-of-metrics-advisor-)를 참조하세요. 

## <a name="next-steps"></a>다음 단계

* 빠른 시작 살펴보기: [웹에서 첫 번째 메트릭 모니터링](quickstarts/web-portal.md)
* 빠른 시작 살펴보기: [REST API를 사용하여 솔루션 사용자 지정](./quickstarts/rest-api-and-client-library.md)