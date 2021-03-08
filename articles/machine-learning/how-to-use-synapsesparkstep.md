---
title: Machine learning 파이프라인에서 Apache Spark 사용 (미리 보기)
titleSuffix: Azure Machine Learning
description: Azure Synapse Analytics 작업 영역을 Azure machine learning 파이프라인에 연결 하 여 데이터 조작을 위해 Apache Spark를 사용 합니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: laobri
author: lobrien
ms.date: 03/04/2021
ms.topic: conceptual
ms.custom: how-to, synapse-azureml
ms.openlocfilehash: 1dc4e0b70b0d39d01bada26992eb2213c1e855c5
ms.sourcegitcommit: 6386854467e74d0745c281cc53621af3bb201920
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/08/2021
ms.locfileid: "102455062"
---
# <a name="how-to-use-apache-spark-powered-by-azure-synapse-analytics-in-your-machine-learning-pipeline-preview"></a>Machine learning 파이프라인 (미리 보기)에서 Apache Spark (Azure Synapse Analytics에서 구동)를 사용 하는 방법

이 문서에서는 Azure Synapse Analytics에서 제공 하는 Apache Spark 풀을 Azure Machine Learning 파이프라인의 데이터 준비 단계에 대 한 계산 대상으로 사용 하는 방법에 대해 알아봅니다. 단일 파이프라인이 특정 단계에 적합 한 계산 리소스 (예: 데이터 준비 또는 학습)를 사용할 수 있는 방법을 알아봅니다. Spark 단계에 대 한 데이터를 준비 하는 방법과 다음 단계에 전달 하는 방법을 확인할 수 있습니다. 

## <a name="prerequisites"></a>사전 요구 사항

* 모든 파이프라인 리소스를 수용하는 [Azure Machine Learning 작업 영역](how-to-manage-workspace.md)을 만듭니다.

* Azure Machine Learning SDK를 설치 하거나 SDK가 이미 설치 된 [Azure Machine Learning 계산 인스턴스](concept-compute-instance.md) 를 사용 하도록 [개발 환경을 구성](how-to-configure-environment.md) 합니다.

* Azure Synapse Analytics 작업 영역 및 Apache Spark 풀을 만듭니다 ( [빠른 시작: Synapse Studio를 사용 하 여 서버를 사용 하지 않는 Apache Spark 풀 만들기](../synapse-analytics/quickstart-create-apache-spark-pool-studio.md)참조). 

## <a name="link-your-azure-machine-learning-workspace-and-azure-synapse-analytics-workspace"></a>Azure Machine Learning 작업 영역 및 Azure Synapse Analytics 작업 영역 연결 

Azure Synapse Analytics 작업 영역에서 Apache Spark 풀을 만들고 관리 합니다. Apache Spark 풀을 Azure Machine Learning 작업 영역과 통합 하려면 [Azure Synapse Analytics 작업 영역에 연결](how-to-link-synapse-ml-workspaces.md)해야 합니다. 

**연결 된 서비스** 페이지를 사용 하 여 AZURE MACHINE LEARNING studio UI를 통해 Apache Spark 풀을 연결할 수 있습니다. Compute **연결** 옵션을 사용 하 여 **계산** 페이지를 통해 수행할 수도 있습니다.

SDK를 통해 (아래에서 구체화 된) 또는 ARM 템플릿을 통해 Apache Spark 풀을 연결할 수도 있습니다 (이 [예제 ARM 템플릿](https://github.com/Azure/azure-quickstart-templates/blob/master/101-machine-learning-linkedservice-create/azuredeploy.json)참조). 

명령줄을 사용 하 여 ARM 템플릿을 따르고, 연결 된 서비스를 추가 하 고, 다음 코드를 사용 하 여 Apache Spark 풀을 연결할 수 있습니다.

```bash
az deployment group create --name --resource-group <rg_name> --template-file "azuredeploy.json" --parameters @"azuredeploy.parameters.json"
```

> [!Important]
> Azure Synapse Analytics 작업 영역에 성공적으로 연결 하려면 Azure Synapse Analytics 작업 영역 리소스에 소유자 역할이 있어야 합니다. Azure Portal에서 액세스를 확인 합니다.
> 연결 된 서비스를 만들 때 연결 된 서비스에는 시스템 할당 Id (SAI)가 표시 됩니다. Spark 작업을 제출할 수 있도록이 링크 서비스를 Synapse Studio의 "Synapse Apache Spark 관리자" 역할에 할당 해야 합니다 ( [Synapse Studio에서 SYNAPSE RBAC 역할 할당을 관리 하는 방법](../synapse-analytics/security/how-to-manage-synapse-rbac-role-assignments.md)참조). 또한 Azure Machine Learning 작업 영역의 사용자에 게 리소스 management Azure Portal에서 "참가자" 역할을 부여 해야 합니다.

## <a name="create-or-retrieve-the-link-between-your-azure-synapse-analytics-workspace-and-your-azure-machine-learning-workspace"></a>Azure Synapse Analytics 작업 영역 및 Azure Machine Learning 작업 영역 간의 링크를 만들거나 검색 합니다.

다음과 같은 코드를 사용 하 여 작업 영역에서 연결 된 서비스를 검색할 수 있습니다.

```python
from azureml.core import Workspace, LinkedService, SynapseWorkspaceLinkedServiceConfiguration

ws = Workspace.from_config()

for service in LinkedService.list(ws) : 
    print(f"Service: {service}")

# Retrieve a known linked service
linked_service = LinkedService.get(ws, 'synapselink1')
```

먼저 `Workspace.from_config()` 의 구성을 사용 하 여 Azure Machine Learning 작업 영역에 액세스 `config.json` 합니다. [자습서: 개발 환경에서 Azure Machine Learning 시작](tutorial-1st-experiment-sdk-setup-local.md)을 참조 하세요. 그런 다음 코드는 작업 영역에서 사용할 수 있는 모든 연결 된 서비스를 인쇄 합니다. 마지막으로, `LinkedService.get()` 라는 연결 된 서비스를 검색 `'synapselink1'` 합니다. 

## <a name="attach-your-apache-spark-pool-as-a-compute-target-for-azure-machine-learning"></a>Azure Machine Learning에 대 한 계산 대상으로 Apache spark 풀 연결

Apache spark 풀을 사용 하 여 machine learning 파이프라인의 단계를 `ComputeTarget` 수행 하려면 다음 코드에 표시 된 것 처럼 파이프라인 단계에 대 한로 연결 해야 합니다.

```python
from azureml.core.compute import SynapseCompute, ComputeTarget

attach_config = SynapseCompute.attach_configuration(
        linked_service = linked_service,
        type="SynapseSpark",
        pool_name="spark01") # This name comes from your Synapse workspace

synapse_compute=ComputeTarget.attach(
        workspace=ws,
        name='link1-spark01',
        attach_configuration=attach_config)

synapse_compute.wait_for_completion()
```

첫 번째 단계는를 구성 하는 것입니다 `SynapseCompute` . `linked_service`인수는 `LinkedService` 이전 단계에서 만들었거나 검색 한 개체입니다. `type`인수는 여야 합니다 `SynapseSpark` . `pool_name`의 인수는 `SynapseCompute.attach_configuration()` Azure Synapse Analytics 작업 영역에 있는 기존 풀의 인수와 일치 해야 합니다. Azure Synapse Analytics 작업 영역에서 Apache spark 풀을 만드는 방법에 대 한 자세한 내용은 [빠른 시작: Synapse Studio를 사용 하 여 서버 리스 Apache Spark 풀 만들기](../synapse-analytics/quickstart-create-apache-spark-pool-studio.md)를 참조 하세요. `attach_config`의 형식은 `ComputeTargetAttachConfiguration`입니다.

구성이 만들어지면 `ComputeTarget` `Workspace` `ComputeTargetAttachConfiguration` machine learning 작업 영역 내에서 계산을 참조 하려는, 및 이름을 전달 하 여 기계 학습을 만듭니다. 에 대 한 호출은 `ComputeTarget.attach()` 비동기 이므로 샘플은 호출이 완료 될 때까지 차단 됩니다.

## <a name="create-a-synapsesparkstep-that-uses-the-linked-apache-spark-pool"></a>연결 된 `SynapseSparkStep` Apache Spark 풀을 사용 하는 만들기

데이터 `DatasetConsumptionConfig` 는 테이블 형식 데이터 또는 파일 집합을 포함할 수 있는 개체를 통해 기계 학습 파이프라인으로 흐릅니다. 데이터는 종종 작업 영역 데이터 저장소의 blob 저장소에 있는 파일에서 제공 됩니다. 다음 코드는 machine learning 파이프라인에 대 한 입력을 만들기 위한 몇 가지 일반적인 코드를 보여 줍니다.

```python
from azureml.core import Dataset

datastore = ws.get_default_datastore()
file_name = 'Titanic.csv'

titanic_tabular_dataset = Dataset.Tabular.from_delimited_files(path=[(datastore, file_name)])
step1_input1 = titanic_tabular_dataset.as_named_input("tabular_input")

# Example only: it wouldn't make sense to duplicate input data, especially one as tabular and the other as files
titanic_file_dataset = Dataset.File.from_files(path=[(datastore, file_name)])
step1_input2 = titanic_file_dataset.as_named_input("file_input").as_hdfs()
```

위의 코드는 파일이 `Titanic.csv` blob 저장소에 있다고 가정 합니다. 이 코드에서는 파일을 및로 읽는 방법을 보여 줍니다 `TabularDataset` `FileDataset` . 이 코드는 데모 목적 으로만 사용 됩니다 .이 코드는 중복 입력을 혼동 하거나 단일 데이터 원본을 테이블 포함 리소스 및 파일로 해석 하는 것과 같은 방식으로 사용 됩니다.

> [!Important]
> 을 입력으로 사용 하려면 `FileDataset` 버전이 이상 이어야 합니다 `azureml-core` `1.20.0` . 클래스를 사용 하 여이를 지정 하는 방법은 `Environment` 아래에 설명 되어 있습니다.

단계가 완료 되 면 다음과 유사한 코드를 사용 하 여 출력 데이터를 저장 하도록 선택할 수 있습니다.

```python
from azureml.data import HDFSOutputDatasetConfig
step1_output = HDFSOutputDatasetConfig(destination=(datastore,"test")).register_on_complete(name="registered_dataset")
```

이 경우 데이터는 `datastore` 이라는 파일에 저장 `test` 되며, 이름이 인로 machine learning 작업 영역 내에서 사용할 수 있습니다 `Dataset` `registered_dataset` .

파이프라인 단계에는 데이터 외에도 단계별 Python 종속성이 있을 수 있습니다. 개별 `SynapseSparkStep` 개체는 정확한 Azure Synapse Apache Spark 구성도 지정할 수 있습니다. 이는 패키지 버전이 이상 이어야 함을 지정 하는 다음 코드에 나와 `azureml-core` `1.20.0` 있습니다. 앞에서 설명한 것 처럼를 입력으로 사용 하려면에 대 한이 요구 사항이 `azureml-core` 필요 `FileDataset` 합니다.

```python
from azureml.core.environment import Environment
from azureml.pipeline.steps import SynapseSparkStep

env = Environment(name="myenv")
env.python.conda_dependencies.add_pip_package("azureml-core>=1.20.0")

step_1 = SynapseSparkStep(name = 'synapse-spark',
                          file = 'dataprep.py',
                          source_directory="./code", 
                          inputs=[step1_input1, step1_input2],
                          outputs=[step1_output],
                          arguments = ["--tabular_input", step1_input1, 
                                       "--file_input", step1_input2,
                                       "--output_dir", step1_output],
                          compute_target = 'link1-spark01',
                          driver_memory = "7g",
                          driver_cores = 4,
                          executor_memory = "7g",
                          executor_cores = 2,
                          num_executors = 1,
                          environment = env)
```

위의 코드는 Azure machine learning 파이프라인에서 단일 단계를 지정 합니다. 이 단계의 `environment` 는 특정 버전을 지정 `azureml-core` 하 고 필요에 따라 다른 conda 또는 pip 종속성을 추가할 수 있습니다.

는 `SynapseSparkStep` 로컬 컴퓨터에서 하위 디렉터리로 압축 하 고 업로드 합니다 `./code` . 계산 서버에서 해당 디렉터리가 다시 만들어지고 단계에서 해당 디렉터리의 파일을 실행 합니다 `dataprep.py` . `inputs` `outputs` 이 단계의 및는 앞에서 `step1_input1` 설명한, `step1_input2` 및 `step1_output` 개체입니다. 스크립트 내에서 이러한 값에 액세스 하는 가장 쉬운 방법은 `dataprep.py` 명명 된와 연결 하는 것입니다 `arguments` .

Apache Spark 생성자 컨트롤에 대 한 다음 인수 집합 `SynapseSparkStep` 입니다. 는 `compute_target` 이전에 `'link1-spark01'` 계산 대상으로 연결한입니다. 다른 매개 변수는 사용 하고자 하는 메모리 및 코어를 지정 합니다.

샘플 노트북은에 대해 다음 코드를 사용 합니다 `dataprep.py` .

```python
import os
import sys
import azureml.core
from pyspark.sql import SparkSession
from azureml.core import Run, Dataset

print(azureml.core.VERSION)
print(os.environ)

import argparse
parser = argparse.ArgumentParser()
parser.add_argument("--tabular_input")
parser.add_argument("--file_input")
parser.add_argument("--output_dir")
args = parser.parse_args()

# use dataset sdk to read tabular dataset
run_context = Run.get_context()
dataset = Dataset.get_by_id(run_context.experiment.workspace,id=args.tabular_input)
sdf = dataset.to_spark_dataframe()
sdf.show()

# use hdfs path to read file dataset
spark= SparkSession.builder.getOrCreate()
sdf = spark.read.option("header", "true").csv(args.file_input)
sdf.show()

sdf.coalesce(1).write\
.option("header", "true")\
.mode("append")\
.csv(args.output_dir)
```

이 "데이터 준비" 스크립트는 실제 데이터 변환을 수행 하지는 않지만 데이터를 검색 하 고, spark 데이터 프레임로 변환 하 고, 몇 가지 기본적인 Apache Spark 조작을 수행 하는 방법을 보여 줍니다. 다음 그림에 표시 된 것 처럼 자식 실행을 열고 출력 **+ 로그** 탭을 선택 하 고 파일을 열어 Azure Machine Learning Studio의 출력을 찾을 수 있습니다 `logs/azureml/driver/stdout` .

:::image type="content" source="media/how-to-use-synapsesparkstep/synapsesparkstep-stdout.png" alt-text="자식 실행의 stdout 탭을 보여 주는 스튜디오의 스크린샷":::

## <a name="use-the-synapsesparkstep-in-a-pipeline"></a>파이프라인에서를 사용 합니다. `SynapseSparkStep`

파이프라인의 다른 단계는 자신의 고유한 환경을 포함 하 고 작업에 적합 한 다양 한 계산 리소스에서 실행할 수 있습니다. 샘플 노트북은 작은 CPU 클러스터에서 "학습 단계"를 실행 합니다.

```python
from azureml.core.compute import AmlCompute

cpu_cluster_name = "cpucluster"

if cpu_cluster_name in ws.compute_targets:
    cpu_cluster = ComputeTarget(workspace=ws, name=cpu_cluster_name)
    print('Found existing cluster, use it.')
else:
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2', max_nodes=1)
    cpu_cluster = ComputeTarget.create(ws, cpu_cluster_name, compute_config)
    print('Allocating new CPU compute cluster')

cpu_cluster.wait_for_completion(show_output=True)

step2_input = step1_output.as_input("step2_input").as_download()

step_2 = PythonScriptStep(script_name="train.py",
                          arguments=[step2_input],
                          inputs=[step2_input],
                          compute_target=cpu_cluster_name,
                          source_directory="./code",
                          allow_reuse=False)
```

위의 코드는 필요한 경우 새 계산 리소스를 만듭니다. 그런 다음 `step1_output` 결과가 학습 단계에 대 한 입력으로 변환 됩니다. `as_download()`옵션은 데이터를 계산 리소스로 이동 하 여 더 빠르게 액세스할 수 있음을 의미 합니다. 데이터가 로컬 계산 하드 드라이브에 맞지 않는 경우에는 옵션을 사용 `as_mount()` 하 여 퓨즈 파일 시스템을 통해 데이터를 스트리밍할 수 있습니다. `compute_target`이 두 번째 단계의은 `'cpucluster'` `'link1-spark01'` 데이터 준비 단계에서 사용한 리소스가 아니라입니다. 이 단계에서는 `train.py` 이전 단계에서 사용한 대신 간단한 프로그램을 사용 `dataprep.py` 합니다. 샘플 노트북에서 세부 정보를 볼 수 있습니다 `train.py` .

모든 단계를 정의한 후 파이프라인을 만들고 실행할 수 있습니다. 

```python
from azureml.pipeline.core import Pipeline

pipeline = Pipeline(workspace=ws, steps=[step_1, step_2])
pipeline_run = pipeline.submit('synapse-pipeline', regenerate_outputs=True)
```

위의 코드는 Azure Synapse Analytics ( `step_1` ) 및 학습 단계 ()에 의해 구동 되는 Apache Spark 풀의 데이터 준비 단계로 구성 된 파이프라인을 만듭니다 `step_2` . Azure는 단계 간의 데이터 종속성을 검사 하 여 실행 그래프를 계산 합니다. 이 경우 반드시 필요한 단순한 종속성이 있습니다 `step2_input` `step1_output` .

에 대 한 호출을 통해 `pipeline.submit` 필요한 경우 실험을 만들고 `synapse-pipeline` 비동기적으로 실행을 시작 합니다. 파이프라인 내의 개별 단계는이 주 실행의 자식 실행으로 실행 되며 스튜디오의 실험 페이지에서 모니터링 및 검토할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [Machine learning 파이프라인 게시 및 추적](how-to-deploy-pipelines.md) 
* [Azure Machine Learning 모니터링](monitor-azure-machine-learning.md)
* [Python의 Azure Machine Learning 파이프라인에서 자동화 된 ML 사용](how-to-use-automlstep-in-pipelines.md)
