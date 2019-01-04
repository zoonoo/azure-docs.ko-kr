---
title: 모델 학습의 컴퓨팅 대상
titleSuffix: Azure Machine Learning service
description: 기계 학습 모델 학습의 학습 환경(컴퓨팅 대상)을 구성합니다. 학습 환경을 쉽게 전환할 수 있습니다. 로컬로 교육을 시작하고, 규모 확장이 필요한 경우 클라우드 기반 계산 대상으로 전환하면 됩니다. Databricks
services: machine-learning
author: heatherbshapiro
ms.author: hshapiro
ms.reviewer: larryfr
manager: cgronlun
ms.service: machine-learning
ms.component: core
ms.topic: article
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: 664d56daf3e70e2e5699d0c07331c466c60e06c5
ms.sourcegitcommit: edacc2024b78d9c7450aaf7c50095807acf25fb6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/13/2018
ms.locfileid: "53338865"
---
# <a name="set-up-compute-targets-for-model-training"></a>모델 학습의 컴퓨팅 대상 설정

Azure Machine Learning Service를 통해 다양한 컴퓨팅 리소스에서 모델을 학습시킬 수 있습니다. __컴퓨팅 대상__이라는 이러한 컴퓨팅 리소스는 로컬일 수도 있고 클라우드에 있을 수도 있습니다. 이 문서에서는 지원되는 계산 대상 및 사용 방법을 알아봅니다.

컴퓨팅 대상은 학습 스크립트가 실행되거나 웹 서비스로 배포 시 모델이 호스트되는 리소스입니다. Azure Machine Learning SDK, Azure Portal 또는 Azure CLI를 사용하여 컴퓨팅 대상을 만들고 관리할 수 있습니다. 다른 서비스(예: HDInsight 클러스터)를 통해 만든 컴퓨팅 대상이 있는 경우 해당 컴퓨팅 대상을 Azure Machine Learning Service 작업 영역에 연결하여 사용할 수 있습니다.

Azure Machine Learning이 지원하는 컴퓨팅 대상의 세 가지 광범위한 범주는 다음과 같습니다.

* __로컬__: 개발/실험 환경으로 사용하는 로컬 머신 또는 클라우드 기반 VM입니다. 

* __관리형 컴퓨팅__: Azure Machine Learning 컴퓨팅은 Azure Machine Learning Service에서 관리되는 컴퓨팅 제품입니다. 이를 통해 학습, 테스트 및 일괄 유추를 위한 단일 또는 다중 노드 컴퓨팅을 쉽게 만들 수 있습니다.

* __연결된 컴퓨팅__: 자신만의 Azure 클라우드 컴퓨팅을 가져와서 Azure Machine Learning에 연결할 수도 있습니다. 지원되는 컴퓨팅 유형 및 이를 사용하는 방법에 대한 자세한 내용은 다음을 참조하세요.


## <a name="supported-compute-targets"></a>지원되는 계산 대상

Azure Machine Learning Service에는 다양한 컴퓨팅 대상에 대한 다양한 지원이 있습니다. 일반적인 모델 개발 수명 주기는 작은 양의 데이터에 대한 개발/실험으로 시작합니다. 이 단계에서는 로컬 환경을 사용하는 것이 좋습니다. 예를 들어 로컬 컴퓨터 또는 클라우드 기반 VM입니다. 더 큰 데이터 세트를 기반으로 학습을 확장하거나 분산 학습을 수행할 경우 Azure Machine Learning 컴퓨팅을 사용하여 실행을 제출할 때마다 자동 크기 조정되는 단일 또는 다중 노드 클러스터를 만드는 것이 좋습니다. 다음 설명대로 다양한 시나리오 지원이 달라질 수는 있지만 고유한 컴퓨팅 리소스를 연결할 수도 있습니다.

|계산 대상| GPU 가속 | 자동화된 하이퍼 매개 변수 튜닝 | 자동화된 기계 학습 | 친숙한 파이프라인|
|----|:----:|:----:|:----:|:----:|
|[로컬 컴퓨터](#local)| 가능할 수도 있음 | &nbsp; | ✓ | &nbsp; |
|[Azure Machine Learning 컴퓨팅](#amlcompute)| ✓ | ✓ | ✓ | ✓ |
|[원격 VM](#vm) | ✓ | ✓ | ✓ | ✓ |
|[Azure Databricks](#databricks)| &nbsp; | &nbsp; | ✓ | ✓[*](#pipeline-only) |
|[Azure 데이터 레이크 분석](#adla)| &nbsp; | &nbsp; | &nbsp; | ✓[*](#pipeline-only) |
|[Azure HDInsight](#hdinsight)| &nbsp; | &nbsp; | &nbsp; | ✓ |

> [!IMPORTANT]
> <a id="pipeline-only"></a>__*__ Azure Databricks 및 Azure Data Lake Analytics는 파이프라인에서__만__ 사용할 수 있습니다. 파이프라인에 대한 자세한 내용은 [Azure Machine Learning의 파이프라인](concept-ml-pipelines.md) 문서를 참조하세요.

> [!IMPORTANT]
> Azure Machine Learning 컴퓨팅은 작업 영역 내에서 만들어야 합니다. 기존 인스턴스는 작업 영역에 연결할 수 없습니다.
>
> 다른 컴퓨팅 대상은 Azure Machine Learning 외부에서 만든 후 작업 영역에 연결해야 합니다.

> [!NOTE]
> 일부 컴퓨팅 대상은 모델을 학습시킬 때 Docker 컨테이너 이미지를 사용합니다. GPU 기본 이미지는 Microsoft Azure Services에서만 사용해야 합니다. 모델 학습의 경우 이러한 서비스는 다음과 같습니다.
>
> * Azure Machine Learning 컴퓨팅
> * Azure Kubernetes Service
> * Data Science Virtual Machine.

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

> [!TIP]
> 컴퓨팅 대상을 작업 영역과 연결할 때마다 관리형 컴퓨팅을 만들거나 기존 컴퓨팅을 연결하여 컴퓨팅에 이름을 지정해야 합니다. 이 이름의 길이는 2~16자여야 합니다.

한 계산 대상에서 다른 계산 대상으로 전환하려면 [실행 구성](concept-azure-machine-learning-architecture.md#run-configuration)을 만들어야 합니다. 실행 구성은 계산 대상에서 스크립트를 실행하는 방법을 정의합니다.

## <a name="training-scripts"></a>교육 스크립트

학습 실행을 시작하면 학습 스크립트가 포함된 디렉터리의 스냅숏이 생성되어 컴퓨팅 대상으로 전송됩니다. 자세한 내용은 [스냅숏](concept-azure-machine-learning-architecture.md#snapshot)을 참조하세요.

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

## <a id="amlcompute"></a>Azure Machine Learning 컴퓨팅

Azure Machine Learning 컴퓨팅은 단일 또는 다중 노드 컴퓨팅을 쉽게 만들 수 있는 관리형 컴퓨팅 인프라입니다. 이는 __작업 영역 지역 내__에서 만들어지고 작업 영역에서 다른 사용자와 공유할 수 있는 리소스입니다. 작업이 제출될 때 자동으로 강화되고 Azure Virtual Network에 포함할 수 있습니다. __컨테이너화된 환경__에서 실행되어 모델 종속성을 Docker 컨테이너로 패키지합니다.

Azure Machine Learning 컴퓨팅을 사용하여 클라우드의 CPU 또는 GPU 컴퓨팅 노드 클러스터에 학습 프로세스를 배포할 수 있습니다. GPU를 포함하는 VM 크기에 대한 자세한 내용은 [GPU 최적화 가상 머신 크기](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-gpu) 설명서를 참조하세요.

> [!NOTE]
> Azure Machine Learning 컴퓨팅에는 할당할 수 있는 코어 수 같은 항목에 대한 기본 한도가 있습니다. 자세한 내용은 [Azure 리소스에 대한 할당량 관리 및 요청](https://docs.microsoft.com/azure/machine-learning/service/how-to-manage-quotas) 문서를 참조하세요.

실행 예약 시 주문형으로 또는 영구적 리소스로 Azure Machine Learning 컴퓨팅을 요청할 수 있습니다.

### <a name="run-based-creation"></a>실행 기반 만들기

Azure Machine Learning 컴퓨팅을 런타임에 컴퓨팅 대상으로 만들 수 있습니다. 이 경우에는 컴퓨팅이 실행에 대해 자동으로 생성되고, 실행 구성에 지정하는 max_nodes로 강화된 다음, 실행이 완료된 후 __자동으로 삭제__됩니다.

> [!IMPORTANT]
> Azure Machine Learning 컴퓨팅의 실행 기반 만들기는 현재 미리 보기 상태로 제공됩니다. 하이퍼 매개 변수 조정 또는 자동화된 기계 학습을 사용 중인 경우에는 실행 기반 만들기를 사용하지 마세요. 하이퍼 매개 변수 조정 또는 자동화된 기계 학습을 사용해야 하는 경우에는 실행을 제출하기 전에 Azure Machine Learning 컴퓨팅을 만듭니다.

```python
from azureml.core.compute import ComputeTarget, AmlCompute

#Let us first list the supported VM families for Azure Machine Learning Compute
AmlCompute.supported_vmsizes()

from azureml.core.runconfig import RunConfiguration

# create a new runconfig object
run_config = RunConfiguration()

# signal that you want to use AmlCompute to execute script.
run_config.target = "amlcompute"

# AmlCompute will be created in the same region as workspace. Set vm size for AmlCompute from the list returned above
run_config.amlcompute.vm_size = 'STANDARD_D2_V2'

```

### <a name="persistent-compute-basic"></a>영구적 컴퓨팅(기본)

영구적 Azure Machine Learning 컴퓨팅은 여러 작업에서 다시 사용할 수 있습니다. 이 컴퓨팅은 작업 영역의 다른 사용자와 공유할 수 있고 작업 간에 유지됩니다.

영구적 Azure Machine Learning 컴퓨팅 리소스를 만들려면 `vm_size` 및 `max_nodes` 매개 변수를 지정합니다. 그런 다음, Azure Machine Learning은 나머지 매개 변수에 스마트 기본값을 사용합니다.  예를 들어 컴퓨팅은 사용되지 않을 때 0 노드로 자동 크기 축소되고 필요에 따라 작업을 실행할 전용 VM을 만들도록 설정됩니다. 

* **vm_size**: Azure Machine Learning 컴퓨팅에서 만든 노드의 VM 패밀리입니다.
* **max_nodes**: Azure Machine Learning 컴퓨팅에서 작업을 실행하는 동안 자동 크기 조정할 최대 노드 수입니다.

```python
from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException

# Choose a name for your CPU cluster
cpu_cluster_name = "cpucluster"

# Verify that cluster does not exist already
try:
    cpu_cluster = ComputeTarget(workspace=ws, name=cpu_cluster_name)
    print('Found existing cluster, use it.')
except ComputeTargetException:
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2',
                                                           max_nodes=4)
    cpu_cluster = ComputeTarget.create(ws, cpu_cluster_name, compute_config)

cpu_cluster.wait_for_completion(show_output=True)

```

### <a name="persistent-compute-advanced"></a>영구적 컴퓨팅(고급)

Azure Machine Learning 컴퓨팅을 만들 때 여러 고급 속성을 구성할 수도 있습니다.  이러한 속성을 사용하면 고정 크기로 또는 구독의 기존 Azure Virtual Network 내에서 영구적 클러스터를 만들 수 있습니다.

`vm_size` 및 `max_nodes` 외에도 다음 속성을 사용할 수 있습니다.

* **min_nodes**: Azure Machine Learning 컴퓨팅에서 작업을 실행하는 동안 크기를 축소할 최소 노드 수(기본값 0 노드)입니다.
* **vm_priority**: Azure Machine Learning 컴퓨팅을 만들 때 ‘dedicated’(기본값) 및 ‘lowpriority’ VM 중에서 선택합니다. 낮은 우선 순위 VM은 Azure의 초과 용량을 사용하므로 비용이 저렴하지만 실행이 선점되는 위험이 있습니다.
* **idle_seconds_before_scaledown**: min_nodes로 자동 크기 조정하기 전에 실행 완료 후까지 대기할 유휴 시간(기본값 120초)입니다.
* **vnet_resourcegroup_name**: __기존__ 가상 네트워크의 리소스 그룹입니다. Azure Machine Learning 컴퓨팅은 이 가상 네트워크 내에서 만들어집니다.
* **vnet_name**: 가상 네트워크 이름입니다. 가상 네트워크는 Azure Machine Learning 작업 영역과 동일한 지역에 있어야 합니다.
* **subnet_name**: 가상 네트워크 내에 있는 서브넷 이름입니다. Azure Machine Learning 컴퓨팅 리소스에 서브넷 범위의 IP 주소가 할당됩니다.

> [!TIP]
> 영구적 Azure Machine Learning 컴퓨팅 리소스를 만들 때 min_nodes 또는 max_nodes와 같은 속성을 업데이트할 수도 있습니다. `update()` 함수를 호출하면 됩니다.

```python
from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException

# Choose a name for your CPU cluster
cpu_cluster_name = "cpucluster"

# Verify that cluster does not exist already
try:
    cpu_cluster = ComputeTarget(workspace=ws, name=cpu_cluster_name)
    print('Found existing cluster, use it.')
except ComputeTargetException:
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2',
                                                           vm_priority='lowpriority',
                                                           min_nodes=2,
                                                           max_nodes=4,
                                                           idle_seconds_before_scaledown='300',
                                                           vnet_resourcegroup_name='<my-resource-group>',
                                                           vnet_name='<my-vnet-name>',
                                                           subnet_name='<my-subnet-name>')
    cpu_cluster = ComputeTarget.create(ws, cpu_cluster_name, compute_config)

cpu_cluster.wait_for_completion(show_output=True)

```


## <a id="vm"></a>원격 VM

Azure Machine Learning은 자신만의 컴퓨팅 리소스를 가져와서 작업 영역에 연결하는 기능을 지원합니다. 해당 리소스 유형은 Azure Machine Learning Service에서 액세스할 수 있는 한 임의 원격 VM입니다. 조직 또는 온-프레미스에 있는 Azure VM 또는 원격 서버일 수 있습니다. 특히 IP 주소 및 자격 증명(사용자 이름/암호 또는 SSH 키)을 지정하면 원격 실행에 액세스 가능한 VM을 사용할 수 있습니다.
시스템 빌드 conda 환경, 기존 Python 환경 또는 Docker 컨테이너를 사용할 수 있습니다. Docker 컨테이너를 사용하여 실행하려면 VM에서 Docker 엔진을 실행하고 있어야 합니다. 이 기능은 로컬 머신보다 더 유연한 클라우드 기반 개발/실험 환경을 원하는 경우에 특히 유용합니다.

> [!TIP]
> 이 시나리오에서 선택하는 Azure VM으로 Data Science Virtual Machine을 사용하는 것이 좋습니다. 이는 ML 개발의 전체 수명 주기를 위한 엄선된 도구 및 프레임워크가 포함된 Azure에 미리 구성된 데이터 과학 및 AI 개발 환경입니다. Azure Machine Learning과 함께 Data Science Virtual Machine을 사용하는 방법에 대한 자세한 내용은 [개발 환경 구성](https://docs.microsoft.com/azure/machine-learning/service/how-to-configure-environment#dsvm) 문서를 참조하세요.

> [!WARNING]
> Azure Machine Learning은 Ubuntu를 실행하는 가상 머신만 지원합니다. 가상 머신을 만들거나 기존 가상 머신을 선택할 때 Ubuntu를 사용하는 가상 머신을 선택해야 합니다.

다음은 SDK를 사용하여 DSVM(Data Science Virtual Machine)을 교육 대상으로 구성하는 단계입니다.

1. 기존 가상 머신을 계산 대상으로 연결하려면 가상 머신의 정규화된 도메인 이름, 로그인 이름 및 암호를 입력해야 합니다.  이 예제에서는 ```<fqdn>```을 VM의 공용 정규화된 도메인 이름 또는 공용 IP 주소로 바꿉니다. ```<username>``` 및 ```<password>```를 VM의 SSH 사용자 및 암호로 바꿉니다.

    ```python
    from azureml.core.compute import RemoteCompute, ComputeTarget
    
    # Create compute config.
    attach_config = RemoteCompute.attach_configuration(address = "ipaddress",
                                                       ssh_port=22,
                                                       username='<username>',
                                                       password="<password>")
    # If using SSH instead of a password, use this:
    #                                                  ssh_port=22,
    #                                                   username='<username>',
    #                                                   password=None,
    #                                                   private_key_file="path-to-file",
    #                                                   private_key_passphrase="passphrase")

    # Attach the compute
    compute = ComputeTarget.attach(ws, "attach-dsvm", attach_config)

    compute.wait_for_completion(show_output=True)
    ```

1. DSVM 컴퓨팅 대상에 대한 구성을 만듭니다. Docker 및 conda는 DSVM에 대한 학습 환경을 만들고 구성하는 데 사용됩니다.

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
    # If you want to use GPU in DSVM, you must also use GPU base Docker image azureml.core.runconfig.DEFAULT_GPU_IMAGE
    run_config.environment.docker.base_image = azureml.core.runconfig.DEFAULT_CPU_IMAGE
    print('Base Docker image is:', run_config.environment.docker.base_image)

    # Ask system to provision a new one based on the conda_dependencies.yml file
    run_config.environment.python.user_managed_dependencies = False

    # Prepare the Docker and conda environment automatically when used the first time.
    run_config.prepare_environment = True

    # specify CondaDependencies obj
    run_config.environment.python.conda_dependencies = CondaDependencies.create(conda_packages=['scikit-learn'])

    ```

## <a id="databricks"></a>Azure Databricks

Azure Databricks는 Azure 클라우드의 Apache Spark 기반 환경입니다. 이 환경은 Azure Machine Learning 파이프라인을 사용하여 모델을 학습할 때 계산 대상으로 사용할 수 있습니다.

> [!IMPORTANT]
> Azure Databricks 계산 대상은 Machine Learning 파이프라인에서만 사용할 수 있습니다.
>
> 모델 학습에 사용하려면 먼저 Azure Databricks 작업 영역을 만들어야 합니다. 이러한 리소스를 만들려면 [Azure Databricks에서 Spark 작업 실행](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal) 문서를 참조하세요.

Azure Databricks를 계산 대상으로 연결하려면 Azure Machine Learning SDK를 사용하고 다음 정보를 제공해야 합니다.

* __컴퓨팅 이름__: 이 컴퓨팅 리소스에 할당하려는 이름입니다.
* __Databricks 작업 영역__: Azure Databricks 작업 영역의 이름입니다.
* __액세스 토큰__: Azure Databricks에서 인증을 받는 데 사용하는 액세스 토큰입니다. 액세스 토큰을 생성하려면 [인증](https://docs.azuredatabricks.net/api/latest/authentication.html) 문서를 참조하세요.

다음 코드는 계산 대상으로 Azure Databricks에 연결하는 방법을 보여 줍니다.

```python
databricks_compute_name = os.environ.get("AML_DATABRICKS_COMPUTE_NAME", "<databricks_compute_name>")
databricks_workspace_name = os.environ.get("AML_DATABRICKS_WORKSPACE", "<databricks_workspace_name>")
databricks_resource_group = os.environ.get("AML_DATABRICKS_RESOURCE_GROUP", "<databricks_resource_group>")
databricks_access_token = os.environ.get("AML_DATABRICKS_ACCESS_TOKEN", "<databricks_access_token>")

try:
    databricks_compute = ComputeTarget(workspace=ws, name=databricks_compute_name)
    print('Compute target already exists')
except ComputeTargetException:
    print('compute not found')
    print('databricks_compute_name {}'.format(databricks_compute_name))
    print('databricks_workspace_name {}'.format(databricks_workspace_name))
    print('databricks_access_token {}'.format(databricks_access_token))

    # Create attach config
    attach_config = DatabricksCompute.attach_configuration(resource_group = databricks_resource_group,
                                                           workspace_name = databricks_workspace_name,
                                                           access_token = databricks_access_token)
    databricks_compute = ComputeTarget.attach(
             ws,
             databricks_compute_name,
             attach_config
         )
    
    databricks_compute.wait_for_completion(True)
```

## <a id="adla"></a>Azure Data Lake Analytics

Azure Data Lake Analytics는 Azure 클라우드의 빅 데이터 분석 플랫폼입니다. 이 환경은 Azure Machine Learning 파이프라인을 사용하여 모델을 학습할 때 계산 대상으로 사용할 수 있습니다.

> [!IMPORTANT]
> Azure Data Lake Analytics 계산 대상은 Machine Learning 파이프라인에서만 사용할 수 있습니다.
>
> 모델 학습에 사용하려면 먼저 Azure Data Lake Analytics 계정을 만들어야 합니다. 이 리소스를 만들려면 [Azure Data Lake Analytics 시작](https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-get-started-portal) 문서를 참조하세요.

Data Lake Analytics를 계산 대상으로 연결하려면 Azure Machine Learning SDK를 사용하고 다음 정보를 제공해야 합니다.

* __컴퓨팅 이름__: 이 컴퓨팅 리소스에 할당하려는 이름입니다.
* __리소스 그룹__: Data Lake Analytics 계정을 포함하는 리소스 그룹입니다.
* __계정 이름__: Data Lake Analytics 계정 이름입니다.

다음 코드는 계산 대상으로 Data Lake Analytics에 연결하는 방법을 보여 줍니다.

```python
adla_compute_name = os.environ.get("AML_ADLA_COMPUTE_NAME", "<adla_compute_name>")
adla_resource_group = os.environ.get("AML_ADLA_RESOURCE_GROUP", "<adla_resource_group>")
adla_account_name = os.environ.get("AML_ADLA_ACCOUNT_NAME", "<adla_account_name>")

try:
    adla_compute = ComputeTarget(workspace=ws, name=adla_compute_name)
    print('Compute target already exists')
except ComputeTargetException:
    print('compute not found')
    print('adla_compute_name {}'.format(adla_compute_name))
    print('adla_resource_id {}'.format(adla_resource_group))
    print('adla_account_name {}'.format(adla_account_name))
    # create attach config
    attach_config = AdlaCompute.attach_configuration(resource_group = adla_resource_group,
                                                     account_name = adla_account_name)
    # Attach ADLA
    adla_compute = ComputeTarget.attach(
             ws,
             adla_compute_name,
             attach_config
         )
    
    adla_compute.wait_for_completion(True)
```

> [!TIP]
> Azure Machine Learning 파이프라인은 Data Lake Analytics 계정의 기본 데이터 저장소에 저장된 데이터에만 작동할 수 있습니다. 사용해야 하는 데이터가 기본이 아닌 저장소에 있으면 학습 전에 [`DataTransferStep`](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.data_transfer_step.datatransferstep?view=azure-ml-py)을 사용하여 데이터를 복사할 수 있습니다.

## <a id="hdinsight"></a>Azure HDInsight 

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
from azureml.core.compute import HDInsightCompute, ComputeTarget

try:
    # Attaches a HDInsight cluster as a compute target.
    attach_config = HDInsightCompute.attach_configuration(address = "fqdn-or-ipaddress",
                                                          ssh_port = 22,
                                                          username = "username",
                                                          password = None, #if using ssh key
                                                          private_key_file = "path-to-key-file",
                                                          private_key_phrase = "key-phrase")
    compute = ComputeTarget.attach(ws, "myhdi", attach_config)
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

학습 실행을 제출하는 두 가지 방법은 다음과 같습니다.

* `ScriptRunConfig` 개체 제출
* `Pipeline` 개체 제출

> [!IMPORTANT]
> Azure Databricks 및 Azure Datalake Analytics 계산 대상은 파이프라인에서만 사용할 수 있습니다.
> 로컬 계산 대상은 파이프라인에서 사용할 수 없습니다.

### <a name="submit-using-scriptrunconfig"></a>`ScriptRunConfig`를 사용하여 제출

`ScriptRunConfig`를 사용하여 학습 실행을 제출하기 위한 코드 패턴은 계산 대상에 관계 없이 동일합니다.

* 계산 대상에 대한 실행 구성을 사용하여 `ScriptRunConfig` 개체를 만듭니다.
* 실행을 제출합니다.
* 실행이 완료될 때까지 기다립니다.

다음 예제에서는 이 문서의 앞부분에서 만든 시스템 관리 로컬 계산 대상을 위한 구성을 사용합니다.

```python
src = ScriptRunConfig(source_directory = script_folder, script = 'train.py', run_config = run_config_system_managed)
run = exp.submit(src)
run.wait_for_completion(show_output = True)
```


### <a name="submit-using-a-pipeline"></a>파이프라인을 사용하여 제출

파이프라인을 사용하여 학습 실행을 제출하기 위한 코드 패턴은 계산 대상에 관계 없이 동일합니다.

* 계산 리소스에 대한 파이프라인에 단계를 추가합니다.
* 파이프라인을 사용하여 실행을 제출합니다.
* 실행이 완료될 때까지 기다립니다.

다음 예제에서는 이 문서의 앞부분에서 만든 Azure Databricks 계산 대상을 사용합니다.

```python
dbStep = DatabricksStep(
    name="databricksmodule",
    inputs=[step_1_input],
    outputs=[step_1_output],
    num_workers=1,
    notebook_path=notebook_path,
    notebook_params={'myparam': 'testparam'},
    run_name='demo run name',
    databricks_compute=databricks_compute,
    allow_reuse=False
)
# list of steps to run
steps = [dbStep]
pipeline = Pipeline(workspace=ws, steps=steps)
pipeline_run = Experiment(ws, 'Demo_experiment').submit(pipeline)
pipeline_run.wait_for_completion()
```

Machine Learning 파이프라인에 대한 자세한 내용은 [파이프라인 및 Azure Machine Learning](concept-ml-pipelines.md) 문서를 참조하세요.

파이프라인을 사용한 학습을 보여 주는 예제 Jupyter 노트를 보려면 [https://github.com/Azure/MachineLearningNotebooks/tree/master/pipeline](https://github.com/Azure/MachineLearningNotebooks/tree/master/pipeline)을 참조하세요.

## <a name="view-and-set-up-compute-using-the-azure-portal"></a>Azure Portal을 사용하여 계산을 살펴보고 설정

Azure Portal에서 작업 영역과 연결된 계산 대상을 살펴볼 수 있습니다. 목록으로 이동하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com)을 방문하여 작업 영역으로 이동합니다.
2. __응용 프로그램__ 섹션 아래에서 __계산__ 링크를 클릭합니다.

    ![[계산 보기] 탭](./media/how-to-set-up-training-targets/azure-machine-learning-service-workspace.png)

### <a name="create-a-compute-target"></a>계산 대상 만들기

위의 단계에 따라 계산 대상 목록을 살펴본 후, 다음 단계에 따라 계산 대상을 만듭니다.

1. __+__ 기호를 클릭하여 계산 대상을 추가합니다.

    ![계산 추가 ](./media/how-to-set-up-training-targets/add-compute-target.png)

1. 컴퓨팅 대상의 이름 입력
1. __학습__에 사용할 컴퓨팅 유형으로 **Machine Learning 컴퓨팅** 선택

    > [!IMPORTANT]
    > Azure Machine Learning 컴퓨팅은 학습용 관리형 컴퓨팅으로만 만들 수 있음

1. 필요한 양식(특히, VM 패밀리 및 컴퓨팅 회전에 사용할 최대 노드) 작성 
1.  __만들기__
1. 목록에서 컴퓨팅 대상을 선택하여 만들기 작업의 상태 보기

    ![컴퓨팅 목록 보기](./media/how-to-set-up-training-targets/View_list.png)

1. 그러면 컴퓨팅 대상의 세부 정보가 표시됩니다.

    ![세부 정보 보기](./media/how-to-set-up-training-targets/compute-target-details.png)

1. 이제 위에서 설명한 것처럼 이러한 대상에 대한 실행을 제출할 수 있음


### <a name="reuse-existing-compute-in-your-workspace"></a>작업 영역에서 기존 계산을 다시 사용

위의 단계에 따라 계산 대상 목록을 살펴본 후, 다음 단계에 따라 계산 대상을 다시 사용합니다.

1. **+** 기호를 클릭하여 컴퓨팅 대상 추가
2. 컴퓨팅 대상의 이름 입력
3. __학습__용으로 연결할 컴퓨팅 유형 선택

    > [!IMPORTANT]
    > 모든 계산 유형을 포털에서 연결할 수 있는 것은 아닙니다.
    > 현재, 학습을 위해 연결할 수 있는 유형은 다음과 같습니다.
    > 
    > * 원격 VM
    > * Databricks
    > * Data Lake Analytics
    > * HDInsight

1. 필요한 양식 작성

    > [!NOTE]
    > Microsoft에서는 암호보다 안전한 SSH 키를 권장합니다. 암호는 무차별 암호 대입 공격(brute force attack)에 취약한 반면, SSH 키는 암호화 서명을 사용합니다. Azure Virtual Machines에 사용할 SSH 키를 만드는 방법은 다음 문서를 참조하세요.
    >
    > * [Linux 또는 macOS에서 SSH 키를 만들고 사용]( https://docs.microsoft.com/azure/virtual-machines/linux/mac-create-ssh-keys)
    > * [Windows에서 SSH 키를 만들고 사용]( https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows)

1. 연결 선택
1. 목록에서 컴퓨팅 대상을 선택하여 연결 작업의 상태 보기
1. 이제 위에서 설명한 것처럼 이러한 대상에 대한 실행을 제출할 수 있음

## <a name="examples"></a>예
다음 위치에서 Notebook을 참조하세요.
* [how-to-use-azureml/training](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training)

* [tutorials/img-classification-part1-training.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/tutorials/img-classification-part1-training.ipynb)

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>다음 단계

* [Azure Machine Learning SDK 참조](https://aka.ms/aml-sdk)
* [자습서: 모델 학습](tutorial-train-models-with-aml.md)
* [모델 배포 위치](how-to-deploy-and-where.md)
* [Azure Machine Learning 서비스를 사용하여 기계 학습 파이프라인 빌드](concept-ml-pipelines.md)
