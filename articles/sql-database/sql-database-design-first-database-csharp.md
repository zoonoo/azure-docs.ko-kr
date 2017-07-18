---
title: "첫 번째 Azure SQL Database 설계 - C# | Microsoft Docs"
description: "첫 번째 Azure SQL Database를 설계하고 ADO.NET을 사용하여 C# 프로그램과 연결하는 방법을 알아봅니다."
services: sql-database
documentationcenter: 
author: MightyPen
manager: craigg-msft
editor: CarlRabeler
tags: 
ms.assetid: 
ms.service: sql-database
ms.custom: develop databases
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: 
ms.date: 06/20/2017
ms.author: genemi
ms.translationtype: Human Translation
ms.sourcegitcommit: 857267f46f6a2d545fc402ebf3a12f21c62ecd21
ms.openlocfilehash: 069656b495929d66f6a47b38141f56b6bf0ac92f
ms.contentlocale: ko-kr
ms.lasthandoff: 06/28/2017

---
# <a name="design-an-azure-sql-database-and-connect-with-cx23-and-adonet"></a>Azure SQL Database 설계 및 C#과 ADO.NET에 연결

Azure SQL Database는 Microsoft 클라우드("Azure")의 관계형 DBaaS(Database-As-A-Service)입니다. 이 자습서에서는 Azure Portal 및 ADO.NET을 사용하여 다음과 같은 작업을 수행하는 방법에 대해 알아봅니다. 

> [!div class="checklist"]
> * Azure Portal에서 데이터베이스 만들기
> * Azure Portal에서 서버 수준 방화벽 규칙 설정
> * ADO.NET을 사용하여 데이터베이스에 연결
> * ADO.NET을 사용하여 테이블 만들기
> * ADO.NET을 사용하여 데이터 삽입 
> * 해당 ADO.NET 데이터 쿼리

Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

<!-- The following included .md, sql-database-tutorial-portal-create-firewall-connection-1.md, is long.
And it starts with a ## H2.
-->

[!INCLUDE [sql-database-tutorial-portal-create-firewall-connection-1](../../includes/sql-database-tutorial-portal-create-firewall-connection-1.md)]


<!-- The following included .md, sql-database-csharp-adonet-create-query-2.md, is long.
And it starts with a ## H2.
-->

[!INCLUDE [sql-database-csharp-adonet-create-query-2](../../includes/sql-database-csharp-adonet-create-query-2.md)]


## <a name="next-steps"></a>다음 단계

사용할 수 있는 단계는 다음과 같습니다.

- [C# 프로그램에서 SQL 쿼리에 LINQ 사용](https://msdn.microsoft.com/library/bb425822.aspx)
- [SQL Server 데이터베이스를 Azure SQL Database로 마이그레이션](sql-database-migrate-your-sql-server-database.md)

