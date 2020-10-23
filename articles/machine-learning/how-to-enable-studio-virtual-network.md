---
title: 가상 네트워크에서 Azure Machine Learning studio 사용
titleSuffix: Azure Machine Learning
description: Azure Machine Learning studio를 사용 하 여 가상 네트워크 내부에 저장 된 데이터에 액세스 합니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.reviewer: larryfr
ms.author: aashishb
author: aashishb
ms.date: 07/16/2020
ms.custom: contperfq4, tracking-python
ms.openlocfilehash: df45068ff14d8ac08a17719e4e0338308b504cac
ms.sourcegitcommit: 6906980890a8321dec78dd174e6a7eb5f5fcc029
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2020
ms.locfileid: "92426095"
---
# <a name="use-azure-machine-learning-studio-in-an-azure-virtual-network"></a>Azure 가상 네트워크에서 Azure Machine Learning studio 사용

이 문서에서는 가상 네트워크에서 Azure Machine Learning studio를 사용 하는 방법에 대해 알아봅니다. 다음 방법을 알아봅니다.

> [!div class="checklist"]
> - 가상 네트워크 내의 리소스에서 studio에 액세스 합니다.
> - 저장소 계정에 대 한 개인 끝점을 구성 합니다.
> - 가상 네트워크 내에 저장 된 데이터에 대 한 액세스를 스튜디오에 제공 합니다.
> - 스튜디오에서 저장소 보안에 미치는 영향을 이해 합니다.

이 문서는 Azure Machine Learning 워크플로를 보호 하는 과정을 안내 하는 다섯 부분으로 구성 된 시리즈의 5 부입니다. 먼저 전체 아키텍처를 이해 하려면 [1 부: VNet 개요](how-to-network-security-overview.md) 를 읽어 보는 것이 좋습니다. 

이 시리즈의 다른 문서를 참조 하세요.

[1. VNet 개요](how-to-network-security-overview.md)  >  [2. 작업 영역 3을 보호](how-to-secure-workspace-vnet.md)합니다  >  [. 학습 환경 4를 안전 하 게 보호](how-to-secure-training-vnet.md)합니다  >  [. 추론 환경 5를 보호](how-to-secure-inferencing-vnet.md)합니다  >  **. 스튜디오 기능 사용**


> [!IMPORTANT]
> 대부분의 스튜디오는 가상 네트워크에 저장 된 데이터와 함께 작동 하지만 통합 된 노트북은 __그렇지 않습니다__. 통합 된 노트북은 가상 네트워크에 있는 저장소 사용을 지원 하지 않습니다. 대신, 계산 인스턴스에서 Jupyter 노트북을 사용할 수 있습니다. 자세한 내용은 [Compute Instance 노트북의 데이터 액세스]() 섹션을 참조 하세요.


## <a name="prerequisites"></a>필수 구성 요소

+ [네트워크 보안 개요](how-to-network-security-overview.md) 를 읽고 일반적인 가상 네트워크 시나리오 및 전반적인 가상 네트워크 아키텍처를 이해 합니다.

+ 사용할 기존 가상 네트워크 및 서브넷

+ [개인 링크가 설정 된 기존 Azure Machine Learning 작업 영역](how-to-secure-workspace-vnet.md#secure-the-workspace-with-private-endpoint)입니다.

+ 기존 [Azure 저장소 계정에서 가상 네트워크를 추가 했습니다](how-to-secure-workspace-vnet.md#secure-azure-storage-accounts-with-service-endpoints).

## <a name="access-the-studio-from-a-resource-inside-the-vnet"></a>VNet 내의 리소스에서 studio에 액세스 합니다.

가상 네트워크 내의 리소스 (예: 계산 인스턴스 또는 가상 머신)에서 studio에 액세스 하는 경우 가상 네트워크에서 스튜디오로의 아웃 바운드 트래픽을 허용 해야 합니다. 

예를 들어 NSG (네트워크 보안 그룹)를 사용 하 여 아웃 바운드 트래픽을 제한 하는 경우 __AzureFrontDoor__의 __서비스 태그__ 대상에 규칙을 추가 합니다.

## <a name="access-data-using-the-studio"></a>Studio를 사용 하 여 데이터 액세스

[서비스 엔드포인트](how-to-secure-workspace-vnet.md#secure-azure-storage-accounts-with-service-endpoints) 나 [개인 끝점](how-to-secure-workspace-vnet.md#secure-azure-storage-accounts-with-private-endpoints)을 사용 하 여 가상 네트워크에 Azure storage 계정을 추가한 후에 [는 관리 id](../active-directory/managed-identities-azure-resources/overview.md) 를 사용 하 여 저장소 계정에 데이터에 대 한 액세스 권한을 부여 해야 합니다.

관리 되는 id를 사용 하지 않는 경우이 오류가 표시 되 고, `Error: Unable to profile this dataset. This might be because your data is stored behind a virtual network or your data does not support profile.` 다음과 같은 작업을 사용할 수 없게 됩니다.

* Studio에서 데이터를 미리 봅니다.
* 디자이너에서 데이터를 시각화 합니다.
* AutoML 실험을 제출 합니다.
* 레이블 지정 프로젝트를 시작 합니다.

> [!NOTE]
> [ML 지원 데이터 labelling](how-to-create-labeling-projects.md#use-ml-assisted-labeling) 는 가상 네트워크 뒤에 보안 되는 기본 저장소 계정을 지원 하지 않습니다. ML 지원 데이터 레이블 지정에는 기본이 아닌 스토리지 계정을 사용해야 합니다. 기본이 아닌 스토리지 계정은 가상 네트워크 뒤에서 보호할 수 있습니다. 

스튜디오는 가상 네트워크의 다음 데이터 저장소 형식에서 데이터를 읽을 수 있도록 지원 합니다.

* Azure Blob
* Azure Data Lake Storage Gen1
* Azure Data Lake Storage Gen2
* Azure SQL Database

### <a name="configure-datastores-to-use-managed-identity"></a>관리 id를 사용 하도록 데이터 저장소 구성

Azure Machine Learning는 데이터 저장소를 사용 [하 여 저장소](concept-data.md#datastores) 계정에 연결 합니다. 관리 id를 사용 하도록 데이터 저장소를 구성 하려면 다음 단계를 사용 합니다. 

1. 스튜디오에서 __Datastores__를 선택 합니다.

1. 새 데이터 저장소를 만들려면 __+ 새 데이터 저장소__를 선택 합니다.

    기존 데이터 저장소를 업데이트 하려면 데이터 저장소를 선택 하 고 __자격 증명 업데이트__를 선택 합니다.

1. 데이터 저장소 설정에서 __작업 영역 관리 id를 사용 하 여 Azure Machine Learning 서비스에서 저장소에 액세스 하도록 허용__에 대해 __예__ 를 선택 합니다.


이러한 단계는 azure 리소스 기반 액세스 제어 (Azure RBAC)를 사용 하 여 작업 영역 관리 id를 저장소 서비스에 __판독기__ 로 추가 합니다. __읽기 권한자__ 액세스를 통해 작업 영역에서 방화벽 설정을 검색 하 고 데이터가 가상 네트워크를 떠나지 않도록 할 수 있습니다.

> [!NOTE]
> 이러한 변경 내용을 적용 하는 데 최대 10 분이 걸릴 수 있습니다.

## <a name="technical-notes-for-managed-identity"></a>관리 id에 대 한 기술 정보

관리 id를 사용 하 여 저장소 서비스에 액세스 하면 몇 가지 보안 고려 사항에 영향을 줍니다. 이러한 고려 사항은 액세스 하는 저장소 계정 유형에 대해 고유 합니다. 이 섹션에서는 각 저장소 계정 유형에 대 한 변경 내용을 설명 합니다.

### <a name="azure-blob-storage"></a>Azure Blob Storage

__Azure blob storage__의 경우 blob storage에서 데이터를 읽을 수 있도록 작업 영역 관리 Id도 [blob 데이터 판독기](../role-based-access-control/built-in-roles.md#storage-blob-data-reader) 로 추가 됩니다.

### <a name="azure-data-lake-storage-gen2-access-control"></a>Azure Data Lake Storage Gen2 access control

Azure RBAC 및 POSIX 스타일의 Acl (액세스 제어 목록)을 모두 사용 하 여 가상 네트워크 내에서 데이터 액세스를 제어할 수 있습니다.

Azure RBAC를 사용 하려면 [Blob 데이터 판독기](../role-based-access-control/built-in-roles.md#storage-blob-data-reader) 역할에 작업 영역 관리 id를 추가 합니다. 자세한 내용은 [Azure 역할 기반 액세스 제어](../storage/blobs/data-lake-storage-access-control-model.md#role-based-access-control)를 참조 하세요.

Acl을 사용 하려면 다른 보안 원칙과 마찬가지로 작업 영역 관리 id에 액세스 권한을 할당할 수 있습니다. 자세한 내용은 [파일 및 디렉터리에 대 한 액세스 제어 목록](../storage/blobs/data-lake-storage-access-control.md#access-control-lists-on-files-and-directories)을 참조 하세요.

### <a name="azure-data-lake-storage-gen1-access-control"></a>Azure Data Lake Storage Gen1 access control

Azure Data Lake Storage Gen1는 POSIX 스타일의 액세스 제어 목록만 지원 합니다. 다른 보안 주체와 마찬가지로 리소스에 작업 영역 관리 id 액세스 권한을 할당할 수 있습니다. 자세한 내용은 [Azure Data Lake Storage Gen1의 Access control](../data-lake-store/data-lake-store-access-control.md)을 참조 하세요.

### <a name="azure-sql-database-contained-user"></a>포함 된 사용자 Azure SQL Database

관리 id를 사용 하 여 Azure SQL Database에 저장 된 데이터에 액세스 하려면 관리 되는 id에 매핑되는 SQL 포함 된 사용자를 만들어야 합니다. 외부 공급자에서 사용자를 만드는 방법에 대 한 자세한 내용은 [AZURE AD id에 매핑된 포함 된 사용자 만들기](../azure-sql/database/authentication-aad-configure.md#create-contained-users-mapped-to-azure-ad-identities)를 참조 하세요.

SQL 포함 된 사용자를 만든 후에는 [Grant t-sql 명령을](https://docs.microsoft.com/sql/t-sql/statements/grant-object-permissions-transact-sql)사용 하 여 해당 사용자에 게 사용 권한을 부여 합니다.

### <a name="azure-machine-learning-designer-default-datastore"></a>Azure Machine Learning designer 기본 데이터 저장소

디자이너는 작업 영역에 연결 된 저장소 계정을 사용 하 여 기본적으로 출력을 저장 합니다. 그러나 사용자가 액세스할 수 있는 모든 데이터 저장소에 출력을 저장 하도록 지정할 수 있습니다. 환경에서 가상 네트워크를 사용 하는 경우 이러한 컨트롤을 사용 하 여 데이터가 안전 하 게 유지 되 고 액세스 가능한 지 확인할 수 있습니다.

파이프라인에 대 한 새 기본 저장소를 설정 하려면 다음을 수행 합니다.

1. 파이프라인 초안에서 파이프라인 제목 근처의 **설정 기어 아이콘** 을 선택 합니다.
1. **기본 데이터 저장소 선택**을 선택 합니다.
1. 새 데이터 저장소를 지정 합니다.

모듈 별로 기본 데이터 저장소를 재정의할 수도 있습니다. 이를 통해 각 개별 모듈의 저장소 위치를 제어할 수 있습니다.

1. 출력을 지정 하려는 모듈을 선택 합니다.
1. **출력 설정** 섹션을 확장 합니다.
1. **기본 출력 설정 재정의**를 선택 합니다.
1. **출력 설정 설정**을 선택 합니다.
1. 새 데이터 저장소를 지정 합니다.

## <a name="next-steps"></a>다음 단계

이 문서는 네 부분으로 구성 된 가상 네트워크 시리즈의 선택적 부분입니다. 가상 네트워크를 보호 하는 방법을 알아보려면 나머지 문서를 참조 하세요.

* [1 부: 가상 네트워크 개요](how-to-network-security-overview.md)
* [2 부: 작업 영역 리소스 보안](how-to-secure-workspace-vnet.md)
* [3 부: 학습 환경 보안](how-to-secure-training-vnet.md)
* [4 부: 추론 환경 보안](how-to-secure-inferencing-vnet.md)