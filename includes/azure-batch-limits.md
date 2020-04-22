---
title: 포함 파일
description: 포함 파일
services: batch
author: LauraBrenner
ms.service: batch
ms.topic: include
ms.date: 05/28/2019
ms.author: labrenne
ms.custom: include file
ms.openlocfilehash: a0d74aa38dffdd41fbe617066391ef271a507349
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81736276"
---
| **리소스** | **기본 제한** | **최대 제한** |
| --- | --- | --- |
| 구독당 지역별 Azure 일괄 처리 계정 | 1-3 |50 |
| Batch 계정당 전용 코어 | 90-900 | 지원에 문의 |
| Batch 계정당 낮은 우선 순위의 코어 수 | 10-100 | 지원에 문의 |
| Batch 계정당 **[활성](https://docs.microsoft.com/rest/api/batchservice/job/get#jobstate)** 작업 및 작업**일정(완료된** 작업에는 제한이 없음) | 100-300 | 1,000<sup>1</sup> |
| Batch 계정당 풀 | 20-100 | 500<sup>1</sup> |

<sup>1개</sup> 이 제한을 초과하는 증가를 요청하려면 Azure 지원에 문의하십시오.

> [!NOTE]
> 기본 제한은 Batch 계정을 만드는 데 사용되는 구독 유형에 따라 달라집니다. 표시된 코어 할당량은 Batch 서비스 모드의 Batch 계정에 대한 할당량입니다. [배치 계정에서 할당량을 봅니다](../articles/batch/batch-quota-limit.md#view-batch-quotas).

> [!IMPORTANT]
> 글로벌 건강 전염병 발생 시 용량을 더 잘 관리할 수 있도록 일부 지역의 새 Batch 계정 및 일부 유형의 구독에 대한 기본 핵심 할당량이 위의 값 범위에서 0 코어로 감소했습니다. 새 Batch 계정을 만들 때 핵심 할당량을 확인하고 필요한 경우 핵심 할당량 증가를 [요청합니다.](../articles/batch/batch-quota-limit.md#view-batch-quotas) [request a core quota increase](../articles/batch/batch-quota-limit.md#increase-a-quota) 
