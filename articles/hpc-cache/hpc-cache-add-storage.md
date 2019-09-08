---
title: Azure HPC 캐시에 저장소 추가
description: Azure HPC 캐시가 장기 파일 저장소에 대해 온-프레미스 NFS 시스템 또는 Azure Blob 컨테이너를 사용할 수 있도록 저장소 대상을 정의 하는 방법
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: v-erkell
ms.openlocfilehash: 4554214b74b4d09fa40e355270208bebda4076b7
ms.sourcegitcommit: a4b5d31b113f520fcd43624dd57be677d10fc1c0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70775261"
---
# <a name="add-storage"></a>스토리지 추가

*저장소 대상은* Azure HPC 캐시 인스턴스를 통해 액세스 되는 파일에 대 한 백 엔드 저장소입니다. 온-프레미스 하드웨어 시스템과 같은 NFS 저장소를 추가 하거나 Azure Blob에 데이터를 저장할 수 있습니다.

한 캐시에 대해 최대 10 개의 서로 다른 저장소 대상을 정의할 수 있습니다. 캐시는 하나의 집계 된 네임 스페이스에 있는 모든 저장소 대상을 제공 합니다.

저장소 내보내기는 캐시의 가상 네트워크에서 액세스할 수 있어야 합니다. 온-프레미스 하드웨어 저장소의 경우 NFS 저장소 액세스의 호스트 이름을 확인할 수 있는 DNS 서버를 설정 해야 할 수 있습니다. 자세한 내용은 [DNS 액세스](hpc-cache-prereqs.md#dns-access)를 참조 하세요.

Azure HPC 캐시를 만드는 동안 또는 나중에 저장소 대상을 추가할 수 있습니다. 이 절차는 Azure Blob storage를 추가 하는지 아니면 NFS 내보내기를 추가 하는지에 따라 약간 다릅니다. 각각에 대 한 세부 정보는 아래와 같습니다.

## <a name="add-storage-targets-while-creating-the-cache"></a>캐시를 만드는 동안 저장소 대상을 추가 합니다.

캐시 생성 마법사의 **저장소 대상** 탭을 사용 하 여 캐시 인스턴스를 만들 때 저장소를 정의할 수 있습니다.

![저장소 대상 페이지의 스크린샷](media/create-targets.png)

저장소를 추가 하려면 **저장소 대상 추가** 링크를 클릭 합니다.

## <a name="add-storage-targets-from-the-cache"></a>캐시에서 저장소 대상 추가

Azure Portal에서 캐시 인스턴스를 열고 왼쪽 세로 막대에서 **저장소 대상** 을 클릭 합니다. 저장소 대상 페이지에는 기존의 모든 대상이 나열 되며 새 대상이 추가 될 수 있는 링크가 제공 됩니다.

## <a name="add-a-new-azure-blob-storage-target"></a>새 Azure Blob 저장소 대상 추가

새 Blob 저장소 대상에는 빈 Blob 컨테이너 또는 Azure HPC Cache cloud filesystem 형식의 데이터로 채워지는 컨테이너가 필요 합니다. [Azure blob storage로 데이터 이동](hpc-cache-ingest.md)에서 blob 컨테이너를 미리 로드 하는 방법에 대해 자세히 알아보세요.

Azure Blob 컨테이너를 정의 하려면이 정보를 입력 합니다.

![새 Azure Blob 저장소 대상에 대 한 정보로 채워진 저장소 대상 추가 페이지의 스크린샷](media/hpc-cache-add-blob.png)

* **저장소 대상 이름** -Azure HPC 캐시에서이 저장소 대상을 식별 하는 이름을 설정 합니다.
* **대상 유형** - **Blob**을 선택 합니다.
* **저장소 계정** -참조할 컨테이너가 있는 계정을 선택 합니다.

  [액세스 역할 추가](#add-the-access-control-roles-to-your-account)에 설명 된 대로 저장소 계정에 액세스 하려면 캐시 인스턴스에 권한을 부여 해야 합니다.
* **저장소 컨테이너** -이 대상에 대 한 Blob 컨테이너를 선택 합니다.

* **가상 네임 스페이스 경로** -이 저장소 대상에 대 한 클라이언트 연결 filepath를 설정 합니다. 가상 네임 스페이스 기능에 대해 자세히 알아보려면 [집계 된 네임 스페이스 구성](hpc-cache-namespace.md) 을 참조 하세요.

<!--  The namespace path value must end with a slash (``/``) and should not start with one.  -->

완료 되 면 **확인** 을 클릭 하 여 저장소 대상을 추가 합니다.

### <a name="add-the-access-control-roles-to-your-account"></a>계정에 액세스 제어 역할 추가

Azure HPC 캐시는 [RBAC (역할 기반 액세스 제어)](https://docs.microsoft.com/azure/role-based-access-control/index) 를 사용 하 여 캐시 응용 프로그램이 Azure Blob 저장소 대상에 대 한 저장소 계정에 액세스 하도록 권한을 부여 합니다.

저장소 계정 소유자는 "StorageCache 리소스 공급자" 사용자에 대 한 롤 [저장소 계정 참가자](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-account-contributor) 및 [저장소 Blob 데이터 참가자](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-contributor) 를 명시적으로 추가 해야 합니다.

이렇게 하려면 나중에 또는 Blob 저장소 대상을 추가 하는 페이지의 링크를 클릭 하면 됩니다.

RBAC 역할을 추가 하는 단계:

1. 저장소 계정에 대 한 **액세스 제어 (IAM)** 페이지를 엽니다. ( **저장소 대상 추가** 페이지의 링크를 선택 하면 선택한 계정에 대해이 페이지가 자동으로 열립니다.)

1. 페이지 맨 **+** 위에서를 클릭 하 고 **역할 할당 추가**를 선택 합니다.

1. 목록에서 "저장소 계정 참가자" 역할을 선택 합니다.

1. **액세스 할당** 필드에서 기본값 ("Azure AD 사용자, 그룹 또는 서비스 사용자")을 선택 된 채로 둡니다.  

1. **Select** 필드에서 "storagecache"를 검색 합니다.  이 문자열은 "HPC 캐시 리소스 공급자" 라는 보안 주체 하나에 일치 해야 합니다. 해당 보안 주체를 클릭 하 여 선택 합니다.

1. **저장** 단추를 클릭 하 여 저장소 계정에 역할 할당을 추가 합니다.

1. 이 프로세스를 반복 하 여 "Storage Blob Data 참여자" 역할을 할당 합니다.  

![역할 할당 GUI의 스크린샷](media/hpc-cache-add-role.png)

## <a name="add-a-new-nfs-storage-target"></a>새 NFS 저장소 대상 추가

NFS 저장소 대상에는 저장소 내보내기에 도달 하는 방법 및 해당 데이터를 효율적으로 캐시 하는 방법을 지정 하는 몇 가지 추가 필드가 있습니다. 또한 둘 이상의 내보내기를 사용할 수 있는 경우 하나의 NFS 호스트에서 여러 네임 스페이스 경로를 만들 수 있습니다.

![NFS 대상이 정의 된 저장소 대상 추가 페이지의 스크린샷](media/hpc-cache-add-nfs-target.png)

NFS 지원 저장소 대상에 대 한 다음 정보를 제공 합니다.

* **저장소 대상 이름** -Azure HPC 캐시에서이 저장소 대상을 식별 하는 이름을 설정 합니다.

* **대상 유형** - **NFS**를 선택 합니다.

* **호스트 이름** -NFS 저장소 시스템에 대 한 IP 주소 또는 정규화 된 도메인 이름을 입력 합니다. (이름을 확인할 수 있는 DNS 서버에 대 한 액세스 권한이 캐시에 있는 경우에만 도메인 이름을 사용 합니다.)

* **사용 모델** - [아래에서 사용 모델 선택](#choose-a-usage-model)에 설명 된 대로 워크플로를 기반으로 하는 데이터 캐싱 프로필 중 하나를 선택 합니다.

여러 네임 스페이스 경로를 만들어 동일한 NFS 저장소 시스템의 다른 내보내기를 나타낼 수 있지만 하나의 저장소 대상에서 모두 만들어야 합니다.

각 내보내기에 대해 다음 값을 입력 합니다.

* **가상 네임 스페이스 경로** -이 저장소 대상에 대 한 클라이언트 연결 filepath를 설정 합니다. 가상 네임 스페이스 기능에 대해 자세히 알아보려면 [집계 된 네임 스페이스 구성](hpc-cache-namespace.md) 을 참조 하세요.

<!--  The virtual path should start with a slash ``/``. -->

* **Nfs 내보내기 경로** -nfs 내보내기의 경로를 입력 합니다.

* **하위 디렉터리 경로** -내보내기의 특정 하위 디렉터리를 탑재 하려면 여기에 입력 합니다. 그렇지 않으면이 필드를 비워 둡니다. 

완료 되 면 **확인** 을 클릭 하 여 저장소 대상을 추가 합니다.

### <a name="choose-a-usage-model"></a>사용 모델 선택 
<!-- link in GUI to this heading -->

NFS 저장소 시스템을 가리키는 저장소 대상을 만들 때 해당 대상에 대 한 *사용 모델* 을 선택 해야 합니다. 이 모델은 데이터를 캐시 하는 방법을 결정 합니다.

* 매우 읽기-주로 캐시를 사용 하 여 데이터 읽기 액세스를 가속화 하는 경우이 옵션을 선택 합니다. 

* 읽기/쓰기-클라이언트가 캐시를 사용 하 여 읽기 및 쓰기를 사용 하는 경우이 옵션을 선택 합니다.

* 클라이언트에서 캐시를 무시 합니다. 클라이언트에서 먼저 캐시에 쓰지 않고 저장소 시스템에 직접 데이터를 쓰는 경우이 옵션을 선택 합니다.

## <a name="next-steps"></a>다음 단계

저장소 대상을 만든 후 다음 작업 중 하나를 고려 합니다.

* [Azure HPC 캐시 탑재](hpc-cache-mount.md)
* [Azure Blob storage로 데이터 이동](hpc-cache-ingest.md)