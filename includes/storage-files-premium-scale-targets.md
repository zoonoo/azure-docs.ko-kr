---
author: roygara
ms.service: storage
ms.topic: include
ms.date: 08/10/2020
ms.author: rogarana
ms.openlocfilehash: d704c6026e9d007a7365a3b72649ca509585da4d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "88057699"
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

|영역  |표준 파일  |프리미엄 파일  |
|---------|---------|---------|
|크기     |1TiB         |4TiB         |
|파일당 최대 IOPS      |1,000         |5,000         |
|동시 핸들     |2,000         |2,000         |
|송신     |표준 파일 처리량 값 참조         |300 MiB/초         |
|수신     |표준 파일 처리량 값 참조         |200MiB/초         |
|처리량     |최대 60MiB/초         |프리미엄 파일 수신/송신 값 참조         |