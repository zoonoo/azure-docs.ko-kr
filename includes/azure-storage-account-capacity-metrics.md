---
author: normesta
ms.service: storage
ms.topic: include
ms.date: 09/28/2020
ms.author: normesta
ms.openlocfilehash: 1aeaa8607618a6f0a2dbf756e6ddae420627bce6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101749990"
---
이 표에서는 [계정 수준 메트릭을](../articles/azure-monitor/essentials/metrics-supported.md#microsoftstoragestorageaccounts)보여 줍니다.

| 메트릭 | Description |
| ------------------- | ----------------- |
| UsedCapacity | 스토리지 계정에서 사용한 스토리지 양입니다. 표준 스토리지 계정의 경우 이는 Blob, 테이블, 파일 및 큐에서 사용한 용량의 합계입니다. Premium Storage 계정 및 Blob Storage 계정의 경우 BlobCapacity와 같습니다. <br/><br/> 단위: 바이트 <br/> 집계 유형: 평균 <br/> 값 예제: 1024 |