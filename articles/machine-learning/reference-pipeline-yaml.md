---
title: Machine Learning 파이프라인 YAML
titleSuffix: Azure Machine Learning
description: YAML 파일을 사용 하 여 기계 학습 파이프라인을 정의 하는 방법을 알아봅니다. YAML 파이프라인 정의는 Azure CLI에 대 한 machine learning 확장과 함께 사용 됩니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: larryfr
ms.author: sanpil
author: sanpil
ms.date: 11/11/2019
ms.openlocfilehash: 474a184b24ca3318a33adb89b25640939a814474
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/09/2020
ms.locfileid: "75771635"
---
# <a name="define-machine-learning-pipelines-in-yaml"></a>YAML에서 기계 학습 파이프라인 정의

[Yaml](https://yaml.org/)에서 기계 학습 파이프라인을 정의 하는 방법에 대해 알아봅니다. Azure CLI에 대해 machine learning 확장을 사용 하는 경우 많은 파이프라인 관련 명령에 파이프라인을 정의 하는 YAML 파일이 있습니다.

다음 표에서는 YAML에서 파이프라인을 정의할 때 현재 지원 되지 않는 항목을 보여 줍니다.

| 단계 유형 | 지원 여부 |
| ----- | :-----: |
| PythonScriptStep | 예 |
| AdlaStep | 예 |
| AzureBatchStep | 예 |
| DatabricksStep | 예 |
| DataTransferStep | 예 |
| AutoMLStep | 아닙니다. |
| HyperDriveStep | 아닙니다. |
| ModuleStep | 아닙니다. |
| MPIStep | 아닙니다. |
| EstimatorStep | 아닙니다. |

## <a name="pipeline-definition"></a>파이프라인 정의

파이프라인 정의는 [파이프라인](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline.pipeline?view=azure-ml-py) 클래스에 해당 하는 다음 키를 사용 합니다.

| YAML 키 | Description |
| ----- | ----- |
| `name` | 파이프라인에 대 한 설명입니다. |
| `parameters` | 파이프라인에 대 한 매개 변수입니다. |
| `data_reference` | 실행에서 데이터를 사용할 수 있는 방법과 위치를 정의 합니다. |
| `default_compute` | 파이프라인의 모든 단계가 실행 되는 기본 계산 대상입니다. |
| `steps` | 파이프라인에 사용 되는 단계입니다. |

## <a name="parameters"></a>매개 변수

`parameters` 섹션에서는 [PipelineParameter](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelineparameter?view=azure-ml-py) 클래스에 해당 하는 다음 키를 사용 합니다.

| YAML 키 | Description |
| ---- | ---- |
| `type` | 매개 변수의 값 형식입니다. 유효한 유형은 `string`, `int`, `float`, `bool`또는 `datapath`입니다. |
| `default` | 기본값입니다. |

각 매개 변수의 이름은입니다. 예를 들어 다음 YAML 코드 조각은 `NumIterationsParameter`, `DataPathParameter`및 `NodeCountParameter`라는 세 개의 매개 변수를 정의 합니다.

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

`data_references` 섹션에서는 [Datareference](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py)에 해당 하는 다음 키를 사용 합니다.

| YAML 키 | Description |
| ----- | ----- |
| `datastore` | 참조할 데이터 저장소입니다. |
| `path_on_datastore` | 데이터 참조에 대 한 지원 저장소의 상대 경로입니다. |

각 데이터 참조는 키에 포함 되어 있습니다. 예를 들어 다음 YAML 코드 조각은 `employee_data`라는 키에 저장 된 데이터 참조를 정의 합니다.

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

단계는 환경에서 실행 되는 파일과 함께 계산 환경을 정의 합니다. 단계 유형을 정의 하려면 `type` 키를 사용 합니다.

| 단계 유형 | Description |
| ----- | ----- |
| `AdlaStep` | Azure Data Lake Analytics를 사용 하 여 U SQL 스크립트를 실행 합니다. [Adlastep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.adlastep?view=azure-ml-py) 클래스에 해당 합니다. |
| `AzureBatchStep` | Azure Batch를 사용 하 여 작업을 실행 합니다. [Azurebatchstep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.azurebatchstep?view=azure-ml-py) 클래스에 해당 합니다. |
| `DatabricsStep` | Databricks 노트북, Python 스크립트 또는 JAR을 추가 합니다. [DatabricksStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.databricksstep?view=azure-ml-py) 클래스에 해당 합니다. |
| `DataTransferStep` | 저장소 옵션 간에 데이터를 전송 합니다. [DataTransferStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.datatransferstep?view=azure-ml-py) 클래스에 해당 합니다. |
| `PythonScriptStep` | Python 스크립트를 실행 합니다. [PythonScriptStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep?view=azure-ml-py) 클래스에 해당 합니다. |

### <a name="adla-step"></a>ADLA 단계

| YAML 키 | Description |
| ----- | ----- |
| `script_name` | `source_directory`에 상대적인 U-SQL 스크립트의 이름입니다. |
| `compute_target` | 이 단계에 사용할 Azure Data Lake 계산 대상입니다. |
| `parameters` | 파이프라인에 대 한 [매개 변수](#parameters) 입니다. |
| `inputs` | 입력은 [Inputportbinding](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.inputportbinding?view=azure-ml-py), [datareference](#data-reference), [portdatareference](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.portdatareference?view=azure-ml-py), [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py), [Dataset](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29?view=azure-ml-py), [datasetdefinition](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_definition.datasetdefinition?view=azure-ml-py)또는 [PipelineDataset](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedataset?view=azure-ml-py)수 있습니다. |
| `outputs` | 출력은 [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py) 또는 [outputportbinding](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.outputportbinding?view=azure-ml-py)중 하나일 수 있습니다. |
| `source_directory` | 스크립트, 어셈블리 등을 포함 하는 디렉터리입니다. |
| `priority` | 현재 작업에 사용할 우선 순위 값입니다. |
| `params` | 이름-값 쌍의 사전입니다. |
| `degree_of_parallelism` | 이 작업에 사용할 병렬 처리 수준입니다. |
| `runtime_version` | Data Lake Analytics 엔진의 런타임 버전입니다. |
| `allow_reuse` | 동일한 설정으로 다시 실행 하는 경우 단계에서 이전 결과를 다시 사용 해야 하는지 여부를 결정 합니다. |

다음 예제에는 ADLA 단계 정의가 포함 되어 있습니다.

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

### <a name="azure-batch-step"></a>Azure Batch 단계

| YAML 키 | Description |
| ----- | ----- |
| `compute_target` | 이 단계에 사용할 Azure Batch 계산 대상입니다. |
| `inputs` | 입력은 [Inputportbinding](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.inputportbinding?view=azure-ml-py), [datareference](#data-reference), [portdatareference](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.portdatareference?view=azure-ml-py), [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py), [Dataset](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29?view=azure-ml-py), [datasetdefinition](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_definition.datasetdefinition?view=azure-ml-py)또는 [PipelineDataset](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedataset?view=azure-ml-py)수 있습니다. |
| `outputs` | 출력은 [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py) 또는 [outputportbinding](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.outputportbinding?view=azure-ml-py)중 하나일 수 있습니다. |
| `source_directory` | 모듈 이진 파일, 실행 파일, 어셈블리 등을 포함 하는 디렉터리입니다. |
| `executable` | 이 작업의 일부로 실행 되는 명령/실행 파일의 이름입니다. |
| `create_pool` | 작업을 실행 하기 전에 풀을 만들지 여부를 나타내는 부울 플래그입니다. |
| `delete_batch_job_after_finish` | 작업이 완료 된 후 배치 계정에서 작업을 삭제할지 여부를 나타내는 부울 플래그입니다. |
| `delete_batch_pool_after_finish` | 작업이 완료 된 후 풀을 삭제할지 여부를 나타내는 부울 플래그입니다. |
| `is_positive_exit_code_failure` | 작업이 긍정적 코드로 종료 될 경우 작업이 실패 하는지 여부를 나타내는 부울 플래그입니다. |
| `vm_image_urn` | `create_pool` `True`경우 VM은 `VirtualMachineConfiguration`를 사용 합니다. |
| `pool_id` | 작업이 실행 될 풀의 ID입니다. |
| `allow_reuse` | 동일한 설정으로 다시 실행 하는 경우 단계에서 이전 결과를 다시 사용 해야 하는지 여부를 결정 합니다. |

다음 예제에는 Azure Batch 단계 정의가 포함 되어 있습니다.

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

### <a name="databricks-step"></a>Databricks 단계

| YAML 키 | Description |
| ----- | ----- |
| `compute_target` | 이 단계에 사용할 Azure Databricks 계산 대상입니다. |
| `inputs` | 입력은 [Inputportbinding](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.inputportbinding?view=azure-ml-py), [datareference](#data-reference), [portdatareference](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.portdatareference?view=azure-ml-py), [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py), [Dataset](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29?view=azure-ml-py), [datasetdefinition](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_definition.datasetdefinition?view=azure-ml-py)또는 [PipelineDataset](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedataset?view=azure-ml-py)수 있습니다. |
| `outputs` | 출력은 [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py) 또는 [outputportbinding](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.outputportbinding?view=azure-ml-py)중 하나일 수 있습니다. |
| `run_name` | 이 실행에 대 한 Databricks의 이름입니다. |
| `source_directory` | 스크립트 및 기타 파일을 포함 하는 디렉터리입니다. |
| `num_workers` | Databricks 실행 클러스터에 대 한 고정 작업자 수입니다. |
| `runconfig` | `.runconfig` 파일의 경로입니다. 이 파일은 [Runconfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfiguration?view=azure-ml-py) 클래스의 yaml 표현입니다. 이 파일의 구조에 대 한 자세한 내용은 [runconfigschema. json](https://github.com/microsoft/MLOps/blob/b4bdcf8c369d188e83f40be8b748b49821f71cf2/infra-as-code/runconfigschema.json)을 참조 하십시오. |
| `allow_reuse` | 동일한 설정으로 다시 실행 하는 경우 단계에서 이전 결과를 다시 사용 해야 하는지 여부를 결정 합니다. |

다음 예제에는 Databricks 단계가 포함 되어 있습니다.

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
| `compute_target` | 이 단계에 사용할 Azure Data Factory 계산 대상입니다. |
| `source_data_reference` | 데이터 전송 작업의 원본으로 사용 되는 입력 연결입니다. 지원 되는 값은 [Inputportbinding](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.inputportbinding?view=azure-ml-py), [datareference](#data-reference), [portdatareference](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.portdatareference?view=azure-ml-py), [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py), [Dataset](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29?view=azure-ml-py), [datasetdefinition](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_definition.datasetdefinition?view=azure-ml-py)또는 [PipelineDataset](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedataset?view=azure-ml-py)입니다. |
| `destination_data_reference` | 데이터 전송 작업의 대상으로 사용 되는 입력 연결입니다. 지원 되는 값은 [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py) 및 [outputportbinding](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.outputportbinding?view=azure-ml-py)입니다. |
| `allow_reuse` | 동일한 설정으로 다시 실행 하는 경우 단계에서 이전 결과를 다시 사용 해야 하는지 여부를 결정 합니다. |

다음 예제에는 데이터 전송 단계가 포함 되어 있습니다.

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

### <a name="python-script-step"></a>Python 스크립트 단계

| YAML 키 | Description |
| ----- | ----- |
| `compute_target` | 이 단계에 사용할 계산 대상입니다. 계산 대상은 Azure Machine Learning 계산, 가상 머신 (Data Science VM) 또는 HDInsight 일 수 있습니다. |
| `inputs` | 입력은 [Inputportbinding](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.inputportbinding?view=azure-ml-py), [datareference](#data-reference), [portdatareference](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.portdatareference?view=azure-ml-py), [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py), [Dataset](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29?view=azure-ml-py), [datasetdefinition](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_definition.datasetdefinition?view=azure-ml-py)또는 [PipelineDataset](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedataset?view=azure-ml-py)수 있습니다. |
| `outputs` | 출력은 [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py) 또는 [outputportbinding](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.outputportbinding?view=azure-ml-py)중 하나일 수 있습니다. |
| `script_name` | `source_directory`기준으로 하는 Python 스크립트의 이름입니다. |
| `source_directory` | 스크립트, Conda 환경 등을 포함 하는 디렉터리입니다. |
| `runconfig` | `.runconfig` 파일의 경로입니다. 이 파일은 [Runconfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfiguration?view=azure-ml-py) 클래스의 yaml 표현입니다. 이 파일의 구조에 대 한 자세한 내용은 [.runconfig](https://github.com/microsoft/MLOps/blob/b4bdcf8c369d188e83f40be8b748b49821f71cf2/infra-as-code/runconfigschema.json)를 참조 하세요. |
| `allow_reuse` | 동일한 설정으로 다시 실행 하는 경우 단계에서 이전 결과를 다시 사용 해야 하는지 여부를 결정 합니다. |

다음 예에는 Python 스크립트 단계가 포함 되어 있습니다.

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

## <a name="schedules"></a>일정

파이프라인에 대 한 일정을 정의 하는 경우 데이터 저장소는 일정 시간 간격에 따라 트리거하거나 반복 될 수 있습니다. 다음은 일정을 정의 하는 데 사용 되는 키입니다.

| YAML 키 | Description |
| ----- | ----- |
| `description` | 일정에 대한 설명입니다. |
| `recurrence` | 일정이 반복 되는 경우 되풀이 설정을 포함 합니다. |
| `pipeline_parameters` | 파이프라인에 필요한 모든 매개 변수입니다. |
| `wait_for_provisioning` | 일정 프로 비전 완료를 완료할 때까지 기다릴지 여부입니다. |
| `wait_timeout` | 제한 시간이 초과 될 때까지 대기 하는 시간 (초)입니다. |
| `datastore_name` | 수정/추가 된 blob을 모니터링할 데이터 저장소입니다. |
| `polling_interval` | 수정/추가 된 blob에 대 한 폴링 사이의 시간 (분)입니다. 기본값: 5 분 데이터 저장소 일정에만 지원 됩니다. |
| `data_path_parameter_name` | 변경 된 blob 경로를 사용 하 여 설정할 데이터 경로 파이프라인 매개 변수의 이름입니다. 데이터 저장소 일정에만 지원 됩니다. |
| `continue_on_step_failure` | 단계가 실패 하는 경우 제출 된 PipelineRun에서 다른 단계를 계속 실행할지 여부를 지정 합니다. 제공 된 경우는 파이프라인의 `continue_on_step_failure` 설정을 재정의 합니다.
| `path_on_datastore` | (선택 사항) 수정/추가 된 blob을 모니터링할 데이터 저장소의 경로입니다. 경로는 데이터 저장소의 컨테이너 아래에 있으므로 일정 모니터링의 실제 경로는 컨테이너/`path_on_datastore`입니다. 없는 경우 데이터 저장소 컨테이너가 모니터링 됩니다. `path_on_datastore`의 하위 폴더에 대 한 추가/수정 사항은 모니터링 되지 않습니다. 데이터 저장소 일정에만 지원 됩니다. |

다음 예제에는 데이터 저장소 트리거 일정에 대 한 정의가 포함 되어 있습니다.

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

**되풀이 일정**을 정의할 때 `recurrence`아래에 다음 키를 사용 합니다.

| YAML 키 | Description |
| ----- | ----- |
| `frequency` | 일정을 되풀이 하는 빈도입니다. 유효한 값은 `"Minute"`, `"Hour"`, `"Day"`, `"Week"`또는 `"Month"`입니다. |
| `interval` | 일정이 발생 하는 빈도입니다. 정수 값은 일정이 다시 발생 될 때까지 대기 하는 시간 단위 수입니다. |
| `start_time` | 일정의 시작 시간입니다. 값의 문자열 형식이 `YYYY-MM-DDThh:mm:ss`입니다. 시작 시간을 지정 하지 않으면 첫 번째 작업은 즉시 실행 되 고 후속 작업은 일정에 따라 실행 됩니다. 시작 시간이 과거 이면 첫 번째 작업은 계산 된 다음 실행 시간에 실행 됩니다. |
| `time_zone` | 시작 시간의 표준 시간대입니다. 표준 시간대를 제공 하지 않으면 UTC가 사용 됩니다. |
| `hours` | `frequency` `"Day"` 또는 `"Week"`경우에는 0에서 23 사이의 정수를 쉼표로 구분 하 여 파이프라인을 실행 해야 하는 시간으로 지정할 수 있습니다. `time_of_day` 또는 `hours` 및 `minutes`만 사용할 수 있습니다. |
| `minutes` | `frequency` `"Day"` 또는 `"Week"`경우 파이프라인이 실행 되는 시간 (분)으로 0에서 59 사이의 정수 하나 이상을 쉼표로 구분 하 여 지정할 수 있습니다. `time_of_day` 또는 `hours` 및 `minutes`만 사용할 수 있습니다. |
| `time_of_day` | `frequency` `"Day"` 또는 `"Week"`경우 일정을 실행할 시간을 지정할 수 있습니다. 값의 문자열 형식이 `hh:mm`입니다. `time_of_day` 또는 `hours` 및 `minutes`만 사용할 수 있습니다. |
| `week_days` | `frequency` `"Week"`되는 경우 일정을 실행 해야 할 때 하나 이상의 날짜를 쉼표로 구분 하 여 지정할 수 있습니다. 유효한 값은 `"Monday"`, `"Tuesday"`, `"Wednesday"`, `"Thursday"`, `"Friday"`, `"Saturday"`, `"Sunday"`입니다. |

다음 예에서는 되풀이 일정에 대 한 정의를 포함 합니다.

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

[Azure Machine Learning에 CLI 확장을 사용](reference-azure-machine-learning-cli.md)하는 방법에 대해 알아봅니다.