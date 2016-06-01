<properties
   pageTitle="Azure SQL 데이터 웨어하우스에서 데이터베이스 복원(Azure 포털) | Microsoft Azure"
   description="Azure SQL 데이터 웨어하우스의 라이브 데이터베이스, 삭제된 데이터베이스, 액세스할 수 없는 데이터베이스를 복원하기 위한 REST API 작업입니다."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="elfisher"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="05/05/2016"
   ms.author="elfish;barbkess;sonyama"/>

# Azure SQL 데이터 웨어하우스에서 데이터베이스 백업 및 복원(REST API)

> [AZURE.SELECTOR]
- [개요](sql-data-warehouse-overview-manage-database-restore.md)
- [포털](sql-data-warehouse-manage-database-restore-portal.md)
- [PowerShell](sql-data-warehouse-manage-database-restore-powershell.md)
- [REST (영문)](sql-data-warehouse-manage-database-restore-rest-api.md)

SQL 데이터 웨어하우스에서 데이터베이스를 복원하기 위한 REST API 작업입니다.

이 항목의 작업:

- 라이브 데이터베이스 복원
- 삭제된 데이터베이스 복원
- 다른 Azure 지역에서 액세스할 수 없는 데이터베이스 복원

## 시작하기 전에

**SQL 데이터베이스의 DTU 용량을 확인합니다.** SQL 데이터 웨어하우스는 논리적 SQL Server에 있는 새 데이터베이스로 복원하므로 복원하려는 SQL Server가 새 데이터베이스에 대해 충분한 DTU 용량을 가지고 있는지 확인해야 합니다. [DTU 할당량을 보고 늘리는 방법][]에 대한 자세한 내용은 이 블로그 게시물을 참조하세요.

## 라이브 데이터베이스 복원

데이터베이스를 복원하려면

1. 데이터베이스 복원 지점 가져오기 작업을 사용하여 데이터베이스 복원 지점 목록을 가져옵니다.
2. [데이터베이스 복원 요청 만들기][] 작업을 사용하여 복원을 시작합니다.
3. [데이터베이스 작업 상태][] 작업을 사용하여 복원 상태를 추적합니다.

>[AZURE.NOTE] 복원이 완료된 후 [복구된 데이터베이스 마무리][] 가이드에 따라 복구된 데이터베이스를 구성할 수 있습니다.

## 삭제된 데이터베이스 복원

삭제된 데이터베이스를 복원하려면

1.	[복원 가능한 삭제된 데이터베이스 나열][] 작업을 사용하여 복원 가능한 모든 삭제된 데이터베이스를 나열합니다.
2.	[복원 가능한 삭제된 데이터베이스 가져오기][] 작업을 사용하여 복원하려는 삭제된 데이터베이스에 대한 세부 정보를 가져옵니다.
3.	[데이터베이스 복원 요청 만들기][] 작업을 사용하여 복원을 시작합니다.
4.	[데이터베이스 작업 상태][] 작업을 사용하여 복원 상태를 추적합니다.

>[AZURE.NOTE] 복원이 완료된 후 [복구된 데이터베이스 마무리][] 가이드에 따라 복구된 데이터베이스를 구성할 수 있습니다.

## Azure 지역에서 복원

지역 복원을 수행하려면

1. [복구 가능한 데이터베이스 나열][] 작업을 통해 복구 가능한 데이터베이스 목록을 가져옵니다.
2. [복구 가능한 데이터베이스 가져오기][] 작업을 통해 복구할 데이터베이스를 가져옵니다.
3. [데이터베이스 복구 요청 만들기][] 작업을 통해 복구 요청을 만듭니다.
4. [데이터베이스 작업 상태][] 작업을 통해 복구 상태를 추적합니다.

### 지역 복원을 수행한 후 데이터베이스 구성
복구된 데이터베이스 프로덕션을 준비하는 데 유용한 검사 목록입니다.

1. **연결 문자열 업데이트**: 클라이언트 도구의 연결 문자열이 새로 복구된 데이터베이스를 가리키는지 확인합니다.
2. **방화벽 규칙 수정**: 대상 서버에서 방화벽 규칙을 확인하고 서버 및 새로 복구된 데이터베이스와 클라이언트 컴퓨터 또는 Azure의 연결이 설정되어 있는지 확인합니다.
3. **서버 로그인 및 데이터베이스 사용자 확인**: 응용 프로그램에서 사용하는 모든 로그인이 복구된 데이터베이스를 호스트하는 서버에 존재하는지 확인합니다. 누락된 로그인을 다시 만들고 복구된 데이터베이스에 대한 적절한 사용 권한을 부여합니다. 
4. **감사 사용**: 데이터베이스에 액세스하기 위해 감사가 필요한 경우 데이터베이스 복구 후에 감사 사용을 설정해야 합니다.

원본 데이터베이스가 TDE를 사용할 수 있는 경우 복구된 데이터베이스도 TDE를 사용할 수 있습니다.


## 다음 단계
Azure SQL 데이터베이스 버전의 무중단 업무 방식 기능에 대해 알아보려면 [Azure SQL 데이터베이스 무중단 업무 방식 개요][]를 읽으세요.

<!--Image references-->

<!--Article references-->
[Azure SQL 데이터베이스 무중단 업무 방식 개요]: sql-database-business-continuity.md
[복구된 데이터베이스 마무리]: sql-database-recovered-finalize.md
[How to install and configure Azure PowerShell]: powershell-install-configure.md

<!--MSDN references-->
[데이터베이스 복원 요청 만들기]: https://msdn.microsoft.com/library/azure/dn509571.aspx
[데이터베이스 작업 상태]: https://msdn.microsoft.com/library/azure/dn720371.aspx
[복원 가능한 삭제된 데이터베이스 가져오기]: https://msdn.microsoft.com/library/azure/dn509574.aspx
[복원 가능한 삭제된 데이터베이스 나열]: https://msdn.microsoft.com/library/azure/dn509562.aspx
[Restore-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt693390.aspx

<!--Blog references-->
[DTU 할당량을 보고 늘리는 방법]: https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests/

<!--Other Web references-->
[Azure Portal]: https://portal.azure.com/
[Microsoft Web Platform Installer]: https://aka.ms/webpi-azps

<!---HONumber=AcomDC_0518_2016-->