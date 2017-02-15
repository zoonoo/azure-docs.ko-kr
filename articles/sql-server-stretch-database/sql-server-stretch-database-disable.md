---
title: "Stretch Database를 사용하지 않고 원격 데이터 다시 가져오기 | Microsoft Docs"
description: "테이블에 대해 Stretch Database를 사용하지 않도록 설정하고 필요에 따라 원격 데이터를 다시 가져오는 방법을 알아봅니다."
services: sql-server-stretch-database
documentationcenter: 
author: douglaslMS
manager: jhubbard
editor: 
ms.assetid: 08291748-0dfd-4a7d-a6a4-a5618e9c248d
ms.service: sql-server-stretch-database
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/05/2016
ms.author: douglasl
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: a140e89058f6a246e80b71248b82d0f6767337cb


---
# <a name="disable-stretch-database-and-bring-back-remote-data"></a>Stretch Database를 사용하지 않고 원격 데이터 다시 가져오기
테이블에 대해 Stretch Database를 사용하지 않도록 설정하려면 SQL Server Management Studio에서 해당 테이블에 대해 **스트레치** 를 선택합니다. 그런 후 다음 옵션 중 하나를 선택합니다.

* **사용 안 함 | Azure에서 데이터 다시 가져오기**. Azure에서 테이블의 원격 데이터를 SQL Server에 다시 복사한 다음 테이블에 대해 Stretch Database를 사용하지 않도록 설정합니다. 이 작업은 데이터 전송 비용이 소요되며, 취소할 수 없습니다.
* **사용 안 함 | Azure에 데이터 남겨 두기**. 테이블에 대해 Stretch Database를 사용하지 않도록 설정합니다.  Azure에서 테이블에 대한 원격 데이터를 중단합니다.

Transact\-SQL을 사용하여 테이블 또는 데이터베이스에 대해 Stretch Database를 사용하지 않도록 설정할 수도 있습니다.

테이블에 대해 Stretch Database를 사용하지 않도록 설정한 후에는 데이터 마이그레이션이 중지되고 원격 테이블의 결과가 더 이상 쿼리 결과에 포함되지 않습니다.

데이터 마이그레이션을 일시 중지하려면 [Stretch Database 일시 중지 및 다시 시작](sql-server-stretch-database-pause.md)을 참조하세요.

> [!NOTE]
> 테이블 또는 데이터베이스에 대해 Stretch Database를 사용하지 않도록 설정하면 원격 개체를 삭제하지 않습니다. 원격 테이블 또는 원격 데이터베이스를 삭제하려면 Azure 관리 포털을 사용하여 삭제해야 합니다. 원격 개체를 삭제할 때까지 계속해서 Azure 비용이 발생합니다. 자세한 내용은 [SQL Server Stretch Database 가격 책정](https://azure.microsoft.com/pricing/details/sql-server-stretch-database/)을 참조하세요.
> 
> 

## <a name="disable-stretch-database-for-a-table"></a>테이블에 대해 Stretch Database를 사용하지 않도록 설정
### <a name="use-sql-server-management-studio-to-disable-stretch-database-for-a-table"></a>SQL Server Management Studio를 사용하여 테이블에 대해 Stretch Database를 사용하지 않도록 설정
1. SQL Server Management Studio의 개체 탐색기에서 Stretch Database를 사용하지 않도록 설정할 테이블을 선택합니다.
2. 마우스 오른쪽\-단추를 클릭하고 **스트레치**를 선택한 후 다음 옵션 중 하나를 선택합니다.
   
   * **사용 안 함 | Azure에서 데이터 다시 가져오기**. Azure에서 테이블의 원격 데이터를 SQL Server에 다시 복사한 다음 테이블에 대해 Stretch Database를 사용하지 않도록 설정합니다. 이 명령은 취소할 수 없습니다.
     
     > [!NOTE]
     > 테이블에 대한 원격 데이터를 Azure에서 SQL Server로 복사할 경우 데이터 전송 비용이 발생합니다. 자세한 내용은 [데이터 전송 가격 책정 정보](https://azure.microsoft.com/pricing/details/data-transfers/)를 참조하세요.
     > 
     > 
     
     Azure에서 SQL Server로 모든 원격 데이터가 다시 복사된 후에는 테이블에 대해 스트레치가 사용하지 않도록 설정됩니다.
   * **사용 안 함 | Azure에 데이터 남겨 두기**. 테이블에 대해 Stretch Database를 사용하지 않도록 설정합니다.  Azure에서 테이블에 대한 원격 데이터를 중단합니다.
   
   > [!NOTE]
   > 테이블에 대해 확장 데이터베이스를 사용하지 않도록 설정하면 원격 데이터 또는 원격 테이블을 삭제하지 않습니다. 원격 테이블을 삭제하려면 Azure 관리 포털을 사용하여 삭제해야 합니다. 원격 테이블을 삭제할 때까지 계속해서 Azure 비용이 발생합니다. 자세한 내용은 [SQL Server Stretch Database 가격 책정](https://azure.microsoft.com/pricing/details/sql-server-stretch-database/)을 참조하세요.
   > 
   > 

### <a name="use-transact-sql-to-disable-stretch-database-for-a-table"></a>Transact\-SQL을 사용하여 테이블에 대해 Stretch Database를 사용하지 않도록 설정
* 테이블에 대해 스트레치를 사용하지 않도록 설정하고 Azure에서 SQL Server로 테이블의 원격 데이터를 다시 복사하려면 다음 명령을 실행합니다. Azure에서 SQL Server로 모든 원격 데이터가 다시 복사된 후에는 테이블에 대해 스트레치가 사용하지 않도록 설정됩니다.
  
  이 명령은 취소할 수 없습니다.
  
  ```tsql
  USE <Stretch-enabled database name>;
  GO
  ALTER TABLE <Stretch-enabled table name>  
     SET ( REMOTE_DATA_ARCHIVE ( MIGRATION_STATE = INBOUND ) ) ;
  GO
  ```
  > [!NOTE]
  > 테이블에 대한 원격 데이터를 Azure에서 SQL Server로 복사할 경우 데이터 전송 비용이 발생합니다. 자세한 내용은 [데이터 전송 가격 책정 정보](https://azure.microsoft.com/pricing/details/data-transfers/)를 참조하세요.
  > 
  > 
* 테이블에 대해 스트레치를 사용하지 않도록 설정하고 원격 데이터를 중단하려면 다음 명령을 실행합니다.
  
  ```tsql
  ALTER TABLE <table_name>
     SET ( REMOTE_DATA_ARCHIVE = OFF_WITHOUT_DATA_RECOVERY ( MIGRATION_STATE = PAUSED ) ) ;
  ```

> [!NOTE]
> 테이블에 대해 확장 데이터베이스를 사용하지 않도록 설정하면 원격 데이터 또는 원격 테이블을 삭제하지 않습니다. 원격 테이블을 삭제하려면 Azure 관리 포털을 사용하여 삭제해야 합니다. 원격 테이블을 삭제할 때까지 계속해서 Azure 비용이 발생합니다. 자세한 내용은 [SQL Server Stretch Database 가격 책정](https://azure.microsoft.com/pricing/details/sql-server-stretch-database/)을 참조하세요.
> 
> 

## <a name="disable-stretch-database-for-a-database"></a>데이터베이스에 대해 Stretch Database를 사용하지 않도록 설정
데이터베이스에 대해 Stretch Database를 사용하지 않도록 설정하려면 먼저 데이터베이스의 개별 스트레치\-지원 테이블에서 Stretch Database를 사용하지 않도록 설정해야 합니다.

### <a name="use-sql-server-management-studio-to-disable-stretch-database-for-a-database"></a>SQL Server Management Studio를 사용하여 데이터베이스에 대해 Stretch Database를 사용하지 않도록 설정
1. SQL Server Management Studio의 개체 탐색기에서 Stretch Database를 사용하지 않도록 설정할 데이터베이스를 선택합니다.
2. 마우스 오른쪽\-단추를 클릭하고 **작업**, **스트레치**, **사용 안 함**을 차례로 선택합니다.

> [!NOTE]
> 데이터베이스에 대해 Stretch Database를 사용하지 않도록 설정하면 원격 데이터베이스를 삭제하지 않습니다. 원격 데이터베이스를 삭제하려면 Azure 관리 포털을 사용하여 삭제해야 합니다. 원격 데이터베이스를 삭제할 때까지 계속해서 Azure 비용이 발생합니다. 자세한 내용은 [SQL Server Stretch Database 가격 책정](https://azure.microsoft.com/pricing/details/sql-server-stretch-database/)을 참조하세요.
> 
> 

### <a name="use-transact-sql-to-disable-stretch-database-for-a-database"></a>Transact\-SQL을 사용하여 데이터베이스에 대해 Stretch Database를 사용하지 않도록 설정
다음 명령을 실행합니다.

```tsql
ALTER DATABASE <database name>
    SET REMOTE_DATA_ARCHIVE = OFF ;
```

> [!NOTE]
> 데이터베이스에 대해 Stretch Database를 사용하지 않도록 설정하면 원격 데이터베이스를 삭제하지 않습니다. 원격 데이터베이스를 삭제하려면 Azure 관리 포털을 사용하여 삭제해야 합니다. 원격 데이터베이스를 삭제할 때까지 계속해서 Azure 비용이 발생합니다. 자세한 내용은 [SQL Server Stretch Database 가격 책정](https://azure.microsoft.com/pricing/details/sql-server-stretch-database/)을 참조하세요.
> 
> 

## <a name="see-also"></a>참고 항목
[ALTER DATABASE SET 옵션(TRANSACT-SQL)](https://msdn.microsoft.com/library/bb522682.aspx)

[Stretch Database 일시 중지 및 다시 시작](sql-server-stretch-database-pause.md)




<!--HONumber=Nov16_HO3-->


