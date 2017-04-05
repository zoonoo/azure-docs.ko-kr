---
title: "Azure SQL Database를 BACPAC 파일로 내보내기 | Microsoft Docs"
description: "Azure Portal을 사용하여 Azure SQL Database를 BACPAC 파일로 내보내기"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 41d63a97-37db-4e40-b652-77c2fd1c09b7
ms.service: sql-database
ms.custom: migrate and move
ms.devlang: NA
ms.date: 02/07/2017
ms.author: carlrab
ms.workload: data-management
ms.topic: article
ms.tgt_pltfrm: NA
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: ff24360696c54091ab93e7e1c8f28d95c226bc0a
ms.lasthandoff: 03/29/2017


---
# <a name="export-an-azure-sql-database-or-a-sql-server-database-to-a-bacpac-file"></a>Azure SQL Database 또는 SQL Server 데이터베이스를 BACPAC 파일로 내보내기

이 문서에서는 Azure SQL Database 또는 SQL Server 데이터베이스를 BACPAC 파일로 내보내는 방법을 설명합니다. 

> [!IMPORTANT]
> Azure SQL Database 자동화된 내보내기는 현재 미리 보기이며 및 2017년 3월 1일에 사용이 중지됩니다. 2016년 12월 1일을 시작으로 모든 SQL Database에서 자동화된 내보내기를 더는 구성할 수 없습니다. 기존의 모든 자동화된 내보내기 작업은 2017년 3월 1일까지 계속 수행됩니다. 2016년 12월 1일 후에는 [장기 백업 보존](sql-database-long-term-retention.md) 또는 [Azure Automation](../automation/automation-intro.md)을 사용하여 선택한 일정에 따라 주기적으로 PowerShell을 사용해 주기적으로 SQL Database를 보관할 수 있습니다. 샘플 스크립트의 경우, [GitHub에서 샘플 스크립트](https://github.com/Microsoft/sql-server-samples/tree/master/samples/manage/azure-automation-automated-export)를 다운로드할 수 있습니다. 
>

## <a name="overview"></a>개요

다른 플랫폼에 보관하거나 이동하기 위해 데이터베이스를 내보내야 할 경우 데이터베이스 스키마 및 데이터를 BACPAC 파일로 내보낼 수 있습니다. BACPAC 파일은 BACPAC의 확장명을 가진 단순한 ZIP 파일입니다. BACPAC 파일은 나중에 Azure Blob 저장소 또는 온-프레미스 저장소의 로컬 저장소에 저장할 수 있으며 나중에 Azure SQL 데이터베이스 또는 SQL Server 온-프레미스 설치로 다시 가져올 수 있습니다. 

* [Azure Portal](sql-database-export-portal.md), [PowerShell](sql-database-export-powershell.md), [SQLPackage](sql-database-export-sqlpackage.md) 또는 [SQL Server Management Studio](sql-database-export-ssms.md)를 사용하여 Azure SQL Database 파일을 내보낼 수 있습니다.
* [PowerShell](sql-database-export-powershell.md), [SQLPackage](sql-database-export-sqlpackage.md) 또는 [SQL Server Management Studio](sql-database-export-ssms.md)를 사용하여 SQL Server 데이터베이스를 내보낼 수 있습니다.

> [!IMPORTANT]
> 마이그레이션에 대한 사전 준비로 SQL Server에서 Azure SQL Database로 내보내는 경우 [Azure SQL Database에 SQL Server 데이터베이스 마이그레이션](sql-database-cloud-migrate.md)을 참조하세요.
> 

## <a name="considerations"></a>고려 사항

* 내보내기 작업에서 트랜잭션이 일치하도록 내보내기 중에나 Azure SQL Database의 [트랜잭션 일치 복사본](sql-database-copy.md)에서 내보내는 중에는 쓰기 활동이 발생하지 않도록 해야 합니다.
* Blob Storage로 내보내는 경우 BACPAC 파일의 최대 크기는 200GB입니다. 더 큰 BACPAC 파일을 보관하려면 로컬 저장소로 내보냅니다.
* 이 문서에서 설명하는 방법을 사용하여 Azure Premium Storage에서 BACPAC 파일을 내보낼 수는 없습니다.
* Azure SQL Database에서 내보내기 작업이 20시간을 초과하면 취소될 수 있습니다. 내보내는 중에 성능을 향상시키기 위해 다음을 수행할 수 있습니다.
  * 서비스 수준을 일시적으로 높이기
  * 내보내기 중에 모든 읽기 및 쓰기 작업 중단
  * 모든 대형 테이블에 null이 아닌 값의 [클러스터형 인덱스](https://msdn.microsoft.com/library/ms190457.aspx) 를 사용합니다. 클러스터형 인덱스가 없는 경우 6~12시간 이상 소요되면 내보내기에 실패할 수 있습니다. 전체 테이블 내보내기를 시도하려면 내보내기 서비스에서 테이블 스캔을 완료해야 하기 때문입니다. 테이블이 내보내기에 최적화되었는지 확인하는 좋은 방법은 **DBCC SHOW_STATISTICS**를 실행하고 *RANGE_HI_KEY*가 null이 아닌지와 해당 값이 잘 배포되어 있는지 검토하는 것입니다. 자세한 내용은 [DBCC SHOW_STATISTICS](https://msdn.microsoft.com/library/ms174384.aspx)를 참조하세요.

> [!NOTE]
> BACPAC는 백업에 사용되는 목적이 아니며 작업을 복원합니다. Azure SQL 데이터베이스에서는 모든 사용자 데이터베이스의 백업이 자동으로 생성됩니다. 자세한 내용은 [비즈니스 연속성 개요](sql-database-business-continuity.md) 및 [SQL Database 백업](sql-database-automated-backups.md)을 참조하세요.  
> 


## <a name="next-steps"></a>다음 단계

* 전체 SQL Server 데이터베이스 마이그레이션 프로세스에 대한 설명은 [Azure SQL Database에 SQL Server 데이터베이스 마이그레이션](sql-database-cloud-migrate.md)을 참조하세요.
* Azure 내에서 데이터베이스 복사에 대한 개요를 보려면 [Azure SQL Database 복사](sql-database-copy.md)를 참조하세요.
* [Azure Portal](sql-database-copy-portal.md), [PowerShell](scripts/sql-database-copy-database-to-new-server-powershell.md) 또는 [Transact-SQL](sql-database-copy-transact-sql.md)을 사용하여 Azure 내에서 Azure SQL Database를 복사할 수 있습니다. 

