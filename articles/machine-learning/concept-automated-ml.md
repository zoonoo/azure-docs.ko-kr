---
title: 자동화 된 ML / 자동 ML이란 무엇입니까?
titleSuffix: Azure Machine Learning
description: Azure Machine Learning에서 자동으로 알고리즘을 선택하고 모델에서 모델을 생성하여 모델에 가장 적합한 알고리즘을 선택하는 데 제공하는 매개 변수 및 조건을 사용하여 시간을 절약할 수 있는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: cartacioS
ms.author: sacartac
ms.date: 02/28/2020
ms.openlocfilehash: c8864e00be9f491d87478c253286070b9334a26e
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2020
ms.locfileid: "80383194"
---
# <a name="what-is-automated-machine-learning"></a>자동화된 Machine Learning이란?

자동화된 기계 학습은 자동화 된 ML이라고도하며 기계 학습 모델 개발의 시간 소모적이고 반복적인 작업을 자동화하는 프로세스입니다. 이를 통해 데이터 과학자, 분석가 및 개발자는 모델 품질을 유지하면서 높은 규모, 효율성 및 생산성으로 ML 모델을 구축할 수 있습니다. 자동화 된 ML은 Microsoft [연구 부서의](https://arxiv.org/abs/1705.05355)돌파구를 기반으로합니다.

기존의 기계 학습 모델 개발은 리소스집약적이며, 수십 개의 모델을 생산하고 비교하는 데 상당한 도메인 지식과 시간이 필요합니다. 자동화된 Machine Learning을 통해 아주 쉽고 효율적으로 프로덕션을 준비할 수 있는 ML 모델을 준비하는 데 걸리는 시간을 단축할 수 있습니다.


 


## <a name="when-to-use-automated-ml"></a>자동화된 ML을 사용하는 경우

Azure 기계 학습에서 지정한 대상 메트릭을 사용하여 모델을 학습하고 조정하려는 경우 자동화된 ML을 적용합니다. 자동화된 ML은 기계 학습 모델 개발 프로세스를 보편화하고, 데이터 과학 전문 지식에 관계없이 사용자의 역량을 강화하여 모든 문제에 대해 엔드투엔드 기계 학습 파이프라인을 식별합니다.

산업 전반의 데이터 과학자, 분석가 및 개발자는 자동화된 ML을 사용하여 다음을 수행할 수 있습니다.

+ 광범위한 프로그래밍 지식 없이 기계 학습 솔루션 구현
+ 시간 및 리소스 절약
+ 데이터 과학 모범 사례 활용
+ Agile 문제 해결 제공

다음 표에는 일반적인 자동화된 ML 사용 사례가 나열되어 있습니다. 

분류| 타임시리즈 예측 | 회귀
---|---|---
[사기 탐지](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-credit-card-fraud/auto-ml-classification-credit-card-fraud.ipynb)|[판매 예측](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-orange-juice-sales/auto-ml-forecasting-orange-juice-sales.ipynb)|[CPU 성능 예측](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/regression-hardware-performance-explanation-and-featurization/auto-ml-regression-hardware-performance-explanation-and-featurization.ipynb)
|[마케팅 예측](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features/auto-ml-classification-bank-marketing-all-features.ipynb)|[수요 예측](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb)|
|[뉴스 그룹 데이터 분류](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-text-dnn/auto-ml-classification-text-dnn.ipynb)|[음료 생산 예측](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-beer-remote/auto-ml-forecasting-beer-remote.ipynb)|

## <a name="design-automated-ml-experiments"></a>자동화된 ML 실험 설계

**Azure 기계 학습을**사용하여 다음 단계를 사용하여 자동화된 ML 학습 실험을 디자인하고 실행할 수 있습니다.

1. 해결해야 할 **ML 문제 식별:** 분류, 예측 또는 회귀

1. **레이블이 지정된 교육 데이터의 소스 및 형식 지정**: Numpy 배열 또는 팬더 데이터프레임

1. [로컬 컴퓨터, Azure 기계 학습 계산 계산, 원격 VM 또는 Azure Databricks와](how-to-set-up-training-targets.md)같은 **모델 학습에 대한 계산 대상을 구성합니다.**  [원격 리소스에 대한 자동화된](how-to-auto-train-remote.md)교육에 대해 알아봅니다.

1. **자동화된 기계 학습 매개 변수를 구성하여** 다양한 모델에 대한 반복 수, 하이퍼매개 변수 설정, 고급 전처리/위화및 최적의 모델을 결정할 때 살펴볼 메트릭을 결정합니다.  [Azure 기계 학습 스튜디오](https://ml.azure.com)또는 [SDK를 사용 하](how-to-configure-auto-train.md)고 자동 학습 실험에 대 한 설정을 구성할 수 있습니다. 

    [!INCLUDE [aml-applies-to-enterprise-sku](../../includes/aml-applies-to-enterprise-sku-inline.md)]

1. **교육 실행을 제출합니다.**

## <a name="how-automated-ml-works"></a>자동화된 ML 작동 방법

학습 하는 동안 Azure 기계 학습 다른 알고리즘 및 매개 변수를 시도 하는 병렬 파이프라인에서 의 수를 만듭니다. 이 서비스는 기능 선택과 페어링된 ML 알고리즘을 반복하며, 각 반복은 학습 점수가 있는 모델을 생성합니다. 점수가 높을수록 모델이 데이터에 "적합"하게 간주됩니다.  실험에 정의된 종료 기준에 도달하면 중지됩니다. 다음 다이어그램은 이 프로세스를 보여 줍니다. 

  ![자동화된 기계 학습](./media/concept-automated-ml/automl-concept-diagram2.png)


실행 중에 수집된 [메트릭이 포함된](how-to-understand-automated-ml.md) 기록된 실행 정보를 검사할 수도 있습니다. 학습 실행은 모델 및 데이터`.pkl` 전처리를 포함하는 Python 직렬화된 개체(파일)를 생성합니다.

모델 작성이 자동화되어 있는 동안 생성된 [모델에 얼마나 중요하거나 관련성이 있는지 도 알](how-to-configure-auto-train.md#explain) 수 있습니다.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE2Xc9t]

<a name="preprocess"></a>

## <a name="preprocessing"></a>전처리

모든 자동화된 기계 학습 실험에서 데이터는 기본 방법을 사용하고 선택적으로 고급 전처리를 통해 전처리됩니다.

> [!NOTE]
> 자동화된 기계 학습 사전 처리 단계(기능 정규화, 누락된 데이터 처리, 텍스트를 숫자로 변환 등)는 기본 모델의 일부가 됩니다. 예측에 모델을 사용하는 경우 학습 중에 적용되는 동일한 전처리 단계가 입력 데이터에 자동으로 적용됩니다.

### <a name="automatic-preprocessing-standard"></a>자동 전처리(표준)

자동화된 모든 기계 학습 실험에서 알고리즘이 잘 수행될 수 있도록 데이터가 자동으로 확장또는 정규화됩니다.  모델 학습 중에 다음 크기 조정 또는 정규화 기술 중 하나가 각 모델에 적용됩니다.

|정규화 크기 조정&nbsp;&&nbsp;| 설명 |
| ------------- | ------------- |
| [스탠다드스케일래퍼](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.StandardScaler.html)  | 평균을 제거하고 단위 분산으로 배율 조정하여 피처를 표준화  |
| [민맥스스칼라](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.MinMaxScaler.html)  | 각 피처를 해당 열의 최소 및 최대값으로 배율조정하여 피처를 변환합니다.  |
| [맥스압스스케일러](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.MaxAbsScaler.html#sklearn.preprocessing.MaxAbsScaler) |각 피쳐의 최대 절대값으로 배율 조정 |
| [로피스스칼라](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.RobustScaler.html) |이 스케일러는 퀀열 범위에 따라 기능합니다. |
| [PCA](https://scikit-learn.org/stable/modules/generated/sklearn.decomposition.PCA.html) |데이터의 단수 값 분해를 사용하여 선형 치수 감소하여 더 낮은 차원 공간으로 투영 |
| [잘린SVD래퍼](https://scikit-learn.org/stable/modules/generated/sklearn.decomposition.TruncatedSVD.html) |이 변압기는 잘린 단수 값 분해(SVD)를 통해 선형 치수 감소를 수행합니다. PCA와 는 달리, 이 추정기는 특이값 분해를 계산하기 전에 데이터를 중앙에 두지 않으므로 scipy.sparse 행렬로 효율적으로 작업할 수 있습니다. |
| [스파스정규리세이너](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.Normalizer.html) | 하나 이상의 0이 아닌 구성 요소가 있는 각 샘플(즉, 데이터 행렬의 각 행)은 다른 샘플과 독립적으로 배율이 조정되어 표준(l1 또는 l2)이 1개와 같아집니다. |

### <a name="advanced-preprocessing-optional-featurization"></a>고급 전처리: 선택적 위화

데이터 가드레일, 인코딩 및 변환과 같은 고급 전처리 및 위화도 사용할 수 있습니다. [어떤 위업이 포함되어 있는지 자세히 알아보십시오.](how-to-use-automated-ml-for-ml-models.md#featurization) 다음을 사용하여 이 설정을 사용하도록 설정합니다.

+ Azure 기계 학습 스튜디오: 다음 단계와 함께 **추가 구성** 보기 섹션에서 **자동 위화** 를 사용하도록 [설정합니다.](how-to-use-automated-ml-for-ml-models.md#create-and-run-experiment)

+ 파이썬 SDK `"feauturization": 'auto' / 'off' / 'FeaturizationConfig'` : [ `AutoMLConfig` 클래스에](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig)대한 지정 . 

## <a name="classification--regression"></a>분류 & 회귀

분류 및 회귀는 가장 일반적인 유형의 기계 학습 작업입니다. 둘 다 모델이 학습 데이터를 사용하여 학습하고 이러한 학습을 새 데이터에 적용하는 감독 학습 유형입니다. Azure 기계 학습은 분류를 위한 심층 신경망 텍스트 위화기와 같은 이러한 작업에 대해 특별히 위화기를 제공합니다. [위화 옵션에](how-to-use-automated-ml-for-ml-models.md#featurization)대해 자세히 알아보십시오. 

분류 모델의 주요 목표는 교육 데이터의 학습을 기반으로 새 데이터가 어떤 범주에 속하는지 예측하는 것입니다. 일반적인 분류 예로는 사기 탐지, 필기 인식 및 개체 감지가 포함됩니다.  [자동화된 기계 학습을 통해 분류](tutorial-train-models-with-aml.md)예제를 참조하세요.

예측 출력 값이 범주형인 분류와 는 달리 회귀 모델은 독립적인 예측 변수를 기반으로 수치 출력 값을 예측합니다. 회귀에서 목적은 하나의 변수가 다른 변수에 미치는 영향을 추정하여 이러한 독립적인 예측 변수 간의 관계를 설정하는 데 도움을 줍니다. 예를 들어, 자동차 가격은 가스 마일리지, 안전 등급 등과 같은 기능을 기반으로 합니다. [자동화된 기계 학습을 통해 회귀의](tutorial-auto-train-models.md)예를 자세히 알아보고 참조하세요.

## <a name="time-series-forecasting"></a>시계열 예측

빌딩 예측은 수익, 재고, 판매 또는 고객 수요 등 모든 비즈니스에서 필수적인 부분입니다. 자동화된 ML을 사용하여 기술과 접근 방식을 결합하고 권장되는 고품질 의 시간계 예측을 얻을 수 있습니다.

자동화된 시간계 실험은 다변량 회귀 문제로 처리됩니다. 과거 타임계 값은 다른 예측 변수와 함께 회귀 변수에 대한 추가 차원이 되도록 "피벗"됩니다. 이 접근 방식은 기존 타임시리즈 방법과 달리 교육 중에 여러 컨텍스트 변수와 서로의 관계를 자연스럽게 통합할 수 있는 장점이 있습니다. 자동화된 ML은 데이터 집합 및 예측 지평의 모든 항목에 대해 단일 하지만 종종 내부적으로 분기된 모델을 학습합니다. 따라서 모델 매개 변수를 추정하고 보이지 않는 계열에 대한 일반화가 가능해지는 데 더 많은 데이터를 사용할 수 있습니다.

자세히 알아보고 [열렬 예측을 위한 자동화된 기계 학습의](how-to-auto-train-forecast.md)예를 참조하십시오. 또는 다음을 포함한 고급 예측 구성의 자세한 코드 예제는 [에너지 수요 노트북을](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb) 참조하십시오.

* 휴일 감지 및 위화
* 시계열 및 DNN 학습자 (자동 아리마, 예언자, 예측TCN)
* 그룹화를 통해 많은 모델이 지원됩니다.
* 롤링 오리진 교차 검증
* 구성 가능한 지연
* 롤링 윈도우 집계 기능

## <a name="ensemble-models"></a><a name="ensemble"></a>앙상블 모델

자동화된 기계 학습은 기본적으로 활성화된 앙상블 모델을 지원합니다. 앙상블 학습은 단일 모델을 사용하는 것이 아니라 여러 모델을 결합하여 기계 학습 결과와 예측 성능을 향상시킵니다. 앙상블 반복은 실행의 마지막 반복으로 나타납니다. 자동화된 기계 학습은 모델을 결합하기 위한 투표 및 스태킹 앙상블 방법을 모두 사용합니다.

* **투표**: 예측 된 클래스 확률 (분류 작업의 경우) 또는 예측 회귀 목표 (회귀 작업의 경우)의 가중 평균을 기반으로 예측합니다.
* **스태킹**: 스태킹은 이종 모델을 결합하고 개별 모델의 출력을 기반으로 메타 모델을 훈련시바입니다. 현재 기본 메타 모델은 분류 작업에 대한 로지스틱회귀이며 회귀/예측 작업에 대한 ElasticNet입니다.

정렬된 앙상블 초기화가 있는 [카루아나 앙상블 선택 알고리즘은](http://www.niculescu-mizil.org/papers/shotgun.icml04.revised.rev2.pdf) 앙상블 내에서 사용할 모델을 결정하는 데 사용됩니다. 높은 수준에서, 이 알고리즘은 최고의 개별 점수와 최대 5 개 모델로 앙상블을 초기화하고,이 모델은 가난한 초기 앙상블을 피하기 위해 최고의 점수의 5 % 임계 값 내에 있는지 확인합니다. 그런 다음 각 앙상블 반복에 대해 새 모델이 기존 앙상블에 추가되고 결과 점수가 계산됩니다. 새로운 모델이 기존 앙상블 점수를 향상하면 앙상블이 새 모델을 포함하도록 업데이트됩니다.

자동화된 기계 학습에서 기본 앙상블 설정을 변경하는 [방법을 참조하세요.](how-to-configure-auto-train.md#ensemble)

## <a name="use-with-onnx-in-c-apps"></a>C# 앱에서 ONNX와 함께 사용

Azure 기계 학습을 사용하면 자동화된 ML을 사용하여 파이썬 모델을 빌드하고 ONNX 형식으로 변환할 수 있습니다. ONNX 런타임은 C#을 지원하므로 RE코딩이나 REST 엔드포인트가 도입하는 네트워크 대기 시간 없이 C# 앱에서 자동으로 빌드된 모델을 사용할 수 있습니다. [이 Jupyter 노트북에서](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features/auto-ml-classification-bank-marketing-all-features.ipynb)이러한 흐름의 예를 들어 보십시오.

## <a name="automated-ml-in-azure-machine-learning"></a>Azure 기계 학습의 자동화된 ML

Azure 기계 학습은 자동화된 ML 작업을 위한 두 가지 환경을 제공합니다.

* 코드 경험이 풍부한 고객의 경우 [Azure 기계 학습 파이썬 SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) 

* 제한된/무코드 환경 고객의 경우 Azure 기계 학습 스튜디오에서[https://ml.azure.com](https://ml.azure.com/)  

다음은 각 환경에서 지원되는 높은 수준의 자동화된 ML 기능을 요약한 것입니다.

<a name="parity"></a>

### <a name="experiment-settings"></a>실험 설정 

다음 설정을 통해 자동화된 ML 실험을 구성할 수 있습니다. 

| | Python SDK| 스튜디오
----|:----:|:----:
데이터를 학습/유효성 검사 세트로 분할| ✓|✓
ML 작업 지원: 분류, 회귀 및 예측| ✓| ✓
기본 메트릭을 기반으로 최적화| ✓| ✓
AML 계산을 계산 대상으로 지원 | ✓|✓
예측 수평선 구성, 대상 지연 & 롤링 창|✓|✓
종료 조건 설정 |✓|✓ 
동시 반복 설정| ✓|✓
열 삭제| ✓|✓
블록 알고리즘|✓|✓
교차 유효성 검사 |✓|✓
Azure 데이터브릭 클러스터에 대한 교육 지원| ✓|
엔지니어링 피쳐 이름 보기|✓|
위화 요약| ✓|
휴일 위화|✓|
로그 파일에 대한 자세한 수준| ✓|

### <a name="model-settings"></a>모델 설정

이러한 설정은 자동화된 ML 실험의 결과로 최상의 모델에 적용할 수 있습니다.

||Python SDK|스튜디오
----|:----:|:----:
최고의 모델 등록| ✓|✓
최고의 모델 배포| ✓| ✓
최고의 모델 설명| ✓|✓
투표 앙상블 & 스택 앙상블 모델 사용| ✓|✓
기본이 아닌 메트릭을 기반으로 최상의 모델 표시|✓|
ONNX 모델 호환성 사용/비활성화|✓|
모델 테스트 | ✓| |

### <a name="run-control-settings"></a>제어 설정 실행

이러한 설정을 사용하면 실험 실행 및 자식 실행을 검토하고 제어할 수 있습니다. 

||Python SDK| 스튜디오
----|:----:|:----:
요약 테이블 실행| ✓|✓
실행 취소| ✓|✓
자식 실행 취소| ✓| ✓
가드레일 받기| ✓|✓
일시 중지 실행| ✓| 
실행 재개| ✓| 

## <a name="next-steps"></a>다음 단계

예제를 참조하고 자동화된 기계 학습을 사용하여 모델을 빌드하는 방법을 알아봅니다.

+ 자습서 [따르기: Azure 기계 학습을 통해 회귀 모델을 자동으로 학습합니다.](tutorial-auto-train-models.md)

+ 자동 학습 실험에 대한 설정을 구성합니다.
  + Azure 기계 학습 스튜디오에서 [다음 단계를 사용합니다.](how-to-use-automated-ml-for-ml-models.md)
  + 파이썬 SDK를 [사용하여 다음 단계를 사용합니다.](how-to-configure-auto-train.md)

+ 시계열 데이터를 사용하여 자동 학습하는 방법에 대해 알아보려면 [다음 단계를 사용합니다.](how-to-auto-train-forecast.md)

+ [자동화된 기계 학습을 위한 Jupyter 노트북 샘플](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/) 사용해 보십시오.

* 자동화 된 ML은 [ML.NET,](https://docs.microsoft.com/dotnet/machine-learning/automl-overview) [HDInsight,](../hdinsight/spark/apache-spark-run-machine-learning-automl.md) [파워 BI](https://docs.microsoft.com/power-bi/service-machine-learning-automated) 및 [SQL 서버와](https://cloudblogs.microsoft.com/sqlserver/2019/01/09/how-to-automate-machine-learning-on-sql-server-2019-big-data-clusters/) 같은 다른 Microsoft 솔루션에서도 사용할 수 있습니다.
