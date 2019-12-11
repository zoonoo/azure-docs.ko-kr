---
title: 학습 scikit-기계 학습 모델 배우기
titleSuffix: Azure Machine Learning
description: Azure Machine Learning 기능 평가기 클래스를 사용 하 여 엔터프라이즈 규모에서 scikit 학습 스크립트를 실행 하는 방법에 대해 알아봅니다. 예제 스크립트는 조리개 꽃 이미지를 분류 하 여 scikit의 조리개 데이터 집합을 기반으로 기계 학습 모델을 작성 합니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: maxluk
author: maxluk
ms.date: 08/02/2019
ms.custom: seodec18
ms.openlocfilehash: 2b6cdf9350d95de901e8a0f1e875d90513b33f1a
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/10/2019
ms.locfileid: "74976114"
---
# <a name="build-scikit-learn-models-at-scale-with-azure-machine-learning"></a>빌드 scikit-Azure Machine Learning를 사용 하 여 규모에 맞게 모델 학습
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

이 문서에서는 Azure Machine Learning 기능 [평가기](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py) 클래스를 사용 하 여 엔터프라이즈 규모에서 scikit 학습 스크립트를 실행 하는 방법에 대해 알아봅니다. 

이 문서의 예제 스크립트는 scikit의 [조리개 데이터 집합](https://archive.ics.uci.edu/ml/datasets/iris)을 기반으로 기계 학습 모델을 빌드하기 위한 조리개 꽃 이미지를 분류 하는 데 사용 됩니다.

처음부터 machine learning scikit 모델을 학습 하 고 있거나 기존 모델을 클라우드로 가져오는 경우에는 Azure Machine Learning를 사용 하 여 탄력적 클라우드 계산 리소스를 사용 하 여 오픈 소스 학습 작업을 확장할 수 있습니다. Azure Machine Learning를 사용 하 여 프로덕션 등급 모델을 빌드, 배포, 버전 및 모니터링할 수 있습니다.

## <a name="prerequisites"></a>전제 조건

이러한 환경 중 하나에서이 코드를 실행 합니다.
 - Azure Machine Learning 노트북 VM-다운로드 또는 설치 필요 없음

    - [자습서: 설치 환경 및 작업 영역](tutorial-1st-experiment-sdk-setup.md) 을 완료 하 여 SDK 및 샘플 리포지토리를 사용 하 여 미리 로드 한 전용 노트북 서버를 만듭니다.
    - 노트북 서버의 샘플 학습 폴더에서 다음 디렉터리로 이동 하 여 완료 되 고 확장 된 노트북을 찾습니다. **사용 방법-azureml > ml-프레임 워크 > scikit-학습 > 학습 > 학습-하이퍼 매개 변수-조정-배포-학습** 폴더.

 - 사용자 고유의 Jupyter Notebook 서버

    - [AZURE MACHINE LEARNING SDK를 설치](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)합니다.
    - [작업 영역 구성 파일을 만듭니다](how-to-configure-environment.md#workspace).
    - 데이터 집합 및 샘플 스크립트 파일 다운로드 
        - [iri 데이터 집합](https://archive.ics.uci.edu/ml/datasets/iris)
        - [`train_iris.py`](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/scikit-learn/training/train-hyperparameter-tune-deploy-with-sklearn)
    - GitHub 샘플 페이지에서이 가이드의 전체 [Jupyter Notebook 버전](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/scikit-learn/training/train-hyperparameter-tune-deploy-with-sklearn/train-hyperparameter-tune-deploy-with-sklearn.ipynb) 을 찾을 수도 있습니다. 노트북에는 지능형 하이퍼 매개 변수 튜닝을 다루는 확장 된 섹션과 기본 메트릭에의 한 최상의 모델 검색 기능이 포함 되어 있습니다.

## <a name="set-up-the-experiment"></a>실험 설정

이 섹션에서는 필요한 python 패키지를 로드 하 고, 작업 영역을 초기화 하 고, 실험을 만들고, 학습 데이터 및 학습 스크립트를 업로드 하 여 학습 실험을 설정 합니다.

### <a name="import-packages"></a>패키지 가져오기

먼저 필요한 Python 라이브러리를 가져옵니다.

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

[Azure Machine Learning 작업 영역은](concept-workspace.md) 서비스에 대 한 최상위 리소스입니다. 사용자가 만드는 모든 아티팩트를 사용할 수 있는 중앙 집중식 환경을 제공 합니다. Python SDK에서 [`workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) 개체를 만들어 작업 영역 아티팩트에 액세스할 수 있습니다.

[전제 조건 섹션](#prerequisites)에서 만든 `config.json` 파일에서 작업 영역 개체를 만듭니다.

```Python
ws = Workspace.from_config()
```

### <a name="create-a-machine-learning-experiment"></a>기계 학습 실험 만들기

학습 스크립트를 보관할 실험 및 폴더를 만듭니다. 이 예제에서는 "비 기능 학습-iri" 라는 실험을 만듭니다.

```Python
project_folder = './sklearn-iris'
os.makedirs(project_folder, exist_ok=True)

exp = Experiment(workspace=ws, name='sklearn-iris')
```

### <a name="prepare-training-script"></a>학습 스크립트 준비

이 자습서에서는 학습 스크립트 **train_iris py** 이미 제공 되어 있습니다. 실제로 사용자 지정 학습 스크립트를 그대로 사용 하 고 코드를 수정 하지 않고도 Azure ML을 사용 하 여 실행할 수 있습니다.

Azure ML 추적 및 메트릭 기능을 사용 하려면 교육 스크립트 내에 적은 양의 Azure ML 코드를 추가 합니다.  **Py 학습 train_iris** 스크립트는 스크립트 내에서 `Run` 개체를 사용 하 여 Azure ML 실행에 일부 메트릭을 기록 하는 방법을 보여 줍니다.

제공 된 학습 스크립트는 `iris = datasets.load_iris()` 함수의 예제 데이터를 사용 합니다.  사용자 고유의 데이터에 대해 데이터 [집합 및 스크립트 업로드](how-to-train-keras.md#data-upload) 와 같은 단계를 사용 하 여 학습 중에 데이터를 사용할 수 있도록 해야 할 수 있습니다.

**Py 학습 train_iris** 스크립트를 프로젝트 디렉터리에 복사 합니다.

```
import shutil
shutil.copy('./train_iris.py', project_folder)
```

## <a name="create-or-get-a-compute-target"></a>계산 대상 만들기 또는 가져오기

Scikit 작업에 대 한 계산 대상을 만듭니다. Scikit-배우기는 단일 노드, CPU 컴퓨팅만 지원 합니다.

다음 코드는 원격 학습 계산 리소스에 대해 AmlCompute (Azure Machine Learning 관리 계산)를 만듭니다. AmlCompute 생성에는 약 5 분이 걸립니다. 해당 이름을 가진 AmlCompute가 이미 작업 영역에 있는 경우이 코드는 생성 프로세스를 건너뜁니다.

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

계산 대상에 대 한 자세한 내용은 [계산 대상 이란?](concept-compute-target.md) 문서를 참조 하세요.

## <a name="create-a-scikit-learn-estimator"></a>Scikit 만들기-배우기 평가기

[Scikit-배우기 평가기](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn?view=azure-ml-py) 는 계산 대상에서 scikit 학습 작업을 시작 하는 간단한 방법을 제공 합니다. 단일 노드 CPU 교육을 지 원하는 데 사용할 수 있는 [`SKLearn`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py) 클래스를 통해 구현 됩니다.

학습 스크립트에 추가 pip 또는 conda 패키지를 실행 해야 하는 경우 `pip_packages` 및 `conda_packages` 인수를 통해 해당 이름을 전달 하 여 결과 docker 이미지에 패키지를 설치할 수 있습니다.

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

## <a name="submit-a-run"></a>실행 제출

[실행 개체](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py) 는 작업이 실행 되는 동안 그리고 작업이 완료 된 후 실행 기록에 인터페이스를 제공 합니다.

```Python
run = experiment.submit(estimator)
run.wait_for_completion(show_output=True)
```

실행이 실행 되 면 다음 단계를 거칩니다.

- **준비**: TensorFlow 평가기에 따라 docker 이미지가 생성 됩니다. 이미지는 작업 영역 컨테이너 레지스트리로 업로드 되 고 나중에 실행할 수 있도록 캐시 됩니다. 로그는 실행 기록에도 스트리밍되 고 진행률을 모니터링 하기 위해 볼 수 있습니다.

- **크기 조정**: 클러스터는 현재 사용 가능한 것 보다 더 많은 노드를 실행 하는 Batch AI 클러스터가 필요한 경우 확장을 시도 합니다.

- **실행 중**: 스크립트 폴더의 모든 스크립트가 계산 대상으로 업로드 되 고, 데이터 저장소가 탑재 되거나 복사 되 고, entry_script 실행 됩니다. Stdout의 출력과./clogs 폴더는 실행 기록으로 스트리밍되 며 실행을 모니터링 하는 데 사용할 수 있습니다.

- **사후 처리**: 실행의./출력 폴더가 실행 기록에 복사 됩니다.

## <a name="save-and-register-the-model"></a>모델 저장 및 등록

모델을 학습 한 후에는 작업 영역에 저장 하 고 등록할 수 있습니다. 모델 등록을 사용 하면 모델 [관리 및 배포](concept-model-management-and-deployment.md)를 간소화 하기 위해 작업 영역에 모델을 저장 하 고 버전을 지정할 수 있습니다.

학습 스크립트 train_iris (py)에 다음 코드를 추가 하 여 모델을 저장 합니다. 

``` Python
import joblib

joblib.dump(svm_model_linear, 'model.joblib')
```

다음 코드를 사용 하 여 작업 영역에 모델을 등록 합니다. `model_framework`, `model_framework_version`및 `resource_configuration`매개 변수를 지정 하면 코드 없는 모델 배포를 사용할 수 있게 됩니다. 이렇게 하면 등록 된 모델에서 모델을 웹 서비스로 직접 배포할 수 있으며 `ResourceConfiguration` 개체는 웹 서비스의 계산 리소스를 정의 합니다.

```Python
from azureml.core import Model
from azureml.core.resource_configuration import ResourceConfiguration

model = run.register_model(model_name='sklearn-iris', 
                           model_path='model.joblib',
                           model_framework=Model.Framework.SCIKITLEARN,
                           model_framework_version='0.19.1',
                           resource_configuration=ResourceConfiguration(cpu=1, memory_in_gb=0.5))
```

## <a name="deployment"></a>배포

방금 등록 한 모델은 학습에 사용한 평가기에 관계 없이 Azure Machine Learning에서 등록 된 다른 모델과 정확히 동일한 방식으로 배포할 수 있습니다. 배포 방법에는 모델 등록에 대 한 섹션이 포함 되어 있지만 등록 된 모델이 이미 있기 때문에 배포에 대 한 [계산 대상을 직접 만드는](how-to-deploy-and-where.md#choose-a-compute-target) 것으로 건너뛸 수 있습니다.

### <a name="preview-no-code-model-deployment"></a>모드 코드 없는 모델 배포

기존 배포 경로 대신 scikit에 대 한 비 코드 배포 기능 (미리 보기)을 사용할 수도 있습니다. 모든 기본 제공 scikit 모델 형식에 대해 코드 모델 배포가 지원 되지 않습니다. 위에 표시 된 대로 `model_framework`, `model_framework_version`및 `resource_configuration` 매개 변수를 사용 하 여 모델을 등록 하면 단순히 `deploy()` 정적 함수를 사용 하 여 모델을 배포할 수 있습니다.

```python
web_service = Model.deploy(ws, "scikit-learn-service", [model])
```

참고: 이러한 종속성은 미리 작성 된 scikit-학습 유추 컨테이너에 포함 되어 있습니다.

```yaml
    - azureml-defaults
    - inference-schema[numpy-support]
    - scikit-learn
    - numpy
```

Azure Machine Learning의 배포에 [대 한 자세한 내용을](how-to-deploy-and-where.md) 자세히 설명 합니다.


## <a name="next-steps"></a>다음 단계

이 문서에서는 scikit 모델에 대해 학습 하 고 등록 했으며 배포 옵션에 대해 알아보았습니다. Azure Machine Learning에 대해 자세히 알아보려면 다음 문서를 참조 하세요.

* [학습 중에 실행 메트릭 추적](how-to-track-experiments.md)
* [하이퍼 매개 변수 조정](how-to-tune-hyperparameters.md)
* [Azure의 분산 심층 학습 교육에 대 한 참조 아키텍처](/azure/architecture/reference-architectures/ai/training-deep-learning)
