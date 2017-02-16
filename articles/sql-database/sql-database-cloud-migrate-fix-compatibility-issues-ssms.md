---
title: "SSMS: Azure SQL Database 호환성 마이그레이션 문제 해결 | Microsoft Docs"
description: "이 문서에서는 Azure SQL Database로 마이그레이션하기 전에 SQL Server Management Studio를 사용하여 SQL Server 데이터베이스 호환성 문제를 해결하는 방법을 소개합니다."
keywords: "Microsoft Azure SQL 데이터베이스, 데이터베이스 마이그레이션, 호환성, SQL Azure 마이그레이션 마법사"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 5f7d3544-b07e-415a-a2ae-96e49bf5d756
ms.service: sql-database
ms.custom: migrate and move
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: sqldb-migrate
ms.date: 11/08/2016
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 2b55b6b4475abdbc1985d8ac370b3b612b77eb0e
ms.openlocfilehash: 841c4d644c6b096251e1ecefac17a8254b5ca712


---
# <a name="fix-sql-server-database-compatibility-issues-using-sql-server-management-studio-before-migration-to-sql-database"></a>SQL 데이터베이스로 마이그레이션하기 전에 SQL Server Management Studio를 사용하여 SQL Server 데이터베이스 호환성 문제 해결
> [!div class="op_single_selector"]
> * [SAMW](sql-database-cloud-migrate-fix-compatibility-issues.md) 사용
> * [SSDT](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md)
> * [SSMS](sql-database-cloud-migrate-fix-compatibility-issues-ssms.md)
>  

고급 사용자는 Azure SQL 데이터베이스로 마이그레이션하기 전에 SQL Server Management Studio를 사용하여 SQL Server 데이터베이스 호환성 문제를 해결할 수 있습니다.

> [!IMPORTANT]
> Microsoft Azure 및 SQL 데이터베이스에 대한 업데이트와 동기화 상태를 유지하려면 항상 최신 버전의 Management Studio를 사용하는 것이 좋습니다. [SQL Server Management Studio를 업데이트합니다](https://msdn.microsoft.com/library/mt238290.aspx).
> 
> 

## <a name="using-sql-server-management-studio"></a>SQL Server Management Studio 사용
SQL Server Management Studio를 사용하여 **ALTER DATABASE**등 다양한 Transact-SQL 명령을 사용할 때 발생하는 호환성 문제를 해결합니다. 이 방법은 주로 라이브 데이터베이스에서 Transact-SQL을 사용하는 데 익숙한 고급 사용자를 위한 방법입니다. 그렇지 않은 경우 SSDT를 사용하는 것이 좋습니다. 

## <a name="next-steps"></a>다음 단계
* [SSDT 최신 버전](https://msdn.microsoft.com/library/mt204009.aspx)
* [SQL Server Management Studio 최신 버전](https://msdn.microsoft.com/library/mt238290.aspx)
* [호환되는 SQL Server 데이터베이스를 SQL 데이터베이스로 마이그레이션](sql-database-cloud-migrate.md#migrate-a-compatible-sql-server-database-to-sql-database)

## <a name="additional-resources"></a>추가 리소스
* [SQL Database 기능](sql-database-features.md)
* [Transact-SQL의 부분적으로 지원되거나 지원되지 않는 기능](sql-database-transact-sql-information.md)
* [SQL Server Migration Assistant를 사용하여 SQL Server 이외의 데이터베이스 마이그레이션](http://blogs.msdn.com/b/ssma/)




<!--HONumber=Jan17_HO1-->


