---
title: 학습 scikit-기계 학습 모델 배우기
titleSuffix: Azure Machine Learning
description: Azure Machine Learning에서 scikit 학습 스크립트를 실행 하는 방법에 대해 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: jordane
author: jpe316
ms.date: 07/24/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: d023fa5786375f8fb4dbcdfe01e32da0400088f8
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/06/2020
ms.locfileid: "87849389"
---
# <a name="build-scikit-learn-models-at-scale-with-azure-machine-learning"></a>빌드 scikit-Azure Machine Learning를 사용 하 여 규모에 맞게 모델 학습
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

이 문서에서는 Azure Machine Learning를 사용 하 여 scikit 학습 스크립트를 실행 하는 방법에 대해 알아봅니다.

이 문서의 예제 스크립트는 scikit의 [조리개 데이터 집합](https://archive.ics.uci.edu/ml/datasets/iris)을 기반으로 기계 학습 모델을 빌드하기 위한 조리개 꽃 이미지를 분류 하는 데 사용 됩니다.

처음부터 machine learning scikit 모델을 학습 하 고 있거나 기존 모델을 클라우드로 가져오는 경우에는 Azure Machine Learning를 사용 하 여 탄력적 클라우드 계산 리소스를 사용 하 여 오픈 소스 학습 작업을 확장할 수 있습니다. Azure Machine Learning를 사용 하 여 프로덕션 등급 모델을 빌드, 배포, 버전 및 모니터링할 수 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

이러한 환경 중 하나에서이 코드를 실행 합니다.
 - Azure Machine Learning 컴퓨팅 인스턴스 - 다운로드 또는 설치 필요 없음

    - [자습서: 설치 환경 및 작업 영역](tutorial-1st-experiment-sdk-setup.md) 을 완료 하 여 SDK 및 샘플 리포지토리를 사용 하 여 미리 로드 한 전용 노트북 서버를 만듭니다.
    - 노트북 서버의 샘플 학습 폴더에서 다음 디렉터리로 이동 하 여 완료 되 고 확장 된 노트북을 찾습니다. **사용 방법-azureml > ml-프레임 워크 > scikit-학습 > 학습 > 학습-하이퍼 매개 변수-조정-배포-학습** 폴더.

 - 사용자 고유의 Jupyter Notebook 서버

    - [AZURE MACHINE LEARNING SDK를 설치](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)합니다.
    - [작업 영역 구성 파일을 만듭니다](how-to-configure-environment.md#workspace).

## <a name="set-up-the-experiment"></a>실험 설정

이 섹션에서는 필요한 python 패키지를 로드 하 고, 작업 영역을 초기화 하 고, 실험을 만들고, 학습 데이터 및 학습 스크립트를 업로드 하 여 학습 실험을 설정 합니다.

### <a name="initialize-a-workspace"></a>작업 영역 초기화

[Azure Machine Learning 작업 영역은](concept-workspace.md) 서비스에 대 한 최상위 리소스입니다. 사용자가 만드는 모든 아티팩트를 사용할 수 있는 중앙 집중식 환경을 제공합니다. Python SDK에서 개체를 만들어 작업 영역 아티팩트에 액세스할 수 있습니다 [`workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) .

`config.json` [전제 조건 섹션](#prerequisites)에서 만든 파일에서 작업 영역 개체를 만듭니다.

```Python
from azureml.core import Workspace

ws = Workspace.from_config()
```


### <a name="prepare-scripts"></a>스크립트 준비

이 자습서에서는 학습 스크립트 **train_iris py** 이미 제공 [되어 있습니다](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/scikit-learn/training/train-hyperparameter-tune-deploy-with-sklearn/train_iris.py). 실제로 사용자 지정 학습 스크립트를 그대로 사용 하 고 코드를 수정 하지 않고도 Azure ML을 사용 하 여 실행할 수 있습니다.

참고:
- 제공 된 학습 스크립트는 `Run` 스크립트 내에서 개체를 사용 하 여 AZURE ML 실행에 일부 메트릭을 기록 하는 방법을 보여 줍니다.
- 제공 된 학습 스크립트는 함수의 예제 데이터를 사용 `iris = datasets.load_iris()` 합니다.  사용자 고유의 데이터에 대해 데이터 [집합 및 스크립트 업로드](how-to-train-keras.md#data-upload) 와 같은 단계를 사용 하 여 학습 중에 데이터를 사용할 수 있도록 해야 할 수 있습니다.

### <a name="define-your-environment"></a>환경을 정의 합니다.

#### <a name="create-a-custom-environment"></a>사용자 지정 환경을 만듭니다.

Conda 환경을 작성 합니다 (sklearn-env).
노트북에서 conda 환경을 작성 하려면 ```%%writefile sklearn-env.yml``` 셀의 위쪽에 선을 추가할 수 있습니다.

```yaml
name: sklearn-training-env
dependencies:
  - python=3.6.2
  - scikit-learn
  - numpy
  - pip:
    - azureml-defaults
```

이 Conda 환경 사양에서 Azure ML 환경을 만듭니다. 환경은 런타임에 docker 컨테이너에 패키지 됩니다.
```python
from azureml.core import Environment

myenv = Environment.from_conda_specification(name = "myenv", file_path = "sklearn-env.yml")
```

#### <a name="use-a-curated-environment"></a>큐 레이트 환경 사용
Azure ML은 사용자 고유의 이미지를 빌드하지 않으려는 경우 미리 작성 되 고 큐 레이트 컨테이너 환경을 제공 합니다. 자세한 내용은 [여기](resource-curated-environments.md)를 참조 하세요.
큐 레이트 환경을 사용 하려는 경우 다음 명령을 대신 실행할 수 있습니다.

```python
env = Environment.get(workspace=ws, name="AzureML-Tutorial")
```

### <a name="create-a-scriptrunconfig"></a>ScriptRunConfig 만들기

이 ScriptRunConfig는 로컬 계산 대상에서 실행할 작업을 제출 합니다.

```python
from azureml.core import ScriptRunConfig

sklearnconfig = ScriptRunConfig(source_directory='.', script='train_iris.py')
src.run_config.environment = myenv
```

원격 클러스터에 대해 전송 하려는 경우 run_config을 원하는 계산 대상으로 변경할 수 있습니다.

### <a name="submit-your-run"></a>실행 제출
```python
from azureml.core import Experiment

run = Experiment(ws,'train-sklearn').submit(config=sklearnconfig)
run.wait_for_completion(show_output=True)

```

> [!WARNING]
> Azure Machine Learning는 전체 원본 디렉터리를 복사 하 여 학습 스크립트를 실행 합니다. 업로드 하지 않으려는 중요 한 데이터가 있는 경우 [무시 파일](how-to-save-write-experiment-files.md#storage-limits-of-experiment-snapshots) 을 사용 하거나 원본 디렉터리에이 파일을 포함 하지 마세요. 대신 데이터 [저장소](https://docs.microsoft.com/python/api/azureml-core/azureml.data?view=azure-ml-py)를 사용 하 여 데이터에 액세스 합니다.

Python 환경을 사용자 지정하는 방법에 대한 자세한 내용은 [학습 및 배포 환경 만들기 및 관리](how-to-use-environments.md)를 참조하세요. 

## <a name="what-happens-during-run-execution"></a>실행 실행 중 수행 되는 작업
실행이 실행 되 면 다음 단계를 거칩니다.

- **준비**: TensorFlow 평가기에 따라 docker 이미지가 생성 됩니다. 이미지는 작업 영역 컨테이너 레지스트리로 업로드 되 고 나중에 실행할 수 있도록 캐시 됩니다. 로그는 실행 기록에도 스트리밍되 고 진행률을 모니터링 하기 위해 볼 수 있습니다.

- **크기 조정**: 클러스터는 현재 사용 가능한 것 보다 더 많은 노드를 실행 하는 Batch AI 클러스터가 필요한 경우 확장을 시도 합니다.

- **실행 중**: 스크립트 폴더의 모든 스크립트가 계산 대상으로 업로드 되 고, 데이터 저장소가 탑재 되거나 복사 되 고, entry_script 실행 됩니다. Stdout의 출력과./clogs 폴더는 실행 기록으로 스트리밍되 며 실행을 모니터링 하는 데 사용할 수 있습니다.

- **사후 처리**: 실행의./출력 폴더가 실행 기록에 복사 됩니다.

## <a name="save-and-register-the-model"></a>모델 저장 및 등록

모델을 학습한 후에는 작업 영역에 저장하고 등록할 수 있습니다. 모델 등록을 사용하면 모델을 작업 영역에 저장하고 버전을 지정하여 [모델 관리 및 배포](concept-model-management-and-deployment.md)를 간소화할 수 있습니다.

학습 스크립트 train_iris (py)에 다음 코드를 추가 하 여 모델을 저장 합니다. 

``` Python
import joblib

joblib.dump(svm_model_linear, 'model.joblib')
```

다음 코드를 사용 하 여 작업 영역에 모델을 등록 합니다. 매개 변수, 및를 지정 하면 `model_framework` `model_framework_version` `resource_configuration` 코드 없는 모델 배포를 사용할 수 있게 됩니다. 코드 모델을 배포 하지 않으면 등록 된 모델에서 모델을 웹 서비스로 직접 배포할 수 있으며, [`ResourceConfiguration`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.resource_configuration.resourceconfiguration?view=azure-ml-py) 개체는 웹 서비스에 대 한 계산 리소스를 정의 합니다.

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

방금 등록 한 모델은 학습에 사용한 평가기에 관계 없이 Azure Machine Learning에서 등록 된 다른 모델과 정확히 동일한 방식으로 배포할 수 있습니다. 배포 방법에는 모델 등록에 대 한 섹션이 포함 되어 있지만 등록 된 모델이 이미 있기 때문에 배포에 대 한 [계산 대상을 직접 만드는](how-to-deploy-and-where.md#choose-a-compute-target) 것으로 건너뛸 수 있습니다.

### <a name="preview-no-code-model-deployment"></a>모드 코드 없는 모델 배포

기존 배포 경로 대신 scikit에 대 한 비 코드 배포 기능 (미리 보기)을 사용할 수도 있습니다. 모든 기본 제공 scikit 모델 형식에 대해 코드 모델 배포가 지원 되지 않습니다. 위에 표시 된 대로, 및 매개 변수를 사용 하 여 모델을 등록 하면 `model_framework` `model_framework_version` `resource_configuration` 단순히 정적 함수를 사용 하 여 모델을 배포할 수 있습니다 [`deploy()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model%28class%29?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) .

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
