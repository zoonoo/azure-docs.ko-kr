---
author: roygara
ms.service: storage
ms.topic: include
ms.date: 06/07/2019
ms.author: rogarana
ms.openlocfilehash: 368f08272173b019873dfe20e1164d6baf72ff5e
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/03/2019
ms.locfileid: "67542647"
---
#### <a name="additional-premium-file-share-level-limits"></a>추가 프리미엄 파일 공유 수준 제한

|영역  |대상  |
|---------|---------|
|최소 크기 증가/감소    |1 GiB      |
|기준 IOPS    |최대 100,000 개 GiB 당 1 개 IOPS|
|버스팅 IOPS    |최대 100,000 개 GiB 당 3 x IOPS|
|송신 속도         |60 초 + 0.06 * GiB 프로 비전        |
|수신 속도| 40 초 + 0.04 * GiB 프로 비전 |

#### <a name="file-level-limits"></a>파일 수준 제한

|영역  |프리미엄 파일  |표준 파일 |
|---------|---------|---------|
|Size                  |1TiB         |1TiB|
|파일당 최대 IOPS     |5,000         |1,000|
|동시 핸들 수    |2,000         |2,000|
|수신  |300 MiB/sec|      표준 파일 처리량 값을 참조 하세요.|
|송신   |200 Mib/sec| 표준 파일 처리량 값을 참조 하세요.|
|처리량| 프리미엄 파일 수신/송신 값을 참조 하세요.| 최대 60MiB/초|