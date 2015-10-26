<properties
	
	pageTitle="Upgrade to the latest elastic database client library" 
	description="Upgrade apps and libraries using Nuget" 
	services="sql-database" 
	documentationCenter="" 
	manager="jeffreyg" 
	authors="sidneyh"/>

<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/22/2015" 
	ms.author="sidneyh" />

# 최신 탄력적 데이터베이스 클라이언트 라이브러리로 업그레이드

탄력적 데이터베이스 클라이언트 라이브러리의 새 버전은 Visual Studio의 NuGetPackage Manager 인터페이스와 [NuGet](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/)을 통해 제공됩니다. 업그레이드에는 클라이언트 라이브러리의 새 기능 지원 및 버그 수정이 포함됩니다.

새 라이브러리를 사용하여 응용 프로그램을 다시 빌드하고 새로운 기능을 지원하도록 Azure SQL 데이터베이스에 저장된 기존 분할된 데이터베이스 맵 관리자 메타데이터를 변경합니다.

이러한 단계를 순서대로 수행하면 메타데이터 개체를 업데이트할 때 클라이언트 라이브러리의 이전 버전이 환경에 더 이상 포함되어 있지 않으므로 업그레이드 후에 이전 버전 메타데이터 개체가 생성되지 않습니다.

## 업그레이드 단계

**1. 응용 프로그램을 업그레이드합니다.** Visual Studio에서 라이브러리를 사용하는 모든 개발 프로젝트에 최신 클라이언트 라이브러리 버전을 다운로드하고 해당 버전을 참조하도록 지정한 다음 프로젝트를 다시 빌드하고 배포합니다.

 * Visual Studio 솔루션에서 **도구** --> **NuGet 패키지 관리자** --> **솔루션용 NuGet 패키지 관리**를 선택합니다. 
 * (Visual Studio 2013) 왼쪽 패널에서 **업데이트**를 선택한 다음 창에 표시되는 **Azure SQL 데이터베이스 탄력적인 확장 클라이언트 라이브러리** 패키지에서 **업데이트** 단추를 선택합니다.
 * (Visual Studio 2015) 필터 상자를 **업그레이드 가능**으로 설정합니다. 업데이트할 패키지를 선택하고 **업데이트** 단추를 클릭합니다.
	
 
 * 빌드와 배포를 수행합니다.

**2. 스크립트를 업그레이드합니다.** **PowerShell** 스크립트를 사용하여 분할을 관리하는 경우 [새 라이브러리 버전을 다운로드](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/)한 다음 스크립트를 실행하는 디렉터리에 복사합니다.

**3. 분할/병합 서비스를 업그레이드합니다.** 탄력적 데이터베이스 분할/병합 도구를 사용하여 분할된 데이터를 다시 구성하는 경우 [도구의 최신 버전을 다운로드하여 배포](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Service.SplitMerge/)합니다. 자세한 서비스 업그레이드 단계는 [여기](sql-database-elastic-scale-overview-split-and-merge.md)서 확인할 수 있습니다.

**4. 분할된 데이터베이스 맵 관리자 DB를 업그레이드합니다**. Azure SQL 데이터베이스에서 분할된 데이터베이스 맵을 지원하는 메타데이터를 업그레이드합니다. 이 작업은 두 가지 방법, 즉 PowerShell이나 C#을 사용하여 수행할 수 있습니다. 아래에는 두 옵션이 모두 나와 있습니다.

***옵션 1: PowerShell을 사용하여 메타데이터 업그레이드***

1. [여기](http://nuget.org/nuget.exe)서 NuGet용 최신 명령줄 유틸리티를 다운로드하여 폴더에 저장합니다. 

2. 명령 프롬프트를 열고 같은 폴더로 이동한 후 다음 명령을 실행합니다. `nuget install Microsoft.Azure.SqlDatabase.ElasticScale.Client`

3. 방금 다운로드한 새 클라이언트 DLL 버전이 포함된 하위 폴더로 이동합니다. 예: `cd .\Microsoft.Azure.SqlDatabase.ElasticScale.Client.1.0.0\lib\net45`

4. [스크립트 센터](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-Database-Elastic-6442e6a9)에서 탄력적 데이터베이스 클라이언트 업그레이드 스크립틀릿을 다운로드한 다음 DLL이 포함된 것과 같은 폴더에 저장합니다.

5. 해당 폴더의 명령 프롬프트에서 "PowerShell .\\upgrade.ps1"을 실행하고 프롬프트의 지시에 따릅니다.
 
***옵션 2: C#을 사용하여 메타데이터 업그레이드***

ShardMapManager를 열고 모든 분할에서 반복 실행한 다음, 다음 예제와 같이 [UpgradeLocalStore](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.upgradelocalstore.aspx) 및 [UpgradeGlobalStore](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.upgradeglobalstore.aspx) 메서드를 호출하여 메타데이터 업그레이드를 수행하는 Visual Studio 응용 프로그램을 만들 수도 있습니다.

	ShardMapManager smm =
	   ShardMapManagerFactory.GetSqlShardMapManager
	   (connStr, ShardMapManagerLoadPolicy.Lazy); 
	smm.UpgradeGlobalStore(); 
	
	foreach (ShardLocation loc in
	 smm.GetDistinctShardLocations()) 
	{   
	   smm.UpgradeLocalStore(loc); 
	} 

이러한 메타데이터 업그레이드 기술은 여러 번 적용해도 안전합니다. 예를 들어 업데이트를 이미 수행한 후에 이전 클라이언트 버전에서 분할을 잘못 만드는 경우 모든 분할에서 업그레이드를 다시 실행하면 인프라 전체에서 최신 메타데이터 버전을 적용할 수 있습니다.

**참고:** 현재까지 게시된 새 버전의 클라이언트 라이브러리는 Azure SQL DB의 이전 버전 분할된 데이터베이스 맵 관리자 메타데이터에도 계속 작동하며, 그 반대의 경우도 마찬가지입니다. 하지만 최신 클라이언트의 일부 새 기능을 활용하려면 메타데이터를 업그레이드해야 합니다. 메타데이터를 업그레이드해도 사용자 데이터 또는 응용 프로그램별 데이터에는 영향을 주지 않으며 분할된 데이터베이스 맵 관리자가 만들어 사용하는 개체에만 영향을 줍니다. 또한 응용 프로그램도 위에서 설명한 업그레이드 시퀀스에 따라 계속 작동합니다.

## 탄력적 데이터베이스 클라이언트 버전 기록 

**버전 1.0 -- 2015년 4월**

* 일반 공급 릴리스
* 분할 키로 날짜/시간 형식에 대한 지원 추가

**버전 0.8 – 2015년 3월**

* 새로운 ShardMap.OpenConnectionForKeyAsync 메서드를 통해 데이터 종속 라우팅을 위한 비동기 지원이 추가되었습니다. 
* 공용 KeyType 속성이 ShardMap에 추가되었습니다. 
* 분할에 대한 개선된 데이터베이스 복원 및 데이터베이스 복구 시나리오 지원 기능이 추가되었습니다. 

**버전 0.7 – 2014년 10월**

초기 미리 보기 버전입니다.


[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]


<!--Image references-->
[1]: ./media/sql-database-elastic-scale-upgrade-client-library/nuget-upgrade.png
 

<!---HONumber=Oct15_HO3-->