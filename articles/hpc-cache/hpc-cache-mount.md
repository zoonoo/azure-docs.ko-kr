---
title: Azure HPC 캐시 마운트
description: Azure HPC 캐시 서비스에 클라이언트를 연결하는 방법
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 04/03/2020
ms.author: rohogue
ms.openlocfilehash: f176e30cfaf9a52e4f58091b7fc76098a4c88a48
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80657379"
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

캐시에 연결할 각 클라이언트에 로컬 디렉터리 경로를 만듭니다. 마운트할 각 저장소 대상에 대한 경로를 만듭니다.

예: `sudo mkdir -p /mnt/hpc-cache-1/target3`

## <a name="use-the-mount-instructions-utility"></a>마운트 지침 유틸리티 사용

Azure 포털의 캐시 보기의 **구성** 섹션에서 **마운트 지침** 페이지를 엽니다.

![포털에서 Azure HPC 캐시 인스턴스의 스크린샷, > 탑재 지침 구성 페이지 로드](media/mount-instructions.png)

Mount 명령 페이지에는 클라이언트 탑재 프로세스 및 필수 구성 사이트에 대한 정보와 복사 가능한 마운트 명령을 만드는 데 사용할 수 있는 필드가 포함됩니다.

이 페이지를 사용하려면 다음 절차를 따르십시오.

<!--1.  In step one of **Mounting your file system**, enter the path that the client will use to access the Azure HPC Cache storage target.

   * This path is local to the client.
   * After you provide the directory name, the field populates with a command you can copy. Use this command on the client directly or in a setup script to create the directory path on the client VM. -->

1. 클라이언트 전제 조건을 검토하고 클라이언트 `mount` [준비에서](#prepare-clients)설명한 대로 NFS 명령을 사용하는 데 필요한 유틸리티를 설치합니다.

1. **파일 시스템 장착** 의 1 단계<!-- label will change --> 클라이언트에서 로컬 경로를 만들기 위한 예제 명령을 제공합니다. 클라이언트가 Azure HPC 캐시에서 콘텐츠에 액세스하는 데 사용할 경로입니다.

   필요한 경우 명령에서 수정할 수 있도록 경로 이름을 기록합니다.

1. 2단계에서 사용 가능한 IP 주소 중 하나를 선택합니다. 캐시의 모든 [클라이언트 탑재 지점은](#find-mount-command-components) 여기에 나열되어 있습니다. 모든 IP 주소 간에 부하를 분산하는 시스템이 있는지 확인합니다.

1. 3단계필드는 프로토타입 마운트 명령으로 자동으로 채워집니다. 필드 오른쪽에 있는 복사 기호를 클릭하여 클립보드에 자동으로 복사합니다.

   > [!NOTE]
   > 복사 명령을 사용하기 전에 복사 명령을 확인합니다. 이 인터페이스에서는 아직 선택할 수 없는 클라이언트 마운트 경로와 저장소 대상 가상 네임스페이스 경로를 사용자 지정해야 할 수 있습니다. 또한 아래 권장 옵션을 반영하도록 마운트 명령 [옵션을](#mount-command-options) 업데이트해야 합니다. 도움말을 보려면 [마운트 명령 구문 이해하기를](#understand-mount-command-syntax) 읽어보십시오.

1. 클라이언트 컴퓨터에서 복사된 마운트 명령(필요한 경우 편집 시)을 사용하여 Azure HPC 캐시의 저장소 대상에 연결합니다. 클라이언트 명령줄에서 직접 명령을 내하거나 클라이언트 설정 스크립트 또는 템플릿에 마운트 명령을 포함할 수 있습니다.

## <a name="understand-mount-command-syntax"></a>마운트 명령 구문 이해

마운트 명령에는 다음과 같은 형식이 있습니다.

> sudo 마운트 *cache_mount_address*:/*namespace_path* *local_path* {*옵션*}

예제:

```bash
root@test-client:/tmp# mkdir hpccache
root@test-client:/tmp# sudo mount 10.0.0.28:/blob-demo-0722 ./hpccache/ -o hard,proto=tcp,mountproto=tcp,retry=30
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

**마운트 명령** 페이지를 사용하지 않고 마운트 명령을 만들려면 캐시 **개요** 페이지에서 마운트 주소와 **저장소 대상** 페이지에서 가상 네임스페이스 경로를 찾을 수 있습니다.

![Azure HPC 캐시 인스턴스의 개요 페이지의 스크린샷, 오른쪽 하단에 있는 마운트 주소 목록 주위에 있는 강조 표시 상자](media/hpc-cache-mount-addresses.png)

> [!NOTE]
> 캐시 마운트 주소는 캐시의 서브넷 내부의 네트워크 인터페이스에 해당합니다. 리소스 그룹에서 이러한 NIC는 이름이 끝나는 `-cluster-nic-` 이름과 숫자로 나열됩니다. 이러한 인터페이스를 변경하거나 삭제하지 않으면 캐시를 사용할 수 없게 됩니다.

가상 네임스페이스 경로는 **저장소 대상** 페이지에 표시됩니다. 개별 저장소 대상 이름을 클릭하여 연결된 집계된 네임스페이스 경로를 포함하여 세부 정보를 확인합니다.

![테이블의 경로 열에 있는 항목 주위에 강조 표시 상자가 있는 캐시의 저장소 대상 패널의 스크린샷](media/hpc-cache-view-namespace-paths.png)

## <a name="next-steps"></a>다음 단계

* 데이터를 캐시의 저장소 대상으로 이동하려면 [새 Azure Blob 저장소 채우기](hpc-cache-ingest.md)를 읽습니다.
