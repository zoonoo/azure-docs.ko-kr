---
title: 피드백 루프 - Personalizer
titleSuffix: Azure Cognitive Services
description: 이 C# 빠른 시작에서는 Personalizer 서비스를 사용하여 콘텐츠를 개인 설정합니다.
services: cognitive-services
author: edjez
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: overview
ms.date: 05/08/2019
ms.author: edjez
ms.openlocfilehash: b0dc8fbbb80a4d03b2cb64d09ffe9a36883c5bf9
ms.sourcegitcommit: bb85a238f7dbe1ef2b1acf1b6d368d2abdc89f10
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/10/2019
ms.locfileid: "65521381"
---
# <a name="quickstart-personalize-content-using-c"></a>빠른 시작: C#을 사용하여 콘텐츠 개인 설정 

이 C# 빠른 시작에서는 Personalizer 서비스를 사용하여 맞춤형 콘텐츠를 표시합니다.

이 샘플에서는 C#용 Personalizer 클라이언트 라이브러리를 사용하여 다음 작업을 수행하는 방법을 보여 줍니다. 

 * 개인 설정에 대한 작업 목록의 순위를 지정합니다.
 * 지정된 이벤트에 대한 사용자의 선택에 따라 상위 작업에 할당할 보상을 보고합니다.

Personalizer를 시작하는 단계는 다음과 같습니다.

1. SDK 참조 
1. 사용자에게 표시할 작업의 순위를 지정하는 코드 작성
1. 보상을 보내서 루프를 학습하는 코드 작성

## <a name="prerequisites"></a>필수 조건

* 구독 키 및 엔드포인트 서비스 URL을 가져오려면 [Personalizer 서비스](how-to-settings.md)가 필요합니다. 
* [Visual Studio 2015 또는 2017](https://visualstudio.microsoft.com/downloads/)
* [Microsoft.Azure.CognitiveServices.Personalizer](https://go.microsoft.com/fwlink/?linkid=2092272) SDK NuGet 패키지. 설치 지침은 아래에 제공됩니다.

## <a name="change-the-model-update-frequency"></a>모델 업데이트 빈도 변경

Azure Portal의 Personalizer 리소스에서 **모델 업데이트 빈도**를 10초로 변경합니다. 이렇게 하면 서비스가 빠르게 학습되어 각 반복에 대한 상위 작업이 변경되는 상태를 확인할 수 있습니다.

![모델 업데이트 빈도 변경](./media/settings/configure-model-update-frequency-settings.png)

## <a name="creating-a-new-console-app-and-referencing-the-personalizer-sdk"></a>새 콘솔 앱을 만들고 Personalizer SDK 참조 

<!--
Get the latest code as a Visual Studio solution from [GitHub] (add link).
-->

1. Visual Studio에서 새로운 Visual C# 콘솔 앱을 만듭니다.
1. Personalizer 클라이언트 라이브러리 NuGet 패키지를 설치합니다. 메뉴에서 **도구**, **Nuget 패키지 관리자**, **솔루션에 대한 NuGet 패키지 관리**를 차례로 선택합니다.
1. **찾아보기** 탭을 선택하고, **검색** 상자에 `Microsoft.Azure.CognitiveServices.Personalizer`을 입력합니다.
1. **Microsoft.Azure.CognitiveServices.Personalizer**가 표시되면 이를 선택합니다.
1. 프로젝트 이름 옆에 있는 확인란을 선택하고 **설치**를 선택합니다.

## <a name="add-the-code-and-put-in-your-personalizer-and-azure-keys"></a>코드를 추가하고 Personalizer 및 Azure 키 입력

1. Program.cs를 다음 코드로 바꿉니다. 
1. `serviceKey` 값을 유효한 Personalizer 구독 키로 바꿉니다.
1. `serviceEndpoint`를 해당 서비스 엔드포인트로 바꿉니다. 예는 `https://westus2.api.cognitive.microsoft.com/`입니다.
1. 프로그램을 실행합니다.

## <a name="add-code-to-rank-the-actions-you-want-to-show-to-your-users"></a>사용자에게 표시할 작업의 순위를 지정하는 코드 추가

다음은 SDK를 사용하여 사용자 정보, 기능 및 _작업_ 콘텐츠에 대한 정보를 Personalizer에 전달하는 완전한 C# 코드입니다. Personalizer는 사용자에게 표시할 상위 작업을 반환합니다.  

```csharp
using Microsoft.Azure.CognitiveServices.Personalizer;
using Microsoft.Azure.CognitiveServices.Personalizer.Models;
using System;
using System.Collections.Generic;
using System.Linq;

namespace PersonalizerExample
{
    class Program
    {
        // The key specific to your personalizer service instance; e.g. "0123456789abcdef0123456789ABCDEF"
        private const string ApiKey = "";

        // The endpoint specific to your personalizer service instance; e.g. https://westus2.api.cognitive.microsoft.com/
        private const string ServiceEndpoint = "";

        static void Main(string[] args)
        {
            int iteration = 1;
            bool runLoop = true;

            // Get the actions list to choose from personalizer with their features.
            IList<RankableAction> actions = GetActions();

            // Initialize Personalizer client.
            PersonalizerClient client = InitializePersonalizerClient(ServiceEndpoint);

            do
            {
                Console.WriteLine("\nIteration: " + iteration++);

                // Get context information from the user.
                string timeOfDayFeature = GetUsersTimeOfDay();
                string tasteFeature = GetUsersTastePreference();

                // Create current context from user specified data.
                IList<object> currentContext = new List<object>() {
                    new { time = timeOfDayFeature },
                    new { taste = tasteFeature }
                };

                // Exclude an action for personalizer ranking. This action will be held at its current position.
                IList<string> excludeActions = new List<string> { "juice" };

                // Generate an ID to associate with the request.
                string eventId = Guid.NewGuid().ToString();

                // Rank the actions
                var request = new RankRequest(actions, currentContext, excludeActions, eventId);
                RankResponse response = client.Rank(request);

                Console.WriteLine("\nPersonalizer service thinks you would like to have: " + response.RewardActionId + ". Is this correct? (y/n)");

                float reward = 0.0f;
                string answer = GetKey();

                if (answer == "Y")
                {
                    reward = 1;
                    Console.WriteLine("\nGreat! Enjoy your food.");
                }
                else if (answer == "N")
                {
                    reward = 0;
                    Console.WriteLine("\nYou didn't like the recommended food choice.");
                }
                else
                {
                    Console.WriteLine("\nEntered choice is invalid. Service assumes that you didn't like the recommended food choice.");
                }

                Console.WriteLine("\nPersonalizer service ranked the actions with the probabilities as below:");
                foreach (var rankedResponse in response.Ranking)
                {
                    Console.WriteLine(rankedResponse.Id + " " + rankedResponse.Probability);
                }

                // Send the reward for the action based on user response.
                client.Reward(response.EventId, new RewardRequest(reward));

                Console.WriteLine("\nPress q to break, any other key to continue:");
                runLoop = !(GetKey() == "Q");

            } while (runLoop);
        }

        /// <summary>
        /// Initializes the personalizer client.
        /// </summary>
        /// <param name="url">Azure endpoint</param>
        /// <returns>Personalizer client instance</returns>
        static PersonalizerClient InitializePersonalizerClient(string url)
        {
            PersonalizerClient client = new PersonalizerClient(
                new ApiKeyServiceClientCredentials(ApiKey)) {Endpoint = url};

            return client;
        }

        /// <summary>
        /// Get users time of the day context.
        /// </summary>
        /// <returns>Time of day feature selected by the user.</returns>
        static string GetUsersTimeOfDay()
        {
            string[] timeOfDayFeatures = new string[] { "morning", "afternoon", "evening", "night" };

            Console.WriteLine("\nWhat time of day is it (enter number)? 1. morning 2. afternoon 3. evening 4. night");
            if (!int.TryParse(GetKey(), out int timeIndex) || timeIndex < 1 || timeIndex > timeOfDayFeatures.Length)
            {
                Console.WriteLine("\nEntered value is invalid. Setting feature value to " + timeOfDayFeatures[0] + ".");
                timeIndex = 1;
            }

            return timeOfDayFeatures[timeIndex - 1];
        }

        /// <summary>
        /// Gets user food preference.
        /// </summary>
        /// <returns>Food taste feature selected by the user.</returns>
        static string GetUsersTastePreference()
        {
            string[] tasteFeatures = new string[] { "salty", "sweet" };

            Console.WriteLine("\nWhat type of food would you prefer (enter number)? 1. salty 2. sweet");
            if (!int.TryParse(GetKey(), out int tasteIndex) || tasteIndex < 1 || tasteIndex > tasteFeatures.Length)
            {
                Console.WriteLine("\nEntered value is invalid. Setting feature value to " + tasteFeatures[0] + ".");
                tasteIndex = 1;
            }

            return tasteFeatures[tasteIndex - 1];
        }

        /// <summary>
        /// Creates personalizer actions feature list.
        /// </summary>
        /// <returns>List of actions for personalizer.</returns>
        static IList<RankableAction> GetActions()
        {
            IList<RankableAction> actions = new List<RankableAction>
            {
                new RankableAction
                {
                    Id = "pasta",
                    Features =
                    new List<object>() { new { taste = "salty", spiceLevel = "medium" }, new { nutritionLevel = 5, cuisine = "italian" } }
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
                    new List<object>() { new { taste = "salty", spiceLevel = "low" }, new { nutritionLevel = 8 } }
                }
            };

            return actions;
        }

        private static string GetKey()
        {
            return Console.ReadKey().Key.ToString().Last().ToString().ToUpper();
        }
    }
}
```

## <a name="run-the-program"></a>프로그램 실행

프로그램을 빌드하고 실행합니다. 빠른 시작 프로그램은 기능으로 알려진 사용자 기본 설정을 수집하기 위해 몇 가지 질문을 합니다.

![빠른 시작 프로그램은 기능으로 알려진 사용자 기본 설정을 수집하기 위해 몇 가지 질문을 합니다.](media/csharp-quickstart-commandline-feedback-loop/quickstart-program-feedback-loop-example.png)

## <a name="clean-up-resources"></a>리소스 정리
빠른 시작을 완료하면 이 빠른 시작에서 생성된 모든 파일을 제거하세요. 

## <a name="next-steps"></a>다음 단계

[Personalizer의 작동 원리](how-personalizer-works.md)


