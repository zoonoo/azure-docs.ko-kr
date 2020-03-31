---
author: roygara
ms.service: storage
ms.topic: include
ms.date: 06/07/2019
ms.author: rogarana
ms.openlocfilehash: b28427b3ede0cfaeb9e08d3c73b15ea7f2961f1b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "71180061"
---
#### <a name="additional-premium-file-share-level-limits"></a>추가 프리미엄 파일 공유 수준 제한

|영역  |대상  |
|---------|---------|
|최소 크기 증가/감소    |1 GiB      |
|기준 IOPS    |GiB당 IOPS 1개, 최대 100,000개|
|IOPS 버스팅    |GiB당 3x IOPS, 최대 100,000|
|출구 율         |60 MiB/s + 0.06 * 프로비저닝된 GiB        |
|침투 율| 40 MiB/s + 0.04 * 프로비저닝된 GiB |

#### <a name="file-level-limits"></a>파일 수준 제한

|영역  |프리미엄 파일  |표준 파일 |
|---------|---------|---------|
|크기                  |1TiB         |1TiB|
|파일당 최대 IOPS     |5,000         |1,000|
|동시 핸들    |2,000         |2,000|
|송신  |초당 300MiB|      표준 파일 처리량 값 보기|
|수신  |200MiB/초| 표준 파일 처리량 값 보기|
|처리량| 프리미엄 파일 송신/송신 값 보기| 최대 60MiB/초|
