---
title: Azure Machine Learning 서비스를 사용하여 모델 교육의 계산 대상 설정 | Microsoft Docs
description: 기계 학습 모델을 교육하는 데 사용되는 교육 환경(계산 대상)을 선택하고 구성하는 방법을 알아봅니다. Azure Machine Learning 서비스를 통해 쉽게 교육 환경을 전환할 수 있습니다. 로컬로 교육을 시작하고, 규모 확장이 필요한 경우 클라우드 기반 계산 대상으로 전환하면 됩니다.
services: machine-learning
author: heatherbshapiro
ms.author: hshapiro
ms.reviewer: larryfr
manager: cgronlun
ms.service: machine-learning
ms.component: core
ms.topic: article
ms.date: 09/24/2018
ms.openlocfilehash: 30a1f2be1917ba6ea404a2862daaf5f51f35ac3f
ms.sourcegitcommit: b4a46897fa52b1e04dd31e30677023a29d9ee0d9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/17/2018
ms.locfileid: "49394887"
---
# <a name="select-and-use-a-compute-target-to-train-your-model"></a>모델을 교육하기 위한 계산 대상의 선택 및 사용

Azure Machine Learning 서비스를 통해 다양한 환경에서 모델을 교육할 수 있습니다. __계산 대상__이라고도 하는 이러한 환경은 로컬일 수도 있고 클라우드에 있을 수도 있습니다. 이 문서에서는 지원되는 계산 대상 및 사용 방법을 알아봅니다.

계산 대상은 웹 서비스로 배포될 때 교육 스크립트를 실행하거나 모델을 호스트하는 리소스입니다. Azure Machine Learning SDK 또는 CLI를 사용하여 만들고 관리할 수 있습니다. 다른 프로세스(예: Azure Portal 또는 Azure CLI)에서 만든 계산 대상이 있는 경우 해당 계산 대상을 Azure Machine Learning 서비스 작업 영역에 연결하여 사용할 수 있습니다.

처음에는 머신에서 로컬로 실행하고, 나중에 GPU 또는 Azure Batch AI를 사용하여 원격 데이터 과학 가상 머신 같은 다른 환경으로 확장 및 강화할 수 있습니다. 

>[!NOTE]
> 이 문서의 코드는 Azure Machine Learning SDK 버전 0.168에서 테스트되었습니다. 

## <a name="supported-compute-targets"></a>지원되는 계산 대상

Azure Machine Learning 서비스에서 지원하는 계산 대상은 다음과 같습니다.

|계산 대상| GPU 가속 | 자동화된 하이퍼 매개 변수 튜닝 | 자동화된 모델 선택 | 파이프라인에서 사용 가능|
|----|:----:|:----:|:----:|:----:|
|[로컬 컴퓨터](#local)| 가능할 수도 있음 | &nbsp; | ✓ | &nbsp; |
|[DSVM(Data Science Virtual Machine)](#dsvm) | ✓ | ✓ | ✓ | ✓ |
|[Azure Batch AI](#batch)| ✓ | ✓ | ✓ | ✓ | ✓ |
|[Azure HDInsight](#hdinsight)| &nbsp; | &nbsp; | &nbsp; | ✓ |

__[ACI(Azure Container Instances)](#aci)__ 를 사용하여 모델을 교육할 수도 있습니다. 비용이 저렴하고 쉽게 만들어서 사용할 수 있는 서버리스 클라우드 제품입니다. ACI는 GPU 가속, 자동화된 하이퍼 매개 변수 튜닝 또는 자동화된 모델 선택을 지원하지 않습니다. 또한 파이프라인에 사용할 수 없습니다.

계산 대상 간의 주요 차별화 요소는 다음과 같습니다.
* __GPU 가속__: GPU는 Data Science Virtual Machine 및 Azure Batch AI에 사용할 수 있습니다. 설치된 하드웨어, 드라이버 및 프레임워크에 따라 로컬 컴퓨터에서 GPU에 액세스할 수도 있습니다.
* __자동화된 하이퍼 매개 변수 튜닝__: Azure Machine Learning 자동화된 하이퍼 매개 변수 최적화는 모델에 가장 적합한 하이퍼 매개 변수를 찾는 데 도움을 줍니다.
* __자동화된 모델 선택__: 모델을 빌드할 때 Azure Machine Learning 서비스는 알고리즘 및 하이퍼 매개 변수를 선택하도록 지능적으로 권장할 수 있습니다. 자동화된 모델 선택을 사용하면 여러 조합을 수동으로 시도하는 것보다 더 빠르게 고품질 모델을 찾을 수 있습니다. 자세한 내용은 [자습서: Azure 자동화된 Machine Learning을 사용하여 자동으로 분류 모델 학습시키기](tutorial-auto-train-models.md) 문서를 참조하세요.
* __파이프라인__: Azure Machine Learning 서비스를 사용하면 학습 및 배포와 같은 다양한 작업을 파이프라인에 결합할 수 있습니다. 파이프라인은 병렬로 또는 순서대로 실행할 수 있으며, 신뢰할 수 있는 자동화 메커니즘을 제공합니다. 자세한 내용은 [Azure Machine Learning 서비스를 사용하여 기계 학습 파이프라인 빌드](concept-ml-pipelines.md) 문서를 참조하세요.

Azure Machine Learning SDK, Azure CLI 또는 Azure Portal을 사용하여 계산 대상을 만들 수 있습니다. 기존 계산 대상을 작업 영역에 추가(연결)하여 사용할 수도 있습니다.

> [!IMPORTANT]
> 기존 Azure 컨테이너 인스턴스는 작업 영역에 연결할 수 없습니다. 그 대신 새 인스턴스를 만들어야 합니다.
>
> 작업 영역 내에 Azure HDInsight 클러스터를 만들 수 없습니다. 그 대신 기존 클러스터를 연결해야 합니다.

## <a name="workflow"></a>워크플로

Azure Machine Learning을 사용하여 모델을 개발하고 배포하는 워크플로는 다음 단계를 따릅니다.

1. Python에서 기계 학습 교육 스크립트를 개발합니다.
1. 계산 대상을 만들고 구성하거나 기존 계산 대상을 연결합니다.
1. 교육 스크립트를 계산 대상에 제출합니다.
1. 결과를 검사하여 가장 적합한 모델을 찾습니다.
1. 모델을 모델 레지스트리에 등록합니다.
1. 모델을 배포합니다.

> [!IMPORTANT]
> 교육 스크립트는 특정 계산 대상에 연결되지 않습니다. 처음에는 로컬 컴퓨터에서 교육하고, 나중에 교육 스크립트를 다시 작성할 필요 없이 계산 대상을 전환할 수 있습니다.

한 계산 대상에서 다른 계산 대상으로 전환하려면 [실행 구성](concept-azure-machine-learning-architecture.md#run-configuration)을 만들어야 합니다. 실행 구성은 계산 대상에서 스크립트를 실행하는 방법을 정의합니다.

## <a name="training-scripts"></a>교육 스크립트

교육 실행을 시작하면 교육 스크립트를 포함하고 있는 전체 디렉터리가 제출됩니다. 스냅숏이 생성되어 계산 대상으로 전송됩니다. 자세한 내용은 [스냅숏](concept-azure-machine-learning-architecture.md#snapshot)을 참조하세요.

## <a id="local"></a>로컬 컴퓨터

로컬로 교육하는 경우 SDK를 사용하여 교육 작업을 제출합니다. 사용자 관리 또는 시스템 관리 환경을 사용하여 교육할 수 있습니다.

### <a name="user-managed-environment"></a>사용자 관리 환경

사용자 관리 환경의 경우 사용자는 스크립트를 실행하기로 선택하는 Python 환경에 모든 필수 패키지를 제공할 책임이 있습니다. 다음 코드 조각은 사용자 관리 환경의 교육을 구성하는 예제입니다.

```python
from azureml.core.runconfig import RunConfiguration

# Editing a run configuration property on-fly.
run_config_user_managed = RunConfiguration()

run_config_user_managed.environment.python.user_managed_dependencies = True

# You can choose a specific Python environment by pointing to a Python path 
#run_config.environment.python.interpreter_path = '/home/ninghai/miniconda3/envs/sdk2/bin/python'
```

사용자 관리 환경에서 학습을 보여주는 Jupyter Notebook은 [https://github.com/Azure/MachineLearningNotebooks/blob/master/01.getting-started/02.train-on-local/02.train-on-local.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/01.getting-started/02.train-on-local/02.train-on-local.ipynb)을 참조하세요.
  
### <a name="system-managed-environment"></a>시스템 관리 환경

시스템 관리 환경은 conda를 사용하여 종속성을 관리합니다. Conda는 종속성 목록을 포함하는 `conda_dependencies.yml` 파일을 만듭니다. 그러면 사용자는 시스템에 새 conda 환경을 빌드하고 스크립트를 실행하라고 요청할 수 있습니다. `conda_dependencies.yml` 파일이 변경되지 않고 그대로 유지되는 한, 나중에 시스템 관리 환경을 다시 사용할 수 있습니다. 

필요한 종속성의 크기에 따라 새 환경의 초기 설정을 완료하는 데 몇 분 정도 걸릴 수 있습니다. 다음 코드 조각은 scikit-learn을 사용하는 시스템 관리 환경을 만드는 방법을 보여줍니다.

```python
from azureml.core.runconfig import RunConfiguration
from azureml.core.conda_dependencies import CondaDependencies

run_config_system_managed = RunConfiguration()

run_config_system_managed.environment.python.user_managed_dependencies = False
run_config_system_managed.auto_prepare_environment = True

# Specify conda dependencies with scikit-learn

run_config_system_managed.environment.python.conda_dependencies = CondaDependencies.create(conda_packages=['scikit-learn'])
```

시스템 관리 환경에서 학습을 보여주는 Jupyter Notebook은 [https://github.com/Azure/MachineLearningNotebooks/blob/master/01.getting-started/02.train-on-local/02.train-on-local.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/01.getting-started/02.train-on-local/02.train-on-local.ipynb)을 참조하세요.

## <a id="dsvm"></a>Data Science Virtual Machine

로컬 머신에 모델을 교육하는 데 필요한 계산 또는 GPU 리소스가 없는 경우가 있습니다. 이 경우 DSVM(Data Science Virtual Machine) 같은 계산 대상을 추가하여 교육 프로세스를 강화 또는 규모 확장할 수 있습니다.

> [!WARNING]
> Azure Machine Learning은 Ubuntu를 실행하는 가상 머신만 지원합니다. 가상 머신을 만들거나 기존 가상 머신을 선택할 때 Ubuntu를 사용하는 가상 머신을 선택해야 합니다.

다음은 SDK를 사용하여 DSVM(Data Science Virtual Machine)을 교육 대상으로 구성하는 단계입니다.

1. Virtual Machine 만들기 또는 연결
    
    * 새 DSVM을 만들려면, 먼저 새 VM을 만들지 않는 경우 동일한 이름의 DSVM이 있는지 확인합니다.
    
        ```python
        from azureml.core.compute import DsvmCompute
        from azureml.core.compute_target import ComputeTargetException

        compute_target_name = 'mydsvm'

        try:
            dsvm_compute = DsvmCompute(workspace = ws, name = compute_target_name)
            print('found existing:', dsvm_compute.name)
        except ComputeTargetException:
            print('creating new.')
            dsvm_config = DsvmCompute.provisioning_configuration(vm_size = "Standard_D2_v2")
            dsvm_compute = DsvmCompute.create(ws, name = compute_target_name, provisioning_configuration = dsvm_config)
            dsvm_compute.wait_for_completion(show_output = True)
        ```
    * 기존 가상 머신을 계산 대상으로 연결하려면 가상 머신의 정규화된 도메인 이름, 로그인 이름 및 암호를 입력해야 합니다.  이 예제에서는 ```<fqdn>```을 VM의 공용 정규화된 도메인 이름 또는 공용 IP 주소로 바꿉니다. ```<username>``` 및 ```<password>```를 VM의 SSH 사용자 및 암호로 바꿉니다.

        ```python
        from azureml.core.compute import RemoteCompute

        dsvm_compute = RemoteCompute.attach(ws,
                                        name="attach-dsvm",
                                        username='<username>',
                                        address="<fqdn>",
                                        ssh_port=22,
                                        password="<password>")

        dsvm_compute.wait_for_completion(show_output=True)
    
   It takes around 5 minutes to create the DSVM instance.

1. Create a configuration for the DSVM compute target. Docker and conda are used to create and configure the training environment on DSVM:

    ```python
    from azureml.core.runconfig import RunConfiguration
    from azureml.core.conda_dependencies import CondaDependencies

    # Load the "cpu-dsvm.runconfig" file (created by the above attach operation) in memory
    run_config = RunConfiguration(framework = "python")

    # Set compute target to the Linux DSVM
    run_config.target = compute_target_name

    # Use Docker in the remote VM
    run_config.environment.docker.enabled = True

    # Use CPU base image
    run_config.environment.docker.base_image = azureml.core.runconfig.DEFAULT_CPU_IMAGE
    print('Base Docker image is:', run_config.environment.docker.base_image)

    # Ask system to provision a new one based on the conda_dependencies.yml file
    run_config.environment.python.user_managed_dependencies = False

    # Prepare the Docker and conda environment automatically when used the first time.
    run_config.prepare_environment = True

    # specify CondaDependencies obj
    run_config.environment.python.conda_dependencies = CondaDependencies.create(conda_packages=['scikit-learn'])

    ```

1. 작업을 마친 후 계산 리소스를 삭제하려면 다음 코드를 사용합니다.

    ```python
    dsvm_compute.delete()
    ```

Data Science Virtual Machine에서 학습을 보여주는 Jupyter Notebook은 [https://github.com/Azure/MachineLearningNotebooks/blob/master/01.getting-started/04.train-on-remote-vm/04.train-on-remote-vm.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/01.getting-started/04.train-on-remote-vm/04.train-on-remote-vm.ipynb)을 참조하세요.

## <a id="batch"></a>Azure Batch AI

모델을 교육하는 시간이 오래 걸리는 경우 Azure Batch AI를 사용하여 클라우드의 계산 리소스 클러스터에 교육을 분산할 수 있습니다. Batch AI는 GPU 리소스를 사용하도록 구성할 수도 있습니다.

다음 예제는 이름으로 기존 Batch AI 클러스터를 찾습니다. 없으면 자동으로 하나가 생성됩니다.

```python
from azureml.core.compute import BatchAiCompute
from azureml.core.compute import ComputeTarget
import os

# choose a name for your cluster
batchai_cluster_name = os.environ.get("BATCHAI_CLUSTER_NAME", ws.name + "gpu")
cluster_min_nodes = os.environ.get("BATCHAI_CLUSTER_MIN_NODES", 1)
cluster_max_nodes = os.environ.get("BATCHAI_CLUSTER_MAX_NODES", 3)
vm_size = os.environ.get("BATCHAI_CLUSTER_SKU", "STANDARD_NC6")
autoscale_enabled = os.environ.get("BATCHAI_CLUSTER_AUTOSCALE_ENABLED", True)


if batchai_cluster_name in ws.compute_targets():
    compute_target = ws.compute_targets()[batchai_cluster_name]
    if compute_target and type(compute_target) is BatchAiCompute:
        print('found compute target. just use it. ' + batchai_cluster_name)
else:
    print('creating a new compute target...')
    provisioning_config = BatchAiCompute.provisioning_configuration(vm_size = vm_size, # NC6 is GPU-enabled
                                                                vm_priority = 'lowpriority', # optional
                                                                autoscale_enabled = autoscale_enabled,
                                                                cluster_min_nodes = cluster_min_nodes, 
                                                                cluster_max_nodes = cluster_max_nodes)

    # create the cluster
    compute_target = ComputeTarget.create(ws, batchai_cluster_name, provisioning_config)
    
    # can poll for a minimum number of nodes and for a specific timeout. 
    # if no min node count is provided it will use the scale settings for the cluster
    compute_target.wait_for_completion(show_output=True, min_node_count=None, timeout_in_minutes=20)
    
     # For a more detailed view of current BatchAI cluster status, use the 'status' property    
    print(compute_target.status.serialize())
```

기존 Batch AI 클러스터를 계산 대상으로 연결하려면 Azure 리소스 ID를 입력해야 합니다. Azure Portal에서 리소스 ID를 가져오려면 다음 단계를 사용하세요.
1. **모든 서비스** 아래에서 `Batch AI` 서비스 검색
1. 클러스터가 소속된 작업 영역 이름을 클릭
1. 클러스터 선택
1. **속성** 클릭
1. **ID** 복사

다음 예제에서는 SDK를 사용하여 작업 영역에 클러스터를 연결합니다. 예제에서 `<name>`을 계산 이름으로 바꿉니다. 이 이름은 클러스터 이름과 일치하지 않아도 됩니다. `<resource-id>`를 위에서 설명한 Azure 리소스 ID로 바꿉니다.

```python
from azureml.core.compute import BatchAiCompute
BatchAiCompute.attach(workspace=ws,
                      name=<name>,
                      resource_id=<resource-id>)
```

또한 다음 Azure CLI 명령을 사용하여 Batch AI 클러스터 및 작업 상태를 확인할 수 있습니다.

- 클러스터 상태 확인. `az batchai cluster list`를 사용하여 노드가 몇 개나 실행 중인지 확인할 수 있습니다.
- 작업 상태 확인. `az batchai job list`를 사용하여 작업이 몇 개나 실행 중인지 확인할 수 있습니다.

Batch AI 클러스터를 만드는 데 약 5분이 걸립니다.

Batch AI 클러스터에서 학습을 보여주는 Jupyter Notebook은 [https://github.com/Azure/MachineLearningNotebooks/blob/master/training/03.train-hyperparameter-tune-deploy-with-tensorflow/03.train-hyperparameter-tune-deploy-with-tensorflow.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/training/03.train-hyperparameter-tune-deploy-with-tensorflow/03.train-hyperparameter-tune-deploy-with-tensorflow.ipynb)을 참조하세요.

## <a name='aci'></a>ACI(Azure Container Instance)

Azure Container Instances는 시작 시간이 빠르고 사용자가 Virtual Machines를 관리할 필요가 없는 격리된 컨테이너입니다. Azure Machine Learning 서비스는 westus, eastus, westeurope, northeurope, westus2 및 southeastasia 지역에서 사용할 수 있는 Linux 컨테이너를 사용합니다. 자세한 내용은 [지역 가용성](https://docs.microsoft.com/azure/container-instances/container-instances-quotas#region-availability)을 참조하세요. 

다음 예제는 SDK를 사용하여 ACI 계산 대상을 만들고 모델 교육에 사용하는 방법을 보여줍니다. 

```python
from azureml.core.runconfig import RunConfiguration
from azureml.core.conda_dependencies import CondaDependencies

# create a new runconfig object
run_config = RunConfiguration()

# signal that you want to use ACI to run script.
run_config.target = "containerinstance"

# ACI container group is only supported in certain regions, which can be different than the region the Workspace is in.
run_config.container_instance.region = 'eastus'

# set the ACI CPU and Memory 
run_config.container_instance.cpu_cores = 1
run_config.container_instance.memory_gb = 2

# enable Docker 
run_config.environment.docker.enabled = True

# set Docker base image to the default CPU-based image
run_config.environment.docker.base_image = azureml.core.runconfig.DEFAULT_CPU_IMAGE
#run_config.environment.docker.base_image = 'microsoft/mmlspark:plus-0.9.9'

# use conda_dependencies.yml to create a conda environment in the Docker image
run_config.environment.python.user_managed_dependencies = False

# auto-prepare the Docker image when used for the first time (if it is not already prepared)
run_config.auto_prepare_environment = True

# specify CondaDependencies obj
run_config.environment.python.conda_dependencies = CondaDependencies.create(conda_packages=['scikit-learn'])
```

ACI 계산 대상을 만드는 데 몇 초에서 몇 분까지 걸릴 수 있습니다.

Azure Container Instances에서 학습을 보여주는 Jupyter Notebook은 [https://github.com/Azure/MachineLearningNotebooks/blob/master/01.getting-started/03.train-on-aci/03.train-on-aci.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/01.getting-started/03.train-on-aci/03.train-on-aci.ipynb)을 참조하세요.

## <a id="hdinsight"></a>HDInsight 클러스터 연결 

HDInsight는 인기 있는 빅 데이터 분석용 플랫폼입니다. 모델을 교육하는 데 사용할 수 있는 Apache Spark를 제공합니다.

> [!IMPORTANT]
> 모델 교육에 사용하려면 먼저 HDInsight 클러스터를 만들어야 합니다. HDInsight 클러스터에서 Spark를 만들려면 [HDInsight에서 Spark 클러스터 만들기](https://docs.microsoft.com/azure/hdinsight/spark/apache-spark-jupyter-spark-sql) 문서를 참조하세요.
>
> 클러스터를 만들 때 SSH 사용자 이름 및 암호를 지정해야 합니다. 이러한 값은 HDInsight를 계산 대상으로 사용할 때 필요하므로 잘 적어 두세요.
>
> 생성된 클러스터는 FQDN(정규화된 도메인 이름) `<clustername>.azurehdinsight.net`을 갖게 되고, 여기서 `<clustername>`은 클러스터에 대해 제공한 이름입니다. 클러스터를 계산 대상으로 사용하려면 이 주소(또는 클러스터의 공용 IP 주소)가 필요합니다.

HDInsight를 계산 대상으로 구성하려면 HDInsight 클러스터의 정규화된 도메인 이름, 클러스터 로그인 이름 및 암호를 입력해야 합니다. 다음 예제에서는 SDK를 사용하여 작업 영역에 클러스터를 연결합니다. 이 예제에서는 `<fqdn>`을 클러스터의 공용 정규화된 도메인 이름 또는 공용 IP 주소로 바꿉니다. `<username>` 및 `<password>`를 클러스터의 SSH 사용자 및 암호로 바꿉니다.

> [!NOTE]
> 클러스터의 FQDN을 찾으려면 Azure Portal을 방문하여 HDInsight 클러스터를 선택합니다. __개요__ 섹션에서 FQDN은 __URL__ 항목의 일부입니다. 값의 시작 부분에서 `https://`만 제거합니다.
>
> ![URL 항목이 강조 표시된 HDInsight 클러스터 개요의 스크린샷](./media/how-to-set-up-training-targets/hdinsight-overview.png)

```python
from azureml.core.compute import HDInsightCompute

try:
    # Attaches a HDInsight cluster as a compute target.
    HDInsightCompute.attach(ws,name = "myhdi",
                            address = "<fqdn>",
                            username = "<username>",
                            password = "<password>")
except UserErrorException as e:
    print("Caught = {}".format(e.message))
    print("Compute config already attached.")

# Configure HDInsight run
# load the runconfig object from the "myhdi.runconfig" file generated by the attach operaton above.
run_config = RunConfiguration.load(project_object = project, run_config_name = 'myhdi')

# ask system to prepare the conda environment automatically when used for the first time
run_config.auto_prepare_environment = True
```

## <a name="submit-training-run"></a>교육 실행 제출
    
교육 실행을 제출하는 코드는 계산 대상에 관계 없이 동일합니다.

* 계산 대상에 대한 실행 구성을 사용하여 `ScriptRunConfig` 개체를 만듭니다.
* 실행을 제출합니다.
* 실행이 완료될 때까지 기다립니다.

다음 예제에서는 이 문서의 앞부분에서 만든 시스템 관리 로컬 계산 대상을 위한 구성을 사용합니다.

```pyghon
src = ScriptRunConfig(source_directory = script_folder, script = 'train.py', run_config = run_config_system_managed)
run = exp.submit(src)
run.wait_for_completion(show_output = True)
```

HDInsight의 Spark를 사용한 학습을 보여주는 Jupyter Notebook은 [https://github.com/Azure/MachineLearningNotebooks/blob/master/01.getting-started/05.train-in-spark/05.train-in-spark.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/01.getting-started/05.train-in-spark/05.train-in-spark.ipynb)을 참조하세요.

## <a name="view-and-set-up-compute-using-the-azure-portal"></a>Azure Portal을 사용하여 계산을 살펴보고 설정

Azure Portal에서 작업 영역과 연결된 계산 대상을 살펴볼 수 있습니다. 목록으로 이동하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com)을 방문하여 작업 영역으로 이동합니다.
2. __응용 프로그램__ 섹션 아래에서 __계산__ 링크를 클릭합니다.

    ![[계산 보기] 탭](./media/how-to-set-up-training-targets/azure-machine-learning-service-workspace.png)

### <a name="create-a-compute-target"></a>계산 대상 만들기

위의 단계에 따라 계산 대상 목록을 살펴본 후, 다음 단계에 따라 계산 대상을 만듭니다.

1. __+__ 기호를 클릭하여 계산 대상을 추가합니다.

    ![계산 추가 ](./media/how-to-set-up-training-targets/add-compute-target.png)

1. 계산 대상의 이름을 입력합니다.
1. __교육__용으로 연결할 계산 유형을 선택합니다. 
1. __새로 만들기__를 선택하고 필수 양식을 작성합니다. 
1. __만들기__
1. 목록에서 계산 대상을 선택하여 만들기 작업의 상태를 볼 수 있습니다.

    ![계산 목록 보기](./media/how-to-set-up-training-targets/View_list.png) 그 후 해당 계산의 세부 정보가 표시됩니다.
    ![세부 정보 보기](./media/how-to-set-up-training-targets/vm_view.PNG)
1. 이제 위에서 설명한 것처럼 이러한 대상에 대한 실행을 제출할 수 있습니다.

### <a name="reuse-existing-compute-in-your-workspace"></a>작업 영역에서 기존 계산을 다시 사용

위의 단계에 따라 계산 대상 목록을 살펴본 후, 다음 단계에 따라 계산 대상을 다시 사용합니다.

1. **+** 기호를 클릭하여 계산 대상을 추가합니다.
2. 계산 대상의 이름을 입력합니다.
3. 교육용으로 연결할 계산 유형을 선택합니다. Batch AI 및 Virtual Machines는 현재 포털에서 교육용으로 지원됩니다.
4. '기존 항목 사용'을 선택합니다.
    - Batch AI 클러스터를 연결할 때 드롭다운에서 계산 대상을 선택하고, Batch AI 작업 영역 및 Batch AI 클러스터를 선택하고, **만들기**를 클릭합니다.
    - Virtual Machine을 연결할 때 IP 주소, 사용자 이름/암호 조합, 개인/공개 키 및 포트를 입력하고 [만들기]를 클릭합니다.

    > [!NOTE]
    > Microsoft에서는 암호보다 안전한 SSH 키를 권장합니다. 암호는 무차별 암호 대입 공격(brute force attack)에 취약한 반면, SSH 키는 암호화 서명을 사용합니다. Azure Virtual Machines에 사용할 SSH 키를 만드는 방법은 다음 문서를 참조하세요.
    >
    > * [Linux 또는 macOS에서 SSH 키를 만들고 사용]( https://docs.microsoft.com/azure/virtual-machines/linux/mac-create-ssh-keys)
    > * [Windows에서 SSH 키를 만들고 사용]( https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows)

5. 계산 목록에서 계산 대상을 선택하여 프로비전 상태를 볼 수 있습니다.
6. 이제 이러한 대상에 대한 실행을 제출할 수 있습니다.

## <a name="examples"></a>예
이 문서의 개념을 보여 주는 노트북은 다음과 같습니다.
* [01.getting-started/02.train-on-local/02.train-on-local.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/01.getting-started/02.train-on-local)
* [01.getting-started/04.train-on-remote-vm/04.train-on-remote-vm.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/01.getting-started/04.train-on-remote-vm)
* [01.getting-started/03.train-on-aci/03.train-on-aci.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/01.getting-started/03.train-on-aci)
* [01.getting-started/05.train-in-spark/05.train-in-spark.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/01.getting-started/05.train-in-spark)
* [tutorials/01.train-models.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/tutorials/01.train-models.ipynb)

노트북을 가져오려면 다음을 수행합니다. [!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>다음 단계

* [Azure Machine Learning SDK 참조](http://aka.ms/aml-sdk)
* [자습서: 모델 교육](tutorial-train-models-with-aml.md)
* [모델 배포 위치](how-to-deploy-and-where.md)
* [Azure Machine Learning 서비스를 사용하여 기계 학습 파이프라인 빌드](concept-ml-pipelines.md)
