---
title: "Azure Recovery Service 자격 증명 모음에서 백업의 데이터베이스 복원 | Microsoft Docs"
description: "Azure 복구 서비스 자격 증명 모음 및 해당 백업이 사용되는 공간에서 백업에 대한 데이터베이스를 복원하는 방법에 대한 빠른 참조"
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
ms.openlocfilehash: cdb898ef86517b6eb6eed4b2ae38c4e327e78181


---
# <a name="restore-a-database-from-a-backup-in-long-term-backup-retention"></a>장기 백업 보존의 백업에서 데이터베이스 복원

이 방법 항목에서는 장기 백업 보존의 백업에서 데이터베이스를 복원하는 방법을 알아봅니다.

## <a name="restore-from-long-term-backup-retention-using-the-azure-portal"></a>Azure 포털을 사용하여 장기 백업 보존에서 복원

1. **Azure 자격 증명 모음 백업** 블레이드에서 백업을 클릭하여 복원한 다음 **선택**을 클릭합니다.

    ![자격 증명 모음에서 백업 선택](./media/sql-database-get-started-backup-recovery/select-backup-in-vault.png)

2. **데이터베이스 이름** 텍스트 상자에서 복원된 데이터베이스에 대한 이름을 제공합니다.

    ![새 데이터베이스 이름](./media/sql-database-get-started-backup-recovery/new-database-name.png)

3. **확인**을 클릭하여 자격 증명 모음의 백업에서 새 데이터베이스로 데이터베이스를 복원합니다.

4. 도구 모음에서 알림 아이콘을 클릭하여 복원 작업의 상태를 봅니다.

    ![자격 증명 모음에서 복원 작업 진행률](./media/sql-database-get-started-backup-recovery/restore-job-progress-long-term.png)

5. 복원 작업이 완료되면 **SQL 데이터베이스** 블레이드를 열어 새로 복원된 데이터베이스를 봅니다.

    ![자격 증명 모음에서 복원된 데이터베이스](./media/sql-database-get-started-backup-recovery/restored-database-from-vault.png)

> [!TIP]
> 자습서는 [데이터 보호 및 복구를 위한 백업 및 복원 시작](sql-database-get-started-backup-recovery.md)을 참조하세요.
>

## <a name="next-steps"></a>다음 단계

- Azure Recovery Services 자격 증명 모음에 자동화된 백업의 장기 보존을 구성하려면 [장기 백업 보존 구성](sql-database-configure-long-term-retention.md)을 참조하세요.
- Azure 복구 서비스 자격 증명 모음에서 백업을 보려면 [장기 보존에서 백업을 보기](sql-database-view-backups-in-vault.md)를 참조하세요.
- 서비스에서 생성된 자동 백업에 대해 알아보려면 [자동 백업](: https://azure.microsoft.com/en-us/documentation/articles/)(sql-database-automated-backups.MD) 참조
- 장기 백업 보존에 대해 알아보려면 [장기 백업 보존](sql-database-long-term-retention.md) 참조
- 백업에서 복원에 대해 알아보려면 [백업에서 복원](sql-database-recovery-using-backups.md) 참조


<!--HONumber=Dec16_HO2-->


