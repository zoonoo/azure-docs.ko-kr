<properties 
   pageTitle="REST API를 사용하여 삭제된 Azure SQL 데이터베이스 복원" 
   description="Microsoft Azure SQL 데이터베이스, 삭제된 데이터베이스 복원, 삭제된 데이터베이스 복구, REST API" 
   services="sql-database" 
   documentationCenter="" 
   authors="elfisher" 
   manager="jeffreyg" 
   editor="v-romcal"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="storage-backup-recovery" 
   ms.date="03/17/2015"
   ms.author="elfish; v-romcal"/>

# REST API를 사용하여 삭제된 Azure SQL 데이터베이스 복원

> [AZURE.SELECTOR]
- [Restore deleted database - portal](sql-database-restore-deleted-database-tutorial-management-portal.md)
- [Restore deleted database - PowerShell](sql-database-restore-deleted-database-tutorial-powershell.md) 

## 개요

이 가이드에서는 REST API를 사용하여 삭제된 Azure SQL 데이터베이스를 복원하는 방법을 보여 줍니다. 보다 자세한 작업의 링크도 제공됩니다.

삭제된 Azure SQL 데이터베이스를 복원하면 새 데이터베이스가 작성됩니다. 서비스는 복원 지점에서 사용되는 백업을 기준으로 서비스 계층을 자동으로 선택합니다. 논리 서버에 다른 데이터베이스를 만들 수 있는 할당량이 있는지 확인하세요. 할당량 증가를 요청하려면 [Azure 지원](http://azure.microsoft.com/support/options/)에 문의하세요.

## 제한 사항 및 보안

[Azure 포털에서 삭제된 Azure SQL 데이터베이스 복원](sql-database-restore-deleted-database-tutorial-management-portal.md)을 참조하세요.

## 방법: REST API를 사용하여 삭제된 Azure SQL 데이터베이스 복원

1.	[복원 가능한 삭제된 데이터베이스 나열](http://msdn.microsoft.com/library/azure/dn509562.aspx) 작업을 사용하여 복원 가능한 모든 삭제된 데이터베이스를 나열합니다.
	
2.	[복원 가능한 삭제된 데이터베이스 가져오기](http://msdn.microsoft.com/library/azure/dn509574.aspx) 작업을 사용하여 복원하려는 삭제된 데이터베이스에 대한 세부 정보를 가져옵니다.

3.	[데이터베이스 복원 요청 만들기](http://msdn.microsoft.com/library/azure/dn509571.aspx) 작업을 사용하여 복원을 시작합니다.
	
4.	[데이터베이스 작업 상태](http://msdn.microsoft.com/library/azure/dn720371.aspx) 작업을 사용하여 복원 상태를 추적합니다.

## 다음 단계

자세한 내용은 다음을 참조하세요.

[Azure SQL 데이터베이스 비즈니스 연속성](http://msdn.microsoft.com/library/azure/hh852669.aspx)

[Azure SQL 데이터베이스 백업 및 복원](http://msdn.microsoft.com/library/azure/jj650016.aspx)

[서비스 관리 REST API 참조](http://msdn.microsoft.com/library/azure/ee460799.aspx)

<!---HONumber=62-->