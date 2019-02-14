---
title: 기술 자료 개선 - QnA Maker
titleSuffix: Azure Cognitive Services
description: ''
author: diberry
manager: cgronlun
displayName: active learning, suggestion, dialog prompt, train api, feedback loop, autolearn, auto-learn, user setting, service setting, services setting
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 01/29/2019
ms.author: diberry
ms.openlocfilehash: 639b665926f54387dfdc6e837c15c8d6d28df925
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/06/2019
ms.locfileid: "55755767"
---
# <a name="use-active-learning-to-improve-knowledge-base"></a>활성 학습을 사용하여 기술 자료 개선

활성 학습을 사용하면 사용자가 제출한 정보에 따라 질문과 대답 쌍에 대체 질문을 제안하여 기술 자료 품질을 개선할 수 있습니다. 이 제안을 검토한 다음 기존 질문에 추가하거나 거부합니다. 

기술 자료가 자동으로 변경되지는 않습니다. 즉, 변경 내용을 적용하려면 제안을 수락해야 합니다. 해당 제안과 질문을 수락해도 기존 질문이 변경되거나 제거되지는 않습니다.

## <a name="active-learning"></a>능동적 학습

QnA Maker는 암시적/명시적 피드백을 사용하여 새로운 질문 변형을 학습합니다.
 
* 암시적 피드백 – 사용자 질문에 점수가 거의 같은 대답이 여러 개 있으면 순위매기기 기능은 해당 대답을 파악하여 피드백으로 간주합니다. 
* 명시적 피드백 – 점수가 약간씩 다른 여러 대답이 기술 자료에서 반환되면 클라이언트 애플리케이션은 올바른 질문을 사용자에게 질문합니다. 이 경우 사용자의 명시적 피드백이 학습 API를 통해 QnA Maker로 전송됩니다. 

둘 중 어떤 방법을 사용하든 순위매기기 기능에 클러스터된 유사 쿼리가 제공됩니다.

유사 쿼리가 클러스터되어 있으면 QnA Maker는 수락하거나 거부할 수 있는 사용자 기반 질문을 기술 자료 디자이너에게 제공합니다.

## <a name="how-active-learning-works"></a>활성 학습의 작동 방식

활성 학습은 지정된 쿼리에 대해 QnA Maker에서 반환하는 몇 가지 상위 대답 점수를 기준으로 트리거됩니다. 점수에 큰 차이가 없으면 쿼리는 가능한 각 대답의 가능한 _제안_으로 간주됩니다. 

모든 제안은 유사성을 기준으로 클러스터되며, 최종 사용자가 특정 쿼리를 전송한 빈도에 따라 대체 질문의 상위 제안이 표시됩니다. 엔드포인트가 적절한 수와 종류의 사용량 쿼리를 수신하면 활성 학습에서는 가능한 최적의 제안을 제공합니다.

## <a name="upgrade-version-to-use-active-learning"></a>활성 학습 사용을 위한 버전 업그레이드

활성 학습은 런타임 버전 4.4.0 이상에서 지원됩니다. 기술 자료가 이전 버전에서 작성된 경우 이 기능을 사용하려면 [런타임을 업그레이드](troubleshooting-runtime.md#how-to-get-latest-qnamaker-runtime-updates)합니다. 

## <a name="best-practices"></a>모범 사례

활성 학습 사용 시의 모범 사례는 [모범 사례](../Concepts/best-practices.md#active-learning)를 참조하세요.

## <a name="score-proximity-between-knowledge-base-questions"></a>기술 자료 질문 간의 점수 범위

질문의 점수 신뢰도가 80% 등으로 높은 경우 활성 학습용으로 간주되는 점수의 범위도 넓어집니다(대략 10% 이내). 반면 신뢰도 점수가 40% 등으로 낮아지면 점수 범위도 좁아집니다(대략 4% 이내). 

점수 범위를 결정하는 알고리즘은 단순한 계산이 아닙니다. 앞에서 언급한 예의 범위는 고정된 범위가 아니며, 알고리즘의 영향을 파악하기 위한 참조 범위로만 사용해야 합니다.

## <a name="turn-on-active-learning"></a>활성 학습 설정

활성 학습은 기본적으로 해제되어 있습니다. 제안된 질문을 확인하려면 활성 학습을 설정합니다. 

1. 활성 학습을 설정하려면 자신의 **이름**을 클릭하고, QnA Maker 포털에서 오른쪽 위 모서리에 있는 [**서비스 설정**](https://www.qnamaker.ai/UserSettings)으로 이동합니다.  

    ![서비스 설정 페이지에서 활성 학습을 설정으로 전환](../media/improve-knowledge-base/Endpoint-Keys.png)


1. QnA Maker 서비스를 찾은 다음 **활성 학습**을 설정 상태로 전환합니다. 

    [![서비스 설정 페이지에서 활성 학습을 설정으로 전환](../media/improve-knowledge-base/turn-active-learning-on-at-service-setting.png)](../media/improve-knowledge-base/turn-active-learning-on-at-service-setting.png#lightbox)

    **활성 학습**이 사용하도록 설정되면 사용자가 제출한 질문에 따라 기술 자료에서 일정한 간격으로 새 질문을 제안합니다. 설정을 다시 전환하여 **활성 학습**을 사용하지 않도록 설정할 수 있습니다.

## <a name="add-active-learning-suggestion-to-knowledge-base"></a>기술 자료에 활성 학습 제안 추가

1. 제안된 질문을 확인하려면 **편집** 기술 자료 페이지에서 **제안 표시**를 선택합니다. 

    [![서비스 설정 페이지에서 활성 학습 단추 전환](../media/improve-knowledge-base/show-suggestions-button.png)](../media/improve-knowledge-base/show-suggestions-button.png#lightbox)

1. **제안 기준 필터링**을 선택하여 제안만 표시되도록 질문과 대답 쌍이 포함된 기술 자료를 필터링합니다.

    [![서비스 설정 페이지에서 질문/대답 쌍만 표시되도록 제안을 기준으로 필터링](../media/improve-knowledge-base/filter-by-suggestions.png)](../media/improve-knowledge-base/filter-by-suggestions.png#lightbox)

1.  제안이 포함된 각 질문 섹션에 새 질문이 표시되고 질문을 수락할 수 있는 확인 표시(`✔`)와 제안을 거부할 수 있는 `x`가 함께 표시됩니다. 질문을 추가하려면 확인 표시를 선택합니다. 

    [![서비스 설정 페이지에서 활성 학습을 설정으로 전환](../media/improve-knowledge-base/accept-active-learning-suggestions.png)](../media/improve-knowledge-base/accept-active-learning-suggestions.png#lightbox)

    **모두 추가** 또는 **모두 거부**를 선택하여 _모든 제안_을 추가하거나 삭제할 수 있습니다.

1. **저장 및 학습**을 선택하여 기술 자료에 변경 내용을 저장합니다.


## <a name="determine-best-choice-when-several-questions-have-similar-scores"></a>여러 질문의 점수가 비슷할 때 가장 적절한 선택 항목 결정

여러 질문의 점수에 거의 차이가 없으면 클라이언트 애플리케이션 개발자는 가장 적절한 질문 확인을 요청할 수 있습니다.

### <a name="use-the-top-property-in-the-generateanswer-request"></a>GenerateAnswer 요청에서 top 속성 사용

대답을 확인하기 위해 QnA Maker에 질문을 제출할 때는 JSON 본문의 특정 부분에서 상위 대답이 여러 개 반환되도록 코드를 작성할 수 있습니다.

```json
{
    "question": "wi-fi",
    "isTest": false,
    "top": 3
}
```

챗봇 등의 클라이언트 애플리케이션이 응답을 수신하면 상위 3개 질문이 반환됩니다.

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

### <a name="client-application-follow-up-when-questions-have-similar-scores"></a>질문의 점수가 비슷할 때의 클라이언트 애플리케이션 추가 작업

클라이언트 애플리케이션은 사용자가 의도를 가장 적절하게 반영하는 질문을 선택할 수 있는 옵션과 함께 모든 질문을 표시합니다. 

사용자가 기존 질문 중 하나를 선택하면 QnA Maker의 [학습](http://www.aka.ms/activelearningsamplebot) API에 사용자 피드백이 전송되어 활성 학습 피드백 루프가 계속 진행됩니다. 

```http
POST https://<QnA-Maker-resource-name>.azurewebsites.net/qnamaker/knowledgebases/<knowledge-base-ID>/train
Authorization: EndpointKey <endpoint-key>
Content-Type: application/json
{"feedbackRecords": [{"userId": "1","userQuestion": "<question-text>","qnaId": 1}]}
```

[Azure Bot C# 예제](https://github.com/Microsoft/BotBuilder-Samples/tree/master/experimental/csharp_dotnetcore/qnamaker-activelearning-bot)를 통해 활성 학습을 사용하는 방법을 자세히 알아보세요.

## <a name="next-steps"></a>다음 단계
 
> [!div class="nextstepaction"]
> [QnA Maker API 사용](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff)
