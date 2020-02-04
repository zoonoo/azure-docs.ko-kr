---
title: 포함 파일
description: 포함 파일
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 01/07/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 7b2d4777772d842898cfcdd04f1c6d926cdbf0ad
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/29/2020
ms.locfileid: "76837564"
---
| 프리미엄 SSD 크기 | P1* | P2* | P3* | P4 | P6 | P10 | P15 | P20 | P30 | P40 | P50 | P60 | P70 | P80 |
|-------------------|----|----|----|----|----|-----|-----|-----|-----|-----|-----|------|------|------|
| 디스크 크기(GiB) | 4 | 8 | 16 | 32 | 64 | 128 | 256 | 512 | 1,024 | 2,048 | 4,096 | 8,192 | 16,384 | 32,767 |
| 디스크당 IOPS | 120 | 120 | 120 | 120 | 240 | 500 | 1,100 | 2,300 | 5,000 | 7,500 | 7,500 | 16,000 | 18,000 | 20,000 |
| 디스크당 처리량 | 25MiB/초 | 25MiB/초 | 25MiB/초 | 25MiB/초 | 50MiB/초 | 100MiB/초 | 125MiB/초 | 150MiB/초 | 200MiB/초 | 250MiB/초 | 250MiB/초| 500MiB/초 | 750MiB/초 | 900MiB/초 |
| 디스크당 최대 버스트 IOPS** | 3,500 | 3,500 | 3,500 | 3,500 | 3,500 | 3,500 | 3,500 | 3,500 |
| 디스크당 최대 버스트 처리량** | 170MiB/초 | 170MiB/초 | 170MiB/초 | 170MiB/초 | 170MiB/초 | 170MiB/초 | 170MiB/초 | 170MiB/초 |
| 최대 버스트 기간** | 30분  | 30분  | 30분  | 30분  | 30분  | 30분  | 30분  | 30분  |
| 예약 가능 | 예  | 예  | 예  | 예  | 예  | 예  | 예  | 예  | 예, 최대 1년 | 예, 최대 1년 | 예, 최대 1년 | 예, 최대 1년 | 예, 최대 1년 | 예, 최대 1년 |

\*현재 미리 보기로 제공되는 디스크 크기를 나타냅니다. 지역별 가용성 정보는 [새 디스크 크기: 관리형 및 비관리형](https://docs.microsoft.com/azure/virtual-machines/linux/faq-for-disks#new-disk-sizes-managed-and-unmanaged)을 참조하세요.  
\*\*현재 미리 보기로 제공되는 기능을 나타냅니다. 자세한 내용은 [디스크 버스트](https://docs.microsoft.com/azure/virtual-machines/linux/disk-bursting#regional-availability)를 참조하세요.
