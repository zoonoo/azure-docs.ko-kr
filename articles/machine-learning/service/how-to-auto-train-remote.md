---
title: 자동화된 ML 원격 컴퓨팅 대상
titleSuffix: Azure Machine Learning service
description: Azure Machine Learning 서비스를 사용 하 여 Azure Machine Learning 원격 계산 대상에서 자동화 된 machine learning을 사용 하 여 모델을 빌드하는 방법을 알아봅니다.
services: machine-learning
author: nacharya1
ms.author: nilesha
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.workload: data-services
ms.topic: conceptual
ms.date: 7/12/2019
ms.openlocfilehash: 3c3205b64803ac4ee67997ef546ffd64c89f23b4
ms.sourcegitcommit: 55e0c33b84f2579b7aad48a420a21141854bc9e3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/19/2019
ms.locfileid: "69624823"
---
# <a name="train-models-with-automated-machine-learning-in-the-cloud"></a>클라우드의 자동화된 기계 학습을 사용하여 모델 학습

Azure Machine Learning에서 관리하는 다양한 종류의 컴퓨팅 리소스에 대해 모델을 교육할 수 있습니다. 계산 대상은 클라우드의 리소스 또는 로컬 컴퓨터 일 수 있습니다.

Azure Machine Learning Compute (AmlCompute)와 같은 추가 계산 대상을 추가 하 여 machine learning 실험을 쉽게 확장 하거나 축소할 수 있습니다. AmlCompute는 단일 또는 다중 노드 계산을 쉽게 만들 수 있는 관리 되는 계산 인프라입니다.

이 문서에서는 AmlCompute에서 자동화 된 ML을 사용 하 여 모델을 작성 하는 방법에 대해 알아봅니다.

## <a name="how-does-remote-differ-from-local"></a>원격과 로컬의 차이

"[자동화 된 기계 학습으로 분류 모델 학습](tutorial-auto-train-models.md)" 자습서에서는 로컬 컴퓨터를 사용 하 여 자동화 된 ML을 사용 하 여 모델을 학습 하는 방법을 배웁니다. 로컬로 교육하는 워크플로는 원격 대상에도 적용됩니다. 그러나 원격 컴퓨팅을 사용하면 자동화된 ML 실험 반복이 비동기적으로 실행됩니다. 이 기능을 사용하면 특정 반복을 취소하거나, 실행 상태를 확인하거나, Jupyter 노트의 다른 셀에서 계속 작업할 수 있습니다. 원격으로 학습 하려면 AmlCompute와 같은 원격 계산 대상을 먼저 만듭니다. 그런 다음, 원격 리소스를 구성하고 코드를 여기에 제출합니다.

이 문서에서는 원격 AmlCompute 대상에서 자동화 된 ML 실험을 실행 하는 데 필요한 추가 단계를 보여 줍니다. 이 자습서의 작업 영역 개체 `ws`는 여기 나오는 코드 전체에서 사용됩니다.

```python
ws = Workspace.from_config()
```

## <a name="create-resource"></a>리소스 만들기

아직 없는 경우 작업 영역에 amlcompute 대상을`ws`만듭니다 ().

**예상 시간**: AmlCompute 대상을 만드는 데 약 5 분이 걸립니다.

```python
from azureml.core.compute import AmlCompute
from azureml.core.compute import ComputeTarget

amlcompute_cluster_name = "automlcl"  # Name your cluster
provisioning_config = AmlCompute.provisioning_configuration(vm_size="STANDARD_D2_V2",
                                                            # for GPU, use "STANDARD_NC6"
                                                            # vm_priority = 'lowpriority', # optional
                                                            max_nodes=6)
compute_target = ComputeTarget.create(
    ws, amlcompute_cluster_name, provisioning_config)

# Can poll for a minimum number of nodes and for a specific timeout.
# If no min_node_count is provided, it will use the scale settings for the cluster.
compute_target.wait_for_completion(
    show_output=True, min_node_count=None, timeout_in_minutes=20)
```

이제 `compute_target` 개체를 원격 컴퓨팅 대상으로 사용할 수 있습니다.

클러스터 이름 제한은 다음과 같습니다.
+ 64자보다 짧아야 합니다.
+ 다음 문자를 포함할 수 없습니다. `\` ~ ! @ # $ % ^ & * ( ) = + _ [ ] { } \\\\ | ; : \' \\" , < > / ?.`

## <a name="access-data-using-tabulardataset-function"></a>TabularDataset 함수를 사용 하 여 데이터 액세스

X 및 y를 s `TabularDataset`로 정의 합니다 .이는 AutoMLConfig에서 자동화 된 ML에 전달 됩니다. `from_delimited_files`기본적으로는 `infer_column_types` 를 true로 설정 합니다. 그러면 열 형식이 자동으로 유추 됩니다. 

수동으로 열 유형을 설정 하려면 `set_column_types` 인수를 설정 하 여 각 열의 유형을 수동으로 설정 합니다. 다음 코드 샘플의 데이터는 sklearn 패키지에서 옵니다.

```python
# Create a project_folder if it doesn't exist
if not os.path.isdir('data'):
    os.mkdir('data')
    
if not os.path.exists(project_folder):
    os.makedirs(project_folder)

from sklearn import datasets
from azureml.core.dataset import Dataset
from scipy import sparse
import numpy as np
import pandas as pd

data_train = datasets.load_digits()

pd.DataFrame(data_train.data[100:,:]).to_csv("data/X_train.csv", index=False)
pd.DataFrame(data_train.target[100:]).to_csv("data/y_train.csv", index=False)

ds = ws.get_default_datastore()
ds.upload(src_dir='./data', target_path='digitsdata', overwrite=True, show_progress=True)

X = Dataset.Tabular.from_delimited_files(path=ds.path('digitsdata/X_train.csv'))
y = Dataset.Tabular.from_delimited_files(path=ds.path('digitsdata/y_train.csv'))

```

## <a name="create-run-configuration"></a>실행 구성 만들기

Get_data py 스크립트에서 종속성을 사용할 수 있도록 하려면 정의 `RunConfiguration` `CondaDependencies`된을 사용 하 여 개체를 정의 합니다. `run_configuration` 의`AutoMLConfig`매개 변수에이 개체를 사용 합니다.

```python
from azureml.core.runconfig import RunConfiguration
from azureml.core.conda_dependencies import CondaDependencies

run_config = RunConfiguration(framework="python")
run_config.target = compute_target
run_config.environment.docker.enabled = True
run_config.environment.docker.base_image = azureml.core.runconfig.DEFAULT_CPU_IMAGE

dependencies = CondaDependencies.create(
    pip_packages=["scikit-learn", "scipy", "numpy"])
run_config.environment.python.conda_dependencies = dependencies
```

이 디자인 패턴의 추가 예제는이 [샘플 노트북](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/remote-amlcompute/auto-ml-remote-amlcompute.ipynb) 을 참조 하세요.

## <a name="configure-experiment"></a>실험 구성
`AutoMLConfig`에 대해 설정을 지정합니다.  ([매개 변수 전체 목록](how-to-configure-auto-train.md#configure-experiment) 및 가능한 값을 참조하세요.)

```python
from azureml.train.automl import AutoMLConfig
import time
import logging

automl_settings = {
    "name": "AutoML_Demo_Experiment_{0}".format(time.time()),
    "iteration_timeout_minutes": 10,
    "iterations": 20,
    "n_cross_validations": 5,
    "primary_metric": 'AUC_weighted',
    "preprocess": False,
    "max_concurrent_iterations": 10,
    "verbosity": logging.INFO
}

automl_config = AutoMLConfig(task='classification',
                             debug_log='automl_errors.log',
                             path=project_folder,
                             compute_target=compute_target,
                             run_configuration=run_config,
                             X = X,
                             y = y,
                             **automl_settings,
                             )
```

### <a name="enable-model-explanations"></a>모델 설명 사용

선택적인 `model_explainability` 매개 변수를 `AutoMLConfig` 생성자에서 설정합니다. 또한 모델 설명 기능을 사용하려면 유효성 검사 데이터 프레임 개체를 매개 변수 `X_valid`로 전달해야 합니다.

```python
automl_config = AutoMLConfig(task='classification',
                             debug_log='automl_errors.log',
                             path=project_folder,
                             compute_target=compute_target,
                             run_configuration=run_config,
                             X = X,
                             y = y,
                             **automl_settings,
                             model_explainability=True,
                             X_valid=X_test
                             )
```

## <a name="submit-training-experiment"></a>학습 실험 제출

이제 알고리즘, 하이퍼 매개 변수를 자동으로 선택하는 구성을 제출하고 모델을 교육합니다.

```python
from azureml.core.experiment import Experiment
experiment = Experiment(ws, 'automl_remote')
remote_run = experiment.submit(automl_config, show_output=True)
```

다음 예제와 비슷한 출력이 표시됩니다.

    Running on remote compute: mydsvmParent Run ID: AutoML_015ffe76-c331-406d-9bfd-0fd42d8ab7f6
    ***********************************************************************************************
    ITERATION: The iteration being evaluated.
    PIPELINE:  A summary description of the pipeline being evaluated.
    DURATION: Time taken for the current iteration.
    METRIC: The result of computing score on the fitted pipeline.
    BEST: The best observed score thus far.
    ***********************************************************************************************

     ITERATION     PIPELINE                               DURATION                METRIC      BEST
             2      Standardize SGD classifier            0:02:36                  0.954     0.954
             7      Normalizer DT                         0:02:22                  0.161     0.954
             0      Scale MaxAbs 1 extra trees            0:02:45                  0.936     0.954
             4      Robust Scaler SGD classifier          0:02:24                  0.867     0.954
             1      Normalizer kNN                        0:02:44                  0.984     0.984
             9      Normalizer extra trees                0:03:15                  0.834     0.984
             5      Robust Scaler DT                      0:02:18                  0.736     0.984
             8      Standardize kNN                       0:02:05                  0.981     0.984
             6      Standardize SVM                       0:02:18                  0.984     0.984
            10      Scale MaxAbs 1 DT                     0:02:18                  0.077     0.984
            11      Standardize SGD classifier            0:02:24                  0.863     0.984
             3      Standardize gradient boosting         0:03:03                  0.971     0.984
            12      Robust Scaler logistic regression     0:02:32                  0.955     0.984
            14      Scale MaxAbs 1 SVM                    0:02:15                  0.989     0.989
            13      Scale MaxAbs 1 gradient boosting      0:02:15                  0.971     0.989
            15      Robust Scaler kNN                     0:02:28                  0.904     0.989
            17      Standardize kNN                       0:02:22                  0.974     0.989
            16      Scale 0/1 gradient boosting           0:02:18                  0.968     0.989
            18      Scale 0/1 extra trees                 0:02:18                  0.828     0.989
            19      Robust Scaler kNN                     0:02:32                  0.983     0.989


## <a name="explore-results"></a>결과 탐색

[학습 자습서](tutorial-auto-train-models.md#explore-the-results) 에 표시 된 것과 동일한 [Jupyter 위젯을](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets?view=azure-ml-py) 사용 하 여 그래프와 결과 테이블을 볼 수 있습니다.

```python
from azureml.widgets import RunDetails
RunDetails(remote_run).show()
```

다음은 위젯의 고정 이미지입니다.  Notebook에서 테이블의 아무 줄을 클릭하여 실행 속성을 살펴보고 해당 실행의 로그를 출력할 수 있습니다.   또한 그래프 위에 있는 드롭다운을 사용하여 각 반복에 제공되는 각 메트릭의 그래프를 볼 수 있습니다.

![위젯 테이블](./media/how-to-auto-train-remote/table.png)
![위젯 플롯](./media/how-to-auto-train-remote/plot.png)

위젯은 개별 실행 세부 정보를 살펴보고 탐색하는 데 사용할 수 있는 URL을 표시합니다.  

Jupyter 노트북에 없는 경우 실행 자체에서 URL을 표시할 수 있습니다.

```
remote_run.get_portal_url()
```

작업 영역에서 동일한 정보를 사용할 수 있습니다.  이러한 결과에 대해 자세히 알아보려면 자동화 된 [machine learning 결과 이해](how-to-understand-automated-ml.md)를 참조 하세요.

### <a name="view-logs"></a>로그 보기

`/tmp/azureml_run/{iterationid}/azureml-logs` 아래에서 DSVM에 대한 로그를 찾습니다.

## <a name="explain"></a>최상의 모델 설명

모델 설명 데이터를 검색하면 모델에 대한 자세한 정보를 확인할 수 있으므로 백 엔드에서 실행되는 대상에 대한 투명성이 높아집니다. 이 예제에서는 최적 맞춤 모델에 대해서만 모델 설명을 실행합니다. 파이프라인의 모든 모델에 대해 실행하는 경우 런타임이 크게 늘어납니다. 모델 설명 정보에는 다음이 포함됩니다.

* shap_values: Shap lib에서 생성 한 설명 정보입니다.
* expected_values: X_train 데이터 세트에 적용되는 모델의 예상 값입니다.
* overall_summary: 모델 수준 기능 중요도 값이 내림차순으로 정렬 됩니다.
* overall: 기능 이름은 overall_summary와 동일한 순서로 정렬 됩니다.
* per_class_summary: 내림차순으로 정렬되는 클래스 수준 기능 중요도 값입니다. 분류 사례에 대해서만 사용할 수 있습니다.
* per_class_imp: per_class_summary와 동일한 순서로 정렬되는 기능 이름입니다. 분류 사례에 대해서만 사용할 수 있습니다.

다음 코드를 사용하여 반복에서 최상의 파이프라인을 선택합니다. `get_output` 메서드는 마지막 맞춤 호출에 대한 최적의 실행 및 맞춤 모델을 반환합니다.

```python
best_run, fitted_model = remote_run.get_output()
```

`retrieve_model_explanation` 함수를 가져오고 최상의 모델에서 실행합니다.

```python
from azureml.train.automl.automlexplainer import retrieve_model_explanation

shap_values, expected_values, overall_summary, overall_imp, per_class_summary, per_class_imp = \
    retrieve_model_explanation(best_run)
```

보려면 `best_run` 설명 변수에 대한 결과를 인쇄합니다.

```python
print(overall_summary)
print(overall_imp)
print(per_class_summary)
print(per_class_imp)
```

`best_run` 설명 요약 변수를 인쇄하면 다음 출력이 표시됩니다.

![모델 설명 콘솔 출력](./media/how-to-auto-train-remote/expl-print.png)

작업 영역 내에서 Azure Portal의 웹 UI 뿐만 아니라 위젯 UI를 통해서도 기능 중요도를 시각화할 수 있습니다.

![모델 설명 UI](./media/how-to-auto-train-remote/model-exp.png)

## <a name="example"></a>예제

[How-to-use-azureml/automated-machine-learning/remote-amlcompute/auto-ml-remote-amlcompute](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/remote-amlcompute/auto-ml-remote-amlcompute.ipynb) 노트북은이 문서의 개념을 보여 줍니다.

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>다음 단계

[자동 학습용 설정을 구성하는 방법](how-to-configure-auto-train.md)을 알아보세요.
