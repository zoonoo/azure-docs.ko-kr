---
title: 기계 학습 파이프라인 YAML
titleSuffix: Azure Machine Learning
description: YAML 파일을 사용하여 기계 학습 파이프라인을 정의하는 방법을 알아봅니다. YAML 파이프라인 정의는 Azure CLI에 대한 기계 학습 확장과 함께 사용됩니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: larryfr
ms.author: sanpil
author: sanpil
ms.date: 11/11/2019
ms.openlocfilehash: 40e6d7f3d9c28708c5adec26ddc3c0463e75adc0
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81529708"
---
# <a name="define-machine-learning-pipelines-in-yaml"></a>YAML에서 기계 학습 파이프라인 정의

[YAML에서](https://yaml.org/)기계 학습 파이프라인을 정의하는 방법에 대해 알아봅니다. Azure CLI에 대 한 기계 학습 확장을 사용 하는 경우 파이프라인 관련 명령의 대부분은 파이프라인을 정의 하는 YAML 파일을 기대 합니다.

다음 표는 YAML에서 파이프라인을 정의할 때 현재 지원되지 않는 사항과 현재 지원되지 않는 내용을 나열합니다.

| 단계 유형 | 지원 여부 |
| ----- | :-----: |
| 파이썬 스크립트 스텝 | 예 |
| 애들라스텝 | 예 |
| Azure 배치 단계 | 예 |
| 데이터 브릭스스스스텝 | 예 |
| 데이터 전송 단계 | 예 |
| 자동 ML 스텝 | 예 |
| HyperDriveStep | 예 |
| 모듈 스텝 | 예 |
| MPIStep | 예 |
| EstimatorStep | 예 |

## <a name="pipeline-definition"></a>파이프라인 정의

파이프라인 정의는 파이프라인 클래스에 해당하는 다음 [키를](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline.pipeline?view=azure-ml-py) 사용합니다.

| YAML 키 | Description |
| ----- | ----- |
| `name` | 파이프라인에 대한 설명입니다. |
| `parameters` | 파이프라인에 대한 매개 변수입니다. |
| `data_reference` | 실행에서 데이터를 사용할 수 있는 방법과 위치를 정의합니다. |
| `default_compute` | 파이프라인의 모든 단계가 실행되는 기본 계산 대상입니다. |
| `steps` | 파이프라인에 사용되는 단계입니다. |

## <a name="parameters"></a>매개 변수

이 `parameters` 섹션에서는 [PipelineParameter](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelineparameter?view=azure-ml-py) 클래스에 해당하는 다음 키를 사용합니다.

| YAML 키 | Description |
| ---- | ---- |
| `type` | 매개 변수의 값 형식입니다. `string`유효한 형식은 `int` `float`" `bool`, `datapath` |
| `default` | 기본값입니다. |

각 매개 변수의 이름이 지정됩니다. 예를 들어 다음 YAML 코드 조각은 `NumIterationsParameter`다음과 같은 `DataPathParameter`세 `NodeCountParameter`가지 매개 변수를 정의합니다.

```yaml
pipeline:
    name: SamplePipelineFromYaml
    parameters:
        NumIterationsParameter:
            type: int
            default: 40
        DataPathParameter:
            type: datapath
            default:
                datastore: workspaceblobstore
                path_on_datastore: sample2.txt
        NodeCountParameter:
            type: int
            default: 4
```

## <a name="data-reference"></a>데이터 참조

이 `data_references` 섹션에서는 [DataReference에](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py)해당하는 다음 키를 사용합니다.

| YAML 키 | Description |
| ----- | ----- |
| `datastore` | 참조할 데이터스토어입니다. |
| `path_on_datastore` | 데이터 참조에 대한 백업 저장소의 상대 경로입니다. |

각 데이터 참조는 키에 포함됩니다. 예를 들어 다음 YAML 코드 조각은 키에 `employee_data`저장된 데이터 참조를 정의합니다.

```yaml
pipeline:
    name: SamplePipelineFromYaml
    parameters:
        PipelineParam1:
            type: int
            default: 3
    data_references:
        employee_data:
            datastore: adftestadla
            path_on_datastore: "adla_sample/sample_input.csv"
```

## <a name="steps"></a>단계

단계는 환경에서 실행할 파일과 함께 계산 환경을 정의합니다. 단계 유형을 정의하려면 키를 `type` 사용합니다.

| 단계 유형 | Description |
| ----- | ----- |
| `AdlaStep` | Azure 데이터 레이크 분석기능을 사용하여 U-SQL 스크립트를 실행합니다. [AdlaStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.adlastep?view=azure-ml-py) 클래스에 해당합니다. |
| `AzureBatchStep` | Azure 일괄 처리를 사용하여 작업을 실행합니다. [AzureBatchStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.azurebatchstep?view=azure-ml-py) 클래스에 해당 합니다. |
| `DatabricsStep` | Databricks 노트북, 파이썬 스크립트 또는 JAR을 추가합니다. [DatabricksStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.databricksstep?view=azure-ml-py) 클래스에 해당합니다. |
| `DataTransferStep` | 저장소 옵션 간에 데이터를 전송합니다. 데이터 전송 [단계](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.datatransferstep?view=azure-ml-py) 클래스에 해당 합니다. |
| `PythonScriptStep` | 파이썬 스크립트를 실행합니다. [파이썬스크립트스텝](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep?view=azure-ml-py) 클래스에 해당합니다. |

### <a name="adla-step"></a>ADLA 단계

| YAML 키 | Description |
| ----- | ----- |
| `script_name` | U-SQL 스크립트의 이름입니다(에 `source_directory`상대). |
| `compute_target` | Azure Data Lake는 이 단계에 사용할 대상을 계산합니다. |
| `parameters` | 파이프라인에 대한 [매개 변수입니다.](#parameters) |
| `inputs` | 입력은 [입력포트바인딩,](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.inputportbinding?view=azure-ml-py) [데이터 참조,](#data-reference) [포트데이터참조,](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.portdatareference?view=azure-ml-py) [파이프라인데이터,](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py) [데이터집합,](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29?view=azure-ml-py) [데이터집합정의,](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_definition.datasetdefinition?view=azure-ml-py)또는 [파이프라인데이터셋일](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedataset?view=azure-ml-py)수 있습니다. |
| `outputs` | 출력은 [파이프라인데이터](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py) 또는 [OutputPortBinding](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.outputportbinding?view=azure-ml-py)입니다. |
| `source_directory` | 스크립트, 어셈블리 등을 포함하는 디렉터리 |
| `priority` | 현재 작업에 사용할 우선 순위 값입니다. |
| `params` | 이름-값 쌍의 사전입니다. |
| `degree_of_parallelism` | 이 작업에 사용할 병렬 처리의 정도입니다. |
| `runtime_version` | 데이터 레이크 분석 엔진의 런타임 버전입니다. |
| `allow_reuse` | 동일한 설정으로 다시 실행할 때 단계가 이전 결과를 다시 사용할지 여부를 결정합니다. |

다음 예제에는 ADLA 단계 정의가 포함되어 있습니다.

```yaml
pipeline:
    name: SamplePipelineFromYaml
    parameters:
        PipelineParam1:
            type: int
            default: 3
    data_references:
        employee_data:
            datastore: adftestadla
            path_on_datastore: "adla_sample/sample_input.csv"
    default_compute: adlacomp
    steps:
        Step1:
            runconfig: "D:\\Yaml\\default_runconfig.yml"
            parameters:
                NUM_ITERATIONS_2:
                    source: PipelineParam1
                NUM_ITERATIONS_1: 7
            type: "AdlaStep"
            name: "MyAdlaStep"
            script_name: "sample_script.usql"
            source_directory: "D:\\scripts\\Adla"
            inputs:
                employee_data:
                    source: employee_data
            outputs:
                OutputData:
                    destination: Output4
                    datastore: adftestadla
                    bind_mode: mount
```

### <a name="azure-batch-step"></a>Azure 일괄 처리 단계

| YAML 키 | Description |
| ----- | ----- |
| `compute_target` | Azure Batch는 이 단계에 사용할 대상을 계산합니다. |
| `inputs` | 입력은 [입력포트바인딩,](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.inputportbinding?view=azure-ml-py) [데이터 참조,](#data-reference) [포트데이터참조,](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.portdatareference?view=azure-ml-py) [파이프라인데이터,](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py) [데이터집합,](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29?view=azure-ml-py) [데이터집합정의,](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_definition.datasetdefinition?view=azure-ml-py)또는 [파이프라인데이터셋일](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedataset?view=azure-ml-py)수 있습니다. |
| `outputs` | 출력은 [파이프라인데이터](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py) 또는 [OutputPortBinding](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.outputportbinding?view=azure-ml-py)입니다. |
| `source_directory` | 모듈 바이너리, 실행, 어셈블리 등을 포함하는 디렉터리 |
| `executable` | 이 작업의 일부로 실행될 명령/실행 의 이름입니다. |
| `create_pool` | 작업을 실행하기 전에 풀을 만들지 여부를 나타내는 부울 플래그입니다. |
| `delete_batch_job_after_finish` | 부울 플래그는 작업이 완료된 후 Batch 계정에서 작업을 삭제할지 여부를 나타냅니다. |
| `delete_batch_pool_after_finish` | 작업이 완료된 후 풀을 삭제할지 여부를 나타내는 부울 플래그입니다. |
| `is_positive_exit_code_failure` | 부울 플래그는 작업이 양수 코드로 종료될 경우 작업이 실패하는지 나타냅니다. |
| `vm_image_urn` | 과 VM은 을 사용합니다. `VirtualMachineConfiguration` `create_pool` `True` |
| `pool_id` | 작업이 실행되는 풀의 ID입니다. |
| `allow_reuse` | 동일한 설정으로 다시 실행할 때 단계가 이전 결과를 다시 사용할지 여부를 결정합니다. |

다음 예제에는 Azure Batch 단계 정의가 포함되어 있습니다.

```yaml
pipeline:
    name: SamplePipelineFromYaml
    parameters:
        PipelineParam1:
            type: int
            default: 3
    data_references:
        input:
            datastore: workspaceblobstore
            path_on_datastore: "input.txt"
    default_compute: testbatch
    steps:
        Step1:
            runconfig: "D:\\Yaml\\default_runconfig.yml"
            parameters:
                NUM_ITERATIONS_2:
                    source: PipelineParam1
                NUM_ITERATIONS_1: 7
            type: "AzureBatchStep"
            name: "MyAzureBatchStep"
            pool_id: "MyPoolName"
            create_pool: true
            executable: "azurebatch.cmd"
            source_directory: "D:\\scripts\\AureBatch"
            allow_reuse: false
            inputs:
                input:
                    source: input
            outputs:
                output:
                    destination: output
                    datastore: workspaceblobstore
```

### <a name="databricks-step"></a>데이터 브릭 스텝

| YAML 키 | Description |
| ----- | ----- |
| `compute_target` | Azure Databricks는 이 단계에 사용할 대상을 계산합니다. |
| `inputs` | 입력은 [입력포트바인딩,](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.inputportbinding?view=azure-ml-py) [데이터 참조,](#data-reference) [포트데이터참조,](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.portdatareference?view=azure-ml-py) [파이프라인데이터,](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py) [데이터집합,](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29?view=azure-ml-py) [데이터집합정의,](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_definition.datasetdefinition?view=azure-ml-py)또는 [파이프라인데이터셋일](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedataset?view=azure-ml-py)수 있습니다. |
| `outputs` | 출력은 [파이프라인데이터](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py) 또는 [OutputPortBinding](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.outputportbinding?view=azure-ml-py)입니다. |
| `run_name` | 이 실행에 대한 Databricks의 이름입니다. |
| `source_directory` | 스크립트 및 기타 파일을 포함하는 디렉터리입니다. |
| `num_workers` | Databricks실행 클러스터에 대한 정적 작업자 수입니다. |
| `runconfig` | 파일의 경로입니다. `.runconfig` 이 파일은 [RunConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfiguration?view=azure-ml-py) 클래스의 YAML 표현입니다. 이 파일의 구조에 대한 자세한 내용은 [runconfigschema.json](https://github.com/microsoft/MLOps/blob/b4bdcf8c369d188e83f40be8b748b49821f71cf2/infra-as-code/runconfigschema.json)을 참조하십시오. |
| `allow_reuse` | 동일한 설정으로 다시 실행할 때 단계가 이전 결과를 다시 사용할지 여부를 결정합니다. |

다음 예제에는 데이터 벽돌 단계가 포함되어 있습니다.

```yaml
pipeline:
    name: SamplePipelineFromYaml
    parameters:
        PipelineParam1:
            type: int
            default: 3
    data_references:
        adls_test_data:
            datastore: adftestadla
            path_on_datastore: "testdata"
        blob_test_data:
            datastore: workspaceblobstore
            path_on_datastore: "dbtest"
    default_compute: mydatabricks
    steps:
        Step1:
            runconfig: "D:\\Yaml\\default_runconfig.yml"
            parameters:
                NUM_ITERATIONS_2:
                    source: PipelineParam1
                NUM_ITERATIONS_1: 7
            type: "DatabricksStep"
            name: "MyDatabrickStep"
            run_name: "DatabricksRun"
            python_script_name: "train-db-local.py"
            source_directory: "D:\\scripts\\Databricks"
            num_workers: 1
            allow_reuse: true
            inputs:
                blob_test_data:
                    source: blob_test_data
            outputs:
                OutputData:
                    destination: Output4
                    datastore: workspaceblobstore
                    bind_mode: mount
```

### <a name="data-transfer-step"></a>데이터 전송 단계

| YAML 키 | Description |
| ----- | ----- |
| `compute_target` | Azure 데이터 팩터리는 이 단계에 사용할 대상을 계산합니다. |
| `source_data_reference` | 데이터 전송 작업의 소스 역할을 하는 입력 연결입니다. 지원되는 값은 [inputPortBinding,](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.inputportbinding?view=azure-ml-py) [데이터 참조,](#data-reference) [포트데이터 참조,](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.portdatareference?view=azure-ml-py) [파이프라인 데이터,](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py) [데이터 집합,](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29?view=azure-ml-py) [데이터 집합 정의](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_definition.datasetdefinition?view=azure-ml-py)또는 [파이프라인데이터 집합입니다.](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedataset?view=azure-ml-py) |
| `destination_data_reference` | 데이터 전송 작업의 대상 역할을 하는 입력 연결입니다. 지원되는 값은 [파이프라인데이터](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py) 및 [OutputPortBinding](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.outputportbinding?view=azure-ml-py)입니다. |
| `allow_reuse` | 동일한 설정으로 다시 실행할 때 단계가 이전 결과를 다시 사용할지 여부를 결정합니다. |

다음 예제에는 데이터 전송 단계가 포함되어 있습니다.

```yaml
pipeline:
    name: SamplePipelineFromYaml
    parameters:
        PipelineParam1:
            type: int
            default: 3
    data_references:
        adls_test_data:
            datastore: adftestadla
            path_on_datastore: "testdata"
        blob_test_data:
            datastore: workspaceblobstore
            path_on_datastore: "testdata"
    default_compute: adftest
    steps:
        Step1:
            runconfig: "D:\\Yaml\\default_runconfig.yml"
            parameters:
                NUM_ITERATIONS_2:
                    source: PipelineParam1
                NUM_ITERATIONS_1: 7
            type: "DataTransferStep"
            name: "MyDataTransferStep"
            adla_compute_name: adftest
            source_data_reference:
                adls_test_data:
                    source: adls_test_data
            destination_data_reference:
                blob_test_data:
                    source: blob_test_data
```

### <a name="python-script-step"></a>파이썬 스크립트 단계

| YAML 키 | Description |
| ----- | ----- |
| `inputs` | 입력은 [입력포트바인딩,](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.inputportbinding?view=azure-ml-py) [데이터 참조,](#data-reference) [포트데이터참조,](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.portdatareference?view=azure-ml-py) [파이프라인데이터,](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py) [데이터집합,](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29?view=azure-ml-py) [데이터집합정의,](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_definition.datasetdefinition?view=azure-ml-py)또는 [파이프라인데이터셋일](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedataset?view=azure-ml-py)수 있습니다. |
| `outputs` | 출력은 [파이프라인데이터](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py) 또는 [OutputPortBinding](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.outputportbinding?view=azure-ml-py)입니다. |
| `script_name` | 파이썬 스크립트의 이름(상대)입니다. `source_directory` |
| `source_directory` | 스크립트, Conda 환경 등을 포함하는 디렉터리 |
| `runconfig` | 파일의 경로입니다. `.runconfig` 이 파일은 [RunConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfiguration?view=azure-ml-py) 클래스의 YAML 표현입니다. 이 파일의 구조에 대한 자세한 내용은 [runconfig.json](https://github.com/microsoft/MLOps/blob/b4bdcf8c369d188e83f40be8b748b49821f71cf2/infra-as-code/runconfigschema.json)을 참조하십시오. |
| `allow_reuse` | 동일한 설정으로 다시 실행할 때 단계가 이전 결과를 다시 사용할지 여부를 결정합니다. |

다음 예제에는 Python 스크립트 단계가 포함되어 있습니다.

```yaml
pipeline:
    name: SamplePipelineFromYaml
    parameters:
        PipelineParam1:
            type: int
            default: 3
    data_references:
        DataReference1:
            datastore: workspaceblobstore
            path_on_datastore: testfolder/sample.txt
    default_compute: cpu-cluster
    steps:
        Step1:
            runconfig: "D:\\Yaml\\default_runconfig.yml"
            parameters:
                NUM_ITERATIONS_2:
                    source: PipelineParam1
                NUM_ITERATIONS_1: 7
            type: "PythonScriptStep"
            name: "MyPythonScriptStep"
            script_name: "train.py"
            allow_reuse: True
            source_directory: "D:\\scripts\\PythonScript"
            inputs:
                InputData:
                    source: DataReference1
            outputs:
                OutputData:
                    destination: Output4
                    datastore: workspaceblobstore
                    bind_mode: mount
```

### <a name="pipeline-with-multiple-steps"></a>여러 단계가 있는 파이프라인 

| YAML 키 | Description |
| ----- | ----- |
| `steps` | 하나 이상의 파이프라인스텝 정의 시퀀스입니다. 한 `destination` 단계의 키가 의 `outputs` 의 키가 `inputs` 됩니다.| 

```yaml
pipeline:
    name: SamplePipelineFromYAML
    description: Sample multistep YAML pipeline
    data_references:
        TitanicDS:
            dataset_name: 'titanic_ds'
            bind_mode: download
    default_compute: cpu-cluster
    steps:
        Dataprep:
            type: "PythonScriptStep"
            name: "DataPrep Step"
            compute: cpu-cluster
            runconfig: ".\\default_runconfig.yml"
            script_name: "prep.py"
            arguments:
            - '--train_path'
            - output:train_path
            - '--test_path'
            - output:test_path
            allow_reuse: True
            inputs:
                titanic_ds:
                    source: TitanicDS
                    bind_mode: download
            outputs:
                train_path:
                    destination: train_csv
                    datastore: workspaceblobstore
                test_path:
                    destination: test_csv
        Training:
            type: "PythonScriptStep"
            name: "Training Step"
            compute: cpu-cluster
            runconfig: ".\\default_runconfig.yml"
            script_name: "train.py"
            arguments:
            - "--train_path"
            - input:train_path
            - "--test_path"
            - input:test_path
            inputs:
                train_path:
                    source: train_csv
                    bind_mode: download
                test_path:
                    source: test_csv
                    bind_mode: download

```

## <a name="schedules"></a>일정

파이프라인의 일정을 정의할 때 시간 간격에 따라 데이터 스토어 트리거되거나 되풀이될 수 있습니다. 다음은 일정을 정의하는 데 사용되는 키입니다.

| YAML 키 | Description |
| ----- | ----- |
| `description` | 일정에 대한 설명입니다. |
| `recurrence` | 일정이 되풀이되는 경우 되풀이 설정을 포함합니다. |
| `pipeline_parameters` | 파이프라인에 필요한 모든 매개 변수입니다. |
| `wait_for_provisioning` | 일정 프로비저닝이 완료될 때까지 기다릴지 여부입니다. |
| `wait_timeout` | 타이밍이 바오기 전에 기다리는 시간(초)입니다. |
| `datastore_name` | 수정/추가된 Blob을 모니터링할 데이터스토어입니다. |
| `polling_interval` | 수정/추가된 Blob에 대한 폴링 사이의 시간(분)입니다. 기본값: 5분. 데이터스토어 일정에 대해서만 지원됩니다. |
| `data_path_parameter_name` | 변경된 Blob 경로로 설정할 데이터 경로 파이프라인 매개 변수의 이름입니다. 데이터스토어 일정에 대해서만 지원됩니다. |
| `continue_on_step_failure` | 단계가 실패할 경우 제출된 PipelineRun에서 다른 단계의 실행을 계속할지 여부입니다. 제공된 경우 파이프라인설정을 `continue_on_step_failure` 재정의합니다.
| `path_on_datastore` | (선택 사항) 수정/추가된 Blob을 모니터링할 데이터스토어의 경로입니다. 경로는 데이터스토어의 컨테이너 아래에 있으므로 일정 모니터의 실제 경로는`path_on_datastore`컨테이너/ . 이 수 없는 경우 데이터스토어 컨테이너가 모니터링됩니다. 의 하위 폴더에서 만든 추가 / `path_on_datastore` 수정은 모니터링되지 않습니다. 데이터스토어 일정에 대해서만 지원됩니다. |

다음 예제에는 데이터스토어 트리거 일정에 대한 정의가 포함되어 있습니다.

```yaml
Schedule: 
      description: "Test create with datastore" 
      recurrence: ~ 
      pipeline_parameters: {} 
      wait_for_provisioning: True 
      wait_timeout: 3600 
      datastore_name: "workspaceblobstore" 
      polling_interval: 5 
      data_path_parameter_name: "input_data" 
      continue_on_step_failure: None 
      path_on_datastore: "file/path" 
```

**되풀이 일정을**정의할 때 다음 `recurrence`키를 사용합니다.

| YAML 키 | Description |
| ----- | ----- |
| `frequency` | 일정이 되풀이되는 빈도입니다. `"Minute"`유효한 값은 `"Hour"` `"Day"`" `"Week"`, `"Month"` |
| `interval` | 일정이 얼마나 자주 발생합니다. 정수 값은 일정이 다시 발생할 때까지 기다릴 시간 단위의 수입니다. |
| `start_time` | 일정의 시작 시간입니다. 값의 문자열 형식은 `YYYY-MM-DDThh:mm:ss`. 시작 시간이 제공되지 않으면 첫 번째 워크로드가 즉시 실행되고 일정에 따라 향후 워크로드가 실행됩니다. 시작 시간이 과거에 있는 경우 첫 번째 워크로드는 다음 계산된 런타임에 실행됩니다. |
| `time_zone` | 시작 시간의 표준 시간대입니다. 표준 시간대가 제공되지 않으면 UTC가 사용됩니다. |
| `hours` | `"Day"` 또는 `frequency` `"Week"`또는 . 만 `time_of_day` `hours` 또는 `minutes` 사용할 수 있습니다. |
| `minutes` | `"Day"` 또는 `frequency` `"Week"`또는 - 파이프라인이 실행되어야 하는 시간의 분으로 쉼표로 구분된 0에서 59까지 하나 이상의 정수를 지정할 수 있습니다. 만 `time_of_day` `hours` 또는 `minutes` 사용할 수 있습니다. |
| `time_of_day` | `"Day"` 또는 `frequency` `"Week"`또는 , 일정을 실행할 시간대를 지정할 수 있습니다. 값의 문자열 형식은 `hh:mm`. 만 `time_of_day` `hours` 또는 `minutes` 사용할 수 있습니다. |
| `week_days` | `"Week"`있는 경우 `frequency` 일정이 실행될 때 쉼표로 구분된 일 수를 하나 이상 지정할 수 있습니다. 유효한 `"Monday"`값은 `"Tuesday"` `"Wednesday"`" `"Thursday"` `"Friday"`, `"Saturday"`" `"Sunday"`, |

다음 예제에는 되풀이 일정에 대한 정의가 포함되어 있습니다.

```yaml
Schedule: 
    description: "Test create with recurrence" 
    recurrence: 
        frequency: Week # Can be "Minute", "Hour", "Day", "Week", or "Month". 
        interval: 1 # how often fires 
        start_time: 2019-06-07T10:50:00 
        time_zone: UTC 
        hours: 
        - 1 
        minutes: 
        - 0 
        time_of_day: null 
        week_days: 
        - Friday 
    pipeline_parameters: 
        'a': 1 
    wait_for_provisioning: True 
    wait_timeout: 3600 
    datastore_name: ~ 
    polling_interval: ~ 
    data_path_parameter_name: ~ 
    continue_on_step_failure: None 
    path_on_datastore: ~ 
```

## <a name="next-steps"></a>다음 단계

[Azure 기계 학습에 CLI 확장을 사용하는](reference-azure-machine-learning-cli.md)방법에 대해 알아봅니다.
