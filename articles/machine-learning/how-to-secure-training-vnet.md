---
title: 가상 네트워크를 사용 하 여 교육 환경 보호
titleSuffix: Azure Machine Learning
description: 격리 된 Azure Virtual Network를 사용 하 여 Azure Machine Learning 교육 환경을 보호 합니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.reviewer: larryfr
ms.author: peterlu
author: peterclu
ms.date: 07/16/2020
ms.custom: contperfq4, tracking-python, contperfq1
ms.openlocfilehash: 6e7499d8402bf31d5ecc4d1b212c08b7064d0446
ms.sourcegitcommit: d479ad7ae4b6c2c416049cb0e0221ce15470acf6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/01/2020
ms.locfileid: "91629729"
---
# <a name="secure-an-azure-machine-learning-training-environment-with-virtual-networks"></a>가상 네트워크를 사용 하 여 Azure Machine Learning 교육 환경 보호

이 문서에서는 Azure Machine Learning의 가상 네트워크를 사용 하 여 교육 환경을 보호 하는 방법에 대해 알아봅니다.

이 문서는 Azure Machine Learning 워크플로를 보호 하는 과정을 안내 하는 다섯 부분으로 구성 된 시리즈 중 3 부입니다. 먼저 전체 아키텍처를 이해 하려면 [1 부: VNet 개요](how-to-network-security-overview.md) 를 읽어 보는 것이 좋습니다. 

이 시리즈의 다른 문서를 참조 하세요.

[1. VNet 개요](how-to-network-security-overview.md)  >  [작업 영역 3을 보호](how-to-secure-workspace-vnet.md)  >  **합니다. 학습 환경 4를 안전 하 게 보호**합니다  >  [. 추론 환경 5를 보호](how-to-secure-inferencing-vnet.md)합니다   >  [. 스튜디오 기능 사용](how-to-enable-studio-virtual-network.md)

이 문서에서는 가상 네트워크에서 다음 교육 계산 리소스를 보호 하는 방법에 대해 알아봅니다.
> [!div class="checklist"]
> - Azure Machine Learning 컴퓨팅 클러스터
> - Azure Machine Learning 컴퓨팅 인스턴스
> - Azure Databricks
> - Virtual Machine
> - HDInsight 클러스터

## <a name="prerequisites"></a>필수 구성 요소

+ 일반적인 가상 네트워크 시나리오 및 전반적인 가상 네트워크 아키텍처를 이해 하려면 [네트워크 보안 개요](how-to-network-security-overview.md) 문서를 참조 하세요.

+ 계산 리소스에 사용할 기존 가상 네트워크 및 서브넷

+ 가상 네트워크 또는 서브넷에 리소스를 배포 하려면 사용자 계정에 Azure 역할 기반 액세스 제어 (RBAC)에서 다음 작업을 수행할 수 있는 권한이 있어야 합니다.

    - 가상 네트워크 리소스에 대 한 "Microsoft. Network/virtualNetworks/join/action".
    - 서브넷 리소스에 대 한 "Microsoft. Network/virtualNetworks/subnet/join/action".

    네트워크를 사용 하는 RBAC에 대 한 자세한 내용은 [네트워킹 기본 제공 역할](/azure/role-based-access-control/built-in-roles#networking) 을 참조 하세요.


## <a name="compute-clusters--instances"></a><a name="compute-instance"></a>컴퓨팅 클러스터 및 인스턴스 

가상 네트워크에서 [관리형 Azure Machine Learning __컴퓨팅 대상__](concept-compute-target.md#azure-machine-learning-compute-managed) 또는 [Azure Machine Learning 컴퓨팅 __인스턴스__](concept-compute-instance.md) 중에 하나를 사용하려면 다음 네트워크 요구 사항을 충족해야 합니다.

> [!div class="checklist"]
> * 가상 네트워크가 Azure Machine Learning 작업 영역과 동일한 구독 및 지역에 있어야 합니다.
> * 컴퓨팅 인스턴스 또는 클러스터에 지정된 서브넷에는 할당되지 않은 IP 주소가 대상 VM 수를 수용할 만큼 충분히 있어야 합니다. 서브넷에 할당되지 않은 IP 주소가 충분하지 않으면 컴퓨팅 클러스터가 부분적으로 할당됩니다.
> * 가상 네트워크의 구독 또는 리소스 그룹에 대한 보안 정책이나 잠금이 가상 네트워크를 관리하는 사용자 권한을 제한하는지 확인합니다. 트래픽을 제한하여 가상 네트워크를 보호하려는 경우 컴퓨팅 서비스를 위해 일부 포트를 열어둡니다. 자세한 내용은 [필수 포트](#mlcports) 섹션을 참조하세요.
> * 하나의 가상 네트워크에 다수의 컴퓨팅 인스턴스나 클러스터를 배치하려는 경우 하나 이상의 리소스에 대해 할당량 증가를 요청해야 할 수 있습니다.
> * 작업 영역의 Azure Storage 계정도 가상 네트워크에서 보호되는 경우, Azure Machine Learning 컴퓨팅 인스턴스나 클러스터와 동일한 가상 네트워크에 있어야 합니다. 
> * 컴퓨팅 인스턴스 Jupyter 기능이 작동하려면 웹 소켓 통신이 비활성화되어 있지 않아야 합니다. 네트워크에서 *. instances.azureml.net 및 *. instances.azureml.ms에 대 한 websocket 연결을 허용 하는지 확인 하세요.

> [!TIP]
> Machine Learning 컴퓨팅 인스턴스나 클러스터는 __가상 네트워크를 포함하는 리소스 그룹__에 추가 네트워킹 리소스를 자동으로 할당합니다. 각 컴퓨팅 인스턴스 또는 클러스터에 대해 서비스는 다음 리소스를 할당합니다.
> 
> * 네트워크 보안 그룹 1개
> * 공용 IP 주소 1개
> * 부하 분산 장치 1개
> 
> 클러스터의 경우 이러한 리소스는 클러스터가 0개 노드로 축소될 때마다 삭제(재생성)되지만, 인스턴스의 경우 인스턴스가 완전히 삭제될 때까지 리소스가 유지됩니다(중지하면 리소스가 제거되지 않음). 
> 이러한 리소스는 구독의 [리소스 할당량](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits)으로 제한됩니다.


### <a name="required-ports"></a><a id="mlcports"></a> 필수 포트

공용 인터넷으로/에서 네트워크 트래픽을 제한 하 여 가상 네트워크를 보호 하려는 경우 Azure Batch 서비스에서 인바운드 통신을 허용 해야 합니다.

Batch 서비스는 VM에 연결된 NIC(네트워크 인터페이스) 수준에서 NSG(네트워크 보안 그룹)를 추가합니다. 이러한 NSG는 다음 트래픽을 허용하도록 인바운드 및 아웃바운드 규칙을 자동으로 구성합니다.

- __BatchNodeManagement__의 __서비스 태그__ 포트 29876 및 29877의 인바운드 TCP 트래픽

    ![BatchNodeManagement 서비스 태그를 사용하는 인바운드 규칙](./media/how-to-enable-virtual-network/batchnodemanagement-service-tag.png)

- (선택 사항) 원격 액세스를 허용하는 포트 22의 인바운드 TCP 트래픽 이 포트는 공용 IP에서 SSH를 사용하여 연결하려는 경우에만 사용합니다.

- 가상 네트워크에 대한 모든 포트의 아웃바운드 트래픽

- 인터넷에 대한 모든 포트의 아웃바운드 트래픽

- __AzureMachineLearning__의 __서비스 태그__ 포트 44224의 컴퓨팅 인스턴스 인바운드 TCP 트래픽

> [!IMPORTANT]
> Batch 구성 NSG에서 인바운드 또는 아웃바운드 규칙을 수정하거나 추가할 경우 주의가 필요합니다. NSG가 컴퓨팅 노드에 대한 통신을 차단하는 경우 컴퓨팅 서비스는 컴퓨팅 노드의 상태를 사용 불가로 설정합니다.
>
> Azure Batch 서비스는 자체 NSG를 구성하기 때문에 서브넷 수준에서 NSG를 지정할 필요는 없습니다. 그러나 Azure Machine Learning 계산을 포함 하는 서브넷에 연결 된 NSGs 또는 방화벽이 있는 경우 앞에 나열 된 트래픽만 허용 해야 합니다.

Azure Portal의 NSG 규칙 구성은 다음 이미지에 나와 있습니다.

:::image type="content" source="./media/how-to-enable-virtual-network/amlcompute-virtual-network-inbound.png" alt-text="Machine Learning 컴퓨팅에 대한 인바운드 NSG 규칙" border="true":::



![Machine Learning 컴퓨팅에 대 한 인바운드 NSG 규칙](./media/how-to-enable-virtual-network/experimentation-virtual-network-outbound.png)

### <a name="limit-outbound-connectivity-from-the-virtual-network"></a><a id="limiting-outbound-from-vnet"></a> 가상 네트워크에서 아웃바운드 연결 제한

기본 아웃바운드 규칙을 사용하지 않고 가상 네트워크의 아웃바운드 액세스를 제한하려면 다음 단계를 사용합니다.

- NSG 규칙을 사용하여 아웃바운드 인터넷 연결을 거부합니다.

- __컴퓨팅 인스턴스__ 또는 __컴퓨팅 클러스터__의 경우 다음 항목에 대한 아웃바운드 트래픽을 제한합니다.
   - Azure Storage, __Storage.RegionName__의 __서비스 태그__를 사용합니다. 여기서 `{RegionName}`은 Azure 지역의 이름입니다.
   - Azure Container Registry, __AzureContainerRegistry.RegionName__의 __서비스 태그__를 사용합니다. 여기서 `{RegionName}`은 Azure 지역의 이름입니다.
   - Azure Machine Learning, __AzureMachineLearning__의 __서비스 태그__를 사용합니다.
   - Azure Resource Manager, __AzureResourceManager__의 __서비스 태그__를 사용합니다.
   - Azure Active Directory, __AzureActiveDirectory__의 __서비스 태그__를 사용합니다.

Azure Portal의 NSG 규칙 구성은 다음 이미지에 나와 있습니다.

[![Machine Learning 컴퓨팅에 대한 아웃바운드 NSG 규칙](./media/how-to-enable-virtual-network/limited-outbound-nsg-exp.png)](./media/how-to-enable-virtual-network/limited-outbound-nsg-exp.png#lightbox)

> [!NOTE]
> Microsoft에서 제공 하는 기본 Docker 이미지를 사용 하 고 사용자 관리 종속성을 사용 하도록 계획 하는 경우 다음 __서비스 태그도__사용 해야 합니다.
>
> * __MicrosoftContainerRegistry__
> * __AzureFrontDoor.FirstParty__
>
> 이 구성은 학습 스크립트의 일부에 다음 코드 조각과 비슷한 코드가 있는 경우 필요합니다.
>
> __RunConfig 교육__
> ```python
> # create a new runconfig object
> run_config = RunConfiguration()
> 
> # configure Docker 
> run_config.environment.docker.enabled = True
> # For GPU, use DEFAULT_GPU_IMAGE
> run_config.environment.docker.base_image = DEFAULT_CPU_IMAGE 
> run_config.environment.python.user_managed_dependencies = True
> ```
>
> __Estimator 교육__
> ```python
> est = Estimator(source_directory='.',
>                 script_params=script_params,
>                 compute_target='local',
>                 entry_script='dummy_train.py',
>                 user_managed=True)
> run = exp.submit(est)
> ```

### <a name="forced-tunneling"></a>강제 터널링

Azure Machine Learning compute를 사용 하 여 [강제 터널링](/azure/vpn-gateway/vpn-gateway-forced-tunneling-rm) 을 사용 하는 경우 계산 리소스가 포함 된 서브넷에서 공용 인터넷과의 통신을 허용 해야 합니다. 이 통신은 작업 예약 및 Azure Storage 액세스에 사용 됩니다.

다음 두 가지 방법으로이 작업을 수행할 수 있습니다.

* [VIRTUAL NETWORK NAT](../virtual-network/nat-overview.md)를 사용 합니다. NAT 게이트웨이는 가상 네트워크에 있는 하나 이상의 서브넷에 대해 아웃 바운드 인터넷 연결을 제공 합니다. 자세한 내용은 [NAT 게이트웨이 리소스를 사용 하 여 가상 네트워크 디자인](../virtual-network/nat-gateway-resource.md)을 참조 하세요.

* 계산 리소스를 포함 하는 서브넷에 [UDRs (사용자 정의 경로)](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview) 를 추가 합니다. 리소스가 있는 지역에서 Azure Batch 서비스에 사용되는 각 IP 주소에 대한 UDR을 설정합니다. 이러한 UDR을 사용하면 Batch 서비스가 작업 예약을 위해 컴퓨팅 노드와 통신할 수 있습니다. 리소스가 있는 Azure Machine Learning Service의 IP 주소도 추가합니다. 컴퓨팅 인스턴스에 액세스하는 데 필요하기 때문입니다. Batch 서비스 및 Azure Machine Learning Service의 IP 주소 목록을 가져오려면 다음 방법 중 하나를 사용합니다.

    * [Azure IP 범위 및 서비스 태그](https://www.microsoft.com/download/details.aspx?id=56519)를 다운로드하고 파일에서 `BatchNodeManagement.<region>` 및 `AzureMachineLearning.<region>`을 검색합니다. 여기서 `<region>`은 Azure 지역입니다.

    * [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true)를 사용하여 정보를 다운로드합니다. 다음 예는 IP 주소 정보를 다운로드하고 미국 동부 2 지역에 대한 정보를 필터링합니다.

        ```azurecli-interactive
        az network list-service-tags -l "East US 2" --query "values[?starts_with(id, 'Batch')] | [?properties.region=='eastus2']"
        az network list-service-tags -l "East US 2" --query "values[?starts_with(id, 'AzureMachineLearning')] | [?properties.region=='eastus2']"
        ```

        > [!TIP]
        > 미국 버지니아, 미국-애리조나 지역 또는 중국 동부-2 지역을 사용 하는 경우 이러한 명령은 IP 주소를 반환 하지 않습니다. 대신, 다음 링크 중 하나를 사용 하 여 IP 주소 목록을 다운로드 합니다.
        >
        > * [Azure Government에 대 한 Azure IP 범위 및 서비스 태그](https://www.microsoft.com/download/details.aspx?id=57063)
        > * [Azure 중국의 azure IP 범위 및 서비스 태그](https://www.microsoft.com//download/details.aspx?id=57062)
    
    UDR을 추가할 때 관련된 각 Batch IP 주소 접두사에 대한 경로를 정의하고 __다음 홉 유형__을 __인터넷__으로 설정합니다. 다음 이미지는 Azure Portal에서 UDR의 예를 보여 줍니다.

    ![주소 접두사에 대한 UDR 예](./media/how-to-enable-virtual-network/user-defined-route.png)

    > [!IMPORTANT]
    > IP 주소는 시간이 지남에 따라 변경 될 수 있습니다.

    사용자가 정의 하는 UDRs 외에도 온-프레미스 네트워크 어플라이언스를 통해 Azure Storage에 대 한 아웃 바운드 트래픽을 허용 해야 합니다. 특히이 트래픽의 Url은 `<account>.table.core.windows.net` , `<account>.queue.core.windows.net` 및 형식 `<account>.blob.core.windows.net` 입니다. 

    자세한 내용은 [가상 네트워크에서 Azure Batch 풀 만들기](../batch/batch-virtual-network.md#user-defined-routes-for-forced-tunneling)를 참조하세요.


### <a name="create-a-compute-cluster-in-a-virtual-network"></a>가상 네트워크에서 컴퓨팅 클러스터 만들기

Machine Learning 컴퓨팅 클러스터를 만들려면 다음 단계를 사용하세요.

1. [Azure Machine Learning 스튜디오](https://ml.azure.com/)에 로그인한 다음, 구독과 작업 영역을 선택합니다.

1. 왼쪽에서 __Compute__를 선택합니다.

1. 가운데에서 __학습 클러스터__를 선택한 다음, __+__ 를 선택합니다.

1. __새 학습 클러스터__ 대화 상자에서 __고급 설정__ 섹션을 확장합니다.

1. 이 컴퓨팅 리소스가 가상 네트워크를 사용하도록 구성하려면 __가상 네트워크 구성__ 섹션에서 다음 작업을 수행합니다.

    1. __리소스 그룹__ 드롭다운 목록에서 가상 네트워크가 포함된 리소스 그룹을 선택합니다.
    1. __가상 네트워크__ 드롭다운 목록에서 서브넷이 포함된 가상 네트워크를 선택합니다.
    1. __서브넷__ 드롭다운 목록에서 사용할 서브넷을 선택합니다.

   ![Machine Learning 컴퓨팅에 대한 가상 네트워크 설정](./media/how-to-enable-virtual-network/amlcompute-virtual-network-screen.png)

Azure Machine Learning SDK를 사용하여 Machine Learning 컴퓨팅 클러스터를 만들 수도 있습니다. 다음 코드에서는 `mynetwork`라는 가상 네트워크의 `default` 서브넷에 새 Machine Learning 컴퓨팅 클러스터를 만듭니다.

```python
from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException

# The Azure virtual network name, subnet, and resource group
vnet_name = 'mynetwork'
subnet_name = 'default'
vnet_resourcegroup_name = 'mygroup'

# Choose a name for your CPU cluster
cpu_cluster_name = "cpucluster"

# Verify that cluster does not exist already
try:
    cpu_cluster = ComputeTarget(workspace=ws, name=cpu_cluster_name)
    print("Found existing cpucluster")
except ComputeTargetException:
    print("Creating new cpucluster")

    # Specify the configuration for the new cluster
    compute_config = AmlCompute.provisioning_configuration(vm_size="STANDARD_D2_V2",
                                                           min_nodes=0,
                                                           max_nodes=4,
                                                           vnet_resourcegroup_name=vnet_resourcegroup_name,
                                                           vnet_name=vnet_name,
                                                           subnet_name=subnet_name)

    # Create the cluster with the specified name and configuration
    cpu_cluster = ComputeTarget.create(ws, cpu_cluster_name, compute_config)

    # Wait for the cluster to be completed, show the output log
    cpu_cluster.wait_for_completion(show_output=True)
```

만들기 프로세스가 완료되면 실험에서 클러스터를 사용하여 모델을 학습시킵니다. 자세한 내용은 [학습의 컴퓨팅 대상 선택 및 사용](how-to-set-up-training-targets.md)을 참조하세요.

[!INCLUDE [low-pri-note](../../includes/machine-learning-low-pri-vm.md)]

### <a name="access-data-in-a-compute-instance-notebook"></a>계산 인스턴스 노트북에서 데이터 액세스

Azure 계산 인스턴스에서 노트북을 사용 하는 경우 노트북이 동일한 가상 네트워크 및 서브넷 뒤에 있는 계산 리소스에서 실행 되 고 있는지 확인 해야 합니다. 

계산 인스턴스는 생성 하는 동안 동일한 가상 네트워크에 있도록 구성 해야 합니다. **고급 설정**  >  **가상 네트워크 구성**합니다. 기존 계산 인스턴스를 가상 네트워크에 추가할 수 없습니다.

## <a name="azure-databricks"></a>Azure Databricks

작업 영역이 있는 가상 네트워크에서 Azure Databricks를 사용하려면 다음 요구 사항을 충족해야 합니다.

> [!div class="checklist"]
> * 가상 네트워크가 Azure Machine Learning 작업 영역과 동일한 구독 및 지역에 있어야 합니다.
> * 작업 영역의 Azure Storage 계정도 가상 네트워크에서 보호되는 경우, Azure Databricks 클러스터와 동일한 가상 네트워크에 있어야 합니다.
> * Azure Databricks에 사용되는 __databricks-private__ 및 __databricks-public__ 서브넷 외에 가상 네트워크에 대해 생성된 __기본__ 서브넷도 필요합니다.

가상 네트워크에서 Azure Databricks를 사용하는 방법에 대한 자세한 내용은 [Azure Virtual Network에서 Azure Databricks 배포](https://docs.azuredatabricks.net/administration-guide/cloud-configurations/azure/vnet-inject.html)를 참조하세요.

<a id="vmorhdi"></a>

## <a name="virtual-machine-or-hdinsight-cluster"></a>가상 머신 또는 HDInsight 클러스터

> [!IMPORTANT]
> Azure Machine Learning은 Ubuntu를 실행하는 가상 머신만 지원합니다.

이 섹션에서는 가상 컴퓨터 또는 가상 네트워크에서 작업 영역을 사용 하는 Azure HDInsight 클러스터를 사용 하는 방법에 대해 알아봅니다.

### <a name="create-the-vm-or-hdinsight-cluster"></a>VM 또는 HDInsight 클러스터 만들기

Azure Portal 또는 Azure CLI를 사용하여 VM 또는 HDInsight 클러스터를 만들고 이 클러스터를 Azure 가상 네트워크에 배치합니다. 자세한 내용은 다음 문서를 참조하세요.
* [Linux VM용 Azure 가상 네트워크 만들기 및 관리](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-virtual-network)

* [Azure 가상 네트워크를 사용하여 HDInsight 확장](https://docs.microsoft.com/azure/hdinsight/hdinsight-extend-hadoop-virtual-network)

### <a name="configure-network-ports"></a>네트워크 포트 구성 

Azure Machine Learning VM 또는 클러스터의 SSH 포트와 통신할 수 있도록 허용 하 고, 네트워크 보안 그룹에 대 한 원본 항목을 구성 합니다. SSH 포트는 일반적으로 포트 22입니다. 이 원본의 트래픽을 허용하려면 다음 작업을 수행합니다.

1. __원본__ 드롭다운 목록에서 __서비스 태그__를 선택합니다.

1. __원본 서비스 태그__ 드롭다운 목록에서 __AzureMachineLearning__을 선택합니다.

    ![가상 네트워크 내의 VM 또는 HDInsight 클러스터에서 실험을 수행하기 위한 인바운드 규칙](./media/how-to-enable-virtual-network/experimentation-virtual-network-inbound.png)

1. __원본 포트 범위__ 드롭다운 목록에서 __*__ 를 선택합니다.

1. __대상__ 드롭다운 목록에서 __임의__를 선택합니다.

1. __대상 포트 범위__ 드롭다운 목록에서 __22__를 선택합니다.

1. __프로토콜__에서 __임의__를 선택합니다.

1. __동작__에서 __허용__을 선택합니다.

네트워크 보안 그룹에 대해 기본 아웃바운드 규칙을 유지합니다. 자세한 내용은 [보안 그룹](https://docs.microsoft.com/azure/virtual-network/security-overview#default-security-rules)의 기본 보안 규칙을 참조하세요.

기본 아웃바운드 규칙을 사용하지 않고 가상 네트워크의 아웃바운드 액세스를 제한하려면 [가상 네트워크에서 아웃바운드 연결 제한](#limiting-outbound-from-vnet) 섹션을 참조하세요.

### <a name="attach-the-vm-or-hdinsight-cluster"></a>VM 또는 HDInsight 클러스터 연결

VM 또는 HDInsight 클러스터를 Azure Machine Learning 작업 영역에 연결합니다. 자세한 내용은 [모델 학습의 컴퓨팅 대상 설정](how-to-set-up-training-targets.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

이 문서는 네 부분으로 구성 된 가상 네트워크 시리즈의 3 부입니다. 가상 네트워크를 보호 하는 방법을 알아보려면 나머지 문서를 참조 하세요.

* [1 부: 가상 네트워크 개요](how-to-network-security-overview.md)
* [2 부: 작업 영역 리소스 보안](how-to-secure-workspace-vnet.md)
* [4 부: 추론 환경 보안](how-to-secure-inferencing-vnet.md)
* [5 부: studio 기능 사용](how-to-enable-studio-virtual-network.md)
