<properties 
   pageTitle="REST API를 통해 특정 시점 복원 기능을 사용하여 Azure SQL 데이터베이스 복원" 
   description="특정 시점 복원, Microsoft Azure SQL 데이터베이스, 데이터베이스 복원, 데이터베이스 복구, REST API" 
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

# REST API를 통해 특정 시점 복원 기능을 사용하여 Azure SQL 데이터베이스 복원

> [AZURE.SELECTOR]
- [Point in Time Restore - portal](sql-database-point-in-time-restore-tutorial-management-portal.md)
- [Point in Time Restore - PowerShell](sql-database-point-in-time-restore-tutorial-powershell.md) 

## 개요

이 가이드에서는 REST API를 통해 특정 시점 복원 기능을 사용하여 Azure SQL 데이터베이스를 복원하는 방법을 설명합니다. 보다 자세한 작업의 링크도 제공됩니다.

특정 시점 복원을 수행하면 새 데이터베이스가 작성됩니다. 서비스는 복원 지점에서 사용되는 백업을 기준으로 서비스 계층을 자동으로 선택합니다. 논리 서버에 다른 데이터베이스를 만들 수 있는 할당량이 있는지 확인하세요. 할당량 증가를 요청하려면 [Azure 지원](http://azure.microsoft.com/support/options/)에 문의하세요.

## 제한 사항 및 보안

[Azure 포털의 특정 시점 복원 기능을 사용하여 Azure SQL 데이터베이스 복원](sql-database-point-in-time-restore-tutorial-management-portal.md)을 참조하세요.

## 방법: REST API를 사용하여 Azure SQL 데이터베이스 복원

1.	[데이터베이스 가져오기](http://msdn.microsoft.com/library/azure/dn505708.aspx) 작업을 통해 복원할 데이터베이스를 가져옵니다.

2.	[데이터베이스 복원 요청 만들기](http://msdn.microsoft.com/library/azure/dn509571.aspx) 작업을 통해 복원 요청을 만듭니다.
	
3.	[데이터베이스 작업 상태](http://msdn.microsoft.com/library/azure/dn720371.aspx) 작업을 통해 복원 요청을 추적합니다.

## 다음 단계

자세한 내용은 다음을 참조하세요.

[Azure SQL 데이터베이스 비즈니스 연속성](http://msdn.microsoft.com/library/azure/hh852669.aspx)

[Azure SQL 데이터베이스 백업 및 복원](http://msdn.microsoft.com/library/azure/jj650016.aspx)

[Azure SQL 데이터베이스 특정 시점 복원(블로그)](http://azure.microsoft.com/blog/2014/10/01/azure-sql-database-point-in-time-restore/)

[서비스 관리 REST API 참조](https://msdn.microsoft.com/library/azure/ee460799.aspx)
 

<!---HONumber=62-->