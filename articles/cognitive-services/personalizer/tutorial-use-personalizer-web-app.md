---
title: 웹 앱 사용-Personalizer
description: 'Personalizer 루프를 사용 하 여 c # .NET 웹 앱을 사용자 지정 하 여 작업 (기능 포함) 및 컨텍스트 기능에 따라 사용자에 게 올바른 콘텐츠를 제공 합니다.'
ms.topic: troubleshooting
ms.date: 06/10/2020
ms.author: diberry
ms.openlocfilehash: 9def69a1540e81b99723c16ad34ba522d1737c7f
ms.sourcegitcommit: 537c539344ee44b07862f317d453267f2b7b2ca6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/11/2020
ms.locfileid: "84713968"
---
# <a name="add-personalizer-to-a-net-web-app"></a>.NET 웹 앱에 Personalizer 추가

Personalizer 루프를 사용 하 여 c # .NET 웹 앱을 사용자 지정 하 여 작업 (기능 포함) 및 컨텍스트 기능에 따라 사용자에 게 올바른 콘텐츠를 제공 합니다.

**이 자습서에서 학습할 내용은 다음과 같습니다.**

<!-- green checkmark -->
> [!div class="checklist"]
> * Personalizer 키 및 끝점 설정
> * 기능 수집
> * 순위 및 보상 Api 호출
> * _RewardActionId_ 로 지정 된 위쪽 동작 표시



## <a name="select-the-best-content-for-a-web-app"></a>웹 앱에 가장 적합 한 콘텐츠를 선택 합니다.

웹 앱은 Personalizer를 사용 해야 합니다. 웹 페이지에는 단일 상위 항목 (rewardActionId)으로 개인 설정 해야 하는 _작업_ 목록 (일부 콘텐츠 형식)이 표시 됩니다. 작업 목록의 예로는 뉴스 기사, 단추 배치 위치 및 제품 이름에 대 한 단어 선택이 있습니다.

컨텍스트 기능과 함께 작업 목록을 Personalizer 루프로 보냅니다. Personalizer는 단일 최상의 작업을 선택 하 고 웹 앱은 해당 작업을 표시 합니다.

이 자습서에서 작업은 음식 유형입니다.

* 파스타
* 아이스크림
* 주스
* 샐러드
* 팝콘
* 커피
* 스프

사용자의 작업에 대 한 자세한 내용을 보려면 각 Rank API 요청으로 기능 및 _컨텍스트 기능이_ _포함 된 _ 작업_ 을 모두 Personalizer.

모델의 **기능은** 웹 앱 사용자 기반의 멤버 간에 집계 (그룹화) 할 수 있는 작업 또는 컨텍스트에 대 한 정보입니다. 기능은 개별적으로 특정 (예: 사용자 ID) 또는 매우 특정 한 시간 (예: 정확한 하루)이 _아닙니다_ .

### <a name="actions-with-features"></a>기능이 포함 된 작업

각 작업 (콘텐츠 항목)에는 음식 항목을 구분 하는 데 도움이 되는 기능이 있습니다.

기능은 Azure Portal에서 루프 구성의 일부로 구성 되지 않습니다. 대신 각 Rank API 호출을 사용 하 여 JSON 개체로 전송 됩니다. 이렇게 하면 작업 및 해당 기능을 시간이 지남에 따라 증가, 변경 및 축소 하 여 Personalizer 추세를 따를 수 있습니다.

```csharp
 /// <summary>
  /// Creates personalizer actions feature list.
  /// </summary>
  /// <returns>List of actions for personalizer.</returns>
  private IList<RankableAction> GetActions()
  {
      IList<RankableAction> actions = new List<RankableAction>
      {
          new RankableAction
          {
              Id = "pasta",
              Features =
              new List<object>() { new { taste = "savory", spiceLevel = "medium" }, new { nutritionLevel = 5, cuisine = "italian" } }
          },

          new RankableAction
          {
              Id = "ice cream",
              Features =
              new List<object>() { new { taste = "sweet", spiceLevel = "none" }, new { nutritionalLevel = 2 } }
          },

          new RankableAction
          {
              Id = "juice",
              Features =
              new List<object>() { new { taste = "sweet", spiceLevel = "none" }, new { nutritionLevel = 5 }, new { drink = true } }
          },

          new RankableAction
          {
              Id = "salad",
              Features =
              new List<object>() { new { taste = "sour", spiceLevel = "low" }, new { nutritionLevel = 8 } }
          },

          new RankableAction
          {
              Id = "popcorn",
              Features =
              new List<object>() { new { taste = "salty", spiceLevel = "none" }, new { nutritionLevel = 3 } }
          },

          new RankableAction
          {
              Id = "coffee",
              Features =
              new List<object>() { new { taste = "bitter", spiceLevel = "none" }, new { nutritionLevel = 3 }, new { drink = true } }
          },

          new RankableAction
          {
              Id = "soup",
              Features =
              new List<object>() { new { taste = "sour", spiceLevel = "high" }, new { nutritionLevel =  7} }
          }
      };

      return actions;
  }
```


## <a name="context-features"></a>컨텍스트 기능

컨텍스트 기능은 Personalizer 작업의 컨텍스트를 이해 하는 데 도움이 됩니다. 이 샘플 응용 프로그램에 대 한 컨텍스트에는 다음이 포함 됩니다.

* 시간-오전, 오후, 야간, 야간
* 사용자가 선호 하는 salty, bitter, sour 또는 sweet and savory
* 브라우저의 컨텍스트-사용자 에이전트, 지리적 위치, 참조 페이지

```csharp
/// <summary>
/// Get users time of the day context.
/// </summary>
/// <returns>Time of day feature selected by the user.</returns>
private string GetUsersTimeOfDay()
{
    Random rnd = new Random();
    string[] timeOfDayFeatures = new string[] { "morning", "noon", "afternoon", "evening", "night", "midnight" };
    int timeIndex = rnd.Next(timeOfDayFeatures.Length);
    return timeOfDayFeatures[timeIndex];
}

/// <summary>
/// Gets user food preference.
/// </summary>
/// <returns>Food taste feature selected by the user.</returns>
private string GetUsersTastePreference()
{
    Random rnd = new Random();
    string[] tasteFeatures = new string[] { "salty", "bitter", "sour", "savory", "sweet" };
    int tasteIndex = rnd.Next(tasteFeatures.Length);
    return tasteFeatures[tasteIndex];
}
```

## <a name="how-does-the-web-app-use-personalizer"></a>웹 앱에서 Personalizer를 어떻게 사용 하나요?

웹 앱은 Personalizer를 사용 하 여 음식 선택 목록에서 최상의 작업을 선택 합니다. 이렇게 하려면 각 Rank API 호출로 다음 정보를 전송 합니다.
* 및와 같은 기능을 사용 하는 **작업** `taste``spiceLevel`
* **context** `time` 일, 사용자의 `taste` 기본 설정 및 브라우저의 사용자 에이전트 정보 및 컨텍스트 기능과 같은 컨텍스트 기능
* 주스와 같은 **제외할 작업**
* **eventid**-Rank API에 대 한 각 호출 마다 다릅니다.

## <a name="personalizer-model-features-in-a-web-app"></a>웹 앱의 Personalizer 모델 기능

Personalizer에는 작업 (콘텐츠) 및 현재 컨텍스트 (사용자 및 환경)를 위한 기능이 필요 합니다. 기능은 작업을 모델의 현재 컨텍스트에 정렬 하는 데 사용 됩니다. 모델은 작업, 컨텍스트 및 해당 기능에 대 한 Personalizer의 과거 정보를 파악 하 여 교육을 내릴 수 있도록 하는 표현입니다.

기능을 포함 한 모델은 Azure Portal의 **모델 업데이트 빈도** 설정에 따라 일정에 따라 업데이트 됩니다.

> [!CAUTION]
> 이 응용 프로그램의 기능은 기능 및 기능 값을 보여 주기 위한 것 이지만 웹 앱에서 사용할 수 있는 최상의 기능은 아닙니다.

### <a name="plan-for-features-and-their-values"></a>기능 및 해당 값에 대 한 계획

기술 아키텍처에서 스키마 나 모델에 적용 하는 것과 동일한 계획 및 디자인을 사용 하 여 기능을 선택 해야 합니다. 비즈니스 논리 또는 타사 시스템을 사용 하 여 기능 값을 설정할 수 있습니다. 기능 값은 그룹이 나 기능 클래스에 적용 되지 않는 구체적이 지 않아야 합니다.

### <a name="generalize-feature-values"></a>일반화 기능 값

#### <a name="generalize-into-categories"></a>범주로 일반화

이 앱은 `time` 기능으로를 사용 하지만,,, 등의 범주로 시간을 그룹화 `morning` `afternoon` `evening` `night` 합니다. 이는 시간 정보를 사용 하지만와 같은 매우 구체적인 방법이 아닌 예제입니다 `10:05:01 UTC+2` .

#### <a name="generalize-into-parts"></a>파트로 일반화

이 앱은 브라우저의 HTTP 요청 기능을 사용 합니다. 모든 데이터를 포함 하는 매우 구체적인 문자열로 시작 합니다. 예를 들면 다음과 같습니다.

```http
Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/530.99 (KHTML, like Gecko) Chrome/80.0.3900.140 Safari/537.36
```

**Httprequestfeatures** 클래스 라이브러리는 개별 값이 포함 된 **useragentinfo** 개체에이 문자열을 일반화 합니다. 너무 구체적인 값은 빈 문자열로 설정 됩니다. 요청에 대 한 컨텍스트 기능이 전송 되 면 JSON 형식은 다음과 같습니다.

```JSON
{
  "httpRequestFeatures": {
    "_synthetic": false,
    "OUserAgent": {
      "_ua": "",
      "_DeviceBrand": "",
      "_DeviceFamily": "Other",
      "_DeviceIsSpider": false,
      "_DeviceModel": "",
      "_OSFamily": "Windows",
      "_OSMajor": "10",
      "DeviceType": "Desktop"
    }
  }
}
```


## <a name="using-sample-web-app"></a>샘플 웹 앱 사용

샘플 브라우저 기반 웹 앱 (모든 코드 제공 됨)에는 앱을 실행 하기 위해 다음 응용 프로그램이 설치 되어 있어야 합니다.

다음 소프트웨어를 설치합니다.

* [.Net core 2.1](https://dotnet.microsoft.com/download/dotnet-core/2.1) -샘플 백 엔드 서버가 .net core를 사용 합니다.
* [Node.js](https://nodejs.org/) -클라이언트/프런트 엔드가이 응용 프로그램에 종속 되어 있습니다.
* [Visual studio 2019](https://visualstudio.microsoft.com/vs/)또는 [.NET Core CLI](https://docs.microsoft.com/dotnet/core/tools/) visual studio 2019의 개발자 환경 또는 .NET Core CLI를 사용 하 여 앱을 빌드하고 실행 합니다.

### <a name="set-up-the-sample"></a>샘플 설정
1. Azure Personalizer 샘플 리포지토리를 복제 합니다.

    ```bash
    git clone https://github.com/Azure-Samples/cognitive-services-personalizer-samples.git
    ```

1. _Samples/HttpRequestFeatures_ 로 이동 하 여 솔루션을 엽니다 `HttpRequestFeaturesExample.sln` .

    요청 하는 경우 Visual Studio에서 Personalizer의 .NET 패키지를 업데이트 하도록 허용 합니다.

### <a name="set-up-azure-personalizer-service"></a>Azure Personalizer Service 설정

1. Azure Portal에서 [Personalizer 리소스를 만듭니다](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer) .

1. Azure Portal의 `Endpoint` `Key1` `Key2` **키 및 끝점** 탭에서를 찾고 또는 중 하나를 찾습니다. 사용자의 `PersonalizerServiceEndpoint` 및 `PersonalizerApiKey` 입니다.
1. 에appsettings.js을 `PersonalizerServiceEndpoint` 입력 **appsettings.json**합니다.
1. `PersonalizerApiKey`다음 방법 중 하나로를 [앱 비밀](https://docs.microsoft.com/aspnet/core/security/app-secrets) 으로 구성 합니다.

    * .NET Core CLI 사용 하는 경우 명령을 사용할 수 있습니다 `dotnet user-secrets set "PersonalizerApiKey" "<API Key>"` .
    * Visual Studio를 사용 하는 경우 프로젝트를 마우스 오른쪽 단추로 클릭 하 고 **사용자 암호 관리** 메뉴 옵션을 선택 하 여 Personalizer 키를 구성할 수 있습니다. 이렇게 하면 Visual Studio에서 다음과 `secrets.json` 같이 키를 추가할 수 있는 파일을 엽니다.

    ```JSON
    {
      "PersonalizerApiKey": "<your personalizer key here>",
    }
    ```

## <a name="run-the-sample"></a>샘플 실행

다음 방법 중 하나를 사용 하 여 HttpRequestFeaturesExample를 빌드하고 실행 합니다.

* Visual Studio 2019: **F5** 키를 누릅니다.
* .NET Core CLI: `dotnet build` then`dotnet run`

웹 브라우저를 통해 순위 요청 및 보상 요청을 보내고 해당 응답을 볼 수 있을 뿐만 아니라 사용자 환경에서 추출 된 http 요청 기능을 볼 수 있습니다.

> [!div class="mx-imgBorder"]
> ![HTTPRequestFeaturesExample 프로젝트를 빌드하고 실행 합니다. 단일 페이지 응용 프로그램을 표시 하는 브라우저 창이 열립니다.](./media/tutorial-web-app/web-app-single-page.png)

## <a name="demonstrate-the-personalizer-loop"></a>Personalizer 루프를 보여 줍니다.

1. **새 순위 요청 생성** 단추를 선택 하 여 rank API 호출에 대 한 새 JSON 개체를 만듭니다. 이렇게 하면 작업 (기능 포함) 및 컨텍스트 기능이 만들어지고 JSON이 표시 되는 모양을 확인할 수 있도록 값이 표시 됩니다.

    이후 응용 프로그램의 경우 클라이언트, 서버, 둘 중 하나 또는 다른 서비스에 대 한 호출에서 작업 및 기능이 생성 될 수 있습니다.

1. 서버에 JSON 개체를 보내려면 **순위 요청 보내기** 를 선택 합니다. 서버는 Personalizer Rank API를 호출 합니다. 서버는 응답을 수신 하 고 가장 높은 순위 작업을 클라이언트에 게 표시 하도록 반환 합니다.

1. 보상 값을 설정한 다음, **보상 요청 보내기** 단추를 선택 합니다. 보상 값을 변경 하지 않는 경우 클라이언트 응용 프로그램은 항상 Personalizer에 값을 보냅니다 `1` .

    > [!div class="mx-imgBorder"]
    > ![HTTPRequestFeaturesExample 프로젝트를 빌드하고 실행 합니다. 단일 페이지 응용 프로그램을 표시 하는 브라우저 창이 열립니다.](./media/tutorial-web-app/reward-score-api-call.png)

    사용자 고유의 향후 응용 프로그램의 경우 클라이언트의 사용자 동작에서 정보를 수집한 후 서버에서 비즈니스 논리를 사용 하 여 보상 점수를 생성 하는 작업이 발생할 수 있습니다.

## <a name="understand-the-sample-web-app"></a>샘플 웹 앱 이해

샘플 웹 앱에는 기능 컬렉션을 관리 하 고 Personalizer 끝점에 대 한 HTTP 호출을 보내고 받는 **c # .net** 서버가 있습니다.

샘플 웹 앱은 **녹아웃 프런트 엔드 클라이언트 응용 프로그램** 을 사용 하 여 기능을 캡처하고 단추를 클릭 하 고 .net 서버에 데이터를 보내는 등의 사용자 인터페이스 동작을 처리 합니다.

다음 섹션에서는 개발자가 Personalizer를 사용 하기 위해 이해 해야 하는 서버 및 클라이언트 부분에 대해 설명 합니다.

## <a name="rank-api-client-application-sends-context-to-server"></a>순위 API: 클라이언트 응용 프로그램이 서버에 컨텍스트를 보냅니다.

클라이언트 응용 프로그램은 사용자의 브라우저 _사용자 에이전트_를 수집 합니다.

> [!div class="mx-imgBorder"]
> ![HTTPRequestFeaturesExample 프로젝트를 빌드하고 실행 합니다. 단일 페이지 응용 프로그램을 표시 하는 브라우저 창이 열립니다.](./media/tutorial-web-app/user-agent.png)

## <a name="rank-api-server-application-calls-personalizer"></a>순위 API: 서버 응용 프로그램 호출 Personalizer

이는 클라이언트 응용 프로그램을 사용 하는 일반적인 .NET 웹 앱으로, 대부분의 상용구 판금 코드가 제공 됩니다. Personalizer에 한정 되지 않은 코드는 다음 코드 조각에서 제거 되므로 Personalizer 특정 코드에 집중할 수 있습니다.

### <a name="create-personalizer-client"></a>Personalizer client 만들기

서버의 **Startup.cs**에서 Personalizer 끝점 및 키를 사용 하 여 Personalizer 클라이언트를 만듭니다. 클라이언트 응용 프로그램은이 앱에서 Personalizer와 통신할 필요가 없으며, 대신 해당 SDK를 호출 하는 서버에 의존 합니다.

웹 서버의 .NET 시작 코드는 다음과 같습니다.

```csharp
using Microsoft.Azure.CognitiveServices.Personalizer;
// ... other using statements removed for brevity

namespace HttpRequestFeaturesExample
{
    public class Startup
    {
        public Startup(IConfiguration configuration)
        {
            Configuration = configuration;
        }

        public IConfiguration Configuration { get; }

        // This method gets called by the runtime. Use this method to add services to the container.
        public void ConfigureServices(IServiceCollection services)
        {
            string personalizerApiKey = Configuration.GetSection("PersonalizerApiKey").Value;
            string personalizerEndpoint = Configuration.GetSection("PersonalizerConfiguration:ServiceEndpoint").Value;
            if (string.IsNullOrEmpty(personalizerEndpoint) || string.IsNullOrEmpty(personalizerApiKey))
            {
                throw new ArgumentException("Missing Azure Personalizer endpoint and/or api key.");
            }
            services.AddSingleton(client =>
            {
                return new PersonalizerClient(new ApiKeyServiceClientCredentials(personalizerApiKey))
                {
                    Endpoint = personalizerEndpoint
                };
            });

            services.AddMvc();
        }

        // ... code removed for brevity
    }
}
```

### <a name="select-best-action"></a>최적 작업 선택

서버의 **PersonalizerController.cs**에서 **GenerateRank** 서버 api는 Rank api 호출 준비를 요약 합니다.

* `eventId`순위 호출에 대 한 새 만들기
* 작업 목록 가져오기
* 사용자 로부터 기능 목록을 가져오고 컨텍스트 기능을 만듭니다.
* 필요에 따라 제외 된 작업을 설정 합니다.
* Rank API 호출, 클라이언트에 결과 반환

```csharp
/// <summary>
/// Creates a RankRequest with user time of day, HTTP request features,
/// and taste as the context and several different foods as the actions
/// </summary>
/// <returns>RankRequest with user info</returns>
[HttpGet("GenerateRank")]
public RankRequest GenerateRank()
{
    string eventId = Guid.NewGuid().ToString();

    // Get the actions list to choose from personalizer with their features.
    IList<RankableAction> actions = GetActions();

    // Get context information from the user.
    HttpRequestFeatures httpRequestFeatures = GetHttpRequestFeaturesFromRequest(Request);
    string timeOfDayFeature = GetUsersTimeOfDay();
    string tasteFeature = GetUsersTastePreference();

    // Create current context from user specified data.
    IList<object> currentContext = new List<object>() {
            new { time = timeOfDayFeature },
            new { taste = tasteFeature },
            new { httpRequestFeatures }
    };

    // Exclude an action for personalizer ranking. This action will be held at its current position.
    IList<string> excludeActions = new List<string> { "juice" };

    // Rank the actions
    return new RankRequest(actions, currentContext, excludeActions, eventId);
}
```

작업 (기능 포함) 및 현재 컨텍스트 기능을 모두 포함 하는 Personalizer로 전송 된 JSON은 다음과 같습니다.

```json
{
    "contextFeatures": [
        {
            "time": "morning"
        },
        {
            "taste": "savory"
        },
        {
            "httpRequestFeatures": {
                "_synthetic": false,
                "MRefer": {
                    "referer": "http://localhost:51840/"
                },
                "OUserAgent": {
                    "_ua": "",
                    "_DeviceBrand": "",
                    "_DeviceFamily": "Other",
                    "_DeviceIsSpider": false,
                    "_DeviceModel": "",
                    "_OSFamily": "Windows",
                    "_OSMajor": "10",
                    "DeviceType": "Desktop"
                }
            }
        }
    ],
    "actions": [
        {
            "id": "pasta",
            "features": [
                {
                    "taste": "savory",
                    "spiceLevel": "medium"
                },
                {
                    "nutritionLevel": 5,
                    "cuisine": "italian"
                }
            ]
        },
        {
            "id": "ice cream",
            "features": [
                {
                    "taste": "sweet",
                    "spiceLevel": "none"
                },
                {
                    "nutritionalLevel": 2
                }
            ]
        },
        {
            "id": "juice",
            "features": [
                {
                    "taste": "sweet",
                    "spiceLevel": "none"
                },
                {
                    "nutritionLevel": 5
                },
                {
                    "drink": true
                }
            ]
        },
        {
            "id": "salad",
            "features": [
                {
                    "taste": "sour",
                    "spiceLevel": "low"
                },
                {
                    "nutritionLevel": 8
                }
            ]
        },
        {
            "id": "popcorn",
            "features": [
                {
                    "taste": "salty",
                    "spiceLevel": "none"
                },
                {
                    "nutritionLevel": 3
                }
            ]
        },
        {
            "id": "coffee",
            "features": [
                {
                    "taste": "bitter",
                    "spiceLevel": "none"
                },
                {
                    "nutritionLevel": 3
                },
                {
                    "drink": true
                }
            ]
        },
        {
            "id": "soup",
            "features": [
                {
                    "taste": "sour",
                    "spiceLevel": "high"
                },
                {
                    "nutritionLevel": 7
                }
            ]
        }
    ],
    "excludedActions": [
        "juice"
    ],
    "eventId": "82ac52da-4077-4c7d-b14e-190530578e75",
    "deferActivation": null
}
```

### <a name="return-personalizer-rewardactionid-to-client"></a>Personalizer rewardActionId를 client로 반환

Rank API는 선택한 최상의 작업 **rewardActionId** 를 서버에 반환 합니다.

**RewardActionId**에 반환 된 작업을 표시 합니다.

```json
{
    "ranking": [
        {
            "id": "popcorn",
            "probability": 0.833333254
        },
        {
            "id": "salad",
            "probability": 0.03333333
        },
        {
            "id": "juice",
            "probability": 0
        },
        {
            "id": "soup",
            "probability": 0.03333333
        },
        {
            "id": "coffee",
            "probability": 0.03333333
        },
        {
            "id": "pasta",
            "probability": 0.03333333
        },
        {
            "id": "ice cream",
            "probability": 0.03333333
        }
    ],
    "eventId": "82ac52da-4077-4c7d-b14e-190530578e75",
    "rewardActionId": "popcorn"
}
```

### <a name="client-displays-the-rewardactionid-action"></a>클라이언트에서 rewardActionId 작업을 표시 합니다.

이 자습서에서는 값이 `rewardActionId` 표시 됩니다.

사용자 고유의 향후 응용 프로그램에서이는 텍스트, 단추 또는 웹 페이지의 섹션이 강조 표시 될 수 있습니다. 콘텐츠 순서가 아니라 점수의 사후 분석에 대해 목록이 반환 됩니다. `rewardActionId`콘텐츠만 표시 됩니다.

## <a name="reward-api-collect-information-for-reward"></a>보상 API: 보상 정보 수집

[보상 점수](concept-rewards.md) 는 기능이 계획 되는 것 처럼 신중 하 게 계획 해야 합니다. 일반적으로 보상 점수는 0에서 1 사이의 값 이어야 합니다. 이 값은 사용자 동작을 기반으로 클라이언트 응용 프로그램에서 부분적으로 계산 되 고 비즈니스 논리 및 목표에 따라 서버에서 부분적으로 계산 될 _수 있습니다_ .

서버가 Personalizer 리소스에 대 한 Azure Portal에 구성 된 **보상 대기 시간** 내에 보상 API를 호출 하지 않으면 해당 이벤트에 대해 **기본 보상** (Azure Portal에도 구성 됨)이 사용 됩니다.

이 샘플 응용 프로그램에서는 값을 선택 하 여 보상에서 선택 항목에 미치는 영향을 확인할 수 있습니다.

## <a name="additional-ways-to-learn-from-this-sample"></a>이 샘플에서 배울 수 있는 추가 방법

이 샘플에서는 personalizer 리소스에 대해 Azure Portal에 구성 된 몇 가지 시간 기반 이벤트를 사용 합니다. 이러한 값을 사용 하 여 재생 한 다음이 샘플 웹 앱으로 돌아와서 변경 내용이 Rank 및 보상 호출에 어떻게 영향을 주는지 확인 합니다.

* 보상 대기 시간
* 모델 업데이트 빈도

추가로 재생할 수 있는 설정은 다음과 같습니다.
* 기본 보상
* 탐색 비율


## <a name="clean-up-resources"></a>리소스 정리

이 자습서를 완료 한 후에는 다음 리소스를 정리 합니다.

* 샘플 프로젝트 디렉터리를 삭제 합니다.
* Personalizer 리소스 삭제-Personalizer 리소스를 작업 전용으로 간주 하 고, 컨텍스트 전용은 여전히 foods as actions subject 도메인을 사용 하는 경우 리소스를 재사용 합니다.


## <a name="next-steps"></a>다음 단계
* [Personalizer의 작동 원리](how-personalizer-works.md)
* [기능](concepts-features.md): with 작업 및 컨텍스트를 사용 하 여 기능에 대 한 개념 알아보기
* [보상](concept-rewards.md): 보상 계산에 대 한 자세한 정보