---
title: 데이터 드리프트 모니터링 (미리 보기)
titleSuffix: Azure Machine Learning service
description: Azure Machine Learning 서비스에서 데이터 드리프트를 모니터링할 수 있는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: cody-dkdc
ms.author: copeters
ms.date: 06/20/2019
ms.openlocfilehash: 13f73718fabd711e9c71a56ac4537b2ebef8a411
ms.sourcegitcommit: c71306fb197b433f7b7d23662d013eaae269dc9c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/22/2019
ms.locfileid: "68371091"
---
# <a name="what-is-data-drift-monitoring-preview"></a>데이터 드리프트 모니터링 (미리 보기) 이란?

데이터 드리프트는 데이터 배포의 변화입니다. 기계 학습의 컨텍스트에서 학습 된 기계 학습 모델은 드리프트로 인 한 예측 성능을 저하 시킬 수 있습니다. 학습 데이터와 예측을 만드는 데 사용 되는 데이터 사이에 드리프트를 모니터링 하면 성능 문제를 감지 하는 데 도움이 됩니다.

기계 학습 모델은이를 학습 하는 데 사용 되는 데이터와 동일 합니다. 성능을 모니터링 하지 않고 프로덕션에 모델을 배포 하면 검색 되지 않고 부정적인 영향을 초래할 수 있습니다. 데이터 드리프트 모니터링을 통해 데이터 드리프트를 검색 하 고 적용할 수 있습니다. 

## <a name="when-to-monitor-for-data-drift"></a>데이터 드리프트를 모니터링 하는 경우

모니터링할 수 있는 메트릭은 다음과 같습니다.

+ 드리프트 크기 (드리프트 계수)
+ 드리프트의 원인 (기능별로 드리프트 기여도)
+ 거리 메트릭 (Wasserstein, 에너지 등)

이 모니터링이 준비 되 면 드리프트가 검색 되 고 데이터 과학자가 문제의 근본 원인을 조사할 수 있는 경우 경고 또는 작업을 설정할 수 있습니다. 

배포 된 모델에 대 한 입력 데이터가 변경 될 수 있다고 생각 되는 경우 데이터 드리프트 검색을 사용 하는 것이 좋습니다.

## <a name="how-data-drift-is-monitored-in-azure-machine-learning-service"></a>Azure Machine Learning service에서 데이터 드리프트를 모니터링 하는 방법

데이터 드리프트는 **Azure Machine Learning 서비스**를 사용 하 여 데이터 집합 또는 배포를 통해 모니터링 됩니다. 데이터 드리프트를 모니터링 하기 위해 기본 데이터 집합 (일반적으로 모델에 대 한 학습 데이터 집합)이 지정 됩니다. 두 번째 데이터 집합 (일반적으로 배포에서 수집 된 입력 데이터 모델)은 기준선 데이터 집합에 대해 테스트 됩니다. 데이터 집합은 모두 [프로 파일링](how-to-explore-prepare-data.md#explore-with-summary-statistics) 되 고 데이터 드리프트 모니터링 서비스에 입력 됩니다. 기계 학습 모델을 학습 하 여 두 데이터 집합 간의 차이를 검색 합니다. 모델의 성능은 두 데이터 집합 간의 드리프트 크기를 측정 하는 드리프트 계수로 변환 됩니다. [모델 interpretability](machine-learning-interpretability-explainability.md) 를 사용 하 여 드리프트 계수에 적용 되는 기능을 계산 합니다. 데이터 집합 프로필에서 각 기능에 대 한 통계 정보를 추적 합니다. 

## <a name="data-drift-metric-output"></a>데이터 드리프트 메트릭 출력

여러 가지 방법으로 드리프트 메트릭을 볼 수 있습니다.

* [Jupyter 위젯을 사용 합니다.](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets?view=azure-ml-py) `RunDetails`
* `get_metrics()` 모든`datadriftRun` 개체에서 함수를 사용 합니다.
* 모델에서 Azure Portal의 메트릭 보기

다음 메트릭은 데이터 드리프트 태스크에 대 한 각 실행 반복에 저장 됩니다.

|메트릭|Description|
--|--|
wasserstein_distance|1 차원 숫자 분포에 대해 정의 된 통계 거리입니다.|
energy_distance|1 차원 숫자 분포에 대해 정의 된 통계 거리입니다.|
datadrift_coefficient|공식적으로 Matthews 상관 계수 이며,-1에서 1 사이의 실수입니다. 드리프트 컨텍스트에서 0은 드리프트를 나타내고 1은 최대 드리프트를 나타냅니다.|
datadrift_contribution|드리프트에 영향을 주는 기능의 중요 한 기능입니다.|

## <a name="next-steps"></a>다음 단계

예제를 참조 하 고 데이터 드리프트를 모니터링 하는 방법을 알아보세요.

+ [AKS (Azure Kubernetes Service)를 통해 배포 된 모델에서 데이터 드리프트를 모니터링 하는 방법 알아보기](how-to-monitor-data-drift.md)
+ [Jupyter Notebook 샘플](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/data-drift/) 사용해 보기