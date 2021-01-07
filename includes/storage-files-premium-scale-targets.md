---
author: roygara
ms.service: storage
ms.topic: include
ms.date: 08/10/2020
ms.author: rogarana
ms.openlocfilehash: 8dcb58499113b0b7ae0814419f0a76965a0ed945
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2020
ms.locfileid: "94680834"
---
#### <a name="additional-premium-file-share-level-limits"></a>추가 프리미엄 파일 공유 수준 제한

|영역  |Target  |
|---------|---------|
|최소 크기 증가/감소    |GiB 1      |
|기준 IOPS    |GiB 당 IOPS 1 개, 최대 10만|
|IOPS 버스트    |GiB 당 3, 000IOPS, 최대 10만|
|송신 율         |60 MiB/s + 0.06 * 프로 비전 된 GiB        |
|수신 율| 40 MiB/s + 0.04 * 프로 비전 된 GiB |

#### <a name="file-level-limits"></a>파일 수준 제한

|영역  |표준 파일  |프리미엄 파일  |
|---------|---------|---------|
|크기     |1TiB         |4TiB         |
|파일당 최대 IOPS      |1,000         |최대 8000 *         |
|동시 핸들     |2,000         |2,000         |
|송신     |표준 파일 처리량 값 참조         |300 MiB/sec (SMB 다중 채널 미리 보기가 포함 된 최대 1 GiB/s) * *         |
|수신     |표준 파일 처리량 값 참조         |200 MiB/sec (SMB 다중 채널 미리 보기가 포함 된 최대 1 GiB/s) * *        |
|처리량     |최대 60MiB/초         |프리미엄 파일 수신/송신 값 참조         |

\*<sup>읽기 및 쓰기 IOs에 적용 됩니다 (일반적으로 더 작은 IO 크기 <= 64k). 읽기 및 쓰기 이외의 메타 데이터 작업은 더 낮을 수 있습니다. </sup>

\*\*<sup>컴퓨터 네트워크 제한, 사용 가능한 대역폭, IO 크기, 큐 크기 및 기타 요인에 따라 결정 됩니다. 자세한 내용은 [SMB 다중 채널 성능](../articles/storage/files/storage-files-smb-multichannel-performance.md)을 참조 하세요. </sup>
