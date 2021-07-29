---
title: scikit-learn 기계 학습 모델 학습
titleSuffix: Azure Machine Learning
description: Azure Machine Learning을 통해 탄력적 클라우드 컴퓨팅 리소스를 사용하여 scikit-learn 학습 작업을 스케일 아웃하는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: jordane
author: jpe316
ms.date: 09/28/2020
ms.topic: how-to
ms.custom: devx-track-python
ms.openlocfilehash: 16f436c4b81aac60075be8cb3bf34a9706f739ab
ms.sourcegitcommit: 02d443532c4d2e9e449025908a05fb9c84eba039
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/06/2021
ms.locfileid: "108746786"
---
# <a name="train-scikit-learn-models-at-scale-with-azure-machine-learning"></a>Azure Machine Learning을 사용하여 대규모 scikit-learn 모델 학습

이 문서에서는 Azure Machine Learning을 사용하여 scikit-learn 학습 스크립트를 실행하는 방법을 알아봅니다.

이 문서의 예제 스크립트는 scikit-learn의 [붓꽃 데이터 세트](https://archive.ics.uci.edu/ml/datasets/iris)에 따라 기계 학습 모델을 빌드하도록 붓꽃 이미지를 분류하는 데 사용됩니다.

처음부터 기계 학습 scikit-learn 모델을 학습시키는지 또는 기존 모델을 클라우드로 가져오는지에 관계없이 Azure Machine Learning을 사용하여 탄력적 클라우드 컴퓨팅 리소스를 사용해 오픈 소스 학습 작업을 스케일 아웃할 수 있습니다. Azure Machine Learning을 사용하여 프로덕션 등급 모델을 빌드, 배포, 버전 관리 및 모니터링할 수 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

다음 환경 중 하나에서 이 코드를 실행합니다.
 - Azure Machine Learning 컴퓨팅 인스턴스 - 다운로드 또는 설치 필요 없음

    - [빠른 시작: Azure Machine Learning 시작하기](quickstart-create-resources.md)를 완료하여 SDK 및 샘플 리포지토리가 미리 로드된 전용 Notebook 서버를 만듭니다.
    - Notebook 서버의 학습 샘플 디렉터리에서 **how-to-use-azureml > ml-frameworks > scikit-learn > train-hyperparameter-tune-deploy-with-sklearn** 폴더로 이동하여 완성된 확장 Notebook을 찾습니다.

 - 사용자 고유의 Jupyter Notebook 서버

    - [Azure Machine Learning SDK를 설치](/python/api/overview/azure/ml/install)합니다(1.13.0 이상).
    - [작업 영역 구성 파일을 만듭니다](how-to-configure-environment.md#workspace).

## <a name="set-up-the-experiment"></a>실험 설정

이 섹션에서는 필요한 Python 패키지를 로드하고, 작업 영역을 초기화하고, 학습 환경을 정의하고, 학습 스크립트를 준비하여 학습 실험을 설정합니다.

### <a name="initialize-a-workspace"></a>작업 영역 초기화

[Azure Machine Learning 작업 영역](concept-workspace.md)은 서비스의 최상위 리소스입니다. 사용자가 만드는 모든 아티팩트를 사용할 수 있는 중앙 집중식 환경을 제공합니다. Python SDK에서 [`workspace`](/python/api/azureml-core/azureml.core.workspace.workspace) 개체를 만들어 작업 영역 아티팩트에 액세스할 수 있습니다.

[사전 요구 사항 섹션](#prerequisites)에서 만든 `config.json` 파일에서 작업 영역 개체를 만듭니다.

```Python
from azureml.core import Workspace

ws = Workspace.from_config()
```

### <a name="prepare-scripts"></a>스크립트 준비

이 자습서에서 학습 스크립트 **train_iris.py** 는 이미 [여기](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/scikit-learn/train-hyperparameter-tune-deploy-with-sklearn/train_iris.py)에서 자동으로 제공됩니다. 실제로 사용자 지정 학습 스크립트를 있는 그대로 사용하여 코드를 수정하지 않고도 Azure ML에서 실행할 수 있어야 합니다.

메모:
- 제공된 학습 스크립트에서는 스크립트 내 `Run` 개체를 사용하여 일부 메트릭을 Azure ML 실행에 로깅하는 방법을 보여줍니다.
- 제공된 학습 스크립트에서는 `iris = datasets.load_iris()` 함수의 예제 데이터를 사용합니다.  고유한 데이터를 사용하고 액세스하려면 [데이터 세트를 사용하여 학습하는 방법](how-to-train-with-datasets.md)을 참조하여 데이터를 학습 중에 사용 가능하도록 만듭니다.

### <a name="define-your-environment"></a>환경 정의

학습 스크립트의 종속성을 캡슐화하는 Azure ML [환경](concept-environments.md)을 정의하려면 사용자 지정 환경을 정의하거나 Azure ML 큐레이팅된 환경을 사용하면 됩니다.

#### <a name="use-a-curated-environment"></a>큐레이팅된 환경 사용
필요에 따라 고유한 환경을 정의하지 않으려면 Azure ML에서 미리 빌드된 큐레이팅된 환경을 제공합니다. 자세한 내용은 [여기](resource-curated-environments.md)를 참조하세요.
큐레이팅된 환경을 사용하려면 다음 명령을 대신 실행하면 됩니다.

```python
from azureml.core import Environment

sklearn_env = Environment.get(workspace=ws, name='AzureML-Tutorial')
```

#### <a name="create-a-custom-environment"></a>사용자 지정 환경 만들기

고유한 사용자 지정 환경을 만들 수도 있습니다. YAML 파일에 conda 종속성을 정의합니다. 다음 예제에서 파일 이름은 `conda_dependencies.yml`입니다.

```yaml
dependencies:
  - python=3.6.2
  - scikit-learn
  - numpy
  - pip:
    - azureml-defaults
```

이 Conda 환경 사양에서 Azure ML 환경을 만듭니다. 환경은 런타임에 Docker 컨테이너로 패키지화됩니다.
```python
from azureml.core import Environment

sklearn_env = Environment.from_conda_specification(name='sklearn-env', file_path='conda_dependencies.yml')
```

환경을 만들고 사용하는 방법에 대한 자세한 내용은 [Azure Machine Learning에서 소프트웨어 환경 만들기 및 사용](how-to-use-environments.md)을 참조하세요.

## <a name="configure-and-submit-your-training-run"></a>학습 실행 구성 및 제출

### <a name="create-a-scriptrunconfig"></a>ScriptRunConfig 만들기
ScriptRunConfig 개체를 만들어 학습 스크립트, 사용할 환경 및 실행할 컴퓨팅 대상 등 학습 작업의 구성 세부 정보를 지정합니다.
`arguments` 매개 변수에 지정되면 학습 스크립트에 대한 모든 인수가 명령줄을 통해 전달됩니다.

다음 코드는 작업이 로컬 컴퓨터에서 실행될 수 있도록 제출하는 ScriptRunConfig 개체를 구성합니다.

```python
from azureml.core import ScriptRunConfig

src = ScriptRunConfig(source_directory='.',
                      script='train_iris.py',
                      arguments=['--kernel', 'linear', '--penalty', 1.0],
                      environment=sklearn_env)
```

대신 작업을 원격 클러스터에서 실행하려면 원하는 컴퓨팅 대상을 ScriptRunConfig의 `compute_target` 매개 변수로 지정하면 됩니다.

```python
from azureml.core import ScriptRunConfig

compute_target = ws.compute_targets['<my-cluster-name>']
src = ScriptRunConfig(source_directory='.',
                      script='train_iris.py',
                      arguments=['--kernel', 'linear', '--penalty', 1.0],
                      compute_target=compute_target,
                      environment=sklearn_env)
```

### <a name="submit-your-run"></a>실행 제출
```python
from azureml.core import Experiment

run = Experiment(ws,'Tutorial-TrainIRIS').submit(src)
run.wait_for_completion(show_output=True)
```

> [!WARNING]
> Azure Machine Learning는 전체 원본 디렉터리를 복사하여 학습 스크립트를 실행합니다. 업로드하지 않으려는 중요한 데이터가 있는 경우 [.ignore 파일](how-to-save-write-experiment-files.md#storage-limits-of-experiment-snapshots)을 사용하거나 데이터를 원본 디렉터리에 포함하지 마세요. 대신 Azure ML [데이터 세트](how-to-train-with-datasets.md)를 사용하여 데이터에 액세스합니다.

### <a name="what-happens-during-run-execution"></a>Run을 실행하는 중에 수행되는 작업
Run이 실행되면 다음 단계를 거칩니다.

- **준비**: 정의된 환경에 따라 Docker 이미지가 생성됩니다. 이미지는 작업 영역의 컨테이너 레지스트리에 업로드되고 나중에 실행될 수 있도록 캐시됩니다. 또한 로그는 실행 기록으로 스트리밍되며 진행 상황을 모니터링할 수 있도록 표시됩니다. 큐레이팅된 환경이 대신 지정되면 해당 큐레이팅된 환경을 지원하는 캐시된 이미지가 사용됩니다.

- **스케일링**: Batch AI 클러스터에서 Run을 실행하는 데 현재 사용할 수 있는 노드보다 더 많은 노드가 필요한 경우 클러스터에서 스케일 업을 시도합니다.

- **실행**: 스크립트 폴더의 모든 스크립트가 컴퓨팅 대상으로 업로드되고, 데이터 저장소가 탑재되거나 복사되며, `script`가 실행됩니다. stdout 및 **./logs** 폴더의 출력은 실행 기록으로 스트리밍되며 Run을 모니터링하는 데 사용될 수 있습니다.

- **후 처리**: Run의 **./outputs** 폴더가 실행 기록에 복사됩니다.

## <a name="save-and-register-the-model"></a>모델 저장 및 등록

모델을 학습한 후에는 작업 영역에 저장하고 등록할 수 있습니다. 모델 등록을 사용하면 모델을 작업 영역에 저장하고 버전을 지정하여 [모델 관리 및 배포](concept-model-management-and-deployment.md)를 간소화할 수 있습니다.

다음 코드를 학습 스크립트 train_iris.py에 추가하여 모델을 저장합니다. 

``` Python
import joblib

joblib.dump(svm_model_linear, 'model.joblib')
```

다음 코드를 사용하여 모델을 작업 영역에 등록합니다. 매개 변수 `model_framework`, `model_framework_version` 및 `resource_configuration`을 지정하면 코드가 없는 모델 배포를 사용할 수 있게 됩니다. 코드가 없는 모델 배포를 사용하면 모델을 등록된 모델에서 웹 서비스로 직접 배포할 수 있으며 [`ResourceConfiguration`](/python/api/azureml-core/azureml.core.resource_configuration.resourceconfiguration) 개체는 웹 서비스의 컴퓨팅 리소스를 정의합니다.

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

방금 등록한 모델을 Azure ML에 등록된 다른 모델과 정확하게 동일한 방식으로 배포할 수 있습니다. 배포 방법에는 모델 등록에 대한 섹션이 포함되어 있지만 이미 등록된 모델이 있으므로 배포에 대한 [컴퓨팅 대상 만들기](how-to-deploy-and-where.md#choose-a-compute-target)로 직접 건너뛸 수 있습니다.

### <a name="preview-no-code-model-deployment"></a>(미리 보기) 코드가 없는 모델 배포

기존 배포 경로 대신 scikit-learn에 코드가 없는 배포 기능(미리 보기)을 사용할 수도 있습니다. 코드가 없는 모델 배포는 기본 제공되는 모든 scikit-learn 모델 형식에 지원됩니다. `model_framework`, `model_framework_version` 및 `resource_configuration` 매개 변수를 사용하여 위와 같이 모델을 등록하면 간단히 [`deploy()`](/python/api/azureml-core/azureml.core.model%28class%29#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) 정적 함수를 사용하여 모델을 배포할 수 있습니다.

```python
web_service = Model.deploy(ws, "scikit-learn-service", [model])
```

참고: 이러한 종속성은 사전에 빌드된 scikit-learn 유추 컨테이너에 포함되어 있습니다.

```yaml
    - azureml-defaults
    - inference-schema[numpy-support]
    - scikit-learn
    - numpy
```

전체 [사용 방법](how-to-deploy-and-where.md)에서는 Azure Machine Learning에서 배포를 자세히 다룹니다.


## <a name="next-steps"></a>다음 단계

이 문서에서 scikit-learn 모델을 학습시키고 등록했으며 배포 옵션에 대해 알아봤습니다. Azure Machine Learning에 대한 자세한 내용은 다음 문서를 참조하세요.

* [학습 중에 실행 메트릭 추적](how-to-log-view-metrics.md)
* [하이퍼 매개 변수 조정](how-to-tune-hyperparameters.md)
