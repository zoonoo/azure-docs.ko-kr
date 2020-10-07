---
title: Metrics Advisor 데모 살펴보기
titleSuffix: Azure Cognitive Services
description: Microsoft에서 제공하는 데모를 사용하는 Metrics Advisor 웹 인터페이스에 대해 알아보기
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: quickstart
ms.date: 09/14/2020
ms.author: aahi
ms.openlocfilehash: fbc73e20b8cc2baa1cc5c5a5b2f674fb1b2dde84
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/05/2020
ms.locfileid: "90945784"
---
# <a name="quickstart-explore-the-metrics-advisor-demo-with-example-data"></a>빠른 시작: 예제 데이터를 사용하여 Metrics Advisor 데모 살펴보기

> [!Note]
> Metrics Advisor 데모는 읽기 전용이며, 사용자가 데이터를 온보딩할 수 없습니다. 데이터에서 서비스를 사용하려면 먼저 [Metrics Advisor 리소스를 생성](web-portal.md)합니다.

이 문서를 사용하여 Metrics Advisor의 주요 기능을 빠르게 살펴볼 수 있습니다. Microsoft에서는 사용자가 완전한 기능을 갖춘 웹 포털에 익숙해질 수 있도록 샘플 데이터 및 미리 설정된 구성이 포함된 데모 사이트를 제공합니다.

[이 링크](https://aka.ms/MetricsAdvisor/Demo)를 클릭하여 데모 사이트로 이동합니다.

## <a name="view-the-available-sample-data"></a>사용 가능한 샘플 데이터 보기

데모 사이트에 로그인하면 **데이터 피드** 페이지가 표시됩니다. 데이터 피드는 데이터 원본에서 쿼리되는 시계열 데이터의 논리적 그룹입니다. Metrics Advisor에서 사용되는 용어 및 개념에 대한 자세한 내용은 [용어집](../glossary.md)을 참조하세요. 

Azure SQL Database 또는 Azure Table과 같은 다양한 유형의 데이터 원본에서 수집되는 여러 데이터 피드가 나열됩니다. 각각 약간 다른 구성 설정을 사용하여 관련 데이터 저장소에 연결합니다.

:::image type="content" source="../media/sample-datafeeds.png" alt-text="샘플 데이터 목록" lightbox="../media/sample-datafeeds.png":::

## <a name="explore-the-data-feed-configurations"></a>데이터 피드 구성 살펴보기

*샘플 - 비용/수익 - 구/군/시/범주* 데이터 피드를 클릭합니다. 피드에 대한 몇 가지 세부 정보 섹션을 볼 수 있습니다.

* 데이터 피드 이름 및 수집 상태
* 데이터 원본에서 쿼리된 메트릭의 목록. 예를 들면 *비용* 및 *수익*이 있습니다. 
* 데이터 피드를 사용할 수 없게 된 시점에 대한 경고 내역입니다. 
* 데이터 피드가 업데이트된 시점에 대한 로그입니다.   
* 데이터 피드 정보 및 설정

:::image type="content" source="../media/data-feed-view.png" alt-text="샘플 데이터 목록" lightbox="../media/data-feed-view.png":::


## <a name="view-time-series-visualizations-and-configurations"></a>시계열 시각화 및 구성 보기

*샘플 - 비용/수익 - 구/군/시/범주* 데이터 피드에서 *비용* 메트릭을 클릭합니다. 메트릭 데이터 기록에 따라 시각화를 사용하여 차원으로 분할된 관련 시계열을 볼 수 있습니다. 메트릭 데이터 주위의 파란색 밴드는 Metrics Advisor의 기계 학습 모델에서 예상 값 범위를 나타냅니다. 이 밴드의 외부에 있는 포인트는 빨간색 점으로 표시되며 검색된 변칙입니다. 

:::image type="content" source="../media/series-visualization.png" alt-text="샘플 데이터 목록" lightbox="../media/series-visualization.png":::

변칙 검색은 메트릭 세부 정보 페이지의 왼쪽에서**검색 구성**을 조정하여 구성할 수 있습니다. 여러 변칙 검색 방법을 사용할 수 있으며 검색 방법을 결합할 수도 있습니다. 여러 민감도, 검색 방향 및 기타 구성을 추가로 시도할 수 있습니다. **검색 구성** 아래쪽에 있는 **고급 구성** 링크를 사용하여 그룹 또는 개별 계열에서 사용할 수 있는 보다 복잡하고 사용자 지정된 검색 설정을 만들 수 있습니다. 

검색 알고리즘에 대한 피드백을 제공하여 변칙 검색을 튜닝할 수도 있습니다. 변칙을 클릭하고 **피드백 추가** 패널을 사용하여 변칙 상태, 계절성 및 변화 포인트 상태를 구성합니다. 이러한 피드백은 향후 포인트 검색에 통합됩니다.  

**피드백 추가** 패널 하단에는 **대상 인시던트 허브**에 대한 링크가 있습니다. 이를 통해 인시던트 분석 페이지로 이동하고 인시던트의 근본 원인을 분석할 수 있습니다.  

:::image type="content" source="../media/incident-link.png" alt-text="샘플 데이터 목록" lightbox="../media/incident-link.png":::

## <a name="explore-anomaly-detection-results-and-perform-root-cause-analysis"></a>변칙 검색 결과를 살펴보고 근본 원인 분석 수행

변칙에서 **대상 인시던트 허브** 링크를 클릭하면 인시던트에 대한 진단 인시던트(예: 심각도, 관련 변칙 수 및 시작/종료 시간)가 포함된 인시던트 분석 페이지가 표시됩니다. **근본 원인** 섹션에서는 인시던트의 근본 원인일 수 있는 부모 변칙에 대한 편차, 분포 및 기여를 고려하여 인시던트 트리를 분석하고 자동 조언을 표시합니다.

**진단** 섹션에는 인시던트를 진단하기 위한 몇 가지 탭과 함께 인시던트의 트리가 표시됩니다.

:::image type="content" source="../media/incident-diagnostic.png" alt-text="샘플 데이터 목록" lightbox="../media/incident-diagnostic.png":::

인시던트의 근본 원인을 정확히 짚어내면 상황이 악화되기 전에 조치를 취하고 문제를 완화할 수 있습니다. 또한 제공된 다른 진단 기능을 클릭하여 더 많은 인시던트를 살펴볼 수도 있습니다. 

## <a name="next-steps"></a>다음 단계

- [웹 포털 사용](web-portal.md)
- [REST API 사용](rest-api.md)
- [데이터 피드 온보딩](../how-tos/onboard-your-data.md)
    - [데이터 피드 관리](../how-tos/manage-data-feeds.md)
    - [여러 데이터 원본에 대한 구성](../data-feeds-from-different-sources.md)
