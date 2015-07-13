<properties 
	title="How to add a users to an elastic database pool" 
	pageTitle="탄력적 데이터베이스 풀에 사용자를 추가하는 방법" 
	description="풀에 있는 각 데이터베이스에 대한 권한을 가진 사용자를 추가해야 합니다." 
	metaKeywords="azure sql database elastic databases credentials" 
	services="sql-database" documentationCenter=""  
	manager="jeffreyg" 
	authors="sidneyh"/>

<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/20/2015" 
	ms.author="sidneyh" />

# 탄력적 데이터베이스 풀에 사용자를 추가하는 방법

탄력적 데이터베이스 작업의 경우 [탄력적 데이터베이스 풀](sql-database-elastic-pool.md)에 속한 모든 데이터베이스에 대해 동일한 스크립트를 실행할 수 있습니다. 스크립트를 실행하려면 풀에 있는 모든 데이터베이스에 적절한 권한을 가진 사용자를 추가해야 합니다. 자세한 내용은 [Azure SQL 데이터베이스에서 데이터베이스 및 로그인 관리](https://msdn.microsoft.com/library/azure/ee336235.aspx?f=255&MSPPError=-2147217396) 또는 [SQL Azure 데이터베이스에 사용자 추가](http://azure.microsoft.com/blog/2010/06/21/adding-users-to-your-sql-azure-database/)를 참조하세요.

## 필수 조건
* [탄력적 데이터베이스 풀(Preview) 만들기](sql-database-elastic-pool-portal.md)
* [탄력적 작업 구성 요소](sql-database-elastic-jobs-service-installation.md)를 설치합니다. 

## 데이터베이스에 사용자를 추가하는 방법

1.	먼저 탄력적 데이터베이스 풀에 속한 데이터베이스가 있는 Azure SQL 데이터베이스 서버의 **마스터**에 연결하고, **탄력적 데이터베이스 작업**을 설치할 때 사용한 것과 동일한 자격 증명을 사용하여 새 로그인을 만듭니다.

		CREATE LOGIN login1 WITH password='<ProvidePassword>';

2. 풀에 있는 각 데이터베이스에 로그인하고 동일한 이름과 암호를 사용하여 사용자를 만듭니다. 사용자에게 작업을 실행하기에 충분한 권한이 있어야 합니다. 이 코드는 각 데이터베이스에서 실행해야 합니다.

		CREATE USER admin1 FROM LOGIN login1;
		
3. 또한 사용자에게 작업에 대해 지정된 스크립트를 실행하기에 충분한 권한이 있어야 합니다. **sp_addrolemember** 프로시저를 사용하여 사용자에게 스크립트를 성공적으로 실행하는 데 필요한 최소한의 권한을 제공합니다.

## 다음 단계

데이터베이스 풀에 대한 작업을 실행합니다. [탄력적 데이터베이스 작업 만들기 및 관리](sql-database-elastic-jobs-create-and-manage.md)를 참조하세요.

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-jobs-overview/elastic-jobs.png
<!--anchors-->

<!---HONumber=62-->