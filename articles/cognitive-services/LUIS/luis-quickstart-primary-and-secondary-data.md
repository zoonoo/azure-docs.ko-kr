---
title: 데이터를 추출하는 LUIS 앱 만들기 자습서 - Azure | Microsoft Docs
description: 이 자습서에서는 의도를 사용하는 간단한 LUIS 앱 및 기계 학습 데이터를 추출하는 단순 엔터티를 만드는 방법을 알아봅니다.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: luis
ms.topic: tutorial
ms.date: 06/29/2018
ms.author: v-geberr
ms.openlocfilehash: a4bf63b7a2fbbb26b8c121f5360aea0a5ca8a687
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/10/2018
ms.locfileid: "37952388"
---
# <a name="tutorial-7-add-simple-entity-and-phrase-list"></a>자습서: 7. 단순 엔터티 및 문구 목록 추가
이 자습서에서는 **단순** 엔터티를 사용하여 발화에서 기계 학습 데이터를 추출하는 방법을 보여 주는 앱을 만듭니다.

<!-- green checkmark -->
> [!div class="checklist"]
> * 단순 엔터티 이해 
> * HR(인사 관리) 도메인에 대한 새 LUIS 앱 만들기 
> * 앱에서 직무를 추출하는 단순 엔터티 만들기
> * 앱 학습 및 게시
> * 앱의 엔드포인트를 쿼리하여 LUIS JSON 응답 확인
> * 구 목록을 추가하여 직무 단어 표시 확대
> * 학습, 앱 게시 및 엔드포인트 다시 쿼리

이 문서에서는 LUIS 앱을 작성하기 위해 체험 [LUIS](luis-reference-regions.md#luis-website) 계정이 필요합니다.

## <a name="before-you-begin"></a>시작하기 전에
[복합 엔터티](luis-tutorial-composite-entity.md) 자습서의 인사 관리 앱이 없으면 JSON을 [LUIS](luis-reference-regions.md#luis-website) 웹 사이트의 새 앱으로 [가져옵니다](luis-how-to-start-new-app.md#import-new-app). 가져올 앱은 [LUIS-Samples](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/custom-domain-composite-HumanResources.json) Github 리포지토리에 있습니다.

원래의 인사 관리 앱을 유지하려면 [설정](luis-how-to-manage-versions.md#clone-a-version) 페이지에서 버전을 복제하고 해당 이름을 `simple`로 지정합니다. 복제는 원래 버전에 영향을 주지 않고도 다양한 LUIS 기능을 사용할 수 있는 좋은 방법입니다.  

## <a name="purpose-of-the-app"></a>앱의 용도
이 앱은 발화에서 데이터를 가져오는 방법을 보여 줍니다. 챗봇에서 다음 발화를 살펴보겠습니다.

|발언|추출 가능한 직무 이름|
|:--|:--|
|새 회계 업무에 지원하려고 합니다.|회계|
|엔지니어링 직위에 대한 내 이력서를 제출해 주세요.|엔지니어링|
|123456 직무에 대한 지원서를 작성합니다.|123456|

이 자습서에서는 직무 이름을 추출하는 새 엔터티를 추가합니다. 

## <a name="purpose-of-the-simple-entity"></a>단순 엔터티의 목적
이 LUIS 앱의 단순 엔터티는 메시지가 무엇인지와 발화 중 어디에서 직무 이름을 찾을 수 있는지를 LUIS에 가르치기 위한 것입니다. 직무에 해당하는 발화 부분은 단어 선택과 발화 길이에 따라 발화 간에 바뀔 수 있습니다. LUIS에는 모든 의도에서 모든 발화의 직무 예제가 필요합니다.  

이름은 명사, 동사 또는 여러 단어로 구성된 구일 수 있으므로 직무 이름을 판별하기가 어렵습니다. 예: 

|교육|
|--|
|엔지니어|
|소프트웨어 엔지니어|
|수석 소프트웨어 엔지니어|
|엔지니어링 팀 리더 |
|항공 관제사|
|자동차 기사|
|구급차 운전 기사|
|간호사|
|압출기 기사|
|기계 수리 기능사|

이 LUIS 앱의 몇 가지 의도에는 직무 이름이 있습니다. LUIS는 의도의 모든 발화에서 이러한 단어를 레이블로 지정함으로써 직무가 무엇인지와 발화 중 어디에서 직무를 찾을 수 있는지에 대해 자세히 학습합니다.

## <a name="create-job-simple-entity"></a>직무 단순 엔터티 만들기

1. 인사 관리 앱이 LUIS의 **빌드** 섹션에 있는지 확인합니다. 오른쪽 위의 메뉴 표시줄에서 **빌드**를 선택하여 이 섹션으로 변경할 수 있습니다. 

    [ ![오른쪽 위의 탐색 모음에서 강조 표시된 빌드가 있는 LUIS 앱의 스크린샷](./media/luis-quickstart-primary-and-secondary-data/hr-first-image.png)](./media/luis-quickstart-primary-and-secondary-data/hr-first-image.png#lightbox)

2. **의도** 페이지에서 **ApplyForJob** 의도를 선택합니다. 

    [![](media/luis-quickstart-primary-and-secondary-data/hr-select-applyforjob.png "'ApplyForJob' 의도가 강조 표시된 LUIS의 스크린샷")](media/luis-quickstart-primary-and-secondary-data/hr-select-applyforjob.png#lightbox)

3. `I want to apply for the new accounting job` 발화에서 `accounting`을 선택하고, 팝업 메뉴의 위쪽 필드에서 `Job`을 입력한 다음, 팝업 메뉴에서 **새 엔터티 만들기**를 선택합니다. 

    [![](media/luis-quickstart-primary-and-secondary-data/hr-create-entity.png "엔터티 만들기 단계가 강조 표시된 LUIS의 'ApplyForJob' 의도 스크린샷")](media/luis-quickstart-primary-and-secondary-data/hr-create-entity.png#lightbox)

4. 팝업 창에서 엔터티 이름과 형식을 확인하고 **완료**를 선택합니다.

    ![직무의 이름과 단순 형식을 사용한 단순 엔터티 만들기 팝업 모달 대화 상자](media/luis-quickstart-primary-and-secondary-data/hr-create-simple-entity-popup.png)

5. `Submit resume for engineering position` 발화에서 `engineering`이라는 단어를 직무 엔터티의 레이블로 지정합니다. `engineering`이라는 단어를 선택한 다음, 팝업 메뉴에서 **직무**를 선택합니다. 

    [![](media/luis-quickstart-primary-and-secondary-data/hr-label-simple-entity.png "강조 표시된 LUIS 직무 엔터티 레이블 지정의 스크린샷")](media/luis-quickstart-primary-and-secondary-data/hr-label-simple-entity.png#lightbox)

    모든 발화에는 레이블이 지정되지만, 5개의 발화는 LUIS에서 직무 관련 단어와 구를 학습하는 데 충분하지 않습니다. 숫자 값을 사용하는 직무는 정규식 엔터티를 사용하므로 이러한 직무에는 예제가 많이 필요하지 않습니다. 그러나 단어 또는 구를 사용하는 직무에는 15개 이상의 예제가 필요합니다. 

6. 더 많은 발화를 추가하고 직무 단어 또는 구를 **직무** 엔터티로 표시합니다. 직무 유형은 고용 서비스의 직업에서 일반적입니다. 특정 산업과 관련된 직무를 원하는 경우 직무 단어에서 해당 산업을 반영해야 합니다. 

    |발언|직무 엔터티|
    |:--|:--|
    |연구 개발 부서의 프로그램 관리자 직에 지원하고 있습니다.|프로그램 관리자|
    |인턴 요리사 직에 대한 내 지원서는 여기에 있습니다.|인턴 요리사|
    |캠프 지도 교사 직에 대한 내 이력서가 첨부되어 있습니다.|캠프 지도 교사|
    |여기의 내 이력서는 전문 비서 직에 지원하기 위한 것입니다.|전문 비서|
    |판매 관리 업무에 지원하려고 합니다.|판매 관리 담당|
    |새 회계 업무 직에 대한 내 이력서입니다.|회계|
    |보조 바텐더 직에 대한 내 지원서가 첨부되어 있습니다.|보조 바텐더|
    |지붕 수리자 및 액자 세공사 직에 대한 신청서를 제출하고 있습니다.|지붕 수리자, 액자 세공사|
    |버스 운전 기사 직에 대한 내 이력서는 여기에 있습니다.|버스 운전 기사|
    |공인 간호사입니다. 내 이력서는 여기에 있습니다.|공인 간호사|
    |채용 관련 자료에서 확인한 강사 직에 필요한 서류를 제출하고 싶습니다.|교육 강사|
    |여기의 내 이력서는 과일 및 야채 창고 부서에 지원하기 위한 것입니다.|창고 담당|
    |타일 공사 직에 지원합니다.|타일|
    |조경사 직에 지원하기 위한 이력서가 첨부되었습니다.|조경사|
    |생물학 교수 직에 지원하기 위한 내 이력서가 동봉되어 있습니다.|생물학 교수|
    |사진 촬영 직에 지원하고 싶습니다.|사진 촬영 기사|git 

## <a name="label-entity-in-example-utterances-for-getjobinformation-intent"></a>GetJobInformation 의도에 대한 발화 예제의 엔터티에 레이블 지정
1. 왼쪽 메뉴에서 **의도**를 선택합니다.

2. 의도 목록에서 **GetJobInformation**을 선택합니다. 

3. 발화 예제의 직무에 대한 레이블을 지정합니다.

    |발언|직무 엔터티|
    |:--|:--|
    |데이터베이스에 어떤 직무가 있나요?|데이터베이스|
    |회계 직무와 관련된 새 상황을 찾고 있습니다.|회계|
    |수석 엔지니어에게 사용할 수 있는 직위는 무엇인가요?|수석 엔지니어|

    다른 발화 예제가 있지만 직무 단어는 포함되어 있지 않습니다.

## <a name="train-the-luis-app"></a>LUIS 앱 학습
LUIS는 학습될 때까지 의도와 엔터티(모델)에 대한 변경 내용을 인식하지 못합니다. 

1. LUIS 웹 사이트의 오른쪽 위에서 **학습** 단추를 선택합니다.

    ![학습 단추 선택](./media/luis-quickstart-primary-and-secondary-data/train-button.png)

2. 웹 사이트의 위쪽에 성공이 확인된 녹색 상태 표시줄이 표시되면 학습이 완료됩니다.

    ![학습 성공 알림](./media/luis-quickstart-primary-and-secondary-data/trained.png)

## <a name="publish-the-app-to-get-the-endpoint-url"></a>앱을 게시하여 엔드포인트 URL 가져오기
챗봇 또는 다른 응용 프로그램에서 LUIS 예측을 얻으려면 앱을 게시해야 합니다. 

1. LUIS 웹 사이트의 오른쪽 위에서 **게시** 단추를 선택합니다. 

2. 프로덕션 슬롯과 **게시** 단추를 선택합니다.

    [![](media/luis-quickstart-primary-and-secondary-data/publish-to-production.png "프로덕션 슬롯에 게시 단추가 강조 표시된 게시 페이지의 스크린샷")](media/luis-quickstart-primary-and-secondary-data/publish-to-production.png#lightbox)

3. 웹 사이트의 위쪽에 성공이 확인된 녹색 상태 표시줄이 표시되면 게시가 완료됩니다.

## <a name="query-the-endpoint-with-a-different-utterance"></a>다른 발화를 사용하여 엔드포인트 쿼리
**게시** 페이지의 아래쪽에서 **엔드포인트** 링크를 선택합니다. 

[![](media/luis-quickstart-primary-and-secondary-data/publish-select-endpoint.png "엔드포인트가 강조 표시된 게시 페이지의 스크린샷")](media/luis-quickstart-primary-and-secondary-data/publish-select-endpoint.png#lightbox)

그러면 주소 표시줄에 엔드포인트 URL이 있는 다른 브라우저 창이 열립니다. 주소의 URL 끝으로 이동하고 `Here is my c.v. for the programmer job`을 입력합니다. 마지막 쿼리 문자열 매개 변수는 발화 **쿼리**를 나타내는 `q`입니다. 이 발화는 레이블이 지정된 발화와 같지 않으므로 유용한 테스트이며, `ApplyForJob` 발화가 반환되어야 합니다.

```JSON
{
  "query": "Here is my c.v. for the programmer job",
  "topScoringIntent": {
    "intent": "ApplyForJob",
    "score": 0.9826467
  },
  "intents": [
    {
      "intent": "ApplyForJob",
      "score": 0.9826467
    },
    {
      "intent": "GetJobInformation",
      "score": 0.0218927357
    },
    {
      "intent": "MoveEmployee",
      "score": 0.007849265
    },
    {
      "intent": "Utilities.StartOver",
      "score": 0.00349470088
    },
    {
      "intent": "Utilities.Confirm",
      "score": 0.00348804821
    },
    {
      "intent": "None",
      "score": 0.00319909188
    },
    {
      "intent": "FindForm",
      "score": 0.00222647213
    },
    {
      "intent": "Utilities.Help",
      "score": 0.00211193133
    },
    {
      "intent": "Utilities.Stop",
      "score": 0.00172086991
    },
    {
      "intent": "Utilities.Cancel",
      "score": 0.00138010911
    }
  ],
  "entities": [
    {
      "entity": "programmer",
      "type": "Job",
      "startIndex": 24,
      "endIndex": 33,
      "score": 0.5230502
    }
  ]
}
```

## <a name="names-are-tricky"></a>처리하기 어려운 이름
LUIS 앱은 높은 신뢰도를 통해 올바른 의도를 찾고 직무 이름을 추출했지만 이름을 처리하는 데 어려움이 있습니다. `This is the lead welder paperwork` 발화를 사용해 보겠습니다.  

다음 JSON에서 LUIS는 올바른 `ApplyForJob` 의도를 사용하여 응답하지만 `lead welder` 직무 이름을 추출하지 못했습니다. 

```JSON
{
  "query": "This is the lead welder paperwork.",
  "topScoringIntent": {
    "intent": "ApplyForJob",
    "score": 0.468558252
  },
  "intents": [
    {
      "intent": "ApplyForJob",
      "score": 0.468558252
    },
    {
      "intent": "GetJobInformation",
      "score": 0.0102701457
    },
    {
      "intent": "MoveEmployee",
      "score": 0.009442534
    },
    {
      "intent": "Utilities.StartOver",
      "score": 0.00639619166
    },
    {
      "intent": "None",
      "score": 0.005859333
    },
    {
      "intent": "Utilities.Cancel",
      "score": 0.005087704
    },
    {
      "intent": "Utilities.Stop",
      "score": 0.00315379258
    },
    {
      "intent": "Utilities.Help",
      "score": 0.00259344373
    },
    {
      "intent": "FindForm",
      "score": 0.00193389168
    },
    {
      "intent": "Utilities.Confirm",
      "score": 0.000420796918
    }
  ],
  "entities": []
}
```

이름은 무엇이든 될 수 있으므로 표시를 확대할 수 있는 단어에 대한 구 목록이 있는 경우 LUIS는 엔터티를 더 정확하게 예측합니다.

## <a name="to-boost-signal-add-jobs-phrase-list"></a>표시를 확대하기 위한 직무 구 목록 추가
LUIS-Samples Github 리포지토리에서 [jobs-phrase-list.csv](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/job-phrase-list.csv)를 엽니다. 이 목록에는 1,000개가 넘는 직무 단어와 구가 있습니다. 이 목록에서 의미 있는 직무 단어를 찾습니다. 단어 또는 구가 목록에 없으면 직접 추가합니다.

1. LUIS 앱의 **빌드** 섹션에서 **앱 성능 향상** 메뉴 아래에 있는 **구 목록**을 선택합니다.

    [![](media/luis-quickstart-primary-and-secondary-data/hr-select-phrase-list-left-nav.png "구 목록의 왼쪽 탐색 단추가 강조 표시된 스크린샷")](media/luis-quickstart-primary-and-secondary-data/hr-select-phrase-list-left-nav.png#lightbox)

2. **새 구 목록 만들기**를 선택합니다. 

    [![](media/luis-quickstart-primary-and-secondary-data/hr-create-new-phrase-list.png "새 구 목록 만들기 단추가 강조 표시된 스크린샷")](media/luis-quickstart-primary-and-secondary-data/hr-create-new-phrase-list.png#lightbox)

3. 새 구 목록의 이름을 `Jobs`로 지정하고, **값** 텍스트 상자에 jobs-phrase-list.csv의 목록을 복사합니다. Enter 키를 선택합니다. 

    [![](media/luis-quickstart-primary-and-secondary-data/hr-create-phrase-list-1.png "새 구 목록 대화 상자 팝업의 스크린샷")](media/luis-quickstart-primary-and-secondary-data/hr-create-phrase-list-1.png#lightbox)

    구 목록에 더 많은 단어를 추가하려면 **관련 값**을 검토하고 관련된 내용을 추가합니다. 

4. **저장**을 선택하여 구 목록을 활성화합니다.

    [![](media/luis-quickstart-primary-and-secondary-data/hr-create-phrase-list-2.png "구 목록 값 상자에 단어가 포함된 새 구 목록 대화 상자 팝업의 스크린샷")](media/luis-quickstart-primary-and-secondary-data/hr-create-phrase-list-2.png#lightbox)

5. 구 목록을 사용할 수 있도록 앱을 다시 [학습](#train-the-luis-app) 및 [게시](#publish-the-app-to-get-the-endpoint-URL)합니다.

6. 동일한 `This is the lead welder paperwork.` 발화를 사용하여 엔드포인트에서 다시 쿼리합니다.

    추출된 엔터티가 JSON 응답에 포함됩니다.

    ```JSON
    {
        "query": "This is the lead welder paperwork.",
        "topScoringIntent": {
            "intent": "ApplyForJob",
            "score": 0.920025647
        },
        "intents": [
            {
            "intent": "ApplyForJob",
            "score": 0.920025647
            },
            {
            "intent": "GetJobInformation",
            "score": 0.003800706
            },
            {
            "intent": "Utilities.StartOver",
            "score": 0.00299335527
            },
            {
            "intent": "MoveEmployee",
            "score": 0.0027167045
            },
            {
            "intent": "None",
            "score": 0.00259556063
            },
            {
            "intent": "FindForm",
            "score": 0.00224019377
            },
            {
            "intent": "Utilities.Stop",
            "score": 0.00200693542
            },
            {
            "intent": "Utilities.Cancel",
            "score": 0.00195913855
            },
            {
            "intent": "Utilities.Help",
            "score": 0.00162656687
            },
            {
            "intent": "Utilities.Confirm",
            "score": 0.0002851904
            }
        ],
        "entities": [
            {
            "entity": "lead welder",
            "type": "Job",
            "startIndex": 12,
            "endIndex": 22,
            "score": 0.8295959
            }
        ]
    }
    ```

## <a name="phrase-lists"></a>문구 목록
구 목록을 추가하면 목록에 있는 단어를 표시하도록 확대되지만 정확한 일치로는 **사용되지 않습니다**. 구 목록에는 첫 번째 단어가 `lead`인 직무가 여러 개 있고 `welder`직무도 있지만, `lead welder` 직무는 없습니다. 직무에 대한 이 구 목록은 완전하지 않을 수 있습니다. 정기적으로 [엔드포인트 발화를 검토](luis-how-to-review-endoint-utt.md)하고 다른 직무 단어가 있으면 이러한 단어를 구 목록에 추가합니다. 그런 다음, 또 다시 학습하고 게시합니다.

## <a name="what-has-this-luis-app-accomplished"></a>이 LUIS 앱에서 수행한 작업은?
이 앱에서는 단순 엔터티와 단어의 구 목록을 사용하여 자연어 쿼리 의도를 확인하고 작업 데이터를 반환했습니다. 

챗봇에는 이제 직무가 적용되는 기본 작업과 해당 직무가 참조되는 해당 작업의 매개 변수를 결정하는 데 필요한 정보가 충분히 있습니다. 

## <a name="where-is-this-luis-data-used"></a>이 LUIS 데이터가 사용되는 위치는? 
LUIS는 이 요청을 통해 수행됩니다. 챗봇과 같은 호출 응용 프로그램에서는 topScoringIntent 결과와 엔터티의 데이터를 사용하여 타사 API를 통해 메시지를 보낼 수 있습니다. 봇 또는 호출 응용 프로그램에 대한 다른 프로그래밍 옵션이 있는 경우 LUIS는 이러한 작업을 수행하지 않습니다. LUIS는 사용자의 의도가 무엇인지만 결정합니다. 

## <a name="clean-up-resources"></a>리소스 정리
더 이상 필요하지 않은 경우 LUIS 앱을 삭제합니다. 왼쪽 위 메뉴에서 **내 앱**을 선택합니다. 앱 목록에서 앱 이름 오른쪽에 있는 줄임표(***...***)를 선택하고 **삭제**를 선택합니다. **앱을 삭제하시겠습니까?** 팝업 대화 상자에서 **확인**을 선택합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [미리 작성된 keyphrase 엔터티 추가](luis-quickstart-intent-and-key-phrase.md)