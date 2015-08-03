<properties 
   pageTitle="Azure 포털에서 삭제된 Azure SQL 데이터베이스 복원" 
   description="Microsoft Azure SQL 데이터베이스, 삭제된 데이터베이스 복원, 삭제된 데이터베이스 복구, Azure 관리 포털, Azure 포털" 
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
   ms.date="07/15/2015"
   ms.author="elfish; v-romcal"/>

# Azure 포털에서 삭제된 Azure SQL 데이터베이스 복원

> [AZURE.SELECTOR]
- [Restore deleted database - PowerShell](sql-database-restore-deleted-database-tutorial-powershell.md)
- [Restore deleted database - REST API](sql-database-restore-deleted-database-tutorial-rest.md)

## 개요

이 자습서에서는 [Azure 포털](http://manage.windowsazure.com)에서 삭제된 Azure SQL 데이터베이스를 복원하는 방법을 설명합니다. 삭제된 데이터베이스는 보존 기간이 지나지 않은 경우 삭제된 시점으로 복원할 수 있습니다. 보존 기간은 데이터베이스의 서비스 계층에 의해 결정됩니다.

삭제된 Azure SQL 데이터베이스를 복원하면 새 데이터베이스가 작성됩니다. 서비스는 복원 지점에서 사용되는 백업을 기준으로 서비스 계층을 자동으로 선택합니다. 논리 서버에 다른 데이터베이스를 만들 수 있는 할당량이 있는지 확인하세요. 할당량 증가를 요청하려면 [Azure 지원](http://azure.microsoft.com/support/options/)에 문의하세요.

## 제한 사항 및 보안

* 삭제된 SQL 데이터베이스 복원 기능은 Basic, Standard 및 Premium 서비스 계층에서 모두 사용하도록 설정됩니다. 

* 백업 보존 기간은 7일(Basic)/14일(Standard)/35일(Premium)입니다.

* 구독의 관리자나 공동 관리자만 복원 요청을 제출할 수 있습니다.

* 서버 수준 보안 주체 로그인이 복원된 데이터베이스의 소유자가 됩니다.
 
* Web 및 Business Edition 서비스 계층에서는 삭제된 SQL 데이터베이스 복원을 지원하지 않습니다.
 
	* Web 또는 Business Edition 데이터베이스를 사용하는 경우 데이터베이스 복사를 통해 데이터베이스의 트랜잭션 일치 복사본을 생성한 다음 Microsoft Azure 저장소 계정으로 복사한 데이터베이스를 내보낼 수 있습니다. 자세한 내용은 [방법: 데이터베이스 복사(Azure SQL 데이터베이스) 사용](http://msdn.microsoft.com/library/azure/ff951631.aspx) 및 [방법: Azure SQL 데이터베이스에서 서비스 가져오기 및 내보내기 사용](http://msdn.microsoft.com/library/azure/hh335292.aspx)을 참조하세요.
	* Web 및 Business Edition은 2015년 9월에 사용이 중지될 예정입니다. 자세한 내용은 [Web 및 Business Edition 지원 종료 FAQ](http://msdn.microsoft.com/library/azure/dn741330.aspx)를 참조하세요.

## 방법: Azure 포털에서 삭제된 Azure SQL 데이터베이스 복원

> [AZURE.VIDEO restore-a-deleted-sql-database]

1. Microsoft 계정을 사용하여 [Azure 포털](http://manage.windowsazure.com)에 로그인합니다.

2. 왼쪽 탐색 영역에서 **SQL 데이터베이스**를 클릭합니다.

3. 페이지 위쪽에서 **삭제된 데이터베이스**를 클릭합니다. **복원 가능한 삭제된 데이터베이스** 목록이 표시됩니다.

4. 복원할 데이터베이스를 클릭합니다.

6. 페이지 아래쪽의 명령 모음에서 **복원**을 클릭합니다. 그러면 **복원 설정 지정** 대화 상자가 시작됩니다.

7. **데이터베이스 이름**을 선택합니다. 기본적으로 데이터베이스 이름은 자동으로 선택되지만 원하는 경우 변경할 수 있습니다.

	* 삭제된 데이터베이스는 삭제했던 서버와 삭제된 지정 시간으로만 복원할 수 있습니다.   

8. 확인 표시를 클릭하여 복원 요청을 제출합니다.

복원을 완료하려면 다소 시간이 걸릴 수 있습니다. 복원 상태를 모니터링하려면 페이지 오른쪽 아래의 명령 모음에서 상태 아이콘을 클릭하고 **세부 정보**를 클릭합니다.

## 다음 단계

자세한 내용은 다음을 참조하세요.

[Azure SQL 데이터베이스 비즈니스 연속성](http://msdn.microsoft.com/library/azure/hh852669.aspx)

[Azure SQL 데이터베이스 백업 및 복원](http://msdn.microsoft.com/library/azure/jj650016.aspx)

<!---HONumber=July15_HO4-->