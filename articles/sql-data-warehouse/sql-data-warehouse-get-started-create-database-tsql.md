---
title: "TSQL를 사용하여 SQL Data Warehouse 만들기 | Microsoft Docs"
description: "TSQL를 사용하여 SQL 데이터 웨어하우스를 만드는 방법을 알아봅니다."
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jhubbard
editor: 
tags: azure-sql-data-warehouse
ms.assetid: a4e2e68e-aa9c-4dd3-abb0-f7df997d237a
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: create
ms.date: 10/31/2016
ms.author: barbkess
translationtype: Human Translation
ms.sourcegitcommit: 5d3bcc3c1434b16279778573ccf3034f9ac28a4d
ms.openlocfilehash: 836d72e32e54ecef9691b55214766a1fc3ff9701
ms.lasthandoff: 12/07/2016


---
# <a name="create-a-sql-data-warehouse-database-by-using-transact-sql-tsql"></a>TRANSACT-SQL(TSQL)를 사용하여 SQL 데이터 웨어하우스 데이터베이스 만들기
> [!div class="op_single_selector"]
> * [Azure 포털](sql-data-warehouse-get-started-provision.md)
> * [TSQL](sql-data-warehouse-get-started-create-database-tsql.md)
> * [PowerShell](sql-data-warehouse-get-started-provision-powershell.md)
>
>

이 문서에서는 T-SQL을 사용하여 SQL 데이터 웨어하우스를 만드는 방법을 보여 줍니다.

## <a name="prerequisites"></a>필수 조건
시작하려면 다음이 필요합니다.

* **Azure 계정**: [Azure 무료 평가판][Azure Free Trial] 또는 [MSDN Azure 크레딧][MSDN Azure Credits]을 방문하여 계정을 만듭니다.
* **Azure SQL Server**: 자세한 내용은 [Azure Portal을 사용하여 Azure SQL Database 논리 서버 만들기][Create an Azure SQL Database logical server with the Azure Portal] 또는 [PowerShell을 사용하여 Azure SQL Database 논리 서버 만들기][Create an Azure SQL Database logical server with PowerShell]를 참조하세요.
* **리소스 그룹**: Azure SQL Server와 동일한 리소스 그룹을 사용하거나 [리소스 그룹을 만드는 방법][how to create a resource group]을 참조하세요.
* **T-SQL을 실행할 환경**: [Visual Studio][Installing Visual Studio and SSDT], [sqlcmd][sqlcmd] 또한 [SSMS][SSMS]을 사용하여 T-SQL을 실행할 수 있습니다.

> [!NOTE]
> SQL 데이터 웨어하우스를 만들면 새로운 유료 서비스가 발생할 수 있습니다.  가격 책정에 대한 자세한 내용은 [SQL Data Warehouse 가격 책정][SQL Data Warehouse pricing]을 참조하세요.
>
>

## <a name="create-a-database-with-visual-studio"></a>Visual Studio를 사용하여 데이터베이스 만들기
Visual Studio를 처음 접하는 경우 [Azure SQL Data Warehouse 쿼리(Visual Studio)][Query Azure SQL Data Warehouse (Visual Studio)] 문서를 참조하세요.  시작하려면 Visual Studio에서 SQL Server 개체 탐색기를 열고 SQL 데이터 웨어하우스 데이터베이스를 호스팅할 서버에 연결합니다.  연결한 후에는 **마스터** 데이터베이스에 대해 다음 SQL 명령을 실행하여 SQL 데이터 웨어하우스를 만들 수 있습니다.  이 명령은 서비스 목표 DW400이 있는 MySqlDwDb 데이터베이스를 만들고 데이터베이스가 최대 10TB까지 증대될 수 있게 허용합니다.

```sql
CREATE DATABASE MySqlDwDb COLLATE SQL_Latin1_General_CP1_CI_AS (EDITION='datawarehouse', SERVICE_OBJECTIVE = 'DW400', MAXSIZE= 10240 GB);
```

## <a name="create-a-database-with-sqlcmd"></a>sqlcmd로 데이터베이스 만들기
또는 명령 프롬프트에서 다음을 실행하여 sqlcmd로 동일한 명령을 실행할 수 있습니다.

```sql
sqlcmd -S <Server Name>.database.windows.net -I -U <User> -P <Password> -Q "CREATE DATABASE MySqlDwDb COLLATE SQL_Latin1_General_CP1_CI_AS (EDITION='datawarehouse', SERVICE_OBJECTIVE = 'DW400', MAXSIZE= 10240 GB)"
```

지정되지 않은 경우 기본 데이터 정렬은 COLLATE SQL_Latin1_General_CP1_CI_AS입니다.  `MAXSIZE` 는 250GB~240TB 사이가 될 수 있습니다.  `SERVICE_OBJECTIVE`는 DW100~DW2000 [DWU][DWU] 사이가 될 수 있습니다.  유효한 모든 값의 목록은 [데이터베이스 만들기][CREATE DATABASE]에 대한 MSDN 설명서를 참조하세요.  MAXSIZE와 SERVICE_OBJECTIVE는 모두 [ALTER DATABASE][ALTER DATABASE] T-SQL 명령으로 변경할 수 있습니다.  생성 후에 데이터베이스의 데이터 정렬을 변경할 수 없습니다.   SERVICE_OBJECTIVE를 변경하면 DWU의 변경으로 인해 서비스가 재시작되어 진행 중인 모든 쿼리가 취소될 수 있으므로 주의가 필요합니다.  MAXSIZE 변경은 간단한 메타데이터 작업이므로 서비스를 다시 시작하지 않습니다.

## <a name="next-steps"></a>다음 단계
SQL Data Warehouse에서 프로비전을 완료했으면 [샘플 데이터를 로드][load sample data]하거나 [개발][develop], [로드][load] 또는 [마이그레이션][migrate] 방법을 확인할 수 있습니다.

<!--Article references-->
[DWU]: ./sql-data-warehouse-overview-what-is.md
[how to create a SQL Data Warehouse from the Azure portal]: sql-data-warehouse-get-started-provision.md
[Query Azure SQL Data Warehouse (Visual Studio)]: sql-data-warehouse-query-visual-studio.md
[migrate]: sql-data-warehouse-overview-migrate.md
[develop]: sql-data-warehouse-overview-develop.md
[load]: sql-data-warehouse-overview-load.md
[load sample data]: sql-data-warehouse-load-sample-databases.md
[Create an Azure SQL database with the Azure Portal]: ../sql-database/sql-database-get-started.md
[Create an Azure SQL database with PowerShell]: ../sql-database/sql-database-create-and-configure-database-powershell
[how to create a resource group]: ../azure-resource-manager/resource-group-template-deploy-portal.md#create-resource-group
[Installing Visual Studio and SSDT]: sql-data-warehouse-install-visual-studio.md
[sqlcmd]: sql-data-warehouse-get-started-connect-sqlcmd.md

<!--MSDN references-->
[CREATE DATABASE]: https://msdn.microsoft.com/library/mt204021.aspx
[ALTER DATABASE]: https://msdn.microsoft.com/library/mt204042.aspx
[SSMS]: https://msdn.microsoft.com/library/mt238290.aspx

<!--Other Web references-->
[SQL Data Warehouse pricing]: https://azure.microsoft.com/pricing/details/sql-data-warehouse/
[Azure Free Trial]: https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F
[MSDN Azure Credits]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F

