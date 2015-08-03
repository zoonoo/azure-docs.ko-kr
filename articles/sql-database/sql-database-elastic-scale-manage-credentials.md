<properties 
	pageTitle="탄력적 데이터베이스 클라이언트 라이브러리에 대한 자격 증명 관리" 
	description="탄력적 데이터베이스 확장 앱에 대해 올바른 수준의 자격 증명(관리자부터 읽기 전용까지)을 설정하는 방법입니다." 
	services="sql-database" 
	documentationCenter="" 
	manager="jeffreyg" 
	authors="sidneyh" 
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/17/2015" 
	ms.author="sidneyh"/>

# 탄력적 데이터베이스 클라이언트 라이브러리에 대한 자격 증명 관리

[탄력적 데이터베이스 클라이언트 라이브러리](http://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/)는 여러 작업 유형에 대한 자격 증명, 특히 [분할된 데이터베이스 맵 관리자](sql-database-elastic-scale-shard-map-management.md) 만들기 또는 조작, 기존 분할된 데이터베이스 맵 관리자를 참조하여 분할된 데이터베이스에 대한 정보 가져오기, 분할된 데이터베이스에 대한 연결을 사용합니다. 이러한 유형의 작업에 대한 자격 증명은 아래에서 설명합니다.


* **분할된 데이터베이스에 대한 액세스를 매핑할 자격증명**: 관리 자격 증명은 분할된 데이터베이스 맵을 조작하는 응용 프로그램에 대한 **분할된 데이터베이스 맵 관리자** 개체를 인스턴스화할 때 사용됩니다. 탄력적인 확장 API의 사용자가 필요한 SQL 사용자 및 SQL 로그인을 만들어야 하고 전역 분할된 데이터베이스 맵 데이터베이스 및 모든 분할된 데이터베이스에 대한 읽기/쓰기 권한이 부여되어 있는지 확인해야 합니다. 이러한 자격 증명은 분할된 데이터베이스 맵에 대한 변경을 수행할 때 전역 분할된 데이터베이스 맵 및 로컬 분할된 데이터베이스 맵을 유지 관리 하는 데 사용됩니다. 예를 들어 관리 자격 증명을 사용하여 다음 코드와 같이 분할된 데이터베이스 맵 관리자 개체를 인스턴스화할 수 있습니다: 

        // Obtain a shard map manager. 
        ShardMapManager shardMapManager = ShardMapManagerFactory.GetSqlShardMapManager( 
                smmAdminConnectionString, 
                ShardMapManagerLoadPolicy.Lazy 
        ); 


     **smmAdminConnectionString** 변수는 관리 자격 증명을 포함하는 연결 문자열입니다. 사용자 ID와 암호는 분할된 데이터베이스 맵 데이터베이스와 개별 분할된 데이터베이스에 대한 읽기/쓰기 액세스를 제공합니다. 또한 관리 연결 문자열은 전역 분할된 데이터베이스 맵 데이터베이스를 식별하는 서버 이름과 데이터베이스 이름을 포함합니다. 다음은 해당 용도를 위한 일반적인 연결 문자열입니다.

        "Server=<yourserver>.database.windows.net;Database=<yourdatabase>;User ID=<yourmgmtusername>;Password=<yourmgmtpassword>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30;” 

     "username@server" 형식의 사용자 ID 값을 사용하지 말고 "username"만 사용하세요. 자격 증명은 분할된 데이터베이스 맵 관리자 데이터베이스와 각기 다른 서버에 있을 수 있는 개별 분할된 데이터베이스에 대해 모두 작동해야 하기 때문입니다.
     
* **분할된 데이터베이스 맵 관리자 액세스에 대한 관리자 자격증명**: 분할된 데이터베이스 맵을 관리하지 않을 응용 프로그램에서 분할된 데이터베이스 맵 관리자를 인스턴스화할 때는 전역 분할된 데이터베이스 맵에 대한 읽기 전용 권한이 있는 자격 증명을 사용합니다. 이러한 자격 증명으로 전역 분할된 데이터베이스 맵에서 검색한 정보는 [데이터 종속 라우팅](sql-database-elastic-scale-data-dependent-routing.md)에 사용되며, 클라이언트의 분할된 데이터베이스 맵 캐시를 채우는 데에도 사용됩니다. 자격 증명은 위에 표시된 것처럼 **GetSqlShardMapManager**에 대한 동일한 호출 패턴을 통해 제공됩니다.
 
        // Obtain shard map manager. 
        ShardMapManager shardMapManager = ShardMapManagerFactory.GetSqlShardMapManager( 
                smmReadOnlyConnectionString, 
                ShardMapManagerLoadPolicy.Lazy
        );  

     **관리자가 아닌** 사용자를 대신하여 이 액세스에 대한 서로 다른 자격 증명의 사용을 반영하기 위해 **smmReadOnlyConnectionString**을 사용합니다. 이러한 자격 증명은 전역 분할된 데이터베이스 맵에 대한 쓰기 권한을 제공하지 않아야 하기 때문입니다.

* **사용자 데이터 접근을 위한 사용자 자격증명**: **OpenConnectionForKey** 메서드를 사용하여 키와 연결된 분할된 데이터베이스에 액세스할 때는 추가 자격 증명이 필요합니다. 이러한 자격 증명은 분할된 데이터베이스에 있는 로컬 분할된 데이터베이스 맵 테이블에 대한 읽기 전용 액세스 권한을 제공해야 합니다. 이 권한은 분할된 데이터베이스의 데이터 종속 라우팅에 대한 연결 유효성 검사를 수행하는 데 필요합니다. 다음 코드 조각은 데이터 종속 라우팅의 컨텍스트에서 사용자 데이터 액세스에 대한 사용자 자격 증명의 사용을 보여 줍니다.
 
        using (SqlConnection conn = rangeMap.OpenConnectionForKey<int>( 
        targetWarehouse, smmUserConnectionString, ConnectionOptions.Validate)) 

    이 예제에서 **smmUserConnectionString**에는 사용자 자격 증명의 연결 문자열이 저장됩니다. Azure SQL DB의 경우 사용자 자격 증명에 대한 일반적인 연결 문자열은 다음과 같습니다.

        "User ID=<yourusername>; Password=<youruserpassword>; Trusted_Connection=False; Encrypt=True; Connection Timeout=30;”  

    관리 자격 증명과 마찬가지로 "username@server" 형식의 사용자 ID 값을 사용하지 말고 "username"만 사용하세요. 또한 서버 이름과 데이터베이스 이름은 연결 문자열에 포함되지 않습니다. **OpenConnectionForKey** 호출은 키에 따라 올바른 분할된 데이터베이스로 연결을 자동으로 지정하기 때문입니다. 따라서 서버 이름과 데이터베이스 이름을 제공하지 않아야 합니다.

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]
 

<!---HONumber=July15_HO4-->