---
title: ML 파이프라인의 입력 및 출력 데이터
titleSuffix: Azure Machine Learning
description: Azure 기계 학습 파이프라인에서 데이터 준비, 사용 및 생성
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: laobri
author: lobrien
ms.date: 04/01/2020
ms.openlocfilehash: 3dd1a82bf7fad1f201f5c0f52af944ef44a3fdf9
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80879766"
---
# <a name="moving-data-into-and-between-ml-pipeline-steps-python"></a>ML 파이프라인 단계(파이썬)로 데이터를 이동

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

데이터는 기계 학습 파이프라인의 핵심입니다. 이 문서에서는 Azure 기계 학습 파이프라인의 단계 간에 데이터를 가져오고 변환하고 이동하는 코드를 제공합니다. Azure 기계 학습에서 데이터가 작동하는 방식에 대한 개요는 [Azure 저장소 서비스의 액세스 데이터를](how-to-access-data.md)참조하십시오. Azure 기계 학습 파이프라인의 이점 및 구조는 [Azure 기계 학습 파이프라인이란 무엇입니까?](concept-ml-pipelines.md)

이 문서에서 설명하는 방법:

- 기존 `Dataset` 데이터에 개체 사용
- 단계 내의 데이터에 액세스
- 교육 `Dataset` 및 유효성 검사 하위 집합과 같은 하위 집합으로 데이터 분할
- 다음 `PipelineData` 파이프라인 단계로 데이터를 전송하는 개체 만들기
- 개체를 파이프라인 단계에 대한 입력으로 사용 `PipelineData`
- 유지하려는 `Dataset` 새 `PipelineData` 개체 만들기

## <a name="prerequisites"></a>사전 요구 사항

필요한 사항:

- Azure 구독 Azure 구독이 없는 경우 시작하기 전에 체험 계정을 만듭니다. Azure [기계 학습의 무료 또는 유료 버전을](https://aka.ms/AMLFree)사용해 보십시오.

- [파이썬용 Azure 기계 학습 SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)또는 [Azure 기계 학습 스튜디오에](https://ml.azure.com/)대한 액세스.

- Azure Machine Learning 작업 영역
  
  [Azure 기계 학습 작업 영역을 만들거나](how-to-manage-workspace.md) Python SDK를 통해 기존 작업 영역을 사용합니다. 및 `Workspace` `Datastore` 클래스를 가져오고 함수를 `config.json` `from_config()`사용하여 파일에서 구독 정보를 로드합니다. 이 함수는 기본적으로 현재 디렉터리에서 JSON 파일을 찾지만 을 사용하여 `from_config(path="your/file/path")`파일을 가리키는 경로 매개 변수를 지정할 수도 있습니다.

   ```python
   import azureml.core
   from azureml.core import Workspace, Datastore
        
   ws = Workspace.from_config()
   ```

- 일부 기존 데이터. 이 문서에서는 [Azure Blob 컨테이너의](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview)사용을 간략하게 보여 주며 있습니다.

- 선택 사항: Azure 기계 학습 [SDK를 사용하여 기계 학습 파이프라인 만들기 및 실행에](how-to-create-your-first-pipeline.md)설명된 파이프라인과 같은 기존 기계 학습 파이프라인입니다.

## <a name="use-dataset-objects-for-pre-existing-data"></a>기존 `Dataset` 데이터에 개체 사용 

파이프라인에 데이터를 통합하는 기본 방법은 데이터 [집합](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29?view=azure-ml-py) 개체를 사용하는 것입니다. `Dataset`개체는 작업 영역 전체에서 사용할 수 있는 영구 데이터를 나타냅니다.

개체를 만들고 등록하는 `Dataset` 방법에는 여러 가지가 있습니다. 테이블 형식 데이터 집합은 하나 이상의 파일에서 사용할 수 있는 제한된 데이터에 대한 것입니다. 파일 데이터 집합은 이진 데이터(예: 이미지) 또는 구문 분석할 데이터에 대한 것입니다. 개체를 만드는 `Dataset` 가장 간단한 프로그래밍 방식의 방법은 작업 영역 저장소 또는 공용 URL에 기존 Blob을 사용하는 것입니다.

```python
datastore = Datastore.get(workspace, 'training_data')
iris_dataset = Dataset.Tabular.from_delimited_files(DataPath(datastore, 'iris.csv'))

cats_dogs_dataset = Dataset.File.from_files(
    paths='https://download.microsoft.com/download/3/E/1/3E1C3F21-ECDB-4869-8368-6DEBA77B919F/kagglecatsanddogs_3367a.zip',
    archive_options=ArchiveOptions(archive_type=ArchiveType.ZIP, entry_glob='**/*.jpg')
)
```

다양한 옵션과 다른 소스에서 데이터 집합을 만들고, 등록하고 Azure 기계 학습 UI에서 검토하고, 데이터 크기가 계산 용량과 상호 작용하는 방식을 이해하고, 데이터 집합을 버전 처리하는 방법에 대한 자세한 옵션은 [Azure Machine Learning 데이터 집합 만들기를](how-to-create-register-datasets.md)참조하십시오. 

### <a name="pass-datasets-to-your-script"></a>데이터 집합을 스크립트로 전달

데이터 집합의 경로를 스크립트에 전달하려면 개체의 `Dataset` `as_named_input()` 메서드를 사용합니다. 결과 `DatasetConsumptionConfig` 개체를 인수로 스크립트에 전달하거나 `inputs` 인수를 파이프라인 스크립트에 사용하여 을 사용하여 `Run.get_context().input_datasets[]`데이터 집합을 검색할 수 있습니다.

명명된 입력을 만든 후에는 액세스 모드 `as_mount()` 또는 `as_download()`을 선택할 수 있습니다. 스크립트가 데이터 집합의 모든 파일을 처리하고 계산 리소스의 디스크가 데이터 집합에 충분히 큰 경우 다운로드 액세스 모드를 선택하는 것이 좋습니다. 다운로드 액세스 모드는 런타임시 데이터 스트리밍의 오버헤드를 방지합니다. 스크립트가 데이터 집합의 하위 집합에 액세스하거나 계산에 너무 큰 경우 마운트 액세스 모드를 사용합니다. 자세한 내용은 [마운트 대 다운로드를 참조하십시오.](https://docs.microsoft.com/azure/machine-learning/how-to-train-with-datasets#mount-vs-download)

데이터 집합을 파이프라인 단계로 전달하려면 다음단계를 수행하십시오.

1. 오브젝트를 만들려면 사용하거나 `TabularDataset.as_named_inputs()` `FileDataset.as_named_input()` (끝에 's'가 없음). `DatasetConsumptionConfig`
1. 사용 `as_mount()` `as_download()` 또는 액세스 모드 설정
1. `arguments` 또는 인수를 사용하여 파이프라인 단계에 데이터 집합을 전달합니다. `inputs`

다음 코드 조각은 생성자 내에서 이러한 단계를 `PythonScriptStep` 결합하는 일반적인 패턴을 보여 주며 다음과 같은 단계를 보여 주며 다음과 같은 단계를 보여 주며 다음과 같은 단계를 보여 주며 다음과 같은 코드 조각은 다음과 같은 단계를 결합하는 일반적인 패턴을 보여 주며 다음과 같은 코드 조각입니다. 

```python

train_step = PythonScriptStep(
    name="train_data",
    script_name="train.py",
    compute_target=cluster,
    inputs=[iris_dataset.as_named_inputs('iris').as_mount()]
)
```

다음과 같은 `random_split()` `take_sample()` 메서드를 사용하여 여러 입력을 만들거나 파이프라인 단계에 전달되는 데이터의 양을 줄일 수도 있습니다.

```python
seed = 42 # PRNG seed
smaller_dataset = iris_dataset.take_sample(0.1, seed=seed) # 10%
train, test = smaller_dataset.random_split(percentage=0.8, seed=seed)

train_step = PythonScriptStep(
    name="train_data",
    script_name="train.py",
    compute_target=cluster,
    inputs=[train.as_named_inputs('train').as_download(), test.as_named_inputs('test').as_download()]
)
```

### <a name="access-datasets-within-your-script"></a>스크립트 내의 데이터 집합 에 액세스

파이프라인 단계 스크립트에 대한 명명된 입력은 `Run` 개체 내에서 사전으로 사용할 수 있습니다. 를 사용하여 `Run` `Run.get_context()` 활성 개체를 검색한 다음 을 `input_datasets`사용하여 명명된 입력의 사전을 검색합니다. 인수가 `DatasetConsumptionConfig` 아닌 `arguments` 인수를 사용하여 개체를 `inputs` 전달한 경우 `ArgParser` 코드를 사용하여 데이터에 액세스합니다. 두 기술 모두 다음 코드 조각에서 설명됩니다.

```python
# In pipeline definition script:
# Code for demonstration only: It would be very confusing to split datasets between `arguments` and `inputs`
train_step = PythonScriptStep(
    name="train_data",
    script_name="train.py",
    compute_target=cluster,
    arguments=['--training-folder', train.as_named_inputs('train').as_download()]
    inputs=[test.as_named_inputs('test').as_download()]
)

# In pipeline script
parser = argparse.ArgumentParser()
parser.add_argument('--training-folder', type=str, dest='train_folder', help='training data folder mounting point')
args = parser.parse_args()
training_data_folder = args.train_folder

testing_data_folder = Run.get_context().input_datasets['test']
```

전달된 값은 데이터 집합 파일(들)의 경로가 됩니다.

등록된 `Dataset` 직접 액세스도 가능합니다. 등록된 데이터 집합은 작업 영역 간에 영구적으로 공유되므로 다음을 직접 검색할 수 있습니다.

```python
run = Run.get_context()
ws = run.experiment.workspace
ds = Dataset.get_by_name(workspace=ws, name='mnist_opendataset')
```

## <a name="use-pipelinedata-for-intermediate-data"></a>중간 `PipelineData` 데이터에 사용

개체는 영구 데이터를 나타내지만 `Dataset` [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py) 개체는 파이프라인 단계에서 출력되는 임시 데이터에 사용됩니다. `PipelineData` 개체의 수명이 단일 파이프라인 단계보다 길기 때문에 파이프라인 정의 스크립트에서 정의합니다. 개체를 `PipelineData` 만들 때 데이터가 상주할 이름과 데이터 스토어를 제공해야 합니다. `PipelineData` 다음 `arguments` 과 `outputs` 인수를 _both_ `PythonScriptStep` 모두 사용하여 개체를 전달합니다.

```python
default_datastore = workspace.get_default_datastore()
dataprep_output = PipelineData("clean_data", datastore=default_datastore)

dataprep_step = PythonScriptStep(
    name="prep_data",
    script_name="dataprep.py",
    compute_target=cluster,
    arguments=["--output-path", dataprep_output]
    inputs=[Dataset.get_by_name(workspace, 'raw_data')],
    outputs=[dataprep_output]
)
```

즉각적인 업로드를 제공하는 `PipelineData` 액세스 모드를 사용하여 개체를 만들도록 선택할 수 있습니다. 이 `PipelineData`경우 을 만들 때 를 `upload_mode` `"upload"` 설정하고 `output_path_on_compute` 인수를 사용하여 데이터를 작성할 경로를 지정합니다.

```python
PipelineData("clean_data", datastore=def_blob_store, output_mode="upload", output_path_on_compute="clean_data_output/")
```

### <a name="use-pipelinedata-as-outputs-of-a-training-step"></a>교육 `PipelineData` 단계의 출력으로 사용

파이프라인 에서 `PythonScriptStep`프로그램의 인수를 사용하여 사용 가능한 출력 경로를 검색할 수 있습니다. 이 단계가 첫 번째 단계이고 출력 데이터를 초기화하는 경우 지정된 경로에서 디렉터리를 만들어야 합니다. 그런 다음 `PipelineData`에 포함하려는 파일을 작성할 수 있습니다.

```python
parser = argparse.ArgumentParser()
parser.add_argument('--output_path', dest='output_path', required=True)
args = parser.parse_args()

# Make directory for file
os.makedirs(os.path.dirname(args.output_path), exist_ok=True)
with open(args.output_path, 'w') as f:
    f.write("Step 1's output")
```

인수를 `PipelineData` `True`설정하여 만든 경우 호출을 수행하기에 `os.makedirs()` 충분하며 경로에 원하는 파일을 자유롭게 작성할 수 있습니다. `is_directory` 자세한 내용은 [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py) 참조 설명서를 참조하십시오.

### <a name="read-pipelinedata-as-inputs-to-non-initial-steps"></a>초기단계가 아닌 단계에 대한 입력으로 읽기 `PipelineData`

초기 파이프라인 단계가 `PipelineData` 경로에 일부 데이터를 쓰고 해당 초기 단계의 출력이 된 후 이후 단계에 대한 입력으로 사용할 수 있습니다.

```python
step1_output_data = PipelineData("processed_data", datastore=def_blob_store, output_mode="upload")

step1 = PythonScriptStep(
    name="generate_data",
    script_name="step1.py",
    runconfig = aml_run_config,
    arguments = ["--output_path", step1_output_data],
    inputs=[],
    outputs=[step1_output_data]
)

step2 = PythonScriptStep(
    name="read_pipeline_data",
    script_name="step2.py",
    compute_target=compute,
    runconfig = aml_run_config,
    arguments = ["--pd", step1_output_data],
    inputs=[step1_output_data]
)

pipeline = Pipeline(workspace=ws, steps=[step1, step2])
```

`PipelineData` 입력 값은 이전 출력에 대한 경로입니다. 앞서 살펴보았듯이 첫 번째 단계에서 단일 파일을 작성한 경우 다음과 같이 사용하십시오. 

```python
parser = argparse.ArgumentParser()
parser.add_argument('--pd', dest='pd', required=True)
args = parser.parse_args()

with open(args.pd) as f:
    print(f.read())
```

## <a name="convert-pipelinedata-objects-to-datasets"></a>개체를 `PipelineData` `Dataset`s로 변환

실행 기간보다 더 오래 `PipelineData` 사용할 수 있도록 하려면 해당 `as_dataset()` 함수를 사용하여 `Dataset`을 로 변환합니다. 그런 다음 `Dataset`을 등록하여 작업 공간에서 일류 시민으로 만들 수 있습니다. `PipelineData` 파이프라인이 실행될 때마다 개체에 다른 경로가 `create_new_version` 있으므로 `True` `Dataset` `PipelineData` 개체에서 만든 생성을 등록할 때 설정하는 것이 좋습니다.

```python
step1_output_ds = step1_output_data.as_dataset()
step1_output_ds.register(name="processed_data", create_new_version=True)
```

## <a name="next-steps"></a>다음 단계

* [Azure 기계 학습 데이터 집합 만들기](how-to-create-register-datasets.md)
* [Azure 기계 학습 SDK를 사용하여 기계 학습 파이프라인 생성 및 실행](how-to-create-your-first-pipeline.md)
