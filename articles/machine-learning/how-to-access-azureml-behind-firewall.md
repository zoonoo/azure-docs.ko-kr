---
title: 방화벽 사용
titleSuffix: Azure Machine Learning
description: Azure Firewall을 사용하여 Azure Machine Learning 작업 영역에 대한 액세스를 제어합니다. 방화벽을 통해 허용해야 하는 호스트에 대해 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 05/11/2021
ms.custom: devx-track-python
ms.openlocfilehash: 9eb8d8b5bd7b840314b2943a6696b8b4e989b2b8
ms.sourcegitcommit: 0ce834cd348bb8b28a5f7f612c2807084cde8e8f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/12/2021
ms.locfileid: "109814355"
---
# <a name="use-workspace-behind-a-firewall-for-azure-machine-learning"></a>Azure Machine Learning에 대해 방화벽 뒤의 작업 영역 사용

이 문서에서는 Azure Machine Learning 작업 영역 및 공용 인터넷에 대한 액세스를 제어하도록 Azure Firewall을 구성하는 방법에 대해 알아봅니다. Azure Machine Learning 보안 설정에 대한 자세한 내용은 [Azure Machine Learning 엔터프라이즈 보안](concept-enterprise-security.md)을 참조하세요.

> [!WARNING]
> 방화벽 뒤에 있는 데이터 스토리지에 대한 액세스는 코드 우선 환경에서만 지원됩니다. [Azure Machine Learning 스튜디오](overview-what-is-machine-learning-studio.md)를 사용하여 방화벽 뒤에 있는 데이터에 액세스하는 것은 지원되지 않습니다. 스튜디오를 사용하여 개인 네트워크에서 데이터 스토리지를 사용하려면 먼저 [가상 네트워크를 설정](../virtual-network/quick-create-portal.md)하고 [가상 네트워크 내에 저장된 데이터에 대한 액세스 권한을 스튜디오에 부여](how-to-enable-studio-virtual-network.md)해야 합니다.

## <a name="azure-firewall"></a>Azure Firewall

Azure Firewall을 사용하는 경우에는 __DNAT(대상 네트워크 주소 변환)__ 를 사용하여 인바운드 트래픽에 대한 NAT 규칙을 만듭니다. 아웃바운드 트래픽의 경우 __네트워크__ 및/또는 __애플리케이션__ 규칙을 만듭니다. 이러한 규칙 컬렉션은 [몇 가지 Azure Firewall 개념](../firewall/firewall-faq.yml#what-are-some-azure-firewall-concepts)에 대해 자세히 설명합니다.

### <a name="inbound-configuration"></a>인바운드 구성

Azure Machine Learning __컴퓨팅 인스턴스__ 또는 __컴퓨팅 클러스터__ 를 사용하는 경우 Azure Machine Learning 리소스를 포함하는 서브넷에 대해 [UDR(사용자 정의 경로)](../virtual-network/virtual-networks-udr-overview.md)을 추가합니다. 이 경로는 `BatchNodeManagement` 및 `AzureMachineLearning` 리소스의 IP 주소 __에서__ 컴퓨팅 인스턴스 및 컴퓨팅 클러스터의 공용 IP에 대한 트래픽을 강제로 적용합니다.

이러한 UDR을 사용하면 Batch 서비스가 작업 예약을 위해 컴퓨팅 노드와 통신할 수 있습니다. 컴퓨팅 인스턴스에 액세스하는 데 필요하므로 Azure Machine Learning Service의 IP 주소도 추가합니다. Azure Machine Learning Service에 대한 IP를 추가하는 경우 __주 및 보조__ Azure 지역에 대한 IP를 추가해야 합니다. 작업 영역이 있는 주 지역입니다.

보조 지역을 찾으려면 [Azure 쌍을 이루는 지역을 사용하여 비즈니스 연속성 및 재해 복구 확인](../best-practices-availability-paired-regions.md#azure-regional-pairs)을 참조하세요. 예를 들어 Azure Machine Learning Service가 미국 동부 2에 있는 경우 보조 지역은 미국 중부입니다. 

Batch 서비스 및 Azure Machine Learning Service의 IP 주소 목록을 가져오려면 다음 방법 중 하나를 사용합니다.

* [Azure IP 범위 및 서비스 태그](https://www.microsoft.com/download/details.aspx?id=56519)를 다운로드하고 파일에서 `BatchNodeManagement.<region>` 및 `AzureMachineLearning.<region>`을 검색합니다. 여기서 `<region>`은 Azure 지역입니다.

* [Azure CLI](/cli/azure/install-azure-cli)를 사용하여 정보를 다운로드합니다. 다음 예제는 IP 주소 정보를 다운로드하고 미국 동부 2 지역(주) 및 미국 중부 지역(보조)에 대한 정보를 필터링합니다.

    ```azurecli-interactive
    az network list-service-tags -l "East US 2" --query "values[?starts_with(id, 'Batch')] | [?properties.region=='eastus2']"
    # Get primary region IPs
    az network list-service-tags -l "East US 2" --query "values[?starts_with(id, 'AzureMachineLearning')] | [?properties.region=='eastus2']"
    # Get secondary region IPs
    az network list-service-tags -l "Central US" --query "values[?starts_with(id, 'AzureMachineLearning')] | [?properties.region=='centralus']"
    ```

    > [!TIP]
    > 미국 버지니아, 미국 아리조나 지역 또는 중국 동부 2 지역을 사용하는 경우 이러한 명령은 IP 주소를 반환하지 않습니다. 대신 다음 링크 중 하나를 사용하여 IP 주소 목록을 다운로드합니다.
    >
    > * [Azure Government에 대한 Azure IP 범위 및 서비스 태그](https://www.microsoft.com/download/details.aspx?id=57063)
    > * [Azure 중국에 대한 Azure IP 범위 및 서비스 태그](https://www.microsoft.com//download/details.aspx?id=57062)

UDR을 추가할 때 관련된 각 Batch IP 주소 접두사에 대한 경로를 정의하고 __다음 홉 유형__ 을 __인터넷__ 으로 설정합니다. 다음 이미지는 Azure Portal에서 UDR의 예를 보여 줍니다.

![주소 접두사에 대한 UDR 예](./media/how-to-enable-virtual-network/user-defined-route.png)

> [!IMPORTANT]
> IP 주소는 시간이 지남에 따라 변경될 수 있습니다.

자세한 내용은 [가상 네트워크에서 Azure Batch 풀 만들기](../batch/batch-virtual-network.md#user-defined-routes-for-forced-tunneling)를 참조하세요.

### <a name="outbound-configuration"></a>아웃바운드 구성

1. 다음 서비스 태그로 __들어오고__ __나가는__ 트래픽을 허용하는 __네트워크 규칙__ 을 추가합니다.

    * AzureActiveDirectory
    * AzureMachineLearning
    * AzureResourceManager
    * Storage.region
    * KeyVault.region
    * ContainerRegistry.region

    Microsoft에서 제공하는 기본 Docker 이미지를 사용하고 사용자 관리 종속성을 사용하도록 계획하는 경우 다음 서비스 태그도 추가해야 합니다.

    * MicrosoftContainerRegistry.region
    * AzureFrontDoor.FirstParty

    `region`이 포함된 항목의 경우 사용 중인 Azure 지역으로 바꿉니다. 예들 들어 `keyvault.westus`입니다.

    __프로토콜__ 에 대해 `TCP`를 선택합니다. 원본 및 대상 __포트__ 에 대해 `*`를 선택합니다.

1. 다음 호스트에 대한 __애플리케이션 규칙__ 을 추가합니다.

    > [!NOTE]
    > 인터넷의 모든 Python 리소스에 필요한 호스트의 전체 목록이 아니라 가장 일반적으로 사용됩니다. 예를 들어 GitHub 리포지토리 또는 다른 호스트에 대한 액세스 권한이 필요한 경우 해당 시나리오에 필요한 호스트를 식별하고 추가해야 합니다.

    | **호스트 이름** | **용도** |
    | ---- | ---- |
    | **graph.windows.net** | Azure Machine Learning 컴퓨팅 인스턴스/클러스터에서 사용됩니다. |
    | **anaconda.com**</br>**\*.anaconda.com** | 기본 패키지를 설치하는 데 사용됩니다. |
    | **\*.anaconda.org** | 리포지토리 데이터를 가져오는 데 사용됩니다. |
    | **pypi.org** | 기본 인덱스에서 종속성을 나열하는 데 사용되며 (있는 경우) 사용자 설정에서 인덱스를 덮어쓰지 않습니다. 인덱스를 덮어쓰는 경우 **\*.pythonhosted.org** 도 허용해야 합니다. |
    | **cloud.r-project.org** | R 개발용 CRAN 패키지를 설치할 때 사용됩니다. |
    | **\*pytorch.org** | PyTorch를 기반으로 하는 일부 예제에서 사용됩니다. |
    | **\*.tensorflow.org** | Tensorflow를 기반으로 하는 일부 예제에서 사용됩니다. |

    __프로토콜:포트__ 에 대해 __http, https__ 사용을 선택합니다.

    애플리케이션 규칙을 구성하는 방법에 대한 자세한 내용은 [Azure Firewall 배포 및 구성](../firewall/tutorial-firewall-deploy-portal.md#configure-an-application-rule)을 참조하세요.

1. AKS(Azure Kubernetes Service)에 배포된 모델에 대한 액세스를 제한하려면 [Azure Kubernetes Service에서 송신 트래픽 제한](../aks/limit-egress-traffic.md)을 참조하세요.

### <a name="diagnostics-for-support"></a>지원을 위한 진단

Microsoft 지원으로 작업할 때 진단 정보를 수집해야 하는 경우 다음 단계를 사용합니다.

1. `AzureMonitor` 태그와의 트래픽을 허용하려면 __네트워크 규칙__ 을 추가합니다.
1. 다음 호스트에 대한 __애플리케이션 규칙__ 을 추가합니다. 다음 호스트의 __프로토콜:포트__ 에 대한 __http, https__ 를 선택합니다.

    + **dc.applicationinsights.azure.com**
    + **dc.applicationinsights.microsoft.com**
    + **dc.services.visualstudio.com**

    Azure Monitor 호스트의 IP 주소 목록은 [Azure Monitor에서 사용하는 IP 주소](../azure-monitor/app/ip-addresses.md)를 참조하세요.
## <a name="other-firewalls"></a>기타 방화벽

각 방화벽에는 고유한 용어 및 특정 구성이 있으므로 이 섹션의 지침은 일반적입니다. 방화벽을 통한 통신을 허용하는 방법에 대한 질문이 있는 경우 사용 중인 방화벽에 대한 문서를 참조하세요.

올바르게 구성되지 않은 경우 방화벽에서 작업 영역 사용에 문제를 일으킬 수 있습니다. Azure Machine Learning 작업 영역에서 사용되는 다양한 호스트 이름이 있습니다. 다음 섹션에서는 Azure Machine Learning에 필요한 호스트를 나열합니다.

### <a name="microsoft-hosts"></a>Microsoft 호스트

이 섹션의 호스트는 Microsoft에서 소유하며 작업 영역의 적절한 기능에 필요한 서비스를 제공합니다. 다음 표에는 Azure 공용, Azure Government 및 Azure 중국 21Vianet 지역에 대한 호스트 이름이 나열되어 있습니다.

**일반 Azure 호스트**

| **필수** | **Azure 공용** | **Azure Government** | **Azure China 21Vianet** |
| ----- | ----- | ----- | ----- |
| Azure Active Directory | login.microsoftonline.com | login.microsoftonline.us | login.chinacloudapi.cn |
| Azure portal | management.azure.com | management.azure.us | management.azure.cn |
| Azure Resource Manager | management.azure.com | management.usgovcloudapi.net | management.chinacloudapi.cn |

**Azure Machine Learning 호스트**

| **필수** | **Azure 공용** | **Azure Government** | **Azure China 21Vianet** |
| ----- | ----- | ----- | ----- |
| Azure Machine Learning Studio | ml.azure.com | ml.azure.us | studio.ml.azure.cn |
| API |\*.azureml.ms | \*.ml.azure.us | \*.ml.azure.cn |
| 통합 Notebook | \*.notebooks.azure.net | \*.notebooks.usgovcloudapi.net |\*.notebooks.chinacloudapi.cn |
| 통합 Notebook | \*.file.core.windows.net | \*.file.core.usgovcloudapi.net | \*.file.core.chinacloudapi.cn |
| 통합 Notebook | \*.dfs.core.windows.net | \*.dfs.core.usgovcloudapi.net | \*.dfs.core.chinacloudapi.cn |
| 통합 Notebook | \*.blob.core.windows.net | \*.blob.core.usgovcloudapi.net | \*.blob.core.chinacloudapi.cn |
| 통합 Notebook | graph.microsoft.com | graph.microsoft.us | graph.chinacloudapi.cn |
| 통합 Notebook | \*.aznbcontent.net |  | |

**Azure Machine Learning 컴퓨팅 인스턴스 및 컴퓨팅 클러스터 호스트**

| **필수** | **Azure 공용** | **Azure Government** | **Azure China 21Vianet** |
| ----- | ----- | ----- | ----- |
| 컴퓨팅 클러스터/인스턴스 | \*.batchai.core.windows.net | \*.batchai.core.usgovcloudapi.net |\*.batchai.ml.azure.cn |
| 컴퓨팅 클러스터/인스턴스 | graph.windows.net | graph.windows.net | graph.chinacloudapi.cn |
| 컴퓨팅 인스턴스 | \*.instances.azureml.net | \*.instances.azureml.us | \*.instances.azureml.cn |
| 컴퓨팅 인스턴스 | \*.instances.azureml.ms |  |  |

> [!IMPORTANT]
> 방화벽은 __TCP__ 포트 __18881__ 을 통해 \*.instances.azureml.ms와의 통신을 허용해야 합니다.

**Azure Machine Learning에서 사용하는 연결된 리소스**

| **필수** | **Azure 공용** | **Azure Government** | **Azure China 21Vianet** |
| ----- | ----- | ----- | ----- |
| Azure Storage 계정 | core.windows.net | core.usgovcloudapi.net | core.chinacloudapi.cn |
| Azure Key Vault | vault.azure.net | vault.usgovcloudapi.net | vault.azure.cn |
| Azure Container Registry | azurecr.io | azurecr.us | azurecr.cn |
| Microsoft Container Registry | mcr.microsoft.com | mcr.microsoft.com | mcr.microsoft.com |


> [!TIP]
> 페더레이션된 ID를 사용하려는 경우 [Active Directory Federation Services 보안 설정에 대한 모범 사례](/windows-server/identity/ad-fs/deployment/best-practices-securing-ad-fs) 문서를 따르세요.

또한 [강제 터널링](how-to-secure-training-vnet.md#forced-tunneling)의 정보를 사용하여 `BatchNodeManagement` 및 `AzureMachineLearning`에 대한 IP 주소를 추가합니다.

AKS(Azure Kubernetes Service)에 배포된 모델에 대한 액세스 제한에 대한 자세한 내용은 [Azure Kubernetes Service에서 송신 트래픽 제한](../aks/limit-egress-traffic.md)을 참조하세요.

> [!TIP]
> Microsoft 지원을 사용하여 진단 정보를 수집하는 경우 Azure Monitor 호스트에서 사용하는 IP 주소에 대한 아웃바운드 트래픽을 허용해야 합니다. Azure Monitor 호스트의 IP 주소 목록은 [Azure Monitor에서 사용하는 IP 주소](../azure-monitor/app/ip-addresses.md)를 참조하세요.
### <a name="python-hosts"></a>Python 호스트

이 섹션의 호스트는 Python 패키지를 설치하는 데 사용됩니다. 개발, 학습 및 배포 중에 필요합니다. 

> [!NOTE]
> 인터넷의 모든 Python 리소스에 필요한 호스트의 전체 목록이 아니라 가장 일반적으로 사용됩니다. 예를 들어 GitHub 리포지토리 또는 다른 호스트에 대한 액세스 권한이 필요한 경우 해당 시나리오에 필요한 호스트를 식별하고 추가해야 합니다.

| **호스트 이름** | **용도** |
| ---- | ---- |
| **anaconda.com**</br>**\*.anaconda.com** | 기본 패키지를 설치하는 데 사용됩니다. |
| **\*.anaconda.org** | 리포지토리 데이터를 가져오는 데 사용됩니다. |
| **pypi.org** | 기본 인덱스에서 종속성을 나열하는 데 사용되며 (있는 경우) 사용자 설정에서 인덱스를 덮어쓰지 않습니다. 인덱스를 덮어쓰는 경우 **\*.pythonhosted.org** 도 허용해야 합니다. |
| **\*pytorch.org** | PyTorch를 기반으로 하는 일부 예제에서 사용됩니다. |
| **\*.tensorflow.org** | Tensorflow를 기반으로 하는 일부 예제에서 사용됩니다. |

### <a name="r-hosts"></a>R 호스트

이 섹션의 호스트는 R 패키지를 설치하는 데 필요합니다. 개발, 학습 및 배포 중에 필요합니다.

> [!NOTE]
> 인터넷의 모든 R 리소스에 필요한 호스트의 전체 목록이 아니라 가장 일반적으로 사용됩니다. 예를 들어 GitHub 리포지토리 또는 다른 호스트에 대한 액세스 권한이 필요한 경우 해당 시나리오에 필요한 호스트를 식별하고 추가해야 합니다.

| **호스트 이름** | **용도** |
| ---- | ---- |
| **cloud.r-project.org** | CRAN 패키지를 설치할 때 사용됩니다. |

> [!IMPORTANT]
> 내부적으로 Azure Machine Learning용 R SDK는 Python 패키지를 사용합니다. 따라서 방화벽을 통해 Python 호스트도 허용해야 합니다.
## <a name="next-steps"></a>다음 단계

* [자습서: Azure Portal을 사용하여 Azure Firewall 배포 및 구성](../firewall/tutorial-firewall-deploy-portal.md)
* [Azure Virtual Network 내에서 Azure ML 실험 및 유추 작업 보호](how-to-network-security-overview.md)
