---
title: "SQL 데이터베이스 자습서: 보안 시작"
description: "데이터베이스에 액세스하고 관리하기 위해 사용자 계정을 만드는 방법에 대해 알아봅니다."
keywords: 
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 67797b09-f5c3-4ec2-8494-fe18883edf7f
ms.service: sql-database
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 08/17/2016
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: a3bd8ac5466ae67df0a1865509c8fb897d1374ec


---
# <a name="sql-database-tutorial-create-sql-database-user-accounts-to-access-and-manage-a-database"></a>SQL 데이터베이스 자습서: 데이터베이스에 액세스하고 관리하도록 SQL 데이터베이스 사용자 계정 만들기
> [!div class="op_single_selector"]
> * [시작 자습서](sql-database-get-started-security.md)
> * [액세스 권한 부여](sql-database-manage-logins.md)
> 
> 

이 자습서에서는 SSMS(SQL Server Management Studio)를 사용하는 방법을 알아봅니다.

* 서버 수준 보안 주체 로그인을 사용하여 SQL 데이터베이스에 로그인
* SQL 데이터베이스 사용자 계정 만들기
* SQL Database 사용자 [db_owner 권한 부여](https://msdn.microsoft.com/library/ms189121.aspx#Anchor_0)
* 서버 수준의 주체가 아닌 사용자 계정으로 SQL 데이터베이스에 연결

[!INCLUDE [Login](../../includes/azure-getting-started-portal-login.md)]

[!INCLUDE [Create SQL Database logical server](../../includes/sql-database-sql-server-management-studio-connect-server-principal.md)]

[!INCLUDE [Create SQL Database database](../../includes/sql-database-create-new-database-user.md)]

[!INCLUDE [Create SQL Database database](../../includes/sql-database-grant-database-user-dbo-permissions.md)]

[!INCLUDE [Create SQL Database database](../../includes/sql-database-sql-server-management-studio-connect-user.md)]

## <a name="next-steps"></a>다음 단계
이제 이 SQL 데이터베이스 자습서를 완료하고 사용자 계정을 만들고 사용자 계정 dbo 권한을 부여했으므로 [SQL 데이터베이스 보안](sql-database-manage-logins.md)에 대해 자세히 알아볼 준비가 되었습니다.




<!--HONumber=Nov16_HO2-->


