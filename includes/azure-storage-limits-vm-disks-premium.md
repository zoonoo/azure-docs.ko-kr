---
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 11/09/2018
ms.author: rogarana
ms.openlocfilehash: e7e57c6a821731874dcb1d99a3133b6ede1da26e
ms.sourcegitcommit: 23389df08a9f4cab1f3bb0f474c0e5ba31923f12
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/10/2019
ms.locfileid: "67182192"
---
**프리미엄 관리 되지 않는 가상 머신 디스크: 계정 당 제한**

| 리소스 | 기본 제한 |
| --- | --- |
| 계정당 총 디스크 용량 |35TB |
| 계정당 총 스냅샷 용량 |10TB |
| 계정 당 최대 대역폭 (수신 + 송신)<sup>1</sup> |<=50 Gbps |

<sup>1</sup> *수신* 은 저장소 계정에 전송 된 요청의 모든 데이터를 나타냅니다. *송신* 은 저장소 계정에서 받은 응답의 모든 데이터를 참조 합니다.

**프리미엄 관리 되지 않는 가상 머신 디스크: 디스크당 제한**

| Premium Storage 디스크 유형 | P10 | P20 | P30 | P40 | P50 |
| --- | --- | --- | --- | --- | --- |
| 디스크 크기 |128GiB |512GiB |1024 GiB (1TB) |2048 GiB (2tb)|4095 GiB (4 TB)|
| 디스크당 최대 IOPS |500 |2,300 |5,000 |7,500 |7,500 |
| 디스크당 최대 처리량 |100MB/초 | 150MB/초 |200MB/초 |250MB/초 |250MB/초 |
| 저장소 계정 당 최대 디스크 수 |280 |70 |35 | 17 | 8 |

**프리미엄 관리 되지 않는 가상 머신 디스크: VM 당 제한**

| 리소스 | 기본 제한 |
| --- | --- |
| VM 당 최대 IOPS |GS5 VM에서 80,000 IOPS |
| VM 당 최대 처리량 |GS5 VM을 사용 하는 2000 m b/초 |

