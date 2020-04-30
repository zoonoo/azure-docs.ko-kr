---
title: 파일 포함
description: 포함 파일
services: batch
author: LauraBrenner
ms.service: batch
ms.topic: include
ms.date: 05/28/2019
ms.author: labrenne
ms.custom: include file
ms.openlocfilehash: a0d74aa38dffdd41fbe617066391ef271a507349
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81736276"
---
| **리소스** | **기본 제한** | **최대 제한** |
| --- | --- | --- |
| 구독 당 지역별 Azure Batch 계정 | 1-3 |50 |
| Batch 계정당 전용 코어 | 90-900 | 지원에 문의 |
| Batch 계정당 낮은 우선 순위의 코어 수 | 10-100 | 지원에 문의 |
| Batch 계정 당 **[활성](https://docs.microsoft.com/rest/api/batchservice/job/get#jobstate)** 작업 및 작업 일정 (**완료** 된 작업에는 제한이 없음) | 100-300 | 1000<sup>1</sup> |
| Batch 계정당 풀 | 20-100 | 500<sup>1</sup> |

<sup>1</sup> 이 제한을 초과 하 여 증가를 요청 하려면 Azure 지원에 문의 하세요.

> [!NOTE]
> 기본 제한은 Batch 계정을 만드는 데 사용되는 구독 유형에 따라 달라집니다. 표시된 코어 할당량은 Batch 서비스 모드의 Batch 계정에 대한 할당량입니다. [배치 계정에서 할당량을 봅니다](../articles/batch/batch-quota-limit.md#view-batch-quotas).

> [!IMPORTANT]
> 전 세계 health 전염병에서 용량을 관리 하는 데 도움이 되도록 일부 지역의 새 Batch 계정에 대 한 기본 코어 할당량 및 일부 유형의 구독에 대 한 기본 코어 할당량은 위의 값 범위에서 감소 되었습니다 (일부 경우에는 0 개 코어). 새 Batch 계정을 만들 때 [코어 할당량을 확인](../articles/batch/batch-quota-limit.md#view-batch-quotas) 하 고 필요한 경우 [코어 할당량 증가를 요청](../articles/batch/batch-quota-limit.md#increase-a-quota)합니다. 
