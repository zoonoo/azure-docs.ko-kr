---
title: 기술 자료 개선 - QnA Maker
titleSuffix: Azure Cognitive Services
description: 활성 학습을 사용하면 사용자가 제출한 정보에 따라 질문과 대답 쌍에 대체 질문을 제안하여 기술 자료 품질을 개선할 수 있습니다. 이 제안을 검토한 다음 기존 질문에 추가하거나 거부합니다. 기술 자료가 자동으로 변경되지는 않습니다. 모든 변경 내용이 적용 되려면 제안을 동의 해야 합니다. 해당 제안과 질문을 수락해도 기존 질문이 변경되거나 제거되지는 않습니다.
author: diberry
manager: nitinme
services: cognitive-services
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 06/19/2019
ms.author: diberry
ms.openlocfilehash: b73884e544ea1b8ee76c8a891048e6a8e17d6ab3
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67204088"
---
# <a name="use-active-learning-to-improve-your-knowledge-base"></a>활성 학습을 사용 하 여 기술 자료를 개선 하기 위해

활성 학습을 사용하면 사용자가 제출한 정보에 따라 질문과 대답 쌍에 대체 질문을 제안하여 기술 자료 품질을 개선할 수 있습니다. 이 제안을 검토한 다음 기존 질문에 추가하거나 거부합니다. 

기술 자료가 자동으로 변경되지는 않습니다. 모든 변경 내용이 적용 되려면, 제안 사항에 동의 해야 합니다. 해당 제안과 질문을 수락해도 기존 질문이 변경되거나 제거되지는 않습니다.

## <a name="what-is-active-learning"></a>활성 학습 이란?

QnA Maker는 암시적/명시적 피드백을 사용하여 새로운 질문 변형을 학습합니다.
 
* [암시적 피드백](#how-qna-makers-implicit-feedback-works) – 순위는 경우 사용자 질문에 근접 하 게 연결 된 점수를 사용 하 여 여러 개의 답변을 이해 하 고 피드백으로 것으로 간주 합니다. 그러려면에 아무 작업도 수행할 필요가 없습니다.
* [명시적 피드백](#how-you-give-explicit-feedback-with-the-train-api) – 점수가 약간 변형을 사용 하 여 여러 개의 답변 기술 자료에서 반환 되는 경우 클라이언트 응용 프로그램 사용자는 질문은 올바른 질문을 요청 합니다. 사용 하 여 QnA Maker로 사용자의 명시적 피드백을 전송 합니다 [학습 API](#train-api)합니다. 

두 방법 모두 클러스터링 되는 비슷한 쿼리를 사용 하 여 우선 순위를 제공 합니다.

## <a name="how-active-learning-works"></a>활성 학습의 작동 방식

활성 학습 QnA Maker에서 반환 하는 상위 몇 가지 응답의 점수를 기반으로 트리거됩니다. 작은 범위를 범위 사이 점수 매기기 차이 쿼리 가능한 QnA 쌍 각각에 대 한 대체는 질문) (으로 가능한 제안을 간주 됩니다. 특정 QnA 쌍에 대 한 제안 된 질문 동의 하면 다른 쌍에 대 한 거부 됩니다. 저장 하 고 제안을 수락 하면 학습 한다는 점을 기억해 야 합니다.

엔드포인트가 적절한 수와 종류의 사용량 쿼리를 수신하면 활성 학습에서는 가능한 최적의 제안을 제공합니다. 5 개 또는 더 유사한 쿼리는 클러스터 된 경우 30 분 마다 QnA Maker 제안 허용 하거나 거부 하 여 기술 자료 디자이너로 사용자 기반 질문 합니다. 모든 제안은 유사성을 기준으로 클러스터되며, 최종 사용자가 특정 쿼리를 전송한 빈도에 따라 대체 질문의 상위 제안이 표시됩니다.

질문 QnA Maker 포털에서 제안 되 면 검토 하 고 허용 하거나 이러한 제안 취소 해야 합니다. 제안 관리 API는 없습니다.

## <a name="how-qna-makers-implicit-feedback-works"></a>QnA Maker의 암시적 피드백의 작동 원리

QnA Maker의 암시적 피드백 근접 점수를 확인 하 고 제안을 활성 학습 알고리즘을 사용 합니다. 점수 범위를 결정하는 알고리즘은 단순한 계산이 아닙니다. 다음 예제에서는 범위 수정 하는 것은 아니기 하지만 알고리즘에 영향을 이해 하려면 지침으로 사용 해야 합니다.

질문의 점수 신뢰도가 80% 등으로 높은 경우 활성 학습용으로 간주되는 점수의 범위도 넓어집니다(대략 10% 이내). 반면 신뢰도 점수가 40% 등으로 낮아지면 점수 범위도 좁아집니다(대략 4% 이내). 

## <a name="how-you-give-explicit-feedback-with-the-train-api"></a>학습 API를 사용 하 여 명시적 피드백을 제공 하는 방법

QnA Maker 명시적 피드백 응답에 대 한가 가장 적합 한 답변을 반드시 합니다. 정답을 결정 하는 방법을 사용자의 몫 이며 포함 될 수 있습니다.

* 사용자 의견을 대 한 답변 중 하나를 선택 합니다.
* 허용 가능한 결정 하는 등의 비즈니스 논리를 점수 범위입니다.  
* 두 사용자 의견 및 비즈니스 논리의 조합입니다.

## <a name="upgrade-your-runtime-version-to-use-active-learning"></a>활성 학습을 사용 하 여 런타임 버전 업그레이드

활성 학습은 런타임 버전 4.4.0 이상에서 지원됩니다. 기술 자료가 이전 버전에서 작성된 경우 이 기능을 사용하려면 [런타임을 업그레이드](troubleshooting-runtime.md#how-to-get-latest-qnamaker-runtime-updates)합니다. 

## <a name="turn-on-active-learning-to-see-suggestions"></a>제안 사항을 보려면 활성 학습 켜기

활성 학습은 기본적으로 해제되어 있습니다. 제안된 질문을 확인하려면 활성 학습을 설정합니다. 활성 학습을 설정한 후 QnA Maker에 클라이언트 앱에서 정보를 전송 해야 합니다. 자세한 내용은 [GenerateAnswer 및 학습 Api를 사용 하 여 봇의에 대 한 아키텍처 흐름](#architectural-flow-for-using-generateanswer-and-train-apis-from-a-bot)합니다.

1. 선택 **게시** 기술 자료를 게시 합니다. 활성 학습 쿼리 GenerateAnswer API 예측 끝점에서 수집 됩니다. QnA Maker 포털에서 테스트 창에 대 한 쿼리로 활성 학습을 영향을 주지 않습니다.

1. 현재 QnA Maker 포털에서의 학습을 설정 하려면를 오른쪽 위 모서리로 이동 하 **이름을**로 이동 하세요 [ **서비스 설정**](https://www.qnamaker.ai/UserSettings)합니다.  

    ![서비스 설정 페이지에서 활성 학습의 제안 된 질문 대안을 켭니다. 사용자 이름 오른쪽 위 메뉴에서 선택한 서비스 설정을 선택 합니다.](../media/improve-knowledge-base/Endpoint-Keys.png)


1. QnA Maker 서비스를 찾은 다음 **활성 학습**을 설정 상태로 전환합니다. 

    [![서비스 설정 페이지에서 활성 학습 기능을 설정/해제 합니다. 기능 설정/해제할 수 없는 경우 서비스를 업그레이드 해야 합니다.](../media/improve-knowledge-base/turn-active-learning-on-at-service-setting.png)](../media/improve-knowledge-base/turn-active-learning-on-at-service-setting.png#lightbox)

    한 번 **활성 학습** 가 사용 하도록 설정 하 여 기술 제안 새로운 질문 정기적으로 사용자가 제출한 질문을 바탕으로 합니다. 설정을 다시 전환하여 **활성 학습**을 사용하지 않도록 설정할 수 있습니다.

## <a name="accept-an-active-learning-suggestion-in-the-knowledge-base"></a>기술 자료에는 활성 학습 제안 허용

1. 제안 된 질문을 확인 하기 위해 합니다 **편집** 기술 자료 페이지에서 **보기 옵션**을 선택한 후 **활성 학습 제안 표시**합니다. 

    [![포털의 편집 섹션에서 활성 학습의 새로운 질문 대안을 확인 하기 위해 제안 표시를 선택 합니다.](../media/improve-knowledge-base/show-suggestions-button.png)](../media/improve-knowledge-base/show-suggestions-button.png#lightbox)

1. 질문 및 답변 쌍을 선택 하 여 제안만을 표시 하 고 기술 자료를 필터링 **제안 필터링**합니다.

    [![활성 학습의 제안 된 질문 대안만 보려면 제안 설정/해제 하 여 필터를 사용 합니다.](../media/improve-knowledge-base/filter-by-suggestions.png)](../media/improve-knowledge-base/filter-by-suggestions.png#lightbox)

1. 확인 표시를 사용 하 여 새 질문 대안을 제안 하는 각 QnA 쌍 `✔` , 질문을 허용 하도록 또는 `x` 제안을 거부 하도록 합니다. 질문을 추가하려면 확인 표시를 선택합니다. 

    [![선택 하거나 녹색 확인 표시 나 빨간색 삭제 표시를 선택 하 여 활성 학습의 제안 된 질문 대안을 거부 합니다.](../media/improve-knowledge-base/accept-active-learning-suggestions.png)](../media/improve-knowledge-base/accept-active-learning-suggestions.png#lightbox)

    추가 하거나 삭제할 수 있습니다 _의 모든 제안_ 를 선택 하 여 **모든 추가** 하거나 **모두 거부** 상황에 맞는 도구 모음에서.

1. **저장 및 학습**을 선택하여 기술 자료에 변경 내용을 저장합니다.

1. 선택 **게시** 에서 사용 가능 하도록 변경할 수 있도록 하는 [GenerateAnswer API](metadata-generateanswer-usage.md#generateanswer-request-configuration)합니다.

    5 개 또는 더 유사한 쿼리는 클러스터 된 경우 30 분 마다 QnA Maker 제안 수락 하거나 거부할 수에 대 한 대체 질문 합니다.


<a name="#score-proximity-between-knowledge-base-questions"></a>

### <a name="architectural-flow-for-using-generateanswer-and-train-apis-from-a-bot"></a>봇에서 GenerateAnswer 및 학습 Api를 사용 하기 위한 아키텍처 흐름

봇 또는 다른 클라이언트 응용 프로그램 활성 학습을 사용 하려면 다음과 같은 아키텍처 흐름을 사용 해야 합니다.

* 봇 [기술 자료에서 대답을 가져옵니다](#use-the-top-property-in-the-generateanswer-request-to-get-several-matching-answers) GenerateAnswer API를 사용 하 여 사용 하 여는 `top` 답변 수 속성입니다.
* 봇 명시적 피드백을 결정합니다.
    * 사용자 고유의 [사용자 지정 비즈니스 논리](#use-the-score-property-along-with-business-logic-to-get-list-of-answers-to-show-user), 낮은 점수를 필터링 합니다.
    * 봇 또는 클라이언트 응용 프로그램에서 사용자에 게 가능한 대답의 목록을 표시 하 고 선택한 사용자의 대답을 가져옵니다.
* 봇 [QnA Maker를 다시 선택한 응답을 보냅니다](#bot-framework-sample-code) 사용 하 여 합니다 [학습 API](#train-api)합니다.


### <a name="use-the-top-property-in-the-generateanswer-request-to-get-several-matching-answers"></a>일치 하는 여러 개의 답변을 얻으려면 GenerateAnswer 요청에 top 속성 사용

QnA Maker에 대 한 답변에 대 한 질문을 제출 하는 경우는 `top` JSON 본문의 속성 반환에 대 한 답변의 수를 설정 합니다. 

```json
{
    "question": "wi-fi",
    "isTest": false,
    "top": 3
}
```

### <a name="use-the-score-property-along-with-business-logic-to-get-list-of-answers-to-show-user"></a>비즈니스 논리와 함께 점수 속성을 사용 하 여 사용자 표시에 대 한 대답의 목록을 가져옵니다.

클라이언트 응용 프로그램 (예: 채팅 봇) 응답을 받으면 상위 3 개 질문 반환 됩니다. 사용 된 `score` 점수 간에 서로 근접 단어를 분석 하는 속성입니다. 이 근접 범위는 사용자 고유의 비즈니스 논리에 의해 결정 됩니다. 

```json
{
    "answers": [
        {
            "questions": [
                "Wi-Fi Direct Status Indicator"
            ],
            "answer": "**Wi-Fi Direct Status Indicator**\n\nStatus bar icons indicate your current Wi-Fi Direct connection status:  \n\nWhen your device is connected to another device using Wi-Fi Direct, '$  \n\n+ •+ ' Wi-Fi Direct is displayed in the Status bar.",
            "score": 74.21,
            "id": 607,
            "source": "Bugbash KB.pdf",
            "metadata": []
        },
        {
            "questions": [
                "Wi-Fi - Connections"
            ],
            "answer": "**Wi-Fi**\n\nWi-Fi is a term used for certain types of Wireless Local Area Networks (WLAN). Wi-Fi communication requires access to a wireless Access Point (AP).",
            "score": 74.15,
            "id": 599,
            "source": "Bugbash KB.pdf",
            "metadata": []
        },
        {
            "questions": [
                "Turn Wi-Fi On or Off"
            ],
            "answer": "**Turn Wi-Fi On or Off**\n\nTurning Wi-Fi on makes your device able to discover and connect to compatible in-range wireless APs.  \n\n1.  From a Home screen, tap ::: Apps > e Settings .\n2.  Tap Connections > Wi-Fi , and then tap On/Off to turn Wi-Fi on or off.",
            "score": 69.99,
            "id": 600,
            "source": "Bugbash KB.pdf",
            "metadata": []
        }
    ]
}
```

## <a name="client-application-follow-up-when-questions-have-similar-scores"></a>질문의 점수가 비슷할 때의 클라이언트 애플리케이션 추가 작업

사용자가 선택할 옵션을 사용 하 여 질문을 표시 하는 클라이언트 응용 프로그램 _단일 질문_ 대부분 해당 의도 나타냅니다. 

사용자가 기존 질문 중 하나를 선택 되 면 클라이언트 응용 프로그램 사용자가 선택한을 QnA Maker의 학습 API를 사용 하 여 피드백을 보냅니다. 이 피드백 완료 활성 피드백 루프를 학습 합니다. 

## <a name="train-api"></a>학습 API

활성 학습 피드백 QnA Maker를 학습 API POST 요청과 함께 전송 됩니다. API 서명은 다음과 같습니다.

```http
POST https://<QnA-Maker-resource-name>.azurewebsites.net/qnamaker/knowledgebases/<knowledge-base-ID>/train
Authorization: EndpointKey <endpoint-key>
Content-Type: application/json
{"feedbackRecords": [{"userId": "1","userQuestion": "<question-text>","qnaId": 1}]}
```

|HTTP 요청 속성|이름|Type|목적|
|--|--|--|--|
|URL 경로 매개 변수|기술 자료 ID|문자열|기술 자료를 위한 GUID입니다.|
|호스트 하위 도메인|QnAMaker 리소스 이름|문자열|Azure 구독에서 QnA Maker에 대 한 호스트 이름입니다. 기술 자료를 게시 한 후 설정 페이지에서 제공 됩니다. |
|헤더|Content-Type|문자열|API로 전송되는 본문의 미디어 유형입니다. 기본값은입니다. `application/json`|
|헤더|권한 부여|문자열|엔드포인트 키(EndpointKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx)입니다.|
|Post 본문|JSON 개체|JSON|교육 피드백|

JSON 본문을 몇 가지 설정은 다음과 같습니다.

|JSON 본문 속성|Type|목적|
|--|--|--|--|
|`feedbackRecords`|array|피드백의 목록입니다.|
|`userId`|문자열|제안 된 질문을 수락 하는 사용자의 사용자 ID입니다. 사용자 ID 형식은 사용자의 몫입니다. 예를 들어 전자 메일 주소에는 아키텍처에 올바른 사용자 ID를 수 있습니다. 선택 사항입니다.|
|`userQuestion`|문자열|정확한 쿼리 텍스트를 사용자의 합니다. 필수 사항입니다.|
|`qnaID`|number|질문에 있는 ID를 [GenerateAnswer 응답](metadata-generateanswer-usage.md#generateanswer-response-properties)합니다. |

예제 JSON 본문 다음과 같습니다.

```json
{
    "feedbackRecords": [
        {
            "userId": "1",
            "userQuestion": "<question-text>",
            "qnaId": 1
        }
    ]
}
```

응답에 성공 하면 204 및 JSON 응답 본문의 상태를 반환합니다. 

### <a name="batch-many-feedback-records-into-a-single-call"></a>단일 호출으로 여러 피드백 레코드를 일괄 처리

봇의 경우와 같은 클라이언트 쪽 응용 프로그램에서 데이터를 저장 후에 단일 JSON 본문에서 많은 레코드를 보낼 수 있습니다는 `feedbackRecords` 배열입니다. 

예제 JSON 본문 다음과 같습니다.

```json
{
    "feedbackRecords": [
        {
            "userId": "1",
            "userQuestion": "How do I ...",
            "qnaId": 1
        },
        {
            "userId": "2",
            "userQuestion": "Where is ...",
            "qnaId": 40
        },
        {
            "userId": "3",
            "userQuestion": "When do I ...",
            "qnaId": 33
        }
    ]
}
```



<a name="active-learning-is-saved-in-the-exported-apps-tsv-file"></a>

## <a name="bot-framework-sample-code"></a>Bot framework 샘플 코드

Bot framework 코드 활성 학습에 대 한 사용자의 쿼리를 사용 해야 하는 경우 학습 API를 호출 해야 합니다. 두 가지 방법으로 양의 코드를 작성 합니다.

* 활성 학습에 대 한 쿼리를 사용할지 여부를 확인 합니다.
* 쿼리를 활성 학습에 대 한 QnA Maker의 학습 API 다시 보내기

에 [Azure Bot 샘플](https://aka.ms/activelearningsamplebot), 두이 작업 모두 프로그래밍 된 합니다. 

### <a name="example-c-code-for-train-api-with-bot-framework-4x"></a>예제에서는 C# Bot Framework를 사용 하 여 학습 API에 대 한 코드 4.x

다음 코드에 정보를 학습 API를 사용 하 여 QnA Maker 보내는 방법을 보여 줍니다. 이렇게 [전체 코드 샘플](https://github.com/microsoft/BotBuilder-Samples/tree/master/experimental/qnamaker-activelearning/csharp_dotnetcore) GitHub에서 사용할 수 있습니다.

```csharp
public class FeedbackRecords
{
    // <summary>
    /// List of feedback records
    /// </summary>
    [JsonProperty("feedbackRecords")]
    public FeedbackRecord[] Records { get; set; }
}

/// <summary>
/// Active learning feedback record
/// </summary>
public class FeedbackRecord
{
    /// <summary>
    /// User id
    /// </summary>
    public string UserId { get; set; }

    /// <summary>
    /// User question
    /// </summary>
    public string UserQuestion { get; set; }

    /// <summary>
    /// QnA Id
    /// </summary>
    public int QnaId { get; set; }
}

/// <summary>
/// Method to call REST-based QnAMaker Train API for Active Learning
/// </summary>
/// <param name="host">Endpoint host of the runtime</param>
/// <param name="FeedbackRecords">Feedback records train API</param>
/// <param name="kbId">Knowledgebase Id</param>
/// <param name="key">Endpoint key</param>
/// <param name="cancellationToken"> Cancellation token</param>
public async static void CallTrain(string host, FeedbackRecords feedbackRecords, string kbId, string key, CancellationToken cancellationToken)
{
    var uri = host + "/knowledgebases/" + kbId + "/train/";

    using (var client = new HttpClient())
    {
        using (var request = new HttpRequestMessage())
        {
            request.Method = HttpMethod.Post;
            request.RequestUri = new Uri(uri);
            request.Content = new StringContent(JsonConvert.SerializeObject(feedbackRecords), Encoding.UTF8, "application/json");
            request.Headers.Add("Authorization", "EndpointKey " + key);

            var response = await client.SendAsync(request, cancellationToken);
            await response.Content.ReadAsStringAsync();
        }
    }
}
```

### <a name="example-nodejs-code-for-train-api-with-bot-framework-4x"></a>Bot Framework를 사용 하 여 학습 API에 대 한 예제 Node.js 코드 4.x 

다음 코드에 정보를 학습 API를 사용 하 여 QnA Maker 보내는 방법을 보여 줍니다. 이렇게 [전체 코드 샘플](https://github.com/microsoft/BotBuilder-Samples/blob/master/experimental/qnamaker-activelearning/javascript_nodejs) GitHub에서 사용할 수 있습니다.

```javascript
async callTrain(stepContext){

    var trainResponses = stepContext.values[this.qnaData];
    var currentQuery = stepContext.values[this.currentQuery];

    if(trainResponses.length > 1){
        var reply = stepContext.context.activity.text;
        var qnaResults = trainResponses.filter(r => r.questions[0] == reply);

        if(qnaResults.length > 0){

            stepContext.values[this.qnaData] = qnaResults;

            var feedbackRecords = {
                FeedbackRecords:[
                    {
                        UserId:stepContext.context.activity.id,
                        UserQuestion: currentQuery,
                        QnaId: qnaResults[0].id
                    }
                ]
            };

            // Call Active Learning Train API
            this.activeLearningHelper.callTrain(this.qnaMaker.endpoint.host, feedbackRecords, this.qnaMaker.endpoint.knowledgeBaseId, this.qnaMaker.endpoint.endpointKey);
            
            return await stepContext.next(qnaResults);
        }
        else{

            return await stepContext.endDialog();
        }
    }

    return await stepContext.next(stepContext.result);
}
```

## <a name="active-learning-is-saved-in-the-exported-knowledge-base"></a>활성 학습 내보낸된 기술 자료에 저장 됩니다.

앱이 활성 학습 사용 하도록 설정 하 고 앱을 내보낼 때의 `SuggestedQuestions` tsv 파일의 열은 활성 학습 데이터를 유지 합니다. 

`SuggestedQuestions` 열은 암시적 일의 정보는 JSON 개체로 `autosuggested`, 및 명시적 `usersuggested` 피드백. 이 JSON 개체의 단일 사용자가 제출한 질문에 대 한 예가 `help` 됩니다.

```JSON
[
    {
        "clusterHead": "help",
        "totalAutoSuggestedCount": 1,
        "totalUserSuggestedCount": 0,
        "alternateQuestionList": [
            {
                "question": "help",
                "autoSuggestedCount": 1,
                "userSuggestedCount": 0
            }
        ]
    }
]
```

이 앱을 다시 가져오는 경우 활성 학습 정보를 수집 하 고 기술 자료에 대 한 제안을 권장 계속 합니다. 

## <a name="best-practices"></a>모범 사례

활성 학습 사용 시의 모범 사례는 [모범 사례](../Concepts/best-practices.md#active-learning)를 참조하세요.

## <a name="next-steps"></a>다음 단계
 
> [!div class="nextstepaction"]
> [GenerateAnswer API를 사용 하 여 메타 데이터를 사용 합니다.](metadata-generateanswer-usage.md)
