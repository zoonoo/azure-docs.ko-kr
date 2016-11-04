---
title: SQL 데이터베이스로 마이그레이션하기 전에 SQL Server 데이터베이스 호환성 문제 해결 | Microsoft Docs
description: Microsoft Azure SQL 데이터베이스, 데이터베이스 마이그레이션, 호환성, SQL Azure 마이그레이션 마법사
services: sql-database
documentationcenter: ''
author: CarlRabeler
manager: jhubbard
editor: ''

ms.service: sql-database
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: sqldb-migrate
ms.date: 08/24/2016
ms.author: carlrab

---
# Azure SQL 데이터베이스로 마이그레이션하기 전에 SQL Azure 마이그레이션 마법사를 사용하여 SQL Server 데이터베이스 호환성 문제 해결
> [!div class="op_single_selector"]
> * [SQL Azure 마이그레이션 마법사](sql-database-cloud-migrate-fix-compatibility-issues.md) 사용
> * [SSDT](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md) 사용
> * [SSMS](sql-database-cloud-migrate-fix-compatibility-issues-ssms.md) 사용
> 
> 

이 문서에서는 Azure SQL 데이터베이스로 마이그레이션하기 전에 SQL Azure 마이그레이션 마법사를 사용하여 SQL Server 데이터베이스 호환성 문제를 감지하고 해결하는 방법을 알아봅니다.

## SQL Azure 마이그레이션 마법사 사용
[SQL Azure 마이그레이션 마법사](http://sqlazuremw.codeplex.com/) CodePlex 도구를 사용하여 호환되지 않는 원본 데이터베이스에서 T-SQL 스크립트를 생성합니다. 그러면 이 스크립트는 SQL 데이터베이스와 호환되도록 마법사에 의해 변환됩니다. 그런 후에는 Azure SQL 데이터베이스에 연결하여 스크립트를 실행합니다. 이 도구는 또한 추적 파일을 분석하여 호환성 문제를 확인합니다. 스크립트는 스키마만 사용하여 생성하거나 BCP 형식의 데이터를 포함할 수 있습니다. 단계별 참고 자료를 포함한 추가 설명서는 [SQL Azure 마이그레이션 마법사](http://sqlazuremw.codeplex.com/)의 CodePlex에서 사용할 수 있습니다.

 ![SAMW 마이그레이션 다이어그램](./media/sql-database-cloud-migrate/02SAMWDiagram.png)

> [!NOTE]
> 마법사에서 감지할 수 있는 모든 호환되지 않는 스키마를 기본 제공 변환으로 해결할 수 있는 것은 아닙니다. 처리할 수 없는 호환되지 않는 스크립트는 오류로 보고되고 생성된 스크립트에 주석이 삽입됩니다. 많은 오류가 감지될 경우 Visual Studio 또는 SQL Server Management Studio를 사용하여 SQL Server 마이그레이션 마법사로 해결할 수 없는 각 오류를 단계적으로 해결합니다.
> 
> 

## 다음 단계
* [SSDT 최신 버전](https://msdn.microsoft.com/library/mt204009.aspx)
* [SQL Server Management Studio 최신 버전](https://msdn.microsoft.com/library/mt238290.aspx)
* [호환되는 SQL Server 데이터베이스를 SQL 데이터베이스로 마이그레이션](sql-database-cloud-migrate.md#migrate-a-compatible-sql-server-database-to-sql-database)

## 추가 리소스
* [SQL 데이터베이스 V12](sql-database-v12-whats-new.md)
* [Transact-SQL의 부분적으로 지원되거나 지원되지 않는 기능](sql-database-transact-sql-information.md)
* [SQL Server Migration Assistant를 사용하여 SQL Server 이외의 데이터베이스 마이그레이션](http://blogs.msdn.com/b/ssma/)

<!---HONumber=AcomDC_0824_2016-->