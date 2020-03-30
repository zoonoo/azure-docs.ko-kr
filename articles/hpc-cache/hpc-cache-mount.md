---
title: Azure HPC 캐시 마운트
description: Azure HPC 캐시 서비스에 클라이언트를 연결하는 방법
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: rohogue
ms.openlocfilehash: d906ed9a1a55e936c6374806a9037085c47e3b01
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73582222"
---
# <a name="mount-the-azure-hpc-cache"></a>Azure HPC Cache 마운트

캐시를 만든 후 NFS 클라이언트는 간단한 마운트 명령으로 액세스할 수 있습니다.

마운트 명령은 다음 두 요소로 구성됩니다.

* 캐시의 마운트 주소 중 하나(캐시 개요 페이지에 나열됨)
* 저장소 대상을 만들 때 설정한 가상 네임스페이스 경로

![Azure HPC 캐시 인스턴스의 개요 페이지의 스크린샷, 오른쪽 하단에 있는 마운트 주소 목록 주위에 있는 강조 표시 상자](media/hpc-cache-mount-addresses.png)

> [!NOTE] 
> 캐시 마운트 주소는 캐시의 서브넷 내부의 네트워크 인터페이스에 해당합니다. 리소스 그룹에서 이러한 NIC는 이름이 끝나는 `-cluster-nic-` 이름과 숫자로 나열됩니다. 이러한 인터페이스를 변경하거나 삭제하지 않으면 캐시를 사용할 수 없게 됩니다.

가상 네임스페이스 경로는 **저장소 대상** 페이지에 표시됩니다. 개별 저장소 대상 이름을 클릭하여 연결된 집계된 네임스페이스 경로를 포함하여 세부 정보를 확인합니다.

![테이블의 경로 열에 있는 항목 주위에 강조 표시 상자가 있는 캐시의 저장소 대상 패널의 스크린샷](media/hpc-cache-view-namespace-paths.png)

## <a name="mount-command-syntax"></a>명령 구문 마운트

다음과 같은 마운트 명령을 사용합니다.

> sudo 마운트 *cache_mount_address*:/*namespace_path* *local_path* {*옵션*}

예제:

```
root@test-client:/tmp# mkdir hpccache
root@test-client:/tmp# sudo mount 10.0.0.28:/blob-demo-0722 ./hpccache/ -orw,tcp,mountproto=tcp,vers3,hard
root@test-client:/tmp# 
```

이 명령이 성공하면 저장소 내보내기의 내용은 클라이언트의 ``hpccache`` 디렉터리에 표시되어야 합니다.

> [!NOTE] 
> 클라이언트는 캐시가 있는 가상 네트워크 및 서브넷에 액세스할 수 있어야 합니다. 예를 들어 동일한 가상 네트워크 내에서 클라이언트 VM을 만들거나 가상 네트워크의 엔드포인트, 게이트웨이 또는 기타 솔루션을 사용하여 외부에서 액세스할 수 있습니다. 캐시의 서브넷 내에서 다른 것은 호스팅할 수 없습니다.

### <a name="mount-command-options"></a>명령 옵션 마운트

강력한 클라이언트 마운트의 경우 마운트 명령에서 다음 설정 및 인수를 전달합니다. 

``mount -o hard,proto=tcp,mountproto=tcp,retry=30 ${CACHE_IP_ADDRESS}:/${NAMESPACE_PATH} ${LOCAL_FILESYSTEM_MOUNT_POINT}``

| 권장 마운트 명령 설정 | |
--- | --- 
``hard`` | Azure HPC 캐시에 대한 소프트 마운트는 응용 프로그램 오류 및 가능한 데이터 손실과 관련이 있습니다. 
``proto=netid`` | NFS 네트워크 오류를 적절하게 처리하도록 지원합니다.
``mountproto=netid`` | 탑재 작업에 대한 네트워크 오류를 적절하게 처리하도록 지원합니다.
``retry=n`` | 일시적인 탑재 오류를 방지하려면 ``retry=30``을 설정합니다. (포그라운드 탑재에는 다른 값이 권장됩니다.)

## <a name="next-steps"></a>다음 단계

* 데이터를 캐시의 저장소 대상으로 이동하려면 [새 Azure Blob 저장소 채우기](hpc-cache-ingest.md)를 읽습니다.
