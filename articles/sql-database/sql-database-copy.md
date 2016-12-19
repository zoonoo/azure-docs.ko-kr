---
title: "Azure SQL Database 복사 | Microsoft Docs"
description: "Azure SQL 데이터베이스 사본 만들기"
services: sql-database
documentationcenter: 
author: anosov1960
manager: jhubbard
editor: 
ms.assetid: 5aaf6bcd-3839-49b5-8c77-cbdf786e359b
ms.service: sql-database
ms.devlang: NA
ms.date: 10/24/2016
ms.author: sstein; sashan
ms.workload: data-management
ms.topic: article
ms.tgt_pltfrm: NA
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 3e6152f6539962653a582ba4921af82e4447fd76


---
# <a name="copy-an-azure-sql-database"></a>Azure SQL 데이터베이스 복사
> [!div class="op_single_selector"]
> * [개요](sql-database-copy.md)
> * [쉬운 테이블](sql-database-copy-portal.md)
> * [PowerShell](sql-database-copy-powershell.md)
> * [T-SQL](sql-database-copy-transact-sql.md)
> 
> 

Azure [SQL 데이터베이스 자동화 백업](sql-database-automated-backups.md) 을 사용하여 SQL 데이터베이스의 복사본을 만들 수 있습니다. 데이터베이스 복사에는 지역에서 복제 기능과 동일한 기술이 사용됩니다. 그러나 지역에서 복제와 달리 시드 단계가 완료되면 복제 링크가 종료됩니다. 따라서 데이터베이스 복사는 복사 요청 당시의 원본 데이터베이스의 스냅숏입니다.  
동일한 서버 또는 다른 서버에 데이터베이스 복사본을 만들 수 있습니다. 데이터베이스 복사의 서버 계층과 성능 수준(가격 책정 계층)은 기본적으로 원본 데이터베이스와 동일합니다. API를 사용하는 경우 동일한 서비스 계층(버전) 내의 다양한 성능 수준을 선택할 수 있습니다. 복사 완료 후 사본은 완전히 작동하는 독립 데이터베이스입니다. 이 시점에서 모든 버전으로 업그레이드하거나 다운그레이드할 수 있습니다. 로그인, 사용자 및 사용 권한은 독립적으로 관리됩니다.  

데이터베이스를 동일한 논리적 서버에 복사할 때 두 데이터베이스에서 동일한 로그인을 사용할 수 있습니다. 데이터베이스를 복사하는 데 사용하느 보안 주체는 새 데이터베이스의 데이터베이스 소유자(DBO)가 됩니다. 모든 데이터베이스 사용자, 권한 및 보안 식별자(SID)가 데이터베이스 사본에 복사됩니다.  

다른 논리 서버로 데이터베이스를 복사할 때 새 서버의 보안 주체가 새 데이터베이스의 데이터베이스 소유자가 됩니다. 데이터 액세스에 [포함된 데이터베이스 사용자](sql-database-manage-logins.md)를 사용하는 경우 복사가 완료된 후 동일한 자격 증명으로 액세스할 수 있도록 주 데이터베이스와 보조 데이터베이스에서 항상 동일한 사용자 자격 증명을 사용해야 합니다. [Azure Active Directory](../active-directory/active-directory-whatis.md)를 사용하는 경우 복사본에서 자격 증명을 관리할 필요가 없습니다. 그러나 데이터베이스를 새 서버로 복사할 때 새 서버에 로그인이 존재하지 않으므로 일반적으로 로그인 기반 액세스가 작동하지 않습니다. 다른 논리 서버로 데이터베이스를 복사할 때 로그인을 관리하는 방법에 대한 자세한 내용은 [재해 복구 후에 Azure SQL Database 보안을 관리하는 방법](sql-database-geo-replication-security-config.md) 을 참조하세요. 

SQL Database를 복사하려면 다음이 필요합니다.

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




<!--HONumber=Nov16_HO3-->


