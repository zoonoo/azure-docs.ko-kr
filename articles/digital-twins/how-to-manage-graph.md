---
title: 관계를 사용하여 쌍 그래프 관리
titleSuffix: Azure Digital Twins
description: 디지털 쌍을 관계와 연결 하 여 그래프를 관리 하는 방법을 참조 하세요.
author: baanders
ms.author: baanders
ms.date: 10/21/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 82c5c7b2d221cdf10c69e0a8921eef6e6d85e554
ms.sourcegitcommit: 6a902230296a78da21fbc68c365698709c579093
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93356334"
---
# <a name="manage-a-graph-of-digital-twins-using-relationships"></a>관계를 사용 하 여 디지털 쌍의 그래프 관리

Azure Digital Twins의 핵심은 전체 환경을 나타내는 쌍 [그래프](concepts-twins-graph.md) 입니다. 쌍 그래프는 **관계** 를 통해 연결 되는 개별 디지털 쌍으로 구성 됩니다. 

[Azure digital 쌍 인스턴스가](how-to-set-up-instance-portal.md) 작동 하 고 클라이언트 앱에서 [인증](how-to-authenticate-client.md) 코드를 설정한 후에는 [**DigitalTwins api**](/rest/api/digital-twins/dataplane/twins) 를 사용 하 여 azure digital 쌍 인스턴스에서 디지털 쌍 및 해당 관계를 생성, 수정 및 삭제할 수 있습니다. [.Net (c #) SDK](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet&preserve-view=true)또는 [Azure DIGITAL twins CLI](how-to-use-cli.md)를 사용할 수도 있습니다.

이 문서에서는 관계와 그래프를 전체적으로 관리 하는 방법을 집중적으로 설명 합니다. 개별 디지털 쌍으로 작업 하려면 [*방법: 디지털 쌍 관리*](how-to-manage-twin.md)를 참조 하세요.

## <a name="prerequisites"></a>사전 요구 사항

[!INCLUDE [digital-twins-prereq-instance.md](../../includes/digital-twins-prereq-instance.md)]
    
[!INCLUDE [visualizing with Azure Digital Twins explorer](../../includes/digital-twins-visualization.md)]

## <a name="create-relationships"></a>관계 만들기

관계는 서로 다른 디지털 쌍이 서로 연결 되는 방법을 설명 하며,이는 쌍 그래프의 기본을 형성 합니다.

관계는 호출을 사용 하 여 생성 됩니다 `CreateOrReplaceRelationshipAsync()` . 

관계를 만들려면 다음을 지정 해야 합니다.
* 원본 쌍 ID ( `srcId` 아래 코드 샘플에서): 관계가 시작 되는 쌍의 id입니다.
* 대상 쌍 ID ( `targetId` 아래 코드 샘플에서): 관계가 도착 하는 쌍의 id입니다.
* 관계 이름 ( `relName` 아래 코드 샘플에서): 관계의 제네릭 형식 (예: _contains_ )입니다.
* 관계 ID ( `relId` 아래 코드 샘플에서):이 관계에 대 한 특정 이름 (예: _Relationship1_ )입니다.

관계 ID는 지정 된 원본 쌍 내에서 고유 해야 합니다. 전역적으로 고유할 필요는 없습니다.
예를 들어 쌍 *foo* 의 경우 각 특정 관계 ID는 고유 해야 합니다. 그러나 다른 쌍 *막대* 에는 동일한 *foo* 관계 ID와 일치 하는 나가는 관계가 있을 수 있습니다.

다음 코드 샘플에서는 Azure Digital Twins 인스턴스에서 관계를 만드는 방법을 보여 줍니다.

```csharp
public async static Task CreateRelationship(DigitalTwinsClient client, string srcId, string targetId, string relName)
        {
            var relationship = new BasicRelationship
            {
                TargetId = targetId,
                Name = relName
            };

            try
            {
                string relId = $"{srcId}-{relName}->{targetId}";
                await client.CreateOrReplaceRelationshipAsync<BasicRelationship>(srcId, relId, relationship);
                Console.WriteLine($"Created {relName} relationship successfully");
            }
            catch (RequestFailedException rex)
            {
                Console.WriteLine($"Create relationship error: {rex.Status}:{rex.Message}");
            }
            
        }
```
이제 main 메서드에서 함수를 호출 `CreateRelationship()` 하 여 다음과 같이 _contains_ 관계를 만들 수 있습니다. 

```csharp
await CreateRelationship(client, srcId, targetId, "contains");
```
여러 관계를 만들려는 경우에는 동일한 메서드에 대 한 호출을 반복 하 여 다른 관계 형식을 인수에 전달할 수 있습니다. 

도우미 클래스에 대 한 자세한 내용은 `BasicRelationship` [*방법: Azure Digital Twins Api 및 sdk 사용*](how-to-use-apis-sdks.md#serialization-helpers)을 참조 하세요.

### <a name="create-multiple-relationships-between-twins"></a>쌍 간에 여러 관계 만들기

관계는 다음 중 하나로 분류 될 수 있습니다. 

* 나가는 관계: 다른 쌍에 연결 하기 위해 바깥쪽을 가리키는이 쌍에 속하는 관계입니다. 메서드는 쌍 `GetRelationshipsAsync()` 의 나가는 관계를 가져오는 데 사용 됩니다.
* 들어오는 관계: "들어오는" 링크를 만들기 위해이 쌍을 가리키는 다른 쌍에 속하는 관계입니다. 메서드는 쌍 `GetIncomingRelationshipsAsync()` 의 들어오는 관계를 가져오는 데 사용 됩니다.

두 쌍 간에 가질 수 있는 관계 수에 대 한 제한은 없습니다. 즉, 사용자가 원하는 대로 쌍 간에 관계를 가질 수 있습니다. 

즉, 한 번에 두 쌍 간에 여러 유형의 관계를 표현할 수 있습니다. 예를 들어 쌍 *A는* 쌍으로 *저장* 된 관계와 *제조* 관계를 둘 다 가질 수 *있습니다.*

원하는 경우 동일한 두 쌍 사이에 동일한 관계 유형의 인스턴스를 여러 개 만들 수도 있습니다. 이 예에서는 관계가 서로 다른 관계 Id를 사용 하는 *한 쌍 a* 가 쌍 *B* 와 두 개의 다른 *저장* 관계를 가질 수 있습니다.

## <a name="list-relationships"></a>관계 목록

그래프의 지정 된 쌍에 대 한 **나가는** 관계의 목록에 액세스 하려면 다음과 같이 메서드를 사용할 수 있습니다 `GetRelationships()` .

```csharp
await client.GetRelationships()
```

이는 `Azure.Pageable<T>` `Azure.AsyncPageable<T>` 호출의 동기 또는 비동기 버전을 사용 하는지에 따라 또는를 반환 합니다.

관계 목록을 검색 하는 예제는 다음과 같습니다.

```csharp
public static async Task<List<BasicRelationship>> FindOutgoingRelationshipsAsync(DigitalTwinsClient client, string dtId)
        {
            // Find the relationships for the twin
            try
            {
                // GetRelationshipsAsync will throw if an error occurs
                AsyncPageable<BasicRelationship> rels = client.GetRelationshipsAsync<BasicRelationship>(dtId);
                List<BasicRelationship> results = new List<BasicRelationship>();
                await foreach (BasicRelationship rel in rels)
                {
                    results.Add(rel);
                    Console.WriteLine($"Found relationship-{rel.Name}->{rel.TargetId}");
                }

                return results;
            }
            catch (RequestFailedException ex)
            {
                Console.WriteLine($"**_ Error {ex.Status}/{ex.ErrorCode} retrieving relationships for {dtId} due to {ex.Message}");
                return null;
            }
        }

```
이제이 메서드를 호출 하 여 다음과 같이 쌍의 나가는 관계를 확인할 수 있습니다.

```csharp
await FindOutgoingRelationshipsAsync(client, twin_Id);
```
검색 된 관계를 사용 하 여 그래프의 다른 쌍으로 이동할 수 있습니다. 이렇게 하려면 `target` 반환 되는 관계에서 필드를 읽고에 대 한 다음 호출의 ID로 사용 `GetDigitalTwin()` 합니다.

### <a name="find-incoming-relationships-to-a-digital-twin"></a>디지털 쌍으로 들어오는 관계 찾기

Azure Digital Twins에는 지정 된 쌍에 대 한 모든 _ *들어오는* * 관계를 찾는 API도 있습니다. 이는 역방향 탐색 이나 쌍을 삭제할 때 유용 합니다.

이전 코드 샘플은 쌍에서 나가는 관계를 찾는 데 중점을 두었습니다. 다음 예제는 유사 하 게 구성 되지만 대신 쌍으로 *들어오는* 관계를 찾습니다.

`IncomingRelationship`호출은 관계의 전체 본문을 반환 하지 않습니다.

```csharp
public static async Task<List<IncomingRelationship>> FindIncomingRelationshipsAsync(DigitalTwinsClient client, string dtId)
        {
            // Find the relationships for the twin
            try
            {
                // GetRelationshipsAsync will throw an error if a problem occurs
                AsyncPageable<IncomingRelationship> incomingRels = client.GetIncomingRelationshipsAsync(dtId);

                List<IncomingRelationship> results = new List<IncomingRelationship>();
                await foreach (IncomingRelationship incomingRel in incomingRels)
                {
                    results.Add(incomingRel);
                    Console.WriteLine($"Found incoming relationship-{incomingRel.RelationshipId}");

                }
                return results;
            }
            catch (RequestFailedException ex)
            {
                Console.WriteLine($"*** Error {ex.Status}/{ex.ErrorCode} retrieving incoming relationships for {dtId} due to {ex.Message}");
                return null;
            }
        }
```

이제이 메서드를 호출 하 여 다음과 같이 쌍의 들어오는 관계를 확인할 수 있습니다.

```csharp
await FindIncomingRelationshipsAsync(client, twin_Id);
```
### <a name="list-all-twin-properties-and-relationships"></a>모든 쌍 속성 및 관계 나열

위의 메서드를 사용 하 여 쌍으로 나가는 및 들어오는 관계를 나열 하 고 쌍의 속성 및 두 형식의 관계를 포함 하 여 전체 쌍 정보를 인쇄 하는 메서드를 만들 수 있습니다. `FetchAndPrintTwinAsync()`이 작업을 수행 하는 방법을 보여 주는 라는 예제 메서드는 다음과 같습니다.

```csharp  
private static async Task FetchAndPrintTwinAsync(DigitalTwinsClient client, string twin_Id)
        {
            BasicDigitalTwin twin;
            Response<BasicDigitalTwin> res = client.GetDigitalTwin(twin_Id);
            
            await FindOutgoingRelationshipsAsync(client, twin_Id);
            await FindIncomingRelationshipsAsync(client, twin_Id);

            return;
        }
```

이제 다음과 같이 main 메서드에서이 함수를 호출할 수 있습니다. 

```csharp
await FetchAndPrintTwinAsync(client, targetId);
```
## <a name="delete-relationships"></a>관계 삭제

첫 번째 매개 변수는 원본 쌍 (관계가 시작 되는 쌍)을 지정 합니다. 다른 매개 변수는 관계 ID입니다. 관계 Id는 쌍의 범위 내 에서만 고유 하므로 쌍 ID와 관계 ID가 모두 필요 합니다.

```csharp
private static async Task DeleteRelationship(DigitalTwinsClient client, string srcId, string relId)
        {
            try
            {
                Response response = await client.DeleteRelationshipAsync(srcId, relId);
                await FetchAndPrintTwinAsync(srcId, client);
                Console.WriteLine("Deleted relationship successfully");
            }
            catch (RequestFailedException Ex)
            {
                Console.WriteLine($"Error {Ex.ErrorCode}");
            }
        }
```

이제이 메서드를 호출 하 여 다음과 같은 관계를 삭제할 수 있습니다.

```csharp
await DeleteRelationship(client, srcId, relId);
```
## <a name="create-a-twin-graph"></a>쌍 그래프 만들기 

다음 실행 가능한 코드 조각은이 문서의 관계 작업을 사용 하 여 디지털 쌍 및 관계에서 쌍으로 된 쌍의 그래프를 만듭니다.

### <a name="set-up-the-runnable-sample"></a>실행 가능한 샘플 설정

이 코드 조각은 [*자습서: 샘플 클라이언트 앱을 사용 하 여 Azure Digital Twins 탐색*](tutorial-command-line-app.md)의 모델 정의에 [*대 한Room.js*](https://github.com/Azure-Samples/digital-twins-samples/blob/master/AdtSampleApp/SampleClientApp/Models/Room.json) 및 [*Floor.js*](https://github.com/azure-Samples/digital-twins-samples/blob/master/AdtSampleApp/SampleClientApp/Models/Floor.json) 를 사용 합니다. 이러한 링크를 사용 하 여 파일로 직접 이동 하거나 [여기](/samples/azure-samples/digital-twins-samples/digital-twins-samples/)에서 전체 종단 간 샘플 프로젝트의 일부로 다운로드할 수 있습니다. 

샘플을 실행 하기 전에 다음을 수행 합니다.
1. 모델 파일을 다운로드 하 고 프로젝트에 추가한 다음 `<path-to>` 아래 코드의 자리 표시자를 바꿔서 프로그램에서 찾을 위치를 알려 줍니다.
2. 자리 표시자를 `<your-instance-hostname>` Azure 디지털 Twins 인스턴스의 호스트 이름으로 바꿉니다.
3. 프로젝트에 다음 패키지를 추가 합니다.
    ```cmd/sh
    dotnet add package Azure.DigitalTwins.Core --version 1.0.0-preview.3
    dotnet add package Azure.identity
    ```

또한 샘플을 직접 실행 하려면 로컬 자격 증명을 설정 해야 합니다. 다음 섹션에서는이 과정을 안내 합니다.
[!INCLUDE [Azure Digital Twins: local credentials prereq (outer)](../../includes/digital-twins-local-credentials-outer.md)]

### <a name="run-the-sample"></a>샘플 실행

위의 단계를 완료 한 후에는 다음 샘플 코드를 직접 실행할 수 있습니다.

```csharp 
using System;
using Azure.DigitalTwins.Core;
using Azure.Identity;
using System.Threading.Tasks;
using System.IO;
using System.Collections.Generic;
using Azure;
using Azure.DigitalTwins.Core.Serialization;
using System.Text.Json;

namespace minimal
{
    class Program
    {

        public static async Task Main(string[] args)
        {
            Console.WriteLine("Hello World!");

            //Create the Azure Digital Twins client for API calls
            DigitalTwinsClient client = createDTClient();
            Console.WriteLine($"Service client created – ready to go");
            Console.WriteLine();

            //Upload models
            Console.WriteLine($"Upload models");
            Console.WriteLine();
            string dtdl = File.ReadAllText("<path-to>/Room.json");
            string dtdl1 = File.ReadAllText("<path-to>/Floor.json");
            var typeList = new List<string>();
            typeList.Add(dtdl);
            typeList.Add(dtdl1);
            // Upload the models to the service
            await client.CreateModelsAsync(typeList);

            //Create new (Floor) digital twin
            BasicDigitalTwin floorTwin = new BasicDigitalTwin();
            string srcId = "myFloorID";
            floorTwin.Metadata = new DigitalTwinMetadata();
            floorTwin.Metadata.ModelId = "dtmi:example:Floor;1";
            //Floor twins have no properties, so nothing to initialize
            //Create the twin
            await client.CreateOrReplaceDigitalTwinAsync<BasicDigitalTwin>(srcId, floorTwin);
            Console.WriteLine("Twin created successfully");

            //Create second (Room) digital twin
            BasicDigitalTwin roomTwin = new BasicDigitalTwin();
            string targetId = "myRoomID";
            roomTwin.Metadata = new DigitalTwinMetadata();
            roomTwin.Metadata.ModelId = "dtmi:example:Room;1";
            // Initialize properties
            Dictionary<string, object> props = new Dictionary<string, object>();
            props.Add("Temperature", 35.0);
            props.Add("Humidity", 55.0);
            roomTwin.Contents = props;
            //Create the twin
            await client.CreateOrReplaceDigitalTwinAsync<BasicDigitalTwin>(targetId, roomTwin);
            
            //Create relationship between them
            await CreateRelationship(client, srcId, targetId, "contains");
            Console.WriteLine();

            //Print twins and their relationships
            Console.WriteLine("--- Printing details:");
            Console.WriteLine("Outgoing relationships from source twin:");
            await FetchAndPrintTwinAsync(srcId, client);
            Console.WriteLine();
            Console.WriteLine("Incoming relationships to target twin:");
            await FetchAndPrintTwinAsync(targetId, client);
            Console.WriteLine("--------");
            Console.WriteLine();

            //Delete the relationship
            Console.WriteLine("Deleting the relationship");
            await DeleteRelationship(client, srcId, $"{srcId}-contains->{targetId}");
            Console.WriteLine();

            //Print twins and their relationships again
            Console.WriteLine("--- Printing details:");
            Console.WriteLine("Outgoing relationships from source twin:");
            await FetchAndPrintTwinAsync(srcId, client);
            Console.WriteLine();
            Console.WriteLine("Incoming relationships to target twin:");
            await FetchAndPrintTwinAsync(targetId, client);
            Console.WriteLine("--------");
            Console.WriteLine();
        }

        private static DigitalTwinsClient createDTClient()
        {
            string adtInstanceUrl = "https://<your-instance-hostname>";
            var credentials = new DefaultAzureCredential();
            DigitalTwinsClient client = new DigitalTwinsClient(new Uri(adtInstanceUrl), credentials);
            return client;
        }
        private async static Task CreateRelationship(DigitalTwinsClient client, string srcId, string targetId, string relName)
        {
            // Create relationship between twins
            var relationship = new BasicRelationship
            {
                TargetId = targetId,
                Name = relName
            };

            try
            {
                string relId = $"{srcId}-{relName}->{targetId}";
                await client.CreateOrReplaceRelationshipAsync<BasicRelationship>(srcId, relId, relationship);
                Console.WriteLine($"Created {relName} relationship successfully");
            }
            catch (RequestFailedException rex)
            {
                Console.WriteLine($"Create relationship error: {rex.Status}:{rex.Message}");
            }

        }

        private static async Task FetchAndPrintTwinAsync(string twin_Id, DigitalTwinsClient client)
        {
            BasicDigitalTwin twin;
            Response<BasicDigitalTwin> res = client.GetDigitalTwin(twin_Id);
            await FindOutgoingRelationshipsAsync(client, twin_Id);
            await FindIncomingRelationshipsAsync(client, twin_Id);

            return;
        }

        private static async Task<List<BasicRelationship>> FindOutgoingRelationshipsAsync(DigitalTwinsClient client, string dtId)
        {
            // Find the relationships for the twin
            
            try
            {
                // GetRelationshipsAsync will throw if an error occurs
                AsyncPageable<BasicRelationship> rels = client.GetRelationshipsAsync<BasicRelationship>(dtId);
                List<BasicRelationship> results = new List<BasicRelationship>();
                await foreach (BasicRelationship rel in rels)
                {
                    results.Add(rel);
                    Console.WriteLine($"Found relationship-{rel.Name}->{rel.TargetId}");
                }

                return results;
            }
            catch (RequestFailedException ex)
            {
                Console.WriteLine($"*** Error {ex.Status}/{ex.ErrorCode} retrieving relationships for {dtId} due to {ex.Message}");
                return null;
            }
        }

        private static async Task<List<IncomingRelationship>> FindIncomingRelationshipsAsync(DigitalTwinsClient client, string dtId)
        {
            // Find the relationships for the twin
            
            try
            {
                // GetRelationshipsAsync will throw an error if a problem occurs
                AsyncPageable<IncomingRelationship> incomingRels = client.GetIncomingRelationshipsAsync(dtId);

                List<IncomingRelationship> results = new List<IncomingRelationship>();
                await foreach (IncomingRelationship incomingRel in incomingRels)
                {
                    results.Add(incomingRel);
                    Console.WriteLine($"Found incoming relationship-{incomingRel.RelationshipId}");
                }
                return results;
            }
            catch (RequestFailedException ex)
            {
                Console.WriteLine($"**_ Error {ex.Status}/{ex.ErrorCode} retrieving incoming relationships for {dtId} due to {ex.Message}");
                return null;
            }
        }

        private static async Task DeleteRelationship(DigitalTwinsClient client, string srcId, string relId)
        {
            try
            {
                Response response = await client.DeleteRelationshipAsync(srcId, relId);
                await FetchAndPrintTwinAsync(srcId, client);
                Console.WriteLine("Deleted relationship successfully");
            }
            catch (RequestFailedException Ex)
            {
                Console.WriteLine($"Error {Ex.ErrorCode}");
            }
        }
    }
}
```

위의 프로그램의 콘솔 출력은 다음과 같습니다. 

:::image type="content" source="./media/how-to-manage-graph/console-output-twin-graph.png" alt-text="쌍 세부 정보, 쌍의 들어오고 나가는 관계를 보여 주는 콘솔 출력입니다." lightbox="./media/how-to-manage-graph/console-output-twin-graph.png":::

> [!TIP]
> 쌍 그래프는 twins 간의 관계를 만드는 개념입니다. 쌍 그래프의 시각적 표시를 보려는 경우이 문서의 [_Visualization *](how-to-manage-graph.md#visualization) 섹션을 참조 하세요. 

### <a name="create-a-twin-graph-from-a-spreadsheet"></a>스프레드시트에서 쌍 그래프 만들기

실제로 사용 하는 경우에는 다른 데이터베이스 또는 스프레드시트에 저장 된 데이터에서 쌍 계층이 생성 되는 경우가 많습니다. 이 섹션에서는 스프레드시트를 구문 분석 하는 방법을 보여 줍니다.

만들 일련의 디지털 쌍 및 관계를 설명 하는 다음 데이터 테이블을 고려 합니다.

| 모델 ID| 쌍 ID (고유 해야 함) | 관계 이름 | 대상 쌍 ID | 쌍 초기화 데이터 |
| --- | --- | --- | --- | --- |
| dtmi: 예: 밑면; 1 | Floor1 |  포함 | Room1 |{"온도": 80, "습도": 60}
| dtmi: 예: 밑면; 1 | Floor0 |  has는      | Room0 |{"온도": 70, "습도": 30}
| dtmi: 예: Room; 1  | Room1 | 
| dtmi: 예: Room; 1  | Room0 |

다음 코드는 [MICROSOFT GRAPH API](/graph/overview) 를 사용 하 여 스프레드시트를 읽고 결과에서 Azure Digital twins 쌍 그래프를 구성 합니다.

```csharp
var range = msftGraphClient.Me.Drive.Items["BuildingsWorkbook"].Workbook.Worksheets["Building"].usedRange;
JsonDocument data = JsonDocument.Parse(range.values);
List<BasicRelationship> RelationshipRecordList = new List<BasicRelationship>();
foreach (JsonElement row in data.RootElement.EnumerateArray())
{
    string modelId = row[0].GetString();
    string sourceId = row[1].GetString();
    string relName = row[2].GetString();
    string targetId = row[3].GetString();
    string initData = row[4].GetString();
    
    // Parse spreadsheet extra data into a JSON string to initialize the digital twin
    // Left out for compactness
    Dictionary<string, object> initData = new Dictionary<string, object>() { ... };

    if (sourceId != null)
    {
        BasicRelationship br = new BasicRelationship()
        {
            SourceId = sourceId,
            TargetId = targetId,
            Name = relName
        };
        RelationshipRecordList.Add(br);
    }

    BasicDigitalTwin twin = new BasicDigitalTwin();
    twin.Contents = initData;
    // Set the type of twin to be created
    twin.Metadata = new DigitalTwinMetadata() { ModelId = modelId };
    
    try
    {
        await client.CreateOrReplaceDigitalTwinAsync<BasicDigitalTwin>(sourceId, twin);
    }
    catch (RequestFailedException e)
    {
       Console.WriteLine($"Error {e.Status}: {e.Message}");
    }
    foreach (BasicRelationship rec in RelationshipRecordList)
    { 
        try { 
            await client.CreateOrReplaceRelationshipAsync(rec.sourceId, Guid.NewGuid().ToString(), rec);
        }
        catch (RequestFailedException e)
        {
            Console.WriteLine($"Error {e.Status}: {e.Message}");
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