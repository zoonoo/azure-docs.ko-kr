---
title: 포함 파일
description: 포함 파일
services: batch
author: JnHs
ms.service: batch
ms.topic: include
ms.date: 12/16/2020
ms.author: jenhayes
ms.custom: include file
ms.openlocfilehash: 0a94b122f1cdd598eeac553c8cc784d2a0a5369f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97614519"
---
| **리소스** | **기본 제한** | **최대 한도** |
| --- | --- | --- |
| 구독당 지역별 Azure Batch 계정 | 1-3 |50 |
| Batch 계정당 전용 코어 | 90-900 | 지원에 문의 |
| Batch 계정당 낮은 우선 순위의 코어 수 | 10-100 | 지원에 문의 |
| Batch 계정당 **[활성](/rest/api/batchservice/job/get#jobstate)** 작업 및 작업 일정(**완료된** 작업에는 제한이 없음) | 100-300 | 1,000<sup>1</sup> |
| Batch 계정당 풀 | 20-100 | 500<sup>1</sup> |

<sup>1</sup> 이 한도를 초과하여 늘리려면 Azure 지원에 문의합니다.

> [!NOTE]
> 기본 제한은 Batch 계정을 만드는 데 사용되는 구독 유형에 따라 달라집니다. 표시된 코어 할당량은 Batch 서비스 모드의 Batch 계정에 대한 할당량입니다. [배치 계정에서 할당량을 봅니다](../articles/batch/batch-quota-limit.md#view-batch-quotas).

> [!IMPORTANT]
> 전 세계 감염병 대유행 기간 동안 용량을 보다 효율적으로 관리하기 위해 일부 지역의 신규 Batch 계정과 일부 유형의 구독에 대한 기본 코어 할당량이 위의 값 범위에서 줄었으며, 일부 경우 0개 코어로 줄었습니다. 새 Batch 계정을 만들 때 [코어 할당량을 확인](../articles/batch/batch-quota-limit.md#view-batch-quotas)하고 필요한 경우 [코어 할당량 증가를 요청](../articles/batch/batch-quota-limit.md#increase-a-quota)합니다. 또는 이미 할당량이 충분한 배치 계정을 다시 사용하는 것이 좋습니다.