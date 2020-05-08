---
title: ML 파이프라인에서 데이터 이동
titleSuffix: Azure Machine Learning
description: Azure Machine Learning 파이프라인에서 데이터의 입력 & 출력에 대해 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: laobri
author: lobrien
ms.date: 04/01/2020
ms.custom: contperfq4
ms.openlocfilehash: 233361fb238342cde3c692174e85fb57f69979b1
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/06/2020
ms.locfileid: "82858461"
---
# <a name="moving-data-into-and-between-ml-pipeline-steps-python"></a>ML 파이프라인 단계로/단계 간에 데이터 이동(Python)

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

이 문서에서는 Azure Machine Learning 파이프라인의 단계 간에 데이터를 가져오고, 변환 하 고, 이동 하는 코드를 제공 합니다. Azure Machine Learning에서 데이터가 작동 하는 방식에 대 한 개요는 [Azure storage 서비스에서 데이터 액세스](how-to-access-data.md)를 참조 하세요. Azure Machine Learning 파이프라인의 이점과 구조는 [Azure Machine Learning 파이프라인 이란?](concept-ml-pipelines.md)을 참조 하세요.

이 문서에서 설명하는 방법:

- 기존 `Dataset` 데이터에 대 한 개체 사용
- 사용자의 단계 내에서 데이터에 액세스
- 데이터 `Dataset` 를 하위 집합 (예: 학습 및 유효성 검사 하위 집합)으로 분할
- 다음 `PipelineData` 파이프라인 단계로 데이터를 전송 하는 개체 만들기
- 파이프라인 `PipelineData` 단계에 대 한 입력으로 개체 사용
- 유지 하려는 `Dataset` 새 개체 `PipelineData` 만들기

## <a name="prerequisites"></a>사전 요구 사항

필요한 사항:

- Azure 구독. Azure 구독이 없는 경우 시작하기 전에 체험 계정을 만듭니다. [Azure Machine Learning의 무료 또는 유료 버전](https://aka.ms/AMLFree)을 사용해 보세요.

- [Python 용 AZURE MACHINE LEARNING SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)또는 [Azure Machine Learning studio](https://ml.azure.com/)에 대 한 액세스입니다.

- Azure Machine Learning 작업 영역
  
  [Azure Machine Learning 작업 영역을 만들거나](how-to-manage-workspace.md) Python SDK를 통해 기존 작업 영역을 사용 합니다. `Workspace` 및 `Datastore` 클래스를 가져오고 함수 `config.json` `from_config()`를 사용 하 여 파일에서 구독 정보를 로드 합니다. 이 함수는 기본적으로 현재 디렉터리에서 JSON 파일을 검색 하지만를 사용 하 여 `from_config(path="your/file/path")`파일을 가리키도록 경로 매개 변수를 지정할 수도 있습니다.

   ```python
   import azureml.core
   from azureml.core import Workspace, Datastore
        
   ws = Workspace.from_config()
   ```

- 일부 기존 데이터. 이 문서에서는 [Azure blob 컨테이너](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview)를 사용 하는 방법을 간략하게 설명 합니다.

- 선택 사항: [AZURE MACHINE LEARNING SDK를 사용 하 여 machine learning 파이프라인 만들기 및 실행](how-to-create-your-first-pipeline.md)에 설명 된 것과 같은 기존 machine learning 파이프라인입니다.

## <a name="use-dataset-objects-for-pre-existing-data"></a>기존 `Dataset` 데이터에 대 한 개체 사용 

파이프라인으로 데이터를 수집 하는 기본 방법은 [Dataset](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29?view=azure-ml-py) 개체를 사용 하는 것입니다. `Dataset`개체는 작업 영역 전체에서 사용할 수 있는 영구적 데이터를 나타냅니다.

개체를 만들고 등록 `Dataset` 하는 방법에는 여러 가지가 있습니다. 테이블 형식 데이터 집합은 하나 이상의 파일에서 사용할 수 있는 구분 된 데이터에 대 한 것입니다. 파일 데이터 집합은 이진 데이터 (예: 이미지) 또는 구문 분석 하는 데이터에 대 한 것입니다. 개체를 만드는 `Dataset` 가장 간단한 프로그래밍 방법은 작업 영역 저장소 또는 공용 url에서 기존 blob을 사용 하는 것입니다.

```python
datastore = Datastore.get(workspace, 'training_data')
iris_dataset = Dataset.Tabular.from_delimited_files(DataPath(datastore, 'iris.csv'))

cats_dogs_dataset = Dataset.File.from_files(
    paths='https://download.microsoft.com/download/3/E/1/3E1C3F21-ECDB-4869-8368-6DEBA77B919F/kagglecatsanddogs_3367a.zip',
    archive_options=ArchiveOptions(archive_type=ArchiveType.ZIP, entry_glob='**/*.jpg')
)
```

다른 옵션을 사용 하 여 데이터 집합을 만들고 다른 원본에서 데이터를 등록 하 고 Azure Machine Learning 검토 하는 방법에 대 한 추가 옵션을 사용 하 여 데이터 크기가 계산 용량과 상호 작용 하는 방식을 이해 하 고 버전을 관리 하는 방법은 [Azure Machine Learning 데이터 집합 만들기](how-to-create-register-datasets.md)를 참조 

### <a name="pass-datasets-to-your-script"></a>데이터 집합을 스크립트에 전달 합니다.

데이터 집합의 경로를 스크립트에 전달 하려면 `Dataset` 개체의 `as_named_input()` 메서드를 사용 합니다. 결과 `DatasetConsumptionConfig` 개체를 스크립트에 인수로 전달 하거나 파이프라인 스크립트에 `inputs` 인수를 사용 하 여를 사용 하 `Run.get_context().input_datasets[]`여 데이터 집합을 검색할 수 있습니다.

명명 된 입력을 만든 후에는 `as_mount()` 또는 `as_download()`의 액세스 모드를 선택할 수 있습니다. 스크립트에서 데이터 집합의 모든 파일을 처리 하 고 계산 리소스의 디스크가 데이터 집합에 충분 한 크기 이면 다운로드 액세스 모드를 선택 하는 것이 좋습니다. 다운로드 액세스 모드는 런타임에 데이터를 스트리밍하는 오버 헤드를 방지 합니다. 스크립트가 데이터 집합의 하위 집합에 액세스 하거나 너무 커서 계산에 사용할 수 없는 경우 탑재 액세스 모드를 사용 합니다. 자세한 내용은 [탑재 및 다운로드를 참조 하세요.](https://docs.microsoft.com/azure/machine-learning/how-to-train-with-datasets#mount-vs-download)

파이프라인 단계에 데이터 집합을 전달 하려면 다음을 수행 합니다.

1. 또는 `TabularDataset.as_named_inputs()` `FileDataset.as_named_input()` (' end '의 ' ')를 사용 하 `DatasetConsumptionConfig` 여 개체를 만듭니다.
1. 또는 `as_mount()` `as_download()` 를 사용 하 여 액세스 모드 설정
1. `arguments` 또는 `inputs` 인수를 사용 하 여 데이터 집합을 파이프라인 단계에 전달 합니다.

다음 코드 조각에서는 `PythonScriptStep` 생성자 내에서 이러한 단계를 결합 하는 일반적인 패턴을 보여 줍니다. 

```python

train_step = PythonScriptStep(
    name="train_data",
    script_name="train.py",
    compute_target=cluster,
    inputs=[iris_dataset.as_named_inputs('iris').as_mount()]
)
```

및 `random_split()` `take_sample()` 와 같은 메서드를 사용 하 여 여러 입력을 만들거나 파이프라인 단계로 전달 되는 데이터의 양을 줄일 수도 있습니다.

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

### <a name="access-datasets-within-your-script"></a>스크립트 내에서 데이터 집합에 액세스

파이프라인 단계 스크립트에 대 한 명명 된 입력은 `Run` 개체 내에서 사전으로 사용할 수 있습니다. 를 사용 `Run` `Run.get_context()` 하 여 활성 개체를 검색 한 다음를 사용 하 여 `input_datasets`명명 된 입력의 사전을 검색 합니다. 인수 대신 `arguments` 인수를 `DatasetConsumptionConfig` 사용 하 여 개체를 전달한 경우 코드를 사용 하 `ArgParser` 여 데이터에 액세스 합니다. `inputs` 다음 코드 조각에서는 두 가지 방법을 모두 보여 줍니다.

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

전달 된 값은 데이터 집합 파일의 경로입니다.

직접 등록 `Dataset` 된에 액세스할 수도 있습니다. 등록 된 데이터 집합은 영구적 이며 작업 영역에서 공유 되므로 직접 검색할 수 있습니다.

```python
run = Run.get_context()
ws = run.experiment.workspace
ds = Dataset.get_by_name(workspace=ws, name='mnist_opendataset')
```

## <a name="use-pipelinedata-for-intermediate-data"></a>중간 `PipelineData` 데이터에 사용

`Dataset` 개체가 영구 데이터를 나타내지만 파이프라인 단계에서 출력 되는 임시 데이터에 [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py) 개체가 사용 됩니다. `PipelineData` 개체의 수명은 단일 파이프라인 단계 보다 길기 때문에 파이프라인 정의 스크립트에서 정의 합니다. `PipelineData` 개체를 만들 때 데이터를 저장할 이름 및 데이터 저장소를 제공 해야 합니다. `arguments` 및 `outputs` 인수 `PipelineData` 를 `PythonScriptStep` _모두_ 사용 하 여 개체를에 전달 합니다.

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

즉시 업로드를 제공 하는 `PipelineData` 액세스 모드를 사용 하 여 개체를 만들도록 선택할 수 있습니다. 이 경우를 `PipelineData`만들 때 `upload_mode` 를로 `"upload"` 설정 하 고 `output_path_on_compute` 인수를 사용 하 여 데이터를 쓸 경로를 지정 합니다.

```python
PipelineData("clean_data", datastore=def_blob_store, output_mode="upload", output_path_on_compute="clean_data_output/")
```

### <a name="use-pipelinedata-as-outputs-of-a-training-step"></a>학습 `PipelineData` 단계의 출력으로 사용

파이프라인의 `PythonScriptStep`에서 프로그램의 인수를 사용 하 여 사용 가능한 출력 경로를 검색할 수 있습니다. 이 단계가 첫 번째이 고 출력 데이터를 초기화 하는 경우 지정 된 경로에 디렉터리를 만들어야 합니다. 그런 다음에 포함 하려는 모든 파일을 작성할 수 있습니다 `PipelineData`.

```python
parser = argparse.ArgumentParser()
parser.add_argument('--output_path', dest='output_path', required=True)
args = parser.parse_args()

# Make directory for file
os.makedirs(os.path.dirname(args.output_path), exist_ok=True)
with open(args.output_path, 'w') as f:
    f.write("Step 1's output")
```

인수를 `True`로 설정 `PipelineData` 하 여를 만든 경우 `os.makedirs()` 호출을 수행 하기만 하면 되므로 경로에 원하는 모든 파일을 자유롭게 작성할 수 있습니다. `is_directory` 자세한 내용은 [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py) 참조 설명서를 참조 하세요.

### <a name="read-pipelinedata-as-inputs-to-non-initial-steps"></a>초기 `PipelineData` 단계가 아닌 단계에 대 한 입력으로 읽기

초기 파이프라인 단계에서 일부 데이터를 `PipelineData` 경로에 기록 하 고 해당 초기 단계의 출력이 되 면 이후 단계에 대 한 입력으로 사용할 수 있습니다.

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

`PipelineData` 입력 값은 이전 출력의 경로입니다. 이전에 표시 된 것 처럼, 첫 번째 단계는 단일 파일을 작성 하 고 사용 하는 것 처럼 보일 수 있습니다. 

```python
parser = argparse.ArgumentParser()
parser.add_argument('--pd', dest='pd', required=True)
args = parser.parse_args()

with open(args.pd) as f:
    print(f.read())
```

## <a name="convert-pipelinedata-objects-to-datasets"></a>개체 `PipelineData` 를로 `Dataset`변환

실행 기간 보다 오랫동안 `PipelineData` 사용할 수 있도록 하려면 해당 `as_dataset()` 함수를 사용 하 여로 변환 `Dataset`합니다. 그런 다음를 등록 하 `Dataset`여 작업 영역에서 최고 수준의 시민으로 만들 수 있습니다. `PipelineData` 개체는 파이프라인이 실행 될 때마다 다른 경로 `create_new_version` 를 갖게 되므로 `True` `Dataset` `PipelineData` 개체에서 만든를 등록할 때를로 설정 하는 것이 좋습니다.

```python
step1_output_ds = step1_output_data.as_dataset()
step1_output_ds.register(name="processed_data", create_new_version=True)
```

## <a name="next-steps"></a>다음 단계

* [Azure machine learning 데이터 집합 만들기](how-to-create-register-datasets.md)
* [Azure Machine Learning SDK를 사용 하 여 machine learning 파이프라인 만들기 및 실행](how-to-create-your-first-pipeline.md)
