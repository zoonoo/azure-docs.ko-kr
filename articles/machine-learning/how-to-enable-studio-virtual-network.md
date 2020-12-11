---
title: 가상 네트워크에서 Azure Machine Learning studio 사용
titleSuffix: Azure Machine Learning
description: Azure Machine Learning studio를 구성 하 여 가상 네트워크 내부에 저장 된 데이터에 액세스 하는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.reviewer: larryfr
ms.author: aashishb
author: aashishb
ms.date: 10/21/2020
ms.custom: contperf-fy20q4, tracking-python
ms.openlocfilehash: 8dc8446ecbc203622ce7c2163136c1c26aac1cc7
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/10/2020
ms.locfileid: "97032731"
---
# <a name="use-azure-machine-learning-studio-in-an-azure-virtual-network"></a>Azure 가상 네트워크에서 Azure Machine Learning studio 사용

이 문서에서는 가상 네트워크에서 Azure Machine Learning studio를 사용 하는 방법에 대해 알아봅니다. 스튜디오에는 AutoML, 디자이너 및 데이터 레이블 지정과 같은 기능이 포함 되어 있습니다. 가상 네트워크에서 이러한 기능을 사용 하려면이 문서의 단계를 수행 해야 합니다.

이 문서에서는 다음 방법을 설명합니다.

> [!div class="checklist"]
> - 가상 네트워크 내에 저장 된 데이터에 대 한 액세스를 스튜디오에 제공 합니다.
> - 가상 네트워크 내의 리소스에서 studio에 액세스 합니다.
> - 스튜디오에서 저장소 보안에 미치는 영향을 이해 합니다.

이 문서는 Azure Machine Learning 워크플로를 보호 하는 과정을 안내 하는 다섯 부분으로 구성 된 시리즈의 5 부입니다. 이전 파트를 읽고 가상 네트워크 환경을 설정 하는 것이 좋습니다.

이 시리즈의 다른 문서를 참조 하세요.

[1. VNet 개요](how-to-network-security-overview.md)  >  [2. 작업 영역 3을 보호](how-to-secure-workspace-vnet.md)합니다  >  [. 학습 환경 4를 안전 하 게 보호](how-to-secure-training-vnet.md)합니다  >  [. 추론 환경 5를 보호](how-to-secure-inferencing-vnet.md)합니다  >  **. 스튜디오 기능 사용**


> [!IMPORTANT]
> 작업 영역이 Azure Government 또는 Azure 중국 21Vianet과 같은 __소 버린 클라우드에__ 있는 경우 통합 된 노트북은 가상 네트워크에 있는 저장소 사용을 지원 _하지 않습니다_ . 대신 컴퓨팅 인스턴스에서 Jupyter Notebook을 사용할 수 있습니다. 자세한 내용은 [Compute Instance 노트북의 데이터 액세스](how-to-secure-training-vnet.md#access-data-in-a-compute-instance-notebook) 섹션을 참조 하세요.


## <a name="prerequisites"></a>필수 구성 요소

+ 일반적인 가상 네트워크 시나리오 및 아키텍처를 이해 하려면 [네트워크 보안 개요](how-to-network-security-overview.md) 를 참조 하세요.

+ 사용할 기존 가상 네트워크 및 서브넷

+ [개인 링크가 설정 된 기존 Azure Machine Learning 작업 영역](how-to-secure-workspace-vnet.md#secure-the-workspace-with-private-endpoint)입니다.

+ 기존 [Azure 저장소 계정에서 가상 네트워크를 추가 했습니다](how-to-secure-workspace-vnet.md#secure-azure-storage-accounts-with-service-endpoints).

## <a name="configure-data-access-in-the-studio"></a>Studio에서 데이터 액세스 구성

일부 스튜디오 기능은 가상 네트워크에서 기본적으로 사용 하지 않도록 설정 되어 있습니다. 이러한 기능을 다시 사용 하도록 설정 하려면 스튜디오에서 사용 하려는 저장소 계정에 대해 관리 되는 id를 사용 하도록 설정 해야 합니다. 

가상 네트워크에서 다음 작업은 기본적으로 사용 하지 않도록 설정 되어 있습니다.

* Studio에서 데이터를 미리 봅니다.
* 디자이너에서 데이터를 시각화 합니다.
* 디자이너 ([기본 저장소 계정](#enable-managed-identity-authentication-for-default-storage-accounts))에 모델을 배포 합니다.
* AutoML 실험 ([기본 저장소 계정](#enable-managed-identity-authentication-for-default-storage-accounts))을 제출 합니다.
* 레이블 지정 프로젝트를 시작 합니다.

스튜디오는 가상 네트워크의 다음 데이터 저장소 형식에서 데이터를 읽을 수 있도록 지원 합니다.

* Azure Blob
* Azure Data Lake Storage Gen1
* Azure Data Lake Storage Gen2
* Azure SQL Database

### <a name="configure-datastores-to-use-workspace-managed-identity"></a>작업 영역 관리 id를 사용 하도록 데이터 저장소 구성

[서비스 엔드포인트](how-to-secure-workspace-vnet.md#secure-azure-storage-accounts-with-service-endpoints) 나 [개인 끝점](how-to-secure-workspace-vnet.md#secure-azure-storage-accounts-with-private-endpoints)을 사용 하 여 가상 네트워크에 Azure storage 계정을 추가한 후 [관리 되는 id](../active-directory/managed-identities-azure-resources/overview.md) 인증을 사용 하도록 데이터 저장소를 구성 해야 합니다. 이렇게 하면 스튜디오에서 저장소 계정의 데이터에 액세스할 수 있습니다.

Azure Machine Learning는 데이터 저장소를 사용 [하 여 저장소](concept-data.md#datastores) 계정에 연결 합니다. 다음 단계를 사용 하 여 관리 id를 사용 하도록 데이터 저장소를 구성 합니다.

1. 스튜디오에서 __Datastores__ 를 선택 합니다.

1. 기존 데이터 저장소를 업데이트 하려면 데이터 저장소를 선택 하 고 __자격 증명 업데이트__ 를 선택 합니다.

    새 데이터 저장소를 만들려면 __+ 새 데이터 저장소__ 를 선택 합니다.

1. 데이터 저장소 설정에서 작업 영역 관리 id 사용에 대해 __예__ 를 선택 하  __고 데이터 미리 보기에는 Azure Machine Learning studio에서 프로 파일링__ 을 선택 합니다.

    ![관리 되는 작업 영역 id를 사용 하도록 설정 하는 방법을 보여 주는 스크린샷](./media/how-to-enable-studio-virtual-network/enable-managed-identity.png)

이러한 단계는 Azure RBAC를 사용 하 여 저장소 서비스에 작업 영역 관리 id를 __판독기__ 로 추가 합니다. __읽기 권한자__ 액세스를 통해 작업 영역에서 방화벽 설정을 검색 하 여 데이터가 가상 네트워크를 떠나지 않도록 할 수 있습니다. 변경 내용을 적용 하는 데 최대 10 분이 걸릴 수 있습니다.

### <a name="enable-managed-identity-authentication-for-default-storage-accounts"></a>기본 저장소 계정에 관리 되는 id 인증 사용

각 Azure Machine Learning 작업 영역에는 작업 영역을 만들 때 정의 되는 두 개의 기본 저장소 계정과 함께 제공 됩니다. 스튜디오는 기본 저장소 계정을 사용 하 여 실험 및 모델 아티팩트를 저장 하며,이는 스튜디오의 특정 기능에 중요 합니다.

다음 표에서는 작업 영역 기본 저장소 계정에 대해 관리 id 인증을 사용 하도록 설정 해야 하는 이유에 대해 설명 합니다.

|스토리지 계정  | 참고  |
|---------|---------|
|작업 영역 기본 blob storage| 디자이너에서 모델 자산을 저장 합니다. 디자이너에서 모델을 배포 하려면이 저장소 계정에서 관리 되는 id 인증을 사용 하도록 설정 해야 합니다. <br> <br> 관리 id를 사용 하도록 구성 된 기본이 아닌 데이터 저장소를 사용 하는 경우 디자이너 파이프라인을 시각화 하 고 실행할 수 있습니다. 그러나 기본 데이터 저장소에서 관리 id를 사용 하도록 설정 하지 않고 학습 된 모델을 배포 하려고 하면 사용 중인 다른 데이터 저장소에 관계 없이 배포가 실패 합니다.|
|작업 영역 기본 파일 저장소| AutoML 실험 자산을 저장 합니다. AutoML 실험을 제출 하려면이 저장소 계정에서 관리 되는 id 인증을 사용 하도록 설정 해야 합니다. |


![기본 데이터 저장소를 찾을 수 있는 위치를 보여 주는 스크린샷](./media/how-to-enable-studio-virtual-network/default-datastores.png)


### <a name="grant-workspace-managed-identity-__reader__-access-to-storage-private-link"></a>저장소 개인 링크에 대 한 작업 영역 관리 id __판독기__ 액세스 권한 부여

Azure 저장소 계정에서 개인 끝점을 사용 하는 경우 개인 링크에 대 한 작업 영역 관리 id **판독기** 액세스 권한을 부여 해야 합니다. 자세한 내용은 [판독기](../role-based-access-control/built-in-roles.md#reader) 기본 제공 역할을 참조 하세요. 

저장소 계정에서 서비스 끝점을 사용 하는 경우이 단계를 건너뛸 수 있습니다.

## <a name="access-the-studio-from-a-resource-inside-the-vnet"></a>VNet 내의 리소스에서 studio에 액세스 합니다.

가상 네트워크 내의 리소스 (예: 계산 인스턴스 또는 가상 머신)에서 studio에 액세스 하는 경우 가상 네트워크에서 스튜디오로의 아웃 바운드 트래픽을 허용 해야 합니다. 

예를 들어 NSG (네트워크 보안 그룹)를 사용 하 여 아웃 바운드 트래픽을 제한 하는 경우 __AzureFrontDoor__ 의 __서비스 태그__ 대상에 규칙을 추가 합니다.

## <a name="technical-notes-for-managed-identity"></a>관리 id에 대 한 기술 정보

관리 id를 사용 하 여 저장소 서비스에 액세스 하면 보안 고려 사항에 영향을 줍니다. 이 섹션에서는 각 저장소 계정 유형에 대 한 변경 내용을 설명 합니다. 

이러한 고려 사항은 액세스 하는 __저장소 계정 유형에 대해__ 고유 합니다.

### <a name="azure-blob-storage"></a>Azure Blob Storage

__Azure blob storage__ 의 경우 blob storage에서 데이터를 읽을 수 있도록 작업 영역 관리 Id도 [blob 데이터 판독기](../role-based-access-control/built-in-roles.md#storage-blob-data-reader) 로 추가 됩니다.

### <a name="azure-data-lake-storage-gen2-access-control"></a>Azure Data Lake Storage Gen2 access control

Azure RBAC 및 POSIX 스타일의 Acl (액세스 제어 목록)을 모두 사용 하 여 가상 네트워크 내에서 데이터 액세스를 제어할 수 있습니다.

Azure RBAC를 사용 하려면 [Blob 데이터 판독기](../role-based-access-control/built-in-roles.md#storage-blob-data-reader) 역할에 작업 영역 관리 id를 추가 합니다. 자세한 내용은 [Azure 역할 기반 액세스 제어](../storage/blobs/data-lake-storage-access-control-model.md#role-based-access-control)를 참조 하세요.

Acl을 사용 하려면 다른 보안 원칙과 마찬가지로 작업 영역 관리 id에 액세스 권한을 할당할 수 있습니다. 자세한 내용은 [파일 및 디렉터리에 대 한 액세스 제어 목록](../storage/blobs/data-lake-storage-access-control.md#access-control-lists-on-files-and-directories)을 참조 하세요.

### <a name="azure-data-lake-storage-gen1-access-control"></a>Azure Data Lake Storage Gen1 access control

Azure Data Lake Storage Gen1는 POSIX 스타일의 액세스 제어 목록만 지원 합니다. 다른 보안 주체와 마찬가지로 리소스에 작업 영역 관리 id 액세스 권한을 할당할 수 있습니다. 자세한 내용은 [Azure Data Lake Storage Gen1의 Access control](../data-lake-store/data-lake-store-access-control.md)을 참조 하세요.

### <a name="azure-sql-database-contained-user"></a>포함 된 사용자 Azure SQL Database

관리 id를 사용 하 여 Azure SQL Database에 저장 된 데이터에 액세스 하려면 관리 되는 id에 매핑되는 SQL 포함 된 사용자를 만들어야 합니다. 외부 공급자에서 사용자를 만드는 방법에 대 한 자세한 내용은 [AZURE AD id에 매핑된 포함 된 사용자 만들기](../azure-sql/database/authentication-aad-configure.md#create-contained-users-mapped-to-azure-ad-identities)를 참조 하세요.

SQL 포함 된 사용자를 만든 후에는 [Grant t-sql 명령을](/sql/t-sql/statements/grant-object-permissions-transact-sql)사용 하 여 해당 사용자에 게 사용 권한을 부여 합니다.

### <a name="azure-machine-learning-designer-intermediate-module-output"></a>Azure Machine Learning 디자이너 중간 모듈 출력

디자이너에서 모듈의 출력 위치를 지정할 수 있습니다. 이를 사용 하 여 보안, 로깅 또는 감사를 위해 중간 데이터 집합을 별도의 위치에 저장 합니다. 출력을 지정 하려면:

1. 출력을 지정 하려는 모듈을 선택 합니다.
1. 오른쪽에 표시 되는 모듈 설정 창에서 **출력 설정** 을 선택 합니다.
1. 각 모듈 출력에 사용 하려는 데이터 저장소를 지정 합니다.
 
가상 네트워크의 중간 저장소 계정에 대 한 액세스 권한이 있는지 확인 합니다. 그렇지 않으면 파이프라인이 실패 합니다.

또한 출력 데이터를 시각화 하기 위해 중간 저장소 계정에 [관리 되는 id 인증을 사용 하도록 설정](#configure-datastores-to-use-workspace-managed-identity) 해야 합니다.

## <a name="next-steps"></a>다음 단계

이 문서는 네 부분으로 구성 된 가상 네트워크 시리즈의 선택적 부분입니다. 가상 네트워크를 보호 하는 방법을 알아보려면 나머지 문서를 참조 하세요.

* [1 부: 가상 네트워크 개요](how-to-network-security-overview.md)
* [2 부: 작업 영역 리소스 보안](how-to-secure-workspace-vnet.md)
* [3 부: 학습 환경 보안](how-to-secure-training-vnet.md)
* [4 부: 추론 환경 보안](how-to-secure-inferencing-vnet.md)