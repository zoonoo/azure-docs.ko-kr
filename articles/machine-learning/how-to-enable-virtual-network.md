---
title: 네트워크 격리 및 프라이버시
titleSuffix: Azure Machine Learning
description: 격리된 Azure Virtual Network를 사용하여 Azure Machine Learning에서 실험/학습은 물론 유추/채점 작업을 보호합니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: larryfr
ms.author: aashishb
author: aashishb
ms.date: 07/07/2020
ms.topic: conceptual
ms.custom: how-to, contperfq4, devx-track-python
ms.openlocfilehash: 58f0a25ebeb7fb715cfba27d7482a031d1fe8c32
ms.sourcegitcommit: b8702065338fc1ed81bfed082650b5b58234a702
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/11/2020
ms.locfileid: "88121207"
---
# <a name="network-isolation-during-training-and-inference-with-private-endpoints-and-virtual-networks"></a>개인 끝점 및 가상 네트워크를 사용 하 여 학습 및 유추 중 네트워크 격리
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

이 문서에서는 Azure Virtual Network (vnet) 내에서 Azure Machine Learning 교육 및 유추 작업을 격리 하 여 machine learning 수명 주기를 보호 하는 방법을 알아봅니다. __가상 네트워크__는 공용 인터넷에서 Azure 리소스를 격리하는 보안 경계의 역할을 합니다. Azure 가상 네트워크를 온-프레미스 네트워크에 연결할 수도 있습니다. 네트워크에 조인하면 모델을 안전하게 교육하고, 배포한 모델에 액세스하여 유추할 수 있습니다.

__개인 끝점__을 사용 하 여 가상 네트워크에서 Azure Machine Learning 작업 영역에 액세스할 수 있습니다. 개인 끝점은 가상 네트워크 내의 개인 IP 주소 집합 이며 작업 영역에 대 한 액세스는 가상 네트워크로 제한 됩니다. 개인 끝점은 데이터 exfiltration의 위험을 줄이는 데 도움이 됩니다. 프라이빗 엔드포인트에 대한 자세한 내용은 [Azure Private Link](/azure/private-link/private-link-overview) 문서를 참조하세요.

> [!NOTE]
> Mozilla Firefox를 사용 하는 경우 개인 끝점을 통해 작업 영역에 액세스 하는 데 문제가 발생할 수 있습니다. 브라우저에서 HTTPS를 통한 DNS 설정과 관련 된 문제가 있을 수 있습니다. 이 문제를 해결 하려면 Microsoft Edge 또는 Google Chrome을 사용 하는 것이 좋습니다.

Azure Machine Learning는 데이터 저장소 및 계산 리소스 (모델을 학습 하 고 배포 하는 데 사용 됨)에 대해 다른 Azure 서비스에 의존 합니다. 이러한 리소스는 가상 네트워크 내에서 만들 수도 있습니다. 예를 들어 Azure Machine Learning 컴퓨팅을 사용하여 모델을 교육한 다음, 이 모델을 AKS(Azure Kubernetes Service)에 배포할 수 있습니다. 

## <a name="prerequisites"></a>사전 요구 사항

+ Azure Machine Learning [작업 영역](how-to-manage-workspace.md)

+ [Azure Virtual Network 서비스](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) 및 [IP 네트워킹](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm)에 대한 일반적인 실무 지식

+ 컴퓨팅 리소스에 사용할 기존 가상 네트워크 및 서브넷

+ 가상 네트워크 또는 서브넷에 리소스를 배포 하려면 사용자 계정에 Azure 역할 기반 액세스 제어 (RBAC)에서 다음 작업을 수행할 수 있는 권한이 있어야 합니다.

    - 가상 네트워크 리소스에 대 한 "Microsoft. Network/virtualNetworks/join/action".
    - 서브넷 리소스에 대 한 "Microsoft. Network/virtualNetworks/subnet/join/action".

    네트워크를 사용 하는 RBAC에 대 한 자세한 내용은 [네트워킹 기본 제공 역할](/azure/role-based-access-control/built-in-roles#networking) 을 참조 하세요.

## <a name="secure-your-workspace"></a>작업 영역 보안

Azure Machine Learning 작업 영역에는 __공용 끝점이__ 나 __개인 끝점__중 하나가 있을 수 있습니다. 공용 끝점은 공용 인터넷에서 액세스할 수 있는 IP 주소 집합이 며 개인 끝점은 가상 네트워크 내의 개인 IP 주소 집합입니다. 

개인 IP 주소를 통해서만 발생 하도록 작업 영역에 대 한 액세스를 제한 하려면 개인 끝점을 사용 합니다.

작업 영역에 대 한 통신은 가상 네트워크 에서만 허용 되므로 작업 영역을 사용 하는 모든 개발 환경은 가상 네트워크의 구성원 이어야 합니다. 예를 들어 가상 네트워크의 가상 머신이 있습니다.

> [!IMPORTANT]
> 개인 끝점은 작업 영역을 삭제 하거나 계산 리소스를 관리 하는 것과 같은 Azure 제어 평면 (관리 작업)에 영향을 주지 않습니다. 예를 들어 계산 대상을 생성, 업데이트 또는 삭제 합니다. 이러한 작업은 일반적인 공용 인터넷을 통해 수행 됩니다.
>
> 개인 끝점은 가상 네트워크 외부에서 작업 영역에 대 한 액세스를 차단 합니다.

개인 끝점을 사용 하는 리소스의 일부 조합에는 Enterprise edition 작업 영역이 필요 합니다. Enterprise Edition이 필요한 시나리오를 이해하려면 다음 표를 참조하세요.

| 시나리오 | Enterprise</br>edition | Basic</br>edition |
| ----- |:-----:|:-----:| 
| 가상 네트워크 또는 개인 끝점 없음 | ✔ | ✔ |
| 전용 끝점이 없는 작업 영역입니다. 가상 네트워크의 기타 리소스(Azure Container Registry 제외) | ✔ | ✔ |
| 전용 끝점이 없는 작업 영역입니다. 전용 끝점을 사용 하는 기타 리소스 | ✔ | |
| 개인 끝점이 있는 작업 영역입니다. 가상 네트워크의 기타 리소스(Azure Container Registry 제외) | ✔ | ✔ |
| 작업 영역 및 개인 끝점을 사용 하는 기타 리소스 | ✔ | |
| 개인 끝점이 있는 작업 영역입니다. 개인 끝점 또는 가상 네트워크가 없는 기타 리소스 | ✔ | ✔ |
| 가상 네트워크의 Azure Container Registry | ✔ | |
| 작업 영역에 대 한 고객 관리 키 | ✔ | |

> [!WARNING]
> 
> Azure Machine Learning 계산 인스턴스는 개인 끝점이 사용 하도록 설정 된 작업 영역에서 지원 되지 않습니다.
>
> Azure Machine Learning는 개인 끝점이 사용 하도록 설정 된 Azure Kubernetes 서비스 사용을 지원 하지 않습니다. 대신 가상 네트워크에서 Azure Kubernetes Service를 사용할 수 있습니다. 자세한 내용은 [Azure Virtual Network 내에서 Azure ML 실험 및 유추 작업 보호](how-to-enable-virtual-network.md)를 참조하세요.

Azure의 개인 끝점에 대 한 자세한 내용은 [Azure 개인 링크](/azure/private-link/private-link-overview) 문서를 참조 하세요.

### <a name="create-a-workspace-that-uses-a-private-endpoint"></a>개인 끝점을 사용 하는 작업 영역 만들기

Azure Machine Learning SDK, CLI, Azure Resource Manager 템플릿 또는 Azure Portal를 사용 하 여 개인 끝점으로 새 작업 영역을 만들 수 있습니다.

__요구 사항__

* 개인 끝점에서 사용 하는 가상 네트워크는 네트워크 정책을 사용 하지 않도록 설정 해야 합니다. 자세한 내용은 [개인 끝점에 대 한 네트워크 정책 사용 안 함](/azure/private-link/disable-private-endpoint-network-policy)을 참조 하세요.

__제한 사항__

* 공용 인터넷을 통해 작업 영역에 연결할 수 없으며 가상 네트워크 내 에서만 작업 영역에 연결할 수 있습니다.

* 개인 끝점을 사용 하 여 여러 작업 영역을 만들고 동일한 개인 DNS 영역을 사용 하는 경우에는 첫 번째 작업 영역만 개인 DNS 영역의 __가상 네트워크 링크__ 에 추가 됩니다.

    이 제한을 해결 하려면 추가 작업 영역에 대 한 가상 네트워크 링크를 수동으로 추가 합니다. 자세한 내용은 [가상 네트워크 링크](/azure/dns/private-dns-virtual-network-links)를 참조 하세요.

__Configuration__

가상 네트워크 및 개인 끝점을 사용 하 여 작업 영역을 만드는 방법 및 기타 구성 옵션에 대 한 자세한 내용은 다음 문서를 참조 하세요.

* [Azure Resource Manager 템플릿을 사용 하 여 Azure Machine Learning에 대 한 작업 영역을 만듭니다](how-to-create-workspace-template.md).
* [포털에서 작업 영역을 만듭니다](how-to-manage-workspace.md).
* [Azure CLI를 사용 하 여 작업 영역을 만듭니다](how-to-manage-workspace-cli.md).
* Python SDK를 사용 하려면 [PrivateEndPointConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.private_endpoint.privateendpointconfig?view=azure-ml-py) 및 [작업 영역. 만들기 ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py#create-name--auth-none--subscription-id-none--resource-group-none--location-none--create-resource-group-true--sku--basic---friendly-name-none--storage-account-none--key-vault-none--app-insights-none--container-registry-none--cmk-keyvault-none--resource-cmk-uri-none--hbi-workspace-false--default-cpu-compute-target-none--default-gpu-compute-target-none--private-endpoint-config-none--private-endpoint-auto-approval-true--exist-ok-false--show-output-true-) 참조 설명서를 참조 하세요.

<a id="amlcompute"></a>

## <a name="machine-learning-studio"></a>Machine Learning studio

__요구 사항__

* 저장소 계정의 데이터에 액세스 하려면 저장소 계정이 작업 영역과 동일한 가상 네트워크에 있어야 합니다.

* 데이터가 가상 네트워크에 저장 된 경우 작업 영역 [관리 id](../active-directory/managed-identities-azure-resources/overview.md) 를 사용 하 여 studio에서 데이터에 액세스 권한을 부여 해야 합니다.

    > [!IMPORTANT]
    > 대부분의 스튜디오는 가상 네트워크에 저장 된 데이터와 함께 작동 하지만 통합 된 노트북은 __그렇지 않습니다__. 통합 된 노트북은 가상 네트워크에 있는 저장소 사용을 지원 하지 않습니다. 대신, 계산 인스턴스에서 Jupyter 노트북을 사용할 수 있습니다. 자세한 내용은 [계산 클러스터 & 인스턴스](#compute-instance) 섹션을 참조 하세요.

    Studio 액세스 권한을 부여 하지 않으면 `Error: Unable to profile this dataset. This might be because your data is stored behind a virtual network or your data does not support profile.` 다음 작업을 사용할 수 없게 됩니다.

    * Studio에서 데이터를 미리 봅니다.
    * 디자이너에서 데이터를 시각화 합니다.
    * AutoML 실험을 제출 합니다.
    * 레이블 지정 프로젝트를 시작 합니다.

__제한 사항__

* Azure Machine Learning studio는 가상 네트워크의 다음 데이터 저장소 유형에 서 데이터 읽기를 지원 합니다.

    * Azure Blob
    * Azure Data Lake Storage Gen1
    * Azure Data Lake Storage Gen2
    * Azure SQL Database

__Configuration__

* 데이터에 액세스 하기 위해 __관리 되는 id를 사용 하도록 데이터 저장소를 구성__ 합니다. 이러한 단계는 Azure 리소스 기반 액세스 제어 (RBAC)를 사용 하 여 저장소 서비스에 작업 영역 관리 id를 __판독기__ 로 추가 합니다. __읽기 권한자__ 액세스를 통해 작업 영역에서 방화벽 설정을 검색 하 고 데이터가 가상 네트워크를 떠나지 않도록 할 수 있습니다.

    1. 스튜디오에서 __Datastores__를 선택 합니다.

    1. 새 데이터 저장소를 만들려면 __+ 새 데이터 저장소__를 선택 합니다. 기존 항목을 업데이트 하려면 데이터 저장소를 선택 하 고 __자격 증명 업데이트__를 선택 합니다.

    1. 데이터 저장소 설정에서 __작업 영역 관리 id를 사용 하 여 저장소에 액세스할 Azure Machine Learning 서비스 허용__에 대해 __예__ 를 선택 합니다.

    > [!NOTE]
    > 이러한 변경 내용을 적용 하는 데 최대 10 분이 걸릴 수 있습니다.

* __Azure blob storage__의 경우 blob storage에서 데이터를 읽을 수 있도록 작업 영역 관리 Id를 [blob 데이터 판독기](../role-based-access-control/built-in-roles.md#storage-blob-data-reader) 로 추가 해야 합니다.

* 디자이너는 작업 영역에 연결 된 저장소 계정을 사용 하 여 기본적으로 출력을 저장 합니다. 그러나 사용자가 액세스할 수 있는 모든 데이터 저장소에 출력을 저장 하도록 지정할 수 있습니다. 환경에서 가상 네트워크를 사용 하는 경우 이러한 컨트롤을 사용 하 여 데이터가 안전 하 게 유지 되 고 액세스 가능한 지 확인할 수 있습니다. 파이프라인에 대 한 새 기본 저장소를 설정 하려면 다음을 수행 합니다.

    1. 파이프라인 초안에서 파이프라인 제목 근처의 **설정 기어 아이콘** 을 선택 합니다.
    1. **기본 데이터 저장소 선택** 항목을 선택 합니다.
    1. 새 데이터 저장소를 지정 합니다.

    모듈 별로 기본 데이터 저장소를 재정의할 수도 있습니다. 이를 통해 각 개별 모듈의 저장소 위치를 제어할 수 있습니다.

    1. 출력을 지정 하려는 모듈을 선택 합니다.
    1. **출력 설정** 섹션을 확장 합니다.
    1. **기본 출력 설정 재정의**를 선택 합니다.
    1. **출력 설정 설정**을 선택 합니다.
    1. 새 데이터 저장소를 지정 합니다.

* __Azure Data Lake Storage Gen2__사용 하는 경우 RBAC 및 POSIX 스타일의 acl (액세스 제어 목록)을 모두 사용 하 여 가상 네트워크 내에서 데이터 액세스를 제어할 수 있습니다.

    RBAC를 사용 하려면 [Blob 데이터 판독기](../role-based-access-control/built-in-roles.md#storage-blob-data-reader) 역할에 작업 영역 관리 id를 추가 합니다. 자세한 내용은 [역할 기반 액세스 제어](../storage/blobs/data-lake-storage-access-control.md#role-based-access-control)를 참조하세요.

    Acl을 사용 하려면 다른 보안 원칙과 마찬가지로 작업 영역 관리 id에 액세스 권한을 할당할 수 있습니다. 자세한 내용은 [파일 및 디렉터리에 대 한 액세스 제어 목록](../storage/blobs/data-lake-storage-access-control.md#access-control-lists-on-files-and-directories)을 참조 하세요.

* __Azure Data Lake Storage Gen1__ 는 POSIX 스타일의 액세스 제어 목록만 지원 합니다. 다른 보안 주체와 마찬가지로 리소스에 작업 영역 관리 id 액세스 권한을 할당할 수 있습니다. 자세한 내용은 [Azure Data Lake Storage Gen1의 Access control](../data-lake-store/data-lake-store-access-control.md)을 참조 하세요.

* 관리 id를 사용 하 여 __Azure SQL Database__ 에 저장 된 데이터에 액세스 하려면 관리 되는 id에 매핑되는 SQL 포함 된 사용자를 만들어야 합니다. 외부 공급자에서 사용자를 만드는 방법에 대 한 자세한 내용은 [AZURE AD id에 매핑된 포함 된 사용자 만들기](../azure-sql/database/authentication-aad-configure.md#create-contained-users-mapped-to-azure-ad-identities)를 참조 하세요.

    SQL 포함 된 사용자를 만든 후에는 [Grant t-sql 명령을](https://docs.microsoft.com/sql/t-sql/statements/grant-object-permissions-transact-sql)사용 하 여 해당 사용자에 게 사용 권한을 부여 합니다.

* __가상 네트워크 내의 리소스__ (예: 계산 인스턴스 또는 가상 머신)에서 studio에 액세스 하는 경우 가상 네트워크에서 스튜디오로의 아웃 바운드 트래픽을 허용 해야 합니다. 

    예를 들어 NSG (네트워크 보안 그룹)를 사용 하 여 아웃 바운드 트래픽을 제한 하는 경우 __AzureFrontDoor__의 __서비스 태그__ 대상에 규칙을 추가 합니다.

## <a name="use-datastores-and-datasets"></a>데이터 저장소 및 데이터 집합 사용

> [!NOTE]
> 이 섹션에서는 SDK 환경의 데이터 저장소 및 데이터 집합 사용에 대해 설명 합니다. 스튜디오 환경에 대 한 자세한 내용은 [Machine Learning studio](#machine-learning-studio)섹션을 참조 하세요.

__제한 사항__

기본적으로 Azure Machine Learning는 SDK를 사용 하 여 데이터에 액세스 하려고 할 때 데이터 유효성 및 자격 증명 확인을 수행 합니다. 데이터가 가상 네트워크 뒤에 있는 경우 데이터에 액세스할 수 없고 해당 검사에 실패 하는 Azure Machine Learning. 이 문제를 해결 하려면 데이터 저장소 및 데이터 집합을 만들 때 유효성 검사를 건너뜁니다.

* __데이터 저장소__를 사용 하는 경우:

    Azure Data Lake Store Gen1 및 Azure Data Lake Store Gen2는 기본적으로 유효성 검사를 건너뛰고 추가 작업이 필요 하지 않습니다. 그러나 다음 서비스의 경우 유사한 구문을 사용 하 여 데이터 저장소 유효성 검사를 건너뛸 수 있습니다.

    - Azure Blob Storage
    - Azure 파일 공유
    - PostgreSQL
    - Azure SQL Database

    다음 코드 샘플은 새 Azure Blob 데이터 저장소 및 집합을 만듭니다 `skip_validation=True` .

    ```python
    blob_datastore = Datastore.register_azure_blob_container(workspace=ws,  
                                                            datastore_name=blob_datastore_name,  
                                                            container_name=container_name,  
                                                            account_name=account_name, 
                                                            account_key=account_key, 
                                                            skip_validation=True ) // Set skip_validation to true
    ```

* __데이터 집합__을 사용 하는 경우:

    데이터 집합 유효성 검사를 건너뛰는 구문은 다음과 같은 데이터 집합 형식에 대해 유사 합니다.
    - 구분 된 파일
    - JSON 
    - Parquet
    - SQL
    - 파일

    다음 코드는 새 JSON 데이터 집합을 만들고를 설정 `validate=False` 합니다.

    ```python
    json_ds = Dataset.Tabular.from_json_lines_files(path=datastore_paths, 
                                                   validate=False) 
    ```

## <a name="azure-storage-account"></a>Azure Storage 계정

> [!IMPORTANT]
> Azure Machine Learning에 대 한 _기본 저장소 계정과_ _기본이 아닌 저장소 계정을_ 모두 가상 네트워크에 둘 수 있습니다.

__요구 사항__

* 스토리지 계정은 학습 또는 유추에 사용되는 컴퓨팅 인스턴스 또는 클러스터와 동일한 가상 네트워크 및 서브넷에 있어야 합니다.

__Configuration__

작업 영역에서 사용 하는 Azure Storage 계정을 보호 하려면 가상 네트워크에서 저장소 계정에 대 한 __개인 끝점__ 또는 __서비스 끝점__ 을 사용 하도록 설정 합니다.

* __개인 끝점__을 사용 하도록 저장소 계정을 구성 하려면 [개인 끝점 사용](/azure/storage/common/storage-private-endpoints.md) 문서를 참조 하세요.

* __서비스 끝점__을 사용 하도록 저장소 계정을 구성 하려면 다음 단계를 사용 합니다.

    1. 작업 영역에서 사용 하는 가상 네트워크에 저장소 계정을 추가 하려면 [Azure Storage 방화벽 및 가상 네트워크 구성](/azure/storage/common/storage-network-security#grant-access-from-a-virtual-network) 문서의 __가상 네트워크에서 액세스 권한 부여__ 섹션의 정보를 사용 합니다.
    1. 가상 네트워크 (예: Azure Machine Learning)에서 Microsoft 서비스에 대 한 액세스를 허용 하려면 [Azure Storage 방화벽 및 가상 네트워크 구성](/azure/storage/common/storage-network-security#exceptions) 문서의 __예외__ 섹션에 있는 정보를 사용 합니다.
    1. Azure Machine Learning SDK를 사용하는 경우 개발 환경에서 Azure Storage 계정에 연결할 수 있어야 합니다. 스토리지 계정이 가상 네트워크 내에 있는 경우 방화벽에서 개발 환경 IP 주소의 액세스를 반드시 허용해야 합니다. 개발 환경의 IP 주소를 추가 하려면 [Azure Storage 방화벽 및 가상 네트워크 구성](/azure/storage/common/storage-network-security#grant-access-from-an-internet-ip-range) 문서에서 __인터넷 ip 범위에서 액세스 권한 부여__ 섹션의 정보를 사용 합니다.

## <a name="azure-container-registry"></a>Azure Container Registry

__요구 사항__

* Azure Machine Learning 작업 영역이 Enterprise edition이어야 합니다. 업그레이드에 대한 자세한 내용은 [Enterprise edition으로 업그레이드](how-to-manage-workspace.md#upgrade)를 참조하세요.
* Azure Machine Learning 작업 영역 지역은 [개인 링크를 사용 하도록 설정 된 지역](https://docs.microsoft.com/azure/private-link/private-link-overview#availability)이어야 합니다. 
* Azure Container Registry은 프리미엄 버전 이어야 합니다. 업그레이드에 대한 자세한 내용은 [SKU 변경](/azure/container-registry/container-registry-skus#changing-skus)을 참조하세요.
* Azure Container Registry가 스토리지 계정 및 학습 또는 유추에 사용되는 컴퓨팅 대상과 동일한 가상 네트워크 및 서브넷에 있어야 합니다.
* Azure Machine Learning 작업 영역에 [Azure Machine Learning 컴퓨팅 클러스터](how-to-set-up-training-targets.md#amlcompute)가 포함되어야 합니다.

__제한 사항__

* ACR이 가상 네트워크 뒤에 있으면 Azure Machine Learning에서 ACR을 사용하여 Docker 이미지를 직접 빌드할 수 없습니다. 대신 컴퓨팅 클러스터가 이미지를 빌드하는 데 사용됩니다.

__Configuration__

1. 작업 영역에 대한 Azure Container Registry 이름을 찾으려면 다음 방법 중 하나를 사용합니다.

    __Azure Portal__

    작업 영역의 개요 섹션에 __레지스트리__ 값은 Azure Container Registry에 연결됩니다.

    :::image type="content" source="./media/how-to-enable-virtual-network/azure-machine-learning-container-registry.png" alt-text="작업 영역에 대한 Azure Container Registry" border="true":::

    __Azure CLI__

    [Azure CLI용 Machine Learning 확장을 설치](reference-azure-machine-learning-cli.md)했으면 `az ml workspace show` 명령을 사용하여 작업 영역 정보를 표시할 수 있습니다.

    ```azurecli-interactive
    az ml workspace show -w yourworkspacename -g resourcegroupname --query 'containerRegistry'
    ```

    이 명령은 `"/subscriptions/{GUID}/resourceGroups/{resourcegroupname}/providers/Microsoft.ContainerRegistry/registries/{ACRname}"`와 비슷한 값을 반환합니다. 문자열의 마지막 부분은 작업 영역에 대한 Azure Container Registry의 이름입니다.

1. 가상 네트워크에 대한 액세스를 제한하려면 [레지스트리에 대한 네트워크 액세스 구성](../container-registry/container-registry-vnet.md#configure-network-access-for-registry)의 단계를 참조하세요. 가상 네트워크를 추가할 때 Azure Machine Learning 리소스에 대한 가상 네트워크와 서브넷을 선택합니다.

1. Azure Machine Learning Python SDK를 사용하여 Docker 이미지를 빌드하는 컴퓨팅 클러스터를 구성합니다. 다음 코드 조각은 수행하는 방법을 보여줍니다.

    ```python
    from azureml.core import Workspace
    # Load workspace from an existing config file
    ws = Workspace.from_config()
    # Update the workspace to use an existing compute cluster
    ws.update(image_build_compute = 'mycomputecluster')
    ```

    > [!IMPORTANT]
    > 스토리지 계정, 컴퓨팅 클러스터 및 Azure Container Registry는 모두 가상 네트워크의 동일한 서브넷에 있어야 합니다.
    
    자세한 내용은 [update ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py#update-friendly-name-none--description-none--tags-none--image-build-compute-none--enable-data-actions-none-) 메서드 참조를 확인하세요.

1. 작업 영역에서 ACR 인스턴스와 통신할 수 있도록 하려면 다음 Azure Resource Manager 템플릿을 적용 합니다.

    > [!WARNING]
    > 이 템플릿은 작업 영역에 대 한 개인 끝점을 사용 하도록 설정 하 고 엔터프라이즈 작업 영역으로 변경 합니다. 이러한 변경 내용은 실행 취소할 수 없습니다.

    ```json
    {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "keyVaultArmId": {
        "type": "string"
        },
        "workspaceName": {
        "type": "string"
        },
        "containerRegistryArmId": {
        "type": "string"
        },
        "applicationInsightsArmId": {
        "type": "string"
        },
        "storageAccountArmId": {
        "type": "string"
        },
        "location": {
        "type": "string"
        }
    },
    "resources": [
        {
        "type": "Microsoft.MachineLearningServices/workspaces",
        "apiVersion": "2019-11-01",
        "name": "[parameters('workspaceName')]",
        "location": "[parameters('location')]",
        "identity": {
            "type": "SystemAssigned"
        },
        "sku": {
            "tier": "enterprise",
            "name": "enterprise"
        },
        "properties": {
            "sharedPrivateLinkResources":
    [{"Name":"Acr","Properties":{"PrivateLinkResourceId":"[concat(parameters('containerRegistryArmId'), '/privateLinkResources/registry')]","GroupId":"registry","RequestMessage":"Approve","Status":"Pending"}}],
            "keyVault": "[parameters('keyVaultArmId')]",
            "containerRegistry": "[parameters('containerRegistryArmId')]",
            "applicationInsights": "[parameters('applicationInsightsArmId')]",
            "storageAccount": "[parameters('storageAccountArmId')]"
        }
        }
    ]
    }
    ```

## <a name="key-vault-instance"></a>Key Vault 인스턴스 

__요구 사항__

__제한 사항__

__Configuration__ 

가상 네트워크 Azure Key Vault 뒤에 Azure Machine Learning 실험 기능을 사용 하려면 [Azure Key Vault 방화벽 및 가상 네트워크 구성](/azure/key-vault/general/network-security) 문서를 사용 합니다.

> [!IMPORTANT]
> 문서의 단계를 수행 하는 경우 실험 계산 리소스에서 사용 하는 것과 동일한 가상 네트워크를 사용 합니다. 또한 __신뢰할 수 있는 Microsoft 서비스에서이 방화벽을 우회 하도록 허용__해야 합니다.

## <a name="compute-clusters--instances"></a><a name="compute-instance"></a>컴퓨팅 클러스터 및 인스턴스 

__요구 사항__

* 가상 네트워크가 Azure Machine Learning 작업 영역과 동일한 구독 및 지역에 있어야 합니다.
* 컴퓨팅 인스턴스 또는 클러스터에 지정된 서브넷에는 할당되지 않은 IP 주소가 대상 VM 수를 수용할 만큼 충분히 있어야 합니다. 서브넷에 할당되지 않은 IP 주소가 충분하지 않으면 컴퓨팅 클러스터가 부분적으로 할당됩니다.
* 트래픽을 제한 하 여 가상 네트워크를 보호 하려는 경우 계산 서비스에 대해 일부 포트를 열어 두어야 합니다.
* 하나의 가상 네트워크에 다수의 컴퓨팅 인스턴스나 클러스터를 배치하려는 경우 하나 이상의 리소스에 대해 할당량 증가를 요청해야 할 수 있습니다.
* 작업 영역의 Azure Storage 계정도 가상 네트워크에서 보호되는 경우, Azure Machine Learning 컴퓨팅 인스턴스나 클러스터와 동일한 가상 네트워크에 있어야 합니다. 
* 컴퓨팅 인스턴스 Jupyter 기능이 작동하려면 웹 소켓 통신이 비활성화되어 있지 않아야 합니다.

__제한 사항__

* Machine Learning 컴퓨팅 인스턴스나 클러스터는 __가상 네트워크를 포함하는 리소스 그룹__에 추가 네트워킹 리소스를 자동으로 할당합니다. 각 컴퓨팅 인스턴스 또는 클러스터에 대해 서비스는 다음 리소스를 할당합니다.

    * 네트워크 보안 그룹 1개
    * 공용 IP 주소 1개
    * 부하 분산 장치 1개
    
    __계산 클러스터__의 경우 클러스터를 0 개 노드로 확장할 때마다 이러한 리소스가 삭제 되 고 다시 생성 됩니다.
    
    __계산 인스턴스의__ 경우 인스턴스를 삭제할 때까지 리소스가 유지 됩니다 (중지 해도 리소스가 제거 되지 않음).

    이러한 리소스는 구독의 [리소스 할당량](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits)으로 제한됩니다.

__Configuration__

* Machine Learning 컴퓨팅 클러스터를 만들려면 다음 단계를 사용하세요.

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

* Azure 계산 인스턴스에서 노트북을 사용 하는 경우 노트북이 동일한 가상 네트워크 및 서브넷 뒤에 있는 계산 리소스에서 실행 되 고 있는지 확인 해야 합니다. 

    **고급 설정**  >  **가상 네트워크 구성**에서 계산 인스턴스를 생성 하는 동안 동일한 가상 네트워크에 있도록 구성 합니다. 기존 계산 인스턴스를 가상 네트워크에 추가할 수 없습니다.

* 공용 인터넷으로/에서 네트워크 트래픽을 제한 하 여 가상 네트워크를 보호 하려는 경우 Azure Batch 서비스에서 인바운드 통신을 허용 해야 합니다.

    Batch 서비스는 VM에 연결된 NIC(네트워크 인터페이스) 수준에서 NSG(네트워크 보안 그룹)를 추가합니다. 이러한 NSG는 다음 트래픽을 허용하도록 인바운드 및 아웃바운드 규칙을 자동으로 구성합니다.

    - __BatchNodeManagement__의 __서비스 태그__ 포트 29876 및 29877의 인바운드 TCP 트래픽

    - (선택 사항) 원격 액세스를 허용하는 포트 22의 인바운드 TCP 트래픽 이 포트는 공용 IP에서 SSH를 사용하여 연결하려는 경우에만 사용합니다.

    - 가상 네트워크에 대한 모든 포트의 아웃바운드 트래픽

    - 인터넷에 대한 모든 포트의 아웃바운드 트래픽

    - __AzureMachineLearning__의 __서비스 태그__ 포트 44224의 컴퓨팅 인스턴스 인바운드 TCP 트래픽

    > [!IMPORTANT]
    > Batch 구성 NSG에서 인바운드 또는 아웃바운드 규칙을 수정하거나 추가할 경우 주의가 필요합니다. NSG가 컴퓨팅 노드에 대한 통신을 차단하는 경우 컴퓨팅 서비스는 컴퓨팅 노드의 상태를 사용 불가로 설정합니다.
    >
    > Azure Batch 서비스는 자체 NSG를 구성하기 때문에 서브넷 수준에서 NSG를 지정할 필요는 없습니다. 그러나 Azure Machine Learning 계산을 포함 하는 서브넷에 연결 된 NSGs 또는 방화벽이 있는 경우 앞에 나열 된 트래픽만 허용 해야 합니다.

* 기본 아웃바운드 규칙을 사용하지 않고 가상 네트워크의 아웃바운드 액세스를 제한하려면 다음 단계를 사용합니다.

    1. NSG 규칙을 사용하여 아웃바운드 인터넷 연결을 거부합니다.
    1. __컴퓨팅 인스턴스__ 또는 __컴퓨팅 클러스터__의 경우 다음 항목에 대한 아웃바운드 트래픽을 제한합니다.
        - Azure Storage, __Storage.RegionName__의 __서비스 태그__를 사용합니다. 여기서 `{RegionName}`은 Azure 지역의 이름입니다.
        - Azure Container Registry, __AzureContainerRegistry.RegionName__의 __서비스 태그__를 사용합니다. 여기서 `{RegionName}`은 Azure 지역의 이름입니다.
        - Azure Machine Learning, __AzureMachineLearning__의 __서비스 태그__를 사용합니다.
        - Azure Resource Manager, __AzureResourceManager__의 __서비스 태그__를 사용합니다.
        - Azure Active Directory, __AzureActiveDirectory__의 __서비스 태그__를 사용합니다.

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

* Azure Machine Learning compute를 사용 하 여 [강제 터널링](/azure/vpn-gateway/vpn-gateway-forced-tunneling-rm) 을 사용 하는 경우 계산 리소스가 포함 된 서브넷에서 공용 인터넷과의 통신을 허용 해야 합니다. 이 통신은 작업 예약 및 Azure Storage 액세스에 사용 됩니다.

    다음 두 가지 방법으로이 작업을 수행할 수 있습니다.

    * [VIRTUAL NETWORK NAT](../virtual-network/nat-overview.md)를 사용 합니다. NAT 게이트웨이는 가상 네트워크에 있는 하나 이상의 서브넷에 대해 아웃 바운드 인터넷 연결을 제공 합니다. 자세한 내용은 [NAT 게이트웨이 리소스를 사용 하 여 가상 네트워크 디자인](../virtual-network/nat-gateway-resource.md)을 참조 하세요.

    * 계산 리소스를 포함 하는 서브넷에 [UDRs (사용자 정의 경로)](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview) 를 추가 합니다. 리소스가 있는 지역에서 Azure Batch 서비스에 사용되는 각 IP 주소에 대한 UDR을 설정합니다. 이러한 UDR을 사용하면 Batch 서비스가 작업 예약을 위해 컴퓨팅 노드와 통신할 수 있습니다. 리소스가 있는 Azure Machine Learning Service의 IP 주소도 추가합니다. 컴퓨팅 인스턴스에 액세스하는 데 필요하기 때문입니다. Batch 서비스 및 Azure Machine Learning Service의 IP 주소 목록을 가져오려면 다음 방법 중 하나를 사용합니다.

        * [Azure IP 범위 및 서비스 태그](https://www.microsoft.com/download/details.aspx?id=56519)를 다운로드하고 파일에서 `BatchNodeManagement.<region>` 및 `AzureMachineLearning.<region>`을 검색합니다. 여기서 `<region>`은 Azure 지역입니다.

        * [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)를 사용하여 정보를 다운로드합니다. 다음 예는 IP 주소 정보를 다운로드하고 미국 동부 2 지역에 대한 정보를 필터링합니다.

            ```azurecli-interactive
            az network list-service-tags -l "East US 2" --query "values[?starts_with(id, 'Batch')] | [?properties.region=='eastus2']"
            az network list-service-tags -l "East US 2" --query "values[?starts_with(id, 'AzureMachineLearning')] | [?properties.region=='eastus2']"
            ```
        
        UDR을 추가할 때 관련된 각 Batch IP 주소 접두사에 대한 경로를 정의하고 __다음 홉 유형__을 __인터넷__으로 설정합니다. 

        사용자가 정의 하는 UDRs 외에도 온-프레미스 네트워크 어플라이언스를 통해 Azure Storage에 대 한 아웃 바운드 트래픽을 허용 해야 합니다. 특히이 트래픽의 Url은 `<account>.table.core.windows.net` , `<account>.queue.core.windows.net` 및 형식 `<account>.blob.core.windows.net` 입니다. 

        자세한 내용은 [가상 네트워크에서 Azure Batch 풀 만들기](../batch/batch-virtual-network.md#user-defined-routes-for-forced-tunneling)를 참조하세요.

<a id="aksvnet"></a>

## <a name="azure-kubernetes-service"></a>Azure Kubernetes Service

__요구 사항__

* Azure Kubernetes 서비스 (AKS) 인스턴스와 Azure virtual network는 동일한 지역에 있어야 합니다. 가상 네트워크의 작업 영역에서 사용 하는 Azure Storage 계정의 보안을 유지 하는 경우 AKS 인스턴스와 동일한 가상 네트워크에 있어야 합니다.

* 클러스터에 대 한 __IP 주소 지정을 계획__ 하려면 [Azure Kubernetes 서비스에서 고급 네트워킹 구성 (AKS)](https://docs.microsoft.com/azure/aks/configure-azure-cni#prerequisites) 문서의 필수 구성 요소를 따릅니다.

* AKS 클러스터에 대 한 인바운드 및 아웃 바운드 통신을 제한 하려면 [Azure Kubernetes Service에서 클러스터 노드에 대 한 송신 트래픽 제어](/azure/aks/limit-egress-traffic) 문서에 설명 된 지침에 따라 AKS의 _아웃 바운드_ 통신이 올바르게 구성 되어 있는지 확인 합니다. _인바운드_ 통신 요구 사항 (있는 경우)은 아래 구성 섹션에서 호출 됩니다.

__제한 사항__

* 개인 링크를 사용 하도록 설정 된 Azure Kubernetes 서비스를 사용 하려면 작업 영역에 연결 해야 합니다. Azure Machine Learning (SDK, portal, CLI 등)에서 개인 링크로 Azure Kubernetes 서비스 클러스터를 만들 수 없습니다.

__Configuration__

> [!IMPORTANT]
> 이 섹션에는 여러 구성이 포함 되어 있습니다. 요구 사항에 가장 적합 한 항목을 선택 합니다.

* __공용 부하 분산 장치를 사용 하 여 가상 네트워크 뒤에 AKS를 사용__하려면 다음을 수행 합니다.

    1. AKS 클러스터를 만들거나 연결 합니다. 새 클러스터를 __만드는__ 경우 클러스터에 사용할 가상 네트워크를 지정 해야 합니다.
    
        다음 예제에서는 Python SDK를 사용 하 여 새 AKS 클러스터를 만드는 방법을 보여 줍니다.

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

        이미 가상 네트워크에 있는 기존 AKS 클러스터가 있는 경우 [Azure Kubernetes에 배포](how-to-deploy-azure-kubernetes-service.md#attach-an-existing-aks-cluster) 문서의 정보를 사용 합니다.

    1. 가상 네트워크 외부에서 호출할 수 있도록 가상 네트워크를 제어 하는 네트워크 보안 그룹에 점수 매기기 끝점에 대 한 __인바운드__ 보안 규칙이 설정 되어 있는지 확인 합니다.

* __전용 부하 분산 장치를 사용 하 여 가상 네트워크 뒤에 AKS를 사용__하려면 다음을 수행 합니다.

    1. AKS 클러스터를 만들거나 연결 합니다. 새 클러스터를 __만드는__ 경우 클러스터에 사용할 가상 네트워크를 지정 해야 합니다.
    
        다음 예제에서는 Python SDK를 사용 하 여 새 AKS 클러스터를 만드는 방법을 보여 줍니다.

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

        이미 가상 네트워크에 있는 기존 AKS 클러스터가 있는 경우 [Azure Kubernetes에 배포](how-to-deploy-azure-kubernetes-service.md#attach-an-existing-aks-cluster) 문서의 정보를 사용 합니다.

    1. AKS에 대 한 서비스 주체 또는 관리 id ID를 찾으려면 다음 Azure CLI 명령을 사용 합니다. `<aks-cluster-name>`을 클러스터의 이름으로 바꿉니다. `<resource-group-name>`을 _AKS 클러스터를 포함_하는 리소스 그룹의 이름으로 바꿉니다.

        ```azurecli-interactive
        az aks show -n <aks-cluster-name> --resource-group <resource-group-name> --query servicePrincipalProfile.clientId
        ``` 

        이 명령이 값을 반환 하는 경우 `msi` 다음 명령을 사용 하 여 관리 되는 id의 보안 주체 ID를 식별 합니다.

        ```azurecli-interactive
        az aks show -n <aks-cluster-name> --resource-group <resource-group-name> --query identity.principalId
        ```
    1. 가상 네트워크를 포함 하는 리소스 그룹의 ID를 찾으려면 다음 명령을 사용 합니다. `<resource-group-name>`을 _가상 네트워크를 포함_하는 리소스 그룹의 이름으로 바꿉니다.

        ```azurecli-interactive
        az group show -n <resource-group-name> --query id
        ```

    1. 서비스 주체 또는 관리 되는 id를 네트워크 참가자로 추가 하려면 다음 명령을 사용 합니다. 를 `<SP-or-managed-identity>` 서비스 주체 또는 관리 id에 대해 반환 된 id로 바꿉니다. 을 `<resource-group-id>` 가상 네트워크를 포함 하는 리소스 그룹에 대해 반환 된 ID로 바꿉니다.

        ```azurecli-interactive
        az role assignment create --assignee <SP-or-managed-identity> --role 'Network Contributor' --scope <resource-group-id>
        ```

    1. __개인 부하 분산 장치__를 사용 하도록 AKS 클러스터를 업데이트 하려면 Python SDK를 사용 합니다. 다음 코드 조각에서는 작업 영역에 추가 되었거나 연결 된 기존 AKS 클러스터를 업데이트 하는 방법을 보여 줍니다.
    
        ```python
        import azureml.core
        from azureml.core.compute.aks import AksUpdateConfiguration
        from azureml.core.compute import AksCompute

        # ws = workspace object. Creation not shown in this snippet
        aks_target = AksCompute(ws,"myaks")

        # Change to the name of the subnet that contains AKS
        subnet_name = "default"
        # Update AKS configuration to use an internal load balancer
        update_config = AksUpdateConfiguration(None, "InternalLoadBalancer", subnet_name)
        aks_target.update(update_config)
        # Wait for the operation to complete
        aks_target.wait_for_completion(show_output = True)
        ```

* __개인 끝점을 사용 하 여 AKS을 연결__하려면:

    1. 다음 Azure CLI 명령을 사용 하 여 AKS 클러스터가 사용 하는 서브넷의 __서브넷 ID__ 를 가져옵니다. 예를 들어 가상 네트워크에 대 한 기본 서브넷은 다음과 같습니다.
    
        ```azurecli
        az network vnet show -g myresourcegroup -n myvnet --query subnets[].id
        ```
        
        이 명령은 가상 네트워크의 서브넷에 대 한 Id 배열을 반환 합니다. 다음 JSON은 하나의 서브넷만 있는 가상 네트워크의 예제입니다.

        ```json
        [
            "/subscriptions/GUID/resourceGroups/myresourcegroup/providers/Microsoft.Network/virtualNetworks/myvnet/subnets/default"
        ]
        ```

        여러 Id를 반환 하는 경우 사용 하려는 Id를 선택 합니다.

    1. AKS 클러스터를 개인 끝점으로 만들려면 [개인 Azure Kubernetes Service 클러스터 만들기](/azure/aks/private-clusters#advanced-networking) 문서의 __고급 네트워킹__ 섹션에 있는 정보를 사용 합니다. 클러스터를 만들 때 매개 변수를 사용 하 여 이전 명령의 서브넷 ID를 사용 `--vnet-subnet-id` 합니다.

    1. 클러스터를 연결 하려면 [Azure Kubernetes에 배포](how-to-deploy-azure-kubernetes-service.md#attach-an-existing-aks-cluster) 문서의 정보를 사용 합니다.

    > [!TIP]
    > 이 구성을 사용 하 고 AKS 클러스터에서 아웃 바운드 통신을 제한 하는 경우 포트 __1194__ 또는 __9000__에서 통신을 허용 하지 않아도 됩니다. 허용 해야 하는 다른 포트의 경우 [Azure Kubernetes Service에서 클러스터 노드에 대 한 송신 트래픽 제어](/azure/aks/limit-egress-traffic) 문서에서 지침을 참조 하세요.

## <a name="use-azure-container-instances-aci"></a>ACI(Azure Container Instances) 사용

__요구 사항__

* Azure Container Instances는 모델을 배포할 때 동적으로 생성됩니다. Azure Machine Learning이 가상 네트워크 내에 ACI를 만들 수 있도록 설정하려면 배포에 사용되는 서브넷에 __서브넷 위임__을 활성화해야 합니다.

__제한 사항__

* 가상 네트워크는 Azure Machine Learning 작업 영역과 동일한 리소스 그룹에 있어야 합니다.

* 작업 영역에 대 한 ACR (Azure Container Registry)은 가상 네트워크에도 있을 수 없습니다.

__Configuration__

가상 네트워크의 ACI를 작업 영역으로 사용하려면 다음 단계를 사용하세요.

1. 가상 네트워크에서 서브넷 위임을 사용하도록 설정하려면 [서브넷 위임 추가 또는 제거](../virtual-network/manage-subnet-delegation.md) 문서의 정보를 참조하세요. 가상 네트워크를 만들 때 위임을 사용하도록 설정하거나 기존 네트워크에 추가할 수 있습니다.

    > [!IMPORTANT]
    > 위임을 사용하도록 설정하는 경우 `Microsoft.ContainerInstance/containerGroups`을 __서비스에 서브넷 위임__ 값으로 사용합니다.

2. [AciWebservice.deploy_configuration()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aci.aciwebservice?view=azure-ml-py#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none--primary-key-none--secondary-key-none--collect-model-data-none--cmk-vault-base-url-none--cmk-key-name-none--cmk-key-version-none--vnet-name-none--subnet-name-none-)을 사용하여 모델을 배포하고 `vnet_name` 및 `subnet_name` 매개 변수를 사용합니다. 이 매개 변수를 위임을 사용하도록 설정한 가상 네트워크 이름 및 서브넷으로 설정합니다.

## <a name="azure-databricks"></a>Azure Databricks

__요구 사항__

* 가상 네트워크가 Azure Machine Learning 작업 영역과 동일한 구독 및 지역에 있어야 합니다.
* 작업 영역의 Azure Storage 계정도 가상 네트워크에서 보호되는 경우, Azure Databricks 클러스터와 동일한 가상 네트워크에 있어야 합니다.
* Azure Databricks에 사용되는 __databricks-private__ 및 __databricks-public__ 서브넷 외에 가상 네트워크에 대해 생성된 __기본__ 서브넷도 필요합니다.

__제한 사항__

__Configuration__

가상 네트워크에서 Azure Databricks를 사용하는 방법에 대한 자세한 내용은 [Azure Virtual Network에서 Azure Databricks 배포](https://docs.azuredatabricks.net/administration-guide/cloud-configurations/azure/vnet-inject.html)를 참조하세요.

<a id="vmorhdi"></a>

## <a name="virtual-machine-or-hdinsight-cluster"></a>가상 머신 또는 HDInsight 클러스터

__요구 사항__

* Azure Machine Learning은 Ubuntu를 실행하는 가상 머신만 지원합니다.
* 가상 컴퓨터 또는 HDInsight 클러스터에서 SSH 포트를 사용 하도록 설정 해야 합니다.

__제한 사항__

__Configuration__

1. Azure Portal 또는 Azure CLI를 사용하여 VM 또는 HDInsight 클러스터를 만들고 이 클러스터를 Azure 가상 네트워크에 배치합니다. 자세한 내용은 다음 문서를 참조하세요.

    * [Linux VM용 Azure 가상 네트워크 만들기 및 관리](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-virtual-network)

    * [Azure 가상 네트워크를 사용하여 HDInsight 확장](https://docs.microsoft.com/azure/hdinsight/hdinsight-extend-hadoop-virtual-network)

1. Linux 기반 Azure Virtual Machines에 대 한 NSG가 자동으로 만들어집니다. 이 NSG는 모든 원본에서 포트 22에 대 한 액세스를 허용 합니다. SSH 포트에 대 한 액세스를 제한 하려는 경우 Azure Machine Learning에서 액세스를 허용 해야 합니다. Azure ML에 대 한 액세스를 유지 하려면 __AzureMachineLearning__의 __원본 서비스 태그__ 를 사용 하 여 __원본 서비스__ 에 대 한 액세스를 허용 해야 합니다. 예를 들어 다음 Azure CLI 명령은 Azure Machine Learning의 액세스만 허용 하도록 SSH 규칙을 수정 합니다.

    ```azurecli
    # Get default SSH rule
    nsgrule=$(az network nsg rule list --resource-group myResourceGroup --nsg-name myNetworkSecurityGroup --query [0].name -o tsv)
    # Update network security group rule to limit SSH to source service.
    az network nsg rule update --resource-group myResourceGroup --nsg-name myNetworkSecurityGroupBackEnd \
    --name $nsgrule --protocol tcp --direction inbound --priority 100 \
    --source-address-prefix AzureMachineLearning --source-port-range '*' --destination-address-prefix '*' \
    --destination-port-range 22 --access allow
    ```

    자세한 내용은 Linux 가상 머신의 Azure Virtual Networks 문서에서 [네트워크 보안 그룹 만들기](/azure/virtual-machines/linux/tutorial-virtual-network#create-network-security-groups) 섹션을 참조 하세요.
    
    네트워크 보안 그룹에 대해 기본 아웃바운드 규칙을 유지합니다. 자세한 내용은 [보안 그룹](https://docs.microsoft.com/azure/virtual-network/security-overview#default-security-rules)의 기본 보안 규칙을 참조하세요.

1. VM 또는 HDInsight 클러스터를 Azure Machine Learning 작업 영역에 연결합니다. 자세한 내용은 [모델 학습의 컴퓨팅 대상 설정](how-to-set-up-training-targets.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

* [Azure 방화벽 뒤에 Azure Machine Learning 작업 영역을 사용](how-to-access-azureml-behind-firewall.md)합니다.
* [학습 환경 설정](how-to-set-up-training-targets.md)
* [프라이빗 엔드포인트 설정](how-to-configure-private-link.md)
* [모델 배포 위치](how-to-deploy-and-where.md)
* [TLS를 사용하여 Azure Machine Learning을 통해 웹 서비스 보호](how-to-secure-web-service.md)
