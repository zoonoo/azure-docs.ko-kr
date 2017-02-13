---
title: "Azure 복구 서비스 자격 증명 모음에서 백업 보기 | Microsoft Docs"
description: "Azure 복구 서비스 자격 증명 모음 및 해당 백업이 사용되는 공간에서 백업을 확인하는 방법에 대한 빠른 참조"
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
ms.openlocfilehash: 723be587128c131670c9905572d808c176362fb8


---
# <a name="view-information-about-your-database-backups-in-long-term-backup-retention"></a>장기 백업 보존에서 데이터베이스 백업에 대한 정보 보기

이 방법 항목에서는 장기 백업 보존에서 데이터베이스 백업에 대한 정보를 알아봅니다.

## <a name="view-long-term-backup-retention-information-using-the-azure-portal"></a>Azure 포털을 사용하여 장기 백업 보존 정보 보기 

1. Azure 서비스 자격 증명 모음에 대한 블레이드를 열어(**모든 리소스**로 이동하고 구독에 대한 리소스 목록에서 선택) 자격 증명 모음의 데이터베이스 백업에서 사용되는 저장소의 양을 봅니다.

   ![백업과 함께 복구 서비스 자격 증명 모음 보기](./media/sql-database-get-started-backup-recovery/view-recovery-services-vault-with-data.png)

2. 데이터베이스에 대한 **SQL Database** 블레이드를 엽니다.

    ![새 샘플 데이터베이스 블레이드](./media/sql-database-get-started/new-sample-db-blade.png)

3. 도구 모음에서 **복원**을 클릭합니다.

    ![도구 모음 복원](./media/sql-database-get-started-backup-recovery/restore-toolbar.png)

4. 복원 블레이드에서 **장기**를 클릭합니다.

5. Azure 자격 증명 모음 백업에서 **백업 선택**을 클릭하여 장기 백업 보존에서 사용 가능한 데이터베이스 백업을 봅니다.

    ![자격 증명 모음의 백업](./media/sql-database-get-started-backup-recovery/view-backups-in-vault.png)

> [!TIP]
> 자습서는 [데이터 보호 및 복구를 위한 백업 및 복원 시작](sql-database-get-started-backup-recovery.md)을 참조하세요.
>

## <a name="next-steps"></a>다음 단계

- Azure 복구 서비스 자격 증명 모음에 자동화된 백업의 장기 보존을 구성하려면 [](sql-database-configure-long-term-retention.md)의 내용을 참조하세요.
- 장기 백업 보존에 있는 백업에서 데이터베이스를 복원하려면 [장기 보존에서 복원](sql-database-restore-from-long-term-retention.md) 참조
- 서비스에서 생성된 자동 백업에 대해 알아보려면 [자동 백업](sql-database-automated-backups.md) 참조
- 장기 백업 보존에 대해 알아보려면 [장기 백업 보존](sql-database-long-term-retention.md) 참조
- 백업에서 복원에 대해 알아보려면 [백업에서 복원](sql-database-recovery-using-backups.md) 참조


<!--HONumber=Dec16_HO2-->


