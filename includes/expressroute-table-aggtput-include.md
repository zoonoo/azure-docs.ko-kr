---
title: 포함 파일
description: 포함 파일
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: include
ms.date: 12/05/2019
ms.author: duau
ms.custom: include file
ms.openlocfilehash: b1465b9bf855d592d195277b616c860a23c179f6
ms.sourcegitcommit: c2a41648315a95aa6340e67e600a52801af69ec7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106504702"
---
| 게이트웨이 SKU | 초당 연결 | 흐름 수 | 초당 메가비트 | 초당 패킷 수 | 회로 대역폭 | 게이트웨이에서 알린 경로 수(MSEE로) | 게이트웨이에서 학습한 경로 수(MSEE에서) | 가상 네트워크의 VM 수 |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| **기본 SKU(더 이상 사용되지 않음)** | 해당 없음 | 해당 없음 | 500 | 해당 없음 | 해당 없음 | 해당 없음 | 해당 없음 | 해당 없음 |
| **표준 SKU/ErGw1AZ** | 7,000 | 400,000 | >1,000 | >100,000 | 1Gbps |  500 | 4,000 | 2,000(유지 관리 중 1,000으로 감소, 나중에 복원) | 
| **고성능 SKU/ErGw2AZ** | 14,000 | 840,000 | >2,000 | 250,000 | 1Gbps | 500 | ~9500(6,500개 이상의 VM이 가상 네트워크에 있는 경우 4,000으로 감소) | 4,500 |
| **Ultra 성능 SKU/ErGw3AZ** | 16,000 | 950,000 | ~10,000 | 1,000,000 | 1Gbps | 500 | ~9,500 | 11,000 |
