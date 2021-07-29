---
title: 가상 네트워크를 사용하여 Azure Machine Learning 작업 영역 보호
titleSuffix: Azure Machine Learning
description: 격리된 Azure Virtual Network를 사용하여 Azure Machine Learning 작업 영역 및 관련 리소스를 보호합니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: larryfr
ms.author: peterlu
author: peterclu
ms.date: 06/10/2021
ms.topic: how-to
ms.custom: contperf-fy20q4, tracking-python, contperf-fy21q1
ms.openlocfilehash: 668584c7c254c1d1f200050154256621ba220b5a
ms.sourcegitcommit: e39ad7e8db27c97c8fb0d6afa322d4d135fd2066
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111981803"
---
# <a name="secure-an-azure-machine-learning-workspace-with-virtual-networks"></a>가상 네트워크를 사용하여 Azure Machine Learning 작업 영역 보호

이 문서에서는 가상 네트워크에서 Azure Machine Learning 작업 영역 및 연결된 리소스를 보호하는 방법에 대해 알아봅니다.

이 문서는 5부로 구성된 Azure Machine Learning 워크플로 보안 과정을 안내하는 시리즈의 2부입니다. 먼저 전체 아키텍처를 이해하려면 [1부: VNet 개요](how-to-network-security-overview.md)를 읽어보는 것이 좋습니다. 

이 시리즈의 다른 문서를 참조하세요.

[1. VNet 개요](how-to-network-security-overview.md) > **2. 워크스페이스 보안** > [3. 학습 환경 보안](how-to-secure-training-vnet.md) > [4. 추론 환경 보안](how-to-secure-inferencing-vnet.md) > [5. 스튜디오 기능 사용 설정](how-to-enable-studio-virtual-network.md)

이 문서에서는 가상 네트워크에서 다음 작업 영역 리소스를 사용하는 방법을 알아봅니다.
> [!div class="checklist"]
> - Azure Machine Learning 작업 영역
> - Azure Storage 계정
> - Azure Machine Learning 데이터 저장소 및 데이터 세트
> - Azure Key Vault
> - Azure Container Registry

## <a name="prerequisites"></a>사전 요구 사항

+ 일반적인 가상 네트워크 시나리오 및 전반적인 가상 네트워크 구조를 이해하려면 [네트워크 보안 개요](how-to-network-security-overview.md) 문서를 참조하세요.

+ 컴퓨팅 리소스에 사용할 기존 가상 네트워크 및 서브넷입니다.

+ 가상 네트워크 또는 서브넷에 리소스를 배포하려면 사용자 계정에 Azure RBAC(Azure 역할 기반 액세스 제어)에서 다음 작업에 대한 사용 권한이 있어야 합니다.

    - 가상 네트워크 리소스에 대한 "Microsoft.Network/virtualNetworks/join/action"
    - 서브넷 리소스에 대한 "Microsoft.Network/virtualNetworks/subnet/join/action"

    네트워킹과 Azure RBAC에 대한 자세한 내용은 [네트워킹 기본 제공 역할](../role-based-access-control/built-in-roles.md#networking)을 참조하세요.


## <a name="secure-the-workspace-with-private-endpoint"></a>프라이빗 엔드포인트로 작업 영역 보호

Azure Private Link를 사용하면 프라이빗 엔드포인트를 사용하여 작업 영역에 연결할 수 있습니다. 프라이빗 엔드포인트는 가상 네트워크 내에 있는 일련의 개인 IP 주소입니다. 그런 다음, 작업 영역에 대한 액세스를 개인 IP 주소를 통해서만 발생하도록 제한할 수 있습니다. 개인 링크를 사용하면 데이터 반출의 위험을 줄일 수 있습니다.

개인 링크 작업 영역을 설정하는 방법에 대한 자세한 내용은 [Private Link 구성 방법](how-to-configure-private-link.md)을 참조하세요.

> [!WARNING]
> 프라이빗 엔드포인트를 사용하여 작업 영역을 보호해도 완전한 보안이 보장되지는 않습니다. 이 문서의 나머지 부분에 있는 단계와 VNet 시리즈를 수행하여 솔루션의 개별 구성요소를 보호해야 합니다. 예를 들어 작업 영역에 프라이빗 엔드포인트를 사용하지만 Azure Storage 계정이 VNet으로 보호되지 않는 경우, 작업 영역과 스토리지 간 트래픽은 보안을 위해 VNet을 사용하지 않습니다.

## <a name="secure-azure-storage-accounts-with-service-endpoints"></a>서비스 엔드포인트가 있는 보안 Azure Storage 계정

Azure Machine Learning은 서비스 엔드포인트나 프라이빗 엔드포인트 중 하나를 사용하도록 구성된 스토리지 계정을 지원합니다. 이 섹션에서는 서비스 엔드포인트를 사용하여 Azure Storage 계정을 보호하는 방법에 대해 알아봅니다. 프라이빗 엔드포인트는 다음 섹션을 참조하세요.

가상 네트워크의 작업 영역에 Azure 스토리지 계정을 사용하려면 다음 단계를 사용합니다.

1. Azure Portal에서 작업 영역에서 사용하려는 스토리지 서비스로 이동합니다.

   [![Azure Machine Learning 작업 영역에 연결된 스토리지](./media/how-to-enable-virtual-network/workspace-storage.png)](./media/how-to-enable-virtual-network/workspace-storage.png#lightbox)

1. 스토리지 서비스 계정 페이지에서 __네트워킹__ 을 선택합니다.

   ![Azure Portal에 있는 Azure Storage 페이지의 네트워킹 영역](./media/how-to-enable-virtual-network/storage-firewalls-and-virtual-networks.png)

1. __방화벽 및 가상 네트워크__ 탭에서 다음 작업을 수행합니다.
    1. __선택한 네트워크__ 를 선택합니다.
    1. __가상 네트워크__ 에서 __기존 가상 네트워크 추가__ 링크를 선택합니다. 이 작업을 수행하면 컴퓨팅이 상주하는 가상 네트워크가 추가됩니다(1단계 참조).

        > [!IMPORTANT]
        > 스토리지 계정은 학습 또는 유추에 사용되는 컴퓨팅 인스턴스 또는 클러스터와 동일한 가상 네트워크 및 서브넷에 있어야 합니다.

    1. __신뢰할 수 있는 Microsoft 서비스가 이 스토리지 계정에 액세스하도록 허용합니다__ 확인란을 선택합니다. 이 변경 내용은 모든 Azure 서비스에 스토리지 계정에 대한 액세스 권한을 부여하지 않습니다.
    
        * **구독에 등록된 경우** 일부 서비스의 리소스는 선택 작업에 대해 **동일한 구독** 에 있는 스토리지 계정에 액세스할 수 있습니다. 예를 들어 로그를 작성하거나 백업을 만듭니다.
        * 일부 서비스의 리소스에는 시스템이 할당한 관리 ID에 __Azure 역할을 할당하여__ 스토리지 계정에 대한 명시적 액세스 권한을 부여할 수 있습니다.

        자세한 내용은 [Azure Storage 방화벽 및 가상 네트워크 구성](../storage/common/storage-network-security.md#trusted-microsoft-services)을 참조하세요.

    > [!IMPORTANT]
    > Azure Machine Learning SDK를 사용하는 경우 개발 환경에서 Azure Storage 계정에 연결할 수 있어야 합니다. 스토리지 계정이 가상 네트워크 내에 있는 경우 방화벽에서 개발 환경 IP 주소의 액세스를 반드시 허용해야 합니다.
    >
    > 스토리지 계정에 액세스가 가능하도록 설정하려면 개발 클라이언트의 웹 브라우저에서 스토리지 계정에 대한 __방화벽 및 가상 네트워크__ 를 방문하세요. 그런 다음, __클라이언트 IP 주소 추가__ 확인란을 사용하여 클라이언트의 IP 주소를 __주소 범위__ 에 추가합니다. __주소 범위__ 필드를 사용하여 개발 환경의 IP 주소를 수동으로 입력할 수도 있습니다. 클라이언트의 IP 주소가 추가되고 나면 SDK를 사용하여 스토리지 계정에 액세스할 수 있습니다.

   [![Azure Portal의 "방화벽 및 가상 네트워크" 창](./media/how-to-enable-virtual-network/storage-firewalls-and-virtual-networks-page.png)](./media/how-to-enable-virtual-network/storage-firewalls-and-virtual-networks-page.png#lightbox)

> [!TIP]
> 서비스 엔드포인트를 사용하는 경우 퍼블릭 액세스를 사용하지 않도록 설정할 수도 있습니다. 자세한 내용은 [퍼블릭 읽기 권한 허용 안 함](../storage/blobs/anonymous-read-access-configure.md#allow-or-disallow-public-read-access-for-a-storage-account)을 참조하세요.

## <a name="secure-azure-storage-accounts-with-private-endpoints"></a>프라이빗 엔드포인트가 있는 보안 Azure Storage 계정

Azure Machine Learning은 서비스 엔드포인트나 프라이빗 엔드포인트 중 하나를 사용하도록 구성된 스토리지 계정을 지원합니다. 스토리지 계정에서 프라이빗 엔드포인트를 사용하는 경우 기본 스토리지 계정에 대해 두 개의 개인 엔드포인트를 구성해야 합니다.
1. **BLOB** 대상 하위 리소스가 있는 프라이빗 엔드포인트입니다.
1. **파일** 대상 하위 리소스가 있는 프라이빗 엔드포인트입니다(FileShare).

![BLOB 및 파일 옵션을 사용하여 프라이빗 엔드포인트 구성 페이지를 보여 주는 스크린샷](./media/how-to-enable-studio-virtual-network/configure-storage-private-endpoint.png)

기본 스토리지가 **아닌** 스토리지 계정에 대한 프라이빗 엔드포인트를 구성하려면 추가하려는 스토리지 계정에 해당하는 **대상 하위 리소스** 종류를 선택합니다.

자세한 내용은 [Azure Storage에 프라이빗 엔드포인트 사용](../storage/common/storage-private-endpoints.md)을 참조하세요.

> [!TIP]
> 프라이빗 엔드포인트를 사용하는 경우 퍼블릭 액세스를 사용하지 않도록 설정할 수도 있습니다. 자세한 내용은 [퍼블릭 읽기 권한 허용 안 함](../storage/blobs/anonymous-read-access-configure.md#allow-or-disallow-public-read-access-for-a-storage-account)을 참조하세요.
## <a name="secure-datastores-and-datasets"></a>데이터 저장소 및 데이터 세트 보안

이 섹션에서는 가상 네트워크를 사용하여 SDK 환경에서 데이터 저장소 및 데이터 세트를 사용하는 방법에 대해 알아봅니다. 스튜디오 환경에 대한 자세한 내용은 [가상 네트워크에서 Azure Machine Learning 스튜디오 사용](how-to-enable-studio-virtual-network.md)을 참조하세요.

SDK를 사용하여 데이터에 액세스하려면 데이터가 저장되는 개별 서비스에 필요한 인증 방법을 사용해야 합니다. 예를 들어 Azure Data Lake Store Gen2에 액세스 하기 위해 데이터 저장소를 등록하는 경우 [Azure 스토리지 서비스에 연결](how-to-access-data.md#azure-data-lake-storage-generation-2)에 설명된 대로 서비스 주체를 계속 사용해야 합니다.

### <a name="disable-data-validation"></a>데이터 유효성 검사 사용 안 함

기본적으로 Azure Machine Learning은 SDK를 사용하여 데이터에 액세스하려고 할 때 데이터 유효성 및 자격 증명 확인을 수행합니다. 데이터가 가상 네트워크 뒤에 있는 경우 Azure Machine Learning은 이러한 검사를 완료할 수 없습니다. 이 확인을 무시하려면 유효성 검사를 건너뛰는 데이터 저장소 및 데이터 세트를 만들어야 합니다.

### <a name="use-datastores"></a>데이터 저장소 사용

 Azure Data Lake Store Gen1과 Azure Data Lake Store Gen2는 기본적으로 유효성 검사를 건너뛰기 때문에 추가 작업이 필요하지 않습니다. 그러나 다음 서비스의 경우 유사한 구문을 사용하여 데이터 저장소 유효성 검사를 건너뛸 수 있습니다.

- Azure Blob Storage
- Azure 파일 공유
- PostgreSQL
- Azure SQL Database

다음 코드 샘플을 사용하면 새 Azure Blob 데이터 저장소 및 집합을 만듭니다.`skip_validation=True`

```python
blob_datastore = Datastore.register_azure_blob_container(workspace=ws,  

                                                         datastore_name=blob_datastore_name,  

                                                         container_name=container_name,  

                                                         account_name=account_name, 

                                                         account_key=account_key, 

                                                         skip_validation=True ) // Set skip_validation to true
```

### <a name="use-datasets"></a>데이터 세트 사용

데이터 세트 유효성 검사를 건너뛰는 구문은 다음과 같은 데이터 세트 형식과 유사합니다.
- 구분된 파일
- JSON 
- Parquet
- SQL
- 파일

다음 코드는 새 JSON 데이터 세트를 만들고 `validate=False`를 설정합니다.

```python
json_ds = Dataset.Tabular.from_json_lines_files(path=datastore_paths, 

validate=False) 

```

## <a name="secure-azure-key-vault"></a>보안 Azure Key Vault

Azure Machine Learning는 연결된 키 자격 증명 모음 인스턴스를 사용하여 다음 자격 증명을 저장합니다.
* 연결된 스토리지 계정 연결 문자열
* Azure Container Repository 인스턴스에 대한 암호
* 데이터 저장소에 대한 연결 문자열

가상 네트워크 뒤 Azure Key Vault에서 Azure Machine Learning 실험 기능을 사용하려면 다음 단계를 사용하세요.

1. 작업 영역과 연결된 키 자격 증명 모음으로 이동합니다.

1. __Key Vault__ 페이지의 왼쪽 창에서 __네트워킹__ 을 선택합니다.

1. __방화벽 및 가상 네트워크__ 탭에서 다음 작업을 수행합니다.
    1. __다음에서 액세스 허용__ 에서 __프라이빗 엔드포인트 및 선택한 네트워크__ 를 선택합니다.
    1. __가상 네트워크__ 에서 __기존 가상 네트워크 추가__ 를 선택하여 실험 컴퓨팅이 상주하는 가상 네트워크를 추가합니다.
    1. __신뢰할 수 있는 Microsoft 서비스가 이 방화벽을 바이패스하도록 허용하시겠습니까?__ 에 __예__ 를 선택합니다.

   [![Key Vault 창의 "방화벽 및 가상 네트워크" 섹션](./media/how-to-enable-virtual-network/key-vault-firewalls-and-virtual-networks-page.png)](./media/how-to-enable-virtual-network/key-vault-firewalls-and-virtual-networks-page.png#lightbox)

## <a name="enable-azure-container-registry-acr"></a>Azure Container Registry(ACR) 사용

가상 네트워크 내의 Azure Container Registry를 사용하려면 다음을 충족해야 합니다.

* Azure Container Registry가 프리미엄 버전이어야 합니다. 업그레이드에 대한 자세한 내용은 [SKU 변경](../container-registry/container-registry-skus.md#changing-tiers)을 참조하세요.

* Azure Container Registry가 스토리지 계정 및 학습 또는 유추에 사용되는 컴퓨팅 대상과 동일한 가상 네트워크 및 서브넷에 있어야 합니다.

* Azure Machine Learning 작업 영역에 [Azure Machine Learning 컴퓨팅 클러스터](how-to-create-attach-compute-cluster.md)가 포함되어야 합니다.

    ACR이 가상 네트워크 뒤에 있으면 Azure Machine Learning에서 ACR을 사용하여 Docker 이미지를 직접 빌드할 수 없습니다. 대신 컴퓨팅 클러스터가 이미지를 빌드하는 데 사용됩니다.

    > [!IMPORTANT]
    > Docker 이미지를 빌드하는 데 사용되는 컴퓨팅 클러스터는 모델을 학습하고 배포하는 데 사용되는 패키지 리포지토리에 액세스할 수 있어야 합니다. 공용 리포지토리에 대한 액세스를 허용하는 네트워크 보안 규칙을 추가하거나, [프라이빗 Python 패키지를 사용](how-to-use-private-python-packages.md)하거나, 이미 패키지가 포함된 [사용자 지정 Docker 이미지](how-to-train-with-custom-image.md)를 사용해야 할 수 있습니다.

이러한 요구 사항이 충족되면 다음 단계를 사용하여 Azure Container Registry를 사용하도록 설정합니다.

> [!TIP]
> 작업 영역을 만들 때 기존 Azure Container Registry를 사용하지 않았다면 없을 수 있습니다. 기본적으로 작업 영역은 ACR 인스턴스가 필요할 때까지 만들지 않습니다. 인스턴스를 강제로 만들도록 하려면 이 섹션의 단계를 사용하기 전에 작업 영역을 사용하여 모델을 학습하거나 배포합니다.

1. 작업 영역에 대한 Azure Container Registry 이름을 찾아 다음 방법 중 하나를 사용합니다.

    __Azure Portal__

    작업 영역의 개요 섹션에 __레지스트리__ 값은 Azure Container Registry에 연결됩니다.

    :::image type="content" source="./media/how-to-enable-virtual-network/azure-machine-learning-container-registry.png" alt-text="작업 영역에 대한 Azure Container Registry" border="true":::

    __Azure CLI__

    [Azure CLI용 Machine Learning 확장을 설치](reference-azure-machine-learning-cli.md)했으면 `az ml workspace show` 명령을 사용하여 작업 영역 정보를 표시할 수 있습니다.

    ```azurecli-interactive
    az ml workspace show -w yourworkspacename -g resourcegroupname --query 'containerRegistry'
    ```

    이 명령은 `"/subscriptions/{GUID}/resourceGroups/{resourcegroupname}/providers/Microsoft.ContainerRegistry/registries/{ACRname}"`와 비슷한 값을 반환합니다. 문자열의 마지막 부분은 작업 영역에 대한 Azure Container Registry의 이름입니다.

1. [레지스트리에 대한 네트워크 액세스 구성](../container-registry/container-registry-vnet.md#configure-network-access-for-registry)의 단계를 사용하여 가상 네트워크에 대한 액세스를 제한합니다. 가상 네트워크를 추가할 때 Azure Machine Learning 리소스에 대한 가상 네트워크와 서브넷을 선택합니다.

1. 작업 영역에 대한 ACR을 구성하여 [신뢰할 수 있는 서비스에 의한 액세스를 허용](../container-registry/allow-access-trusted-services.md)합니다.

1. Azure Machine Learning Python SDK를 사용하여 Docker 이미지를 빌드하는 컴퓨팅 클러스터를 구성합니다. 다음 코드 조각은 수행하는 방법을 보여줍니다.

    ```python
    from azureml.core import Workspace
    # Load workspace from an existing config file
    ws = Workspace.from_config()
    # Update the workspace to use an existing compute cluster
    ws.update(image_build_compute = 'mycomputecluster')
    # To switch back to using ACR to build (if ACR is not in the VNet):
    # ws.update(image_build_compute = '')
    ```

    > [!IMPORTANT]
    > 스토리지 계정, 컴퓨팅 클러스터 및 Azure Container Registry는 모두 가상 네트워크의 동일한 서브넷에 있어야 합니다.
    
    자세한 내용은 [update ()](/python/api/azureml-core/azureml.core.workspace.workspace#update-friendly-name-none--description-none--tags-none--image-build-compute-none--enable-data-actions-none-) 메서드 참조를 확인하세요.

> [!TIP]
> VNet으로 보호되는 ACR에 대한 [퍼블릭 액세스를 사용하지 않도록 설정](../container-registry/container-registry-access-selected-networks.md#disable-public-network-access)할 수도 있습니다.
## <a name="next-steps"></a>다음 단계

이 문서는 5부로 구성된 가상 네트워크 시리즈의 2부입니다. 가상 네트워크를 보호하는 방법을 알아보려면 시리즈의 나머지 문서를 참조하세요.

* [1부: 가상 네트워크 개요](how-to-network-security-overview.md)
* [3부: 학습 환경 보안](how-to-secure-training-vnet.md)
* [4부: 추론 환경 보호](how-to-secure-inferencing-vnet.md)
* .[5부: 스튜디오 기능 사용](how-to-enable-studio-virtual-network.md)

또한 이름 확인을 위한 [사용자 지정 DNS](how-to-custom-dns.md) 사용에 대한 문서를 참조하세요.
