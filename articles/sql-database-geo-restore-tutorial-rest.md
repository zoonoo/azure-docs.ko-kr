<properties 
   pageTitle="REST API를 통해 지역에서 복원 기능을 사용하여 Azure SQL 데이터베이스 복구" 
   description="지역에서 복원, Microsoft Azure SQL 데이터베이스, 데이터베이스 복원, 데이터베이스 복구, REST API" 
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
   ms.date="02/24/2015"
   ms.author="elfish; v-romcal"/>

# REST API를 통해 지역에서 복원 기능을 사용하여 Azure SQL 데이터베이스 복구

> [AZURE.SELECTOR]
- [지역에서 복원 - 포털](http://azure.microsoft.com/documentation/articles/sql-database-geo-restore-tutorial-management-portal/)
- [지역에서 복원 - PowerShell](http://azure.microsoft.com/documentation/articles/sql-database-geo-restore-tutorial-powershell/)

## 개요

이 가이드에서는 REST API를 사용하여 Azure SQL 데이터베이스를 복구하는 방법을 설명합니다. 보다 자세한 작업의 링크도 제공됩니다. 지역에서 복원은 Azure SQL 데이터베이스의 Basic, Standard 및 Premium 서비스 계층에 모두 포함되어 있는 핵심적인 재해 복구 보호 기능입니다.

## 제한 사항 및 보안

[Azure 포털의 지역에서 복원 기능을 사용하여 Azure SQL 데이터베이스 복구](http://azure.microsoft.com/documentation/articles/sql-database-geo-restore-tutorial-management-portal/)를 참조하세요.

## 방법: REST API를 사용하여 Azure SQL 데이터베이스 복구

1.	[복구 가능한 데이터베이스 나열](http://msdn.microsoft.com/library/azure/dn800984.aspx) 작업을 통해 복구 가능한 데이터베이스 목록을 가져옵니다.
	
2.	[복구 가능한 데이터베이스 가져오기](http://msdn.microsoft.com/library/azure/dn800985.aspx) 작업을 통해 복구할 데이터베이스를 가져옵니다.
	
3.	[데이터베이스 복구 요청 만들기](http://msdn.microsoft.com/library/azure/dn800986.aspx) 작업을 통해 복구 요청을 만듭니다.
	
4.	[데이터베이스 작업 상태](http://msdn.microsoft.com/library/azure/dn720371.aspx) 작업을 통해 복구 상태를 추적합니다.

## 다음 단계

자세한 내용은 다음을 참조하세요.

[Azure 포털의 지정 시간 복원 기능을 사용하여 Azure SQL 데이터베이스 복원](http://azure.microsoft.com/documentation/articles/sql-database-point-in-time-restore-tutorial-management-portal/)

[Azure 포털에서 삭제된 Azure SQL 데이터베이스 복원](http://azure.microsoft.com/documentation/articles/sql-database-restore-deleted-database-tutorial-management-portal/)

[Azure SQL 데이터베이스 비즈니스 연속성](http://msdn.microsoft.com/library/azure/hh852669.aspx)

[Azure SQL 데이터베이스 백업 및 복원](http://msdn.microsoft.com/library/azure/jj650016.aspx)

[Azure SQL 데이터베이스 지역에서 복원(블로그)](http://azure.microsoft.com/blog/2014/09/13/azure-sql-database-geo-restore/)

[서비스 관리 REST API 참조](https://msdn.microsoft.com/library/azure/ee460799.aspx)

<!--HONumber=47-->
