<properties
   pageTitle="트랜잭션 복제를 사용하여 SQL 데이터베이스로 마이그레이션"
   description="Microsoft Azure SQL 데이터베이스, 데이터베이스 마이그레이션, 데이터베이스 가져오기, 트랜잭션 복제"
   services="sql-database"
   documentationCenter=""
   authors="carlrabeler"
   manager="jeffreyg"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-management"
   ms.date="03/14/2016"
   ms.author="carlrab"/>

# 트랜잭션 복제를 사용하여 SQL Server 데이터베이스를 SQL 데이터베이스로 마이그레이션

마이그레이션하는 동안 SQL Server 데이터베이스의 운영을 중지할 수 없는 경우 마이그레이션 솔루션으로 SQL Server 트랜잭션 복제를 사용할 수 있습니다. 트랜잭션 복제를 사용하면 마이그레이션을 시작하는 순간부터 끝나는 시점 사이에 발생하는 모든 데이터 또는 스키마 변경 사항이 Azure SQL 데이터베이스에 표시됩니다. 마이그레이션이 완료되면 응용 프로그램이 온-프레미스 데이터베이스가 아닌 Azure SQL 데이터베이스를 가리키도록 응용 프로그램의 연결 문자열을 변경하기만 하면 됩니다. 온-프레미스 데이터베이스에 남아 있는 모든 변경 사항이 트랜잭션 복제를 통해 Azure DB로 이동되고 모든 응용 프로그램이 Azure DB를 가리키면 이제 Azure SQL 데이터베이스를 프로덕션 시스템으로 남겨 두고 안전하게 복제를 제거할 수 있습니다.

 ![SeedCloudTR 다이어그램](./media/sql-database-cloud-migrate/SeedCloudTR.png)


트랜잭션 복제는 SQL Server 6.5부터 SQL Server에 내장 및 통합된 기술입니다. 대부분의 DBA가 경험한 적이 있는 매우 성숙하고 검증된 기술입니다. 이제 [SQL Server 2016 미리 보기](http://www.microsoft.com/server-cloud/products/sql-server-2016/)를 사용하여 Azure SQL 데이터베이스를 온-프레미스 게시의 [트랜잭션 복제 구독자](https://msdn.microsoft.com/library/mt589530.aspx)로 구성할 수 있습니다. Management Studio에서 설정하는 방법은 온-프레미스 서버에서 트랜잭션 복제 구독자를 설정하는 방법과 정확하게 일치합니다. 이 시나리오는 게시자 및 배포자가 다음 SQL Server 버전 중 하나일 때 지원됩니다.

 - SQL Server 2016 CTP3(미리 보기) 이상 
 - SQL Server 2014 SP1 CU3 이상
 - SQL Server 2014 RTM CU10 이상
 - SQL Server 2012 SP2 CU8 이상
 - SQL Server 2013 SP3(출시 예정)

또한 트랜잭션 복제를 사용하여 온-프레미스 데이터베이스의 하위 집합을 마이그레이션할 수 있습니다. 사용자가 Azure SQL 데이터베이스로 복제하는 게시물을 복제되는 데이터베이스의 테이블 하위 집합으로 제한할 수 있습니다. 뿐만 아니라 복제되는 각 테이블에 대해 데이터를 행의 하위 집합 및/또는 열의 하위 집합으로 제한할 수 있습니다.

<!---HONumber=AcomDC_0316_2016-->