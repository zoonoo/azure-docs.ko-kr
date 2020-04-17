---
title: Azure HPC 캐시 클라우드 컨테이너로 데이터 이동
description: Azure HPC 캐시와 함께 사용할 Azure Blob 저장소를 채우는 방법
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: rohogue
ms.openlocfilehash: fd21a78d0271f91d334bba5aba748f3770ad38cf
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81537936"
---
# <a name="move-data-to-azure-blob-storage"></a>Azure Blob 저장소로 데이터 이동

워크플로에 Azure Blob 저장소로 데이터를 이동하는 것이 포함된 경우 효율적인 전략을 사용하고 있는지 확인합니다. 새 Blob 컨테이너에서 데이터를 저장소 대상으로 정의하기 전에 미리 로드하거나 컨테이너를 추가한 다음 Azure HPC 캐시를 사용하여 데이터를 복사할 수 있습니다.

이 문서에서는 Azure HPC 캐시와 함께 사용할 데이터를 Blob 저장소로 이동하는 가장 좋은 방법을 설명합니다.

다음 사항을 염두에 두십시오.

* Azure HPC 캐시는 특수 한 저장소 형식을 사용 하 여 Blob 저장소에서 데이터를 구성 합니다. Blob 저장소 대상이 새 빈 컨테이너이거나 이전에 Azure HPC 캐시 데이터에 사용되었던 Blob 컨테이너여야 하는 이유입니다.

* Azure HPC 캐시를 통해 데이터를 백 엔드 저장소 대상으로 복사하는 것은 여러 클라이언트 및 병렬 작업을 사용할 때 더 효율적입니다. 한 클라이언트의 간단한 복사 명령은 데이터를 느리게 이동합니다.

Python 기반 유틸리티를 사용하여 콘텐츠를 Blob 저장소 컨테이너에 로드할 수 있습니다. 자세한 내용은 [Blob 저장소의 사전 로드 데이터를](#pre-load-data-in-blob-storage-with-clfsload) 읽어보십시오.

로드 유틸리티를 사용하지 않으려는 경우 또는 기존 저장소 대상에 콘텐츠를 추가하려면 [Azure HPC 캐시를 통해 복사 데이터에서](#copy-data-through-the-azure-hpc-cache)병렬 데이터 수집 팁을 따릅니다.

## <a name="pre-load-data-in-blob-storage-with-clfsload"></a>CLFSLoad를 사용하여 Blob 저장소의 데이터 사전 로드

Avere CLFSLoad 유틸리티를 사용하여 데이터를 저장소 대상으로 추가하기 전에 새 Blob 저장소 컨테이너에 데이터를 복사할 수 있습니다. 이 유틸리티는 단일 Linux 시스템에서 실행되며 Azure HPC 캐시에 필요한 전용 형식으로 데이터를 씁니다. CLFSLoad는 캐시와 함께 사용할 Blob 저장소 컨테이너를 채우는 가장 효율적인 방법입니다.

Avere CLFSLoad 유틸리티는 Azure HPC 캐시 팀의 요청에 따라 사용할 수 있습니다. 팀 담당자에게 문의하거나 지원 [티켓을](hpc-cache-support-ticket.md) 열어 도움을 요청하십시오.

이 옵션은 비어 있는 새 컨테이너에서만 작동합니다. Avere CLFSLoad를 사용하기 전에 컨테이너를 만듭니다.

자세한 정보는 Azure HPC 캐시 팀의 요청에 따라 사용할 수 있는 Avere CLFSLoad 배포에 포함되어 있습니다.

프로세스의 일반적인 개요:

1. 파이썬 버전 3.6 이상으로 리눅스 시스템 (VM 또는 물리적)을 준비합니다. 더 나은 성능을 위해 파이썬 3.7을 권장합니다.
1. 리눅스 시스템에 Avere-CLFSLoad 소프트웨어를 설치합니다.
1. Linux 명령줄에서 전송을 실행합니다.

Avere CLFSLoad 유틸리티에는 다음 정보가 필요합니다.

* Blob 저장소 컨테이너가 포함된 저장소 계정 ID
* 빈 Blob 저장소 컨테이너의 이름
* 유틸리티가 컨테이너에 쓸 수 있도록 하는 SAS(공유 액세스 서명) 토큰
* 데이터 원본에 대한 로컬 경로 - 복사할 데이터가 포함된 로컬 디렉터리 또는 데이터가 있는 마운트된 원격 시스템에 대한 로컬 경로

## <a name="copy-data-through-the-azure-hpc-cache"></a>Azure HPC 캐시를 통해 데이터 복사

Avere CLFSLoad 유틸리티를 사용하지 않으려거나 기존 Blob 저장소 대상에 많은 양의 데이터를 추가하려는 경우 캐시를 통해 복사할 수 있습니다. Azure HPC 캐시는 여러 클라이언트를 동시에 제공하도록 설계되었기 때문에 캐시를 통해 데이터를 복사하려면 여러 클라이언트의 병렬 쓰기를 사용해야 합니다.

![다중 클라이언트, 다중 스레드 데이터 이동을 보여 주는 다이어그램 - 왼쪽 위의 온-프레미스 하드웨어 스토리지 아이콘에 여러 개의 화살표가 있습니다. 화살표는 네 개의 클라이언트 머신을 가리킵니다. 각 클라이언트 컴퓨터에서 세 개의 화살표가 Azure HPC 캐시를 가리킵니다. Azure HPC 캐시에서 여러 화살표가 Blob 저장소를 가리킵니다.](media/hpc-cache-parallel-ingest.png)

일반적으로 ``cp`` ``copy`` 한 저장소 시스템에서 다른 저장소 시스템으로 데이터를 전송하는 데 사용하는 명령은 한 번에 하나의 파일만 복사하는 단일 스레드 프로세스입니다. 즉, 파일 서버는 한 번에 하나의 파일만 을 섭취하는데, 이는 캐시의 리소스를 낭비하는 것입니다.

이 섹션에서는 Azure HPC 캐시를 사용하여 데이터를 Blob 저장소로 이동하기 위한 다중 클라이언트, 다중 스레드 파일 복사 시스템을 만드는 전략을 설명합니다. 여러 클라이언트와 단순 복사 명령을 사용하여 효율적인 데이터 복사에 사용할 수 있는 파일 전송 개념 및 결정 사항에 대해 설명합니다.

또한 도움이 되는 몇 가지 유틸리티도 설명합니다. ``msrsync`` 유틸리티는 데이터 세트를 버킷으로 분할하고 rsync 명령을 사용하는 프로세스를 부분적으로 자동화하는 데 사용할 수 있습니다. ``parallelcp`` 스크립트는 원본 디렉터리를 읽고 복사 명령을 자동으로 실행하는 또 다른 유틸리티입니다.

### <a name="strategic-planning"></a>전략적 계획

데이터를 병렬로 복사하는 전략을 수립할 때는 파일 크기, 파일 수 및 디렉터리 깊이의 장단점을 이해해야 합니다.

* 파일이 작은 경우 관심 있는 메트릭은 초당 파일 수입니다.
* 파일이 큰 경우(10MiBi 이상) 관심 있는 메트릭은 초당 바이트 수입니다.

각 복사 프로세스에는 복사 명령의 길이를 타이밍으로 측정하고 파일 크기와 파일 수를 팩터링하여 측정할 수 있는 처리량 속도와 파일 전송 속도가 있습니다. 속도를 측정하는 방법은 이 문서의 범위를 벗어나므로 설명할 수 없지만 작거나 큰 파일을 처리할지 여부를 이해하는 것이 중요합니다.

Azure HPC 캐시를 사용하여 병렬 데이터 수집을 위한 전략은 다음과 같습니다.

* 수동 복사 - 미리 정의된 파일 또는 경로 집합에 대해 백그라운드에서 한 번에 두 개 이상의 복사 명령을 실행하여 클라이언트에서 다중 스레드 복사본을 수동으로 만들 수 있습니다. 자세한 내용은 [Azure HPC 캐시 데이터 수집 - 수동 복사 방법을](hpc-cache-ingest-manual.md) 읽어보십시오.

* 부분적으로 자동화된 ``msrsync``  -  ``msrsync`` 복사는 여러 병렬 ``rsync`` 프로세스를 실행하는 래퍼 유틸리티입니다. 자세한 내용은 [Azure HPC 캐시 데이터 수집 - msrsync 메서드를](hpc-cache-ingest-msrsync.md)참조하십시오.

* 스크립팅된 ``parallelcp`` 복사 - [Azure HPC 캐시 데이터 인제스트 - 병렬 복사 스크립트 메서드에서](hpc-cache-ingest-parallelcp.md)병렬 복사 스크립트를 만들고 실행하는 방법에 대해 알아봅니다.

## <a name="next-steps"></a>다음 단계

저장소를 설정한 후 클라이언트가 캐시를 탑재하는 방법을 알아봅니다.

* [Azure HPC 캐시 시스템에 액세스](hpc-cache-mount.md)
