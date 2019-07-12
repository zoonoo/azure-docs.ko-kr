---
title: 학습 하 고 등록 scikit-모델에 알아봅니다.
titleSuffix: Azure Machine Learning service
description: 이 문서에서는 학습 된 scikit 등록 하는 방법을 보여 줍니다.-알아봅니다 Azure Machine Learning 서비스를 사용 하 여 모델링 합니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: maxluk
author: maxluk
ms.date: 06/30/2019
ms.custom: seodec18
ms.openlocfilehash: c9e983f7981c1155964617694d2cce86aba741b7
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67840012"
---
# <a name="train-and-register-scikit-learn-models-at-scale-with-azure-machine-learning-service"></a>학습 및 Azure Machine Learning 서비스를 사용 하 여 대규모로 scikit-learn 모델 등록

이 문서에서는 학습 Azure Machine Learning 서비스를 사용 하 여 scikit-learn 모델을 등록 하는 방법을 보여 줍니다. 사용 하 여 널리 사용 되 [Iris 데이터 집합](https://archive.ics.uci.edu/ml/datasets/iris) 사용자 지정을 사용 하 여 iris 꽃 이미지 분류 [scikit-알아봅니다](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py) 클래스입니다.

Scikit-학습는 기계 학습에 흔히 사용 되는 오픈 소스 계산 프레임 워크입니다. Azure Machine Learning 서비스를 사용 하 여 탄력적인 클라우드 계산 리소스를 사용 하 여 오픈 소스 학습 작업 규모를 신속 하 게 확장할 수 있습니다. 교육 실행, 버전 모델을 통해 추적할 수 있습니다 모델 등을 배포 합니다.

부터 Scikit-학습 모델을 개발 하 든 기존 모델을 클라우드로 가져오는 Azure Machine Learning 서비스 도움이 될 수 있습니다 프로덕션이 준비 된 모델을 작성 합니다.

## <a name="prerequisites"></a>필수 구성 요소

이러한 환경 중 하나에서이 코드를 실행 합니다.
 - Azure Machine Learning Notebook VM-다운로드 나 설치 필요 없이

    - 완료 합니다 [클라우드 기반 notebook 퀵 스타트](quickstart-run-cloud-notebook.md) SDK 및 샘플 리포지토리를 사용 하 여 미리 로드 전용된 노트북 서버를 만들려면.
    - Notebook 서버에 샘플 폴더에서이 디렉터리로 이동 하 여 완료 되 고 확장 된 notebook을 찾습니다: **방법으로-사용-azureml > 교육 > train-hyperparameter-tune-deploy-with-sklearn** 폴더입니다.

 - 사용자 고유의 Jupyter Notebook 서버

    - [Azure Python SDK 학습 컴퓨터 설치](setup-create-workspace.md#sdk)
    - [작업 영역 구성 파일 만들기](setup-create-workspace.md#write-a-configuration-file)
    - [예제 스크립트 파일을 다운로드 합니다.](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training/train-hyperparameter-tune-deploy-with-sklearn) `train_iris.py`
    - 완료 된 찾을 수도 있습니다 [Jupyter Notebook 버전](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-hyperparameter-tune-deploy-with-keras/train-hyperparameter-tune-deploy-with-sklearn.ipynb) GitHub 샘플 페이지에이 가이드의 합니다. Notebook 지능형 하이퍼 매개 변수 튜닝 하 고 기본 메트릭 하 여 최상의 모델 검색을 포함 하는 확장된 된 섹션을 포함 합니다.

## <a name="set-up-the-experiment"></a>실험을 설정

이 섹션에서는 필요한 python 패키지를 로드, 초기화 작업 영역, 실험, 만들기 및 학습 데이터와 학습 스크립트를 업로드 하 여 학습 실험을 설정 합니다.

### <a name="import-packages"></a>패키지 가져오기

먼저, 필요한 Python 라이브러리를 가져와야 합니다.

```Python
import os
import urllib
import shutil
import azureml

from azureml.core import Experiment
from azureml.core import Workspace, Run

from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException
```

### <a name="initialize-a-workspace"></a>작업 영역 초기화

[Azure Machine Learning 서비스 작업 영역](concept-workspace.md) 서비스에 대 한 최상위 리소스입니다. 만든 모든 아티팩트를 작업할 수 있는 중앙된 위치를 사용 하 여 제공 합니다. Python SDK에서 작업 영역 아티팩트를 만들어 액세스할 수 있습니다는 [ `workspace` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) 개체입니다.

작업 영역 개체를 만들어야 합니다 `config.json` 에서 만든 파일을 [전제 조건 섹션](#prerequisites)합니다.

```Python
ws = Workspace.from_config()
```

### <a name="create-an-experiment"></a>실험 만들기

실험 및 학습 스크립트를 보관할 폴더를 만듭니다. 이 예제에서는 "sklearn 아이리스"를 호출 하는 실험을 만듭니다.

```Python
project_folder = './sklearn-iris'
os.makedirs(project_folder, exist_ok=True)

exp = Experiment(workspace=ws, name='sklearn-iris')
```

### <a name="upload-dataset-and-scripts"></a>데이터 집합 및 스크립트 업로드

합니다 [데이터 저장소](how-to-access-data.md) 데이터 저장 고 탑재 하거나 계산 대상에 데이터를 복사 하 여 액세스할 수 있는 위치입니다. 각 작업 영역의 기본 데이터 저장소를 제공합니다. 학습 하는 동안 쉽게 액세스할 수 있도록 데이터 저장소에 데이터 및 학습 스크립트를 업로드 합니다.

1. 데이터에 대 한 디렉터리를 만듭니다.

    ```Python
    os.makedirs('./data/iris', exist_ok=True)
    ```

1. Iris 데이터 집합은 기본 데이터 저장소에 업로드 합니다.

    ```Python
    ds = ws.get_default_datastore()
    ds.upload(src_dir='./data/iris', target_path='iris', overwrite=True, show_progress=True)
    ```

1. Scikit-학습 업로드 학습 스크립트 `train_iris.py`합니다.

    ```Python
    shutil.copy('./train_iris.py', project_folder)
    ```

## <a name="create-or-get-a-compute-target"></a>만들기 또는 가져오기 계산 대상

Scikit-학습 작업을 실행에 대 한 계산 대상을 만듭니다. Scikit 알아봅니다 지원 단일 노드만 CPU 계산 합니다.

다음 코드에서는 원격 학습 계산 리소스에 대 한 관리 되는 Azure Machine Learning compute (AmlCompute)를 만듭니다. 생성의 AmlCompute 약 5 분이 걸립니다. 해당 이름의 AmlCompute 작업 영역에 이미 있으면이 코드는 생성 프로세스를 건너뜁니다.

```Python
cluster_name = "cpu-cluster"

try:
    compute_target = ComputeTarget(workspace=ws, name=cluster_name)
    print('Found existing compute target')
except ComputeTargetException:
    print('Creating a new compute target...')
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2', 
                                                           max_nodes=4)

    compute_target = ComputeTarget.create(ws, cluster_name, compute_config)

    compute_target.wait_for_completion(show_output=True, min_node_count=None, timeout_in_minutes=20)
```

계산 대상에 대 한 자세한 내용은 참조는 [계산 대상을 란](concept-compute-target.md) 문서.

## <a name="create-a-scikit-learn-estimator"></a>Scikit-learn 추정을 만들려면

합니다 [scikit-learn 추정](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py) Scikit-학습을 시작 하는 간단한 방법을 제공 합니다. 계산 대상의 학습 작업 합니다. 통해 구현 되는 [ `SKLearn` ](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py) 단일 노드 CPU 교육을 지 원하는 데 사용할 수 있는 클래스입니다.

통해 해당 이름을 전달 하 여 결과 docker 이미지에 설치 된 패키지 있습니다 학습 스크립트 추가 pip 또는 conda 패키지 실행에 필요한 경우는 `pip_packages` 고 `conda_packages` 인수입니다.

```Python
from azureml.train.sklearn import SKLearn

script_params = {
    '--kernel': 'linear',
    '--penalty': 1.0,
}

estimator = SKLearn(source_directory=project_folder, 
                    script_params=script_params,
                    compute_target=compute_target,
                    entry_script='train_iris.py'
                    pip_packages=['joblib']
                   )
```

## <a name="submit-a-run"></a>실행을 제출 합니다.

합니다 [개체 실행](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py) 작업이 실행 되 고 완료 된 후 실행된 기록에 인터페이스를 제공 합니다.

```Python
run = experiment.submit(estimator)
run.wait_for_completion(show_output=True)
```

실행을 실행 하는 다음 단계를 통해 이동 합니다.

- **준비**: Docker 이미지를 TensorFlow 스 티 메이 터에 따라 생성 됩니다. 이미지 작업 공간의 container registry에 업로드 이며 이후 실행에 대 한 캐시 됩니다. 로그는 실행된 기록에도 스트리밍되고 진행 상황을 모니터링 볼 수 있습니다.

- **크기 조정**: 클러스터는 Batch AI 클러스터 노드를 현재 사용할 수 있는 것 보다는 실행을 실행 해야 하는 경우 확장 하려고 합니다.

- **Running**: 스크립트 폴더에 있는 모든 스크립트가 계산 대상에 업로드 됩니다, 데이터 저장소는 탑재 하거나 복사할 수 고를 entry_script 실행 됩니다. Stdout에서 출력 및. / logs 폴더 실행된 기록에 스트리밍됩니다 및 실행을 모니터링 할 수 있습니다.

- **후 처리 중**: 합니다. / 출력 폴더 실행의 실행된 기록에 복사 됩니다.

## <a name="save-and-register-the-model"></a>저장 하 고 모델 등록

모델을 학습 한 후에 저장 하 고 작업 영역에 등록 수 있습니다. 모델 등록 하면 저장소와 버전 간소화 하기 위해 작업 영역에서 모델 [모델 관리 및 배포](concept-model-management-and-deployment.md)합니다.

모델을 저장할 train_iris.py 학습 스크립트에 다음 코드를 추가 합니다. 

``` Python
import joblib

joblib.dump(svm_model_linear, 'model.joblib')
```

다음 코드를 사용 하 여 모델 작업 영역을 등록 합니다.

```Python
model = run.register_model(model_name='sklearn-iris', model_path='model.joblib')
```

## <a name="next-steps"></a>다음 단계

이 문서에서는 학습 하 고 Azure Machine Learning 서비스에서 scikit-learn 모델을 등록 합니다.

* 모델을 배포 하는 방법에 알아보려면에 계속 우리의 [배포 모델](how-to-deploy-and-where.md) 문서.

* [하이퍼 매개 변수 조정](how-to-tune-hyperparameters.md)

* [학습 중에 실행 메트릭 추적](how-to-track-experiments.md)