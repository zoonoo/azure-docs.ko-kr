---
title: 기술 자료 개선 - QnA Maker
description: 적극적인 학습을 통해 지식 기반의 품질을 향상시킵니다. 기존 질문을 제거하거나 변경하지 않고 검토, 수락 또는 거부, 추가합니다.
ms.topic: conceptual
ms.date: 04/06/2020
ms.openlocfilehash: 7fafc23eaf21099ebb974da226d07c351fa19699
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80756774"
---
# <a name="accept-active-learning-suggested-questions-in-the-knowledge-base"></a>기술 자료에서 적극적인 학습 제안 질문 수락


<a name="accept-an-active-learning-suggestion-in-the-knowledge-base"></a>

활성 학습은 제안을 승인한 후 기술 자료 또는 검색 서비스를 변경한 다음 저장하고 학습합니다. 제안을 승인하면 다른 질문으로 추가됩니다.

## <a name="turn-on-active-learning"></a>활성 학습 설정

제안된 질문을 보려면 QnA Maker 리소스에 대한 [활성 학습을 켜야](use-active-learning.md) 합니다.

## <a name="view-suggested-questions"></a>제안된 질문 보기

1. 제안된 질문을 보려면 기술 자료 **편집** 페이지에서 **보기 옵션을**선택한 다음 활성 학습 **제안 표시를 선택합니다.**

    [![포털의 편집 섹션에서 활성 학습의 새 질문 대안을 보려면 제안 표시를 선택합니다.](../media/improve-knowledge-base/show-suggestions-button.png)](../media/improve-knowledge-base/show-suggestions-button.png#lightbox)

1. 질문 및 답변 쌍으로 기술 자료 필터링하여 **제안별 필터를**선택하여 제안만 표시합니다.

    [![제안별 필터를 사용하여 활성 학습의 제안된 질문 대안만 보려면 토글을 사용합니다.](../media/improve-knowledge-base/filter-by-suggestions.png)](../media/improve-knowledge-base/filter-by-suggestions.png#lightbox)

1. 각 QnA 쌍은 체크 표시가있는 `✔` 새로운 질문 대안을 제안합니다. `x` 질문을 추가하려면 확인 표시를 선택합니다.

    [![녹색 확인 표시 또는 빨간색 삭제 표시를 선택하여 활성 학습의 제안된 질문 대안을 선택하거나 거부합니다.](../media/improve-knowledge-base/accept-active-learning-suggestions-small.png)](../media/improve-knowledge-base/accept-active-learning-suggestions.png#lightbox)

    컨텍스트 도구 모음에서 **모두 추가** 또는 **모두 거부를** 선택하여 _모든 제안을_ 추가하거나 삭제할 수 있습니다.

1. **저장 및 학습**을 선택하여 기술 자료에 변경 내용을 저장합니다.

1. [생성응답 API에서](metadata-generateanswer-usage.md#generateanswer-request-configuration)변경 내용을 사용할 수 있도록 **하려면 게시를** 선택합니다.

    5개 이상의 유사한 쿼리가 클러스터화되면 30분마다 QnA Maker에서 수락하거나 거부할 수 있는 대체 질문을 제안합니다.


<a name="#score-proximity-between-knowledge-base-questions"></a>

### <a name="architectural-flow-for-using-generateanswer-and-train-apis-from-a-bot"></a>봇에서 생성응답 및 학습 API를 사용하기 위한 아키텍처 흐름

봇 또는 기타 클라이언트 응용 프로그램은 활성 학습을 사용하려면 다음 아키텍처 흐름을 사용해야 합니다.

* Bot은 생성Answer API를 사용하여 [기술 자료로부터 답변을 얻고](#use-the-top-property-in-the-generateanswer-request-to-get-several-matching-answers) `top` 속성을 사용하여 여러 가지 답변을 얻습니다.
* 봇은 명시적 피드백을 결정합니다.
    * 사용자 [지정 비즈니스 논리를](#use-the-score-property-along-with-business-logic-to-get-list-of-answers-to-show-user)사용하여 낮은 점수를 필터링합니다.
    * 봇 또는 클라이언트 응용 프로그램에서 사용자에게 가능한 답변 목록을 표시하고 사용자가 선택한 답변을 가져옵니다.
* 봇은 [기차 API를](#train-api) [사용하여 선택한 답변을 QnA Maker에 다시 보냅니다.](#bot-framework-sample-code)


### <a name="use-the-top-property-in-the-generateanswer-request-to-get-several-matching-answers"></a>생성Answer 요청에서 최상위 속성을 사용하여 일치하는 몇 가지 답변을 얻습니다.

답변을 위해 QnA Maker에 질문을 제출할 `top` 때 JSON 본문의 속성은 반환할 답변 수를 설정합니다.

```json
{
    "question": "wi-fi",
    "isTest": false,
    "top": 3
}
```

### <a name="use-the-score-property-along-with-business-logic-to-get-list-of-answers-to-show-user"></a>비즈니스 논리와 함께 점수 속성을 사용하여 사용자에게 보여 줄 답변 목록을 가져옵니다.

클라이언트 응용 프로그램(예: 채팅 봇)이 응답을 받으면 상위 3개 질문이 반환됩니다. 속성을 `score` 사용하여 점수 간의 근접성을 분석합니다. 이 근접 범위는 사용자 고유의 비즈니스 논리에 의해 결정됩니다.

```json
{
    "answers": [
        {
            "questions": [
                "Wi-Fi Direct Status Indicator"
            ],
            "answer": "**Wi-Fi Direct Status Indicator**\n\nStatus bar icons indicate your current Wi-Fi Direct connection status:  \n\nWhen your device is connected to another device using Wi-Fi Direct, '$  \n\n+ *+ ' Wi-Fi Direct is displayed in the Status bar.",
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

클라이언트 응용 프로그램에는 사용자가 가장 의도를 나타내는 _단일 질문을_ 선택할 수 있는 옵션이 있는 질문이 표시됩니다.

사용자가 기존 질문 중 하나를 선택하면 클라이언트 응용 프로그램은 QnA Maker의 Train API를 사용하여 사용자의 선택을 피드백으로 보냅니다. 이 피드백은 활성 학습 피드백 루프를 완료합니다.

## <a name="train-api"></a>학습 API

활성 학습 피드백은 기차 API POST 요청과 함께 QnA 메이커로 전송됩니다. API 서명은 다음과 입니다.

```http
POST https://<QnA-Maker-resource-name>.azurewebsites.net/qnamaker/knowledgebases/<knowledge-base-ID>/train
Authorization: EndpointKey <endpoint-key>
Content-Type: application/json
{"feedbackRecords": [{"userId": "1","userQuestion": "<question-text>","qnaId": 1}]}
```

|HTTP 요청 속성|이름|형식|목적|
|--|--|--|--|
|URL 경로 매개 변수|기술 자료 ID|문자열|기술 자료를 위한 GUID입니다.|
|사용자 지정 하위 도메인|QnAMaker 리소스 이름|문자열|리소스 이름은 QnA Maker의 사용자 지정 하위 도메인으로 사용됩니다. 이 정보는 기술 보고서를 게시한 후 설정 페이지에서 사용할 수 있습니다. 로 `host`나열됩니다.|
|헤더|콘텐츠 형식|문자열|API로 전송되는 본문의 미디어 유형입니다. 기본값은 다음과 입니다.`application/json`|
|헤더|권한 부여|문자열|엔드포인트 키(EndpointKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx)입니다.|
|포스트 바디|JSON 개체|JSON|교육 피드백|

JSON 본체에는 다음과 같은 몇 가지 설정이 있습니다.

|JSON 바디 속성|형식|목적|
|--|--|--|--|
|`feedbackRecords`|array|피드백 목록입니다.|
|`userId`|문자열|제안된 질문을 수락하는 사람의 사용자 ID입니다. 사용자 ID 형식은 사용자에게 달려 있습니다. 예를 들어 전자 메일 주소는 아키텍처에서 유효한 사용자 ID일 수 있습니다. (선택 사항)|
|`userQuestion`|문자열|사용자 쿼리의 정확한 텍스트입니다. 필수 사항입니다.|
|`qnaID`|number|[생성응답 응답에](metadata-generateanswer-usage.md#generateanswer-response-properties)있는 질문 ID. |

JSON 본문이 다음과 같은 예입니다.

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

성공적인 응답은 204의 상태를 반환하고 JSON 응답 본문이 없습니다.

### <a name="batch-many-feedback-records-into-a-single-call"></a>많은 피드백 레코드를 단일 호출로 일괄 처리

봇과 같은 클라이언트 쪽 응용 프로그램에서 데이터를 저장한 다음 배열의 단일 JSON 본문에 `feedbackRecords` 많은 레코드를 보낼 수 있습니다.

JSON 본문이 다음과 같은 예입니다.

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

## <a name="bot-framework-sample-code"></a>봇 프레임워크 샘플 코드

사용자의 쿼리를 활성 학습에 사용해야 하는 경우 봇 프레임워크 코드는 Train API를 호출해야 합니다. 작성할 코드에는 두 가지가 있습니다.

* 활성 학습에 쿼리를 사용해야 하는지 확인
* 활성 학습을 위해 QnA Maker의 Train API로 쿼리 다시 보내기

Azure [Bot 샘플에서](https://aka.ms/activelearningsamplebot)이러한 두 활동이 프로그래밍되었습니다.

### <a name="example-c-code-for-train-api-with-bot-framework-4x"></a>봇 프레임워크 4.x를 사용하면 TRAIN API에 대한 예제 C# 코드

다음 코드는 Train API를 사용하여 QnA Maker에 정보를 다시 보내는 방법을 보여 줍니다.

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
/// <param name="endpoint">Endpoint URI of the runtime</param>
/// <param name="FeedbackRecords">Feedback records train API</param>
/// <param name="kbId">Knowledgebase Id</param>
/// <param name="key">Endpoint key</param>
/// <param name="cancellationToken"> Cancellation token</param>
public async static void CallTrain(string endpoint, FeedbackRecords feedbackRecords, string kbId, string key, CancellationToken cancellationToken)
{
    var uri = endpoint + "/knowledgebases/" + kbId + "/train/";

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

### <a name="example-nodejs-code-for-train-api-with-bot-framework-4x"></a>봇 프레임워크 4.x를 사용하면 TRAIN API에 대한 Node.js 코드 예제

다음 코드는 Train API를 사용하여 QnA Maker에 정보를 다시 보내는 방법을 보여 줍니다.

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

## <a name="active-learning-is-saved-in-the-exported-knowledge-base"></a>활성 학습은 내보낸 기술 자료에 저장됩니다.

앱에 활성 학습이 활성화되어 있고 앱을 `SuggestedQuestions` 내보내면 tsv 파일의 열은 활성 학습 데이터를 유지합니다.

열은 `SuggestedQuestions` 암시적, `autosuggested`명시적 피드백의 `usersuggested` 정보의 JSON 개체입니다. 사용자가 제출한 단일 질문에 대한 이 JSON `help` 개체의 예는 다음과 입니다.

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

또한 다운로드 변경 API를 사용하여 REST 또는 언어 기반 SDK를 사용하여 이러한 변경 사항을 검토할 수도 있습니다.
* [REST API](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75fc)
* [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.alterationsextensions.getasync?view=azure-dotnet)


이 응용 프로그램을 다시 가져올 때, 활성 학습은 정보를 수집하고 기술 자료에 대한 제안을 추천 계속.



## <a name="best-practices"></a>모범 사례

활성 학습 사용 시의 모범 사례는 [모범 사례](../Concepts/best-practices.md#active-learning)를 참조하세요.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [GenerateAnswer API로 메타데이터 사용](metadata-generateanswer-usage.md)
