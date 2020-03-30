---
title: Bing 엔터티 검색 API를 사용하는 사용자 지정 기술 예제
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search에서 AI가 풍부한 인덱싱 파이프라인에 매핑된 사용자 지정 기술에서 Bing 엔터티 검색 서비스를 사용하는 방법을 보여 줍니다.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 2994c55b39d30ff16a0ca135e93a116784feb201
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74113808"
---
# <a name="example-create-a-custom-skill-using-the-bing-entity-search-api"></a>예: Bing 엔터티 검색 API를 사용하여 사용자 지정 기술 만들기

이 예제에서는 웹 API 사용자 지정 기술을 만드는 방법을 알아봅니다. 이 기술은 위치, 공인 및 조직을 받아들이고 설명을 반환합니다. 이 예제에서는 [Azure Function을](https://azure.microsoft.com/services/functions/) 사용하여 [Bing 엔터티 검색 API를](https://azure.microsoft.com/services/cognitive-services/bing-entity-search-api/) 래핑하여 사용자 지정 기술 인터페이스를 구현합니다.

## <a name="prerequisites"></a>사전 요구 사항

+ 사용자 [지정 기술이](cognitive-search-custom-skill-interface.md) 구현해야 하는 입력/출력 인터페이스에 익숙하지 않은 경우 사용자 지정 기술 인터페이스 문서에 대해 읽어보십시오.

+ [!INCLUDE [cognitive-services-bing-entity-search-signup-requirements](../../includes/cognitive-services-bing-entity-search-signup-requirements.md)]

+ Azure 개발 워크로드를 포함하여 [Visual Studio 2019](https://www.visualstudio.com/vs/) 이상 설치합니다.

## <a name="create-an-azure-function"></a>Azure Function 만들기

이 예제에서는 Azure Function을 사용하여 웹 API를 호스팅하지만 필수는 아닙니다.  [인식 기술에 대한 인터페이스 요구 사항](cognitive-search-custom-skill-interface.md)을 충족하기만 하면, 사용하는 방식은 중요하지 않습니다. 그러나 Azure Functions를 사용하면 사용자 지정 기술을 쉽게 만들 수 있습니다.

### <a name="create-a-function-app"></a>함수 앱 만들기

1. 비주얼 스튜디오에서 파일 메뉴에서 **새** > **프로젝트를** 선택합니다.

1. 새 프로젝트 대화 상자에서 **설치됨**을 선택하고, **Visual C#** > **클라우드**를 확장하고, **Azure Functions**를 선택하고, 프로젝트의 이름을 입력한 다음, **확인**을 선택합니다. 함수 앱 이름은 C# 네임스페이스로 유효해야 하므로 밑줄, 하이픈 또는 기타 영숫자가 아닌 문자를 사용하지 마십시오.

1. **Azure 함수 v2(.NET 코어)를 선택합니다.** 버전 1에서도 수행할 수 있지만 아래에 작성된 코드는 v2 템플릿을 기반으로 합니다.

1. 형식을 **HTTP 트리거**로 선택합니다.

1. 스토리지 계정의 경우, 이 함수에 대한 스토리지가 필요하지 않으므로 **없음**을 선택할 수 있습니다.

1. **확인**을 선택하여 함수 프로젝트 및 HTTP 트리거 함수를 만듭니다.

### <a name="modify-the-code-to-call-the-bing-entity-search-service"></a>Bing 엔터티 검색 서비스를 호출하도록 코드를 수정합니다.

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
    /// AI enrichment pipeline.
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

Bing *엔터티* 검색 API에 등록할 때 얻은 키를 기반으로 `key` 상수에 고유한 키 값을 입력해야 합니다.

이 샘플에는 편의를 위해 필요한 모든 코드가 단일 파일에 포함되어 있습니다. [전원 기술 저장소에서](https://github.com/Azure-Samples/azure-search-power-skills/tree/master/Text/BingEntitySearch)동일한 기술의 약간 더 구조화 된 버전을 찾을 수 있습니다.

물론 파일의 이름을 에서 `Function1.cs` 로 `BingEntitySearch.cs`바꿀 수 있습니다.

## <a name="test-the-function-from-visual-studio"></a>Visual Studio에서 함수 테스트

**F5** 키를 눌러 프로그램을 실행하고 함수 동작을 테스트합니다. 이 경우 아래 함수를 사용하여 두 엔터티를 찾습니다. Postman 또는 Fiddler를 사용하여 아래와 같은 호출을 실행합니다.

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

함수 동작에 만족하면 게시할 수 있습니다.

1. **솔루션 탐색기**에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **게시**를 선택합니다. 새**게시** **만들기를 선택합니다.** > 

1. Visual Studio를 Azure 계정에 아직 연결하지 않았으면 **계정 추가...** 를 선택합니다.

1. 화면에 표시되는 메시지를 따릅니다. 앱 서비스, Azure 구독, 리소스 그룹, 호스팅 계획 및 사용하려는 저장소 계정에 대한 고유한 이름을 지정하라는 메시지가 표시됩니다. 아직 없는 경우 새 리소스 그룹, 새 호스팅 플랜 및 스토리지 계정을 만들 수 있습니다. 완료되면 **만들기를** 선택합니다.

1. 배포가 완료되면 사이트 URL을 확인합니다. 이 URL은 Azure에서 해당 함수 앱의 주소입니다. 

1. Azure [포털에서](https://portal.azure.com)리소스 그룹으로 이동하여 게시한 `EntitySearch` 함수를 찾습니다. **관리** 섹션 아래에 호스트 키가 표시됩니다. ‘기본’ 호스트 키의 **복사** 아이콘을 선택합니다.**  

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

이 예제에서는 로컬 환경에서 함수를 실행할 때 이전에 보았던 것과 동일한 결과를 생성해야 합니다.

## <a name="connect-to-your-pipeline"></a>파이프라인에 연결
이제 새 사용자 지정 기술이 있으므로 기능에 추가할 수 있습니다. 아래 예제에서는 문서의 조직에 설명을 추가하는 기술을 호출하는 방법을 보여 주며 위치 및 사람도 작업하도록 확장할 수 있습니다. 앱 `[your-entity-search-app-name]` 이름으로 바꿉니다.

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

여기서는 기본 제공 엔터티 인식 [기술이](cognitive-search-skill-entity-recognition.md) 기술 집합에 존재하고 조직 목록과 함께 문서를 보강할 것으로 기대하고 있습니다. 참고로 필요한 데이터를 생성하는 데 충분한 엔터티 추출 기술 구성은 다음과 같습니다.

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
축하합니다! 첫 번째 사용자 지정 기술을 만들었습니다. 이제 동일한 패턴을 따라 고유한 사용자 지정 기능을 추가할 수 있습니다. 자세한 내용을 보려면 다음 링크를 클릭하십시오.

+ [파워 스킬: 사용자 지정 기술의 리포지토리](https://github.com/Azure-Samples/azure-search-power-skills)
+ [AI 보강 파이프라인에 사용자 지정 기술 추가](cognitive-search-custom-skill-interface.md)
+ [기술 집합을 정의하는 방법](cognitive-search-defining-skillset.md)
+ [기술 집합 만들기(REST)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)
+ [보강 필드를 매핑하는 방법](cognitive-search-output-field-mapping.md)
