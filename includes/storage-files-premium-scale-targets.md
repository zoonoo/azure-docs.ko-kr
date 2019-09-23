---
author: roygara
ms.service: storage
ms.topic: include
ms.date: 06/07/2019
ms.author: rogarana
ms.openlocfilehash: b28427b3ede0cfaeb9e08d3c73b15ea7f2961f1b
ms.sourcegitcommit: 83df2aed7cafb493b36d93b1699d24f36c1daa45
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/22/2019
ms.locfileid: "71180061"
---
#### <a name="additional-premium-file-share-level-limits"></a>추가 프리미엄 파일 공유 수준 제한

|영역  |대상  |
|---------|---------|
|최소 크기 증가/감소    |GiB 1      |
|기준 IOPS    |GiB 당 IOPS 1 개, 최대 10만|
|IOPS 버스트    |GiB 당 3, 000IOPS, 최대 10만|
|송신 율         |60 MiB/s + 0.06 * 프로 비전 된 GiB        |
|수신 율| 40 MiB/s + 0.04 * 프로 비전 된 GiB |

#### <a name="file-level-limits"></a>파일 수준 제한

|영역  |프리미엄 파일  |표준 파일 |
|---------|---------|---------|
|Size                  |1TiB         |1TiB|
|파일당 최대 IOPS     |5,000         |1,000|
|동시 핸들    |2,000         |2,000|
|송신  |300 MiB/sec|      표준 파일 처리량 값 참조|
|수신  |200 MiB/초| 표준 파일 처리량 값 참조|
|처리량| 프리미엄 파일 수신/송신 값 참조| 최대 60MiB/초|
