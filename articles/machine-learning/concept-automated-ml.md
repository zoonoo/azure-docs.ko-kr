---
title: 자동화 된 ML/AutoML 이란?
titleSuffix: Azure Machine Learning
description: 사용자가 알고리즘을 자동으로 선택 하 고 모델에서 모델을 생성 하 여 사용자가 모델에 가장 적합 한 알고리즘을 선택 하는 데 사용 하는 매개 변수 및 조건을 사용 하 여 시간을 절약할 수 있는 Azure Machine Learning 방법에 대해 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: cartacioS
ms.author: sacartac
ms.date: 04/22/2020
ms.openlocfilehash: ce51a1b25453a5bbacbd268b37f2bd21cfe37fea
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/08/2020
ms.locfileid: "82983468"
---
# <a name="what-is-automated-machine-learning-automl"></a>AutoML (자동화 된 기계 학습) 이란?

자동화 된 ML 또는 AutoML이 라고도 하는 자동화 된 machine learning은 기계 학습 모델 개발의 시간이 많이 걸리는 반복적인 작업을 자동화 하는 프로세스입니다. 이를 통해 데이터 과학자, 분석가 및 개발자는 모델 품질을 유지 하면서도 확장성, 효율성 및 생산성이 높은 ML 모델을 빌드할 수 있습니다. 자동화 된 ML은 [Microsoft Research 부서의](https://arxiv.org/abs/1705.05355)혁신을 기반으로 합니다.

기존의 기계 학습 모델 개발은 리소스를 많이 사용 하므로 수십 개의 모델을 생성 하 고 비교 하는 데 상당한 도메인 지식과 시간이 필요 합니다. 자동화된 Machine Learning을 통해 아주 쉽고 효율적으로 프로덕션을 준비할 수 있는 ML 모델을 준비하는 데 걸리는 시간을 단축할 수 있습니다.


## <a name="when-to-use-automl-classify-regression--forecast"></a>AutoML을 사용 하는 경우: 분류, 회귀, & 예측

지정한 대상 메트릭을 사용 하 여 모델을 학습 하 고 조정 하려면 자동화 된 ML을 적용 Azure Machine Learning 합니다. 자동화된 ML은 기계 학습 모델 개발 프로세스를 보편화하고, 데이터 과학 전문 지식에 관계없이 사용자의 역량을 강화하여 모든 문제에 대해 엔드투엔드 기계 학습 파이프라인을 식별합니다.

산업 전반에 걸친 데이터 과학자, 분석가 및 개발자는 자동화 된 ML을 사용 하 여 다음을 수행할 수 있습니다.
+ 광범위 한 프로그래밍 지식 없이 ML 솔루션 구현
+ 시간 및 리소스 절약
+ 데이터 과학 모범 사례 활용
+ Agile 문제 해결 제공

### <a name="classification"></a>분류

분류는 일반적인 기계 학습 작업입니다. 분류는 모델에서 학습 데이터를 사용 하 여 학습 하 고 이러한 학습를 새 데이터에 적용 하는 감독 학습의 유형입니다. Azure Machine Learning는 분류를 위한 심층 신경망 텍스트 featurizers와 같은 이러한 작업에 대해 특별히 featurizations을 제공 합니다. [기능화 옵션](how-to-use-automated-ml-for-ml-models.md#featurization)에 대해 자세히 알아보세요. 

분류 모델의 주요 목표는 학습 데이터의 학습을 기반으로 새 데이터를 넣을 범주를 예측 하는 것입니다. 일반적인 분류 예에는 사기 감지, 필기 인식 및 개체 검색이 포함 됩니다.  [자동화 된 machine learning을 사용 하 여 분류](tutorial-train-models-with-aml.md)의 예제를 자세히 알아보고 확인 하세요.

이러한 Python 노트북의 분류 및 자동화 된 기계 학습 예: [사기 감지](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-credit-card-fraud/auto-ml-classification-credit-card-fraud.ipynb), [마케팅 예측](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features/auto-ml-classification-bank-marketing-all-features.ipynb)및 [뉴스 그룹 데이터 분류](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-text-dnn/auto-ml-classification-text-dnn.ipynb) 를 참조 하세요.

### <a name="regression"></a>재발
분류와 마찬가지로, 회귀 작업도 일반적인 감독 된 학습 작업 이기도 합니다. Azure Machine Learning [은 이러한 작업에 대해 특별히 featurizations을](how-to-use-automated-ml-for-ml-models.md#featurization)제공 합니다.

예측 된 출력 값이 범주에 해당 하는 분류와는 달리 회귀 모델은 독립 예측 변수을 기반으로 숫자 출력 값을 예측 합니다. 회귀에서 목표는 한 변수가 다른 변수에 영향을 주는 방식을 예측 하 여 해당 하는 예측 변수 간의 관계를 설정 하는 데 도움을 주는 것입니다. 예를 들어, 가스 주행, 안전 등급 등의 기능을 기반으로 하는 자동차 가격입니다. [자동화 된 기계 학습을 사용 하 여](tutorial-auto-train-models.md)자세한 정보 및 재발 예를 확인 하세요.

이러한 Python 노트북의 예측에 대 한 재발 및 자동화 된 기계 학습의 예를 참조 하세요. [CPU 성능 예측](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/regression-hardware-performance-explanation-and-featurization/auto-ml-regression-hardware-performance-explanation-and-featurization.ipynb), 

### <a name="time-series-forecasting"></a>시계열 예측

예측 빌드는 수익, 재고, 판매 또는 고객 수요에 관계 없이 모든 비즈니스의 필수적인 부분입니다. 자동화 된 ML을 사용 하 여 기법과 접근 방식을 결합 하 고 권장 되는 고품질 시간 계열 예측을 얻을 수 있습니다. 이 방법: [시계열 예측에 대 한 자동화 된 기계 학습](how-to-auto-train-forecast.md)을 통해 자세히 알아보세요. 

자동화 된 시간 계열 실험은 다중 변형 회귀 문제로 처리 됩니다. 이전 시간 계열 값은 다른 예측 변수와 함께 회귀 변수의 추가 차원이 되도록 "피벗" 됩니다. 클래식 시계열 메서드와 달리이 방법은 학습 중에 여러 상황별 변수와 해당 변수 간의 관계를 자연스럽 게 통합 하는 이점을 제공 합니다. 자동화 된 ML은 데이터 집합 및 예측 horizons의 모든 항목에 대해 종종 내부적으로 분기 된 단일 모델을 학습 합니다. 따라서 모델 매개 변수를 예측 하는 데 더 많은 데이터를 사용할 수 있으며 보이지 않는 시리즈에 대 한 일반화가 가능 합니다.

고급 예측 구성에는 다음이 포함 됩니다.
* 휴일 검색 및 기능화
* 시계열 및 DNN 학습자 (Auto-ARIMA, Prophet, ForecastTCN)
* 그룹화를 통해 많은 모델 지원
* 롤링 원본 교차 유효성 검사
* 구성 가능한 지연
* 창 롤링 집계 기능


이러한 Python 노트북의 예측에 대 한 회귀 및 자동화 된 기계 학습 예를 참조 하세요. [판매 예측](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-orange-juice-sales/auto-ml-forecasting-orange-juice-sales.ipynb), [수요 예측](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb)및 [음료 프로덕션 예측](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-beer-remote/auto-ml-forecasting-beer-remote.ipynb).

## <a name="how-automl-works"></a>AutoML 작동 방법

학습 하는 동안 Azure Machine Learning 다양 한 알고리즘과 매개 변수를 시도 하는 많은 파이프라인을 동시에 만듭니다. 서비스는 각 반복이 학습 점수가 있는 모델을 생성 하는 기능 선택과 쌍을 이루는 ML 알고리즘을 반복 합니다. 점수가 높을수록 데이터를 "맞추기" 하는 것이 더 효율적입니다.  실험에 정의 된 종료 조건에 도달 하면 중지 됩니다. 

**Azure Machine Learning**를 사용 하 여 다음 단계를 통해 자동화 된 ML 학습 실험을 디자인 하 고 실행할 수 있습니다.

1. 해결 될 기계 학습 **문제 식별** : 분류, 예측 또는 회귀

1. **PYTHON sdk 또는 스튜디오 웹 환경을 사용할지 여부를 선택**합니다. [python sdk와 스튜디오 웹 환경](#parity)간의 패리티에 대해 알아봅니다.

   * 제한 되거나 코드를 실행 하지 않는 경우에는에서 Azure Machine Learning studio 웹 환경을 사용해 보세요.[https://ml.azure.com](https://ml.azure.com/)  
   * Python 개발자를 위한 [Azure Machine Learning PYTHON SDK](how-to-configure-auto-train.md) 를 확인 하세요. 

    [!INCLUDE [aml-applies-to-enterprise-sku](../../includes/aml-applies-to-enterprise-sku-inline.md)]  
    
1. **레이블이 지정 된 학습 데이터의 원본 및 형식 지정**: Numpy Array 또는 Pandas 데이터 프레임

1. **모델 학습을 위한 계산 목표**(예: [로컬 컴퓨터, Azure Machine Learning 계산, 원격 vm 또는 Azure Databricks](how-to-set-up-training-targets.md))를 구성 합니다.  [원격 리소스에](how-to-auto-train-remote.md)대 한 자동화 된 학습에 대해 알아봅니다.

1. 다른 모델에 대 한 반복 횟수, 하이퍼 매개 변수 설정, 고급 전처리/기능화 및 최상의 모델을 결정할 때 살펴볼 메트릭을 결정 하는 **자동화 된 machine learning 매개 변수를 구성 합니다** .  
1. **학습 실행을 제출 합니다.**

1. **결과 검토** 

다음 다이어그램은 이 프로세스를 보여 줍니다. 
![자동화 된 기계 학습](./media/concept-automated-ml/automl-concept-diagram2.png)


실행 중에 수집 된 [메트릭을 포함](how-to-understand-automated-ml.md) 하는 로그 된 실행 정보를 검사할 수도 있습니다. 학습 실행은 모델 및 데이터 전처리를 포함`.pkl` 하는 Python 직렬화 된 개체 (파일)를 생성 합니다.

모델 빌드를 자동화 하는 동안 생성 된 모델에 [중요 하거나 관련 된 기능을 배울](how-to-configure-auto-train.md#explain) 수도 있습니다.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE2Xc9t]

<a name="preprocess"></a>

## <a name="preprocessing"></a>전처리

자동화 된 모든 기계 학습 실험에서 데이터는 기본 메서드를 사용 하 여 전처리 되 고 선택적으로 고급 전처리를 통해 전처리 됩니다.

> [!NOTE]
> 자동화된 기계 학습 사전 처리 단계(기능 정규화, 누락된 데이터 처리, 텍스트를 숫자로 변환 등)는 기본 모델의 일부가 됩니다. 예측에 모델을 사용하는 경우 학습 중에 적용되는 동일한 전처리 단계가 입력 데이터에 자동으로 적용됩니다.

### <a name="automatic-preprocessing-standard"></a>자동 전처리 (표준)

자동화 된 모든 기계 학습 실험에서 데이터는 알고리즘의 성능을 향상 시킬 수 있도록 자동으로 확장 되거나 정규화 됩니다.  모델 학습 중에는 다음 크기 조정 또는 정규화 기술 중 하나가 각 모델에 적용 됩니다.

|정규화&nbsp;&&nbsp;확장| Description |
| ------------- | ------------- |
| [StandardScaleWrapper](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.StandardScaler.html)  | 단위 분산의 평균 및 크기 조정을 제거 하 여 기능 표준화  |
| [MinMaxScalar](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.MinMaxScaler.html)  | 해당 열의 최소값과 최대값을 기준으로 각 기능의 크기를 조정 하 여 기능을 변환 합니다.  |
| [MaxAbsScaler](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.MaxAbsScaler.html#sklearn.preprocessing.MaxAbsScaler) |최대 절대값으로 각 기능 크기 조정 |
| [RobustScalar](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.RobustScaler.html) |변 위치 범위에의 한 Scaler 기능 |
| [PCA](https://scikit-learn.org/stable/modules/generated/sklearn.decomposition.PCA.html) |데이터를 하위 차원 공간으로 프로젝션 하기 위한 단일 값 분해를 사용 하 여 선형 차원 축소 |
| [TruncatedSVDWrapper](https://scikit-learn.org/stable/modules/generated/sklearn.decomposition.TruncatedSVD.html) |이 변환기는 잘린 단일 값 분해 (.SVD)를 통해 선형 차원 감소를 수행 합니다. PCA와 달리이 평가기는 단일 값 분해를 계산 하기 전에 데이터를 중심으로 하지 않습니다. 즉, scipy 행렬을 효율적으로 사용할 수 있습니다. |
| [SparseNormalizer](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.Normalizer.html) | 0이 아닌 구성 요소를 하나 이상 포함 하는 각 샘플 (즉, 데이터 행렬의 각 행)은 다른 샘플과 독립적으로 재조정 하 여 일반적인 (l1 또는 l2)와 동일 하 게 구성 됩니다. |

### <a name="advanced-preprocessing--featurization"></a>고급 전처리 & 기능화

데이터 guardrails, 인코딩, 변환 등의 추가 고급 전처리 및 기능화도 사용할 수 있습니다. [기능화 포함 된 항목에 대해 자세히 알아보세요](how-to-use-automated-ml-for-ml-models.md#featurization). 다음을 사용 하 여이 설정 사용:

+ Azure Machine Learning studio: **추가 구성 보기** 섹션에서 [다음 단계를 수행 하](how-to-use-automated-ml-for-ml-models.md#create-and-run-experiment)여 **자동 기능화** 를 사용 하도록 설정 합니다.

+ Python SDK: `"feauturization": 'auto' / 'off' / 'FeaturizationConfig'` [ `AutoMLConfig` 클래스](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig)에 대해를 지정 합니다. 



## <a name="ensemble-models"></a><a name="ensemble"></a>앙상블 모델

자동화 된 machine learning은 기본적으로 사용 되는 앙상블 모델을 지원 합니다. 앙상블 learning은 단일 모델 사용과 반대로 여러 모델을 결합 하 여 기계 학습 결과와 예측 성능을 향상 시킵니다. 앙상블 반복은 실행의 최종 반복으로 나타납니다. 자동화 된 machine learning은 모델 결합을 위해 투표 및 스택 앙상블 메서드를 모두 사용 합니다.

* **투표**: 예측 된 클래스 확률 (분류 작업의 경우) 또는 예측 된 회귀 목표 (회귀 작업의 경우)의 가중치가 적용 된 평균을 기반으로 예측 합니다.
* **스택**: 스택은 다른 유형의 모델을 결합 하 고 개별 모델의 출력을 기반으로 메타 모델을 학습 합니다. 현재 기본 메타 모델은 분류 작업의 경우 LogisticRegression이 고 회귀/예측 작업의 경우 ElasticNet입니다.

[Caruana 앙상블 선택 알고리즘](http://www.niculescu-mizil.org/papers/shotgun.icml04.revised.rev2.pdf) 은 정렬 된 앙상블 초기화를 사용 하 여 앙상블 내에서 사용할 모델을 결정 하는 데 사용 됩니다. 높은 수준에서이 알고리즘은 가장 적합 한 개별 점수가 포함 된 최대 5 개의 모델을 사용 하 여 앙상블를 초기화 하 고 이러한 모델이 가장 높은 점수의 5% 임계값을 초과 하 여 초기 앙상블 저하를 방지 하는지 확인 합니다. 그런 다음 각 앙상블 반복에 대해 새 모델이 기존 앙상블에 추가 되 고 결과 점수가 계산 됩니다. 새 모델에서 기존 앙상블 점수가 향상 되 면 새 모델을 포함 하도록 앙상블이 업데이트 됩니다.

자동화 된 machine learning에서 기본 앙상블 설정을 변경 [하는 방법을](how-to-configure-auto-train.md#ensemble) 참조 하세요.

## <a name="guidance-on-local-vs-remote-managed-ml-compute-targets"></a><a name="local-remote"></a>로컬 및 원격 관리 ML 계산 대상에 대 한 지침

자동화 된 ML의 웹 인터페이스는 항상 원격 [계산 대상을](concept-compute-target.md)사용 합니다.  하지만 Python SDK를 사용 하는 경우 자동화 된 ML 학습을 위한 로컬 계산 또는 원격 계산 대상을 선택 합니다.

* **로컬 계산**: 교육은 로컬 노트북 또는 VM 계산에서 발생 합니다. 
* **원격 계산**: Machine Learning 계산 클러스터에서 교육이 발생 합니다.  

### <a name="choose-compute-target"></a>계산 대상 선택
계산 대상을 선택할 때 다음 요소를 고려 합니다.

 * **로컬 계산 선택**: 시나리오에 작은 데이터 및 짧은 기차를 사용 하는 초기 탐색 나 데모에 대 한 시나리오가 있는 경우 (예: 자식 실행 당 몇 분 또는 몇 분) 로컬 컴퓨터의 교육이 더 적합할 수 있습니다.  설치 시간이 없으며 인프라 리소스 (사용자 PC 또는 VM)를 직접 사용할 수 있습니다.
 * **원격 ML 계산 클러스터를 선택 합니다**. 더 큰 학습을 필요로 하는 모델을 만드는 프로덕션 학습에서와 같은 큰 데이터 집합을 사용 하 여 학습 하는 경우 원격 계산은 클러스터의 노드에 `AutoML` 대해 학습을 병렬화 하기 때문에 훨씬 더 나은 종단 간 시간 성능을 제공 합니다. 원격 계산에서 내부 인프라의 시작 시간은 자식 실행 당 1.5 분에 추가 되 고, Vm이 아직 실행 되지 않은 경우 클러스터 인프라의 추가 분이 추가 됩니다.

### <a name="pros-and-cons"></a>장점 및 단점
로컬 및 원격을 사용 하도록 선택할 때 이러한 장단점을 고려 합니다.

|  | 장점 (이점)  |단점 (Handicaps)  |
|---------|---------|---------|---------|
|**로컬 계산 대상** |  <li> 환경 시작 시간 없음   | <li>  기능의 하위 집합<li>  실행을 병렬화 할 수 없음 <li> 큰 데이터의 경우 더 악화 됩니다. <li>학습 하는 동안 데이터 스트리밍이 없습니다. <li>  DNN 기반 기능화 없음 <li> Python SDK만 해당 |
|**원격 ML 계산 클러스터**|  <li> 전체 기능 집합 <li> 자식 실행 병렬 처리 <li>   대량 데이터 지원<li>  DNN 기반 기능화 <li>  주문형 계산 클러스터의 동적 확장성 <li> 코드 없음 환경 (웹 UI)도 사용할 수 있습니다.  |  <li> 클러스터 노드의 시작 시간 <li> 각 자식 실행에 대 한 시작 시간    |

### <a name="feature-availability"></a>기능 가용성 

 아래 표에 나와 있는 것 처럼 원격 계산을 사용 하는 경우 더 많은 기능을 사용할 수 있습니다. 이러한 기능 중 일부는 엔터프라이즈 작업 영역 에서만 사용할 수 있습니다.

| 기능                                                    | 원격 | 로컬 | 위해서는 <br>엔터프라이즈 작업 영역 |
|------------------------------------------------------------|--------|-------|-------------------------------|
| 데이터 스트리밍 (대량 데이터 지원, 최대 100 GB)          | ✓      |       | ✓                             |
| DNN-BERT 기반 텍스트 기능화 및 교육             | ✓      |       | ✓                             |
| 기본 GPU 지원 (학습 및 유추)        | ✓      |       | ✓                             |
| 이미지 분류 및 레이블 지정 지원                  | ✓      |       | ✓                             |
| 예측을 위한 자동 ARIMA, Prophet 및 ForecastTCN 모델 | ✓      |       | ✓                             |
| 동시에 여러 번 실행/반복                       | ✓      |       | ✓                             |
| AutoML studio 웹 환경 UI에서 interpretability를 사용 하 여 모델 만들기      | ✓      |       | ✓                             |
| Studio 웹 환경 UI의 기능 엔지니어링 사용자 지정                        | ✓      |       | ✓                              |
| Azure ML 하이퍼 매개 변수 변수 튜닝                             | ✓      |       |                               |
| Azure ML 파이프라인 워크플로 지원                         | ✓      |       |                               |
| 계속 실행                                             | ✓      |       |                               |
| 예측                                                | ✓      | ✓     | ✓                             |
| 노트북에서 실험 만들기 및 실행                    | ✓      | ✓     |                               |
| UI에서 실험의 정보 및 메트릭을 등록 하 고 시각화 합니다. | ✓      | ✓     |                               |
| 데이터 guardrails                                            | ✓      | ✓     |                               |


## <a name="automated-ml-in-azure-machine-learning"></a>Azure Machine Learning의 자동화 된 ML

Azure Machine Learning는 자동화 된 ML 작업을 위한 두 가지 환경을 제공 합니다.

* 코드를 능숙 하 게 [Azure Machine Learning PYTHON SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) 

* 제한 된/아니요 코드 환경 고객의 경우 Azure Machine Learning studio[https://ml.azure.com](https://ml.azure.com/)  

<a name="parity"></a>

### <a name="experiment-settings"></a>실험 설정 

다음 설정을 사용 하 여 자동화 된 ML 실험을 구성할 수 있습니다. 

| |Python SDK|스튜디오 웹 환경|
----|:----:|:----:
학습/유효성 검사 집합으로 데이터 분할| ✓|✓
ML 작업 지원: 분류, 회귀 및 예측| ✓| ✓
기본 메트릭에 기반 하 여 최적화| ✓| ✓
AML 계산을 계산 대상으로 지원 | ✓|✓
예측 범위 구성, 대상 지연 & 롤링 창|✓|✓
종료 조건 설정 |✓|✓ 
동시 반복 설정| ✓|✓
열 삭제| ✓|✓
블록 알고리즘|✓|✓
교차 유효성 검사 |✓|✓
Azure Databricks 클러스터에 대 한 학습 지원| ✓|
엔지니어링 된 기능 이름 보기|✓|
기능화 요약| ✓|
휴일 기능화|✓|
로그 파일의 자세한 정도 수준| ✓|

### <a name="model-settings"></a>모델 설정

이러한 설정은 자동화 된 ML 실험의 결과로 최상의 모델에 적용할 수 있습니다.

| |Python SDK|스튜디오 웹 환경|
|----|:----:|:----:|
|최상의 모델 등록, 배포, explainability| ✓|✓|
|투표 앙상블 & stack 앙상블 모델 사용| ✓|✓|
|기본이 아닌 메트릭을 기반으로 최상의 모델 표시|✓||
|ONNX 모델 호환성 사용/사용 안 함|✓||
|모델 테스트 | ✓| |

### <a name="run-control-settings"></a>컨트롤 설정 실행

이러한 설정을 사용 하 여 실험 실행과 해당 자식 실행을 검토 하 고 제어할 수 있습니다. 

| |Python SDK|스튜디오 웹 환경|
|----|:----:|:----:|
|실행 요약 테이블| ✓|✓|
|자식 실행 & 취소 실행| ✓|✓|
|Guardrails 가져오기| ✓|✓|
|실행 일시 중지 & 다시 시작| ✓| |

<a name="use-with-onnx"></a>

## <a name="automl--onnx"></a>AutoML & ONNX

Azure Machine Learning를 사용 하면 자동화 된 ML을 사용 하 여 Python 모델을 작성 하 고이를 ONNX 형식으로 변환할 수 있습니다. 모델을 ONNX 형식으로 설정 하면 다양 한 플랫폼 및 장치에서 실행할 수 있습니다. [ONNX를 사용 하 여 ML 모델 가속화](concept-onnx.md)에 대해 자세히 알아보세요.

[이 Jupyter 노트북 예제에서](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features/auto-ml-classification-bank-marketing-all-features.ipynb)onnx 형식으로 변환 하는 방법을 참조 하세요. [ONNX에서 지원 되는 알고리즘](how-to-configure-auto-train.md#select-your-experiment-type)에 대해 알아봅니다.

ONNX 런타임은 c #도 지원 하므로, 기록이 필요 하지 않거나 REST 끝점이 도입 하는 네트워크 대기 시간 없이 c # 앱에서 자동으로 작성 된 모델을 사용할 수 있습니다. [Onnx RUNTIME c # API를 사용 하 여 추론 onnx 모델](https://github.com/Microsoft/onnxruntime/blob/master/docs/CSharp_API.md)에 대해 자세히 알아보세요. 

## <a name="next-steps"></a>다음 단계

자동화 된 machine learning을 사용 하 여 모델을 작성 하는 방법 및 예제를 참조 하세요.

+ 자동 학습 실험의 설정 구성:
  + Azure Machine Learning studio에서 [다음 단계를 사용](how-to-use-automated-ml-for-ml-models.md)합니다.
  + Python SDK를 사용 하 여 [다음 단계를 수행](how-to-configure-auto-train.md)합니다.

+ [원격 계산 대상을](how-to-auto-train-remote.md) 사용 하는 방법 알아보기

+ [자습서: 자동으로 회귀 모델 학습 Azure Machine Learning](tutorial-auto-train-models.md) 

+ 시계열 데이터를 사용 하 여 자동으로 학습 하는 방법에 대해 설명 하 고 [다음 단계를 사용](how-to-auto-train-forecast.md)합니다.

+ 자동화 된 [기계 학습에 대 한 Jupyter Notebook 샘플](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/) 사용해 보기
* 자동화 된 ML은, [ML.NET](https://docs.microsoft.com/dotnet/machine-learning/automl-overview), [HDInsight](../hdinsight/spark/apache-spark-run-machine-learning-automl.md), [Power BI](https://docs.microsoft.com/power-bi/service-machine-learning-automated) 및 [SQL Server](https://cloudblogs.microsoft.com/sqlserver/2019/01/09/how-to-automate-machine-learning-on-sql-server-2019-big-data-clusters/) 같은 다른 Microsoft 솔루션 에서도 사용할 수 있습니다.
