<properties 
	pageTitle="분할된 데이터베이스 맵 관리 | Microsoft Azure" 
	description="ShardMapManager 및 .NET용 탄력적 데이터베이스를 사용하는 방법" 
	services="sql-database" 
	documentationCenter="" 
	manager="jhubbard" 
	authors="ddove" 
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/25/2016" 
	ms.author="ddove"/>

# 분할된 데이터베이스 맵 관리자를 사용하여 데이터베이스 확장

SQL Azure에서 데이터베이스를 쉽게 확장하려면 분할된 데이터베이스 맵 관리자를 사용합니다. 분할된 데이터베이스 맵 관리자는 분할된 데이터베이스 집합에서 모든 분할된 데이터베이스(데이터베이스)에 대한 전역 매핑 정보를 유지 관리하는 특수한 데이터베이스입니다. 메타데이터를 사용하면 응용 프로그램을 **분할 키**의 값에 따라 올바른 데이터베이스에 연결할 수 있습니다. 또한 집합에 있는 모든 분할된 데이터베이스은 로컬 분할된 데이터베이스 데이터를 추적하는 맵을 포함합니다(**shardlet**라고도 함).

![분할된 데이터베이스 맵 관리](./media/sql-database-elastic-scale-shard-map-management/glossary.png)

분할된 데이터베이스 맵 관리를 위해서는 이 맵의 구성을 이해하는 것이 필수적입니다. [탄력적 데이터베이스 클라이언트 라이브러리](sql-database-elastic-database-client-library.md)에 있는 [ShardMapManager 클래스](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.aspx)를 사용하여 분할된 데이터베이스 맵을 관리합니다.


## 분할된 데이터베이스 맵 및 분할된 데이터베이스 매핑

각 분할된 데이터베이스에 만들 분할된 데이터베이스 맵의 종류를 선택해야 합니다. 데이터베이스 아키텍처에 따라 선택합니다.

1. 데이터베이스당 단일 테넌트  
2. 데이터베이스당 다중 테넌트(두 가지 형식):
	3. 목록 매핑
	4. 범위 매핑
 
단일 테넌트 모델은 **목록 매핑** 분할된 데이터베이스 맵을 만듭니다. 단일 테넌트 모델은 테넌트당 하나의 데이터베이스를 할당합니다. 관리를 단순화하므로 SaaS 개발자에게 효과적인 모델입니다.

![목록 매핑][1]

다중 테넌트 모델은 단일 데이터베이스에 여러 테넌트를 할당합니다(또한 테넌트 그룹을 여러 데이터베이스에 배포할 수 있음). 각 테넌트에 데이터 요구가 적다고 예상되는 경우 이 모델을 사용합니다. 이 모델에서 **범위 매핑**을 사용하여 데이터베이스에 테넌트의 범위를 할당합니다.
 

![범위 매핑][2]

또는 다중 테넌트를 단일 데이터베이스에 할당하는 *목록 매핑*을 사용하여 다중 테넌트 데이터베이스 모델을 구현할 수 있습니다. 예를 들어 DB1은 테넌트 ID 1과 5에 대한 정보를 저장하는 데 사용하고 DB2는 테넌트 7과 테넌트 10의 데이터를 저장합니다.

![단일 DB의 다중 테넌트][3]
 
### 분할 키에 대해 지원되는 .Net 형식

탄력적인 확장은 분할 키로 다음의 .NET Framework 형식을 지원합니다.

* 정수
* long
* GUID
* 바이트  
* datetime
* TimeSpan
* datetimeoffset

### 목록 및 범위 분할된 데이터베이스 맵
분할된 데이터베이스 맵은 **개별 분할 키 값의 목록**이나 **분할 키 값의 범위**를 사용하여 생성할 수 있습니다.

###목록 분할된 데이터베이스 맵
**분할된 데이터베이스** 에는 **shardlet**이 포함되며, 분할된 데이터베이스에 대한 shardlet 매핑은 분할된 데이터베이스 맵을 통해 유지 관리됩니다. **목록 분할된 데이터베이스 맵**은 shardlet을 식별하는 개별 키 값과 분할된 데이터베이스로 사용되는 데이터베이스 간의 연결입니다. **목록 매핑**은 동일한 데이터베이스에 매핑될 수 있는 명시적이고 서로 다른 키 값입니다. 예를 들어 키 1은 데이터베이스 A에 매핑되고 키 값 3과 6은 모두 데이터베이스 B를 참조합니다.

| 키 | 분할된 데이터베이스 위치 |
|-----|----------------|
| 1 | Database\_A |
| 3 | Database\_B |
| 4 | Database\_C |
| 6 | Database\_B |
| ... | ... |
 

### 범위 분할된 데이터베이스 맵 
**범위 분할된 데이터베이스 맵**에서 키 범위는 한 쌍**[낮은 값, 높은 값)**으로 기술됩니다. 여기서 *낮은 값*은 범위에서 최소 키이고 *높은 값*은 범위보다 높은 첫 번째 값입니다.

예를 들어 **[0, 100)**에는 0 이상 100 미만의 모든 정수가 포함됩니다. 여러 범위가 동일한 데이터베이스를 가리킬 수 있으며 연결되지 않은 범위도 지원됩니다. 예를 들어 아래 예제에서 [100, 200) 및 400, 600)은 모두 데이터베이스 C를 가리킵니다.

| 키 | 분할된 데이터베이스 위치 |
|-----------|----------------|
| [1,50) | Database\_A |
| [50,100) | Database\_B |
| [100,200) | Database\_C |
| [400,600) | Database\_C |
| ... | ...            

위에 나와 있는 각 테이블은 **ShardMap **개체의 개념적 예제입니다. 각 행은 개별 **PointMapping**(목록 분할된 데이터베이스 맵의 경우) 또는 **RangeMapping**(범위 분할된 데이터베이스 맵의 경우) 개체에 대한 단순한 예제입니다.

## 분할된 데이터베이스 맵 관리자 

클라이언트 라이브러리에서 분할된 데이터베이스 맵 관리자는 분할된 데이터베이스 맵의 컬렉션입니다. **ShardMapManager** 인스턴스를 통해 관리되는 데이터는 다음 세 개 위치에 저장됩니다.

1. **GSM(전역 분할된 데이터베이스 맵)**: 해당 분할된 데이터베이스 맵 및 매핑 모두에 대한 리포지토리 역할을 할 데이터베이스를 지정합니다. 정보를 관리할 특수 테이블 및 저장된 프로시저는 자동으로 생성됩니다. 일반적으로 작은 데이터베이스이며 손쉽게 액세스할 수 있지만 응용 프로그램의 다른 요구 사항에는 사용할 수 없습니다. 테이블은 **\_\_ShardManagement**라는 특수한 스키마에 있습니다.

2. **LSM(로컬 분할된 데이터베이스 맵)**: 분할된 데이터베이스로 지정하는 모든 데이터베이스는 해당 분할된 데이터베이스와 관련된 분할된 데이터베이스 맵 정보를 포함하고 유지하는 여러 개의 작은 테이블 및 특수 저장 프로시저를 포함하도록 수정됩니다. 이 정보는 GSM의 정보와 중복되지만 그렇기 때문에 응용 프로그램에서 GSM에 어떤 부하도 주지 않으면서 캐시된 분할된 데이터베이스 맵 정보의 유효성을 검사할 수 있습니다. 응용 프로그램은 LSM을 사용하여 캐시된 매핑이 여전히 유효한지 여부를 확인합니다. 각 분할된 데이터베이스의 LSM에 해당하는 테이블은 **\_\_ShardManagement** 스키마에도 있습니다.

3. **Application cache**: **ShardMapManager** 개체에 액세스하는 각 응용 프로그램 인스턴스는 해당 매핑의 로컬 메모리 내 캐시를 유지 관리합니다. 이 캐시는 최근에 검색된 라우팅 정보를 저장합니다.

## ShardMapManager 생성

**ShardMapManager** 개체는 [팩터리](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.aspx) 패턴을 사용하여 생성됩니다. **[ShardMapManagerFactory.GetSqlShardMapManager](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.getsqlshardmapmanager.aspx)** 메서드는 **ConnectionString** 형식으로 자격 증명(GSM이 저장되는 데이터베이스 이름과 서버 이름 포함)을 가져오고 **ShardMapManager** 인스턴스를 반환합니다.

**참고:** **ShardMapManager**는 응용 프로그램용 초기화 코드 내에서 앱 도메인별로 한 번만 인스턴스화해야 합니다. 동일한 AppDomain에서 ShardMapManager의 추가 인스턴스를 만들면 응용 프로그램의 메모리와 CPU 사용률이 증가합니다. **ShardMapManager**는 분할된 데이터베이스 맵을 개수와 관계없이 포함할 수 있습니다. 많은 응용 프로그램의 경우 단일 분할된 데이터베이스 맵으로 충분할 수 있지만 서로 다른 스키마에 대해서 또는 고유성을 위해서는 서로 다른 데이터베이스 집합이 사용되며 이러한 경우 다중 분할된 데이터베이스 맵을 사용하는 것이 좋습니다.

이 코드에서는 응용 프로그램이 [TryGetSqlShardMapManager 메서드](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.trygetsqlshardmapmanager.aspx)를 사용하여 기존 **ShardMapManager**를 열려고 합니다. GSM(전역 **ShardMapManager**)을 나타내는 개체가 아직 데이터베이스에 없는 경우 클라이언트 라이브러리에서 [CreateSqlShardMapManager 메서드](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.createsqlshardmapmanager.aspx)를 사용하여 해당 개체를 자동으로 만듭니다.

    // Try to get a reference to the Shard Map Manager 
 	// via the Shard Map Manager database.  
    // If it doesn't already exist, then create it. 
    ShardMapManager shardMapManager; 
    bool shardMapManagerExists = ShardMapManagerFactory.TryGetSqlShardMapManager(
                                        connectionString, 
                                        ShardMapManagerLoadPolicy.Lazy, 
                                        out shardMapManager); 

    if (shardMapManagerExists) 
     { 
        Console.WriteLine("Shard Map Manager already exists");
    } 
    else
    {
        // Create the Shard Map Manager. 
        ShardMapManagerFactory.CreateSqlShardMapManager(connectionString);
        Console.WriteLine("Created SqlShardMapManager"); 

        shardMapManager = ShardMapManagerFactory.GetSqlShardMapManager(
            connectionString, 
            ShardMapManagerLoadPolicy.Lazy);

        // The connectionString contains server name, database name, and admin credentials 
        // for privileges on both the GSM and the shards themselves.
    } 
 
대신 Powershell을 사용하여 새 분할된 데이터베이스 맵 관리자를 만들 수 있습니다. [여기](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-DB-Elastic-731883db)에 예제가 있습니다.

## RangeShardMap 또는 ListShardMap 가져오기

분할된 데이터베이스 맵 관리자를 만든 후 [TryGetRangeShardMap](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.trygetrangeshardmap.aspx), [TryGetListShardMap](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.trygetlistshardmap.aspx) 또는 [GetShardMap](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.getshardmap.aspx) 메서드를 사용하여 [RangeShardMap](https://msdn.microsoft.com/library/azure/dn807318.aspx) 또는 [ListShardMap](https://msdn.microsoft.com/library/azure/dn807370.aspx)을 가져올 수 있습니다.

	/// <summary>
    /// Creates a new Range Shard Map with the specified name, or gets the Range Shard Map if it already exists.
    /// </summary>
    public static RangeShardMap<T> CreateOrGetRangeShardMap<T>(ShardMapManager shardMapManager, string shardMapName)
    {
        // Try to get a reference to the Shard Map.
        RangeShardMap<T> shardMap;
        bool shardMapExists = shardMapManager.TryGetRangeShardMap(shardMapName, out shardMap);

        if (shardMapExists)
        {
            ConsoleUtils.WriteInfo("Shard Map {0} already exists", shardMap.Name);
        }
        else
        {
            // The Shard Map does not exist, so create it
            shardMap = shardMapManager.CreateRangeShardMap<T>(shardMapName);
            ConsoleUtils.WriteInfo("Created Shard Map {0}", shardMap.Name);
        }

        return shardMap;
    } 

### 분할된 데이터베이스 맵 관리 자격 증명

분할된 데이터베이스 맵을 관리하고 조작하는 응용 프로그램은 분할된 데이터베이스 맵을 사용하여 연결을 라우트하는 응용 프로그램과 다릅니다.

분할된 데이터베이스, 분할된 데이터베이스 맵, 분할된 데이터베이스 매핑 등을 추가/변경하는 등 분할된 데이터베이스 맵을 관리하려는 경우 **ShardMapManager**를 인스턴스화해야 하며, 이때 **GSM 데이터베이스 및 분할된 데이터베이스로 사용되는 각 데이터베이스 모두에 대해 읽기/쓰기 권한이 있는 자격 증명**을 사용해야 합니다. 이 자격 증명은 분할된 데이터베이스 맵 정보를 입력하거나 변경할 뿐만 아니라 새로운 분할된 데이터베이스에 LSM 테이블을 생성할 때 GSM 및 LSM에서 테이블에 쓸 수 있도록 허용해야 합니다.

[탄력적 데이터베이스 클라이언트 라이브러리에 액세스하는 데 사용되는 자격 증명](sql-database-elastic-scale-manage-credentials.md)을 참조하세요.

### 영향을 받는 메타데이터만 

**ShardMapManager** 데이터를 채우거나 변경하는 데 사용되는 메서드는 분할된 데이터베이스 자체에 저장된 사용자 데이터를 변경하지 않습니다. 예를 들어 **CreateShard**, **DeleteShard**, **UpdateMapping** 등의 메서드는 분할된 데이터베이스 맵 메타데이터에만 적용됩니다. 분할된 데이터베이스에 포함된 사용자 데이터를 제거, 추가 또는 변경하지 않습니다. 대신, 이러한 메서드는 실제 데이터베이스를 생성 또는 제거하기 위해 수행하는 개별 작업 또는 분할된 환경을 리밸런스하기 위해 분할된 데이터베이스 간에 행을 이동하는 개별 작업과 함께 사용하도록 설계되었습니다. 탄력적 데이터베이스 도구에 포함된 **분할-병합** 도구는 분할된 데이터베이스 간의 실제 데이터 이동을 조정하면서 이러한 API를 사용합니다. [탄력적 데이터베이스 분할/병합 도구를 사용하여 확장하기](sql-database-elastic-scale-overview-split-and-merge.md).를 참조하세요.

## 분할된 데이터베이스 맵 채우기 예제
 
특정 분할된 데이터베이스 맵을 채우는 작업의 예제 시퀀스는 아래와 같습니다. 이 코드는 다음 단계를 수행합니다.

1. 새로운 분할된 데이터베이스 맵이 분할된 데이터베이스 맵 관리자 내에 생성됩니다. 
2. 서로 다른 두 개의 분할된 데이터베이스용 메타데이터가 분할된 데이터베이스 맵에 추가됩니다. 
3. 다양한 키 범위 매핑이 추가되고 분할된 데이터베이스 맵의 전체 내용이 표시됩니다. 

오류가 발생하면 메서드를 다시 실행할 수 있도록 코드가 작성됩니다. 각 요청은 분할된 데이터베이스 또는 매핑을 만들기 전에 해당 항목이 이미 존재하는지 여부를 테스트합니다. 코드에서는 **sample\_shard\_0**, **sample\_shard\_1** 및 **sample\_shard\_2**라는 데이터베이스가 **shardServer** 문자열로 참조되는 서버에 이미 생성되어 있다고 가정합니다.

    public void CreatePopulatedRangeMap(ShardMapManager smm, string mapName) 
        {            
            RangeShardMap<long> sm = null; 

            // check if shardmap exists and if not, create it 
            if (!smm.TryGetRangeShardMap(mapName, out sm)) 
            { 
                sm = smm.CreateRangeShardMap<long>(mapName); 
            } 

            Shard shard0 = null, shard1=null; 
            // Check if shard exists and if not, 
            // create it (Idempotent / tolerant of re-execute) 
            if (!sm.TryGetShard(new ShardLocation(
	                                 shardServer, 
	                                 "sample_shard_0"), 
	                                 out shard0)) 
            { 
                Shard0 = sm.CreateShard(new ShardLocation(
	                                        shardServer, 
	                                        "sample_shard_0")); 
            } 

            if (!sm.TryGetShard(new ShardLocation(
	                                shardServer, 
	                                "sample_shard_1"), 
	                                out shard1)) 
            { 
                Shard1 = sm.CreateShard(new ShardLocation(
	                                         shardServer, 
	                                        "sample_shard_1"));  
            } 

            RangeMapping<long> rmpg=null; 

            // Check if mapping exists and if not,
            // create it (Idempotent / tolerant of re-execute) 
            if (!sm.TryGetMappingForKey(0, out rmpg)) 
            { 
                sm.CreateRangeMapping(
	                      new RangeMappingCreationInfo<long>
	                      (new Range<long>(0, 50), 
	                      shard0, 
	                      MappingStatus.Online)); 
            } 

            if (!sm.TryGetMappingForKey(50, out rmpg)) 
            { 
                sm.CreateRangeMapping(
	                     new RangeMappingCreationInfo<long> 
                         (new Range<long>(50, 100), 
	                     shard1, 
	                     MappingStatus.Online)); 
            } 

            if (!sm.TryGetMappingForKey(100, out rmpg)) 
            { 
                sm.CreateRangeMapping(
	                     new RangeMappingCreationInfo<long>
                         (new Range<long>(100, 150), 
	                     shard0, 
	                     MappingStatus.Online)); 
            } 

            if (!sm.TryGetMappingForKey(150, out rmpg)) 
            { 
                sm.CreateRangeMapping(
	                     new RangeMappingCreationInfo<long> 
                         (new Range<long>(150, 200), 
	                     shard1, 
	                     MappingStatus.Online)); 
            } 

            if (!sm.TryGetMappingForKey(200, out rmpg)) 
            { 
               sm.CreateRangeMapping(
	                     new RangeMappingCreationInfo<long> 
                         (new Range<long>(200, 300), 
	                     shard0, 
	                     MappingStatus.Online)); 
            } 

            // List the shards and mappings 
            foreach (Shard s in sm.GetShards()
                         .OrderBy(s => s.Location.DataSource)
                         .ThenBy(s => s.Location.Database))
            { 
               Console.WriteLine("shard: "+ s.Location); 
            } 

            foreach (RangeMapping<long> rm in sm.GetMappings()) 
            { 
                Console.WriteLine("range: [" + rm.Value.Low.ToString() + ":" 
                        + rm.Value.High.ToString()+ ")  ==>" +rm.Shard.Location); 
            } 
        } 
 
또는 PowerShell 스크립트를 사용하여 동일한 결과를 얻을 수 있습니다. [여기](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-DB-Elastic-731883db)에 일부 샘플 PowerShell 예제가 있습니다.

분할된 데이터베이스 맵이 채워진 후에는 해당 맵에 대한 작업을 수행하도록 데이터 액세스 응용 프로그램을 생성하거나 조정할 수 있습니다. **맵 레이아웃**을 변경해야 할 때까지는 맵을 다시 채우거나 조작할 필요가 없습니다.

## 데이터 종속 라우팅 

분할된 데이터베이스 맵 관리자는 앱별 작업을 수행하기 위해 데이터베이스 연결이 필요한 응용 프로그램에서 대부분 사용됩니다. 이러한 연결은 올바른 데이터베이스와 연결되어야 합니다. 이를 **데이터 종속 라우팅**이라고 합니다. 이러한 응용 프로그램의 경우 GSM 데이터베이스에 대한 읽기 전용 액세스 권한이 있는 자격 증명을 사용하여 팩터리의 분할된 데이터베이스 맵 관리자 개체를 인스턴스화합니다. 이후 연결에 대한 개별 요청은 적합한 분할된 데이터베이스에 연결하는 데 필요한 자격 증명을 제공합니다.

읽기 전용 자격 증명으로 열린 **ShardMapManager**를 사용하는 이러한 응용 프로그램은 맵 또는 매핑을 변경할 수 없습니다. 이러한 요구 사항을 위해, 앞서 설명한 대로 더 높은 권한의 자격 증명을 제공하는 PowerShell 스크립트 또는 관리별 응용 프로그램을 만듭니다. [탄력적 데이터베이스 클라이언트 라이브러리 액세스에 사용되는 자격 증명](sql-database-elastic-scale-manage-credentials.md)을 참조하세요.

자세한 내용은 [데이터 종속 라우팅](sql-database-elastic-scale-data-dependent-routing.md)을 참조하세요.

## 분할된 데이터베이스 맵 수정 

분할된 데이터베이스 맵은 여러 방법으로 변경할 수 있습니다. 다음 메서드 모두가 분할된 데이터베이스 및 해당 매핑을 기술하는 메타데이터를 수정하지만 분할된 데이터베이스 내에서 데이터를 실제로 수정하지 않으며 실제 데이터베이스를 만들거나 삭제하지 않습니다. 아래에서 설명하는 분할된 데이터베이스 맵에 대한 일부 작업은 데이터를 실제로 이동하거나 분할된 데이터베이스로 사용되는 데이터베이스를 추가 및 제거하는 관리 작업으로 조정해야 할 수 있습니다.

이러한 메서드는 분할된 데이터베이스 환경에서 데이터의 전반적인 분산을 수정하기 위해 사용할 수 있는 구성 요소로 함께 작동합니다.

* 분할된 데이터베이스를 추가하거나 제거하려면 [Shardmap 클래스](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.aspx)의 **[CreateShard](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.createshard.aspx)** 및 **[DeleteShard](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.deleteshard.aspx)**를 사용합니다. 
    
    이러한 작업을 실행하려면 대상 분할된 데이터베이스를 나타내는 서버 및 데이터베이스가 이미 있어야 합니다. 이러한 메서드는 데이터베이스 자체가 아닌 분할된 데이터베이스 맵의 메타데이터에만 적용됩니다.

* 분할된 데이터베이스에 매핑되는 지점 또는 범위를 만들거나 제거하려면 [RangeShardMapping 클래스](https://msdn.microsoft.com/library/azure/dn807318.aspx)의 **[CreateRangeMapping](https://msdn.microsoft.com/library/azure/dn841993.aspx)**, **[DeleteMapping](https://msdn.microsoft.com/library/azure/dn824200.aspx)** 및 [ListShardMap](https://msdn.microsoft.com/library/azure/dn842123.aspx)의 **[CreatePointMapping](https://msdn.microsoft.com/library/azure/dn807218.aspx)**을 사용합니다.
    
    여러 많은 지점 또는 범위를 동일한 분할된 데이터베이스에 매핑할 수 있습니다. 이러한 메서드는 메타데이터에만 영향을 주며, 분할된 데이터베이스에 이미 존재하는 데이터에는 영향을 주지 않습니다. **DeleteMapping** 작업과의 일관성을 위해 데이터베이스에서 데이터를 제거해야 하는 경우 이러한 메서드를 사용하면서 해당 작업을 개별적으로 수행해야 합니다.

* 기존 범위를 둘로 분할하거나 인접한 범위를 하나로 병합하려면 **[SplitMapping](https://msdn.microsoft.com/library/azure/dn824205.aspx)** 및 **[MergeMappings](https://msdn.microsoft.com/library/azure/dn824201.aspx)**를 사용합니다.

    분할 및 병합 작업에서는 **키 값이 매핑되는 분할된 데이터베이스가 변경되지 않습니다**. 분할은 기존 범위를 두 부분으로 나누지만 둘 다를 동일한 분할된 데이터베이스에 매핑된 상태로 둡니다. 병합은 동일한 분할된 데이터베이스에 이미 매핑되어 있는 인접한 두 범위에 대해 작동하여 단일 범위로 결합합니다. 분할된 데이터베이스 간에 지점 또는 범위 자체를 이동할 때는 실제 데이터를 이동함과 동시에 **UpdateMapping**을 사용하여 이동을 조정해야 합니다. 탄력적 데이터베이스 도구의 일부인 **분할/병합** 서비스를 사용하여 이동이 필요할 때 데이터 이동에 맞춰 분할된 데이터베이스 맵 변경을 조정할 수 있습니다.

* 개별 지점 또는 범위를 여러 분할된 데이터베이스로 다시 매핑하거나 이동하려면 **[UpdateMapping](https://msdn.microsoft.com/library/azure/dn824207.aspx)**을 사용합니다.

    **UpdateMapping** 작업과의 일관성을 유지하기 위해 분할된 데이터베이스 간에 데이터를 이동해야 할 수 있으므로, 이러한 메서드를 사용하면서 이동을 개별적으로 수행해야 합니다.

* 매핑을 온라인 및 오프라인으로 설정하려면 **[MarkMappingOffline](https://msdn.microsoft.com/library/azure/dn824202.aspx)** 및 **[MarkMappingOnline](https://msdn.microsoft.com/library/azure/dn807225.aspx)**을 사용하여 매핑의 온라인 상태를 제어합니다.

    **UpdateMapping** 및 **DeleteMapping**을 비롯하여 매핑이 "오프라인" 상태인 경우에만 분할된 데이터베이스 매핑에 대한 특정 작업이 허용됩니다. 매핑이 오프라인 상태이면 해당 매핑에 포함된 특정 키를 기반으로 하는 데이터 종속 요청이 오류를 반환합니다. 또한 범위가 먼저 오프라인으로 전환되면 변경 중인 범위에 대해 전송된 쿼리 결과가 불일치하거나 불완전해지지 않도록 영향 받는 분할된 데이터베이스에 대한 모든 연결이 자동으로 중지됩니다.

매핑은.Net에서 변경할 수 없는 개체입니다. 매핑을 변경하는 위의 모든 메서드는 사용자의 코드 안의 메서드의 모든 참조를 무효화합니다 쉽게 매핑의 상태를 변경 하는 작업의 시퀀스를 수행 하려면 모든 매핑을 변경 하는 메서드의 반환 새 매핑 참조를 작업을 연결할 수 있도록 합니다. 예를 들면, key25가 포함된 shardmap sm의 기존 매핑을 삭제하려면 다음을 실행합니다:

        sm.DeleteMapping(sm.MarkMappingOffline(sm.GetMappingForKey(25)));

## 분할된 데이터베이스 추가 

이미 존재하는 분할된 데이터베이스 맵의 경우 응용 프로그램은 대개 새 키 또는 키 범위에서 예상되는 데이터를 처리할 새로운 분할된 데이터베이스를 추가하기만 하면 됩니다. 예를 들어 테넌트 ID를 기준으로 분할된 응용 프로그램이 새로운 테넌트에 대한 새로운 분할된 데이터베이스를 프로비전해야 할 수 있습니다. 또는 월별로 분할된 데이터의 경우 새로운 달이 시작되기 전에 새로운 분할된 데이터베이스를 프로비전해야 할 수 있습니다.

키 값의 새로운 범위가 기존 매핑에 속하지 않으며 데이터를 이동할 필요가 없는 경우에는 단지 새로운 분할된 데이터베이스를 추가하고 새 키 또는 범위를 해당 분할된 데이터베이스에 연결하면 됩니다. 새로운 분할된 데이터베이스를 추가하는 방법에 대한 자세한 내용은 [새로운 분할된 데이터베이스 추가](sql-database-elastic-scale-add-a-shard.md)를 참조하세요.

그러나 데이터 이동이 필요한 시나리오에서는, 필요한 분할된 데이터베이스 맵 업데이트와 함께 분할된 데이터베이스 간의 데이터 이동을 조정하기 위해 분할/병합 도구가 필요합니다. 분할-병합 도구 사용에 대한 자세한 내용은 [분할-병합 개요](sql-database-elastic-scale-overview-split-and-merge.md)를 참조하세요.

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]
 
<!--Image references-->
[1]: ./media/sql-database-elastic-scale-shard-map-management/listmapping.png
[2]: ./media/sql-database-elastic-scale-shard-map-management/rangemapping.png
[3]: ./media/sql-database-elastic-scale-shard-map-management/multipleonsingledb.png

<!---HONumber=AcomDC_0525_2016-->