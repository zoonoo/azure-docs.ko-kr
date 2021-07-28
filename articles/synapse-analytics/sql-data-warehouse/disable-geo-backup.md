---
title: 지역 백업 사용 안 함
description: Azure Synapse Analytics에서 전용 SQL 풀(이전의 SQL DW)에 대해 지역 백업을 사용하지 않는 방법 가이드
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
ms.openlocfilehash: 2cebee3ad9b515c6f40529fe5d25da687fd53687
ms.sourcegitcommit: 32ee8da1440a2d81c49ff25c5922f786e85109b4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/12/2021
ms.locfileid: "109786288"
---
# <a name="disable-geo-backups-for-a-dedicated-sql-pool-formerly-sql-dw-in-azure-synapse-analytics"></a>Azure Synapse Analytics에서 [전용 SQL 풀(이전의 SQL DW)](sql-data-warehouse-overview-what-is.md)에 대해 지역 백업 사용 안 함

이 문서에서는 전용 SQL 풀(이전의 SQL DW) Azure Portal에 대해 지역 백업을 사용하지 않는 방법에 대해 알아봅니다.

## <a name="disable-geo-backups-through-azure-portal"></a>Azure Portal을 통해 지역 백업을 사용하지 않음

전용 SQL 풀(이전의 SQL DW)에 대해 지역 백업을 사용하지 않으려면 다음 단계를 따릅니다.

> [!NOTE]
> 지역 백업을 사용하지 않으면 더 이상 전용 SQL 풀(이전의 SQL DW)을 다른 Azure 지역으로 복구할 수 없습니다. 
> 

1. [Azure Portal](https://portal.azure.com/) 계정에 로그인합니다.
1. 지역 백업을 사용하지 않을 전용 SQL 풀(이전의 SQL DW) 리소스를 선택합니다. 
1. 왼쪽 탐색 패널의 **설정** 에서 **지역 백업 정책** 을 선택합니다.

   ![지역 백업 사용 안 함](./media/sql-data-warehouse-restore-from-geo-backup/disable-geo-backup-1.png)

1. 지역 백업을 사용하지 않으려면 **사용 안 함** 을 선택합니다. 

   ![사용할 수 없는 지역 백업](./media/sql-data-warehouse-restore-from-geo-backup/disable-geo-backup-2.png)

1. *저장* 을 선택하여 설정이 저장되도록 합니다. 

   ![지역 백업 설정 저장](./media/sql-data-warehouse-restore-from-geo-backup/disable-geo-backup-3.png)

## <a name="next-steps"></a>다음 단계

- [기존 전용 SQL 풀(이전의 SQL DW) 복원](sql-data-warehouse-restore-active-paused-dw.md)
- [삭제된 전용 SQL 풀(이전의 SQL DW) 복원](sql-data-warehouse-restore-deleted-dw.md)
