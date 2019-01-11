---
title: 모델 학습의 컴퓨팅 대상
titleSuffix: Azure Machine Learning service
description: 기계 학습 모델 학습의 학습 환경(컴퓨팅 대상)을 구성합니다. 학습 환경을 쉽게 전환할 수 있습니다. 로컬로 학습을 시작합니다. 규모 확장이 필요한 경우 클라우드 기반 컴퓨팅 대상으로 전환합니다.
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
ms.openlocfilehash: 9037f6d7602f186bc30e55acbc050280bca134ee
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/27/2018
ms.locfileid: "53794467"
---
# <a name="set-up-compute-targets-for-model-training"></a>모델 학습의 컴퓨팅 대상 설정

Azure Machine Learning Service를 통해 다양한 컴퓨팅 리소스에서 모델을 학습시킬 수 있습니다. __컴퓨팅 대상__이라는 이러한 컴퓨팅 리소스는 로컬일 수도 있고 클라우드에 있을 수도 있습니다. 이 문서에서는 지원되는 컴퓨팅 대상 및 사용 방법을 알아봅니다.

컴퓨팅 대상은 학습 스크립트를 실행하거나 웹 서비스로 배포 시 모델이 호스트되는 리소스입니다. Azure Machine Learning SDK, Azure Portal 또는 Azure CLI를 사용하여 컴퓨팅 대상을 만들고 관리할 수 있습니다. 다른 서비스(예: Azure HDInsight 클러스터)를 통해 만든 컴퓨팅 대상이 있는 경우 이러한 대상을 Azure Machine Learning Service 작업 영역에 연결하여 사용할 수 있습니다.

Azure Machine Learning이 지원하는 컴퓨팅 대상의 세 가지 광범위한 범주는 다음과 같습니다.

* __로컬__: 개발 및 실험 환경으로 사용하는 로컬 머신 또는 클라우드 기반 VM(가상 머신)입니다. 
* __관리형 컴퓨팅__: Azure Machine Learning 컴퓨팅은 Azure Machine Learning Service에서 관리되는 컴퓨팅 제품입니다. 이를 통해 학습, 테스트 및 일괄 유추를 위한 단일 또는 다중 노드 컴퓨팅을 쉽게 만들 수 있습니다.
* __연결된 컴퓨팅__: 자신만의 Azure 클라우드 컴퓨팅을 가져와서 Azure Machine Learning에 연결할 수도 있습니다. 지원되는 컴퓨팅 유형 및 사용 방법을 다음 섹션에서 자세히 알아보세요.


## <a name="supported-compute-targets"></a>지원되는 계산 대상

Azure Machine Learning Service에는 다양한 컴퓨팅 대상에 대한 다양한 지원이 있습니다. 일반적인 모델 개발 수명 주기는 적은 양의 데이터에 대한 개발 및 실험으로 시작합니다. 이 단계에서는 로컬 컴퓨터 또는 클라우드 기반 VM과 같은 로컬 환경을 사용하는 것이 좋습니다. 더 큰 데이터 세트에서 학습을 확장하거나 분산 학습을 수행할 경우 Azure Machine Learning 컴퓨팅 환경을 사용하여 실행을 제출할 때마다 자동으로 크기가 조정되는 단일 또는 다중 노드 클러스터를 만드는 것이 좋습니다. 다음 표에 설명된 것처럼 다양한 시나리오에 대한 지원이 달라질 수는 있지만 고유한 컴퓨팅 리소스를 연결할 수도 있습니다.

|계산 대상| GPU 가속 | 자동<br/> 하이퍼 매개 변수 튜닝 | 자동</br> Machine Learning | 친숙한 파이프라인|
|----|:----:|:----:|:----:|:----:|
|[로컬 컴퓨터](#local)| 가능할 수도 있음 | &nbsp; | ✓ | &nbsp; |
|[Azure Machine Learning 컴퓨팅](#amlcompute)| ✓ | ✓ | ✓ | ✓ |
|[원격 VM](#vm) | ✓ | ✓ | ✓ | ✓ |
|[Azure Databricks](#databricks)| &nbsp; | &nbsp; | ✓ | ✓[*](#pipeline-only) |
|[Azure 데이터 레이크 분석](#adla)| &nbsp; | &nbsp; | &nbsp; | ✓[*](#pipeline-only) |
|[Azure HDInsight](#hdinsight)| &nbsp; | &nbsp; | &nbsp; | ✓ |

> [!IMPORTANT]
> <a id="pipeline-only"></a>__*__ _Azure Databricks 및 Azure Data Lake Analytics는 파이프라인에만 사용할 수 있습니다._<br/>
> 파이프라인에 대한 자세한 내용은 [Azure Machine Learning의 파이프라인](concept-ml-pipelines.md)을 참조하세요.
>
> Azure Machine Learning 컴퓨팅 환경은 작업 영역 내에서 만들어야 합니다. 기존 인스턴스는 작업 영역에 연결할 수 없습니다.
>
> 다른 컴퓨팅 대상은 Azure Machine Learning 외부에서 만든 후 작업 영역에 연결해야 합니다.
>
> 일부 컴퓨팅 대상은 모델을 학습시킬 때 Docker 컨테이너 이미지를 사용합니다. GPU 기본 이미지는 Microsoft Azure Services에서만 사용해야 합니다. 모델 학습의 경우 서비스에는 다음이 포함됩니다.
> * Azure Machine Learning 컴퓨팅
> * Azure Kubernetes Service
> * Windows DSVM(Data Science Virtual Machine)

## <a name="workflow"></a>워크플로

Azure Machine Learning을 사용하여 모델을 개발하고 배포하는 워크플로는 다음 단계를 따릅니다.

1. Python에서 Machine Learning 학습 스크립트를 개발합니다.
1. 컴퓨팅 대상을 만들고 구성하거나 기존 컴퓨팅 대상을 연결합니다.
1. 교육 스크립트를 계산 대상에 제출합니다.
1. 결과를 검사하여 가장 적합한 모델을 찾습니다.
1. 모델을 모델 레지스트리에 등록합니다.
1. 모델을 배포합니다.

> [!NOTE]
> 교육 스크립트는 특정 계산 대상에 연결되지 않습니다. 처음에는 로컬 컴퓨터에서 교육하고, 나중에 교육 스크립트를 다시 작성할 필요 없이 계산 대상을 전환할 수 있습니다.
> 
> 관리형 컴퓨팅을 만들거나 기존 컴퓨팅을 연결하여 컴퓨팅 대상을 작업 영역과 연결할 때 컴퓨팅에 대한 이름을 입력합니다. 이름은 2자에서 16자 사이여야 합니다.

한 컴퓨팅 대상에서 다른 컴퓨팅 대상으로 전환하려면 [실행 구성](concept-azure-machine-learning-architecture.md#run-configuration)이 필요합니다. 실행 구성은 계산 대상에서 스크립트를 실행하는 방법을 정의합니다.

## <a name="training-scripts"></a>교육 스크립트

학습 실행을 시작하면 학습 스크립트가 포함된 디렉터리의 스냅숏이 생성되어 컴퓨팅 대상으로 전송됩니다. 자세한 내용은 [스냅숏](concept-azure-machine-learning-architecture.md#snapshot)을 참조하세요.

## <a id="local"></a>로컬 컴퓨터

로컬로 학습하는 경우 SDK를 사용하여 학습 작업을 제출합니다. 사용자 관리 또는 시스템 관리 환경을 사용하여 학습할 수 있습니다.

### <a name="user-managed-environment"></a>사용자 관리 환경

사용자 관리 환경에서 필요한 모든 패키지를 스크립트를 실행하는 Python 환경에서 사용할 수 있는지 확인합니다. 다음 코드 조각은 사용자 관리 환경의 학습을 구성하는 방법에 관한 예제입니다.

```python
from azureml.core.runconfig import RunConfiguration

# Edit a run configuration property on the fly.
run_config_user_managed = RunConfiguration()

run_config_user_managed.environment.python.user_managed_dependencies = True

# Choose a specific Python environment by pointing to a Python path. For example:
# run_config.environment.python.interpreter_path = '/home/ninghai/miniconda3/envs/sdk2/bin/python'
```

  
### <a name="system-managed-environment"></a>시스템 관리 환경

시스템 관리 환경은 conda를 사용하여 종속성을 관리합니다. Conda는 종속성 목록을 포함하는 **conda_dependencies.yml**이라는 파일을 만듭니다. 사용자는 새 conda 환경을 빌드하고 스크립트를 실행할 것을 시스템에 요청할 수 있습니다. conda_dependencies.yml 파일이 변경되지 않는 한, 나중에 시스템 관리 환경을 다시 사용할 수 있습니다. 

새 환경의 초기 설정을 완료하는 데는 필요한 종속성의 크기에 따라 몇 분 정도 걸릴 수 있습니다. 다음 코드 조각은 scikit-learn에 종속되는 시스템 관리 환경을 만드는 방법을 설명합니다.

```python
from azureml.core.runconfig import RunConfiguration
from azureml.core.conda_dependencies import CondaDependencies

run_config_system_managed = RunConfiguration()

run_config_system_managed.environment.python.user_managed_dependencies = False
run_config_system_managed.auto_prepare_environment = True

# Specify the conda dependencies with scikit-learn

run_config_system_managed.environment.python.conda_dependencies = CondaDependencies.create(conda_packages=['scikit-learn'])
```

## <a id="amlcompute"></a>Azure Machine Learning 컴퓨팅

Azure Machine Learning 컴퓨팅은 사용자가 단일 또는 다중 노드 컴퓨팅을 손쉽게 만들 수 있는 관리형 컴퓨팅 인프라입니다. 작업 영역 지역 내에서 컴퓨팅은 작업 영역에서 다른 사용자와 공유할 수 있는 리소스로 만들어집니다. 작업이 제출될 때 컴퓨팅이 자동으로 강화되고 Azure 가상 네트워크에 포함할 수 있습니다. 컴퓨팅은 컨테이너화된 환경에서 실행되며 모델 종속성을 Docker 컨테이너로 패키지합니다.

Azure Machine Learning 컴퓨팅을 사용하여 클라우드의 CPU 또는 GPU 컴퓨팅 노드 클러스터에 학습 프로세스를 배포할 수 있습니다. GPU를 포함하는 VM 크기에 대한 자세한 내용은 [GPU 최적화 가상 머신 크기](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-gpu)를 참조하세요.

> [!NOTE]
> Azure Machine Learning 컴퓨팅에는 할당할 수 있는 코어 수와 같은 기본적인 제한이 있습니다. 자세한 내용은 [Azure 리소스에 대한 할당량 관리 및 요청](https://docs.microsoft.com/azure/machine-learning/service/how-to-manage-quotas)을 참조하세요.

실행을 예약할 때 주문형으로 또는 영구적 리소스로 Azure Machine Learning 컴퓨팅 환경을 만들 수 있습니다.

### <a name="run-based-creation"></a>실행 기반 만들기

Azure Machine Learning 컴퓨팅 환경을 런타임 시 컴퓨팅 대상으로 만들 수 있습니다. 컴퓨팅은 실행 구성에서 지정한 **max_nodes**의 수로 실행 및 강화되도록 자동으로 생성됩니다. 실행이 완료되면 컴퓨팅이 자동으로 삭제됩니다.

> [!IMPORTANT]
> Azure Machine Learning 컴퓨팅 환경의 실행 기반 만들기는 현재 미리 보기 상태로 제공됩니다. 하이퍼 매개 변수 튜닝 또는 자동화된 Machine Learning을 사용 중인 경우에는 실행 기반 만들기를 사용하지 마세요. 하이퍼 매개 변수 튜닝 또는 자동화된 Machine Learning을 사용하려면 실행을 제출하기 전에 Azure Machine Learning 컴퓨팅 환경을 만듭니다.

```python
from azureml.core.compute import ComputeTarget, AmlCompute

# First, list the supported VM families for Azure Machine Learning Compute
AmlCompute.supported_vmsizes()

from azureml.core.runconfig import RunConfiguration

# Create a new runconfig object
run_config = RunConfiguration()

# Signal that you want to use AmlCompute to execute the script
run_config.target = "amlcompute"

# AmlCompute is created in the same region as your workspace
# Set the VM size for AmlCompute from the list of supported_vmsizes
run_config.amlcompute.vm_size = 'STANDARD_D2_V2'

```

### <a name="persistent-compute-basic"></a>영구적 컴퓨팅: Basic

영구적 Azure Machine Learning 컴퓨팅 환경은 여러 작업에서 다시 사용할 수 있습니다. 컴퓨팅은 작업 영역의 다른 사용자와 공유할 수 있고 작업 간에 유지됩니다.

영구적 Azure Machine Learning 컴퓨팅 환경 리소스를 만들려면 **vm_size** 및 **max_nodes** 속성을 지정합니다. 그런 다음, Azure Machine Learning은 다른 속성에 스마트 기본값을 사용합니다. 컴퓨팅은 사용되지 않을 때 0 노드로 자동으로 크기가 축소되고, 필요한 경우 작업을 실행할 전용 VM을 만듭니다. 

* **vm_size**: Azure Machine Learning 컴퓨팅에서 만든 노드의 VM 제품군입니다.
* **max_nodes**: Azure Machine Learning 컴퓨팅에서 작업을 실행하는 동안 자동으로 크기를 조정할 최대 노드 수입니다.

```python
from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException

# Choose a name for your CPU cluster
cpu_cluster_name = "cpucluster"

# Verify that the cluster doesn't already exist
try:
    cpu_cluster = ComputeTarget(workspace=ws, name=cpu_cluster_name)
    print('Found existing cluster, use it.')
except ComputeTargetException:
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2',
                                                           max_nodes=4)
    cpu_cluster = ComputeTarget.create(ws, cpu_cluster_name, compute_config)

cpu_cluster.wait_for_completion(show_output=True)

```

### <a name="persistent-compute-advanced"></a>영구적 컴퓨팅: 고급

Azure Machine Learning 컴퓨팅 환경을 만들 때 여러 고급 속성도 구성할 수 있습니다. 속성을 사용하면 고정 크기로 또는 구독의 기존 Azure Virtual Network 내에서 영구적 클러스터를 만들 수 있습니다.

**vm_size** 및 **max_nodes** 속성과 함께 다음 속성을 사용할 수도 있습니다.

* **min_nodes**: Azure Machine Learning 컴퓨팅 환경에서 작업을 실행할 때 크기를 축소할 최소 노드 수입니다. 기본 최솟값은 0개 노드입니다.
* **vm_priority**: Azure Machine Learning 컴퓨팅 환경 리소스를 만들 때 사용할 VM의 유형입니다. **전용**(기본값) 및 **낮은 우선 순위** 사이에서 선택합니다. 낮은 우선 순위 VM은 Azure의 초과 용량을 사용합니다. 이러한 VM은 비용이 낮습니다. 하지만 이러한 VM을 사용하는 경우 실행이 선취될 수 있습니다.
* **idle_seconds_before_scaledown**: 실행 완료 후 및 **min_nodes** 수로 자동 크기 조정되기 전 대기할 유휴 시간입니다. 기본 유휴 시간은 120초입니다.
* **vnet_resourcegroup_name**: __기존__ 가상 네트워크의 리소스 그룹입니다. Azure Machine Learning 컴퓨팅 환경은 이 가상 네트워크 내에서 만들어집니다.
* **vnet_name**: 가상 네트워크의 이름입니다. 가상 네트워크는 Azure Machine Learning 작업 영역과 동일한 지역에 있어야 합니다.
* **subnet_name**: 가상 네트워크 내에 있는 서브넷 이름입니다. Azure Machine Learning 컴퓨팅 환경 리소스는 이 서브넷 범위의 IP 주소가 할당됩니다.

> [!TIP]
> 영구적 Azure Machine Learning 컴퓨팅 환경 리소스를 만드는 경우 **min_nodes** 또는 **max_nodes**의 수와 같은 속성을 업데이트할 수 있습니다. 속성을 업데이트하려면 속성에 대한 `update()` 함수를 호출합니다.

```python
from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException

# Choose a name for your CPU cluster
cpu_cluster_name = "cpucluster"

# Verify that the cluster doesn't already exist 
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

Azure Machine Learning은 자신만의 컴퓨팅 리소스를 가져와서 작업 영역에 연결하는 기능을 지원합니다. 해당 리소스 유형은 Azure Machine Learning Service에서 액세스할 수 있는 한 임의의 원격 VM입니다. 리소스는 조직 또는 온-프레미스에 있는 Azure VM 또는 원격 서버일 수 있습니다. 특히 IP 주소 및 자격 증명(사용자 이름 및 암호 또는 SSH 키)이 지정되면 원격 실행에 액세스 가능한 VM을 사용할 수 있습니다.
시스템 빌드 conda 환경, 기존 Python 환경 또는 Docker 컨테이너를 사용할 수 있습니다. Docker 컨테이너를 사용하여 실행하는 경우 Docker 엔진이 VM에서 실행되어야 합니다. 원격 VM 기능은 로컬 머신보다 더 유연한 클라우드 기반 개발 및 실험 환경을 원하는 경우에 특히 유용합니다.

> [!TIP]
> 이 시나리오에 대한 선택인 Azure VM으로 DSVM을 사용합니다. 이 VM은 Azure에 미리 구성된 데이터 과학 및 AI 개발 환경으로, Machine Learning 개발의 전체 수명 주기를 위해 엄선된 도구 및 프레임워크를 제공합니다. Azure Machine Learning과 함께 DSVM을 사용하는 방법에 관한 자세한 내용은 [개발 환경 구성](https://docs.microsoft.com/azure/machine-learning/service/how-to-configure-environment#dsvm)을 참조하세요.

> [!WARNING]
> Azure Machine Learning은 Ubuntu를 실행하는 가상 머신만 지원합니다. VM을 만들거나 기존 VM을 선택하는 경우 Ubuntu를 사용하는 VM을 선택해야 합니다.

다음은 SDK를 사용하여 DSVM을 학습 대상으로 구성하는 단계입니다.

1. 기존 가상 머신을 컴퓨팅 대상으로 연결하려면 가상 머신의 FQDN(정규화된 도메인 이름), 사용자 이름 및 암호를 입력해야 합니다. 이 예제에서는 \<fqdn>을 VM의 공용 FQDN 또는 공용 IP 주소로 바꿉니다. \<사용자 이름> 및 \<암호>를 VM에 대한 SSH 사용자 이름 및 암호로 바꿉니다.

    ```python
    from azureml.core.compute import RemoteCompute, ComputeTarget
    
    # Create the compute config
    attach_config = RemoteCompute.attach_configuration(address = "ipaddress",
                                                       ssh_port=22,
                                                       username='<username>',
                                                       password="<password>")

    # If you use SSH instead of a password, use this code:
    #                                                  ssh_port=22,
    #                                                  username='<username>',
    #                                                  password=None,
    #                                                  private_key_file="path-to-file",
    #                                                  private_key_passphrase="passphrase")

    # Attach the compute target
    compute = ComputeTarget.attach(ws, "attach-dsvm", attach_config)

    compute.wait_for_completion(show_output=True)
    ```

1. DSVM 컴퓨팅 대상에 대한 구성을 만듭니다. Docker 및 conda는 DSVM에서 학습 환경을 만들고 구성하는 데 사용됩니다.

    ```python
    from azureml.core.runconfig import RunConfiguration
    from azureml.core.conda_dependencies import CondaDependencies

    # Load into memory the cpu-dsvm.runconfig file created in the previous attach operation
    run_config = RunConfiguration(framework = "python")

    # Set the compute target to the Linux DSVM
    run_config.target = compute_target_name

    # Use Docker in the remote VM
    run_config.environment.docker.enabled = True

    # Use the CPU base image
    # To use GPU in DSVM, you must also use the GPU base Docker image "azureml.core.runconfig.DEFAULT_GPU_IMAGE"
    run_config.environment.docker.base_image = azureml.core.runconfig.DEFAULT_CPU_IMAGE
    print('Base Docker image is:', run_config.environment.docker.base_image)

    # Ask the system to provision a new conda environment based on the conda_dependencies.yml file
    run_config.environment.python.user_managed_dependencies = False

    # Prepare the Docker and conda environment automatically when they're used for the first time
    run_config.prepare_environment = True

    # Specify the CondaDependencies object
    run_config.environment.python.conda_dependencies = CondaDependencies.create(conda_packages=['scikit-learn'])

    ```

## <a id="databricks"></a>Azure Databricks

Azure Databricks는 Azure 클라우드의 Apache Spark 기반 환경입니다. 환경은 Azure Machine Learning 파이프라인을 사용하여 모델을 학습할 때 컴퓨팅 대상으로 사용할 수 있습니다.

> [!IMPORTANT]
> Azure Databricks 계산 대상은 Machine Learning 파이프라인에서만 사용할 수 있습니다.
>
> 모델을 학습하는 데 사용하려면 먼저 Azure Databricks 작업 영역을 만들어야 합니다. 이러한 리소스를 만들려면 [Azure Databricks에서 Spark 작업 실행](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal)을 참조하세요.

Azure Databricks를 계산 대상으로 연결하려면 Azure Machine Learning SDK를 사용하고 다음 정보를 제공해야 합니다.

* __컴퓨팅 이름__: 이 컴퓨팅 리소스에 할당할 이름입니다.
* __Databricks 작업 영역__: Azure Databricks 작업 영역의 이름입니다.
* __액세스 토큰__: Azure Databricks에서 인증을 받는 데 사용하는 액세스 토큰입니다. 액세스 토큰을 생성하려면 [인증](https://docs.azuredatabricks.net/api/latest/authentication.html)을 참조하세요.

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

    # Create the attach config
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

Azure Data Lake Analytics는 Azure 클라우드의 빅 데이터 분석 플랫폼입니다. 플랫폼은 Azure Machine Learning 파이프라인을 사용하여 모델을 학습할 때 컴퓨팅 대상으로 사용할 수 있습니다.

> [!IMPORTANT]
> Azure Data Lake Analytics 계산 대상은 Machine Learning 파이프라인에서만 사용할 수 있습니다.
>
> 모델을 학습하는 데 사용하려면 먼저 Azure Data Lake Analytics 계정을 만들어야 합니다. 이 리소스를 만들려면 [Azure Data Lake Analytics 시작](https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-get-started-portal)을 참조하세요.

Data Lake Analytics를 계산 대상으로 연결하려면 Azure Machine Learning SDK를 사용하고 다음 정보를 제공해야 합니다.

* __컴퓨팅 이름__: 이 컴퓨팅 리소스에 할당할 이름입니다.
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
    
    # Create the attach config
    attach_config = AdlaCompute.attach_configuration(resource_group = adla_resource_group,
                                                     account_name = adla_account_name)
    # Attach the ADLA
    adla_compute = ComputeTarget.attach(
             ws,
             adla_compute_name,
             attach_config
         )
    
    adla_compute.wait_for_completion(True)
```

> [!TIP]
> Azure Machine Learning 파이프라인은 Data Lake Analytics 계정의 기본 데이터 저장소에 저장된 데이터에만 작동할 수 있습니다. 필요한 데이터가 기본 저장소에 없을 경우 모델을 학습하기 전에 [DataTransferStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.data_transfer_step.datatransferstep?view=azure-ml-py) 작업을 사용하여 데이터를 복사할 수 있습니다.

## <a id="hdinsight"></a>Azure HDInsight 

Azure HDInsight는 빅 데이터 분석을 위한 인기 있는 플랫폼입니다. 플랫폼은 모델을 학습하는 데 사용할 수 있는 Apache Spark를 제공합니다.

> [!IMPORTANT]
> 모델을 학습하는 데 사용하려면 먼저 HDInsight 클러스터를 만들어야 합니다. HDInsight 클러스터에서 Spark를 만들려면 [HDInsight에서 Spark 클러스터 만들기](https://docs.microsoft.com/azure/hdinsight/spark/apache-spark-jupyter-spark-sql)를 참조하세요.
>
> 클러스터를 만들 때 SSH 사용자 이름 및 암호를 지정해야 합니다. 이러한 값은 HDInsight를 컴퓨팅 대상으로 사용할 때 필요하므로 기록해 둡니다.
>
> 클러스터가 생성된 후 FQDN \<clustername>.azurehdinsight.net이 생깁니다. 여기서 \<clustername>은 클러스터에 대해 사용자가 제공한 이름입니다. 클러스터를 컴퓨팅 대상으로 사용하려면 FQDN 주소(또는 클러스터의 공용 IP 주소)가 필요합니다.

HDInsight를 컴퓨팅 대상으로 구성하려면 HDInsight 클러스터의 FQDN, 사용자 이름 및 암호를 입력해야 합니다. 다음 예제에서는 SDK를 사용하여 작업 영역에 클러스터를 연결합니다. 예제에서는 \<fqdn>을 클러스터의 공용 FQDN 또는 공용 IP 주소로 바꿉니다. \<username> 및 \<password>를 클러스터에 대한 SSH 사용자 이름 및 암호로 바꿉니다.

> [!NOTE]
> 클러스터의 FQDN을 찾으려면 Azure Portal로 이동하여 HDInsight 클러스터를 선택합니다. __개요__에 있는 __URL__ 항목에서 FQDN을 확인할 수 있습니다. FQDN을 가져오려면 항목의 시작 부분에서 https:\// 접두사를 제거합니다.

![Azure Portal에서 HDInsight 클러스터에 대한 FQDN을 가져옵니다.](./media/how-to-set-up-training-targets/hdinsight-overview.png)

```python
from azureml.core.compute import HDInsightCompute, ComputeTarget

try:
    # Attach an HDInsight cluster as a compute target
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

# Configure the HDInsight run
# Load the runconfig object from the myhdi.runconfig file generated in the previous attach operation
run_config = RunConfiguration.load(project_object = project, run_config_name = 'myhdi')

# Ask the system to prepare the conda environment automatically when it's used for the first time
run_config.auto_prepare_environment = True
```

## <a name="submit-training-runs"></a>학습 실행 제출

학습 실행을 제출하는 두 가지 방법은 다음과 같습니다.

* `ScriptRunConfig` 개체를 사용하여 학습 실행을 제출합니다.
* `Pipeline` 개체를 사용하여 학습 실행을 제출합니다.

> [!IMPORTANT]
> Azure Databricks 및 Azure Datalake Analytics 계산 대상은 파이프라인에서만 사용할 수 있습니다.
>
> 로컬 컴퓨팅 대상은 파이프라인에서 사용할 수 없습니다.

### <a name="scriptrunconfig-object"></a>ScriptRunConfig 개체

`ScriptRunConfig` 개체를 사용하여 학습 실행을 제출하는 코드 패턴은 컴퓨팅 대상의 모든 형식에 대해 동일합니다.

1. 컴퓨팅 대상에 대한 실행 구성을 사용하여 `ScriptRunConfig` 개체를 만듭니다.
1. 실행을 제출합니다.
1. 실행이 완료될 때까지 기다립니다.

다음 예제에서는 앞서 만든 시스템 관리 로컬 컴퓨팅 대상을 위한 구성을 사용합니다.

```python
src = ScriptRunConfig(source_directory = script_folder, script = 'train.py', run_config = run_config_system_managed)
run = exp.submit(src)
run.wait_for_completion(show_output = True)
```


### <a name="pipeline-object"></a>파이프라인 개체

`Pipeline` 개체를 사용하여 학습 실행을 제출하는 코드 패턴은 컴퓨팅 대상의 모든 형식에 대해 동일합니다.

1. 컴퓨팅 리소스에 대한 `Pipeline` 개체에 단계를 추가합니다.
1. 파이프라인을 사용하여 실행을 제출합니다.
1. 실행이 완료될 때까지 기다립니다.

다음 예제에서는 앞서 만든 Azure Databricks 컴퓨팅 대상을 사용합니다.

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

# List of steps to run
steps = [dbStep]
pipeline = Pipeline(workspace=ws, steps=steps)
pipeline_run = Experiment(ws, 'Demo_experiment').submit(pipeline)
pipeline_run.wait_for_completion()
```

Machine Learning 파이프라인에 대한 자세한 내용은 [파이프라인 및 Azure Machine Learning](concept-ml-pipelines.md)을 참조하세요.

파이프라인을 사용하여 모델을 학습하는 방법을 설명하는 예제 Jupyter Notebook은 [https://github.com/Azure/MachineLearningNotebooks/tree/master/pipeline](https://github.com/Azure/MachineLearningNotebooks/tree/master/pipeline)을 참조하세요.

## <a name="access-computes-in-the-azure-portal"></a>Azure Portal에서 컴퓨팅에 액세스

Azure Portal에서 작업 영역과 연결된 컴퓨팅 대상에 액세스할 수 있습니다. 

### <a name="view-compute-targets"></a>컴퓨팅 대상 보기

작업 영역에 대한 컴퓨팅 대상을 확인하려면 다음 단계를 사용합니다.

1. [Azure Portal](https://portal.azure.com)로 이동하여 자신의 작업 영역을 엽니다.
1. __애플리케이션__에서 __컴퓨팅__을 선택합니다.

    ![컴퓨팅 대상 보기](./media/how-to-set-up-training-targets/azure-machine-learning-service-workspace.png)

### <a name="create-a-compute-target"></a>계산 대상 만들기

컴퓨팅 대상의 목록을 보려면 이전 단계를 수행합니다. 그런 다음, 다음과 같은 단계를 사용하여 컴퓨팅 대상을 만듭니다.

1. 더하기 기호(+)를 선택하여 컴퓨팅 대상을 추가합니다.

    ![컴퓨팅 대상 추가](./media/how-to-set-up-training-targets/add-compute-target.png)

1. 계산 대상의 이름을 입력합니다.
1. __학습__에 사용할 컴퓨팅 유형으로 **Machine Learning 컴퓨팅**을 선택합니다.

    > [!IMPORTANT]
    > Azure Machine Learning 컴퓨팅 환경만 학습용 관리 컴퓨팅 리소스로 만들 수 있습니다.

1. 양식을 작성합니다. 필요한 속성, 특히 컴퓨팅을 스핀업하는 데 사용하는 **VM 제품군** 및 **최대 노드**에 대한 값을 입력합니다. 
1. __만들기__를 선택합니다.
1. 목록에서 컴퓨팅 대상을 선택하여 만들기 작업의 상태를 봅니다.

    ![만들기 작업 상태를 보려면 컴퓨팅 대상을 선택합니다.](./media/how-to-set-up-training-targets/View_list.png)

1. 그러면 컴퓨팅 대상의 세부 정보가 표시됩니다.

    ![컴퓨팅 대상 세부 정보 보기](./media/how-to-set-up-training-targets/compute-target-details.png)

이제 앞에서 설명한 대로 컴퓨팅 대상에 대해 실행을 제출할 수 있습니다.


### <a name="reuse-existing-compute-targets"></a>기존 컴퓨팅 대상 다시 사용

컴퓨팅 대상의 목록을 보려면 이전에 설명한 단계를 수행합니다. 그런 다음, 다음과 같은 단계를 사용하여 컴퓨팅 대상을 다시 사용합니다.

1. 더하기 기호(+)를 선택하여 컴퓨팅 대상을 추가합니다.
1. 계산 대상의 이름을 입력합니다.
1. __학습__용으로 연결할 컴퓨팅 유형을 선택합니다.

    > [!IMPORTANT]
    > Azure Portal에서 모든 컴퓨팅 유형을 연결할 수 있는 것은 아닙니다.
    > 현재 학습용으로 연결할 수 있는 컴퓨팅 유형은 다음과 같습니다.
    >
    > * 원격 VM
    > * Azure Databricks
    > * Azure 데이터 레이크 분석
    > * Azure HDInsight

1. 양식을 입력하고 필요한 속성에 대한 값을 입력합니다.

    > [!NOTE]
    > Microsoft에서는 암호보다 더 안전한 SSH 키를 권장합니다. 암호는 무차별 암호 대입 공격에 취약합니다. SSH 키는 암호화 서명을 사용합니다. Azure Virtual Machines에 사용할 SSH 키를 만드는 방법에 대한 자세한 내용은 다음 문서를 참조하세요.
    >
    > * [Linux 또는 macOS에서 SSH 키를 만들고 사용](https://docs.microsoft.com/azure/virtual-machines/linux/mac-create-ssh-keys)
    > * [Windows에서 SSH 키를 만들고 사용](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows)

1. __연결__을 선택합니다.
1. 목록에서 컴퓨팅 대상을 선택하여 연결 작업의 상태를 봅니다.

이제 앞에서 설명한 대로 이러한 컴퓨팅 대상에 대해 실행을 제출할 수 있습니다.

## <a name="notebook-examples"></a>Notebook 예제

예제는 다음 위치에 있는 Notebook을 참조하세요.

* [how-to-use-azureml/training](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training)
* [tutorials/img-classification-part1-training.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/tutorials/img-classification-part1-training.ipynb)

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>다음 단계

* [Azure Machine Learning SDK 참조](https://aka.ms/aml-sdk)
* [자습서: 모델 학습](tutorial-train-models-with-aml.md)
* [모델 배포 위치](how-to-deploy-and-where.md)
* [Azure Machine Learning 서비스를 사용하여 기계 학습 파이프라인 빌드](concept-ml-pipelines.md)
