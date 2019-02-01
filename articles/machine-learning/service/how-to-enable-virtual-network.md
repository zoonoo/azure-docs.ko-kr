---
title: 가상 네트워크에서 실험 및 유추 실행
titleSuffix: Azure Machine Learning service
description: Azure Virtual Network 내에서 안전하게 기계 학습 실험 및 추론을 실행합니다. 모델 학습에 대한 컴퓨팅 대상을 만드는 방법과 Azure Virtual Network 내에서 유추하는 방법을 알아봅니다. 인바운드 및 아웃바운드 포트 요구와 같은 보안 가상 네트워크에 대한 요구 사항도 다룹니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: aashishb
author: aashishb
ms.date: 01/08/2019
ms.openlocfilehash: fb67821d883317901617bda101ae91a9a92018c2
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/30/2019
ms.locfileid: "55246401"
---
# <a name="securely-run-experiments-and-inferencing-inside-an-azure-virtual-network"></a>Azure Virtual Network 내에서 안전하게 실험 및 추론 실행

이 문서에서는 Virtual Network 내에서 실험 및 추론을 실행하는 방법을 알아봅니다. 가상 네트워크는 공용 인터넷에서 Azure 리소스를 격리하는 보안 경계의 역할을 합니다. 또한 Azure Virtual Network를 온-프레미스 네트워크에 연결할 수도 있습니다. 이렇게 하면 모델을 안전하게 학습하고, 배포한 모델에 액세스하여 추론을 수행할 수 있습니다.

Azure Machine Learning 서비스는 컴퓨팅 리소스 등의 다른 Azure 서비스에 의존합니다. 컴퓨팅 리소스(컴퓨팅 대상)는 모델을 학습 및 배포하는 데 사용합니다. 이러한 컴퓨팅 대상을 가상 네트워크 내에서 만들 수 있습니다. 예를 들어, Data Science VM을 사용하여 모델을 학습한 다음, Azure Kubernetes Service에 모델을 배포할 수 있습니다. 가상 네트워크에 대한 자세한 내용은 [가상 네트워크 개요](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview)를 참조하세요.

## <a name="storage-account-for-your-workspace"></a>작업 영역에 대한 스토리지 계정

Azure Machine Learning 서비스 작업 영역을 만들 때는 Azure Storage 계정이 필요합니다. 이 스토리지 계정에 대해서는 방화벽 규칙을 켜지 마세요. Azure Machine Learning 서비스는 스토리지 계정에 대해 무제한 액세스 권한이 필요합니다.

이러한 설정을 수정했는지 여부가 확실하지 않으면 [Azure Storage 방화벽 및 가상 네트워크 구성](https://docs.microsoft.com/azure/storage/common/storage-network-security) 문서의 __기본 네트워크 액세스 규칙 변경__ 섹션을 참조하거나 __모든 네트워크__의 _액세스를 허용_하는 단계를 사용합니다.

## <a name="use-machine-learning-compute"></a>Machine Learning Compute 사용

가상 네트워크에서 Machine Learning Compute를 사용하려면 다음 정보를 참조하여 네트워크 요구 사항을 이해합니다.

- 가상 네트워크는 Azure Machine Learning 서비스 작업 영역과 동일한 구독 및 지역에 있어야 합니다.

- Machine Learning Compute 클러스터에 대해 지정한 서브넷에는 클러스터의 대상이 되는 VM 수를 수용할 만큼 충분한 할당되지 않은 IP 주소가 있어야 합니다. 서브넷에 충분한 할당되지 않은 IP 주소가 없으면 클러스터는 부분적으로 할당됩니다.

- 트래픽을 제한하여 가상 네트워크를 보호하려는 경우 Machine Learning Compute 서비스를 위해 일부 포트를 열어 두어야 합니다. 자세한 내용은 [필수 포트](#mlcports) 섹션을 참조하세요.

- 가상 네트워크 구독 또는 리소스 그룹의 보안 정책이나 잠금이 가상 네트워크 관리를 위한 사용자 권한을 제한하고 있는지 확인합니다.

- 하나의 가상 네트워크에 여러 Machine Learning Compute 클러스터를 배치하려는 경우 하나 이상의 리소스에 대해 할당량 증가를 요청해야 할 수 있습니다.

    Machine Learning Compute는 가상 네트워크를 포함하는 리소스 그룹에서 추가 네트워킹 리소스를 자동으로 할당합니다. 각 Machine Learning Compute 클러스터에 대해 Azure Machine Learning 서비스는 다음 리소스를 할당합니다. 

    - 하나의 NSG(네트워크 보안 그룹)

    - 공용 IP 주소 1개

    - 부하 분산 장치 1개

    이러한 리소스는 구독의 [리소스 할당량](https://docs.microsoft.com/azure/azure-subscription-service-limits)으로 제한됩니다. 

### <a id="mlcports"></a> 필수 포트

Machine Learning Compute는 현재 Azure Batch 서비스를 사용하여 지정한 가상 네트워크에서 VM을 프로비전합니다. 서브넷은 Batch 서비스에서의 인바운드 통신을 허용해야 합니다. 이 통신은 Machine Learning Compute 노드에서의 실행을 예약하고 Azure Storage 및 기타 리소스와 통신하는 데 사용합니다. Batch는 VM에 연결된 NIC(네트워크 인터페이스) 수준에서 NSG를 추가합니다. 이러한 NSG는 다음 트래픽을 허용하도록 인바운드 및 아웃바운드 규칙을 자동으로 구성합니다.

- 29876 및 29877 포트에서 Batch 서비스 역할 IP 주소로부터의 인바운드 TCP 트래픽  
 
- 원격 액세스를 허용하기 위한 포트 22의 인바운드 TCP 트래픽
 
- 가상 네트워크에 대한 모든 포트의 아웃바운드 트래픽

- 인터넷에 대한 모든 포트의 아웃바운드 트래픽

Batch 구성 NSG에서 인바운드/아웃바운드 규칙을 수정하거나 추가할 경우 주의가 필요합니다. NSG가 컴퓨팅 노드에 대한 통신을 차단하는 경우 Machine Learning Compute 서비스는 컴퓨팅 노드의 상태를 사용 불가로 설정합니다.

Batch 구성에는 자체 NSG가 있으므로 서브넷 수준에서 NSG를 지정할 필요가 없습니다. 그러나 지정된 서브넷에 연결된 NSG 및/또는 방화벽이 있는 경우 앞서 언급한 것처럼 인바운드 및 아웃바운드 보안 규칙을 구성합니다. 다음 스크린샷은 Azure Portal에서 규칙 구성이 표시되는 방식을 보여 줍니다.

![Machine Learning Compute에 대한 인바운드 NSG 규칙 스크린샷](./media/how-to-enable-virtual-network/amlcompute-virtual-network-inbound.png)

![Machine Learning Compute에 대한 아웃바운드 NSG 규칙 스크린샷](./media/how-to-enable-virtual-network/experimentation-virtual-network-outbound.png)

### <a name="create-machine-learning-compute-in-a-virtual-network"></a>가상 네트워크에서 Machine Learning Compute 만들기

**Azure Portal**을 사용하여 Machine Learning Compute 클러스터를 만들려면 다음 단계를 사용하세요.

1. [Azure Portal](https://portal.azure.com)에서 Azure Machine Learning 서비스 작업 영역을 선택합니다.

1. __애플리케이션__ 섹션에서 __컴퓨팅__을 선택합니다. 그런 후 __컴퓨팅 추가__를 선택합니다. 

    ![Azure Machine Learning 서비스에서 컴퓨팅을 추가하는 방법](./media/how-to-enable-virtual-network/add-compute.png)

1. 가상 네트워크를 사용하도록 이 컴퓨팅 리소스를 구성하려면 다음 옵션을 사용합니다.

    - __네트워크 구성__: __고급__을 선택합니다.

    - __리소스 그룹__: 가상 네트워크를 포함하는 리소스 그룹을 선택합니다.

    - __가상 네트워크__: 서브넷을 포함하는 가상 네트워크를 선택합니다.

    - __서브넷__: 사용할 서브넷을 선택합니다.

    ![Machine learning Compute에 대한 가상 네트워크 설정을 보여 주는 스크린샷](./media/how-to-enable-virtual-network/amlcompute-virtual-network-screen.png)

**Azure Machine Learning SDK**를 사용하여 Machine Learning Compute 클러스터를 만들 수도 있습니다. 다음 코드에서는 `mynetwork`라는 가상 네트워크의 `default` 서브넷에 새 Machine Learning Compute 클러스터를 만듭니다.

```python
from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException

# The Azure Virtual Network name, subnet, and resource group
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
                                                           vnet_resourcegroup_name = vnet_resourcegroup_name,
                                                           vnet_name = vnet_name,
                                                           subnet_name = subnet_name)

    # Create the cluster with the specified name and configuration
    cpu_cluster = ComputeTarget.create(ws, cpu_cluster_name, compute_config)
    
    # Wait for the cluster to complete, show the output log
    cpu_cluster.wait_for_completion(show_output=True)
```

만들기 프로세스가 완료되면 클러스터를 사용하여 모델을 학습할 수 있습니다. 자세한 내용은 [학습을 위한 컴퓨팅 대상 선택 및 사용](how-to-set-up-training-targets.md) 문서를 참조하세요.

## <a name="use-a-virtual-machine-or-hdinsight"></a>가상 머신 또는 HDInsight 사용

작업 영역에 Virtual Network의 가상 머신 또는 HDInsight 클러스터를 사용하려면 다음 단계를 사용합니다.

> [!IMPORTANT]
> Azure Machine Learning 서비스는 Ubuntu를 실행하는 가상 머신만 지원합니다.

1. Azure Portal, Azure CLI 등을 사용하여 VM 또는 HDInsight 클러스터를 만든 후 Azure Virtual Network에 배치합니다. 자세한 내용은 다음 문서를 참조하세요.
    * [Linux VM을 위한 Azure Virtual Network 만들기 및 관리](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-virtual-network)

    * [Azure Virtual Network를 사용하여 HDInsight 확장](https://docs.microsoft.com/azure/hdinsight/hdinsight-extend-hadoop-virtual-network) 

1. Azure Machine Learning 서비스가 VM 또는 클러스터에서 SSH 포트와 통신할 수 있도록 하려면 NSG에 대한 원본 항목을 구성해야 합니다. SSH 포트는 일반적으로 포트 22입니다. 이 원본의 트래픽을 허용하려면 다음 정보를 사용합니다.

    * __원본__: __서비스 태그__를 선택합니다.

    * __원본 서비스 태그__: __AzureMachineLearning__ 선택

    * __원본 포트 범위__: __*__ 선택

    * __대상__: __Any__ 선택

    * __대상 포트 범위__: __22__ 선택

    * __프로토콜__: __Any__ 선택

    * __작업__: __허용__ 선택

   ![가상 네트워크 내에서 VM 또는 HDInsight에 대해 실험을 수행하기 위한 인바운드 규칙 스크린샷](./media/how-to-enable-virtual-network/experimentation-virtual-network-inbound.png)

    NSG에 대한 기본 아웃바운드 규칙을 유지합니다. 자세한 내용은 [보안 그룹](https://docs.microsoft.com/azure/virtual-network/security-overview#default-security-rules) 설명서의 기본 보안 규칙 섹션을 참조하세요.
    
1. VM 또는 HDInsight 클러스터를 Azure Machine Learning 서비스 작업 영역에 연결합니다. 자세한 내용은 [모델 학습을 위한 컴퓨팅 대상 설정](how-to-set-up-training-targets.md) 문서를 참조하세요.

## <a name="use-azure-kubernetes-service-aks"></a>AKS(Azure Kubernetes Service) 사용

> [!IMPORTANT]
> 아래 언급된 단계를 진행하기 전에 필수 구성 요소를 확인하고 클러스터의 IP 주소 지정을 계획하세요. [AKS(Azure Kubernetes Service)에서 고급 네트워킹 구성](https://docs.microsoft.com/azure/aks/configure-advanced-networking)을 참조할 수 있습니다.
> 
> NSG에 대한 기본 아웃바운드 규칙을 유지합니다. 자세한 내용은 [보안 그룹](https://docs.microsoft.com/azure/virtual-network/security-overview#default-security-rules) 설명서의 기본 보안 규칙 섹션을 참조하세요.
>
> Azure Kubernetes Service 및 Azure Virtual Network를 동일한 지역에 있어야 합니다.

Virtual Network의 Azure Kubernetes Service를 작업 영역에 추가하려면 __Azure Portal__에서 다음 단계를 사용합니다.

1. [Azure Portal](https://portal.azure.com)에서 Azure Machine Learning 서비스 작업 영역을 선택합니다.

1. __애플리케이션__ 섹션에서 __컴퓨팅__을 선택합니다. 그런 후 __컴퓨팅 추가__를 선택합니다. 

    ![Azure Machine Learning 서비스에서 컴퓨팅을 추가하는 방법](./media/how-to-enable-virtual-network/add-compute.png)

1. 가상 네트워크를 사용하도록 이 컴퓨팅 리소스를 구성하려면 다음 옵션을 사용합니다.

    - __네트워크 구성__: __고급__을 선택합니다.

    - __리소스 그룹__: 가상 네트워크를 포함하는 리소스 그룹을 선택합니다.

    - __가상 네트워크__: 서브넷을 포함하는 가상 네트워크를 선택합니다.

    - __서브넷__: 서브넷을 선택합니다.

    - __Kubernetes 서비스 주소 범위__: Kubernetes 서비스 주소 범위를 선택합니다. 이 주소 범위는 CIDR 표기법 IP 범위를 사용하여 클러스터에 사용할 수 있는 IP 주소를 정의합니다. 서브넷 IP 범위와 겹치지 않아야 합니다. 예:  10.0.0.0/16.

    - __Kubernetes DNS 서비스 IP 주소__: Kubernetes DNS 서비스 IP 주소를 선택합니다. 이 IP 주소는 Kubernetes DNS 서비스에 할당됩니다. 이 주소는 Kubernetes 서비스 주소 범위에 속해야 합니다. 예:  10.0.0.10.

    - __Docker 브리지 주소__: Docker 브리지 주소를 선택합니다. 이 IP 주소는 Docker 브리지에 할당됩니다. 서브넷 IP 범위 또는 Kubernetes 서비스 주소 범위에 속하지 않아야 합니다. 예:  172.17.0.1/16

   ![Azure Machine Learning 서비스: Machine Learning Compute Virtual Network 설정](./media/how-to-enable-virtual-network/aks-virtual-network-screen.png)

    > [!TIP]
    > Virtual Network에 AKS 클러스터가 이미 있는 경우 작업 영역에 연결할 수 있습니다. 자세한 내용은 [AKS에 배포하는 방법](how-to-deploy-to-aks.md)을 참조하세요.

**Azure Machine Learning SDK**를 사용하여 Virtual Network에서 Azure Kubernetes 서비스를 추가할 수도 있습니다. 다음 코드에서는 `mynetwork`이라는 가상 네트워크의 `default` 서브넷에 새 Azure Kubernetes 서비스를 만듭니다.

```python
from azureml.core.compute import ComputeTarget, AksCompute

# Create the compute configuration and set virtual network information
config = AksCompute.provisioning_configuration(location="eastus2")
config.vnet_resourcegroup_name = "mygroup"
config.vnet_name = "mynetwork"
config.subnet_name = "default"
config.service_cidr = "10.0.0.0/16"
config.dns_service_ip = "10.0.0.10"
config.docker_bridge_cidr = "172.17.0.1/16"

# Create the compute target
aks_target = ComputeTarget.create(workspace = ws,
                                  name = "myaks",
                                  provisioning_configuration = config)
```

만들기 프로세스가 완료되면 가상 네트워크에 가려져 있는 AKS 클러스터에 대해 추론을 수행할 수 있습니다. 자세한 내용은 [AKS에 배포하는 방법](how-to-deploy-to-aks.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

* [학습 환경 설정](how-to-set-up-training-targets.md)
* [모델 배포 위치](how-to-deploy-and-where.md)
* [SSL을 사용하여 배포된 모델 보호](how-to-secure-web-service.md)
