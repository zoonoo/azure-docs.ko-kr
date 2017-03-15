---
title: "Azure Portal: 단일 Azure SQL Database 만들기 및 관리 | Microsoft Docs"
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
ms.sourcegitcommit: 5e01db2676e3515f41bf98d23595e6509c0d6805
ms.openlocfilehash: c5f50213be9cd20c82acf8dd94463e7dce0a0195
ms.lasthandoff: 02/27/2017


---
# <a name="create-and-manage-single-azure-sql-databases-with-the-azure-portal"></a>Azure Portal을 사용하여 단일 Azure SQL Database 만들기 및 관리

[Azure Portal](https://portal.azure.com/), PowerShell, Transact-SQL, REST API 또는 C#을 사용하여 단일 Azure SQL Database를 만들고 관리할 수 있습니다. 이 항목은 Azure Portal 사용에 대해 설명합니다. PowerShell의 경우 [Powershell을 사용하여 단일 데이터베이스 만들기 및 관리](sql-database-manage-single-databases-powershell.md)를 참조하세요. Transact-SQL의 경우 [Transact-SQL을 사용하여 단일 데이터베이스 만들기 및 관리](sql-database-manage-single-databases-tsql.md)를 참조하세요. 

## <a name="create-a-single-azure-sql-database-with-the-azure-portal"></a>Azure Portal을 사용하여 단일 Azure SQL Database 만들기

1. [Azure Portal](https://portal.azure.com/)에서 **SQL Database** 블레이드를 엽니다. 

    ![SQL 데이터베이스](./media/sql-database-get-started/sql-databases.png)
2. SQL 데이터베이스 블레이드에서 **추가**를 클릭합니다.

    ![SQL 데이터베이스 추가](./media/sql-database-get-started/add-sql-database.png)

> [!TIP]
> Azure Portal을 사용하여 데이터베이스를 만드는 방법에 대한 자습서를 보려면 [데이터베이스 만들기 - Azure Portal](sql-database-get-started.md)을 참조하세요.
>    

## <a name="view-and-update-sql-database-settings-using-the-azure-portal"></a>Azure Portal을 사용하여 SQL Database 설정 보기 및 업데이트

1. [Azure Portal](https://portal.azure.com/)에서 **SQL Database** 블레이드를 엽니다. 

    ![SQL 데이터베이스](./media/sql-database-get-started/sql-databases.png)

2. 작업하려는 데이터베이스를 클릭한 다음 SQL Database 블레이드에서 원하는 설정을 클릭합니다.

    ![새 샘플 데이터베이스 블레이드](./media/sql-database-get-started/new-sample-db-blade.png)

## <a name="change-the-service-tier-and-performance-level-of-a-single-database"></a>단일 데이터베이스의 서비스 계층 및 성능 수준 변경

규모 확장 또는 축소할 데이터베이스에 대한 SQL 데이터베이스 블레이드를 엽니다.

1. [Azure Portal](https://portal.azure.com)에서 **추가 서비스** > **SQL 데이터베이스**를 클릭합니다.
2. 변경할 데이터베이스를 클릭합니다.
3. **SQL Database** 블레이드에서 **가격 책정 계층(배율 DTU)**을 클릭합니다.
   
   ![가격 책정 계층](./media/sql-database-manage-single-database-portal/new-tier.png)

4. 새 계층을 선택하고 **선택**을 클릭합니다.
   
   **선택**을 클릭하면 가격 책정 계층을 변경하기 위한 규모 요청을 제출합니다. 데이터베이스 크기에 따라 가져오기 작업의 규모를 설정하는 데 다소 시간이 걸릴 수 있습니다([서비스 계층](sql-database-service-tiers.md) 참조).
   
   > [!NOTE]
   > 데이터베이스 가격 책정 계층을 변경하는 경우 최대 데이터베이스 크기는 변경되지 않습니다. 데이터베이스 최대 크기를 변경하려면 [Transact-SQL(T-SQL)](https://msdn.microsoft.com/library/mt574871.aspx) 또는 [PowerShell](https://msdn.microsoft.com/library/mt619433.aspx)을 사용하세요.
   >  
   
   ![가격 책정 계층 선택](./media/sql-database-manage-single-database-portal/choose-tier.png)
5. 오른쪽 위에 있는 알림 아이콘(벨)을 클릭합니다.
   
   ![알림](./media/sql-database-manage-single-database-portal/scale-notification.png)
   
6. 알림 텍스트를 클릭하여 요청의 상태를 볼 수 있는 세부 정보 창을 엽니다.

## <a name="next-steps"></a>다음 단계
* 관리 도구에 대한 개요는 [관리 도구 개요](sql-database-manage-overview.md)를 참조하세요.
* Azure Portal을 사용하여 관리 작업을 수행하는 방법을 보려면 [Azure Portal을 사용하여 Azure SQL Database 관리](sql-database-manage-portal.md)를 참조하세요.
* PowerShell을 사용하여 관리 작업을 수행하는 방법을 보려면 [PowerShell을 사용하여 Azure SQL Database 관리](sql-database-manage-powershell.md)를 참조하세요.
* SQL Server Management Studio를 사용하여 관리 작업을 수행하는 방법을 보려면 [SQL Server Management Studio](sql-database-manage-azure-ssms.md)를 참조하세요.
* SQL Database 서비스에 대한 정보는 [SQL Database 정의](sql-database-technical-overview.md)를 참조하세요. 
* Azure 데이터베이스 서버 및 데이터베이스 기능에 대한 자세한 내용은 [기능](sql-database-features.md)을 참조하세요.

