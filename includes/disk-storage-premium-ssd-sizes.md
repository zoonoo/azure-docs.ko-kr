---
title: 포함 파일
description: 포함 파일
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 10/24/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 52dbf03c7d4c2c2de565d6793fc867cf5b886db3
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73996981"
---
| 프리미엄 SSD 크기 | P1* | P2* | P3* | P4 | P6 | P10 | P15 | P20 | P30 | P40 | P50 | P60 | P70 | P80 |
|-------------------|----|----|----|----|----|-----|-----|-----|-----|-----|-----|------|------|------|
| 디스크 크기(GiB) | 4 | 8 | 16 | 32 | 64 | 128 | 256 | 512 | 1,024 | 2,048 | 4,096 | 8,192 | 16,384 | 32,767 |
| 디스크당 IOPS | 120 | 120 | 120 | 120 | 240 | 500 | 1,100 | 2,300 | 5,000 | 7,500 | 7,500 | 16,000 | 18,000 | 20,000 |
| 디스크당 처리량 | 25MiB/초 | 25MiB/초 | 25MiB/초 | 25MiB/초 | 50MiB/초 | 100MiB/초 | 125MiB/초 | 150MiB/초 | 200MiB/초 | 250MiB/초 | 250MiB/초| 500MiB/초 | 750MiB/초 | 900MiB/초 |
| 디스크당 최대 버스트 IOPS** | 3,500 | 3,500 | 3,500 | 3,500 | 3,500 | 3,500 | 3,500 | 3,500 |
| 디스크당 최대 버스트 처리량** | 170MiB/초 | 170MiB/초 | 170MiB/초 | 170MiB/초 | 170MiB/초 | 170MiB/초 | 170MiB/초 | 170MiB/초 |
| 최대 버스트 기간** | 30분  | 30분  | 30분  | 30분  | 30분  | 30분  | 30분  | 30분  |

\*현재 미리 보기로 제공되는 디스크 크기를 나타냅니다. 지역별 가용성 정보는 [새 디스크 크기: 관리형 및 비관리형](https://docs.microsoft.com/azure/virtual-machines/linux/faq-for-disks#new-disk-sizes-managed-and-unmanaged)을 참조하세요.

\**현재 미리 보기로 제공되는 기능을 나타냅니다. 자세한 내용은 [디스크 버스트](https://docs.microsoft.com/azure/virtual-machines/linux/disk-bursting#regional-availability)를 참조하세요.
