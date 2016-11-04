---
title: Azure SQL 데이터베이스 복사 | Microsoft Docs
description: Azure SQL 데이터베이스 사본 만들기
services: sql-database
documentationcenter: ''
author: stevestein
manager: jhubbard
editor: ''

ms.service: sql-database
ms.devlang: NA
ms.date: 06/16/2016
ms.author: sstein
ms.workload: data-management
ms.topic: article
ms.tgt_pltfrm: NA

---
# <a name="copy-an-azure-sql-database"></a>Azure SQL 데이터베이스 복사
> [!div class="op_single_selector"]
> * [개요](sql-database-copy.md)
> * [Azure 포털](sql-database-copy-portal.md)
> * [PowerShell](sql-database-copy-powershell.md)
> * [T-SQL](sql-database-copy-transact-sql.md)
> 
> 

Azure [SQL 데이터베이스 자동화 백업](sql-database-automated-backups.md) 을 사용하여 SQL 데이터베이스의 복사본을 만들 수 있습니다. 복사 작업은 트랜잭션 로그의 끝부분을 복사한 다음 자동화 백업에 속하는 전체, 차등 및 트랜잭션 로그 백업을 사용하여 마지막 트랜잭션 로그 백업 시간을 기준으로 소스 데이터베이스와 트랜잭션이 일치하는 복사본을 만듭니다. 

동일한 서버 또는 다른 서버에 데이터베이스 복사본을 만들 수 있습니다. 데이터베이스 사본의 서버 계층과 성능 수준(가격 책정 계층)은 원본 데이터베이스와 동일합니다. 복사 완료 후 사본은 완전히 작동하는 독립 데이터베이스입니다. 로그인, 사용자 및 사용 권한은 독립적으로 관리됩니다. 

데이터베이스를 동일한 논리적 서버에 복사할 때 두 데이터베이스에서 동일한 로그인을 사용할 수 있습니다. 데이터베이스를 복사하는 데 사용하느 보안 주체는 새 데이터베이스의 데이터베이스 소유자(DBO)가 됩니다. 모든 데이터베이스 사용자, 권한 및 보안 식별자(SID)가 데이터베이스 사본에 복사됩니다. 

다른 논리 서버로 데이터베이스를 복사할 때 새 서버의 보안 주체가 새 데이터베이스의 데이터베이스 소유자가 됩니다. 포함된 사용자에 해당하는 데이터베이스 사용자를 복사된 데이터베이스에서 사용할 수 있습니다. 그러나 데이터베이스를 새 서버로 복사할 때 로그인을 기준으로 하는 사용자는 일반적으로 작동하지 않습니다. 그 이유는 로그인이 새 서버에 존재하지 않거나 존재하더라도 SID가 일치하지 않을 수 있기 때문입니다. 대상 서버에서 새 데이터베이스가 온라인 상태가 되면 [ALTER USER](https://msdn.microsoft.com/library/ms176060.aspx) 문을 사용하여 새 데이터베이스의 사용자를 대상 서버의 로그인과 다시 매핑합니다. 분리된 사용자를 확인하려면 [분리된 사용자 문제 해결](https://msdn.microsoft.com/library/ms175475.aspx)을 참조하세요. 

SQL 데이터베이스를 복사하려면 다음이 필요합니다.

* Azure 구독. Azure 구독이 필요할 경우 이 페이지 위쪽에서 **무료 평가판** 을 클릭하고 되돌아와 이 문서를 완료합니다.
* 복사할 SQL 데이터베이스. SQL 데이터베이스가 없는 경우 [첫 Azure SQL 데이터베이스 만들기](sql-database-get-started.md)문서의 단계에 따라 만듭니다.

## <a name="next-steps"></a>다음 단계
* Azure 포털을 사용하여 데이터베이스를 복사하려면 [Azure 포털을 사용하여 Azure SQL 데이터베이스 복사](sql-database-copy-portal.md) 를 참조하세요.
* PowerShell을 사용하여 데이터베이스를 복사하려면 [PowerShell을 사용하여 Azure SQL 데이터베이스 복사](sql-database-copy-powershell.md) 를 참조하세요.
* Transact-SQL을 사용하여 데이터베이스를 복사하려면 [T-SQL을 사용하여 Azure SQL 데이터베이스 복사](sql-database-copy-transact-sql.md) 를 참조하세요.
* 다른 논리 서버로 데이터베이스를 복사할 때 사용자 및 로그인을 관리하는 방법에 대한 자세한 내용은 [재해 복구 후에 Azure SQL 데이터베이스 보안을 관리하는 방법](sql-database-geo-replication-security-config.md) 을 참조하세요.

## <a name="additional-resources"></a>추가 리소스
* [로그인 관리](sql-database-manage-logins.md)
* [SQL Server Management Studio를 사용하여 SQL 데이터베이스에 연결하고 샘플 T-SQL 쿼리를 수행합니다.](sql-database-connect-query-ssms.md)
* [데이터베이스를 BACPAC로 내보내기](sql-database-export.md)
* [비즈니스 연속성 개요](sql-database-business-continuity.md)
* [SQL 데이터베이스 설명서](https://azure.microsoft.com/documentation/services/sql-database/)

<!--HONumber=Oct16_HO2-->


