---
title: '예: PersonDirectory 구조 사용 - Face'
titleSuffix: Azure Cognitive Services
description: PersonDirectory 데이터 구조를 사용하여 더 많은 용량 및 다른 새로운 기능으로 얼굴 및 사람 데이터를 저장하는 방법을 알아봅니다.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 04/22/2021
ms.author: pafarley
ms.custom: devx-track-csharp
ms.openlocfilehash: 70bbf20570c39fa59da9af7f7883aeef2e107df4
ms.sourcegitcommit: 9f1a35d4b90d159235015200607917913afe2d1b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/21/2021
ms.locfileid: "122633789"
---
# <a name="use-the-persondirectory-structure"></a>PersonDirectory 구조 사용

식별 및 유사 찾기와 같은 얼굴 인식 작업을 수행하려면 Face API 고객은 다양한 **Person** 개체 목록을 만들어야 합니다. 새 **PersonDirectory** 는 디렉터리에 추가된 각 **Person** ID의 고유한 ID, 선택적 이름 문자열 및 선택적 사용자 메타데이터 문자열이 포함된 데이터 구조입니다.

현재 Face API는 기능은 유사하지만 ID가 100만 개로 제한되는 **LargePersonGroup** 구조를 제공합니다. **PersonDirectory** 구조는 최대 7500만 개 ID로 확장될 수 있습니다.

**PersonDirectory** 와 이전 데이터 구조 간의 또 다른 주요 차이점은 얼굴을 **Person** 개체&mdash;에 추가하면 업데이트가 자동으로 처리되므로 더 이상 학습을 호출할 필요가 없다는 점입니다.

## <a name="prerequisites"></a>사전 요구 사항
* Azure 구독 - [체험 구독 만들기](https://azure.microsoft.com/free/cognitive-services/)
* Azure 구독을 보유한 후에는 Azure Portal에서 [Face 리소스를 만들어](https://portal.azure.com/#create/Microsoft.CognitiveServicesFace) 키와 엔드포인트를 가져옵니다. 배포 후 **리소스로 이동** 을 클릭합니다.
  * 애플리케이션을 Face API에 연결하려면 생성한 리소스의 키와 엔드포인트가 필요합니다. 아래 코드에 키와 엔드포인트를 붙여넣습니다.
  * 체험 가격 책정 계층(F0)을 통해 서비스를 사용해 보고, 나중에 프로덕션을 위한 유료 계층으로 업그레이드할 수 있습니다.

## <a name="add-persons-to-the-persondirectory"></a>PersonDirectory에 Person 추가
**Person** 은 **PersonDirectory** 의 기본 등록 단위입니다. 디렉터리에 **Person** 을 추가한 후에는 해당 **Person** 에게 인식 모델당 최대 248개의 얼굴 이미지를 추가할 수 있습니다. 그런 다음 다양한 범위를 사용하여 얼굴을 식별할 수 있습니다.

### <a name="create-the-person"></a>Person 만들기
**Person** 을 만들려면 **CreatePerson** API를 호출하고 이름 또는 userData 속성 값을 제공해야 합니다.

```csharp
using Newtonsoft.Json;
using System;
using System.Collections.Generic;
using System.Linq;
using System.Net.Http;
using System.Net.Http.Headers;
using System.Text;
using System.Threading.Tasks;

var client = new HttpClient();

// Request headers
client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", "{subscription key}");

var addPersonUri = "https:// {endpoint}/face/v1.0-preview/persons";

HttpResponseMessage response;

// Request body
var body = new Dictionary<string, object>();
body.Add("name", "Example Person");
body.Add("userData", "User defined data");
byte[] byteData = Encoding.UTF8.GetBytes(JsonConvert.SerializeObject(body));

using (var content = new ByteArrayContent(byteData))
{
    content.Headers.ContentType = new MediaTypeHeaderValue("application/json");
    response = await client.PostAsync(addPersonUri, content); 
}
```

CreatePerson 호출은 **Person** 및 작업 위치에 대해 생성된 ID를 반환합니다. **Person** 데이터는 비동기적으로 처리되므로 작업 위치를 사용하여 결과를 페치합니다. 

### <a name="wait-for-asynchronous-operation-completion"></a>비동기 작업이 완료될 때까지 대기
반환된 작업 위치 문자열을 사용하여 비동기 작업 상태를 쿼리하여 진행률을 확인해야 합니다. 

먼저 상태 응답을 처리하는 다음과 같은 데이터 모델을 정의해야 합니다.

```csharp
[Serializable]
public class AsyncStatus
{
    [DataMember(Name = "status")]
    public string Status { get; set; }

    [DataMember(Name = "createdTime")]
    public DateTime CreatedTime { get; set; }

    [DataMember(Name = "lastActionTime")]
    public DateTime? LastActionTime { get; set; }

    [DataMember(Name = "finishedTime", EmitDefaultValue = false)]
    public DateTime? FinishedTime { get; set; }

    [DataMember(Name = "resourceLocation", EmitDefaultValue = false)]
    public string ResourceLocation { get; set; }

    [DataMember(Name = "message", EmitDefaultValue = false)]
    public string Message { get; set; }
}
```

위의 HttpResponseMessage를 사용하여 URL을 폴링하고 결과를 기다리면 됩니다.

```csharp
string operationLocation = response.Headers.GetValues("Operation-Location").FirstOrDefault();

Stopwatch s = Stopwatch.StartNew();
string status = "notstarted";
do
{
    if (status == "succeeded")
    {
        await Task.Delay(500);
    }

    var operationResponseMessage = await client.GetAsync(operationLocation);

    var asyncOperationObj = JsonConvert.DeserializeObject<AsyncStatus>(await operationResponseMessage.Content.ReadAsStringAsync());
    status = asyncOperationObj.Status;

} while ((status == "running" || status == "notstarted") && s.Elapsed < TimeSpan.FromSeconds(30));
```


상태가 "성공"으로 반환되면 **Person** 개체가 디렉터리에 추가된 것으로 간주됩니다.

> [!NOTE]
> **Person** 만들기 호출의 비동기 작업에는 얼굴을 추가하기 전에 "성공" 상태를 표시할 필요가 없지만, 이 작업이 완료되어야만 **DynamicPersonGroup** 에 해당 **Person** 을 추가하거나(아래 **DynamicPersonGroup** 만들기 및 업데이트 참조) 식별 호출 중에 비교할 수 있습니다. **Person** 에 얼굴이 성공적으로 추가되는 즉시 호출이 작동하는지 확인합니다.


### <a name="add-faces-to-persons"></a>Persons에 얼굴 추가

Person 만들기 호출에서 **Person** ID가 있으면 인식 모델당 최대 248개의 얼굴 이미지를 **Person** 에 추가할 수 있습니다. 각 인식 모델의 데이터는 **PersonDirectory** 내에서 별도로 처리되기 때문에 호출에서 사용할 인식 모델(및 필요에 따라 검색 모델)을 지정합니다.

현재 지원되는 인식 모델은 다음과 같습니다.
* `Recognition_02`
* `Recognition_03`
* `Recognition_04`

또한 이미지에 여러 얼굴을 포함하는 경우 의도한 대상에 해당하는 얼굴에 대해 사각형 경계 상자를 지정해야 합니다. 다음 코드에서는 **Person** 개체에 얼굴을 추가합니다.

```csharp
var client = new HttpClient();

// Request headers
client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", "{subscription key}");

// Optional query strings for more fine grained face control
var queryString = "userData={userDefinedData}&targetFace={left,top,width,height}&detectionModel={detectionModel}";
var uri = "https://{endpoint}/face/v1.0-preview/persons/{personId}/recognitionModels/{recognitionModel}/persistedFaces?" + queryString;

HttpResponseMessage response;

// Request body
var body = new Dictionary<string, object>();
body.Add("url", "{image url}");
byte[] byteData = Encoding.UTF8.GetBytes(JsonConvert.SerializeObject(body));

using (var content = new ByteArrayContent(byteData))
{
    content.Headers.ContentType = new MediaTypeHeaderValue("application/json");
    response = await client.PostAsync(uri, content);
}
```

얼굴 추가 호출 후에는 얼굴 데이터가 비동기식으로 처리되며 이전과 동일한 방식으로 작업이 성공할 때까지 대기해야 합니다.

얼굴 추가 작업이 완료되면 데이터의 식별 호출이 가능한 상태가 됩니다.

## <a name="create-and-update-a-dynamicpersongroup"></a>**DynamicPersonGroup** 만들기 및 업데이트

**DynamicPersonGroups** 는 **PersonDirectory** 내의 **Person** 개체에 대한 참조 컬렉션으로, 디렉터리의 하위 집합을 만드는 데 사용됩니다. 일치하는 범위를 **Person** 개체로만 제한하여 식별 작업에서 가양성을 줄이고 정확도를 증가시키려는 경우에 일반적으로 사용됩니다. 실제 사용 사례에는 더 큰 캠퍼스 또는 조직 중 특정 빌딩 액세스를 위한 디렉터리가 포함됩니다. 조직 디렉터리는 500만 명의 개인을 포함할 수 있지만 특정 빌딩에 대해 특정 800명의 사람만 검색해야 하므로 해당 특정 개인을 포함하는 **DynamicPersonGroup** 을 만듭니다. 

이전에 **PersonGroup** 을 사용한 경우 다음 두 가지 주요 차이점에 유의합니다. 
* **DynamicPersonGroup** 내의 각 **Person** 은 **PersonDirectory** 의 실제 **Person** 에 대한 참조이므로 각 그룹에서 **Person** 을 다시 만들 필요가 없습니다.
* 이전 섹션에서 설명한 것처럼 얼굴 데이터는 디렉터리 수준에서 자동으로 처리되므로 학습 호출을 수행할 필요가 없습니다.

### <a name="create-the-group"></a>그룹 만들기

**DynamicPersonGroup** 을 만들려면 영숫자 또는 대시 문자를 포함하는 그룹 ID를 제공해야 합니다. 이 ID는 그룹의 모든 용도에 대한 고유 식별자로 작동합니다.

그룹 컬렉션을 초기화하는 방법에는 두 가지가 있습니다. 처음에 빈 그룹을 만들고 나중에 채울 수 있습니다.

```csharp
var client = new HttpClient();

// Request headers
client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", "{subscription key}");

var uri = "https://{endpoint}/face/v1.0-preview/dynamicpersongroups/{dynamicPersonGroupId}";

HttpResponseMessage response;

// Request body
var body = new Dictionary<string, object>();
body.Add("name", "Example DynamicPersonGroup");
body.Add("userData", "User defined data");
byte[] byteData = Encoding.UTF8.GetBytes(JsonConvert.SerializeObject(body));

using (var content = new ByteArrayContent(byteData))
{
    content.Headers.ContentType = new MediaTypeHeaderValue("application/json");
    response = await client.PutAsync(uri, content);
}
```

이 프로세스는 즉시 수행되며 비동기 작업이 성공할 때까지 기다릴 필요가 없습니다.

또는 _AddPersonIds_ 인수에 집합을 제공하여 처음부터 해당 참조를 포함하도록 **Person** ID 집합을 사용하여 만들 수 있습니다.

```csharp
var client = new HttpClient();

// Request headers
client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", "{subscription key}");

var uri = "https://{endpoint}/face/v1.0-preview/dynamicpersongroups/{dynamicPersonGroupId}";

HttpResponseMessage response;

// Request body
var body = new Dictionary<string, object>();
body.Add("name", "Example DynamicPersonGroup");
body.Add("userData", "User defined data");
body.Add("addPersonIds", new List<string>{"{guid1}", "{guid2}", …});
byte[] byteData = Encoding.UTF8.GetBytes(JsonConvert.SerializeObject(body));

using (var content = new ByteArrayContent(byteData))
{
    content.Headers.ContentType = new MediaTypeHeaderValue("application/json");
    response = await client.PutAsync(uri, content);

    // Async operation location to query the completion status from
    var operationLocation = response.Headers.Get("Operation-Location");
}
```

> [!NOTE]
> 호출이 반환되는 즉시 생성된 **DynamicPersonGroup** 을 프로세스에서 제공되는 모든 **Person** 참조를 사용하여 식별 호출에 사용할 수 있습니다. 반면 반환된 작업 ID의 완료 상태는 사람-그룹 관계의 업데이트 상태를 나타냅니다.

### <a name="update-the-dynamicpersongroup"></a>DynamicPersonGroup 업데이트

처음 만든 후에 Update Dynamic Person Group API를 사용하여 **DynamicPersonGroup** 에서 **Person** 참조를 추가 및 제거할 수 있습니다. **Person** 개체를 그룹에 추가하려면 _addPersonsIds_ 인수에 **Person** ID를 나열합니다. **Person** 개체를 제거하려면 _removePersonIds_ 인수에 해당 ID를 나열합니다. 추가 및 제거 작업은 한 번의 호출로 수행할 수 있습니다.

```csharp
var client = new HttpClient();

// Request headers
client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", "{subscription key}");

var uri = "https://{endpoint}/face/v1.0-preview/dynamicpersongroups/{dynamicPersonGroupId}";

HttpResponseMessage response;

// Request body
var body = new Dictionary<string, object>();
body.Add("name", "Example Dynamic Person Group updated");
body.Add("userData", "User defined data updated");
body.Add("addPersonIds", new List<string>{"{guid1}", "{guid2}", …});
body.Add("removePersonIds", new List<string>{"{guid1}", "{guid2}", …});
byte[] byteData = Encoding.UTF8.GetBytes(JsonConvert.SerializeObject(body));

using (var content = new ByteArrayContent(byteData))
{
    content.Headers.ContentType = new MediaTypeHeaderValue("application/json");
    response = await client.PatchAsync(uri, content);

    // Async operation location to query the completion status from
    var operationLocation = response.Headers.Get("Operation-Location");
}
```

호출이 반환되면 그룹을 쿼리할 때 컬렉션에 대한 업데이트가 반영됩니다. 생성 API와 마찬가지로 반환된 작업은 업데이트에 관련된 모든 **Person** 에 대한 사람-그룹 관계의 업데이트 상태를 나타냅니다. 그룹에 대한 추가 업데이트 호출을 수행하기 전에 작업이 완료될 때까지 기다릴 필요가 없습니다.

## <a name="identify-faces-in-a-persondirectory"></a>PersonDirectory에서 얼굴 식별

**PersonDirectory** 에서 얼굴 데이터를 사용하는 가장 일반적인 방법은 등록된 **Person** 개체를 지정된 얼굴과 비교하고 가장 가능성이 높은 후보를 식별하는 것입니다. 요청에서 여러 얼굴을 제공할 수 있으며 각 응답에서 고유한 비교 결과 집합을 받습니다.

**PersonDirectory** 에는 각 얼굴을 식별할 수 있는 세 가지 유형의 범위가 있습니다.

### <a name="scenario-1-identify-against-a-dynamicpersongroup"></a>시나리오 1: DynamicPersonGroup에 대해 식별
 
요청에서 _dynamicPersonGroupId_ 속성을 지정하면 그룹에서 참조되는 모든 **Person** 과 얼굴을 비교합니다. 호출에서 단일 **DynamicPersonGroup** 만 식별할 수 있습니다. 

```csharp
var client = new HttpClient();

// Request headers
client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", "{subscription key}");

// Optional query strings for more fine grained face control
var uri = "https://{endpoint}/face/v1.0-preview/identify";

HttpResponseMessage response;

// Request body
var body = new Dictionary<string, object>();
body.Add("faceIds", new List<string>{"{guid1}", "{guid2}", …});
body.Add("dynamicPersonGroupId", "{dynamicPersonGroupIdToIdentifyIn}");
byte[] byteData = Encoding.UTF8.GetBytes(JsonConvert.SerializeObject(body));

using (var content = new ByteArrayContent(byteData))
{
    content.Headers.ContentType = new MediaTypeHeaderValue("application/json");
    response = await client.PostAsync(uri, content);
}
```

### <a name="scenario-2-identify-against-a-specific-list-of-persons"></a>시나리오 2: 특정 사람 목록에 대해 식별

_personIds_ 속성에서 **Person** ID 목록을 지정하여 각 항목에 대해 얼굴을 비교할 수도 있습니다.

```csharp
var client = new HttpClient();

// Request headers
client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", "{subscription key}");
            
var uri = "https://{endpoint}/face/v1.0-preview/identify";

HttpResponseMessage response;

// Request body
var body = new Dictionary<string, object>();
body.Add("faceIds", new List<string>{"{guid1}", "{guid2}", …});
body.Add("personIds", new List<string>{"{guid1}", "{guid2}", …});
byte[] byteData = Encoding.UTF8.GetBytes(JsonConvert.SerializeObject(body));

using (var content = new ByteArrayContent(byteData))
{
    content.Headers.ContentType = new MediaTypeHeaderValue("application/json");
    response = await client.PostAsync(uri, content);
}
```

### <a name="scenario-3-identify-against-the-entire-persondirectory"></a>시나리오 3: 전체 **PersonDirectory** 에 대해 식별

요청에서 _personIds_ 속성에 단일 별표를 제공하면 **PersonDirectory** 에 등록된 모든 단일 **Person** 에 대해 얼굴을 비교합니다. 
 

```csharp
var client = new HttpClient();

// Request headers
client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", "{subscription key}");
            
var uri = "https://{endpoint}/face/v1.0-preview/identify";

HttpResponseMessage response;

// Request body
var body = new Dictionary<string, object>();
body.Add("faceIds", new List<string>{"{guid1}", "{guid2}", …});
body.Add("personIds", new List<string>{"*"});
byte[] byteData = Encoding.UTF8.GetBytes(JsonConvert.SerializeObject(body));

using (var content = new ByteArrayContent(byteData))
{
    content.Headers.ContentType = new MediaTypeHeaderValue("application/json");
    response = await client.PostAsync(uri, content);
}
```
세 가지 시나리오 모두, AddPersonFace 호출에서 "성공" 응답으로 반환되는 얼굴과 들어오는 얼굴을 비교하는 작업만으로 식별합니다.

## <a name="verify-faces-against-persons-in-the-persondirectory"></a>**PersonDirectory** 의 Person에 대해 얼굴 확인

검색 호출에서 반환된 얼굴 ID를 사용하여 얼굴이 **PersonDirectory** 내에 등록된 특정 **Person** 에 해당하는지 확인할 수 있습니다. _personId_ 속성을 사용하여 **Person** 을 지정합니다.

```csharp
var client = new HttpClient();

// Request headers
client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", "{subscription key}");

var uri = "https://{endpoint}/face/v1.0-preview/verify";

HttpResponseMessage response;

// Request body
var body = new Dictionary<string, object>();
body.Add("faceId", "{guid1}");
body.Add("personId", "{guid1}");
var jsSerializer = new JavaScriptSerializer();
byte[] byteData = Encoding.UTF8.GetBytes(jsSerializer.Serialize(body));

using (var content = new ByteArrayContent(byteData))
{
    content.Headers.ContentType = new MediaTypeHeaderValue("application/json");
    response = await client.PostAsync(uri, content);
}
```

응답에는 서비스에서 새 얼굴을 동일한 **Person** 에 해당한다고 간주하는지 여부를 나타내는 부울 값과 예측의 신뢰도 점수가 포함됩니다.

## <a name="next-steps"></a>다음 단계

이 가이드에서는 Face 앱의 대해 **PersonDirectory** 구조를 사용하여 얼굴 및 사람 데이터를 저장하는 방법을 배웠습니다. 다음으로, 사용자의 얼굴 데이터를 추가하는 모범 사례를 알아봅니다.

* [사용자 추가 모범 사례](../enrollment-overview.md)
