---
title: Azure HPC 캐시 데이터 수집 - msrsync
description: msrsync를 사용하여 Azure HPC 캐시에서 데이터를 Blob 저장소 대상으로 이동하는 방법
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: rohogue
ms.openlocfilehash: 4f8863d706d623d613ac156cf202c3b7b12f2ae0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74168433"
---
# <a name="azure-hpc-cache-data-ingest---msrsync-method"></a>Azure HPC 캐시 데이터 수집 - msrsync 방법

이 문서에서는 ``msrsync`` Azure HPC 캐시와 함께 사용할 Azure Blob 저장소 컨테이너에 데이터를 복사 하는 유틸리티를 사용 하 여에 대 한 자세한 지침을 제공 합니다.

Azure HPC 캐시의 Blob 저장소로 데이터를 이동하는 방법에 대해 자세히 알아보려면 [Azure Blob 저장소로 데이터 이동을](hpc-cache-ingest.md)읽어보십시오.

이 ``msrsync`` 도구를 사용하여 Azure HPC 캐시의 백 엔드 저장소 대상으로 데이터를 이동할 수 있습니다. 이 도구는 여러 개의 ``rsync`` 병렬 프로세스를 실행하여 대역폭 사용량을 최적화하도록 설계되었습니다. GitHub의 https://github.com/jbd/msrsync에서 사용할 수 있습니다.

``msrsync``는 원본 디렉터리를 별도의 "버킷"으로 분할한 다음, 각 버킷에서 ``rsync`` 프로세스를 개별적으로 실행합니다.

4개 코어 VM을 사용한 예비 테스트에서는 64개의 프로세스를 사용할 때 최고의 효율성을 보였습니다. ``msrsync`` 옵션인 ``-p``를 사용하여 프로세스 수를 64로 설정합니다.

``msrsync``는 로컬 볼륨 간에만 쓸 수 있습니다. 명령을 내리는 데 사용되는 워크스테이션의 로컬 마운트로 소스와 대상에 액세스할 수 있어야 합니다.

다음 지침을 따라 ``msrsync`` Azure Blob 저장소를 Azure HPC 캐시로 채우는 데 사용합니다.

1. 설치 ``msrsync`` 및 필수 구성``rsync`` 조건(및 파이썬 2.6 이상)
1. 복사할 파일 및 디렉터리의 총 수를 결정합니다.

   예를 들어 인수를 ``prime.py`` 사용하여 유틸리티를 ```prime.py --directory /path/to/some/directory``` 사용합니다(다운로드 <https://github.com/Azure/Avere/blob/master/src/clientapps/dataingestor/prime.py>하여 사용 가능).

   을 사용하지 ``prime.py``않는 경우 다음과 같이 GNU ``find`` 도구를 사용하여 항목 수를 계산할 수 있습니다.

   ```bash
   find <path> -type f |wc -l         # (counts files)
   find <path> -type d |wc -l         # (counts directories)
   find <path> |wc -l                 # (counts both)
   ```

1. 항목 수를 64로 나누어 프로세스당 항목 수를 결정합니다. 명령을 실행할 때 ``-f`` 옵션에 이 숫자를 사용하여 버킷의 크기를 설정합니다.

1. 파일을 ``msrsync`` 복사하는 명령을 내보세요.

   ```bash
   msrsync -P --stats -p64 -f<ITEMS_DIV_64> --rsync "-ahv --inplace" <SOURCE_PATH> <DESTINATION_PATH>
   ```

   예를 들어 이 명령은 64개 프로세스에서 11,000개의 파일을 /test/source-리포지토리에서 /mnt/hpccache/리포지토리로 이동하도록 설계되었습니다.

   ``mrsync -P --stats -p64 -f170 --rsync "-ahv --inplace" /test/source-repository/ /mnt/hpccache/repository``
