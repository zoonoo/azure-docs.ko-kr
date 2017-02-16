---
title: "SQL Database 연결 - SQL Server Management Studio | Microsoft Docs"
description: "SQL Server Management Studio(SSMS)를 사용하여 SQL 데이터베이스에 연결하는 방법을 알아봅니다. 그런 다음 Transact-SQL(T-SQL)를 사용하여 샘플 쿼리를 실행합니다."
metacanonical: 
keywords: "SQL 데이터베이스에 연결, SQL Server Management Studio"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 7cd2a114-c13c-4ace-9088-97bd9d68de12
ms.service: sql-database
ms.custom: development
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/22/2016
ms.author: sstein;carlrab
translationtype: Human Translation
ms.sourcegitcommit: 2a85b3dc1078bad9e5e2fc0ce0bec7e994b29150
ms.openlocfilehash: a6b147521525fad343376db0454f786a77b55c42


---
# <a name="connect-to-sql-database-with-sql-server-management-studio-and-execute-a-sample-t-sql-query"></a>SQL Server Management Studio를 사용하여 SQL 데이터베이스에 연결하고 샘플 T-SQL 쿼리를 실행합니다.
> [!div class="op_single_selector"]
> * [Visual Studio](sql-database-connect-query.md)
> * [SSMS](sql-database-connect-query-ssms.md)
> * [Excel](sql-database-connect-excel.md)
> 

이 문서에서는 SQL Server Management Studio(SSMS)를 사용하여 Azure SQL 데이터베이스에 연결하는 방법을 보여 줍니다. 성공적으로 연결되면 데이터베이스와의 통신을 확인하는 간단한 T-SQL(Transact-SQL) 쿼리를 실행합니다.

[!INCLUDE [SSMS Install](../../includes/sql-server-management-studio-install.md)]

1. 아직 연결하지 않은 경우 [SQL Server Management Studio 다운로드](https://msdn.microsoft.com/library/mt238290.aspx)에서 SSMS 최신 버전을 다운로드하여 설치합니다. 최신 상태로 유지하기 위해 최신 버전의 SSMS에서는 새 버전을 다운로드할 수 있는 경우 메시지를 표시합니다.

2. 설치한 후에 Windows 검색 상자에서 **Microsoft SQL Server Management Studio**를 입력하고 **Enter** 키를 클릭하여 SSMS를 엽니다.

    ![SQL Server Management Studio](./media/sql-database-get-started/ssms.png)
3. [서버에 연결] 대화 상자에서 SQL Server 인증을 사용하여 SQL 서버에 연결하는 데 필요한 정보를 입력합니다.

    ![서버 연결](./media/sql-database-get-started/connect-to-server.png)
4. **Connect**를 클릭합니다.

    ![연결된 서버](./media/sql-database-get-started/connected-to-server.png)
5. 개체 Explorer에서 **데이터베이스**를 확장하고 해당 데이터베이스에서 개체가 보이도록 모든 데이터베이스를 확장합니다.

    ![SSMS를 통한 새 샘플 데이터베이스 개체](./media/sql-database-get-started/new-sample-db-objects-ssms.png)
6. 이 데이터베이스를 마우스 오른쪽 단추로 클릭한 다음 **새 쿼리**를 클릭합니다.

    ![SSMS를 통한 새 샘플 데이터베이스 쿼리](./media/sql-database-get-started/new-sample-db-query-ssms.png)
7. 쿼리 창에서 다음 쿼리를 입력합니다.

   ```select * from sys.objects```
   
8.  도구 모음에서 **실행**을 클릭하여 샘플 데이터베이스의 모든 시스템 개체 목록을 반환합니다.

    ![SSMS를 통한 새 샘플 데이터베이스 쿼리 시스템 개체](./media/sql-database-get-started/new-sample-db-query-objects-ssms.png)

> [!Tip]
> 자습서에 대해서는 [SQL Database 자습서: 서버, 서버 수준 방화벽 규칙, 샘플 데이터베이스, 데이터베이스 수준 방화벽 규칙 및 SQL Server 연결 만들기](sql-database-get-started.md)를 참조하세요.    
>

## <a name="next-steps"></a>다음 단계

- T-SQL 문을 사용하여 SQL Server를 통해 할 수 있는 것과 동일한 방식으로 Azure에서 데이터베이스를 만들고 관리할 수 있습니다. SQL server에서 T-SQL을 사용하는 것에 익숙하다면 차이점 요약에 대해 [Azure SQL 데이터베이스 Transact-SQL 정보](sql-database-transact-sql-information.md) 를 참조하세요.
- T-SQL을 처음 접하는 경우 [자습서: Transact-SQL 문 쓰기](https://msdn.microsoft.com/library/ms365303.aspx) 및 [Transact-SQL 참조(데이터베이스 엔진)](https://msdn.microsoft.com/library/bb510741.aspx)를 참조하세요.
- 데이터베이스 사용자 및 데이터베이스 사용자 관리자 만들기를 시작하려면 [Azure SQL 데이터베이스 보안 시작](sql-database-control-access-sql-authentication-get-started.md)
- SSMS에 대한 자세한 내용은 [SQL Server Management Studio 사용](https://msdn.microsoft.com/library/ms174173.aspx)을 참조하세요.




<!--HONumber=Jan17_HO3-->


