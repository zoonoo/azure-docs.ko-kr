---
title: 가상 네트워크에서 안전한 실험 및 추론
titleSuffix: Azure Machine Learning
description: Azure 가상 네트워크 내에서 Azure 기계 학습에서 실험/학습 작업 및 추론/채점 작업을 보호하는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: larryfr
ms.author: aashishb
author: aashishb
ms.date: 01/13/2020
ms.openlocfilehash: c813e8a27a7f85eccff2c23d9ffdcfa4a1442f34
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80282837"
---
# <a name="secure-azure-ml-experimentation-and-inference-jobs-within-an-azure-virtual-network"></a>Azure 가상 네트워크 내에서 안전한 Azure ML 실험 및 추론 작업
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

이 문서에서는 Azure 가상 네트워크(vnet) 내에서 Azure 기계 학습에서 실험/학습 작업 및 추론/채점 작업을 보호하는 방법을 배웁니다.

**가상 네트워크는** 공용 인터넷에서 Azure 리소스를 격리하는 보안 경계 역할을 합니다. Azure 가상 네트워크를 온-프레미스 네트워크에 연결할 수도 있습니다. 네트워크에 가입하면 모델을 안전하게 학습하고 배포된 모델에 액세스하여 추론할 수 있습니다.

Azure 기계 학습은 계산 리소스에 대한 다른 Azure 서비스에 의존합니다. 계산 리소스 또는 [계산 대상은](concept-compute-target.md)모델을 학습하고 배포하는 데 사용됩니다. 대상은 가상 네트워크 내에서 만들 수 있습니다. 예를 들어 Microsoft 데이터 과학 가상 컴퓨터를 사용하여 모델을 학습한 다음 모델을 AKS(Azure Kubernetes Service)에 배포할 수 있습니다. 가상 네트워크에 대한 자세한 내용은 [Azure 가상 네트워크 개요를](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview)참조하십시오.

또한 이 문서에서는 기본 또는 실험적 사용 사례에 필요하지 않은 *고급 보안 설정,* 정보에 대한 자세한 정보를 제공합니다. 이 문서의 특정 섹션에서는 다양한 시나리오에 대한 구성 정보를 제공합니다. 순서대로 또는 전체를 작성할 필요가 없습니다.

> [!TIP]
> 구체적으로 명시되지 않는 한 가상 네트워크 내의 저장소 계정 또는 계산 대상과 같은 리소스를 사용하면 기계 학습 파이프라인과 스크립트 실행과 같은 비파이프라인 워크플로에서 모두 작동합니다.

> [!WARNING]
> Microsoft는 가상 네트워크 내부의 리소스를 사용하여 Azure 기계 학습 디자이너 또는 스튜디오에서 자동화된 기계 학습(스튜디오)을 사용하는 것을 지원하지 않습니다.

## <a name="prerequisites"></a>사전 요구 사항

+ Azure 기계 학습 [작업 영역](how-to-manage-workspace.md).

+ Azure 가상 네트워크 [서비스](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) 및 [IP 네트워킹](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm)모두에 대한 일반적인 작업 지식.

+ 컴퓨팅 리소스와 함께 사용할 기존 가상 네트워크 및 서브넷입니다.

## <a name="use-a-storage-account-for-your-workspace"></a>작업 영역에 저장소 계정 사용

가상 네트워크의 작업 영역에 Azure 저장소 계정을 사용하려면 다음 단계를 사용합니다.

1. 가상 네트워크 뒤에 컴퓨팅 리소스(예: 기계 학습 계산 인스턴스 또는 클러스터)를 만들거나 작업 영역에 컴퓨팅 리소스(예: HDInsight 클러스터, 가상 머신 또는 Azure Kubernetes 서비스 클러스터)를 연결합니다. 계산 리소스는 실험 또는 모델 배포용일 수 있습니다.

   자세한 내용은 기계 [학습 계산 사용,](#amlcompute) [가상 컴퓨터 또는 HDInsight 클러스터 사용](#vmorhdi)및 이 문서에서 [Azure Kubernetes 서비스 사용](#aksvnet) 섹션을 참조하십시오.

1. Azure 포털에서 작업 영역에 연결된 저장소로 이동합니다.

   [![Azure 기계 학습 작업 영역에 연결된 저장소](./media/how-to-enable-virtual-network/workspace-storage.png)](./media/how-to-enable-virtual-network/workspace-storage.png#lightbox)

1. Azure **저장소** 페이지에서 __방화벽 및 가상 네트워크를 선택합니다.__

   ![Azure 포털의 Azure 저장소 페이지의 "방화벽 및 가상 네트워크" 영역](./media/how-to-enable-virtual-network/storage-firewalls-and-virtual-networks.png)

1. 방화벽 및 가상 네트워크 페이지에서 다음 작업을 __수행합니다.__
    - __선택한 네트워크__를 선택합니다.
    - __가상 네트워크에서__기존 __가상 네트워크 추가__ 링크를 선택합니다. 이 작업은 계산이 상주하는 가상 네트워크를 추가합니다(1단계 참조).

        > [!IMPORTANT]
        > 저장소 계정은 학습 또는 추론에 사용되는 계산 인스턴스 또는 클러스터와 동일한 가상 네트워크에 있어야 합니다.

    - 신뢰할 __수 있는 Microsoft 서비스 허용을 선택하여 이 저장소 계정 계정__ 확인란에 액세스할 수 있습니다.

    > [!IMPORTANT]
    > Azure 기계 학습 SDK로 작업할 때 개발 환경은 Azure 저장소 계정에 연결할 수 있어야 합니다. 저장소 계정이 가상 네트워크 내에 있으면 방화벽은 개발 환경의 IP 주소에서 액세스를 허용해야 합니다.
    >
    > 저장소 계정에 대한 액세스를 사용하려면 *개발 클라이언트의 웹 브라우저에서*저장소 계정에 대한 __방화벽 및 가상 네트워크를__ 방문하십시오. 그런 다음 __클라이언트 IP 주소 추가__ 확인란을 사용하여 __클라이언트의__IP 주소를 주소 범위에 추가합니다. __또한 주소 RANGE__ 필드를 사용하여 개발 환경의 IP 주소를 수동으로 입력할 수도 있습니다. 클라이언트의 IP 주소가 추가되면 SDK를 사용하여 저장소 계정에 액세스할 수 있습니다.

   [![Azure 포털의 "방화벽 및 가상 네트워크" 창](./media/how-to-enable-virtual-network/storage-firewalls-and-virtual-networks-page.png)](./media/how-to-enable-virtual-network/storage-firewalls-and-virtual-networks-page.png#lightbox)

> [!IMPORTANT]
> Azure Machine Learning의 _기본 저장소 계정_ 또는 가상 네트워크에 _기본이 아닌 저장소 계정을_ 둘 다 배치할 수 있습니다.
>
> 작업 영역을 만들 때 기본 저장소 계정이 자동으로 프로비전됩니다.
>
> 기본이 아닌 저장소 계정의 경우 `storage_account` [ `Workspace.create()` 함수의](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py#create-name--auth-none--subscription-id-none--resource-group-none--location-none--create-resource-group-true--sku--basic---friendly-name-none--storage-account-none--key-vault-none--app-insights-none--container-registry-none--cmk-keyvault-none--resource-cmk-uri-none--hbi-workspace-false--default-cpu-compute-target-none--default-gpu-compute-target-none--exist-ok-false--show-output-true-) 매개 변수를 사용하여 Azure 리소스 ID별로 사용자 지정 저장소 계정을 지정할 수 있습니다.

## <a name="use-azure-data-lake-storage-gen-2"></a>Azure 데이터 레이크 스토리지 2세대 사용

Azure 데이터 레이크 저장소 세대 2는 Azure Blob 저장소를 기반으로 구축된 빅 데이터 분석을 위한 기능 집합입니다. Azure 기계 학습을 사용하여 모델을 학습하는 데 사용되는 데이터를 저장하는 데 사용할 수 있습니다. 

Azure 기계 학습 작업 영역의 가상 네트워크 내에서 Data Lake Storage Gen 2를 사용하려면 다음 단계를 사용합니다.

1. Azure 데이터 레이크 저장소 세대 2 계정을 만듭니다. 자세한 내용은 [Azure 데이터 레이크 저장소 Gen2 저장소 계정 만들기를](../storage/blobs/data-lake-storage-quickstart-create-account.md)참조하십시오.

1. 이전 섹션의 2-4 단계를 사용하여 작업 영역에 대한 저장소 계정을 사용하여 가상 네트워크에 계정을 [배치합니다.](#use-a-storage-account-for-your-workspace)

가상 네트워크 내에서 데이터 레이크 스토리지 세대 2와 함께 Azure 기계 학습을 사용하는 경우 다음 지침을 사용하십시오.

* __SDK를__사용하여 데이터 집합을 만들고 코드를 실행하는 시스템이 __가상 네트워크에 없는__경우 `validate=False` 매개 변수를 사용합니다. 이 매개 변수는 유효성 검사를 건너뛰고 시스템이 저장소 계정과 동일한 가상 네트워크에 없는 경우 실패합니다. 자세한 내용은 [from_files()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.filedatasetfactory?view=azure-ml-py#from-files-path--validate-true-) 메서드를 참조하십시오.

* Azure 기계 학습 계산 인스턴스 또는 계산 클러스터를 사용하여 데이터 집합을 사용하여 모델을 학습하는 경우 저장소 계정과 동일한 가상 네트워크에 있어야 합니다.

## <a name="use-a-key-vault-instance-with-your-workspace"></a>작업 공간에서 키 자격 증명 모음 인스턴스 사용

작업 영역과 연결된 키 자격 증명 모음 인스턴스는 Azure Machine Learning에서 다음 자격 증명을 저장하는 데 사용됩니다.
* 연결된 저장소 계정 연결 문자열
* Azure 컨테이너 리포지토리 인스턴스에 대한 암호
* 데이터 저장소에 문자열 연결

가상 네트워크 뒤에 있는 Azure Key Vault를 사용하여 Azure 기계 학습 실험 기능을 사용하려면 다음 단계를 사용합니다.

1. 작업 영역과 연결된 키 자격 증명 모음으로 이동합니다.

   [![Azure 기계 학습 작업 영역과 연결된 키 자격 증명 모음](./media/how-to-enable-virtual-network/workspace-key-vault.png)](./media/how-to-enable-virtual-network/workspace-key-vault.png#lightbox)

1. 키 **볼트** 페이지에서 왼쪽 창에서 방화벽 __및 가상 네트워크를 선택합니다.__

   ![키 볼트 창의 "방화벽 및 가상 네트워크" 섹션](./media/how-to-enable-virtual-network/key-vault-firewalls-and-virtual-networks.png)

1. 방화벽 및 가상 네트워크 페이지에서 다음 작업을 __수행합니다.__
    - __다음에서 액세스 허용__에서 __선택한 네트워크__를 선택합니다.
    - __가상 네트워크에서__기존 가상 __네트워크 추가를__ 선택하여 실험 계산이 있는 가상 네트워크를 추가합니다.
    - __신뢰할 수 있는 Microsoft 서비스가 이 방화벽을 우회하도록 허용에서__ __예__를 선택합니다.

   [![키 볼트 창의 "방화벽 및 가상 네트워크" 섹션](./media/how-to-enable-virtual-network/key-vault-firewalls-and-virtual-networks-page.png)](./media/how-to-enable-virtual-network/key-vault-firewalls-and-virtual-networks-page.png#lightbox)

<a id="amlcompute"></a>

## <a name="use-a-machine-learning-compute"></a><a name="compute-instance"></a>기계 학습 컴퓨팅 사용

Azure Machine Learning에서 인스턴스를 계산하거나 클러스터를 가상 네트워크에서 계산하려면 다음 네트워크 요구 사항을 충족해야 합니다.

> [!div class="checklist"]
> * 가상 네트워크는 Azure 기계 학습 작업 영역과 동일한 구독 및 지역에 있어야 합니다.
> * 계산 인스턴스 또는 클러스터에 대해 지정된 서브넷에는 대상VM 수를 수용할 수 있는 할당되지 않은 IP 주소가 충분해야 합니다. 서브넷에 할당되지 않은 IP 주소가 충분하지 않으면 계산 클러스터가 부분적으로 할당됩니다.
> * 보안 정책 또는 가상 네트워크의 구독 또는 리소스 그룹에 대한 잠금이 가상 네트워크를 관리할 수 있는 권한을 제한하는지 확인합니다. 트래픽을 제한하여 가상 네트워크를 보호하려는 경우 계산 서비스에 대해 일부 포트를 열어 둡니다. 자세한 내용은 [필수 포트](#mlcports) 섹션을 참조하세요.
> * 하나의 가상 네트워크에 여러 계산 인스턴스 또는 클러스터를 배치하려는 경우 하나 이상의 리소스에 대한 할당량 증가를 요청해야 할 수 있습니다.
> * 작업 영역에 대한 Azure 저장소 계정도 가상 네트워크에서 보호되는 경우 Azure Machine Learning계산 인스턴스 또는 클러스터와 동일한 가상 네트워크에 있어야 합니다. 

> [!TIP]
> Machine Learning은 인스턴스를 계산하거나 클러스터가 가상 네트워크를 포함하는 리소스 그룹에 추가 네트워킹 리소스를 자동으로 할당합니다. 각 계산 인스턴스 또는 클러스터에 대해 서비스는 다음 리소스를 할당합니다.
> 
> * 네트워크 보안 그룹 1개
> * 공용 IP 주소 1개
> * 부하 분산 장치 1개
> 
> 이러한 리소스는 구독의 [리소스 할당량](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits)으로 제한됩니다.


### <a name="required-ports"></a><a id="mlcports"></a> 필수 포트

Machine Learning 컴퓨팅은 현재 Azure Batch 서비스를 사용하여 지정된 가상 네트워크에 VM을 프로비전합니다. 서브넷은 Batch 서비스에서의 인바운드 통신을 허용해야 합니다. 이 통신을 사용하여 기계 학습 계산 노드에서 실행을 예약하고 Azure Storage 및 기타 리소스와 통신합니다. 일괄 처리 서비스는 VM에 연결된 네트워크 인터페이스(NIC) 수준에서 NSG(네트워크 보안 그룹)를 추가합니다. 이러한 NSG는 다음 트래픽을 허용하도록 인바운드 및 아웃바운드 규칙을 자동으로 구성합니다.

- __BatchNodeManagement의__ __서비스 태그에서__ 포트 29876 및 29877의 인바운드 TCP 트래픽 .

    ![BatchNodeManagement 서비스 태그를 사용하는 인바운드 규칙](./media/how-to-enable-virtual-network/batchnodemanagement-service-tag.png)

- (선택 사항) 포트 22의 인바운드 TCP 트래픽으로 원격 액세스를 허용합니다. 공용 IP에서 SSH를 사용하여 연결하려는 경우에만 이 포트를 사용합니다.

- 가상 네트워크에 대한 모든 포트의 아웃바운드 트래픽

- 인터넷에 대한 모든 포트의 아웃바운드 트래픽

- __AzureMachineLearning의__ __서비스 태그에서__ 포트 44224에서 인스턴스 인바운드 TCP 트래픽을 계산합니다.

Batch 구성 NSG에서 인바운드 또는 아웃바운드 규칙을 수정하거나 추가할 경우 주의가 필요합니다. NSG가 계산 노드에 대한 통신을 차단하는 경우 계산 서비스는 계산 노드의 상태를 사용할 수 없게 설정합니다.

Azure Batch 서비스는 자체 NSG를 구성하므로 서브넷 수준에서 NSG를 지정할 필요가 없습니다. 그러나 지정된 서브넷에 NSG 또는 방화벽이 연결된 경우 앞에서 설명한 대로 인바운드 및 아웃바운드 보안 규칙을 구성합니다.

Azure 포털의 NSG 규칙 구성은 다음 이미지에 표시됩니다.

[![기계 학습 컴퓨팅을 위한 인바운드 NSG 규칙](./media/how-to-enable-virtual-network/amlcompute-virtual-network-inbound.png)](./media/how-to-enable-virtual-network/amlcompute-virtual-network-inbound.png#lightbox)

![기계 학습 컴퓨팅을 위한 아웃바운드 NSG 규칙](./media/how-to-enable-virtual-network/experimentation-virtual-network-outbound.png)

### <a name="limit-outbound-connectivity-from-the-virtual-network"></a><a id="limiting-outbound-from-vnet"></a>가상 네트워크에서 아웃바운드 연결 제한

기본 아웃바운드 규칙을 사용하지 않고 가상 네트워크의 아웃바운드 액세스를 제한하려는 경우 다음 단계를 따르십시오.

- NSG 규칙을 사용하여 아웃바운드 인터넷 연결을 거부합니다.

- 계산 __인스턴스__ 또는 __계산 클러스터의__경우 아웃바운드 트래픽을 다음 항목으로 제한합니다.
   - Azure 저장소저장소의 __서비스 태그를__ __사용하여.RegionName__. Azure `{RegionName}` 지역의 이름은 어디에 있습니까?
   - Azure 컨테이너 레지스트리, __AzureContainerRegistry.RegionName의__ __서비스 태그를__ 사용 하 여 . Azure `{RegionName}` 지역의 이름은 어디에 있습니까?
   - __Azure MachineLearning의__ __서비스 태그를__ 사용하여 Azure 기계 학습
   
- 계산 인스턴스의 경우 다음 항목도 __추가합니다.__
   - Azure __ResourceManager의__ __서비스 태그를__ 사용 하 여 Azure 리소스 관리자
   - Azure Active Directory의 __서비스 태그를__ 사용 하 여 Azure Active __Directory__

Azure 포털의 NSG 규칙 구성은 다음 이미지에 표시됩니다.

[![기계 학습 컴퓨팅을 위한 아웃바운드 NSG 규칙](./media/how-to-enable-virtual-network/limited-outbound-nsg-exp.png)](./media/how-to-enable-virtual-network/limited-outbound-nsg-exp.png#lightbox)

> [!NOTE]
> Microsoft에서 제공하는 기본 Docker 이미지를 사용하고 사용자가 관리하는 종속성을 사용하도록 설정하려는 경우 __MicrosoftContainerRegistry.Region_Name(예:__ MicrosoftContainerRegistry.EastUS)의 __서비스 태그도__ 사용해야 합니다.
>
> 이 구성은 학습 스크립트의 일부로 다음 코드와 유사한 코드가 있는 경우에 필요합니다.
>
> __런콘피그 교육__
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
> __추정기 교육__
> ```python
> est = Estimator(source_directory='.',
>                 script_params=script_params,
>                 compute_target='local',
>                 entry_script='dummy_train.py',
>                 user_managed=True)
> run = exp.submit(est)
> ```

### <a name="user-defined-routes-for-forced-tunneling"></a>강제 터널링을 위한 사용자 정의 경로

기계 학습 계산을 사용하여 강제 터널링을 사용하는 경우 계산 리소스가 포함된 서브넷에 [사용자 정의 경로(DR)를](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview) 추가합니다.

* 리소스가 있는 리전에서 Azure Batch 서비스에서 사용하는 각 IP 주소에 대해 UDR을 설정합니다. 이러한 D들러를 사용하면 Batch 서비스가 작업 예약을 위해 계산 노드와 통신할 수 있습니다. Batch 서비스의 IP 주소 목록을 얻으려면 다음 방법 중 하나를 사용합니다.

    * Azure [IP 범위 및 서비스 태그를](https://www.microsoft.com/download/details.aspx?id=56519) 다운로드하고 `BatchNodeManagement.<region>`Azure `<region>` 지역이 있는 경우 파일을 검색합니다.

    * Azure [CLI를](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) 사용하여 정보를 다운로드합니다. 다음 예제는 IP 주소 정보를 다운로드하고 미국 동부 2 지역에 대한 정보를 필터링합니다.

        ```azurecli-interactive
        az network list-service-tags -l "East US 2" --query "values[?starts_with(id, 'Batch')] | [?properties.region=='eastus2']"
        ```

* Azure Storage로의 아웃바운드 트래픽은 온-프레미스 네트워크 어플라이언스에 의해 차단되어서는 안 됩니다. 특히 URL은 의 형태로 `<account>.table.core.windows.net`, `<account>.queue.core.windows.net`및 `<account>.blob.core.windows.net`.

DR을 추가할 때 각 관련 Batch IP 주소 접두사에 대한 경로를 정의하고 __다음 홉 유형을__ __인터넷으로__설정합니다. 다음 이미지는 Azure 포털에서 이 UDR의 예를 보여 주며 있습니다.

![주소 접두사에 대한 UDR의 예](./media/how-to-enable-virtual-network/user-defined-route.png)

자세한 내용은 [가상 네트워크에서 Azure Batch 풀 만들기를](../batch/batch-virtual-network.md#user-defined-routes-for-forced-tunneling)참조하십시오.

### <a name="create-a-compute-cluster-in-a-virtual-network"></a>가상 네트워크에서 컴퓨팅 클러스터 만들기

기계 학습 컴퓨팅 클러스터를 만들려면 다음 단계를 사용합니다.

1. Azure [포털에서](https://portal.azure.com)Azure 기계 학습 작업 영역을 선택합니다.

1. 응용 __프로그램__ 섹션에서 __계산을__선택한 다음 계산 추가 를 __선택합니다.__

1. 가상 네트워크를 사용하도록 이 계산 리소스를 구성하려면 다음 작업을 수행합니다.

    a. __네트워크 구성의__경우 고급 을 __선택합니다.__

    b. 리소스 __그룹__ 드롭다운 목록에서 가상 네트워크가 포함된 리소스 그룹을 선택합니다.

    다. 가상 __네트워크__ 드롭다운 목록에서 서브넷이 포함된 가상 네트워크를 선택합니다.

    d. __서브넷__ 드롭다운 목록에서 사용할 서브넷을 선택합니다.

   ![기계 학습 컴퓨팅을 위한 가상 네트워크 설정](./media/how-to-enable-virtual-network/amlcompute-virtual-network-screen.png)

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

생성 프로세스가 완료되면 실험에서 클러스터를 사용하여 모델을 학습합니다. 자세한 내용은 [학습의 컴퓨팅 대상 선택 및 사용](how-to-set-up-training-targets.md)을 참조하세요.

## <a name="use-azure-databricks"></a>Azure 데이터 브릭 사용

작업 영역이 있는 가상 네트워크에서 Azure Databricks를 사용하려면 다음 요구 사항을 충족해야 합니다.

> [!div class="checklist"]
> * 가상 네트워크는 Azure 기계 학습 작업 영역과 동일한 구독 및 지역에 있어야 합니다.
> * 작업 영역에 대한 Azure 저장소 계정도 가상 네트워크에서 보호되는 경우 Azure Databricks 클러스터와 동일한 가상 네트워크에 있어야 합니다.
> * Azure __Databricks에서 사용하는 데이터 브릭-프라이빗__ 및 __데이터브릭-공용__ 서브넷 외에도 가상 네트워크에 대해 생성된 __기본__ 서브넷도 필요합니다.

가상 네트워크와 함께 Azure Databricks사용에 대한 자세한 내용은 [Azure 가상 네트워크에서 Azure Databricks 배포를](https://docs.azuredatabricks.net/administration-guide/cloud-configurations/azure/vnet-inject.html)참조하십시오.

<a id="vmorhdi"></a>

## <a name="use-a-virtual-machine-or-hdinsight-cluster"></a>가상 머신 또는 HDInsight 클러스터 사용

> [!IMPORTANT]
> Azure 기계 학습은 우분투를 실행하는 가상 머신만 지원합니다.

작업 영역이 있는 가상 네트워크에서 가상 컴퓨터 또는 Azure HDInsight 클러스터를 사용하려면 다음 단계를 사용합니다.

1. Azure 포털 또는 Azure CLI를 사용하여 VM 또는 HDInsight 클러스터를 만들고 클러스터를 Azure 가상 네트워크에 넣습니다. 자세한 내용은 다음 문서를 참조하세요.
    * [Linux VM용 Azure 가상 네트워크 생성 및 관리](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-virtual-network)

    * [Azure 가상 네트워크를 사용하여 HDInsight 확장](https://docs.microsoft.com/azure/hdinsight/hdinsight-extend-hadoop-virtual-network)

1. Azure 기계 학습이 VM 또는 클러스터의 SSH 포트와 통신할 수 있도록 하려면 네트워크 보안 그룹에 대한 원본 항목을 구성합니다. SSH 포트는 일반적으로 포트 22입니다. 이 소스의 트래픽을 허용하려면 다음 작업을 수행합니다.

    * __소스__ 드롭다운 목록에서 서비스 __태그__를 선택합니다.

    * 소스 __서비스 태그__ 드롭다운 목록에서 __AzureMachineLearning__을 선택합니다.

    * 소스 __포트 범위__ 드롭다운 목록에서 을 __*__ 선택합니다.

    * __대상__ 드롭다운 목록에서 __Any를__선택합니다.

    * 대상 __포트 범위__ 드롭다운 목록에서 __22를__선택합니다.

    * __프로토콜에서__ __임의를__선택합니다.

    * __작업__에서 __를 선택합니다.__

   ![가상 네트워크 내에서 VM 또는 HDInsight 클러스터에서 실험을 수행하기 위한 인바운드 규칙](./media/how-to-enable-virtual-network/experimentation-virtual-network-inbound.png)

    네트워크 보안 그룹에 대한 기본 아웃바운드 규칙을 유지합니다. 자세한 내용은 [보안 그룹](https://docs.microsoft.com/azure/virtual-network/security-overview#default-security-rules)의 기본 보안 규칙을 참조하세요.

    기본 아웃바운드 규칙을 사용하지 않고 가상 네트워크의 아웃바운드 액세스를 제한하려면 가상 네트워크 의 [아웃바운드 연결 제한](#limiting-outbound-from-vnet) 섹션을 참조하세요.

1. VM 또는 HDInsight 클러스터를 Azure 기계 학습 작업 영역에 연결합니다. 자세한 내용은 [모델 학습의 컴퓨팅 대상 설정](how-to-set-up-training-targets.md)을 참조하세요.

<a id="aksvnet"></a>

## <a name="use-azure-kubernetes-service-aks"></a>AKS(Azure Kubernetes Service) 사용

작업 영역에 가상 네트워크에 AKS를 추가하려면 다음 단계를 사용합니다.

> [!IMPORTANT]
> 다음 절차를 시작하기 전에 [AKS(Azure Kubernetes Service)의 고급 네트워킹 구성의](https://docs.microsoft.com/azure/aks/configure-advanced-networking#prerequisites) 필수 구성 방법을 따르고 클러스터에 대한 IP 주소 지정을 계획합니다.
>
> AKS 인스턴스와 Azure 가상 네트워크는 동일한 지역에 있어야 합니다. 가상 네트워크의 작업 영역에서 사용하는 Azure 저장소 계정을 보호하는 경우 AKS 인스턴스와 동일한 가상 네트워크에 있어야 합니다.

1. Azure [포털에서](https://portal.azure.com)가상 네트워크를 제어하는 NSG에 __AzureMachineLearning을__ **소스로**사용하여 Azure MachineLearning에 사용할 수 있는 인바운드 규칙이 있는지 확인합니다.

    [![계산 창을 추가하는 Azure 기계 학습](./media/how-to-enable-virtual-network/aks-vnet-inbound-nsg-aml.png)](./media/how-to-enable-virtual-network/aks-vnet-inbound-nsg-aml.png#lightbox)

1. Azure 기계 학습 작업 영역을 선택합니다.

1. 응용 __프로그램__ 섹션에서 __계산을__선택한 다음 계산 추가 를 __선택합니다.__

1. 가상 네트워크를 사용하도록 이 계산 리소스를 구성하려면 다음 작업을 수행합니다.

    - __네트워크 구성의__경우 고급 을 __선택합니다.__

    - 리소스 __그룹__ 드롭다운 목록에서 가상 네트워크가 포함된 리소스 그룹을 선택합니다.

    - 가상 __네트워크__ 드롭다운 목록에서 서브넷이 포함된 가상 네트워크를 선택합니다.

    - __서브넷__ 드롭다운 목록에서 서브넷을 선택합니다.

    - __Kubernetes 서비스 주소 범위__ 상자에 Kubernetes 서비스 주소 범위를 입력합니다. 이 주소 범위는 클래스리스 도메인 라우팅(CIDR) 표기 형 IP 범위를 사용하여 클러스터에 사용할 수 있는 IP 주소를 정의합니다. 서브넷 IP 범위(예: 10.0.0.0/16)와 겹치지 않아야 합니다.

    - __Kubernetes DNS 서비스 IP 주소__ 상자에 Kubernetes DNS 서비스 IP 주소를 입력합니다. 이 IP 주소는 Kubernetes DNS 서비스에 할당됩니다. Kubernetes 서비스 주소 범위(예: 10.0.0.10) 내에 있어야 합니다.

    - Docker __브리지 주소__ 상자에 Docker 브리지 주소를 입력합니다. 이 IP 주소는 Docker 브리지에 할당됩니다. 서브넷 IP 범위 또는 Kubernetes 서비스 주소 범위(예: 172.17.0.1/16)에 없어야 합니다.

   ![Azure 기계 학습: 기계 학습 가상 네트워크 설정 계산](./media/how-to-enable-virtual-network/aks-virtual-network-screen.png)

1. 가상 네트워크를 제어하는 NSG 그룹에 가상 네트워크 외부에서 호출할 수 있도록 점수 매기기 끝점에 대해 인바운드 보안 규칙이 활성화되어 있는지 확인합니다.
   > [!IMPORTANT]
   > NSG에 대한 기본 아웃바운드 규칙을 유지합니다. 자세한 내용은 [보안 그룹](https://docs.microsoft.com/azure/virtual-network/security-overview#default-security-rules)의 기본 보안 규칙을 참조하세요.

   [![인바운드 보안 규칙](./media/how-to-enable-virtual-network/aks-vnet-inbound-nsg-scoring.png)](./media/how-to-enable-virtual-network/aks-vnet-inbound-nsg-scoring.png#lightbox)

Azure 기계 학습 SDK를 사용하여 가상 네트워크에 Azure Kubernetes 서비스를 추가할 수도 있습니다. 가상 네트워크에 AKS 클러스터가 이미 있는 경우 [AKS에 배포하는 방법에](how-to-deploy-and-where.md)설명된 대로 작업 영역에 연결합니다. 다음 코드는 다음이라는 `default` `mynetwork`가상 네트워크의 서브넷에 새 AKS 인스턴스를 만듭니다.

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
aks_target = ComputeTarget.create(workspace=ws,
                                  name="myaks",
                                  provisioning_configuration=config)
```

생성 프로세스가 완료되면 가상 네트워크 뒤에 있는 AKS 클러스터에서 추론 또는 모델 채점을 실행할 수 있습니다. 자세한 내용은 [AKS에 배포하는 방법을](how-to-deploy-and-where.md)참조하십시오.

### <a name="use-private-ips-with-azure-kubernetes-service"></a>Azure Kubernetes 서비스에서 개인 IP 사용

기본적으로 공용 IP 주소는 AKS 배포에 할당됩니다. 가상 네트워크 내에서 AKS를 사용하는 경우 개인 IP 주소를 대신 사용할 수 있습니다. 개인 IP 주소는 가상 네트워크 또는 조인된 네트워크 내에서만 액세스할 수 있습니다.

내부 _로드 밸런서를_사용하도록 AKS를 구성하여 개인 IP 주소를 사용할 수 있습니다. 

> [!IMPORTANT]
> Azure Kubernetes 서비스 클러스터를 만들 때 개인 IP를 사용할 수 없습니다. 기존 클러스터에 대한 업데이트로 활성화해야 합니다.

다음 코드 코드 조각에서는 **새 AKS 클러스터를 만든**다음 개인 IP/내부 로드 밸런서를 사용하도록 업데이트하는 방법을 보여 줍니다.

```python
import azureml.core
from azureml.core.compute.aks import AksUpdateConfiguration
from azureml.core.compute import AksCompute, ComputeTarget

# Verify that cluster does not exist already
try:
    aks_target = AksCompute(workspace=ws, name=aks_cluster_name)
    print("Found existing aks cluster")

except:
    print("Creating new aks cluster")

    # Create AKS configuration
    prov_config = AksCompute.provisioning_configuration(location = "eastus2")
    # Set info for existing virtual network to create the cluster in
    prov_config.vnet_resourcegroup_name = "myvnetresourcegroup"
    prov_config.vnet_name = "myvnetname"
    prov_config.service_cidr = "10.0.0.0/16"
    prov_config.dns_service_ip = "10.0.0.10"
    prov_config.subnet_name = "default"
    prov_config.docker_bridge_cidr = "172.17.0.1/16"

    # Create compute target
    aks_target = ComputeTarget.create(workspace = ws, name = “myaks”, provisioning_configuration = prov_config)
    # Wait for the operation to complete
    aks_target.wait_for_completion(show_output = True)
    
    # Update AKS configuration to use an internal load balancer
    update_config = AksUpdateConfiguration(None, "InternalLoadBalancer", "default")
    aks_target.update(update_config)
    # Wait for the operation to complete
    aks_target.wait_for_completion(show_output = True)
```

__Azure CLI__

```azurecli-interactive
az rest --method put --uri https://management.azure.com"/subscriptions/<subscription-id>/resourcegroups/<resource-group>/providers/Microsoft.ContainerService/managedClusters/<aks-resource-id>?api-version=2018-11-19 --body @body.json
```

명령에서 `body.json` 참조하는 파일의 내용은 다음 JSON 문서와 유사합니다.

```json
{ 
    "location": “<region>”, 
    "properties": { 
        "resourceId": "/subscriptions/<subscription-id>/resourcegroups/<resource-group>/providers/Microsoft.ContainerService/managedClusters/<aks-resource-id>", 
        "computeType": "AKS", 
        "provisioningState": "Succeeded", 
        "properties": { 
            "loadBalancerType": "InternalLoadBalancer", 
            "agentCount": <agent-count>, 
            "agentVmSize": "vm-size", 
            "clusterFqdn": "<cluster-fqdn>" 
        } 
    } 
} 
```

> [!NOTE]
> 현재 기존 클러스터에서 __연결__ 작업을 수행할 때 로드 밸런서를 구성할 수 없습니다. 먼저 클러스터를 연결한 다음 업데이트 작업을 수행하여 로드 밸런서를 변경해야 합니다.

AKS를 사용하여 내부 로드 밸러터 사용에 대한 자세한 내용은 [Azure Kubernetes 서비스의 내부 부하 분산 기 사용을](/azure/aks/internal-lb)참조하십시오.

## <a name="use-azure-firewall"></a>Azure 방화벽 사용

Azure 방화벽을 사용하는 경우 다음 주소에서 트래픽을 허용하도록 네트워크 규칙을 구성해야 합니다.

- `*.batchai.core.windows.net`
- `ml.azure.com`
- `*.azureml.ms`
- `*.experiments.azureml.net`
- `*.modelmanagement.azureml.net`
- `mlworkspace.azure.ai`
- `*.aether.ms`

규칙을 추가할 때 __프로토콜을__ 임의로 설정하고 포트를 `*`로 설정합니다.

네트워크 규칙 구성에 대한 자세한 내용은 [Azure 방화벽 배포 및 구성을](/azure/firewall/tutorial-firewall-deploy-portal#configure-a-network-rule)참조하십시오.

## <a name="use-azure-container-registry"></a>Azure Container Registry 사용

Azure 기계 학습을 사용 하 여 가상 네트워크를 사용 하는 경우 가상 네트워크에서 작업 영역에 대 한 Azure 컨테이너 레지스트리를 배치 __하지 마십시오.__ 이 구성은 지원되지 않습니다.

## <a name="next-steps"></a>다음 단계

* [학습 환경 설정](how-to-set-up-training-targets.md)
* [모델 배포 위치](how-to-deploy-and-where.md)
* [TLS를 사용하여 Azure 기계 학습을 통해 웹 서비스 보호](how-to-secure-web-service.md)
