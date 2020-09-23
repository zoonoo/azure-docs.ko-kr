---
title: 자동화된 ML 원격 컴퓨팅 대상
titleSuffix: Azure Machine Learning
description: Azure Machine Learning를 통해 Azure Machine Learning 원격 계산 대상에서 자동화 된 machine learning을 사용 하 여 모델을 빌드하는 방법을 알아봅니다.
services: machine-learning
author: cartacioS
ms.author: sacartac
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.workload: data-services
ms.topic: conceptual
ms.custom: how-to
ms.date: 03/09/2020
ms.openlocfilehash: 57108bed97b59513309213d55a0654f4405d509c
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90893315"
---
# <a name="train-models-with-automated-machine-learning-in-the-cloud"></a>클라우드의 자동화된 기계 학습을 사용하여 모델 학습



Azure Machine Learning에서 관리하는 다양한 종류의 컴퓨팅 리소스에 대해 모델을 교육할 수 있습니다. 계산 대상은 클라우드의 리소스 또는 로컬 컴퓨터 일 수 있습니다.

Azure Machine Learning Compute (AmlCompute)와 같은 추가 계산 대상을 추가 하 여 machine learning 실험을 쉽게 확장 하거나 축소할 수 있습니다. AmlCompute는 단일 또는 다중 노드 컴퓨팅을 손쉽게 만들 수 있는 관리형 컴퓨팅 인프라입니다.

이 문서에서는 AmlCompute에서 자동화 된 ML을 사용 하 여 모델을 작성 하는 방법에 대해 알아봅니다.

## <a name="how-does-remote-differ-from-local"></a>원격과 로컬의 차이

원격 계산 대상을 사용 하는 경우 더 많은 기능을 사용할 수 있습니다.  자세한 내용은 [로컬 및 원격 계산 대상](concept-automated-ml.md#local-remote)을 참조 하세요.

"[자동화 된 기계 학습으로 분류 모델 학습](tutorial-auto-train-models.md)" 자습서에서는 로컬 컴퓨터를 사용 하 여 자동화 된 ML을 사용 하 여 모델을 학습 하는 방법을 배웁니다. 로컬로 교육하는 워크플로는 원격 대상에도 적용됩니다. 원격으로 학습 하려면 AmlCompute와 같은 원격 계산 대상을 먼저 만듭니다. 그런 다음, 원격 리소스를 구성하고 코드를 여기에 제출합니다.

이 문서에서는 원격 AmlCompute 대상에서 자동화 된 ML 실험을 실행 하는 데 필요한 추가 단계를 보여 줍니다. 이 자습서의 작업 영역 개체 `ws`는 여기 나오는 코드 전체에서 사용됩니다.

```python
ws = Workspace.from_config()
```

## <a name="create-resource"></a>리소스 만들기

[`AmlCompute`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute%28class%29?view=azure-ml-py&preserve-view=true)아직 없는 경우 작업 영역 ()에 대상을 만듭니다 `ws` .

**예상 시간**: AmlCompute 대상을 만드는 데 약 5 분이 걸립니다.

```python
from azureml.core.compute import AmlCompute
from azureml.core.compute import ComputeTarget
import os

# choose a name for your cluster
compute_name = os.environ.get("AML_COMPUTE_CLUSTER_NAME", "cpu-cluster")
compute_min_nodes = os.environ.get("AML_COMPUTE_CLUSTER_MIN_NODES", 0)
compute_max_nodes = os.environ.get("AML_COMPUTE_CLUSTER_MAX_NODES", 4)

# This example uses CPU VM. For using GPU VM, set SKU to STANDARD_NC6
vm_size = os.environ.get("AML_COMPUTE_CLUSTER_SKU", "STANDARD_D2_V2")


if compute_name in ws.compute_targets:
    compute_target = ws.compute_targets[compute_name]
    if compute_target and type(compute_target) is AmlCompute:
        print('found compute target. just use it. ' + compute_name)
else:
    print('creating a new compute target...')
    provisioning_config = AmlCompute.provisioning_configuration(vm_size = vm_size,
                                                                min_nodes = compute_min_nodes, 
                                                                max_nodes = compute_max_nodes)

    # create the cluster
    compute_target = ComputeTarget.create(ws, compute_name, provisioning_config)
    
    # can poll for a minimum number of nodes and for a specific timeout. 
    # if no min node count is provided it will use the scale settings for the cluster
    compute_target.wait_for_completion(show_output=True, min_node_count=None, timeout_in_minutes=20)
    
     # For a more detailed view of current AmlCompute status, use get_status()
    print(compute_target.get_status().serialize())
```

이제 `compute_target` 개체를 원격 컴퓨팅 대상으로 사용할 수 있습니다.

클러스터 이름 제한은 다음과 같습니다.
+ 64자보다 짧아야 합니다.
+ 다음 문자를 포함할 수 없습니다. `\` ~ ! @ # $% ^ & * () = + _ [] {} \\ \\ |;: \' \\ ",  < > /?. `

## <a name="access-data-using-tabulardataset-function"></a>TabularDataset 함수를 사용 하 여 데이터 액세스

Training_data로 정의 되 [`TabularDataset`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py&preserve-view=true) 고 레이블은의 자동화 된 ML에 전달 됩니다 [`AutoMLConfig`](https://docs.microsoft.com/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig?view=azure-ml-py&preserve-view=true) . `TabularDataset`기본적으로 메서드는를 `from_delimited_files` true로 설정 하 여 `infer_column_types` 열 형식을 자동으로 유추 합니다. 

수동으로 열 유형을 설정 하려면 인수를 설정 `set_column_types` 하 여 각 열의 유형을 수동으로 설정 합니다. 다음 코드 샘플의 데이터는 sklearn 패키지에서 옵니다.

```python
from sklearn import datasets
from azureml.core.dataset import Dataset
from scipy import sparse
import numpy as np
import pandas as pd
import os

# Create a project_folder if it doesn't exist
if not os.path.isdir('data'):
    os.mkdir('data')
    
if not os.path.exists('project_folder'):
    os.makedirs('project_folder')

X = pd.DataFrame(data_train.data[100:,:])
y = pd.DataFrame(data_train.target[100:])

# merge X and y
label = "digit"
X[label] = y

training_data = X

training_data.to_csv('data/digits.csv')
ds = ws.get_default_datastore()
ds.upload(src_dir='./data', target_path='digitsdata', overwrite=True, show_progress=True)

training_data = Dataset.Tabular.from_delimited_files(path=ds.path('digitsdata/digits.csv'))
```

## <a name="configure-experiment"></a>실험 구성
`AutoMLConfig`에 대해 설정을 지정합니다.  ([매개 변수 전체 목록](how-to-configure-auto-train.md#configure-experiment) 및 가능한 값을 참조하세요.)

```python
from azureml.train.automl import AutoMLConfig
import time
import logging

automl_settings = {
    "name": "AutoML_Demo_Experiment_{0}".format(time.time()),
    "experiment_timeout_minutes" : 20,
    "enable_early_stopping" : True,
    "iteration_timeout_minutes": 10,
    "n_cross_validations": 5,
    "primary_metric": 'AUC_weighted',
    "max_concurrent_iterations": 10,
}

automl_config = AutoMLConfig(task='classification',
                             debug_log='automl_errors.log',
                             path=project_folder,
                             compute_target=compute_target,
                             training_data=training_data,
                             label_column_name=label,
                             **automl_settings,
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

```output
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
```

## <a name="explore-results"></a>결과 탐색

[학습 자습서](tutorial-auto-train-models.md#explore-the-results) 에 표시 된 것과 동일한 [Jupyter 위젯을](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets?view=azure-ml-py&preserve-view=true) 사용 하 여 그래프와 결과 테이블을 볼 수 있습니다.

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

## <a name="example"></a>예제

다음 [노트북](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/regression/auto-ml-regression.ipynb) 은이 문서의 개념을 보여 줍니다.

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>다음 단계

* [자동 학습용 설정을 구성하는 방법](how-to-configure-auto-train.md)을 알아보세요.
* 자동화 된 ML 실험에서 모델 interpretability 기능을 사용 하도록 설정 [하는 방법을](how-to-machine-learning-interpretability-automl.md) 참조 하세요.
