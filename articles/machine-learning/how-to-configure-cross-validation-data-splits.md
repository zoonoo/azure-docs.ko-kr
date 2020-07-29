---
title: 자동화 된 기계 학습 실험에서 교차 유효성 검사 및 데이터 분할 구성
titleSuffix: Azure Machine Learning
description: 자동화 된 machine learning 실험을 위해 교차 유효성 검사 및 데이터 집합 분할을 구성 하는 방법 알아보기
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.author: cesardl
author: CESARDELATORRE
ms.reviewer: nibaccam
ms.date: 06/16/2020
ms.openlocfilehash: 900d5cd435a913c0859c862d176fd30130e0a079
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87321499"
---
# <a name="configure-data-splits-and-cross-validation-in-automated-machine-learning"></a>자동화 된 기계 학습에서 데이터 분할 및 교차 유효성 검사 구성

이 문서에서는 자동화 된 machine learning, AutoML, 실험에 대해 학습/유효성 검사 데이터 분할 및 교차 유효성 검사를 구성 하는 다양 한 옵션에 대해 알아봅니다.

Azure Machine Learning에서 AutoML을 사용 하 여 여러 ML 모델을 작성 하는 경우 각 자식 실행은 정확도 또는 지 수 가중치와 같은 해당 모델의 품질 메트릭을 계산 하 여 관련 모델의 유효성을 검사 해야 합니다. 이러한 메트릭은 각 모델과의 예측을 유효성 검사 데이터의 과거 관찰 으로부터 실제 레이블과 비교 하 여 계산 합니다. 

AutoML 실험은 모델 유효성 검사를 자동으로 수행 합니다. 다음 섹션에서는 [Azure Machine Learning PYTHON SDK](https://docs.microsoft.com/python/api/overview/azure/ml/?view=azure-ml-py)를 사용 하 여 유효성 검사 설정을 추가로 사용자 지정할 수 있는 방법을 설명 합니다. 

코드 또는 코드가 없는 환경의 경우 [Azure Machine Learning studio에서 자동화 된 기계 학습 실험 만들기](how-to-use-automated-ml-for-ml-models.md)를 참조 하세요. 

> [!NOTE]
> 스튜디오는 현재 학습/유효성 검사 데이터 분할 및 교차 유효성 검사 옵션을 지원 하지만 유효성 검사 집합에 대 한 개별 데이터 파일을 지정 하는 것은 지원 하지 않습니다. 

## <a name="prerequisites"></a>사전 요구 사항

이 문서에는 다음이 필요 합니다.

* Azure Machine Learning 작업 영역 작업 영역을 만들려면 [Azure Machine Learning 작업 영역 만들기](how-to-manage-workspace.md)를 참조하세요.

* Azure Machine Learning SDK를 사용 하 여 자동화 된 machine learning 실험을 설정 하는 것에 대해 잘 알고 있어야 합니다. [자습서](tutorial-auto-train-models.md) 또는 [방법](how-to-configure-auto-train.md)에 따라 기본적인 자동화된 기계 학습 실험 디자인 패턴을 확인합니다.

* 교차 유효성 검사 및 학습/유효성 검사 데이터를 이해 하는 것은 ML 개념으로 분할 됩니다. 개략적인 설명의 경우

    * [Machine Learning의 학습, 유효성 검사 및 테스트 집합 정보](https://towardsdatascience.com/train-validation-and-test-sets-72cb40cba9e7)

    * [교차 유효성 검사 이해](https://towardsdatascience.com/understanding-cross-validation-419dbd47e9bd)

## <a name="default--data-splits-and-cross-validation"></a>기본 데이터 분할 및 교차 유효성 검사

[AutoMLConfig](https://docs.microsoft.com/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig?view=azure-ml-py) 개체를 사용 하 여 실험 및 학습 설정을 정의 합니다. 다음 코드 조각에서는 또는에 대 한 매개 변수가 `n_cross_validation` `validation_ data` 포함 **되지** 않은 필수 매개 변수만 정의 합니다.

```python
data = "https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/creditcard.csv"

dataset = Dataset.Tabular.from_delimited_files(data)

automl_config = AutoMLConfig(compute_target = aml_remote_compute,
                             task = 'classification',
                             primary_metric = 'AUC_weighted',
                             training_data = dataset,
                             label_column_name = 'Class'
                            )
```

또는 매개 변수 중 하나를 명시적으로 지정 하지 않으면 `validation_data` `n_cross_validation` automl은 제공 된 단일 데이터 집합의 행 수에 따라 기본 기술을 적용 합니다 `training_data` .

|학습 &nbsp; 데이터 &nbsp; 크기| 유효성 검사 기술 |
|---|-----|
|**&nbsp; &nbsp; 2만 행 보다 &nbsp; 큼**| 학습/유효성 검사 데이터 분할이 적용 됩니다. 기본값은 유효성 검사 집합으로 초기 학습 데이터 집합의 10%를 차지 하는 것입니다. 그러면이 유효성 검사 집합이 메트릭 계산에 사용 됩니다.
|**&nbsp; &nbsp; 2만 행 보다 &nbsp; 작음**| 교차 유효성 검사 방법이 적용 됩니다. 기본 접기 수는 행 수에 따라 달라 집니다. <br> **데이터 집합의 행이 1000 보다 작은 경우**에는 10 개의 접기가 사용 됩니다. <br> **행이 1000 ~ 2만 사이인 경우**세 가지 접기가 사용 됩니다.

## <a name="provide-validation-data"></a>유효성 검사 데이터 제공

이 경우 단일 데이터 파일로 시작 하 여 학습 및 유효성 검사 집합으로 분할 하거나 유효성 검사 집합에 대 한 별도의 데이터 파일을 제공할 수 있습니다. 어느 쪽이 든 `validation_data` 개체의 매개 변수는 `AutoMLConfig` 유효성 검사 집합으로 사용할 데이터를 할당 합니다. 이 매개 변수는 [Azure Machine Learning 데이터 집합](how-to-create-register-datasets.md) 또는 pandas 데이터 프레임 형식의 데이터 집합만을 허용 합니다.   

다음 코드 예제에서는에서 제공 되는 데이터 중 `dataset` 학습 및 유효성 검사에 사용할 데이터 부분을 명시적으로 정의 합니다.

```python
data = "https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/creditcard.csv"

dataset = Dataset.Tabular.from_delimited_files(data)

training_data, validation_data = dataset.random_split(percentage=0.8, seed=1)

automl_config = AutoMLConfig(compute_target = aml_remote_compute,
                             task = 'classification',
                             primary_metric = 'AUC_weighted',
                             training_data = training_data,
                             validation_data = validation_data,
                             label_column_name = 'Class'
                            )
```

## <a name="provide-validation-set-size"></a>유효성 검사 설정 크기를 제공 합니다.

이 경우 실험을 위해 단일 데이터 집합만 제공 됩니다. 즉, `validation_data` 매개 변수가 지정 **되지 않고** 제공 된 데이터 집합이 `training_data` 매개 변수에 할당 됩니다.  개체에서 `AutoMLConfig` `validation_size` 유효성 검사를 위해 학습 데이터의 일부를 포함 하도록 매개 변수를 설정할 수 있습니다. 즉, 제공 된 초기에서 자동 Ml에 의해 유효성 검사 집합이 분할 됩니다 `training_data` . 이 값은 0.0에서 1.0 사이 여야 합니다. 예를 들어 0.2는 유효성 검사 데이터에 대 한 20%의 데이터를 보유 하 고 있음을 의미 합니다.

다음 코드 예제를 참조 하세요.

```python
data = "https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/creditcard.csv"

dataset = Dataset.Tabular.from_delimited_files(data)

automl_config = AutoMLConfig(compute_target = aml_remote_compute,
                             task = 'classification',
                             primary_metric = 'AUC_weighted',
                             training_data = dataset,
                             validation_size = 0.2,
                             label_column_name = 'Class'
                            )
```

## <a name="set-the-number-of-cross-validations"></a>교차 유효성 검사의 수 설정

교차 유효성 검사를 수행 하려면 매개 변수를 포함 하 `n_cross_validations` 고 값으로 설정 합니다. 이 매개 변수는 동일한 접기 수에 따라 수행할 교차 유효성 검사의 수를 설정 합니다.

다음 코드에서는 교차 유효성 검사에 대 한 5 개의 접기가 정의 됩니다. 따라서 5 개의 서로 다른 학습, 각각 4/5 데이터를 사용 하는 각 학습 및 각각 다른 홀드 아웃을 사용 하는 데이터의 1/5을 사용 하 여 각 유효성 검사가 수행 됩니다.

따라서 메트릭은 5 개의 유효성 검사 메트릭의 평균을 사용 하 여 계산 됩니다.

```python
data = "https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/creditcard.csv"

dataset = Dataset.Tabular.from_delimited_files(data)

automl_config = AutoMLConfig(compute_target = aml_remote_compute,
                             task = 'classification',
                             primary_metric = 'AUC_weighted',
                             training_data = dataset,
                             n_cross_validations = 5
                             label_column_name = 'Class'
                            )
```

## <a name="specify-custom-cross-validation-data-folds"></a>사용자 지정 교차 유효성 검사 데이터 접기 지정

또한 사용자 고유의 CV (교차 유효성 검사) 데이터 접기를 제공할 수 있습니다. 분할 및 유효성 검사에 사용할 열을 지정 하는 것이 더 고급 시나리오로 간주 됩니다.  학습 데이터에 사용자 지정 CV 분할 열을 포함 하 고 매개 변수에서 열 이름을 채워서 열을 지정 `cv_split_column_names` 합니다. 각 열은 단일 교차 유효성 검사 분할을 나타내며 정수 값 1 또는 0으로 채워집니다. 여기서 1은 학습에 사용 되어야 하는 행을 나타내고 0은 행이 유효성 검사에 사용 되어야 함을 나타냅니다.

다음 코드 조각에는 두 개의 CV 분할 열인 ' cv1 ' 및 ' cv2 '가 포함 된 은행 마케팅 데이터가 포함 되어 있습니다.

```python
data = "https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/bankmarketing_with_cv.csv"

dataset = Dataset.Tabular.from_delimited_files(data)

automl_config = AutoMLConfig(compute_target = aml_remote_compute,
                             task = 'classification',
                             primary_metric = 'AUC_weighted',
                             training_data = dataset,
                             label_column_name = 'y',
                             cv_split_column_names = ['cv1', 'cv2']
                            )
```

> [!NOTE]
> `cv_split_column_names`및와 함께를 사용 하려면 `training_data` `label_column_name` Azure Machine Learning Python SDK 버전 1.6.0 이상으로 업그레이드 하세요. 이전 SDK 버전의 경우 사용을 참조 하세요 `cv_splits_indices` . 단, `X` 및 `y` 데이터 집합 입력에만 사용 됩니다. 

## <a name="next-steps"></a>다음 단계

* [불균형 데이터 및 과잉 맞춤를 방지](concept-manage-ml-pitfalls.md)합니다.
* [자습서: 자동화 된 machine learning을 사용 하 여 taxi 정의 요금-분할 데이터 섹션을 예측](tutorial-auto-train-models.md#split-the-data-into-train-and-test-sets)합니다.
* [시계열 예측 모델을 자동으로 학습](how-to-auto-train-forecast.md)하는 방법
