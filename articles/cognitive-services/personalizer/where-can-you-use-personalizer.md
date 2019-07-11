---
title: 시나리오 평가 - Personalizer
titleSuffix: Azure Cognitive Services
description: 애플리케이션에서 표시할 적절한 항목, 작업 또는 제품을 선택할 수 있는 모든 상황에 Personalizer를 적용하여 환경을 더 효율적으로 만들거나, 더 나은 비즈니스 결과를 달성하거나, 생산성을 향상시킬 수 있습니다.
services: cognitive-services
author: edjez
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 05/07/2019
ms.author: edjez
ms.openlocfilehash: a5bf0d7d0478ad836ed1921b302ab01b004ec73c
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/10/2019
ms.locfileid: "67722221"
---
# <a name="where-can-you-use-personalizer"></a>Personalizer를 사용할 수 있는 경우

애플리케이션에서 표시할 적절한 항목, 작업 또는 제품을 선택해야 하는 모든 상황에서 Personalizer를 사용하여 환경을 더 효율적으로 만들거나, 더 나은 비즈니스 결과를 달성하거나, 생산성을 향상시킬 수 있습니다. 

Personalizer는 기계 학습을 사용하여 사용자에게 표시할 작업을 선택합니다. 선택은 서비스에 보내는 데이터의 양, 품질, 분포에 따라 크게 달라질 수 있습니다.

### <a name="checklist-for-applying-personalizer"></a>Personalizer을 적용하기 위한 검사 목록


Personalizer를 적용할 수 있는 경우는 다음과 같습니다.

* 애플리케이션에 대한 비즈니스 또는 유용성 목표가 있습니다.
* 사용자에게 표시할 항목을 상황에 맞게 결정하여 해당 목표를 향상시킬 수 있는 위치가 애플리케이션에 있습니다.
* 최상의 선택은 전체 사용자 동작과 총 보상 점수에서 학습할 수 있고 학습해야 합니다.
* 기계 학습을 맞춤 설정에 사용하는 경우 [사용 책임 지침](ethics-responsible-use.md)과 선택 항목을 따릅니다.
* 상황별 의사 결정은 일단의 제한된 선택 항목에서 최상의 옵션(작업) 순위를 지정하는 것으로 표현될 수 있습니다.
* 사용자 동작의 일부 측면을 측정하고 _보상 점수_로 표시하여 순위가 지정된 선택 항목이 애플리케이션에서 효율적으로 작동한 정도를 확인할 수 있습니다. 이는 -1과 1 사이의 숫자입니다.
* 보상 점수는 너무 많은 혼동 또는 외부 요인에 관계하지 않습니다. 실험 기간은 여전히 관련되고 있는 동안 보상 점수를 계산할 수 있을 만큼 충분히 짧습니다.
* 순위에 대한 컨텍스트는 올바른 선택을 하는 데 도움이 되며 PII(개인 식별 정보)가 포함되지 않은 5개 이상의 [기능](concepts-features.md)의 목록으로 표현할 수 있습니다.
* 각각의 콘텐츠 선택 항목인 _작업_에 대한 정보는 Personalizer에서 올바른 선택을 하는 데 도움이 될 것으로 생각되는 5개 이상의 [기능](concepts-features.md)의 목록으로 제공됩니다.
* 애플리케이션은 100,000개 이상의 상호 작용 기록을 누적할 수 있을 만큼 충분히 오랫동안 데이터를 보존할 수 있습니다.

## <a name="machine-learning-considerations-for-applying-personalizer"></a>Personalizer를 적용하기 위한 기계 학습 고려 사항

Personalizer는 제공하는 피드백을 통해 학습되는 기계 학습 방법인 보충 학습을 기반으로 합니다. 

Personalizer에서 가장 효율적으로 학습할 수 있는 상황은 다음과 같습니다.

* 시간이 지남에 따라 문제가 발생하면 최적의 맞춤 설정(예: 뉴스 또는 패션의 기본 설정)에 따라 유지할 수 있는 충분한 이벤트가 있습니다. Personalizer는 현실 세계의 지속적인 변화에 적응할 수 있지만, 새로운 패턴을 검색하고 해결하는 데 충분한 이벤트와 데이터가 없으면 최적의 결과가 되지 않습니다. 자주 발생하는 사용 사례를 선택해야 합니다. 하루에 500회 이상 발생하는 사용 사례를 확인하는 것이 좋습니다.
* 컨텍스트 및 작업에는 학습을 용이하게 하는 데 충분한 [기능](concepts-features.md)이 있습니다.
* 호출당 순위를 지정하는 50개 미만의 작업이 있습니다.
* 데이터 보존 설정을 통해 Personalizer는 오프라인 평가 및 정책 최적화를 수행하는 데 충분한 데이터를 수집할 수 있습니다. 이는 일반적으로 50,000개 이상의 데이터 요소입니다.

## <a name="monitor-effectiveness-of-personalizer"></a>Personalizer의 모니터 효율성

[오프라인 평가](concepts-offline-evaluation.md)를 수행하여 Personalizer의 효율성을 정기적으로 모니터링할 수 있습니다.

## <a name="use-personalizer-with-recommendation-engines"></a>추천 엔진에서 Personalizer 사용

많은 회사에서는 추천 엔진, 마케팅 및 캠페인 도구, 대상 그룹 구분 및 클러스터링, 공동 작업 필터링 및 대규모 카탈로그에서 제품을 고객에게 추천하는 다른 방법을 사용합니다.

[Microsoft Recommenders GitHub 리포지토리](https://github.com/Microsoft/Recommenders)는 Jupyter Notebook으로 제공되는 추천 시스템을 빌드하기 위한 예제와 모범 사례를 제공합니다. xDeepFM, SAR, ALS, RBM, DKN을 포함한 다양한 일반적인 방법에 대한 데이터 준비, 모델 작성, 추천 엔진의 평가, 튜닝 및 운영을 수행하는 작업 예제를 제공합니다.

추천 엔진이 있는 경우 Personalizer는 이를 사용하여 작업할 수 있습니다.

* 추천 엔진은 많은 양의 항목(예: 50만 개)을 사용하고 수백 또는 수천 개의 옵션에서 나온 하위 집합(예: 상위 20개)을 추천합니다.
* Personalizer는 많은 관련 정보를 포함한 적은 수의 작업을 사용하고 제공된 풍부한 컨텍스트에 맞게 실시간으로 순위를 지정하지만, 대부분의 추천 엔진은 사용자, 제품 및 상호 작용에 대한 몇 가지 특성만 사용합니다.
* Personalizer는 항상 사용자 기본 설정을 자동으로 검색하도록 설계되어 있으므로 뉴스, 라이브 이벤트, 라이브 커뮤니티 콘텐츠, 매일 업데이트되는 콘텐츠 또는 계절별 콘텐츠와 같이 콘텐츠가 빠르게 변하는 상황에서 더 나은 결과를 얻을 수 있습니다.

일반적으로 추천 엔진의 출력(예: 특정 고객에 대한 상위 20개 제품)을 가져와서 Personalizer에 대한 입력 작업으로 사용합니다.

## <a name="next-steps"></a>다음 단계

[윤리 및 사용 책임](ethics-responsible-use.md)