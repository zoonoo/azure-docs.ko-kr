---
title: "Azure SQL Database 자동, 지역 중복 백업 | Microsoft Docs"
description: "SQL Database는 몇 분마다 로컬 데이터베이스 백업을 자동으로 만들고 Azure 읽기 액세스 지역 중복 저장소를 사용하여 지리적 중복을 제공합니다."
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 3ee3d49d-16fa-47cf-a3ab-7b22aa491a8d
ms.service: sql-database
ms.custom: business continuity
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/05/2017
ms.author: carlrab
ms.openlocfilehash: 5fbad8beebfaef370ea766e07932f1927f1d42e7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="learn-about-automatic-sql-database-backups"></a>자동 SQL Database 백업에 대한 자세한 정보

SQL Database는 데이터베이스 백업을 자동으로 만들고 Azure RA-GRS(읽기 액세스 지역 중복 저장소)를 사용하여 지리적 중복을 제공합니다. 이러한 백업은 추가 비용 없이 자동으로 만들어집니다. 사용자는 아무 작업도 할 필요가 없습니다. 데이터베이스 백업은 실수로 손상되거나 삭제되지 않도록 데이터를 보호해 주기 때문에 비즈니스 연속성 및 재해 복구 전략의 필수적인 부분입니다. 고유한 저장소 컨테이너에서 백업을 유지하려면 장기 백업 보존 정책을 구성할 수 있습니다. 자세한 내용은 [장기 보존](sql-database-long-term-retention.md)을 참조하세요.

## <a name="what-is-a-sql-database-backup"></a>SQL Database 백업이란?

SQL Database는 SQL Server 기술을 사용하여 [전체](https://msdn.microsoft.com/library/ms186289.aspx), [차등](https://msdn.microsoft.com/library/ms175526.aspx) 및 [트랜잭션 로그](https://msdn.microsoft.com/library/ms191429.aspx) 백업을 만듭니다. 일반적으로 트랜잭션 로그 백업은 데이터베이스 활동의 성능 수준 및 양에 따른 빈도로 5 - 10분마다 발생합니다. 전체 및 차등 백업을 사용하는 트랜잭션 로그 백업을 통해 데이터베이스를 호스트하는 동일한 서버로 특정 시점에 대해 데이터베이스를 복원할 수 있습니다. 사용자가 데이터베이스를 복원할 때 서비스에서는 전체, 차등, 트랜잭션 로그 백업 중 무엇을 복원해야 하는지 파악합니다.


이러한 백업을 사용하여 다음을 수행할 수 있습니다.

* 보존 기간 내 지정 시간으로 데이터베이스를 복원합니다. 이 작업으로 원본 데이터베이스와 같은 서버에 새 데이터베이스가 만들어집니다.
* 삭제된 시간 또는 보존 기간 내 임의 시간으로 삭제된 데이터베이스를 복원합니다. 삭제된 데이터베이스는 원래 데이터베이스가 생성되었던 동일한 서버에만 복원할 수 있습니다.
* 데이터베이스를 다른 지리적 지역으로 복원합니다. 이렇게 하면 서버 및 데이터베이스에 액세스할 수 없는 경우 지리적 재해로부터 복구할 수 있습니다. 그러면 전 세계 어디에서든 기존 서버에 새 데이터베이스가 만들어집니다. 
* Azure Recovery Services 자격 증명 모음에 저장된 특정 백업에서 데이터베이스를 복원합니다. 그러면 이전 버전의 데이터베이스를 복원하여 규정 준수 요청을 충족하고 이전 버전의 응용 프로그램을 실행할 수 있습니다. [장기 보존](sql-database-long-term-retention.md)을 참조하세요.
* 복원을 수행하려면 [백업에서 데이터베이스 복원](sql-database-recovery-using-backups.md)을 참조하세요.

> [!NOTE]
> Azure Storage에서 *복제* 라는 용어는 한 위치에서 다른 위치로 파일을 복사하는 것을 말합니다. SQL의 *데이터베이스 복제* 는 주 데이터베이스와 동기화된 다수의 보조 데이터베이스를 유지하는 것을 말합니다. 
> 

## <a name="how-much-backup-storage-is-included-at-no-cost"></a>무료 백업 저장소가 얼마나 포함되어 있습니까?
SQL Database는 추가 비용 없이 최대 프로비전된 데이터베이스 저장소의 최대 200%가 백업 저장소로 제공됩니다. 예를 들어, 프로비전된 DB의 크기가 250GB인 Standard DB 인스턴스가 있으면 추가 비용 없이 500GB의 백업 저장소를 갖습니다. 제공된 백업 저장소를 데이터베이스가 초과하면, Azure 지원 팀에 문의하여 보존 기간을 줄이도록 선택할 수 있습니다. 또 다른 옵션은 표준 RA-GRS(읽기 액세스 지리 중복 저장소) 요금이 청구되는 추가 백업 저장소에 대한 비용을 지불하는 것입니다. 

## <a name="how-often-do-backups-happen"></a>백업이 얼마나 자주 수행됩니까?
매주 전체 데이터베이스 백업이 수행되고, 일반적으로 몇 시간마다 차등 데이터베이스 백업이 수행되며, 일반적으로 5 - 10분마다 트랜잭션 로그 백업이 수행됩니다. 첫 번째 전체 백업은 데이터베이스를 만든 후에 즉시 예약됩니다. 일반적으로 30분 내에 완료되지만 데이터베이스의 크기가 상당히 큰 경우에는 더 오래 걸릴 수 있습니다. 예를 들어, 복원된 데이터베이스 또는 데이터베이스 사본에 대한 초기 백업은 더 오래 걸릴 수 있습니다. 첫 번째 전체 백업 후에 모든 향후 백업은 자동으로 예약되며 백그라운드에서 자동으로 관리됩니다. 모든 데이터베이스 백업의 정확한 타이밍은 전반적인 시스템 워크로드를 감안하여 SQL Database 서비스에 의해 결정됩니다. 

백업 저장소 지역에서 복제는 Azure Storage 복제 일정에 따라 발생합니다.

## <a name="how-long-do-you-keep-my-backups"></a>백업 보존 기간
각 SQL Database 백업의 보존 기간은 데이터베이스의 [서비스 계층](sql-database-service-tiers.md)에 따라 다릅니다. 데이터베이스의 보존 기간은 다음과 같습니다.


* 기본 서비스 계층은 7일입니다.
* 표준 서비스 계층은 35일입니다.
* 프리미엄 서비스 계층은 35일입니다.

데이터베이스를 표준 또는 프리미엄 서비스 계층에서 기본 계층으로 다운그레이드하면 백업이 7일 동안 저장됩니다. 7일보다 오래된 모든 기존 백업은 더 이상 사용할 수 없게 됩니다. 

데이터베이스를 기본 서비스 계층에서 표준 또는 프리미엄으로 업그레이드하면 SQL Database가 기존 백업을 35일 동안 보관합니다. 새 백업이 발생하면 새 백업을 35일 동안 보관합니다.

데이터베이스를 삭제하면 SQL Database는 온라인 데이터베이스에 하는 것과 동일한 방식으로 백업을 보관합니다. 예를 들어 보존 기간이 7일인 기본 데이터베이스를 삭제한다고 가정해 봅시다. 4일 된 백업은 앞으로 3일 동안 더 보관됩니다.

> [!IMPORTANT]
> SQL Database를 호스트하는 Azure SQL 서버를 삭제하면 해당 서버에 속하는 모든 데이터베이스도 삭제되어 복구할 수 없습니다. 삭제된 서버는 복원할 수 없습니다.
> 

## <a name="how-to-extend-the-backup-retention-period"></a>백업 보존 기간을 확장하는 방법
응용 프로그램에 더 긴 기간 동안 백업을 사용할 수 있어야 하는 경우 개별 데이터베이스(LTR 정책)에 대해 장기 백업 보존 정책을 구성하여 기본 제공 보존 기간을 연장할 수 있습니다. 기본 제공 보존 기간은 35일에서 최대 10년까지 연장할 수 있습니다. 자세한 내용은 [장기 보존](sql-database-long-term-retention.md)을 참조하세요.

Azure Portal 또는 API를 사용하여 LTR 정책을 데이터베이스에 추가했으면 매주 전체 데이터베이스 백업이 사용자 고유의 Azure Backup Service Vault로 자동으로 복사됩니다. TDE를 사용하여 암호화된 데이터베이스는 미사용 시 자동으로 암호화됩니다.  Services Vault는 타임 스탬프 또는 LTR 정책에 따라 만료된 백업을 자동으로 삭제합니다.  따라서 백업 일정을 관리할 필요가 없으며 이전 파일의 정리를 걱정할 필요가 없습니다. 복원 API는 자격 증명 모음이 SQL Database와 동일한 구독 내에만 있다면 자격 증명 모음에 저장된 백업을 지원합니다. Azure Portal 또는 PowerShell을 사용하여 이러한 백업에 액세스할 수 있습니다.

> [!TIP]
> 사용 방법 가이드는 [Configure and restore from Azure SQL Database long-term backup retention](sql-database-long-term-backup-retention-configure.md)(Azure SQL Database 장기 백업 보존 관리에서 구성 및 복원)을 참조하세요.
>

## <a name="are-backups-encrypted"></a>백업이 암호화되나요?

Azure SQL Database에 TDE를 사용할 때 백업이 암호화됩니다. 모든 새 Azure SQL Database는 기본적으로 사용한 TDE로 구성됩니다. TDE에 대한 자세한 내용은 [Azure SQL Database를 사용한 투명한 데이터 암호화](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)를 참조하세요.

## <a name="next-steps"></a>다음 단계

- 데이터베이스 백업은 실수로 손상되거나 삭제되지 않도록 데이터를 보호해 주기 때문에 비즈니스 연속성 및 재해 복구 전략의 필수적인 부분입니다. 다른 Azure SQL Database 비즈니스 연속성 솔루션에 대해 알아보려면 [비즈니스 연속성 개요](sql-database-business-continuity.md)를 참조하세요.
- Azure Portal을 사용하여 지정 시간으로 복원하려면 [Azure Portal을 사용하여 지정 시간으로 데이터베이스 복원](sql-database-recovery-using-backups.md)을 참조하세요.
- PowerShell을 사용하여 지정 시간으로 복원하려면 [PowerShell을 사용하여 지정 시간으로 데이터베이스 복원](scripts/sql-database-restore-database-powershell.md)을 참조하세요.
- Azure Portal을 사용하여 Azure Recovery Services에서 자동화된 백업을 장기 보존에서 구성, 관리 및 복원하려면 [Azure Portal을 사용하여 장기 백업 보존 관리](sql-database-long-term-backup-retention-configure.md)를 참조하세요.
- PowerShell을 사용하여 Azure Recovery Services에서 자동화된 백업을 장기 보존에서 구성, 관리 및 복원하려면 [PowerShell을 사용하여 장기 백업 보존 관리](sql-database-long-term-backup-retention-configure.md)를 참조하세요.
