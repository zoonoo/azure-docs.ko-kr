<properties 
   pageTitle="Azure 포털의 지역에서 복원 기능을 사용하여 Azure SQL 데이터베이스 복구" 
   description="지역에서 복원, Microsoft Azure SQL 데이터베이스, 데이터베이스 복원, 데이터베이스 복구, Azure 관리 포털, Azure 포털" 
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
   ms.date="03/18/2015"
   ms.author="elfish; v-romcal"/>

# Azure 포털의 지역에서 복원 기능을 사용하여 Azure SQL 데이터베이스 복구

> [AZURE.SELECTOR]
- [Geo-Restore - PowerShell](sql-database-geo-restore-tutorial-powershell.md)
- [Geo-Restore - REST API](sql-database-geo-restore-tutorial-rest.md)   

## 개요

이 자습서에서는 [Azure 포털](http://manage.windowsazure.com)의 지역에서 복원 기능을 사용하여 Azure SQL 데이터베이스를 복구하는 방법을 설명합니다. 지역에서 복원은 Azure SQL 데이터베이스의 Basic, Standard 및 Premium 서비스 계층에 모두 포함되어 있는 핵심적인 재해 복구 보호 기능입니다.

## 제한 사항 및 보안

* 지역에서 복원은 Basic, Standard 및 Premium 서비스 계층 모두에서 사용하도록 설정되어 있습니다.

* 백업 보존 기간은 7일(Basic)/14일(Standard)/35일(Premium)입니다.

* 구독의 관리자나 공동 관리자만 복원 요청을 제출할 수 있습니다.

* 서버 수준 보안 주체 로그인이 복원된 데이터베이스의 소유자가 됩니다.

* Web 및 Business Edition 서비스 계층은 지역에서 복원을 지원하지 않습니다.
 
	* Web 또는 Business Edition 데이터베이스를 사용하는 경우 데이터베이스 복사를 통해 데이터베이스의 트랜잭션 일치 복사본을 생성한 다음 Microsoft Azure 저장소 계정으로 복사한 데이터베이스를 내보낼 수 있습니다. 자세한 내용은 [방법: 데이터베이스 복사(Azure SQL 데이터베이스) 사용](http://msdn.microsoft.com/library/azure/ff951631.aspx) 및 [방법: Azure SQL 데이터베이스에서 서비스 가져오기 및 내보내기 사용](http://msdn.microsoft.com/library/azure/hh335292.aspx)을 참조하세요.

	* Web 및 Business Edition은 2015년 9월에 사용이 중지될 예정입니다. 자세한 내용은 [Web 및 Business Edition 지원 종료 FAQ](http://msdn.microsoft.com/library/azure/dn741330.aspx)를 참조하세요.

## 방법: Azure 포털의 지역에서 복원 기능을 사용하여 Azure SQL 데이터베이스 복구

> [AZURE.VIDEO restore-a-sql-database-using-geo-restore]

1. Microsoft 계정을 사용하여 [Azure 포털](http://manage.windowsazure.com)에 로그인한 다음 **SQL 데이터베이스**를 선택합니다.

2. 왼쪽 탐색 영역에서 **SQL 데이터베이스**를 클릭합니다.

3. 페이지 위쪽에서 **서버**를 클릭합니다.

4. **서버** 목록에서 **저하됨**을 클릭합니다.

4. 페이지 위쪽에서 **백업**을 클릭합니다.

5. 복원할 데이터베이스를 클릭합니다.

6. 페이지 아래쪽의 명령 모음에서 **복원**을 클릭합니다. 그러면 **복원 설정 지정** 대화 상자가 시작됩니다.

7. 데이터베이스를 복원할 **데이터베이스 이름** 및 **대상 서버**를 선택합니다. 기본적으로 데이터베이스 이름은 자동으로 선택되지만 원하는 경우 변경할 수 있습니다.

9. 확인 표시를 클릭하여 복원 요청을 제출합니다.

복원을 완료하려면 다소 시간이 걸릴 수 있습니다. 복원 상태를 모니터링하려면 페이지 오른쪽 아래의 명령 모음에서 상태 아이콘을 클릭하고 **세부 정보**를 클릭합니다.

## 다음 단계

자세한 내용은 다음을 참조하세요.

[Azure 포털의 특정 시점 복원 기능을 사용하여 Azure SQL 데이터베이스 복원](sql-database-point-in-time-restore-tutorial-management-portal.md)

[Azure 포털에서 삭제된 Azure SQL 데이터베이스 복원](sql-database-restore-deleted-database-tutorial-management-portal.md)

[Azure SQL 데이터베이스 비즈니스 연속성](http://msdn.microsoft.com/library/azure/hh852669.aspx)

[Azure SQL 데이터베이스 백업 및 복원](http://msdn.microsoft.com/library/azure/jj650016.aspx)

[Azure SQL 데이터베이스 지역에서 복원(블로그)](http://azure.microsoft.com/blog/2014/09/13/azure-sql-database-geo-restore/)

<!---HONumber=July15_HO4-->