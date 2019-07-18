---
title: '예제: Bing Entity Search API-Azure Search를 사용 하 여 cognitive 스킬을 사용자 지정 만들기'
description: Bing Entity Search 서비스를 사용 하 여 Azure Search에서 cognitive search 인덱싱 파이프라인에 매핑되는 사용자 지정 기술에서 하는 방법을 보여 줍니다.
manager: pablocas
author: luiscabrer
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: 7d90f46ada9b9453b4c1516a4a898456dc73b8e7
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67672139"
---
# <a name="example-create-a-custom-skill-using-the-bing-entity-search-api"></a>예제: Bing Entity Search API를 사용 하 여 사용자 지정 기술 만들기

이 예제에서는 웹 API 사용자 지정 기술을 만드는 방법에 알아봅니다. 이 기술 위치, 공용 수치 및 조직에 적용 되며에 대 한 설명을 반환 합니다. 이 예제에서는 사용를 [Azure Function](https://azure.microsoft.com/services/functions/) 래핑할 합니다 [Bing Entity Search API](https://azure.microsoft.com/services/cognitive-services/bing-entity-search-api/) 을 사용자 지정 기술 인터페이스를 구현 합니다.

## <a name="prerequisites"></a>필수 구성 요소

+ 읽어보세요 [사용자 지정 기술 인터페이스](cognitive-search-custom-skill-interface.md) 문서는 사용자 지정 기술 구현 해야 하는 입/출력 인터페이스에 익숙하지 않은 경우.

+ [!INCLUDE [cognitive-services-bing-entity-search-signup-requirements](../../includes/cognitive-services-bing-entity-search-signup-requirements.md)]

+ 설치할 [Visual Studio 2019](https://www.visualstudio.com/vs/) 하거나 나중에 Azure 개발 워크 로드를 포함 합니다.

## <a name="create-an-azure-function"></a>Azure Function 만들기

이 예제에서는 Azure Function 웹 API를 호스트 하 하지만 필요 하지 않습니다.  [인식 기술에 대한 인터페이스 요구 사항](cognitive-search-custom-skill-interface.md)을 충족하기만 하면, 사용하는 방식은 중요하지 않습니다. 그러나 Azure Functions를 사용하면 사용자 지정 기술을 쉽게 만들 수 있습니다.

### <a name="create-a-function-app"></a>함수 앱 만들기

1. Visual Studio의 파일 메뉴에서 **새로 만들기** > **프로젝트**를 선택합니다.

1. 새 프로젝트 대화 상자에서 **설치됨**을 선택하고, **Visual C#**  > **클라우드**를 확장하고, **Azure Functions**를 선택하고, 프로젝트의 이름을 입력한 다음, **확인**을 선택합니다. 함수 앱 이름으로 유효 해야는 C# 네임 스페이스에 있으므로 밑줄, 하이픈 또는 기타 영숫자가 아닌 문자를 사용 하지 마세요.

1. 선택 **Azure Functions v2 (.NET Core)** 합니다. 버전 1에서도 수행할 수 있지만 아래에 작성된 코드는 v2 템플릿을 기반으로 합니다.

1. 형식을 **HTTP 트리거**로 선택합니다.

1. 저장소 계정의 경우, 이 함수에 대한 저장소가 필요하지 않으므로 **없음**을 선택할 수 있습니다.

1. **확인**을 선택하여 함수 프로젝트 및 HTTP 트리거 함수를 만듭니다.

### <a name="modify-the-code-to-call-the-bing-entity-search-service"></a>Bing Entity Search 서비스를 호출 하는 코드를 수정 합니다.

Visual Studio는 프로젝트를 만들고 그 안에는 선택한 함수 형식에 대한 상용구 코드를 포함하는 클래스를 만듭니다. 메서드의 *FunctionName* 특성은 함수 이름을 설정합니다. *HttpTrigger* 특성은 함수가 HTTP 요청에 의해 트리거되도록 지정합니다.

이제 *Function1.cs* 파일의 모든 내용을 다음 코드로 바꿉니다.

```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Linq;
using System.Net.Http;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.AspNetCore.Http;
using Microsoft.Extensions.Logging;
using Newtonsoft.Json;

namespace SampleSkills
{
    /// <summary>
    /// Sample custom skill that wraps the Bing entity search API to connect it with a 
    /// cognitive search pipeline.
    /// </summary>
    public static class BingEntitySearch
    {
        #region Credentials
        // IMPORTANT: Make sure to enter your credential and to verify the API endpoint matches yours.
        static readonly string bingApiEndpoint = "https://api.cognitive.microsoft.com/bing/v7.0/entities/";
        static readonly string key = "<enter your api key here>";  
        #endregion

        #region Class used to deserialize the request
        private class InputRecord
        {
            public class InputRecordData
            {
                public string Name { get; set; }
            }

            public string RecordId { get; set; }
            public InputRecordData Data { get; set; }
        }

        private class WebApiRequest
        {
            public List<InputRecord> Values { get; set; }
        }
        #endregion

        #region Classes used to serialize the response

        private class OutputRecord
        {
            public class OutputRecordData
            {
                public string Name { get; set; } = "";
                public string Description { get; set; } = "";
                public string Source { get; set; } = "";
                public string SourceUrl { get; set; } = "";
                public string LicenseAttribution { get; set; } = "";
                public string LicenseUrl { get; set; } = "";
            }

            public class OutputRecordMessage
            {
                public string Message { get; set; }
            }

            public string RecordId { get; set; }
            public OutputRecordData Data { get; set; }
            public List<OutputRecordMessage> Errors { get; set; }
            public List<OutputRecordMessage> Warnings { get; set; }
        }

        private class WebApiResponse
        {
            public List<OutputRecord> Values { get; set; }
        }
        #endregion

        #region Classes used to interact with the Bing API
        private class BingResponse
        {
            public BingEntities Entities { get; set; }
        }
        private class BingEntities
        {
            public BingEntity[] Value { get; set; }
        }

        private class BingEntity
        {
            public class EntityPresentationinfo
            {
                public string[] EntityTypeHints { get; set; }
            }

            public class License
            {
                public string Url { get; set; }
            }

            public class ContractualRule
            {
                public string _type { get; set; }
                public License License { get; set; }
                public string LicenseNotice { get; set; }
                public string Text { get; set; }
                public string Url { get; set; }
            }

            public ContractualRule[] ContractualRules { get; set; }
            public string Description { get; set; }
            public string Name { get; set; }
            public EntityPresentationinfo EntityPresentationInfo { get; set; }
        }
        #endregion

        #region The Azure Function definition

        [FunctionName("EntitySearch")]
        public static async Task<IActionResult> Run(
            [HttpTrigger(AuthorizationLevel.Function, "post", Route = null)] HttpRequest req,
            ILogger log)
        {
            log.LogInformation("Entity Search function: C# HTTP trigger function processed a request.");

            var response = new WebApiResponse
            {
                Values = new List<OutputRecord>()
            };

            string requestBody = new StreamReader(req.Body).ReadToEnd();
            var data = JsonConvert.DeserializeObject<WebApiRequest>(requestBody);

            // Do some schema validation
            if (data == null)
            {
                return new BadRequestObjectResult("The request schema does not match expected schema.");
            }
            if (data.Values == null)
            {
                return new BadRequestObjectResult("The request schema does not match expected schema. Could not find values array.");
            }

            // Calculate the response for each value.
            foreach (var record in data.Values)
            {
                if (record == null || record.RecordId == null) continue;

                OutputRecord responseRecord = new OutputRecord
                {
                    RecordId = record.RecordId
                };

                try
                {
                    responseRecord.Data = GetEntityMetadata(record.Data.Name).Result;
                }
                catch (Exception e)
                {
                    // Something bad happened, log the issue.
                    var error = new OutputRecord.OutputRecordMessage
                    {
                        Message = e.Message
                    };

                    responseRecord.Errors = new List<OutputRecord.OutputRecordMessage>
                    {
                        error
                    };
                }
                finally
                {
                    response.Values.Add(responseRecord);
                }
            }

            return (ActionResult)new OkObjectResult(response);
        }

        #endregion

        #region Methods to call the Bing API
        /// <summary>
        /// Gets metadata for a particular entity based on its name using Bing Entity Search
        /// </summary>
        /// <param name="entityName">The name of the entity to extract data for.</param>
        /// <returns>Asynchronous task that returns entity data. </returns>
        private async static Task<OutputRecord.OutputRecordData> GetEntityMetadata(string entityName)
        {
            var uri = bingApiEndpoint + "?q=" + entityName + "&mkt=en-us&count=10&offset=0&safesearch=Moderate";
            var result = new OutputRecord.OutputRecordData();

            using (var client = new HttpClient())
            using (var request = new HttpRequestMessage {
                Method = HttpMethod.Get,
                RequestUri = new Uri(uri)
            })
            {
                request.Headers.Add("Ocp-Apim-Subscription-Key", key);

                HttpResponseMessage response = await client.SendAsync(request);
                string responseBody = await response?.Content?.ReadAsStringAsync();

                BingResponse bingResult = JsonConvert.DeserializeObject<BingResponse>(responseBody);
                if (bingResult != null)
                {
                    // In addition to the list of entities that could match the name, for simplicity let's return information
                    // for the top match as additional metadata at the root object.
                    return AddTopEntityMetadata(bingResult.Entities?.Value);
                }
            }

            return result;
        }

        private static OutputRecord.OutputRecordData AddTopEntityMetadata(BingEntity[] entities)
        {
            if (entities != null)
            {
                foreach (BingEntity entity in entities.Where(
                    entity => entity?.EntityPresentationInfo?.EntityTypeHints != null
                        && (entity.EntityPresentationInfo.EntityTypeHints[0] == "Person"
                            || entity.EntityPresentationInfo.EntityTypeHints[0] == "Organization"
                            || entity.EntityPresentationInfo.EntityTypeHints[0] == "Location")
                        && !String.IsNullOrEmpty(entity.Description)))
                {
                    var rootObject = new OutputRecord.OutputRecordData
                    {
                        Description = entity.Description,
                        Name = entity.Name
                    };

                    if (entity.ContractualRules != null)
                    {
                        foreach (var rule in entity.ContractualRules)
                        {
                            switch (rule._type)
                            {
                                case "ContractualRules/LicenseAttribution":
                                    rootObject.LicenseAttribution = rule.LicenseNotice;
                                    rootObject.LicenseUrl = rule.License.Url;
                                    break;
                                case "ContractualRules/LinkAttribution":
                                    rootObject.Source = rule.Text;
                                    rootObject.SourceUrl = rule.Url;
                                    break;
                            }
                        }
                    }

                    return rootObject;
                }
            }

            return new OutputRecord.OutputRecordData();
        }
        #endregion
    }
}
```

입력 해야 자신의 *키* 값을 `key` Bing 엔터티 검색 API 등록할 때 얻은 키를 기준으로 한 상수입니다.

이 샘플의 편의 위해 단일 파일에 필요한 모든 코드를 포함합니다. 동일한 기술에 약간 더 구조화 된 버전을 찾을 수 있습니다 [전원 기술 리포지토리](https://github.com/Azure-Samples/azure-search-power-skills/tree/master/Text/BingEntitySearch)합니다.

물론, 파일의 이름을 변경할 수 있습니다 `Function1.cs` 에 `BingEntitySearch.cs`입니다.

## <a name="test-the-function-from-visual-studio"></a>Visual Studio에서 함수 테스트

**F5** 키를 눌러 프로그램을 실행하고 함수 동작을 테스트합니다. 이 경우 두 개의 엔터티를 조회 하는 아래 사용 하겠습니다. Postman 또는 Fiddler를 사용하여 아래와 같은 호출을 실행합니다.

```http
POST https://localhost:7071/api/EntitySearch
```

### <a name="request-body"></a>요청 본문
```json
{
    "values": [
        {
            "recordId": "e1",
            "data":
            {
                "name":  "Pablo Picasso"
            }
        },
        {
            "recordId": "e2",
            "data":
            {
                "name":  "Microsoft"
            }
        }
    ]
}
```

### <a name="response"></a>응답
다음 예제와 유사한 응답이 표시됩니다.

```json
{
    "values": [
        {
            "recordId": "e1",
            "data": {
                "name": "Pablo Picasso",
                "description": "Pablo Ruiz Picasso was a Spanish painter [...]",
                "source": "Wikipedia",
                "sourceUrl": "http://en.wikipedia.org/wiki/Pablo_Picasso",
                "licenseAttribution": "Text under CC-BY-SA license",
                "licenseUrl": "http://creativecommons.org/licenses/by-sa/3.0/"
            },
            "errors": null,
            "warnings": null
        },
        "..."
    ]
}
```

## <a name="publish-the-function-to-azure"></a>Azure에 함수 게시

함수 동작에 만족 하는 경우에이 게시할 수 있습니다.

1. **솔루션 탐색기**에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **게시**를 선택합니다. **새로 만들기** > **게시**를 선택합니다.

1. Visual Studio를 Azure 계정에 아직 연결하지 않았으면 **계정 추가...** 를 선택합니다.

1. 화면에 표시되는 메시지를 따릅니다. App service, Azure 구독, 리소스 그룹, 호스팅 계획 및 사용 하려는 저장소 계정에 대 한 고유 이름을 지정 하려면 묻는 메시지가 나타납니다. 아직 없는 경우 새 리소스 그룹, 새 호스팅 플랜 및 저장소 계정을 만들 수 있습니다. 작업을 마쳤으면 **만들기**를 선택합니다.

1. 배포가 완료 되 면 사이트 URL을 확인할 수 있습니다. 이 URL은 Azure에서 해당 함수 앱의 주소입니다. 

1. 에 [Azure portal](https://portal.azure.com), 리소스 그룹으로 이동 하 고 확인을 `EntitySearch` 게시 하는 함수. **관리** 섹션 아래에 호스트 키가 표시됩니다. ‘기본’ 호스트 키의 **복사** 아이콘을 선택합니다.   

## <a name="test-the-function-in-azure"></a>Azure에서 함수 테스트

이제 기본 호스트 키가 있으므로 다음과 같이 함수를 테스트합니다.

```http
POST https://[your-entity-search-app-name].azurewebsites.net/api/EntitySearch?code=[enter default host key here]
```

### <a name="request-body"></a>요청 본문
```json
{
    "values": [
        {
            "recordId": "e1",
            "data":
            {
                "name":  "Pablo Picasso"
            }
        },
        {
            "recordId": "e2",
            "data":
            {
                "name":  "Microsoft"
            }
        }
    ]
}
```

이 예제에서는 로컬 환경에서 함수를 실행 하는 경우 앞서 본 것과 동일한 결과 생성 해야 합니다.

## <a name="connect-to-your-pipeline"></a>파이프라인에 연결
이제 새 사용자 지정 기술이 있으므로 기능에 추가할 수 있습니다. 아래 예제에서는 (이 확장할 수 있습니다 위치 및 사용자에서) 문서에는 조직에 설명을 추가 하려면 기술을 호출 하는 방법을 보여 줍니다. 대체 `[your-entity-search-app-name]` 앱의 이름입니다.

```json
{
    "skills": [
      "[... your existing skills remain here]",  
      {
        "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
        "description": "Our new Bing entity search custom skill",
        "uri": "https://[your-entity-search-app-name].azurewebsites.net/api/EntitySearch?code=[enter default host key here]",
          "context": "/document/merged_content/organizations/*",
          "inputs": [
            {
              "name": "name",
              "source": "/document/merged_content/organizations/*"
            }
          ],
          "outputs": [
            {
              "name": "description",
              "targetName": "description"
            }
          ]
      }
  ]
}
```

여기에 기본 제공 계산 하는 것 [엔터티 인식 기술](cognitive-search-skill-entity-recognition.md) 기술에 있어야 하는 조직 목록으로 문서를 한층 다양해졌습니다. 참조용으로 필요한 데이터를 생성 하는 데 충분 하다는 엔터티 추출 기술 구성을 다음과 같습니다.

```json
{
    "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
    "name": "#1",
    "description": "Organization name extraction",
    "context": "/document/merged_content",
    "categories": [ "Organization" ],
    "defaultLanguageCode": "en",
    "inputs": [
        {
            "name": "text",
            "source": "/document/merged_content"
        },
        {
            "name": "languageCode",
            "source": "/document/language"
        }
    ],
    "outputs": [
        {
            "name": "organizations",
            "targetName": "organizations"
        }
    ]
},
```

## <a name="next-steps"></a>다음 단계
축하합니다. 첫 번째 사용자 지정 보강 자를 만들었습니다. 이제 동일한 패턴을 따라 고유한 사용자 지정 기능을 추가할 수 있습니다. 

+ [인식 검색 파이프라인에 사용자 지정 기술 추가](cognitive-search-custom-skill-interface.md)
+ [기능을 정의하는 방법](cognitive-search-defining-skillset.md)
+ [기능 만들기(REST)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)
+ [보강 필드를 매핑하는 방법](cognitive-search-output-field-mapping.md)
