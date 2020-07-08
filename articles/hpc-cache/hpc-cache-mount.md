---
title: Azure HPC 캐시 탑재
description: Azure HPC 캐시 서비스에 클라이언트를 연결 하는 방법
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 04/15/2020
ms.author: v-erkel
ms.openlocfilehash: fb3e4fd4935afc4869e50ccbc35c53333d43b1df
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85515529"
---
# <a name="mount-the-azure-hpc-cache"></a>Azure HPC Cache 마운트

캐시를 만든 후에는 NFS 클라이언트에서 간단한 명령을 사용 하 여 해당 캐시에 액세스할 수 있습니다 `mount` . 명령은 Azure HPC 캐시의 특정 저장소 대상 경로를 클라이언트 컴퓨터의 로컬 디렉터리에 연결 합니다.

Mount 명령은 다음 요소로 구성 됩니다.

* 캐시의 탑재 주소 중 하나 (캐시 개요 페이지에 나열 됨)
* 저장소 대상을 만들 때 설정 하는 가상 네임 스페이스 경로
* 클라이언트에서 사용할 로컬 경로입니다.
* 이러한 종류의 NFS 탑재의 성공 여부를 최적화 하는 명령 매개 변수

캐시의 **탑재 명령** 페이지에서는 정보 및 권장 되는 옵션을 수집 하 고 복사할 수 있는 프로토타입 탑재 명령을 만듭니다. 자세한 내용은 [탑재 명령 유틸리티 사용](#use-the-mount-instructions-utility) 을 참조 하세요.

## <a name="prepare-clients"></a>클라이언트 준비

클라이언트는이 섹션의 지침에 따라 Azure HPC 캐시를 탑재할 수 있는지 확인 합니다.

### <a name="provide-network-access"></a>네트워크 액세스 제공

클라이언트 컴퓨터는 캐시의 가상 네트워크와 개인 서브넷에 대 한 네트워크 액세스 권한이 있어야 합니다.

예를 들어 동일한 가상 네트워크 내에서 클라이언트 Vm을 만들거나 외부에서 액세스 하기 위해 가상 네트워크에서 끝점, 게이트웨이 또는 다른 솔루션을 사용 합니다. 캐시 자체 외에 캐시의 서브넷 내에 호스트 되는 것은 아닙니다.

### <a name="install-utilities"></a>유틸리티 설치

NFS 탑재 명령을 지원 하기 위해 적절 한 Linux 유틸리티 소프트웨어를 설치 합니다.

* Red Hat Enterprise Linux 또는 SuSE의 경우:`sudo yum install -y nfs-utils`
* Ubuntu 또는 Debian의 경우:`sudo apt-get install nfs-common`

### <a name="create-a-local-path"></a>로컬 경로 만들기

각 클라이언트에서 캐시에 연결할 로컬 디렉터리 경로를 만듭니다. 탑재 하려는 각 네임 스페이스 경로에 대 한 경로를 만듭니다.

예: `sudo mkdir -p /mnt/hpc-cache-1/target3`

Azure Portal의 [탑재](#use-the-mount-instructions-utility) 명령 페이지에는 복사할 수 있는 프로토타입 명령이 포함 되어 있습니다.

클라이언트 컴퓨터를 캐시에 연결 하는 경우이 경로를 저장소 대상 내보내기를 나타내는 가상 네임 스페이스 경로와 연결 합니다. 클라이언트에서 사용 하는 각 가상 네임 스페이스 경로에 대 한 디렉터리를 만듭니다.

## <a name="use-the-mount-instructions-utility"></a>탑재 명령 유틸리티 사용

Azure Portal **탑재** 명령 페이지를 사용 하 여 복사할 수 탑재 명령을 만들 수 있습니다. 포털에 있는 캐시 보기의 **구성** 섹션에서 페이지를 엽니다.

클라이언트에서 명령을 사용 하기 전에 클라이언트가 필수 구성 요소를 충족 하는지, `mount` 위에서 설명한 대로 [클라이언트 준비](#prepare-clients)에서 NFS 명령을 사용 하는 데 필요한 소프트웨어가 있는지 확인 합니다.

![> 탑재 명령 구성 페이지가 로드 된 포털의 Azure HPC 캐시 인스턴스 스크린샷](media/mount-instructions.png)

Mount 명령을 만들려면 다음 절차를 따르세요.

1. **클라이언트 경로** 필드를 사용자 지정 합니다. 이 필드에는 클라이언트에서 로컬 경로를 만드는 데 사용할 수 있는 예제 명령이 제공 됩니다. 클라이언트는이 디렉터리에서 로컬로 Azure HPC 캐시의 콘텐츠에 액세스 합니다.

   필드를 클릭 하 고 원하는 디렉터리 이름을 포함 하도록 명령을 편집 합니다. 다음 뒤의 문자열 끝에 이름이 표시 됩니다.`sudo mkdir -p`

   ![커서가 끝에 배치 된 클라이언트 경로 필드의 스크린샷](media/mount-edit-client.png)

   필드 편집을 완료 하면 페이지 맨 아래에 있는 mount 명령이 새 클라이언트 경로를 사용 하 여 업데이트 됩니다.

1. 목록에서 **캐시 탑재 주소** 를 선택 합니다. 이 메뉴에는 캐시의 모든 [클라이언트 탑재 지점이](#find-mount-command-components)나열 됩니다.

   캐시 성능을 향상 시키기 위해 사용 가능한 모든 탑재 주소에서 클라이언트 부하를 분산 합니다.

   ![선택할 3 개의 IP 주소를 표시 하는 선택기를 포함 하는 캐시 탑재 주소 필드의 스크린샷](media/mount-select-ip.png)

1. 클라이언트에 사용할 **가상 네임 스페이스 경로** 를 선택 합니다. 이러한 경로는 백 엔드 저장소 시스템의 내보내기에 연결 됩니다.

   ![선택기를 연 네임 스페이스 경로 필드의 스크린샷](media/mount-select-target.png)

   저장소 대상 포털 페이지에서 가상 네임 스페이스 경로를 확인 하 고 변경할 수 있습니다. [저장소 대상 추가](hpc-cache-add-storage.md) 를 읽어 방법을 확인 합니다.

   Azure HPC 캐시의 집계 된 네임 스페이스 기능에 대해 자세히 알아보려면 [집계 된 네임 스페이스 계획](hpc-cache-namespace.md)을 참조 하세요.

1. 3 단계의 **탑재 명령** 필드는 이전 필드에서 설정한 탑재 주소, 가상 네임 스페이스 경로 및 클라이언트 경로를 사용 하는 사용자 지정 된 탑재 명령으로 자동으로 채워집니다.

   필드의 오른쪽에 있는 복사 기호를 클릭 하 여 클립보드에 자동으로 복사 합니다.

   ![선택기를 연 네임 스페이스 경로 필드의 스크린샷](media/mount-command-copy.png)

1. 클라이언트 컴퓨터에서 복사 된 탑재 명령을 사용 하 여 Azure HPC 캐시에 연결 합니다. 클라이언트 명령줄에서 직접 명령을 실행 하거나 클라이언트 설치 스크립트 또는 템플릿에 탑재 명령을 포함할 수 있습니다.

## <a name="understand-mount-command-syntax"></a>탑재 명령 구문 이해

Mount 명령의 형식은 다음과 같습니다.

> sudo 탑재 {*options*} *cache_mount_address*/*namespace_path* *local_path*

예:

```bash
root@test-client:/tmp# mkdir hpccache
root@test-client:/tmp# sudo mount -o hard,proto=tcp,mountproto=tcp,retry=30 10.0.0.28:/blob-demo-0722 hpccache
root@test-client:/tmp#
```

이 명령이 성공 하면 저장소 내보내기의 내용이 ``hpccache`` 클라이언트의 디렉터리에 표시 됩니다.

### <a name="mount-command-options"></a>탑재 명령 옵션

강력한 클라이언트 탑재의 경우 탑재 명령에 다음 설정 및 인수를 전달 합니다.

> mount-o 하드, proto = tcp, mountproto = tcp, retry = 30 $ {CACHE_IP_ADDRESS}:/$ {NAMESPACE_PATH} $ {LOCAL_FILESYSTEM_MOUNT_POINT}

| 권장 탑재 명령 설정 | |
--- | ---
``hard`` | Azure HPC 캐시에 소프트 탑재는 응용 프로그램 오류 및 가능한 데이터 손실과 관련이 있습니다.
``proto=tcp`` | NFS 네트워크 오류를 적절하게 처리하도록 지원합니다.
``mountproto=tcp`` | 탑재 작업에 대한 네트워크 오류를 적절하게 처리하도록 지원합니다.
``retry=<value>`` | 일시적인 탑재 오류를 방지하려면 ``retry=30``을 설정합니다. (포그라운드 탑재에는 다른 값이 권장됩니다.)

### <a name="find-mount-command-components"></a>탑재 명령 구성 요소 찾기

**탑재** 명령 페이지를 사용 하지 않고 탑재 명령을 만들려면 캐시 **개요** 페이지의 탑재 주소와 **저장소 대상** 페이지의 가상 네임 스페이스 경로를 찾을 수 있습니다.

![오른쪽 아래에 있는 탑재 주소 목록 주위에 강조 표시 상자가 있는 Azure HPC 캐시 인스턴스의 개요 페이지 스크린샷](media/hpc-cache-mount-addresses.png)

> [!NOTE]
> 캐시 탑재 주소는 캐시의 서브넷 내에 있는 네트워크 인터페이스에 해당 합니다. 리소스 그룹에서 이러한 Nic는로 끝나는 이름 및 숫자로 나열 됩니다 `-cluster-nic-` . 이러한 인터페이스를 변경 하거나 삭제 하지 마세요. 캐시를 사용할 수 없게 됩니다.

가상 네임 스페이스 경로는 각 저장소 대상의 세부 정보 페이지에 표시 됩니다. 개별 저장소 대상 이름을 클릭 하 여 관련 된 집계 네임 스페이스 경로를 포함 하 여 세부 정보를 확인 합니다.

![저장소 대상의 세부 정보 페이지에 대 한 스크린샷입니다 ("저장소 대상 업데이트" 헤더). 테이블의 가상 네임 스페이스 경로 열에 있는 항목 주위에 강조 표시 상자가 있습니다.](media/hpc-cache-view-namespace-paths.png)

## <a name="next-steps"></a>다음 단계

* 캐시의 저장소 대상으로 데이터를 이동 하려면 [새 Azure Blob Storage 채우기](hpc-cache-ingest.md)를 참조 하세요.
