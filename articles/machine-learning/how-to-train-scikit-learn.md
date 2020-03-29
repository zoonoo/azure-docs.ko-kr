---
title: 사이킷 학습 기계 학습 모델 교육
titleSuffix: Azure Machine Learning
description: Azure 기계 학습 SKlearn 추정 클래스를 사용하여 엔터프라이즈 규모에서 scikit 학습 학습 교육 스크립트를 실행하는 방법을 알아봅니다. 예제 스크립트는 홍채 꽃 이미지를 분류하여 scikit-learn의 홍채 데이터 집합을 기반으로 기계 학습 모델을 빌드합니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: maxluk
author: maxluk
ms.date: 03/09/2020
ms.custom: seodec18
ms.openlocfilehash: bdd2cc400c3df75742689258caea8cb87ee8ccc6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78942269"
---
# <a name="build-scikit-learn-models-at-scale-with-azure-machine-learning"></a>Azure 기계 학습을 통해 대규모로 scikit-learn 모델 빌드
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

이 문서에서는 Azure 기계 학습 [SKlearn 추정 클래스를](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py) 사용하여 엔터프라이즈 규모에서 scikit 학습 학습 스크립트를 실행하는 방법을 알아봅니다. 

이 문서의 예제 스크립트는 아이리스 꽃 이미지를 분류하여 scikit-learn의 [홍채 데이터 집합을](https://archive.ics.uci.edu/ml/datasets/iris)기반으로 기계 학습 모델을 작성하는 데 사용됩니다.

처음부터 기계 학습 scikit-learn 모델을 교육하든 기존 모델을 클라우드로 가져오든 Azure Machine Learning을 사용하여 탄력적 클라우드 컴퓨팅 리소스를 사용하여 오픈 소스 교육 작업을 확장할 수 있습니다. Azure 기계 학습을 통해 프로덕션 급 모델을 빌드, 배포, 버전 및 모니터링할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

다음 환경 중 하나에서 이 코드를 실행합니다.
 - Azure 기계 학습 계산 인스턴스 - 다운로드 또는 설치 필요 없음

    - 자습서 [완료: 설정 환경 및 작업 영역을](tutorial-1st-experiment-sdk-setup.md) 설정하여 SDK 및 샘플 리포지토리로 미리 로드된 전용 노트북 서버를 만듭니다.
    - 노트북 서버의 샘플 학습 폴더에서 이 디렉토리로 이동하여 완료되고 확장된 노트북 **> > > >을** 찾습니다.

 - 사용자 고유의 Jupyter Notebook 서버

    - [Azure 기계 학습 SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)를 설치합니다.
    - [작업 영역 구성 파일 만들기](how-to-configure-environment.md#workspace).
    - 데이터 집합 및 샘플 스크립트 파일 다운로드 
        - [아이리스 데이터 세트](https://archive.ics.uci.edu/ml/datasets/iris)
        - [train_iris.py](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/scikit-learn/training/train-hyperparameter-tune-deploy-with-sklearn)
    - GitHub 샘플 페이지에서 이 가이드의 완성된 [Jupyter 노트북 버전을](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/scikit-learn/training/train-hyperparameter-tune-deploy-with-sklearn/train-hyperparameter-tune-deploy-with-sklearn.ipynb) 찾을 수도 있습니다. 노트북에는 지능형 하이퍼파라미터 튜닝을 다루고 기본 메트릭별로 최상의 모델을 검색하는 확장된 섹션이 포함되어 있습니다.

## <a name="set-up-the-experiment"></a>실험 설정

이 섹션에서는 필요한 파이썬 패키지를 로드하고, 작업 영역을 초기화하고, 실험을 만들고, 학습 데이터 및 학습 스크립트를 업로드하여 학습 실험을 설정합니다.

### <a name="import-packages"></a>패키지 가져오기

먼저 필요한 파이썬 라이브러리를 가져옵니다.

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

[Azure 기계 학습 작업 영역은](concept-workspace.md) 서비스의 최상위 리소스입니다. 그것은 당신이 만드는 모든 아티팩트와 함께 작업 할 수있는 중앙 집중식 장소를 제공합니다. Python SDK에서 개체를 만들어 작업 영역 아티팩트에 [`workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) 액세스할 수 있습니다.

[필수 구성 조건 섹션에서](#prerequisites)만든 `config.json` 파일에서 작업 영역 개체를 만듭니다.

```Python
ws = Workspace.from_config()
```

### <a name="create-a-machine-learning-experiment"></a>기계 학습 실험 만들기

학습 스크립트를 보관할 실험 및 폴더를 만듭니다. 이 예제에서는 "sklearn-iris"라는 실험을 만듭니다.

```Python
project_folder = './sklearn-iris'
os.makedirs(project_folder, exist_ok=True)

exp = Experiment(workspace=ws, name='sklearn-iris')
```

### <a name="prepare-training-script"></a>교육 스크립트 준비

이 자습서에서는 **train_iris.py** 교육 스크립트가 이미 제공됩니다. 실제로 코드를 수정하지 않고도 사용자 지정 학습 스크립트를 있는 것처럼 사용하여 Azure ML에서 실행할 수 있어야 합니다.

Azure ML 추적 및 메트릭 기능을 사용하려면 학습 스크립트 에 소량의 Azure ML 코드를 추가합니다.  **train_iris.py** 학습 스크립트는 스크립트 내의 `Run` 개체를 사용하여 Azure ML 실행에 일부 메트릭을 기록하는 방법을 보여 줍니다.

제공된 학습 스크립트는 함수의 `iris = datasets.load_iris()` 예제 데이터를 사용합니다.  사용자 고유의 데이터의 경우 학습 중에 데이터를 사용할 수 있도록 [데이터 집합 및 스크립트 업로드와](how-to-train-keras.md#data-upload) 같은 단계를 사용해야 할 수 있습니다.

**train_iris.py** 교육 스크립트를 프로젝트 디렉터리에 복사합니다.

```
import shutil
shutil.copy('./train_iris.py', project_folder)
```

## <a name="create-or-get-a-compute-target"></a>계산 대상 만들기 또는 받기

scikit-learn 작업에 대한 계산 대상을 만듭니다. Scikit-learn은 단일 노드, CPU 컴퓨팅만 지원합니다.

다음 코드는 원격 학습 계산 리소스에 대한 Azure 기계 학습 관리 형 계산(AmlCompute)을 만듭니다. AmlCompute를 만드는 데 약 5분이 소요됩니다. 해당 이름의 AmlCompute가 작업 영역에 이미 있는 경우 이 코드는 생성 프로세스를 건너뜁니다.

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

계산 대상에 대한 자세한 내용은 [계산 대상](concept-compute-target.md) 문서를 참조하세요.

## <a name="create-a-scikit-learn-estimator"></a>사이킷 학습 추정기 만들기

[scikit 학습 추정기는](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn?view=azure-ml-py) 계산 대상에서 scikit 학습 교육 작업을 시작하는 간단한 방법을 제공합니다. 단일 노드 CPU [`SKLearn`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py) 교육을 지원하는 데 사용할 수 있는 클래스를 통해 구현됩니다.

교육 스크립트에서 추가 pip 또는 conda 패키지를 실행해야 하는 경우 `pip_packages` 해당 `conda_packages` 도커 이미지에 해당 이름을 전달하여 패키지를 설치할 수 있습니다.

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


Python 환경 사용자 지정에 대한 자세한 내용은 [교육 및 배포를 위한 환경 만들기 및 관리를](how-to-use-environments.md)참조하십시오. 

## <a name="submit-a-run"></a>달리기 제출

[Run 개체는](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py) 작업이 실행되는 동안 및 작업이 완료된 후 실행 기록에 대한 인터페이스를 제공합니다.

```Python
run = experiment.submit(estimator)
run.wait_for_completion(show_output=True)
```

실행이 실행되면 다음 단계를 거칩니다.

- **준비**: 텐서플로우 추정기에 따라 도커 이미지가 만들어집니다. 이미지가 작업 영역의 컨테이너 레지스트리에 업로드되고 나중에 실행될 수 있도록 캐시됩니다. 로그는 실행 기록으로 스트리밍되며 진행 상황을 모니터링하기 위해 볼 수 있습니다.

- **크기 조정**: Batch AI 클러스터에 현재 사용 가능한 것보다 실행을 실행하는 데 더 많은 노드가 필요한 경우 클러스터가 확장하려고 시도합니다.

- **실행**중 : 스크립트 폴더의 모든 스크립트가 계산 대상에 업로드되고 데이터 저장소가 탑재되거나 복사되고 entry_script 실행됩니다. stdout 및 ./logs 폴더의 출력은 실행 기록으로 스트리밍되며 실행을 모니터링하는 데 사용할 수 있습니다.

- **사후 처리**: 실행의 ./outputs 폴더가 실행 기록에 복사됩니다.

## <a name="save-and-register-the-model"></a>모델 저장 및 등록

모델을 학습한 후에는 모델을 저장하고 작업 영역에 등록할 수 있습니다. 모델 등록을 사용하면 모델 관리 및 배포를 간소화하기 위해 작업 영역에 모델을 [저장하고](concept-model-management-and-deployment.md)버전이 정번할 수 있습니다.

다음 코드를 학습 스크립트(train_iris.py)에 추가하여 모델을 저장합니다. 

``` Python
import joblib

joblib.dump(svm_model_linear, 'model.joblib')
```

다음 코드로 모델을 작업 영역에 등록합니다. 매개 변수를 `model_framework`지정하면 `model_framework_version`및 `resource_configuration`, 코드 없음 모델 배포를 사용할 수 있게 됩니다. 이렇게 하면 등록된 모델에서 웹 서비스로 모델을 직접 배포할 수 있으며 개체는 [`ResourceConfiguration`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.resource_configuration.resourceconfiguration?view=azure-ml-py) 웹 서비스에 대한 계산 리소스를 정의합니다.

```Python
from azureml.core import Model
from azureml.core.resource_configuration import ResourceConfiguration

model = run.register_model(model_name='sklearn-iris', 
                           model_path='outputs/model.joblib',
                           model_framework=Model.Framework.SCIKITLEARN,
                           model_framework_version='0.19.1',
                           resource_configuration=ResourceConfiguration(cpu=1, memory_in_gb=0.5))
```

## <a name="deployment"></a>배포

방금 등록한 모델은 교육에 사용한 추정기에 관계없이 Azure Machine Learning의 다른 등록된 모델과 동일한 방식으로 배포할 수 있습니다. 배포 방법은 모델 등록에 대한 섹션을 포함하지만 이미 등록된 모델이 있으므로 배포에 대한 [계산 대상을 만드는](how-to-deploy-and-where.md#choose-a-compute-target) 데 직접 건너뛸 수 있습니다.

### <a name="preview-no-code-model-deployment"></a>(미리 보기) 코드 없음 모델 배포

기존 배포 경로 대신 scikit 학습에 코드 없음 배포 기능(미리 보기)을 사용할 수도 있습니다. 모든 기본 제공 scikit-learn 모델 유형에 대해 코드 없음 모델 배포가 지원됩니다. 위에 표시된 대로 `model_framework`의 를 의 `model_framework_version`. `resource_configuration` 및 매개 변수에 [`deploy()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model%28class%29?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) 등록하면 정적 함수를 사용하여 모델을 배포할 수 있습니다.

```python
web_service = Model.deploy(ws, "scikit-learn-service", [model])
```

참고: 이러한 종속성은 미리 빌드된 scikit-learn 추론 컨테이너에 포함됩니다.

```yaml
    - azureml-defaults
    - inference-schema[numpy-support]
    - scikit-learn
    - numpy
```

Azure 기계 학습의 배포에 대한 자세한 [방법을](how-to-deploy-and-where.md) 자세히 설명합니다.


## <a name="next-steps"></a>다음 단계

이 문서에서는 scikit 학습 모델을 학습하고 등록했으며 배포 옵션에 대해 배웠습니다. Azure 기계 학습에 대해 자세히 알아보려면 다음 다른 문서를 참조하세요.

* [학습 중에 실행 메트릭 추적](how-to-track-experiments.md)
* [하이퍼 매개 변수 조정](how-to-tune-hyperparameters.md)
* [Azure의 분산 딥 러닝 교육을 위한 참조 아키텍처](/azure/architecture/reference-architectures/ai/training-deep-learning)
