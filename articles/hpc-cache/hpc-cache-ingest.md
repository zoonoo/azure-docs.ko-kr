---
title: Azure HPC 캐시 클라우드 컨테이너로 데이터 이동
description: Azure HPC 캐시에서 사용할 Azure Blob 저장소를 채우는 방법
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: rohogue
ms.openlocfilehash: a5625341e3dd279d93a59c57cd3325245351723e
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/13/2020
ms.locfileid: "79271877"
---
# <a name="move-data-to-azure-blob-storage"></a>Azure Blob storage로 데이터 이동

워크플로에 데이터를 Azure Blob 저장소로 이동 하는 작업이 포함 된 경우 효율적인 전략을 사용 하 고 있는지 확인 합니다. 저장소 대상으로 정의 하기 전에 새 Blob 컨테이너의 데이터를 미리 로드 하거나, 컨테이너를 추가 하 고, Azure HPC 캐시를 사용 하 여 데이터를 복사할 수 있습니다.

이 문서에서는 Azure HPC 캐시에서 사용할 수 있도록 Blob 저장소로 데이터를 이동 하는 가장 좋은 방법을 설명 합니다.

이러한 사실을 염두에 두십시오.

* Azure HPC 캐시는 특수화 된 저장소 형식을 사용 하 여 Blob storage에서 데이터를 구성 합니다. 이것은 Blob storage 대상이 새 빈 컨테이너 이거나 이전에 Azure HPC 캐시 데이터에 사용 된 Blob 컨테이너 여야 하는 이유입니다. <!--([Avere vFXT for Azure](https://azure.microsoft.com/services/storage/avere-vfxt/) also uses this cloud file system.)-->

* 여러 클라이언트와 병렬 작업을 사용 하는 경우 Azure HPC 캐시를 통해 백 엔드 저장소 대상으로 데이터를 복사 하는 것이 더 효율적입니다. 한 클라이언트의 단순 복사 명령이 데이터를 느리게 이동 합니다.

Python 기반 유틸리티는 Blob 저장소 컨테이너에 콘텐츠를 로드 하는 데 사용할 수 있습니다. 자세한 내용은 [Blob 저장소에서 데이터 미리 로드](#pre-load-data-in-blob-storage-with-clfsload) 를 참조 하세요.

로드 유틸리티를 사용 하지 않으려는 경우 또는 기존 저장소 대상에 콘텐츠를 추가 하려는 경우 [AZURE HPC 캐시를 통해 데이터 복사](#copy-data-through-the-azure-hpc-cache)의 병렬 데이터 수집 팁을 따르세요.

## <a name="pre-load-data-in-blob-storage-with-clfsload"></a>CLFSLoad를 사용 하 여 Blob storage에 데이터 미리 로드

이 경우 <!--[Avere CLFSLoad](https://aka.ms/avere-clfsload)--> Avere CLFSLoad 유틸리티를 통해 데이터를 저장소 대상으로 추가 하기 전에 새 Blob 저장소 컨테이너로 데이터를 복사 합니다. 이 유틸리티는 단일 Linux 시스템에서 실행 되며 Azure HPC 캐시에 필요한 소유 형식으로 데이터를 기록 합니다. CLFSLoad는 캐시에 사용할 Blob 저장소 컨테이너를 채우는 가장 효율적인 방법입니다.

Avere CLFSLoad 유틸리티는 Azure HPC 캐시 팀의 요청에 의해 제공 됩니다. 팀에 문의 하거나 [지원 티켓](hpc-cache-support-ticket.md) 을 열어 지원을 요청 하세요.

이 옵션은 비어 있는 새 컨테이너에만 적용 됩니다. Avere CLFSLoad를 사용 하기 전에 컨테이너를 만듭니다.

자세한 정보는 Azure HPC 캐시 팀의 요청에서 사용할 수 있는 Avere CLFSLoad 배포에 포함 되어 있습니다. <!-- [Avere CLFSLoad readme](https://github.com/microsoft/Avere-CLFSLoad/blob/master/README.md). --><!-- caution literal link -->

프로세스의 일반적인 개요는 다음과 같습니다.

1. Python 버전 3.6 이상을 사용 하 여 Linux 시스템 (VM 또는 물리적)을 준비 합니다. 더 나은 성능을 위해 Python 3.7이 권장 됩니다.
1. Linux 시스템에 Avere-CLFSLoad 소프트웨어를 설치 합니다.
1. Linux 명령줄에서 전송을 실행 합니다.

Avere CLFSLoad 유틸리티에는 다음 정보가 필요 합니다.

* Blob 저장소 컨테이너를 포함 하는 저장소 계정 ID
* 빈 Blob storage 컨테이너의 이름입니다.
* 유틸리티가 컨테이너에 쓸 수 있도록 하는 SAS (공유 액세스 서명) 토큰입니다.
* 데이터 원본에 대 한 로컬 경로-복사할 데이터를 포함 하는 로컬 디렉터리 또는 데이터가 포함 된 탑재 된 원격 시스템의 로컬 경로입니다.

<!-- The requirements are explained in detail in the [Avere CLFSLoad readme](https://aka.ms/avere-clfsload). -->

## <a name="copy-data-through-the-azure-hpc-cache"></a>Azure HPC 캐시를 통해 데이터 복사

Avere CLFSLoad 유틸리티를 사용 하지 않으려는 경우 또는 기존 Blob 저장소 대상에 많은 양의 데이터를 추가 하려는 경우 캐시를 통해 복사할 수 있습니다. Azure HPC 캐시는 여러 클라이언트를 동시에 제공 하도록 설계 되었으므로 캐시를 통해 데이터를 복사 하려면 여러 클라이언트에서 병렬 쓰기를 사용 해야 합니다.

![다중 클라이언트, 다중 스레드 데이터 이동을 보여 주는 다이어그램 - 왼쪽 위의 온-프레미스 하드웨어 스토리지 아이콘에 여러 개의 화살표가 있습니다. 화살표는 네 개의 클라이언트 머신을 가리킵니다. 각 클라이언트 컴퓨터에서 세 개의 화살표가 Azure HPC 캐시를 가리킵니다. Azure HPC 캐시에서 여러 화살표가 Blob storage를 가리킵니다.](media/hpc-cache-parallel-ingest.png)

일반적으로 한 저장소 시스템에서 다른 저장소 시스템으로 데이터를 전송 하는 데 사용 하는 ``cp`` 또는 ``copy`` 명령은 한 번에 하나의 파일만 복사 하는 단일 스레드 프로세스입니다. 즉, 파일 서버는 한 번에 하나의 파일만 수집 캐시의 리소스가 낭비 됩니다.

이 섹션에서는 Azure HPC 캐시를 사용 하 여 Blob 저장소로 데이터를 이동 하는 다중 클라이언트 다중 스레드 파일 복사 시스템을 만들기 위한 전략을 설명 합니다. 여러 클라이언트와 단순 복사 명령을 사용하여 효율적인 데이터 복사에 사용할 수 있는 파일 전송 개념 및 결정 사항에 대해 설명합니다.

또한 도움이 되는 몇 가지 유틸리티도 설명합니다. ``msrsync`` 유틸리티는 데이터 세트를 버킷으로 분할하고 rsync 명령을 사용하는 프로세스를 부분적으로 자동화하는 데 사용할 수 있습니다. ``parallelcp`` 스크립트는 원본 디렉터리를 읽고 복사 명령을 자동으로 실행하는 또 다른 유틸리티입니다.

### <a name="strategic-planning"></a>전략적 계획

데이터를 병렬로 복사하는 전략을 수립할 때는 파일 크기, 파일 수 및 디렉터리 깊이의 장단점을 이해해야 합니다.

* 파일이 작은 경우 관심 있는 메트릭은 초당 파일 수입니다.
* 파일이 큰 경우(10MiBi 이상) 관심 있는 메트릭은 초당 바이트 수입니다.

각 복사 프로세스에는 복사 명령의 길이를 타이밍으로 측정하고 파일 크기와 파일 수를 팩터링하여 측정할 수 있는 처리량 속도와 파일 전송 속도가 있습니다. 속도를 측정하는 방법은 이 문서의 범위를 벗어나므로 설명할 수 없지만 작거나 큰 파일을 처리할지 여부를 이해하는 것이 중요합니다.

Azure HPC 캐시로 병렬 데이터 수집에 대 한 전략은 다음과 같습니다.

* 수동 복사-미리 정의 된 파일 또는 경로 집합에 대해 백그라운드에서 한 번에 둘 이상의 복사 명령을 실행 하 여 클라이언트에서 다중 스레드 복사를 수동으로 만들 수 있습니다. 자세한 내용은 [AZURE HPC 캐시 데이터 수집-수동 복사 방법](hpc-cache-ingest-manual.md) 을 참조 하세요.

* ``msrsync`` - ``msrsync``에서 부분적으로 자동화 된 복사는 여러 병렬 ``rsync`` 프로세스를 실행 하는 래퍼 유틸리티입니다. 자세한 내용은 [AZURE HPC 캐시 데이터 수집-msrsync 메서드](hpc-cache-ingest-msrsync.md)를 참조 하세요.

* ``parallelcp``를 사용 하 여 스크립팅된 복사- [AZURE HPC 캐시 데이터 수집-병렬 복사 스크립트 메서드에서](hpc-cache-ingest-parallelcp.md)병렬 복사 스크립트를 만들고 실행 하는 방법을 알아봅니다.

## <a name="next-steps"></a>다음 단계

저장소를 설정한 후 클라이언트에서 캐시를 탑재 하는 방법을 알아봅니다.

* [Azure HPC 캐시 시스템 액세스](hpc-cache-mount.md)
