---
title: 데이터 집합 버전 관리
titleSuffix: Azure Machine Learning service
description: 데이터 집합의 버전을 지정 하 고 machine learning 파이프라인을 사용 하 여 버전 관리를 수행 하는 모범 사례를 알아봅니다
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: sihhu
ms.reviewer: nibaccam
ms.date: 10/25/2019
ms.custom: ''
ms.openlocfilehash: a361800623796cb3bc26a47c4f8f532503836124
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/24/2019
ms.locfileid: "72902003"
---
# <a name="version-and-track-datasets-in-experiments"></a>실험의 버전 및 트랙 데이터 집합

이 방법에서는 재현 가능성에 대 한 Azure Machine Learning 데이터 집합의 버전을 관리 하 고 추적 하는 방법을 알아봅니다. 데이터 집합 버전 관리를 통해 데이터의 상태를 책갈피로 지정할 수 있으므로 이후 실험을 위해 특정 버전의 데이터 집합을 적용할 수 있습니다.

버전 관리를 위해 고려해 야 할 일반적인 시나리오는 다음과 같습니다.

* 새 데이터를 다시 학습에 사용할 수 있는 경우.
* 다른 데이터 준비 또는 기능 엔지니어링 방법을 적용 하는 경우

## <a name="prerequisites"></a>전제 조건

이 방법의 경우 다음이 필요 합니다.

- [Azureml 데이터 집합](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset?view=azure-ml-py) 패키지가 포함 된 [PYTHON 용 Azure Machine Learning SDK가 설치 되어](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)있습니다.
    
- [Azure Machine Learning 작업 영역](concept-workspace.md)입니다. 다음 코드를 사용 하 여 기존 항목을 검색 하거나 [새 작업 영역을 만듭니다](how-to-manage-workspace.md).

    ```Python
    import azureml.core
    from azureml.core import Workspace
    
    ws = Workspace.from_config()
    ```
- [Azure Machine Learning 데이터 집합](how-to-create-register-datasets.md)입니다.

<a name="register"></a>

## <a name="register-and-retrieve-dataset-versions"></a>데이터 집합 버전 등록 및 검색

데이터 집합을 등록 하면 실험 및 동료 간에 버전을 다시 사용 하 고 공유할 수 있습니다. 동일한 이름으로 여러 데이터 집합을 등록 하 고 이름 및 버전 번호로 특정 버전을 검색할 수 있습니다.

### <a name="register-a-dataset-version"></a>데이터 집합 버전 등록

다음 코드는 `create_new_version` 매개 변수를 `True`로 설정 하 여 `titanic_ds`데이터 집합의 새 버전을 등록 합니다. 작업 영역에 등록 된 기존 `titanic_ds` 없는 경우 `titanic_ds` 이름을 사용 하 여 새 데이터 집합을 만들고 해당 버전을 1로 설정 합니다.

```Python
titanic_ds = titanic_ds.register(workspace = workspace,
                                 name = 'titanic_ds',
                                 description = 'titanic training data',
                                 create_new_version = True)
```

### <a name="retrieve-a-dataset-by-name"></a>이름을 기준으로 데이터 집합 검색

기본적으로 `Dataset` 클래스의 [get_by_name ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#get-by-name-workspace--name--version--latest--) 메서드는 작업 영역에 등록 된 데이터 집합의 최신 버전을 반환 합니다. 

다음 코드는 `titanic_ds` 데이터 집합의 버전 1을 가져옵니다.

```Python
from azureml.core import Dataset
# Get a dataset by name and version number
titanic_ds = Dataset.get_by_name(workspace = workspace,
                                 name = 'titanic_ds', 
                                 version = 1)
```

<a name="best-practice"></a>

## <a name="versioning-best-practice"></a>버전 관리 모범 사례

데이터 집합 버전을 만들 때 작업 영역을 사용 하 여 데이터의 추가 복사본을 만들지 **않습니다** . 데이터 집합은 저장소 서비스의 데이터에 대 한 참조 이므로 저장소 서비스에서 관리 하는 하나의 단일 원본만 있습니다. 

>[!IMPORTANT]
> 데이터 집합에서 참조 하는 데이터를 덮어쓰거나 삭제 한 경우 특정 버전의 데이터 집합을 호출 하면 변경 내용을 되돌릴 수 없습니다. 

데이터 집합에서 데이터를 로드 하는 경우 데이터 집합에서 참조 하는 현재 데이터 콘텐츠를 항상 로드 합니다. 각 데이터 집합 버전의 재현 가능성를 확인 하려는 경우 데이터 집합 버전에서 참조 하는 데이터 콘텐츠를 수정 하지 않는 것이 좋습니다. 에서 새 데이터를 가져오는 경우 새 데이터 파일을 별도의 데이터 폴더에 저장 하 고 새 데이터 집합 버전을 만들어 새 데이터 폴더의 데이터를 포함 합니다.

다음 이미지 및 샘플 코드는 데이터 폴더를 구성 하는 권장 방법을 보여 주고 해당 폴더를 참조 하는 데이터 집합 버전을 만듭니다.

![폴더 구조](media/how-to-version-datasets/folder-image.png)

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

각 기계 학습 (ML) 파이프라인 단계의 입력 및 출력으로 데이터 집합을 사용할 수 있습니다. 파이프라인을 다시 실행 하면 각 파이프라인 단계의 출력이 새 데이터 집합 버전으로 등록 됩니다. 

ML 파이프라인은 파이프라인이 다시 실행 될 때마다 각 단계의 출력을 새 폴더로 채우기 때문에 버전이 지정 된 출력 데이터 집합을 재현할 수 있습니다.

```Python
from azureml.core import Dataset
from azureml.pipeline.steps import PythonScriptStep
from azureml.pipeline.core import Pipeline, PipelineData

# get input dataset 
input_ds = Dataset.get_by_name(workspace, 'weather_ds')

# register pipeline output as dataset
output_ds = PipelineData('prepared_weather_ds', datastore=datastore).as_dataset()
output_ds = output_ds.register(name='prepared_weather_ds', create_new_version=True)

# configure pipeline step to use dataset as the input and output
prep_step = PythonScriptStep(script_name="prepare.py",
                             inputs=[input_ds.as_named_input('weather_ds')],
                             outputs=[output_ds],
                             compute_target=compute_target,
                             source_directory=project_folder)
```

<a name="track"></a>

## <a name="track-datasets-in-experiments"></a>실험에서 데이터 집합 추적

각 기계 학습 실험에 대해 등록 된 모델의 `Run` 개체를 통해 입력으로 사용 되는 데이터 집합을 쉽게 추적할 수 있습니다.

데이터 집합을 사용 하 여 모델을 등록 하려면 다음 코드를 사용 합니다.

```Python
model = run.register_model(model_name='keras-mlp-mnist',
                           model_path=model_path,
                           datasets =[('training data',train_dataset)])
```

등록 후 Python 또는 [작업 영역 방문 페이지](https://ml.azure.com/)를 사용 하 여 데이터 집합에 등록 된 모델의 목록을 볼 수 있습니다.

다음 코드는 [`get_details()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#get-details--) 메서드를 사용 하 여 실험 실행에서 사용 된 입력 데이터 집합을 추적 합니다.

```Python
# get input datasets
inputs = run.get_details()['inputDatasets']
train_dataset = inputs[0]['dataset']

# list the files referenced by train_dataset
train_dataset.to_path()
```

[작업 영역 방문 페이지 (미리 보기)](https://ml.azure.com/)를 사용 하 여 실험에서 `input_datasets`를 찾을 수도 있습니다. 

다음 이미지는 작업 영역 방문 페이지에서 실험의 입력 데이터 집합을 찾을 수 있는 위치를 보여 줍니다. 이 예에서는 **실험** 창으로 이동 하 고, `keras-mnist`실험의 특정 실행에 대 한 **속성** 탭을 엽니다. 

![입력 데이터 집합](media/how-to-version-datasets/input-datasets.png)

작업 영역 방문 페이지에서 데이터 집합을 사용 하는 모델을 찾을 수도 있습니다. 다음 뷰는 자산 아래의 데이터 집합 블레이드에서 가져온 것입니다. 데이터 집합을 선택 하 고 모델 탭으로 이동 하 여 해당 데이터 집합을 사용 하는 모델 목록을 찾습니다. 

![입력 데이터 집합 모델](media/how-to-version-datasets/dataset-models.png)

## <a name="next-steps"></a>다음 단계

* [데이터 집합을 사용 하 여 학습](how-to-train-with-datasets.md)합니다.
* [데이터 집합의 더 많은 샘플 노트북](https://aka.ms/dataset-tutorial).
