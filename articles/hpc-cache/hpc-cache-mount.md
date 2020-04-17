---
title: Azure HPC 캐시 마운트
description: Azure HPC 캐시 서비스에 클라이언트를 연결하는 방법
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: v-erkel
ms.openlocfilehash: a44232f06b455e20530271723e816c2117b339a0
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81458387"
---
# <a name="mount-the-azure-hpc-cache"></a>Azure HPC Cache 마운트

캐시를 만든 후 NFS 클라이언트는 간단한 `mount` 명령으로 액세스할 수 있습니다. 명령은 Azure HPC 캐시의 특정 저장소 대상 경로를 클라이언트 컴퓨터의 로컬 디렉터리에 연결합니다.

마운트 명령은 다음 요소로 구성됩니다.

* 캐시의 마운트 주소 중 하나(캐시 개요 페이지에 나열됨)
* 저장소 대상을 만들 때 설정한 가상 네임스페이스 경로
* 클라이언트에서 사용할 로컬 경로
* 이러한 종류의 NFS 마운트의 성공을 최적화하는 명령 매개 변수

캐시에 대한 **마운트 지침** 페이지에서는 정보와 권장 옵션을 수집하고 복사할 수 있는 프로토타입 탑재 명령을 만듭니다. 자세한 내용은 [마운트 지침 유틸리티를 사용하십시오.](#use-the-mount-instructions-utility)

## <a name="prepare-clients"></a>클라이언트 준비

이 섹션의 지침에 따라 클라이언트가 Azure HPC 캐시를 탑재할 수 있는지 확인합니다.

### <a name="provide-network-access"></a>네트워크 액세스 제공

클라이언트 컴퓨터는 캐시의 가상 네트워크 및 개인 서브넷에 대한 네트워크 액세스 권한이 있어야 합니다.

예를 들어 동일한 가상 네트워크 내에서 클라이언트 VM을 만들거나 가상 네트워크의 엔드포인트, 게이트웨이 또는 기타 솔루션을 사용하여 외부에서 액세스할 수 있습니다. 캐시 자체 이외의 것은 캐시의 서브넷 내에서 호스팅되지 않아야 합니다.

### <a name="install-utilities"></a>유틸리티 설치

NFS 탑재 명령을 지원하기 위해 적절한 Linux 유틸리티 소프트웨어를 설치합니다.

* 레드 햇 엔터프라이즈 리눅스 또는 SuSE에 대 한:`sudo yum install -y nfs-utils`
* 우분투 또는 데비안의 경우:`sudo apt-get install nfs-common`

### <a name="create-a-local-path"></a>로컬 경로 만들기

캐시에 연결할 각 클라이언트에 로컬 디렉터리 경로를 만듭니다. 마운트할 각 네임스페이스 경로에 대한 경로를 만듭니다.

예: `sudo mkdir -p /mnt/hpc-cache-1/target3`

Azure 포털의 [마운트 명령](#use-the-mount-instructions-utility) 페이지에는 복사할 수 있는 프로토타입 명령이 포함되어 있습니다.

클라이언트 컴퓨터를 캐시에 연결하면 이 경로를 저장소 대상 내보내기를 나타내는 가상 네임스페이스 경로와 연결합니다. 클라이언트가 사용할 각 가상 네임스페이스 경로에 대한 디렉터리를 만듭니다.

## <a name="use-the-mount-instructions-utility"></a>마운트 지침 유틸리티 사용

Azure 포털의 **사용 설명서** 페이지를 사용하여 복사 가능한 마운트 명령을 만들 수 있습니다. 포털에서 캐시 보기의 **구성** 섹션에서 페이지를 엽니다.

클라이언트에서 명령을 사용하기 전에 클라이언트가 필수 구성 조건을 충족하고 클라이언트 [준비에](#prepare-clients)설명된 `mount` 대로 NFS 명령을 사용하는 데 필요한 소프트웨어가 있는지 확인합니다.

![포털에서 Azure HPC 캐시 인스턴스의 스크린샷, > 탑재 지침 구성 페이지 로드](media/mount-instructions.png)

이 절차를 수행하여 마운트 명령을 만듭니다.

1. **클라이언트 경로** 필드를 사용자 지정합니다. 이 필드에는 클라이언트에서 로컬 경로를 만드는 데 사용할 수 있는 예제 명령이 있습니다. 클라이언트는 이 디렉터리에서 로컬로 Azure HPC 캐시의 콘텐츠에 액세스합니다.

   필드를 클릭하고 명령을 편집하여 원하는 디렉터리 이름을 포함합니다. 이름은 다음 문자열의 끝에 나타납니다.`sudo mkdir -p`

   ![끝에 커서가 있는 클라이언트 경로 필드의 스크린샷](media/mount-edit-client.png)

   필드 편집을 완료하면 페이지 하단의 마운트 명령이 새 클라이언트 경로로 업데이트됩니다.

1. 목록에서 **캐시 탑재 주소를** 선택합니다. 이 메뉴에는 캐시의 모든 [클라이언트 탑재 지점이 나열됩니다.](#find-mount-command-components)

   사용 가능한 모든 마운트 주소에서 클라이언트 로드의 균형을 조정하여 캐시 성능을 향상시원하는 것입니다.

   ![선택기에서 선택할 수 있는 세 개의 IP 주소를 표시하는 캐시 마운트 주소 필드의 스크린샷](media/mount-select-ip.png)

1. 클라이언트에 사용할 **가상 네임스페이스 경로를** 선택합니다. 이러한 경로는 백 엔드 저장소 시스템의 내보내기에 연결됩니다.

   ![선택기가 열려 있는 네임스페이스 경로 필드의 스크린샷](media/mount-select-target.png)

   저장소 대상 포털 페이지에서 가상 네임스페이스 경로를 보고 변경할 수 있습니다. [저장소 대상 추가를](hpc-cache-add-storage.md) 읽고 방법을 확인합니다.

   Azure HPC 캐시의 집계된 네임스페이스 기능에 대해 자세히 알아보려면 [집계된 네임스페이스 계획을](hpc-cache-namespace.md)참조하십시오.

1. 3단계의 **Mount 명령** 필드는 이전 필드에서 설정한 마운트 주소, 가상 네임스페이스 경로 및 클라이언트 경로를 사용하는 사용자 지정된 마운트 명령으로 자동으로 채워집니다.

   필드 오른쪽에 있는 복사 기호를 클릭하여 클립보드에 자동으로 복사합니다.

   ![선택기가 열려 있는 네임스페이스 경로 필드의 스크린샷](media/mount-command-copy.png)

1. 클라이언트 컴퓨터에서 복사된 마운트 명령을 사용하여 Azure HPC 캐시에 연결합니다. 클라이언트 명령줄에서 직접 명령을 내하거나 클라이언트 설정 스크립트 또는 템플릿에 마운트 명령을 포함할 수 있습니다.

## <a name="understand-mount-command-syntax"></a>마운트 명령 구문 이해

마운트 명령에는 다음과 같은 형식이 있습니다.

> sudo 마운트 {*옵션*} *cache_mount_address*:/*namespace_path* *local_path*

예:

```bash
root@test-client:/tmp# mkdir hpccache
root@test-client:/tmp# sudo mount -o hard,proto=tcp,mountproto=tcp,retry=30 10.0.0.28:/blob-demo-0722 hpccache
root@test-client:/tmp#
```

이 명령이 성공하면 저장소 내보내기의 내용이 클라이언트의 ``hpccache`` 디렉터리에 표시됩니다.

### <a name="mount-command-options"></a>명령 옵션 마운트

강력한 클라이언트 마운트의 경우 마운트 명령에서 다음 설정 및 인수를 전달합니다.

> 마운트 -o 하드, 프로토 = tcp, mountproto = tcp, 재시도 = 30 ${CACHE_IP_ADDRESS}:/$NAMESPACE_PATH} ${LOCAL_FILESYSTEM_MOUNT_POINT}

| 권장 마운트 명령 설정 | |
--- | ---
``hard`` | Azure HPC 캐시에 대한 소프트 마운트는 응용 프로그램 오류 및 가능한 데이터 손실과 관련이 있습니다.
``proto=tcp`` | NFS 네트워크 오류를 적절하게 처리하도록 지원합니다.
``mountproto=tcp`` | 탑재 작업에 대한 네트워크 오류를 적절하게 처리하도록 지원합니다.
``retry=<value>`` | 일시적인 탑재 오류를 방지하려면 ``retry=30``을 설정합니다. (포그라운드 탑재에는 다른 값이 권장됩니다.)

### <a name="find-mount-command-components"></a>마운트 명령 구성요소 찾기

**마운트 명령** 페이지를 사용하지 않고 마운트 명령을 만들려면 캐시 **개요** 페이지에서 마운트 주소와 **Storage 대상** 페이지에서 가상 네임스페이스 경로를 찾을 수 있습니다.

![Azure HPC 캐시 인스턴스의 개요 페이지의 스크린샷, 오른쪽 하단에 있는 마운트 주소 목록 주위에 있는 강조 표시 상자](media/hpc-cache-mount-addresses.png)

> [!NOTE]
> 캐시 마운트 주소는 캐시의 서브넷 내부의 네트워크 인터페이스에 해당합니다. 리소스 그룹에서 이러한 NIC는 이름이 끝나는 `-cluster-nic-` 이름과 숫자로 나열됩니다. 이러한 인터페이스를 변경하거나 삭제하지 않으면 캐시를 사용할 수 없게 됩니다.

가상 네임스페이스 경로는 각 저장소 대상의 세부 정보 페이지에 표시됩니다. 개별 저장소 대상 이름을 클릭하여 연결된 집계된 네임스페이스 경로를 포함하여 세부 정보를 확인합니다.

![저장소 대상의 세부 정보 페이지의 스크린샷(헤더 "저장소 대상 업데이트"). 테이블의 가상 네임스페이스 경로 열에 항목 주위에 강조 표시 상자가 있습니다.](media/hpc-cache-view-namespace-paths.png)

## <a name="next-steps"></a>다음 단계

* 데이터를 캐시의 저장소 대상으로 이동하려면 [새 Azure Blob 저장소 채우기](hpc-cache-ingest.md)를 읽습니다.
