---
title: sqlcmd와 연결
description: sqlcmd 명령줄 유틸리티를 사용하여 Synapse SQL 풀에 연결하고 쿼리합니다.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 02157ca0d32d2347e50cc84a5c52e9c47b0f33b5
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80745192"
---
# <a name="connect-to-synapse-sql-pool-with-sqlcmd"></a>SQLCMd를 사용하여 시냅스 SQL 풀에 연결

> [!div class="op_single_selector"]
>
> * [Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md)
> * [Azure 기계 학습](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)
> * [Visual Studio](sql-data-warehouse-query-visual-studio.md)
> * [Sqlcmd](sql-data-warehouse-get-started-connect-sqlcmd.md)
> * [SSMS](sql-data-warehouse-query-ssms.md)

SQL 풀에 연결하고 쿼리하려면 [sqlcmd][sqlcmd] 명령줄 유틸리티를 사용합니다.  

## <a name="1-connect"></a>1. 연결

[sqlcmd][sqlcmd]로 시작하려면 명령 프롬프트를 열고 **sqlcmd를** 입력한 다음 SQL 풀 데이터베이스에 대한 연결 문자열을 입력합니다. 연결 문자열에는 다음 매개 변수가 필요합니다.

* **서버(-S):**`<`서버 이름`>`.database.windows.net 형식의 서버
* **데이터베이스(-d):** 데이터베이스 이름입니다.
* **인용 식별자(-I) 사용:** SQL 풀 인스턴스에 연결하려면 quoted식별자를 사용하도록 설정해야 합니다.

SQL Server 인증을 사용하려면 사용자 이름/암호 매개 변수를 추가해야 합니다.

* **사용자(-U):**`<`사용자`>` 형태의 서버 사용자
* **암호(-P):** 사용자와 연결된 암호

예를 들어 연결 문자열은 다음과 같습니다.

```sql
C:\>sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I
```

Azure Active Directory 통합 인증을 사용하려면 Azure Active Directory 매개 변수를 추가해야 합니다.

* **Azure Active Directory 인증(-G):** 인증을 위해 Azure Active Directory를 사용합니다.

예를 들어 연결 문자열은 다음과 같습니다.

```sql
C:\>sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -G -I
```

> [!NOTE]
> Active Directory를 사용하여 인증하려면 [Azure Active Directory 인증을 사용하도록 설정](sql-data-warehouse-authentication.md) 해야 합니다.

## <a name="2-query"></a>2. 쿼리

연결 후, 인스턴스에 대해 지원되는 모든 TRANSACT-SQL 문을 실행할 수 있습니다.  이 예에서 쿼리는 대화형 모드로 전송됩니다.

```sql
C:\>sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I
1> SELECT name FROM sys.tables;
2> GO
3> QUIT
```

다음 예제에서는 -Q 옵션을 사용하거나 sqlcmd에 SQL을 파이핑하여 배치 모드에서 쿼리를 실행하는 방법을 보여 줍니다.

```sql
sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I -Q "SELECT name FROM sys.tables;"
```

```sql
"SELECT name FROM sys.tables;" | sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I > .\tables.out
```

## <a name="next-steps"></a>다음 단계

sqlcmd에서 사용할 수 있는 옵션에 대한 자세한 내용은 [sqlcmd 설명서를](/sql/tools/sqlcmd-utility?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)참조하십시오.
