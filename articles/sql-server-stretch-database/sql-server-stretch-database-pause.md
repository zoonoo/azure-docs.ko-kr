---
title: "데이터 마이그레이션 일시 중지 및 다시 시작(Stretch Database)| Microsoft Docs"
description: "Azure에 대한 데이터 마이그레이션을 일시 중지하거나 다시 시작하는 방법에 대해 알아봅니다."
services: sql-server-stretch-database
documentationcenter: 
author: douglaslMS
manager: jhubbard
editor: 
ms.assetid: d45c05ad-254e-4950-a652-3d5cc40ed967
ms.service: sql-server-stretch-database
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/14/2016
ms.author: douglasl
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: f8b5f4461a20c1407ba2ce834ec6dd970f4e7544


---
# <a name="pause-and-resume-data-migration-stretch-database"></a>데이터 마이그레이션 일시 중지 및 다시 시작(Stretch Database)
Azure에 대한 데이터 마이그레이션을 일시 중지하거나 다시 시작하려면 SQL Server Management Studio의 테이블에 대해 **스트레치**를 선택한 다음 **일시 중지**를 선택하여 데이터 마이그레이션을 일시 중지하거나 **다시 시작**을 선택하여 데이터 마이그레이션을 다시 시작합니다. 또한 TRANSACT\-SQL을 사용하여 데이터 마이그레이션을 일시 중지하거나 다시 시작할 수 있습니다.

로컬 서버에서 문제를 해결하거나 사용 가능한 네트워크 대역폭을 최대화하려는 경우 개별 테이블에서 데이터 마이그레이션을 일시 중지합니다.

## <a name="pause-data-migration"></a>데이터 마이그레이션 일시 중지
### <a name="use-sql-server-management-studio-to-pause-data-migration"></a>SQL Server Management Studio를 사용하여 데이터 마이그레이션 일시 중지
1. SQL Server Management Studio의 개체 탐색기에서 데이터 마이그레이션을 일시 중지하려는 스트레치\-사용 테이블을 선택합니다.
2. 마우스 오른쪽\-단추로 클릭하고 **스트레치**를 선택한 다음 **일시 중지**를 선택합니다.

### <a name="use-transact-sql-to-pause-data-migration"></a>TRANSACT\-SQL을 사용하여 데이터 마이그레이션 일시 중지
다음 명령을 실행합니다.

```tsql
USE <Stretch-enabled database name>;
GO
ALTER TABLE <Stretch-enabled table name>  
    SET ( REMOTE_DATA_ARCHIVE ( MIGRATION_STATE = PAUSED ) ) ;  
GO
```

## <a name="resume-data-migration"></a>데이터 마이그레이션 다시 시작
### <a name="use-sql-server-management-studio-to-resume-data-migration"></a>SQL Server Management Studio를 사용하여 데이터 마이그레이션 다시 시작
1. SQL Server Management Studio의 개체 탐색기에서 데이터 마이그레이션을 다시 시작하려는 스트레치\-사용 테이블을 선택합니다.
2. 마우스 오른쪽\-단추로 클릭하고 **스트레치**를 선택한 다음 **다시 시작**을 선택합니다.

### <a name="use-transact-sql-to-resume-data-migration"></a>TRANSACT\-SQL을 사용하여 데이터 마이그레이션 다시 시작
다음 명령을 실행합니다.

```tsql
USE <Stretch-enabled database name>;
GO
ALTER TABLE <Stretch-enabled table name>   
    SET ( REMOTE_DATA_ARCHIVE ( MIGRATION_STATE = OUTBOUND ) ) ;  
 GO
```

## <a name="check-whether-migration-is-active-or-paused"></a>마이그레이션이 활성화되었는지 또는 일시 중지되었는지 확인
### <a name="use-sql-server-management-studio-to-check-whether-migration-is-active-or-paused"></a>SQL Server Management Studio를 사용하여 마이그레이션이 활성화되었는지 또는 일시 중지되었는지 확인
SQL Server Management Studio를 열고 **Stretch Database Monitor**를 열고 **마이그레이션 상태** 열 값을 확인합니다. 자세한 내용은 [데이터 마이그레이션 모니터링 및 문제 해결](sql-server-stretch-database-monitor.md)을 참조하세요.

### <a name="use-transact-sql-to-check-whether-migration-is-active-or-paused"></a>Transact-SQL을 사용하여 마이그레이션이 활성화되었는지 또는 일시 중지되었는지 확인
카탈로그 뷰 **sys.remote_data_archive_tables**를 쿼리하고 **is_migration_paused** 열의 값을 확인합니다. 자세한 내용은 [sys.remote_data_archive_tables](https://msdn.microsoft.com/library/dn935003.aspx)를 참조하세요.

## <a name="see-also"></a>참고 항목
[ALTER TABLE(Transact-SQL)](https://msdn.microsoft.com/library/ms190273.aspx)
[데이터 마이그레이션 모니터링 및 문제 해결](sql-server-stretch-database-monitor.md)




<!--HONumber=Nov16_HO3-->


