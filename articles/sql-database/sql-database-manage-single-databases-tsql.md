---
title: "T-SQL: 단일 Azure SQL Database 만들기 및 관리 | Microsoft Docs"
description: "Azure Portal을 사용하여 Azure SQL Database를 만들고 관리하는 방법에 대한 빠른 참조입니다."
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: single databases
ms.devlang: NA
ms.workload: data-management
ms.topic: article
ms.tgt_pltfrm: NA
ms.date: 02/06/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 38c53d6f1a2635afbba199e6f0a2adb23abb6a3a
ms.openlocfilehash: fcf8326edc2ff84d274f7c4dadeae1fbe2595349


---
# <a name="create-and-manage-single-azure-sql-databases-with-transact-sql"></a>Transact-SQL을 사용하여 단일 Azure SQL Database 만들기 및 관리

[Azure Portal](https://portal.azure.com/), PowerShell, Transact-SQL, REST API 또는 C#을 사용하여 단일 Azure SQL Database를 만들고 관리할 수 있습니다. 이 항목은 Azure Portal 사용에 대해 설명합니다. PowerShell의 경우 [Powershell을 사용하여 단일 데이터베이스 만들기 및 관리](sql-database-manage-single-databases-powershell.md)를 참조하세요. Transact-SQL의 경우 [Transact-SQL을 사용하여 단일 데이터베이스 만들기 및 관리](sql-database-manage-single-databases-tsql.md)를 참조하세요. 

## <a name="create-an-azure-sql-database-using-transact-sql-in-sql-server-management-studio"></a>SQL Server Management Studio의 Transact-SQL을 사용하여 Azure SQL Database 만들기

SQL Server Management Studio에서 Transact-SQL을 사용하여 SSQL Database를 만들려면 다음을 수행합니다.

1. SQL Server Management Studio에서는 서버 수준 보안 주체 로그인 또는 **dbmanager** 역할의 구성원인 로그인을 사용하여 Azure 데이터베이스 서버에 연결합니다. 로그인에 대한 자세한 내용은 [로그인 관리](sql-database-manage-logins.md)를 참조하세요.
2. 개체 탐색기에서 데이터베이스 노드를 열고 **시스템 데이터베이스** 폴더를 확장한 후 **master**를 마우스 오른쪽 단추로 클릭한 다음 **새 쿼리**를 클릭합니다.
3. 데이터베이스를 만들려면 **CREATE DATABASE** 문을 사용하세요. 자세한 내용은 [CREATE DATABASE(SQL 데이터베이스)](https://msdn.microsoft.com/library/dn268335.aspx)를 참조하세요. 다음 문은 **myTestDB** 라는 데이터베이스를 만들고 기본 최대 크기가 250GB인 Standard S0 Edition 데이터베이스로 지정합니다.
  
      CREATE DATABASE myTestDB    (EDITION='Standard',     SERVICE_OBJECTIVE='S0');

4. **실행** 을 클릭하여 쿼리를 실행합니다.
5. 개체 탐색기에서 데이터베이스 노드를 마우스 오른쪽 단추로 클릭하고 **새로 고침**을 클릭하여 개체 탐색기에서 새 데이터베이스를 봅니다. 


## <a name="change-the-service-tier-and-performance-level-of-a-single-database"></a>단일 데이터베이스의 서비스 계층 및 성능 수준 변경
[Transact-SQL(T-SQL)](https://msdn.microsoft.com/library/mt574871.aspx)을 사용하여 데이터베이스 최대 크기를 변경합니다.

> [!TIP]
> Transact-SQL을 사용하여 데이터베이스를 만드는 방법에 대한 자습서를 보려면 [데이터베이스 만들기 - Azure Portal](sql-database-get-started.md)을 참조하세요.
>

## <a name="next-steps"></a>다음 단계
* 관리 도구에 대한 개요는 [관리 도구 개요](sql-database-manage-overview.md)를 참조하세요.
* Azure Portal을 사용하여 관리 작업을 수행하는 방법을 보려면 [Azure Portal을 사용하여 Azure SQL Database 관리](sql-database-manage-portal.md)를 참조하세요.
* PowerShell을 사용하여 관리 작업을 수행하는 방법을 보려면 [PowerShell을 사용하여 Azure SQL Database 관리](sql-database-manage-powershell.md)를 참조하세요.
* SQL Server Management Studio를 사용하여 관리 작업을 수행하는 방법을 보려면 [SQL Server Management Studio](sql-database-manage-azure-ssms.md)를 참조하세요.
* SQL Database 서비스에 대한 정보는 [SQL Database 정의](sql-database-technical-overview.md)를 참조하세요. 
* Azure 데이터베이스 서버 및 데이터베이스 기능에 대한 자세한 내용은 [기능](sql-database-features.md)을 참조하세요.



<!--HONumber=Feb17_HO2-->


