---
title: 데이터 집합 버전 처리
titleSuffix: Azure Machine Learning
description: 데이터 집합을 가장 잘 버전화하는 방법과 기계 학습 파이프라인에서 버전 전환이 작동하는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
ms.reviewer: nibaccam
ms.date: 03/09/2020
ms.custom: ''
ms.openlocfilehash: acbd2e3ba756255cbc69ae8a7b7ad62d7a1c1c5a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79528475"
---
# <a name="version-and-track-datasets-in-experiments"></a>실험에서 데이터 집합 버전 및 추적
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

이 문서에서는 재현성을 위해 Azure 기계 학습 데이터 집합을 버전 및 추적하는 방법을 배웁니다. 데이터 집합 버전 전환은 향후 실험에 특정 버전의 데이터 집합을 적용할 수 있도록 데이터 상태를 북마크하는 방법입니다.

일반적인 버전 전환 시나리오:

* 재교육에 사용할 수 있는 새 데이터
* 다양한 데이터 준비 또는 기능 엔지니어링 접근 방식을 적용하는 경우

## <a name="prerequisites"></a>사전 요구 사항

이 자습서에서는 다음이 필요합니다.

- [파이썬에 대한 Azure 기계 학습 SDK가 설치되었습니다.](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py) 이 SDK에는 [azureml 데이터 집합](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset?view=azure-ml-py) 패키지가 포함됩니다.
    
- [Azure 기계 학습 작업 영역](concept-workspace.md). 다음 코드를 실행하여 기존 코드를 검색하거나 [새 작업 영역을 만듭니다.](how-to-manage-workspace.md)

    ```Python
    import azureml.core
    from azureml.core import Workspace
    
    ws = Workspace.from_config()
    ```
- [Azure 기계 학습 데이터 집합](how-to-create-register-datasets.md).

<a name="register"></a>

## <a name="register-and-retrieve-dataset-versions"></a>데이터 집합 버전 등록 및 검색

데이터 집합을 등록하면 실험 및 동료간에 데이터 집합을 버전화하고 다시 사용하고 공유할 수 있습니다. 동일한 이름으로 여러 데이터 집합을 등록하고 이름과 버전 번호로 특정 버전을 검색할 수 있습니다.

### <a name="register-a-dataset-version"></a>데이터 집합 버전 등록

다음 코드는 매개 변수를 `titanic_ds` 로 설정하여 `create_new_version` 데이터 `True`집합의 새 버전을 등록합니다. 작업 영역에 등록된 `titanic_ds` 기존 데이터 집합이 없는 경우 코드는 이름으로 `titanic_ds` 새 데이터 집합을 만들고 해당 버전을 1로 설정합니다.

```Python
titanic_ds = titanic_ds.register(workspace = workspace,
                                 name = 'titanic_ds',
                                 description = 'titanic training data',
                                 create_new_version = True)
```
새 버전의 데이터 집합을 등록할 수도 있습니다. 

### <a name="retrieve-a-dataset-by-name"></a>이름으로 데이터 집합 검색

기본적으로 `Dataset` 클래스의 [get_by_name()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#get-by-name-workspace--name--version--latest--) 메서드는 작업 영역에 등록된 데이터 집합의 최신 버전을 반환합니다. 

다음 코드는 데이터 집합의 버전 1을 `titanic_ds` 가져옵니다.

```Python
from azureml.core import Dataset
# Get a dataset by name and version number
titanic_ds = Dataset.get_by_name(workspace = workspace,
                                 name = 'titanic_ds', 
                                 version = 1)
```

<a name="best-practice"></a>

## <a name="versioning-best-practice"></a>버전 버전 모범 사례

데이터 집합 버전을 만들 때 작업 영역으로 데이터의 추가 복사본을 만들지 *않습니다.* 데이터 집합은 저장소 서비스의 데이터에 대한 참조이므로 저장소 서비스에서 관리하는 단일 소스가 있습니다.

>[!IMPORTANT]
> 데이터 집합에서 참조하는 데이터가 덮어쓰거나 삭제된 경우 특정 버전의 데이터 집합을 호출해도 변경 사항이 되돌로 *전환되지 않습니다.*

데이터 집합에서 데이터를 로드하면 데이터 집합에서 참조하는 현재 데이터 콘텐츠가 항상 로드됩니다. 각 데이터 집합 버전을 재현 가능한지 확인하려면 데이터 집합 버전에서 참조하는 데이터 콘텐츠를 수정하지 않는 것이 좋습니다. 새 데이터가 들어오면 새 데이터 파일을 별도의 데이터 폴더에 저장한 다음 새 데이터 집합 버전을 만들어 해당 새 폴더의 데이터를 포함합니다.

다음 이미지 및 샘플 코드는 데이터 폴더를 구조화하고 해당 폴더를 참조하는 데이터 집합 버전을 만드는 데 권장되는 방법을 보여 줍니다.

![폴더 구조](./media/how-to-version-track-datasets/folder-image.png)

```Python
from azureml.core import Dataset

# get the default datastore of the workspace
datastore = workspace.get_default_datastore()

# create & register weather_ds version 1 pointing to all files in the folder of week 27
datastore_path1 = [(datastore, 'Weather/week 27')]
dataset1 = Dataset.File.from_files(path=datastore_path1)
dataset1.register(workspace = workspace,
                  name = 'weather_ds',
                  description = 'weather data in week 27',
                  create_new_version = True)

# create & register weather_ds version 2 pointing to all files in the folder of week 27 and 28
datastore_path2 = [(datastore, 'Weather/week 27'), (datastore, 'Weather/week 28')]
dataset2 = Dataset.File.from_files(path = datastore_path2)
dataset2.register(workspace = workspace,
                  name = 'weather_ds',
                  description = 'weather data in week 27, 28',
                  create_new_version = True)

```

<a name="pipeline"></a>

## <a name="version-a-pipeline-output-dataset"></a>파이프라인 출력 데이터 집합 버전

데이터 집합을 각 기계 학습 파이프라인 단계의 입력 및 출력으로 사용할 수 있습니다. 파이프라인을 다시 실행하면 각 파이프라인 단계의 출력이 새 데이터 집합 버전으로 등록됩니다.

기계 학습 파이프라인은 파이프라인이 다시 실행될 때마다 각 단계의 출력을 새 폴더에 채우기 때문에 버전이 있는 출력 데이터 집합을 재현할 수 있습니다. [파이프라인의 데이터 집합에](how-to-create-your-first-pipeline.md#steps)대해 자세히 알아봅니다.

```Python
from azureml.core import Dataset
from azureml.pipeline.steps import PythonScriptStep
from azureml.pipeline.core import Pipeline, PipelineData
from azureml.core. runconfig import CondaDependencies, RunConfiguration

# get input dataset 
input_ds = Dataset.get_by_name(workspace, 'weather_ds')

# register pipeline output as dataset
output_ds = PipelineData('prepared_weather_ds', datastore=datastore).as_dataset()
output_ds = output_ds.register(name='prepared_weather_ds', create_new_version=True)

conda = CondaDependencies.create(
    pip_packages=['azureml-defaults', 'azureml-dataprep[fuse,pandas]'], 
    pin_sdk_version=False)

run_config = RunConfiguration()
run_config.environment.docker.enabled = True
run_config.environment.python.conda_dependencies = conda

# configure pipeline step to use dataset as the input and output
prep_step = PythonScriptStep(script_name="prepare.py",
                             inputs=[input_ds.as_named_input('weather_ds')],
                             outputs=[output_ds],
                             runconfig=run_config,
                             compute_target=compute_target,
                             source_directory=project_folder)
```

<a name="track"></a>

## <a name="track-datasets-in-experiments"></a>실험에서 데이터 집합 추적

각 기계 학습 실험에 대해 실험 `Run` 개체를 통해 입력으로 사용되는 데이터 집합을 쉽게 추적할 수 있습니다.

다음 코드는 [`get_details()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#get-details--) 메서드를 사용하여 실험 실행에 사용된 입력 데이터 집합을 추적합니다.

```Python
# get input datasets
inputs = run.get_details()['inputDatasets']
input_dataset = inputs[0]['dataset']

# list the files referenced by input_dataset
input_dataset.to_path()
```

을 사용하여 `input_datasets` https://ml.azure.com/실험에서 찾을 수도 있습니다. 

다음 이미지는 Azure 기계 학습 스튜디오에서 실험의 입력 데이터 집합을 찾을 수 있는 위치를 보여 주며 있습니다. 이 예제에서는 **실험** 창으로 이동하여 실험의 특정 실행에 대한 **속성** `keras-mnist`탭을 엽니다.

![입력 데이터 집합](./media/how-to-version-track-datasets/input-datasets.png)

다음 코드를 사용하여 모델을 데이터 집합으로 등록합니다.

```Python
model = run.register_model(model_name='keras-mlp-mnist',
                           model_path=model_path,
                           datasets =[('training data',train_dataset)])
```

등록 후 Python을 사용하여 데이터 집합에 등록된 모델 목록을 보거나 https://ml.azure.com/로 이동합니다.

다음 보기는 **자산**아래의 **데이터 집합** 창에서 입니다. 데이터 집합을 선택한 다음 데이터 **집합에** 등록된 모델 목록에 대한 모델 탭을 선택합니다. 

![입력 데이터 세트 모델](./media/how-to-version-track-datasets/dataset-models.png)

## <a name="next-steps"></a>다음 단계

* [데이터 세트로 학습](how-to-train-with-datasets.md)
* [더 많은 샘플 데이터 세트 노트북](https://aka.ms/dataset-tutorial)
