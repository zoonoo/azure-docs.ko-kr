---
title: ML 파이프라인 만들기, 실행 및 추적
titleSuffix: Azure Machine Learning service
description: Python용 Azure Machine Learning SDK를 사용하여 기계 학습 파이프라인을 만들고 실행합니다. 기계 학습 (ML) 단계를 함께 사용 하는 워크플로를 만들고 관리 하려면 ML 파이프라인을 사용 합니다. 이러한 단계에는 데이터 준비, 모델 학습, 모델 배포 및 유추/점수 매기기가 포함 됩니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: sgilley
ms.author: sanpil
author: sanpil
ms.date: 08/09/2019
ms.custom: seodec18
ms.openlocfilehash: 87897c031ff717fb67830cb8fa3bc5fced336418
ms.sourcegitcommit: 32242bf7144c98a7d357712e75b1aefcf93a40cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70278850"
---
# <a name="create-and-run-machine-learning-pipelines-with-azure-machine-learning-sdk"></a>Azure Machine Learning SDK를 사용 하 여 machine learning 파이프라인 만들기 및 실행

이 문서에서는 [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)를 사용하여 [기계 학습 파이프라인](concept-ml-pipelines.md)을 만들고 게시, 실행 및 추적하는 방법을 알아봅니다.  **Ml 파이프라인** 을 사용 하 여 다양 한 ml 단계를 함께 연결 하는 워크플로를 만든 후 나중에 액세스 하거나 다른 사용자와 공유 하기 위해 해당 파이프라인을 Azure Machine Learning 작업 영역에 게시 합니다.  ML 파이프라인은 다양 한 계산을 사용 하 여 일괄 처리를 다시 실행 하는 대신 단계를 다시 사용 하 고 ML 워크플로를 다른 사람들과 공유 하는 일괄 처리 점수 매기기 시나리오에 적합 합니다. 

ML 작업의 CI/CD 자동화를 위해 [Azure 파이프라인](https://docs.microsoft.com/azure/devops/pipelines/targets/azure-machine-learning?context=azure%2Fmachine-learning%2Fservice%2Fcontext%2Fml-context&view=azure-devops&tabs=yaml) 이라는 다른 종류의 파이프라인을 사용할 수 있지만 해당 유형의 파이프라인은 작업 영역 내에 저장 되지 않습니다. [이러한 서로 다른 파이프라인을 비교](concept-ml-pipelines.md#which-azure-pipeline-technology-should-i-use)합니다.

데이터 준비 및 모델 교육과 같은 ML 파이프라인의 각 단계에는 하나 이상의 단계가 포함 될 수 있습니다.

만든 ML 파이프라인은 Azure Machine Learning 서비스 [작업 영역의](how-to-manage-workspace.md)구성원에 게 표시 됩니다. 

ML 파이프라인은 계산에 원격 계산 대상을 사용 하 고 해당 파이프라인과 연결 된 중간 및 최종 데이터의 저장소를 사용 합니다. 지원 되는 [Azure Storage](https://docs.microsoft.com/azure/storage/) 위치에서 데이터를 읽고 쓸 수 있습니다.

Azure 구독이 없는 경우 시작하기 전에 체험 계정을 만듭니다. [Azure Machine Learning Service의 평가판 또는 유료 버전](https://aka.ms/AMLFree)을 사용해 보세요.

## <a name="prerequisites"></a>전제 조건

* 모든 파이프라인 리소스를 수용하는 [Azure Machine Learning 작업 영역](how-to-manage-workspace.md)을 만듭니다.

* Azure Machine Learning SDK를 설치 하거나 SDK가 이미 설치 된 [노트북 VM](tutorial-1st-experiment-sdk-setup.md#azure) 을 사용 하도록 [개발 환경을 구성](how-to-configure-environment.md) 합니다.

작업 영역을 연결 하 여 시작 합니다.

```Python
import azureml.core
from azureml.core import Workspace, Datastore

ws = Workspace.from_config()
```


## <a name="set-up-machine-learning-resources"></a>기계 학습 리소스 설정

ML 파이프라인을 실행 하는 데 필요한 리소스를 만듭니다.

* 파이프라인 단계에서 필요한 데이터에 액세스하는 데 사용되는 데이터 저장소를 설정합니다.

* 데이터 저장소에 상주하거나 여기서 액세스할 수 있는 데이터를 가리키도록 `DataReference` 개체를 구성합니다.

* 파이프라인 단계가 실행될 [컴퓨팅 대상](concept-azure-machine-learning-architecture.md#compute-targets)을 설정합니다.

### <a name="set-up-a-datastore"></a>데이터 저장소 설정

데이터 저장소는 파이프라인에서 액세스할 데이터를 저장합니다. 각 작업 영역마다 기본 데이터 저장소가 있습니다. 추가 데이터 저장소를 등록할 수 있습니다. 

작업 영역을 만들 때 [Azure Files](https://docs.microsoft.com/azure/storage/files/storage-files-introduction) 및 [Azure Blob 저장소가](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction) 작업 영역에 연결 됩니다. 기본 데이터 저장소는 Azure Blob storage에 연결 하기 위해 등록 됩니다. 자세한 내용은 [Azure Files, Azure Blob 또는 Azure Disk를 사용할지 여부 결정](https://docs.microsoft.com/azure/storage/common/storage-decide-blobs-files-disks)을 참조하세요. 

```python
# Default datastore 
def_data_store = ws.get_default_datastore()

# Get the blob storage associated with the workspace
def_blob_store = Datastore(ws, "workspaceblobstore")

# Get file storage associated with the workspace
def_file_store = Datastore(ws, "workspacefilestore")

```

파이프라인에서 액세스할 수 있도록 데이터 파일 또는 디렉터리를 해당 데이터 저장소에 업로드합니다. 이 예제에서는 Blob 저장소를 데이터 저장소로 사용 합니다.

```python
def_blob_store.upload_files(
    ["./data/20news.pkl"],
    target_path="20newsgroups",
    overwrite=True)
```

파이프라인은 하나 이상의 단계로 구성됩니다. 단계는 컴퓨팅 대상에서 실행되는 단위입니다. 단계는 데이터 원본을 사용하고 “중간” 데이터를 생성할 수 있습니다. 단계는 모델, 모델과 종속 파일이 있는 디렉터리 또는 임시 데이터와 같은 데이터를 만들 수 있습니다. 그런 다음, 파이프라인의 다른 후속 단계에서 이 데이터를 사용할 수 있습니다.

### <a name="configure-data-reference"></a>데이터 참조 구성

파이프라인에서 단계의 입력으로 참조할 수 있는 데이터 원본을 방금 만들었습니다. 파이프라인에서 데이터 원본은 [DataReference](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference) 개체로 표시됩니다. `DataReference` 개체는 데이터 저장소에 상주하거나 여기서 액세스할 수 있는 데이터를 가리킵니다.

```python
from azureml.data.data_reference import DataReference

blob_input_data = DataReference(
    datastore=def_blob_store,
    data_reference_name="test_data",
    path_on_datastore="20newsgroups/20news.pkl")
```

중간 데이터(또는 단계의 출력)는 [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py) 개체로 표시됩니다. `output_data1`은 단계의 출력으로 생성되며 하나 이상 후속 단계의 입력으로 사용됩니다. `PipelineData`는 단계 간에 데이터 종속성을 도입하고 파이프라인에 암시적 실행 순서를 만듭니다.

```python
from azureml.pipeline.core import PipelineData

output_data1 = PipelineData(
    "output_data1",
    datastore=def_blob_store,
    output_name="output_data1")
```

## <a name="set-up-compute-target"></a>컴퓨팅 대상 설정

Azure Machine Learning에서 computes__ (또는 __계산 대상__) 이라는 용어는 Machine Learning 파이프라인에서 계산 단계를 수행 하는 컴퓨터 또는 클러스터를 의미 합니다.   컴퓨팅 대상의 전체 목록 및 컴퓨팅 대상을 만들고 작업 영역에 연결하는 방법에 대해서는 [모델 학습을 위한 컴퓨팅 대상](how-to-set-up-training-targets.md)을 참조하세요.  모델을 학습하든 파이프라인 단계를 실행하든 상관 없이 컴퓨팅 모델을 만들고 연결하는 프로세스는 동일합니다. 컴퓨팅 대상을 만들고 연결한 후 [파이프라인 단계](#steps)에서 `ComputeTarget` 개체를 사용합니다.

> [!IMPORTANT]
> 컴퓨팅 대상에 대한 관리 작업 수행은 원격 작업 내에서 지원되지 않습니다. 기계 학습 파이프라인은 원격 작업으로 제출되므로 파이프라인 내부에서 컴퓨팅 대상에 관리 작업을 사용하지 마십시오.

다음에 대한 컴퓨팅 대상을 만들고 연결하는 예가 아래에 나와 있습니다.

* Azure Machine Learning 컴퓨팅
* Azure Databricks 
* Azure Data Lake Analytics

### <a name="azure-machine-learning-compute"></a>Azure Machine Learning 컴퓨팅

단계를 실행하기 위한 Azure Machine Learning 컴퓨팅을 만들 수 있습니다.

```python
from azureml.core.compute import ComputeTarget, AmlCompute

compute_name = "aml-compute"
vm_size = "STANDARD_NC6"
if compute_name in ws.compute_targets:
    compute_target = ws.compute_targets[compute_name]
    if compute_target and type(compute_target) is AmlCompute:
        print('Found compute target: ' + compute_name)
else:
    print('Creating a new compute target...')
    provisioning_config = AmlCompute.provisioning_configuration(vm_size=vm_size,  # STANDARD_NC6 is GPU-enabled
                                                                min_nodes=0,
                                                                max_nodes=4)
    # create the compute target
    compute_target = ComputeTarget.create(
        ws, compute_name, provisioning_config)

    # Can poll for a minimum number of nodes and for a specific timeout.
    # If no min node count is provided it will use the scale settings for the cluster
    compute_target.wait_for_completion(
        show_output=True, min_node_count=None, timeout_in_minutes=20)

    # For a more detailed view of current cluster status, use the 'status' property
    print(compute_target.status.serialize())
```

### <a id="databricks"></a>Azure Databricks

Azure Databricks는 Azure 클라우드의 Apache Spark 기반 환경입니다. 이 환경은 Azure Machine Learning 파이프라인 사용 시 컴퓨팅 대상으로 사용할 수 있습니다.

사용하기 전에 Azure Databricks 작업 영역을 만듭니다. 작업 영역 리소스를 만들려면 [Azure Databricks에서 Spark 작업 실행](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal) 문서를 참조 하세요.

Azure Databricks를 컴퓨팅 대상으로 연결하려면 다음 정보를 제공합니다.

* __Databricks 컴퓨팅 이름__: 이 컴퓨팅 리소스에 할당하려는 이름입니다.
* __Databricks 작업 영역__: Azure Databricks 작업 영역의 이름입니다.
* __Databricks 액세스 토큰__: Azure Databricks에서 인증을 받는 데 사용하는 액세스 토큰입니다. 액세스 토큰을 생성하려면 [인증](https://docs.azuredatabricks.net/api/latest/authentication.html) 문서를 참조하세요.

다음 코드는 Azure Machine Learning SDK를 사용하여 Azure Databricks를 컴퓨팅 대상으로 연결하는 방법을 보여줍니다.

```python
import os
from azureml.core.compute import ComputeTarget, DatabricksCompute
from azureml.exceptions import ComputeTargetException

databricks_compute_name = os.environ.get(
    "AML_DATABRICKS_COMPUTE_NAME", "<databricks_compute_name>")
databricks_workspace_name = os.environ.get(
    "AML_DATABRICKS_WORKSPACE", "<databricks_workspace_name>")
databricks_resource_group = os.environ.get(
    "AML_DATABRICKS_RESOURCE_GROUP", "<databricks_resource_group>")
databricks_access_token = os.environ.get(
    "AML_DATABRICKS_ACCESS_TOKEN", "<databricks_access_token>")

try:
    databricks_compute = ComputeTarget(
        workspace=ws, name=databricks_compute_name)
    print('Compute target already exists')
except ComputeTargetException:
    print('compute not found')
    print('databricks_compute_name {}'.format(databricks_compute_name))
    print('databricks_workspace_name {}'.format(databricks_workspace_name))
    print('databricks_access_token {}'.format(databricks_access_token))

    # Create attach config
    attach_config = DatabricksCompute.attach_configuration(resource_group=databricks_resource_group,
                                                           workspace_name=databricks_workspace_name,
                                                           access_token=databricks_access_token)
    databricks_compute = ComputeTarget.attach(
        ws,
        databricks_compute_name,
        attach_config
    )

    databricks_compute.wait_for_completion(True)
```

자세한 예제는 GitHub의 [예제 노트북](https://aka.ms/pl-databricks) 을 참조 하세요.

### <a id="adla"></a>Azure Data Lake Analytics

Azure Data Lake Analytics는 Azure 클라우드의 빅 데이터 분석 플랫폼입니다. 이 환경은 Azure Machine Learning 파이프라인 사용 시 컴퓨팅 대상으로 사용할 수 있습니다.

사용하기 전에 Azure Data Lake Analytics 계정을 만듭니다. 이 리소스를 만들려면 [Azure Data Lake Analytics 시작](https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-get-started-portal) 문서를 참조하세요.

Data Lake Analytics를 컴퓨팅 대상으로 연결하려면 Azure Machine Learning SDK를 사용하고 다음 정보를 제공해야 합니다.

* __컴퓨팅 이름__: 이 컴퓨팅 리소스에 할당하려는 이름입니다.
* __리소스 그룹__: Data Lake Analytics 계정을 포함하는 리소스 그룹입니다.
* __계정 이름__: Data Lake Analytics 계정 이름입니다.

다음 코드는 컴퓨팅 대상으로 Data Lake Analytics에 연결하는 방법을 보여 줍니다.

```python
import os
from azureml.core.compute import ComputeTarget, AdlaCompute
from azureml.exceptions import ComputeTargetException


adla_compute_name = os.environ.get(
    "AML_ADLA_COMPUTE_NAME", "<adla_compute_name>")
adla_resource_group = os.environ.get(
    "AML_ADLA_RESOURCE_GROUP", "<adla_resource_group>")
adla_account_name = os.environ.get(
    "AML_ADLA_ACCOUNT_NAME", "<adla_account_name>")

try:
    adla_compute = ComputeTarget(workspace=ws, name=adla_compute_name)
    print('Compute target already exists')
except ComputeTargetException:
    print('compute not found')
    print('adla_compute_name {}'.format(adla_compute_name))
    print('adla_resource_id {}'.format(adla_resource_group))
    print('adla_account_name {}'.format(adla_account_name))
    # create attach config
    attach_config = AdlaCompute.attach_configuration(resource_group=adla_resource_group,
                                                     account_name=adla_account_name)
    # Attach ADLA
    adla_compute = ComputeTarget.attach(
        ws,
        adla_compute_name,
        attach_config
    )

    adla_compute.wait_for_completion(True)
```

자세한 예제는 GitHub의 [예제 노트북](https://aka.ms/pl-adla) 을 참조 하세요.

> [!TIP]
> Azure Machine Learning 파이프라인은 Data Lake Analytics 계정의 기본 데이터 저장소에 저장된 데이터에만 작동할 수 있습니다. 사용해야 하는 데이터가 기본이 아닌 저장소에 있으면 학습 전에 [`DataTransferStep`](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.data_transfer_step.datatransferstep?view=azure-ml-py)을 사용하여 데이터를 복사할 수 있습니다.

## <a id="steps"></a>파이프라인 단계 구성

컴퓨팅 대상을 만들고 작업 영역에 연결하면 파이프라인 단계를 정의할 준비가 된 것입니다. Azure Machine Learning SDK를 통해 사용할 수 있는 여러 가지 기본 제공 단계가 있습니다. 이러한 단계의 가장 기본적인 방법은 지정 된 계산 대상에서 Python 스크립트를 실행 하는 [PythonScriptStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep?view=azure-ml-py)입니다.

```python
from azureml.pipeline.steps import PythonScriptStep

trainStep = PythonScriptStep(
    script_name="train.py",
    arguments=["--input", blob_input_data, "--output", output_data1],
    inputs=[blob_input_data],
    outputs=[output_data1],
    compute_target=compute_target,
    source_directory=project_folder
)
```

이전 결과 (`allow_reuse`)를 다시 사용 하는 것은 불필요 한 다시 정렬을 제거 하면 민첩성을 제공 하므로 공동 작업 환경에서 파이프라인을 사용할 때 키입니다. 다시 사용은 script_name, 입력 및 단계의 매개 변수가 동일 하 게 유지 되는 경우의 기본 동작입니다. 단계의 출력이 다시 사용 되는 경우 작업은 계산에 전송 되지 않고 이전 실행의 결과를 다음 단계의 실행에 즉시 사용할 수 있습니다. 가 `allow_reuse` false로 설정 된 경우 파이프라인 실행 중에이 단계에 대 한 새 실행이 항상 생성 됩니다. 

단계를 정의한 후 일부 또는 모든 단계를 사용하여 파이프라인을 빌드합니다.

> [!NOTE]
> 단계를 정의하거나 파이프라인을 빌드할 때는 Azure Machine Learning Service에 파일 또는 데이터가 업로드되지 않습니다.

```python
# list of steps to run
compareModels = [trainStep, extractStep, compareStep]

from azureml.pipeline.core import Pipeline

# Build the pipeline
pipeline1 = Pipeline(workspace=ws, steps=[compareModels])
```

다음 예제에서는 앞서 만든 Azure Databricks 컴퓨팅 대상을 사용합니다. 

```python
from azureml.pipeline.steps import DatabricksStep

dbStep = DatabricksStep(
    name="databricksmodule",
    inputs=[step_1_input],
    outputs=[step_1_output],
    num_workers=1,
    notebook_path=notebook_path,
    notebook_params={'myparam': 'testparam'},
    run_name='demo run name',
    compute_target=databricks_compute,
    allow_reuse=False
)
# List of steps to run
steps = [dbStep]

# Build the pipeline
pipeline1 = Pipeline(workspace=ws, steps=steps)
```

자세한 내용은 [azure 파이프라인 단계 패키지](https://docs.microsoft.com/python/api/azureml-pipeline-steps/?view=azure-ml-py) 및 [파이프라인 클래스](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline%28class%29?view=azure-ml-py) 참조를 참조 하세요.

## <a name="submit-the-pipeline"></a>파이프라인 제출

파이프라인을 제출하면 Azure Machine Learning Service는 각 단계에 대한 종속성을 확인하고 지정된 소스 디렉터리의 스냅샷을 업로드합니다. 소스 디렉터리를 지정하지 않으면 현재 로컬 디렉터리가 업로드됩니다. 또한 스냅숏은 작업 영역에 실험의 일부로 저장 됩니다.

> [!IMPORTANT]
> 파일이 스냅숏에 포함 되지 않도록 하려면 디렉터리에 [.gitignore](https://git-scm.com/docs/gitignore) 또는 `.amlignore` 파일을 만들고 파일을 추가 합니다. 이 `.amlignore` 파일은 [.gitignore](https://git-scm.com/docs/gitignore) 파일과 동일한 구문과 패턴을 사용 합니다. 두 파일이 모두 있는 경우 `.amlignore` 파일이 우선적으로 적용 됩니다.
>
> 자세한 내용은 [스냅샷](concept-azure-machine-learning-architecture.md#snapshots)을 참조하세요.

```python
from azureml.core import Experiment

# Submit the pipeline to be run
pipeline_run1 = Experiment(ws, 'Compare_Models_Exp').submit(pipeline1)
pipeline_run1.wait_for_completion()
```

파이프라인을 처음 실행하는 경우 Azure Machine Learning은 다음을 수행합니다.

* 작업 영역과 연결된 Blob Storage에서 컴퓨팅 대상으로 프로젝트 스냅샷을 다운로드합니다.
* 파이프라인의 각 단계에 해당하는 Docker 이미지를 빌드합니다.
* 각 단계에 대 한 Docker 이미지를 컨테이너 레지스트리에서 계산 대상으로 다운로드 합니다.
* 단계에서 `DataReference` 개체가 지정 된 경우 데이터 저장소를 탑재 합니다. 탑재가 지원되지 않는 경우 데이터가 대신 컴퓨팅 대상에 복사됩니다.
* 단계 정의에 지정된 컴퓨팅 대상에서 단계를 실행합니다. 
* 단계에서 지정한 로그, stdout, stderr, 메트릭, 출력 등의 아티팩트를 만듭니다. 그런 다음, 이러한 아티팩트가 업로드되어 사용자의 기본 데이터 저장소에 보관됩니다.

![실험을 파이프라인으로 실행하는 다이어그램](./media/how-to-create-your-first-pipeline/run_an_experiment_as_a_pipeline.png)

자세한 내용은 [실험 클래스](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment?view=azure-ml-py) 참조를 참조 하세요.



## <a name="github-tracking-and-integration"></a>GitHub 추적 및 통합

원본 디렉터리가 로컬 Git 리포지토리 인 학습 실행을 시작 하면 리포지토리에 대 한 정보가 실행 기록에 저장 됩니다. 예를 들어 리포지토리의 현재 커밋 ID는 기록의 일부로 기록 됩니다.

## <a name="publish-a-pipeline"></a>파이프라인 게시

파이프라인을 게시하여 나중에 다른 입력을 사용하여 실행할 수 있습니다. 이미 게시된 파이프라인의 REST 엔드포인트가 매개 변수를 허용하려면 파이프라인을 게시하기 전에 매개 변수화해야 합니다.

1. 파이프라인 매개 변수를 만들려면 기본값으로 [PipelineParameter](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.pipelineparameter?view=azure-ml-py) 개체를 사용합니다.

   ```python
   from azureml.pipeline.core.graph import PipelineParameter
   
   pipeline_param = PipelineParameter(
     name="pipeline_arg",
     default_value=10)
   ```

2. 다음과 같이 이 `PipelineParameter` 개체를 파이프라인의 단계에 대한 매개 변수로 추가합니다.

   ```python
   compareStep = PythonScriptStep(
     script_name="compare.py",
     arguments=["--comp_data1", comp_data1, "--comp_data2", comp_data2, "--output_data", out_data3, "--param1", pipeline_param],
     inputs=[ comp_data1, comp_data2],
     outputs=[out_data3],
     target=compute_target,
     source_directory=project_folder)
   ```

3. 이 파이프라인을 게시하면 호출 시 매개 변수를 허용합니다.

   ```python
   published_pipeline1 = pipeline_run1.publish_pipeline(
        name="My_Published_Pipeline",
        description="My Published Pipeline Description",
        version="1.0")
   ```

### <a name="run-a-published-pipeline"></a>게시된 파이프라인 실행

게시된 모든 파이프라인에는 REST 엔드포인트가 있습니다. 이 엔드포인트는 비-Python 클라이언트 등의 외부 시스템에서 파이프라인 실행을 호출합니다. 이 엔드포인트는 일괄 처리 채점 및 다시 학습 시나리오에서 “관리되는 반복 가능성”을 지원합니다.

이전 파이프라인의 실행을 호출 하려면 [Azurecliauthentication 클래스](https://docs.microsoft.com/python/api/azureml-core/azureml.core.authentication.azurecliauthentication?view=azure-ml-py) 참조에 설명 된 대로 Azure Active Directory 인증 헤더 토큰이 필요 하거나 [Azure Machine Learning 노트북의 인증](https://aka.ms/pl-restep-auth) 에 대 한 자세한 내용을 확인 하세요.

```python
from azureml.pipeline.core import PublishedPipeline
import requests

response = requests.post(published_pipeline1.endpoint,
                         headers=aad_token,
                         json={"ExperimentName": "My_Pipeline",
                               "ParameterAssignments": {"pipeline_arg": 20}})
```

### <a name="view-results-of-a-published-pipeline"></a>게시 된 파이프라인의 결과 보기

게시 된 모든 파이프라인 목록과 해당 실행 세부 정보를 확인 합니다.
1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.  

1. [작업 영역을 보고](how-to-manage-workspace.md#view) 파이프라인 목록을 찾습니다.
 ![기계 학습 파이프라인 목록](./media/how-to-create-your-first-pipeline/list_of_pipelines.png)
 
1. 특정 파이프라인을 선택하여 실행 결과를 확인합니다.

### <a name="disable-a-published-pipeline"></a>게시 된 파이프라인 사용 안 함

게시 된 파이프라인 목록에서 파이프라인을 숨기려면 사용 하지 않도록 설정 합니다.

```
# Get the pipeline by using its ID in the Azure portal
p = PublishedPipeline.get(ws, id="068f4885-7088-424b-8ce2-eeb9ba5381a6")
p.disable()
```

에서 `p.enable()`다시 사용 하도록 설정할 수 있습니다. 자세한 내용은 [PublishedPipeline 클래스](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.publishedpipeline?view=azure-ml-py) 참조를 참조 하세요.


## <a name="caching--reuse"></a>캐싱 & 재사용  

파이프라인의 동작을 최적화 하 고 사용자 지정 하기 위해 캐싱 및 다시 사용에 대 한 몇 가지 작업을 수행할 수 있습니다. 예를 들어 다음을 선택할 수 있습니다.
+ [단계 정의](https://docs.microsoft.com/python/api/azureml-pipeline-steps/?view=azure-ml-py)중에를 설정 `allow_reuse=False` 하 여 **단계의 기본 재사용을 해제** 합니다. 불필요 한 실행을 제거 하면 민첩성을 제공 하므로 공동 작업 환경에서 파이프라인을 사용할 때 재사용할 수 있습니다. 그러나 재사용을 옵트아웃 (opt out) 할 수 있습니다.
+ **스크립트를 벗어나 해시를 확장**하 여 source_directory의 절대 경로 또는 상대 경로를 사용 하 여 다른 파일 및 디렉터리에 추가 합니다.`hash_paths=['<file or directory']` 
+ 를 사용 하 여 **실행의 모든 단계에 대해 출력 다시 생성**`pipeline_run = exp.submit(pipeline, regenerate_outputs=False)`

기본적 `allow_reuse` 으로 단계를 사용 하 고 주 스크립트 파일만 해시 합니다. 따라서 지정 된 단계의 스크립트가 동일 (`script_name`, 입력 및 매개 변수) 상태로 유지 되는 경우 이전 단계 실행의 출력이 다시 사용 되 고, 작업이 계산에 전송 되지 않고, 이전 실행의 결과를 다음 단계에서 즉시 사용할 수 있습니다. .  

```python
step = PythonScriptStep(name="Hello World",
                        script_name="hello_world.py",
                        compute_target=aml_compute,
                        source_directory=source_directory,
                        allow_reuse=False,
                        hash_paths=['hello_world.ipynb'])
```
 

## <a name="next-steps"></a>다음 단계

- [GitHub의 Jupyter 노트북](https://aka.ms/aml-pipeline-readme)을 사용하여 기계 학습 파이프라인을 추가로 살펴봅니다.
- [Azureml-파이프라인-코어](https://docs.microsoft.com/python/api/azureml-pipeline-core/?view=azure-ml-py) 패키지 및 azureml- [파이프라인 단계](https://docs.microsoft.com/python/api/azureml-pipeline-steps/?view=azure-ml-py) 패키지에 대 한 SDK 참조 도움말을 참조 하세요.

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]
