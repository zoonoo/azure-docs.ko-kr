---
title: "Azure Recovery Services 자격 증명 모음에 자동화된 백업의 장기 보존 구성 | Microsoft Docs"
description: "Azure 복구 서비스 자격 증명 모음에 자동화된 백업의 장기 보존을 구성하는 방법에 대한 빠른 참조"
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
ms.openlocfilehash: b395fc5c1deaf04205dcfccacd13d30ec90dd1e7


---
# <a name="configure-long-term-retention-of-automated-backups-in-an-azure-recovery-services-vault"></a>Azure Recovery Services 자격 증명 모음에 자동화된 백업의 장기 보존 구성 

이 방법 항목에서는 Azure 복구 서비스 자격 증명 모음에 자동화된 백업의 장기 보존을 구성하는 방법에 대해 알아봅니다.

## <a name="configure-long-term-retention-using-the-azure-portal"></a>Azure Portal를 사용한 장기 보존 구성

1. 서버에 대한 **SQL Server** 블레이드를 엽니다.

    ![SQL 서버 블레이드](./media/sql-database-get-started/sql-server-blade.png)

2. **장기 백업 보존**을 클릭합니다.

   ![장기 백업 보존 링크](./media/sql-database-get-started-backup-recovery/long-term-backup-retention-link.png)

3. **장기 백업 보존** 블레이드에서 미리 보기 약관을 검토하고 동의합니다(이미 실행하지 않은 한 - 또는 이 기능은 미리 보기에 없음).

   ![미리 보기 약관 동의](./media/sql-database-get-started-backup-recovery/accept-the-preview-terms.png)

4. 데이터베이스에 대한 장기 백업 보존을 구성하려면 표에서 해당 데이터베이스를 선택하고 도구 모음에서 **구성**을 클릭합니다.

   ![장기 백업 보존을 위한 데이터베이스 선택](./media/sql-database-get-started-backup-recovery/select-database-for-long-term-backup-retention.png)

5. **구성** 블레이드에서 **복구 서비스 자격 증명 모음** 아래의 **필요한 설정 구성**을 클릭합니다.

   ![자격 증명 모음 링크 구성](./media/sql-database-get-started-backup-recovery/configure-vault-link.png)

6. **Recovery Services 자격 증명 모음** 블레이드에서 있는 경우 기존 자격 증명 모음을 선택합니다. 그렇지 않으면 구독에 대한 복구 서비스 자격 증명 모음을 찾을 수 없는 경우 클릭하여 흐름을 종료하고 복구 서비스 자격 증명 모음을 만듭니다.

   ![새 자격 증명 모음 링크 만들기](./media/sql-database-get-started-backup-recovery/create-new-vault-link.png)

7. **Recovery Services 자격 증명 모음** 블레이드에서 **추가**를 클릭합니다.

   ![새 자격 증명 모음 링크 추가](./media/sql-database-get-started-backup-recovery/add-new-vault-link.png)
   
8. **Recovery Services 자격 증명 모음** 블레이드에서 새 Recovery Services 자격 증명 모음에 대한 유효한 이름을 제공합니다.

   ![새 자격 증명 모음 이름](./media/sql-database-get-started-backup-recovery/new-vault-name.png)

9. 구독 및 리소스 그룹을 선택한 다음 자격 증명 모음에 대한 위치를 선택합니다. 완료하면 **만들기**를 클릭합니다.

   ![새 자격 증명 모음 만들기](./media/sql-database-get-started-backup-recovery/create-new-vault.png)

   > [!IMPORTANT]
   > 자격 증명 모음은 Azure SQL 논리 서버와 동일한 지역에 있어야 하고 논리 서버와 동일한 리소스 그룹을 사용해야 합니다.
   >

10. 새 자격 증명 모음을 만든 후 필요한 단계를 실행하여 **Recovery Services 자격 증명 모음** 블레이드로 돌아갑니다.

11. **Recovery Services 자격 증명 모음** 블레이드에서 자격 증명 모음을 클릭한 다음 **선택**을 클릭합니다.

   ![기존 자격 증명 모음 선택](./media/sql-database-get-started-backup-recovery/select-existing-vault.png)

12. **구성** 블레이드에서 새 보존 정책에 대한 유효한 이름을 제공하고 기본 보존 정책을 적절하게 수정한 다음 **확인**을 클릭합니다.

   ![보존 정책 정의](./media/sql-database-get-started-backup-recovery/define-retention-policy.png)

13. **장기 백업 보존** 블레이드에서 **저장**을 클릭한 다음 **확인**을 클릭하여 선택된 모든 데이터베이스에 장기 백업 보존 정책을 적용합니다.

   ![보존 정책 정의](./media/sql-database-get-started-backup-recovery/save-retention-policy.png)

14. **저장**을 클릭하여 구성한 Azure Recovery Services 자격 증명 모음에 이 새 정책을 사용하는 장기 백업 보존을 활성화합니다.

   ![보존 정책 정의](./media/sql-database-get-started-backup-recovery/enable-long-term-retention.png)

14. 장기 백업 보존을 활성화한 후에 **복구 서비스 자격 증명 모음** 블레이드를 엽니다(**모든 리소스**로 이동하고 구독에 대한 리소스 목록에서 선택).

   ![복구 서비스 자격 증명 모음 보기](./media/sql-database-get-started-backup-recovery/view-recovery-services-vault.png)


> [!TIP]
> 자습서는 [데이터 보호 및 복구를 위한 백업 및 복원 시작](sql-database-get-started-backup-recovery.md)을 참조하세요.
>

## <a name="next-steps"></a>다음 단계

- 장기 보존에 있는 백업에서 데이터베이스를 복구하려면 [장기 보존에 있는 백업에서 복구](sql-database-restore-from-long-term-retention.md)를 참조하세요
- Azure 복구 서비스 자격 증명 모음에서 백업을 보려면 [장기 보존에서 백업을 보기](sql-database-view-backups-in-vault.md)를 참조하세요.
- 서비스에서 생성된 자동 백업에 대해 알아보려면 [자동 백업](: https://azure.microsoft.com/en-us/documentation/articles/)(sql-database-automated-backups.MD) 참조
- 장기 백업 보존에 대해 알아보려면 [장기 백업 보존](sql-database-long-term-retention.md) 참조
- 백업에서 복원에 대해 알아보려면 [백업에서 복원](sql-database-recovery-using-backups.md) 참조


<!--HONumber=Dec16_HO2-->


