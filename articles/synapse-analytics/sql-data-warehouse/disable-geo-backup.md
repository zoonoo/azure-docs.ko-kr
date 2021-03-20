---
title: 지역 백업 사용 안 함
description: Azure Synapse Analytics에서 전용 SQL 풀 (이전의 SQL DW)에 대해 지역 백업을 사용 하지 않도록 설정 하는 방법 가이드
services: synapse-analytics
author: joannapea
manager: igorstan
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 01/06/2021
ms.author: joanpo
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 395d5f0697138155b0bb0c629461aada9e9c18c6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98739118"
---
# <a name="disable-geo-backups-for-a-dedicated-sql-pool-formerly-sql-dw-in-azure-synapse-analytics"></a>Azure Synapse Analytics에서 전용 SQL 풀 (이전의 SQL DW)에 대해 지역 백업 사용 안 함

이 문서에서는 전용 SQL 풀 (이전의 SQL DW) Azure Portal에 대 한 지역 백업을 사용 하지 않도록 설정 하는 방법에 대해 알아봅니다.

## <a name="disable-geo-backups-through-azure-portal"></a>Azure Portal를 통한 지역 백업 사용 안 함

전용 SQL 풀 (이전의 SQL DW)에 대해 지역 백업을 사용 하지 않도록 설정 하려면 다음 단계를 수행 합니다.

> [!NOTE]
> 지역 백업을 사용 하지 않도록 설정 하는 경우 더 이상 전용 SQL 풀 (이전의 SQL DW)을 다른 Azure 지역으로 복구할 수 없습니다. 
>

1. [Azure Portal](https://portal.azure.com/) 계정에 로그인 합니다.
1. 에서 지역 백업을 사용 하지 않도록 설정할 전용 SQL 풀 (이전의 SQL DW) 리소스를 선택 합니다. 
1. 왼쪽 탐색 패널의 **설정** 에서 **지역 백업 정책** 을 선택 합니다.

   ![지역 백업 사용 안 함](./media/sql-data-warehouse-restore-from-geo-backup/disable-geo-backup-1.png)

1. 지역 백업을 사용 하지 않도록 설정 하려면 **사용 안 함** 을 선택 합니다. 

   ![지역 백업 사용 안 함](./media/sql-data-warehouse-restore-from-geo-backup/disable-geo-backup-2.png)

1. *저장* 을 선택 하 여 설정이 저장 되도록 합니다. 

   ![지역 백업 설정 저장](./media/sql-data-warehouse-restore-from-geo-backup/disable-geo-backup-3.png)

## <a name="next-steps"></a>다음 단계

- [기존 전용 SQL 풀 복원 (이전의 SQL DW)](sql-data-warehouse-restore-active-paused-dw.md)
- [삭제 된 전용 SQL 풀 복원 (이전의 SQL DW)](sql-data-warehouse-restore-deleted-dw.md)
