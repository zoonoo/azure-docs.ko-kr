---
title: "Azure SQL Database의 서비스에서 생성된 백업에서 가장 오래된 복원 지점 보기 | Microsoft Docs"
description: "데이터베이스의 서비스에서 생성된 백업에서 가장 오래된 복원 지점을 보는 방법 빠른 참조"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: business continuity
ms.devlang: NA
ms.workload: data-management
ms.topic: article
ms.tgt_pltfrm: NA
ms.date: 12/07/2016
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 6d729f89322b67fe75d2c0ad001e70b42ccedb7e
ms.openlocfilehash: ae35dd15c386bbbf4e77a143d245efcd1f44f238


---
# <a name="view-the-oldest-restore-point-from-the-service-generated-backups-of-an-azure-sql-database"></a>Azure SQL Database의 서비스에서 생성된 백업에서 가장 오래된 복원 지점 보기

이 방법 항목에서는 Azure SQL Database의 서비스에서 생성된 백업에서 가장 오래된 복원 지점을 보는 방법을 알아봅니다.

## <a name="view-the-oldest-restore-point-using-the-azure-portal"></a>Azure 포털을 사용하여 가장 오래된 복원 지점 보기

1. 데이터베이스에 대한 **SQL Database** 블레이드를 엽니다.

    ![새 샘플 데이터베이스 블레이드](./media/sql-database-get-started/new-sample-db-blade.png)

2. 도구 모음에서 **복원**을 클릭합니다.

    ![도구 모음 복원](./media/sql-database-get-started-backup-recovery/restore-toolbar.png)

3. 복원 블레이드에서 가장 오래된 복원 지점을 검토합니다.

    ![가장 오래된 복원 지점](./media/sql-database-get-started-backup-recovery/oldest-restore-point.png)

> [!TIP]
> 자습서는 [데이터 보호 및 복구를 위한 백업 및 복원 시작](sql-database-get-started-backup-recovery.md)을 참조하세요.
>

## <a name="next-steps"></a>다음 단계

- 서비스에서 생성된 자동 백업에 대해 알아보려면 [자동 백업](: https://azure.microsoft.com/en-us/documentation/articles/)(sql-database-automated-backups.MD) 참조
- 장기 백업 보존에 대해 알아보려면 [장기 백업 보존](sql-database-long-term-retention.md) 참조
- 백업에서 복원에 대해 알아보려면 [백업에서 복원](sql-database-recovery-using-backups.md) 참조


<!--HONumber=Dec16_HO2-->


