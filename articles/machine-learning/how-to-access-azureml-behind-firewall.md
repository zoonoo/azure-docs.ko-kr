---
title: 방화벽 사용
titleSuffix: Azure Machine Learning
description: Azure 방화벽을 사용 하 여 Azure Machine Learning 작업 영역에 대 한 액세스를 제어 합니다. 방화벽을 통해 허용 해야 하는 호스트에 대해 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 11/18/2020
ms.custom: how-to, devx-track-python
ms.openlocfilehash: cf89532fc41b10d6fbcba57963ebe30a361a2e6f
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/21/2020
ms.locfileid: "95012984"
---
# <a name="use-workspace-behind-a-firewall-for-azure-machine-learning"></a>방화벽 뒤의 작업 영역을 사용 하 여 Azure Machine Learning

이 문서에서는 Azure Machine Learning 작업 영역 및 공용 인터넷에 대 한 액세스를 제어 하도록 Azure 방화벽을 구성 하는 방법에 대해 알아봅니다. Azure Machine Learning 보안 설정에 대 한 자세한 내용은 [Azure Machine Learning 엔터프라이즈 보안](concept-enterprise-security.md) 을 참조 하세요.

## <a name="azure-firewall"></a>Azure Firewall

Azure 방화벽을 사용 하는 경우에는 __DNAT (대상 네트워크 주소 변환)__ 를 사용 하 여 인바운드 트래픽에 대 한 NAT 규칙을 만듭니다. 아웃 바운드 트래픽의 경우 __네트워크__ 및/또는 __응용 프로그램__ 규칙을 만듭니다. 이러한 규칙 컬렉션은 [몇 가지 Azure 방화벽 개념](../firewall/firewall-faq.md#what-are-some-azure-firewall-concepts)에 대해 자세히 설명 합니다.

### <a name="inbound-configuration"></a>인바운드 구성

Azure Machine Learning __계산 인스턴스__ 또는 __계산 클러스터__ 를 사용 하는 경우 Azure Machine Learning 리소스를 포함 하는 서브넷에 대해 [udrs (사용자 정의 경로)](../virtual-network/virtual-networks-udr-overview.md) 를 추가 합니다. 이 경로는 및 리소스의 IP 주소 __에서__ `BatchNodeManagement` `AzureMachineLearning` 계산 인스턴스 및 계산 클러스터의 공용 ip에 대 한 트래픽을 강제로 적용 합니다.

이러한 UDR을 사용하면 Batch 서비스가 작업 예약을 위해 컴퓨팅 노드와 통신할 수 있습니다. 리소스가 있는 Azure Machine Learning Service의 IP 주소도 추가합니다. 컴퓨팅 인스턴스에 액세스하는 데 필요하기 때문입니다. Batch 서비스 및 Azure Machine Learning Service의 IP 주소 목록을 가져오려면 다음 방법 중 하나를 사용합니다.

* [Azure IP 범위 및 서비스 태그](https://www.microsoft.com/download/details.aspx?id=56519)를 다운로드하고 파일에서 `BatchNodeManagement.<region>` 및 `AzureMachineLearning.<region>`을 검색합니다. 여기서 `<region>`은 Azure 지역입니다.

* [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true)를 사용하여 정보를 다운로드합니다. 다음 예는 IP 주소 정보를 다운로드하고 미국 동부 2 지역에 대한 정보를 필터링합니다.

    ```azurecli-interactive
    az network list-service-tags -l "East US 2" --query "values[?starts_with(id, 'Batch')] | [?properties.region=='eastus2']"
    az network list-service-tags -l "East US 2" --query "values[?starts_with(id, 'AzureMachineLearning')] | [?properties.region=='eastus2']"
    ```

    > [!TIP]
    > 미국 버지니아, US-Arizona 지역 또는 중국-2 지역을 사용 하는 경우 이러한 명령은 IP 주소를 반환 하지 않습니다. 대신, 다음 링크 중 하나를 사용 하 여 IP 주소 목록을 다운로드 합니다.
    >
    > * [Azure Government에 대 한 Azure IP 범위 및 서비스 태그](https://www.microsoft.com/download/details.aspx?id=57063)
    > * [Azure 중국의 azure IP 범위 및 서비스 태그](https://www.microsoft.com//download/details.aspx?id=57062)

UDR을 추가할 때 관련된 각 Batch IP 주소 접두사에 대한 경로를 정의하고 __다음 홉 유형__ 을 __인터넷__ 으로 설정합니다. 다음 이미지는 Azure Portal에서 UDR의 예를 보여 줍니다.

![주소 접두사에 대한 UDR 예](./media/how-to-enable-virtual-network/user-defined-route.png)

> [!IMPORTANT]
> IP 주소는 시간이 지남에 따라 변경 될 수 있습니다.

자세한 내용은 [가상 네트워크에서 Azure Batch 풀 만들기](../batch/batch-virtual-network.md#user-defined-routes-for-forced-tunneling)를 참조하세요.

### <a name="outbound-configuration"></a>아웃 바운드 구성

1. 다음 서비스 태그로 들어오고 나가는 트래픽을 __허용 하는__ __네트워크 규칙__ 을 __추가 합니다.__

    * AzureActiveDirectory
    * AzureMachineLearning
    * AzureResourceManager
    * 저장소. 지역
    * KeyVault. 지역
    * Microsoft.containerregistry

    Microsoft에서 제공 하는 기본 Docker 이미지를 사용 하 고 사용자 관리 종속성을 사용 하도록 계획 하는 경우 다음 서비스 태그도 추가 해야 합니다.

    * MicrosoftContainerRegistry
    * AzureFrontDoor.FirstParty

    이 포함 된 항목의 경우 `region` 를 사용 중인 Azure 지역으로 바꿉니다. `keyvault.westus`)을 입력합니다.

    __프로토콜__ 에 대해를 선택 `TCP` 합니다. 원본 및 대상 __포트__ 의 경우를 선택 `*` 합니다.

1. 다음 호스트에 대 한 __응용 프로그램 규칙__ 을 추가 합니다.

    > [!NOTE]
    > 인터넷의 모든 Python 리소스에 필요한 호스트의 전체 목록이 아니라 가장 일반적으로 사용 됩니다. 예를 들어 GitHub 리포지토리 또는 다른 호스트에 대 한 액세스 권한이 필요한 경우 해당 시나리오에 필요한 호스트를 식별 하 고 추가 해야 합니다.

    | **호스트 이름** | **용도** |
    | ---- | ---- |
    | **anaconda.com**</br>**\*. anaconda.com** | 기본 패키지를 설치 하는 데 사용 됩니다. |
    | **\*. anaconda.org** | 리포지토리 데이터를 가져오는 데 사용 됩니다. |
    | **pypi.org** | 기본 인덱스에서 종속성을 나열 하는 데 사용 되며 (있는 경우) 사용자 설정에서 인덱스를 덮어쓰지 않습니다. 인덱스를 덮어쓰는 경우 **\* pythonhosted.org** 도 허용 해야 합니다. |
    | **cloud.r-project.org** | R 개발용 CRAN 패키지를 설치할 때 사용 됩니다. |
    | **\*pytorch.org** | PyTorch을 기반으로 하는 일부 예제에서 사용 됩니다. |
    | **\*. tensorflow.org** | Tensorflow을 기반으로 하는 일부 예제에서 사용 됩니다. |

    __프로토콜: 포트__ 에 대해 __http, https__ 사용을 선택 합니다.

    응용 프로그램 규칙을 구성 하는 방법에 대 한 자세한 내용은 [Azure 방화벽 배포 및 구성](../firewall/tutorial-firewall-deploy-portal.md#configure-an-application-rule)을 참조 하세요.

1. Azure Kubernetes Service (AKS)에 배포 된 모델에 대 한 액세스를 제한 하려면 [Azure Kubernetes service에서 송신 트래픽 제한](../aks/limit-egress-traffic.md)을 참조 하세요.

## <a name="other-firewalls"></a>기타 방화벽

각 방화벽에는 고유한 용어 및 특정 구성이 있으므로이 섹션의 지침은 일반적입니다. 방화벽을 통한 통신을 허용 하는 방법에 대 한 질문이 있는 경우 사용 중인 방화벽에 대 한 설명서를 참조 하세요.

올바르게 구성 되지 않은 경우 방화벽에서 작업 영역을 사용 하 여 문제를 일으킬 수 있습니다. Azure Machine Learning 작업 영역에서 사용 되는 다양 한 호스트 이름이 있습니다. 다음 섹션에서는 Azure Machine Learning에 필요한 호스트를 나열 합니다.

### <a name="microsoft-hosts"></a>Microsoft 호스트

이 섹션의 호스트는 Microsoft에서 소유 하며 작업 영역의 적절 한 기능을 제공 하는 데 필요한 서비스를 제공 합니다. 다음 표에는 Azure 공용, Azure Government 및 Azure 중국 21Vianet 지역에 대 한 호스트 이름이 나열 되어 있습니다.

**일반 Azure 호스트**

| **필수** | **Azure 공용** | **Azure Government** | **Azure China 21Vianet** |
| ----- | ----- | ----- | ----- |
| Azure Active Directory | login.microsoftonline.com | login.microsoftonline.us | login.chinacloudapi.cn |
| Azure portal | management.azure.com | management.azure.us | management.azure.cn |

**Azure Machine Learning 호스트**

| **필수** | **Azure 공용** | **Azure Government** | **Azure China 21Vianet** |
| ----- | ----- | ----- | ----- |
| Azure Machine Learning Studio | ml.azure.com | ml.azure.us | studio.ml.azure.cn |
| API |\*. azureml.ms | \*. ml.azure.us | \*. ml.azure.cn |
| 실험, 기록, Hyperdrive, 레이블 지정 | \*. experiments.azureml.net | \*. ml.azure.us | \*. ml.azure.cn |
| 모델 관리 | \*. modelmanagement.azureml.net | \*. ml.azure.us | \*. ml.azure.cn |
| 파이프라인 | \*. aether.ms | \*. ml.azure.us | \*. ml.azure.cn |
| 디자이너 (studio 서비스) | \*. studioservice.azureml.com | \*. ml.azure.us | \*. ml.azure.cn |
| 통합 노트북 | \*. notebooks.azure.net | \*. notebooks.usgovcloudapi.net |\*. notebooks.chinacloudapi.cn |
| 통합 노트북 | \*. file.core.windows.net | \*. file.core.usgovcloudapi.net | \*. file.core.chinacloudapi.cn |
| 통합 노트북 | \*.dfs.core.windows.net | \*. dfs.core.usgovcloudapi.net | \*. dfs.core.chinacloudapi.cn |
| 통합 노트북 | \*.blob.core.windows.net | \*.blob.core.usgovcloudapi.net | \*. blob.core.chinacloudapi.cn |
| 통합 노트북 | graph.microsoft.com | graph.microsoft.us | graph.chinacloudapi.cn |
| 통합 노트북 | \*. aznbcontent.net |  | |

**계산 인스턴스 및 계산 클러스터 호스트 Azure Machine Learning**

| **필수** | **Azure 공용** | **Azure Government** | **Azure China 21Vianet** |
| ----- | ----- | ----- | ----- |
| 계산 클러스터/인스턴스 | \*. batchai.core.windows.net | \*. batchai.core.usgovcloudapi.net |\*. batchai.ml.azure.cn |
| 컴퓨팅 인스턴스 | \*. instances.azureml.net | \*. instances.azureml.us | \*. instances.azureml.cn |
| 컴퓨팅 인스턴스 | \*. instances.azureml.ms |  |  |

**Azure Machine Learning에서 사용 하는 연결 된 리소스**

| **필수** | **Azure 공용** | **Azure Government** | **Azure China 21Vianet** |
| ----- | ----- | ----- | ----- |
| Azure Storage 계정 | core.windows.net | core.usgovcloudapi.net | core.chinacloudapi.cn |
| Azure Key Vault | vault.azure.net | vault.usgovcloudapi.net | vault.azure.cn |
| Azure Container Registry | azurecr.io | azurecr.us | azurecr.cn |
| Microsoft Container Registry | mcr.microsoft.com | mcr.microsoft.com | mcr.microsoft.com |


> [!TIP]
> 페더레이션된 id를 사용 하려는 경우 [Active Directory Federation Services 보안 설정에 대 한 모범 사례](/windows-server/identity/ad-fs/deployment/best-practices-securing-ad-fs) 문서를 따르세요.

또한 [강제 터널링](how-to-secure-training-vnet.md#forced-tunneling) 의 정보를 사용 하 여 및에 대 한 IP 주소를 추가 `BatchNodeManagement` `AzureMachineLearning` 합니다.

AKS (Azure Kubernetes Service)에 배포 된 모델에 대 한 액세스를 제한 하는 방법에 대 한 자세한 내용은 [Azure Kubernetes service에서 송신 트래픽 제한](../aks/limit-egress-traffic.md)을 참조 하세요.

### <a name="python-hosts"></a>Python 호스트

이 섹션의 호스트는 Python 패키지를 설치 하는 데 사용 됩니다. 개발, 학습 및 배포 중에 필요 합니다. 

> [!NOTE]
> 인터넷의 모든 Python 리소스에 필요한 호스트의 전체 목록이 아니라 가장 일반적으로 사용 됩니다. 예를 들어 GitHub 리포지토리 또는 다른 호스트에 대 한 액세스 권한이 필요한 경우 해당 시나리오에 필요한 호스트를 식별 하 고 추가 해야 합니다.

| **호스트 이름** | **용도** |
| ---- | ---- |
| **anaconda.com**</br>**\*. anaconda.com** | 기본 패키지를 설치 하는 데 사용 됩니다. |
| **\*. anaconda.org** | 리포지토리 데이터를 가져오는 데 사용 됩니다. |
| **pypi.org** | 기본 인덱스에서 종속성을 나열 하는 데 사용 되며 (있는 경우) 사용자 설정에서 인덱스를 덮어쓰지 않습니다. 인덱스를 덮어쓰는 경우 **\* pythonhosted.org** 도 허용 해야 합니다. |
| **\*pytorch.org** | PyTorch을 기반으로 하는 일부 예제에서 사용 됩니다. |
| **\*. tensorflow.org** | Tensorflow을 기반으로 하는 일부 예제에서 사용 됩니다. |

### <a name="r-hosts"></a>R 호스트

이 섹션의 호스트는 R 패키지를 설치 하는 데 사용 됩니다. 개발, 학습 및 배포 중에 필요 합니다.

> [!NOTE]
> 인터넷의 모든 R 리소스에 필요한 호스트의 전체 목록이 아니라 가장 일반적으로 사용 됩니다. 예를 들어 GitHub 리포지토리 또는 다른 호스트에 대 한 액세스 권한이 필요한 경우 해당 시나리오에 필요한 호스트를 식별 하 고 추가 해야 합니다.

| **호스트 이름** | **용도** |
| ---- | ---- |
| **cloud.r-project.org** | CRAN 패키지를 설치할 때 사용 됩니다. |

> [!IMPORTANT]
> 내부적으로 Azure Machine Learning 용 R SDK는 Python 패키지를 사용 합니다. 따라서 방화벽을 통해 Python 호스트도 허용 해야 합니다.
## <a name="next-steps"></a>다음 단계

* [자습서: Azure Portal을 사용하여 Azure Firewall 배포 및 구성](../firewall/tutorial-firewall-deploy-portal.md)
* [Azure Virtual Network 내에서 Azure ML 실험 및 유추 작업 보호](how-to-network-security-overview.md)
