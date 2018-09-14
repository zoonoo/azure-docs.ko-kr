---
title: QnA Maker 및 LUIS 통합 - Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: QnA Maker와 LUIS 통합에 대한 단계별 자습서
services: cognitive-services
author: nstulasi
manager: sangitap
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 04/21/2018
ms.author: saneppal
ms.openlocfilehash: 18eae69867dc9774f63b11c762b22df4595bdce6
ms.sourcegitcommit: e2348a7a40dc352677ae0d7e4096540b47704374
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/05/2018
ms.locfileid: "43781750"
---
# <a name="integrate-qna-maker-and-luis-to-distribute-your-knowledge-base"></a>QnA Maker와 LUIS를 통합하여 기술 자료 배포
QnA Maker 기술 자료가 확장되면 단일 모놀리식 집합으로 유지 관리하기가 어려워지며, 기술 자료를 보다 작은 논리적 청크로 분할해야 합니다.

QnA Maker에서 간단하게 여러 기술 자료를 만들 수 있지만, 들어오는 질문을 적절한 기술 자료로 라우팅하기 위한 논리가 필요합니다. LUIS를 사용하여 이 작업을 수행할 수 있습니다.

## <a name="architecture"></a>아키텍처

![QnA Maker luis 아키텍처](../media/qnamaker-tutorials-qna-luis/qnamaker-luis-architecture.PNG)

위의 시나리오에서 QnA Maker는 먼저 LUIS 모델에서 들어오는 질문의 의도를 가져온 다음, 이 의도를 사용하여 올바른 QnA Maker 기술 자료로 라우팅합니다.

## <a name="prerequisites"></a>필수 조건
- [LUIS](https://www.luis.ai/) 포털에 로그인하고 [앱을 만듭니다](https://docs.microsoft.com/en-us/azure/cognitive-services/luis/create-new-app).
- 시나리오에 따라 [의도를 추가](https://docs.microsoft.com/en-us/azure/cognitive-services/luis/add-intents)합니다.
- LUIS 앱을 [학습](https://docs.microsoft.com/en-us/azure/cognitive-services/luis/luis-how-to-train)하고 [게시](https://docs.microsoft.com/en-us/azure/cognitive-services/luis/publishapp)합니다.
- [QnA Maker](https://qnamaker.ai)에 로그인하고 시나리오에 따라 기술 자료를 [만듭니다](https://www.qnamaker.ai/Create).
- 기술 자료를 테스트하고 게시합니다.

## <a name="qna-maker--luis-bot"></a>QnA Maker + LUIS 봇
1. 먼저 LUIS 템플릿을 사용하여 웹앱 봇을 만들고, 위에서 만든 LUIS 앱과 연결한 다음, 의도를 수정합니다. [여기](https://docs.microsoft.com/en-us/azure/cognitive-services/luis/luis-csharp-tutorial-build-bot-framework-sample)에서 자세한 단계를 참조하세요.

2. 다른 종속성과 함께 파일의 맨 위에 종속성을 추가합니다.

    ```
    using RestSharp;
    using System.Collections.Generic;
    using Newtonsoft.Json;
    ```
3. QnA Maker 서비스를 호출하기 위해 아래 클래스를 추가합니다.

    ```
        /// <summary>
        /// QnAMakerService is a wrapper over the QnA Maker REST endpoint
        /// </summary>
        [Serializable]
        public class QnAMakerService
        {
            private string qnaServiceHostName;
            private string knowledgeBaseId;
            private string endpointKey;
    
            /// <summary>
            /// Initialize a particular endpoint with it's details
            /// </summary>
            /// <param name="hostName">Hostname of the endpoint</param>
            /// <param name="kbId">Knowledge base ID</param>
            /// <param name="ek">Endpoint Key</param>
            public QnAMakerService(string hostName, string kbId, string ek)
            {
                qnaServiceHostName = hostName;
                knowledgeBaseId = kbId;
                endpointKey = ek;
            }
    
            /// <summary>
            /// Call the QnA Maker endpoint and get a response
            /// </summary>
            /// <param name="query">User question</param>
            /// <returns></returns>
            public string GetAnswer(string query)
            {
                var client = new RestClient( qnaServiceHostName + "/qnamaker/knowledgebases/" + knowledgeBaseId + "/generateAnswer");
                var request = new RestRequest(Method.POST);
                request.AddHeader("authorization", "EndpointKey " + endpointKey);
                request.AddHeader("content-type", "application/json");
                request.AddParameter("application/json", "{\"question\": \"" + query + "\"}", ParameterType.RequestBody);
                IRestResponse response = client.Execute(request);
    
                // Deserialize the response JSON
                QnAAnswer answer = JsonConvert.DeserializeObject<QnAAnswer>(response.Content);
    
                // Return the answer if present
                if (answer.answers.Count > 0)
                    return answer.answers[0].answer;
                else
                    return "No good match found.";
            }
        }
    
        /* START - QnA Maker Response Class */
        public class Metadata
        {
            public string name { get; set; }
            public string value { get; set; }
        }
    
        public class Answer
        {
            public IList<string> questions { get; set; }
            public string answer { get; set; }
            public double score { get; set; }
            public int id { get; set; }
            public string source { get; set; }
            public IList<object> keywords { get; set; }
            public IList<Metadata> metadata { get; set; }
        }
    
        public class QnAAnswer
        {
            public IList<Answer> answers { get; set; }
        }
        /* END - QnA Maker Response Class */
    ```

3. 해당 기술 자료의 https://qnamaker.ai -> 내 기술 자료 -> 코드 보기로 이동합니다. 다음 정보를 가져옵니다.

    ![QnA Maker HTTP 요청](../media/qnamaker-tutorials-qna-luis/qnamaker-http-request.png)

4. 각 기술 자료에 대해 QnAMakerService 클래스를 인스턴스화합니다.
    ```
            // Instantiate the knowledge bases
            public QnAMakerService hrQnAService = new QnAMakerService("https://hrkb.azurewebsites.net", "<HR knowledge base id>", "<HR endpoint key>");
            public QnAMakerService payrollQnAService = new QnAMakerService("https://payrollkb.azurewebsites.net", "<Payroll knowledge base id>", "<Payroll endpoint key>");
            public QnAMakerService financeQnAService = new QnAMakerService("https://financekb.azurewebsites.net", "<Finance knowledge base id>", "<Finance endpoint key>");
    ```

5. 의도를 위해 해당 기술 자료를 호출합니다.
    ```
            // HR Intent
            [LuisIntent("HR")]
            public async Task CancelIntent(IDialogContext context, LuisResult result)
            {
                // Ask the HR knowledge base
                await context.PostAsync(hrQnAService.GetAnswer(result.Query));
            }
    
            // Payroll intent
            [LuisIntent("Payroll")]
            public async Task GreetingIntent(IDialogContext context, LuisResult result)
            {
                // Ask the payroll knowledge base
                await context.PostAsync(payrollQnAService.GetAnswer(result.Query));
            }
    
            // Finance intent
            [LuisIntent("Finance")]
            public async Task HelpIntent(IDialogContext context, LuisResult result)
            {
                // Ask the finance knowledge base
                await context.PostAsync(financeQnAService.GetAnswer(result.Query));
            }
    ```

## <a name="build-the-bot"></a>봇 빌드
1. 코드 편집기에서 `build.cmd`를 마우스 오른쪽 단추로 클릭하고 **콘솔에서 실행**을 선택합니다.

    ![콘솔에서 실행](../media/qnamaker-tutorials-qna-luis/run-from-console.png)

2. 코드 보기는 빌드의 진행 상황 및 결과를 표시하는 터미널 창으로 바뀝니다.

    ![콘솔 빌드](../media/qnamaker-tutorials-qna-luis/console-build.png)

## <a name="test-the-bot"></a>봇 테스트
Azure Portal에서 **웹 채팅에서 테스트**를 선택하여 봇을 테스트합니다. 다양한 의도의 메시지를 입력하여 해당하는 기술 자료의 응답을 가져옵니다.

![웹 채팅 테스트](../media/qnamaker-tutorials-qna-luis/qnamaker-web-chat.png)

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [QnA Maker를 위한 비즈니스 연속성 플랜 만들기](../How-To/business-continuity-plan.md)
