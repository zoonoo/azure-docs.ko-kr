---
title: 관계를 사용하여 쌍 그래프 관리
titleSuffix: Azure Digital Twins
description: 디지털 쌍을 관계와 연결 하 여 그래프를 관리 하는 방법을 참조 하세요.
author: baanders
ms.author: baanders
ms.date: 4/10/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 7f7239e0c13478af712d8e8d9dad8fda23fe42c7
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/24/2020
ms.locfileid: "87125535"
---
# <a name="manage-a-graph-of-digital-twins-using-relationships"></a>관계를 사용 하 여 디지털 쌍의 그래프 관리

Azure Digital Twins의 핵심은 전체 환경을 나타내는 쌍 [그래프](concepts-twins-graph.md) 입니다. 쌍 그래프는 **관계**를 통해 연결 되는 개별 디지털 쌍으로 구성 됩니다.

[Azure digital 쌍 인스턴스가](how-to-set-up-instance-scripted.md) 작동 하 고 클라이언트 앱에서 [인증](how-to-authenticate-client.md) 코드를 설정한 후에는 [**DigitalTwins api**](how-to-use-apis-sdks.md) 를 사용 하 여 azure digital 쌍 인스턴스에서 디지털 쌍 및 해당 관계를 생성, 수정 및 삭제할 수 있습니다. [.Net (c #) SDK](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core)또는 [Azure DIGITAL twins CLI](how-to-use-cli.md)를 사용할 수도 있습니다.

이 문서에서는 관계와 그래프를 전체적으로 관리 하는 방법을 집중적으로 설명 합니다. 개별 디지털 쌍으로 작업 하려면 [*방법: 디지털 쌍 관리*](how-to-manage-twin.md)를 참조 하세요.

[!INCLUDE [visualizing with Azure Digital Twins explorer](../../includes/digital-twins-visualization.md)]

## <a name="create-relationships"></a>관계 만들기

관계는 서로 다른 디지털 쌍이 서로 연결 되는 방법을 설명 하며,이는 쌍 그래프의 기본을 형성 합니다.

관계는 호출을 사용 하 여 생성 됩니다 `CreateRelationship` . 

관계를 만들려면 다음을 지정 해야 합니다.
* 원본 쌍 ID (관계가 시작 되는 쌍)
* 대상 쌍 ID (관계가 도착 하는 쌍)
* 관계 이름
* 관계 ID

관계 ID는 지정 된 원본 쌍 내에서 고유 해야 합니다. 전역적으로 고유할 필요는 없습니다.
예를 들어 쌍 *foo*의 경우 각 특정 관계 ID는 고유 해야 합니다. 그러나 다른 쌍 *막대* 에는 동일한 *foo* 관계 ID와 일치 하는 나가는 관계가 있을 수 있습니다. 

다음 코드 샘플에서는 Azure Digital Twins 인스턴스에 관계를 추가 하는 방법을 보여 줍니다.

```csharp
public async static Task CreateRelationship(DigitalTwinsClient client, string srcId, string targetId)
{
    var relationship = new BasicRelationship
    {
        TargetId = targetId,
        Name = "contains"
    };

    try
    {
        string relId = $"{srcId}-contains->{targetId}";
        await client.CreateRelationshipAsync(srcId, relId, JsonSerializer.Serialize(relationship));
        Console.WriteLine("Created relationship successfully");
    }
    catch (RequestFailedException rex) {
        Console.WriteLine($"Create relationship error: {rex.Status}:{rex.Message}");
    }
}
```

도우미 클래스에 대 한 자세한 내용은 `BasicRelationship` [*방법: Azure Digital Twins Api 및 sdk 사용*](how-to-use-apis-sdks.md)을 참조 하세요.

## <a name="list-relationships"></a>관계 목록

그래프에서 지정 된 쌍의 관계 목록에 액세스 하려면 다음을 사용할 수 있습니다.

```csharp
await client.GetRelationshipsAsync(id);
```

이는 `Azure.Pageable<T>` `Azure.AsyncPageable<T>` 호출의 동기 또는 비동기 버전을 사용 하는지 여부에 따라 또는를 반환 합니다.

다음은 관계 목록을 검색 하는 전체 예제입니다.

```csharp
public async Task<List<BasicRelationship>> FindOutgoingRelationshipsAsync(string dtId)
{
    // Find the relationships for the twin
    try
    {
        // GetRelationshipsAsync will throw if an error occurs
        AsyncPageable<string> relsJson = client.GetRelationshipsAsync(dtId);
        List<BasicRelationship> results = new List<BasicRelationship>();
        await foreach (string relJson in relsJson)
        {
            var rel = System.Text.Json.JsonSerializer.Deserialize<BasicRelationship>(relJson);
            results.Add(rel);
        }
        return results;
    }
    catch (RequestFailedException ex)
    {
        Log.Error($"*** Error {ex.Status}/{ex.ErrorCode} retrieving relationships for {dtId} due to {ex.Message}");
        return null;
    }
}
```

검색 된 관계를 사용 하 여 그래프의 다른 쌍으로 이동할 수 있습니다. 이렇게 하려면 `target` 반환 되는 관계에서 필드를 읽고에 대 한 다음 호출의 ID로 사용 `GetDigitalTwin` 합니다. 

### <a name="find-relationships-to-a-digital-twin"></a>디지털 쌍에 대 한 관계 찾기

Azure Digital Twins에는 지정 된 쌍으로 들어오는 모든 관계를 찾을 수 있는 API도 있습니다. 이는 역방향 탐색 이나 쌍을 삭제할 때 유용 합니다.

이전 코드 샘플은 나가는 관계를 찾는 데 중점을 두었습니다. 다음 예는 유사 하지만 들어오는 관계를 대신 찾습니다. 또한 찾은 후 삭제 합니다.

`IncomingRelationship`호출은 관계의 전체 본문을 반환 하지 않습니다.

```csharp
async Task<List<IncomingRelationship>> FindIncomingRelationshipsAsync(string dtId)
{
    // Find the relationships for the twin
    try
    {
        // GetRelationshipsAsync will throw an error if a problem occurs
        AsyncPageable<IncomingRelationship> incomingRels = client.GetIncomingRelationshipsAsync(dtId);

        List<IncomingRelationship> results = new List<IncomingRelationship>();
        await foreach (IncomingRelationship incomingRel in incomingRels)
            results.Add(incomingRel);
    }
    catch (RequestFailedException ex)
    {
        Log.Error($"*** Error {ex.Status}/{ex.ErrorCode} retrieving incoming relationships for {dtId} due to {ex.Message}");
    }
}
```

## <a name="delete-relationships"></a>관계 삭제

을 사용 하 여 관계를 삭제할 수 있습니다 `DeleteRelationship(source, relId);` .

첫 번째 매개 변수는 원본 쌍 (관계가 시작 되는 쌍)을 지정 합니다. 다른 매개 변수는 관계 ID입니다. 관계 Id는 쌍의 범위 내 에서만 고유 하므로 쌍 ID와 관계 ID가 모두 필요 합니다.

## <a name="create-a-twin-graph"></a>쌍 그래프 만들기 

다음 코드 조각에서는이 문서의 관계 작업을 사용 하 여 디지털 쌍 및 관계에서 쌍으로 된 쌍의 그래프를 만듭니다.

```csharp
static async Task CreateTwins()
{
    // Create twins - see utility functions below 
    await CreateRoom("Room01", 68, 50, false, "");
    await CreateRoom("Room02", 70, 66, true, "EId-00124");
    await CreateFloorOrBuilding("Floor01", makeFloor:true);

    // Create relationships
    await AddRelationship("Floor01", "contains", "Floor-to-Room01", "Room01");
    await AddRelationship("Floor01", "contains", "Floor-to-Room02", "Room02");
}

static async Task<bool> AddRelationship(string source, string relationship, string id, string target)
{
    var relationship = new BasicRelationship
    {
        TargetId = target,
        Name = relationship
    };

    try
    {
        string relId = $"{source}-contains->{target}";
        await client.CreateRelationshipAsync(source, relId, JsonSerializer.Serialize(relationship));
        Console.WriteLine("Created relationship successfully");
        return true;
    }
    catch (RequestFailedException rex) {
        Console.WriteLine($"Create relationship error: {rex.Status}:{rex.Message}");
        return false;
    }
}

static async Task<bool> CreateRoom(string id, double temperature, double humidity)
{
    BasicDigitalTwin twin = new BasicDigitalTwin();
    twin.Metadata = new DigitalTwinMetadata();
    twin.Metadata.ModelId = "dtmi:com:contoso:Room;2";
    // Initialize properties
    Dictionary<string, object> props = new Dictionary<string, object>();
    props.Add("Temperature", temperature);
    props.Add("Humidity", humidity);
    twin.CustomProperties = props;
    
    try
    {
        client.CreateDigitalTwin(id, JsonSerializer.Serialize<BasicDigitalTwin>(twin)); 
        return true;       
    }
    catch (ErrorResponseException e)
    {
        Console.WriteLine($"*** Error creating twin {id}: {e.Response.StatusCode}"); 
        return false;
    }
}

static async Task<bool> CreateFloorOrBuilding(string id, bool makeFloor=true)
{
    string type = "dtmi:com:contoso:Building;3";
    if (makeFloor==true)
        type = "dtmi:com:contoso:Floor;2";
    BasicDigitalTwin twin = new BasicDigitalTwin();
    twin.Metadata = new DigitalTwinMetadata();
    twin.Metadata.ModelId = type;
    // Initialize properties
    Dictionary<string, object> props = new Dictionary<string, object>();
    props.Add("AverageTemperature", 0);
    twin.CustomProperties = props;
    
    try
    {
        client.CreateDigitalTwin(id, JsonSerializer.Serialize<BasicDigitalTwin>(twin));  
        return true;      
    }
    catch (ErrorResponseException e)
    {
        Console.WriteLine($"*** Error creating twin {id}: {e.Response.StatusCode}"); 
        return false;
    }
}
```

### <a name="create-a-twin-graph-from-a-spreadsheet"></a>스프레드시트에서 쌍 그래프 만들기

실제로 사용 하는 경우에는 다른 데이터베이스 또는 스프레드시트에 저장 된 데이터에서 쌍 계층이 생성 되는 경우가 많습니다. 이 섹션에서는 스프레드시트를 구문 분석 하는 방법을 보여 줍니다.

만들 일련의 디지털 쌍 및 관계를 설명 하는 다음 데이터 테이블을 고려 합니다.

| 모델    | ID | Parent | 관계 이름 | 기타 데이터 |
| --- | --- | --- | --- | --- |
| floor    | Floor01 | | | … |
| 객실    | Room10 | Floor01 | contains | … |
| 객실    | Room11 | Floor01 | contains | … |
| 객실    | Room12 | Floor01 | contains | … |
| floor    | Floor02 | | | … |
| 객실    | Room21 | Floor02 | contains | … |
| 객실    | Room22 | Floor02 | contains | … |

다음 코드는 [MICROSOFT GRAPH API](https://docs.microsoft.com/graph/overview) 를 사용 하 여 스프레드시트를 읽고 결과에서 Azure Digital twins 쌍 그래프를 구성 합니다.

```csharp
var range = msftGraphClient.Me.Drive.Items["BuildingsWorkbook"].Workbook.Worksheets["Building"].usedRange;
JsonDocument data = JsonDocument.Parse(range.values);
List<BasicRelationship> RelationshipRecordList = new List<BasicRelationship>();
foreach (JsonElement row in data.RootElement.EnumerateArray())
{
    string type = row[0].GetString();
    string id = row[1].GetString();
    string relSource = row[2].GetString();
    string relName = row[3].GetString();
    // Parse spreadsheet extra data into a JSON string to initialize the digital twin
    // Left out for compactness
    Dictionary<string, object> initData = new Dictionary<string, object>() { ... };

    if (relSource != null)
    {
        BasicRelationship br = new BasicRelationship()
        {
            SourceId = relSource,
            TargetId = id,
            Name = relName
        };
        RelationshipRecordList.Add(br);
    }

    BasicDigitalTwin twin = new BasicDigitalTwin();
    twin.CustomProperties = initData;
    // Set the type of twin to be created
    switch (type)
    {
        case "room":
            twin.Metadata = new DigitalTwinMetadata() { ModelId = "dtmi:com:contoso:Room;2" };
            break;
        case "floor":
            twin.Metadata = new DigitalTwinMetadata() { ModelId = "dtmi:com:contoso:Floor;2" };
            break;
        ... handle additional types
    }
    try
    {
        client.CreateDigitalTwin(id, JsonSerializer.Serialize<BasicDigitalTwin>(twin));
    }
    catch (RequestFailedException e)
    {
        Log.Error($"Error {e.Status}: {e.Message}");
    }
    foreach (BasicRelationship rec in RelationshipRecordList)
    { 
        try { 
            client.CreateRelationship(rec.SourceId, Guid.NewGuid().ToString(), JsonSerializer.Serialize<BasicRelationship>(rec));
        }
        catch (RequestFailedException e)
        {
            Log.Error($"Error {e.Status}: {e.Message}");
        }
    }
}
```
## <a name="manage-relationships-with-cli"></a>CLI를 사용 하 여 관계 관리

또한 Azure Digital Twins CLI를 사용 하 여 쌍 및 해당 관계를 관리할 수 있습니다. 명령은 [*방법: Azure Digital Twins CLI 사용*](how-to-use-cli.md)에서 찾을 수 있습니다.

## <a name="next-steps"></a>다음 단계

Azure Digital Twins 쌍 그래프를 쿼리 하는 방법에 대해 알아봅니다.
* [*개념: 쿼리 언어*](concepts-query-language.md)
* [*방법: 쌍 그래프 쿼리*](how-to-query-graph.md)