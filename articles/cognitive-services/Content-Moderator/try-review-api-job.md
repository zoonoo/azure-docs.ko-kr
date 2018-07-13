---
title: Azure Content Moderator에서 콘텐츠 조정 작업 실행 | Microsoft Docs
description: API 콘솔에서 콘텐츠 조정 작업을 실행하는 방법을 알아봅니다.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 08/03/2017
ms.author: sajagtap
ms.openlocfilehash: 6f741be1001ae70d5fdbf6f374204aaad1601abe
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35373015"
---
# <a name="start-a-moderation-job-from-the-api-console"></a>API 콘솔에서 조정 작업 시작

Review API의 [작업 운영](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c5)을 사용하여 Azure Content Moderator에서 이미지 또는 텍스트 콘텐츠에 대한 종합적인 콘텐츠 조정 작업을 시작할 수 있습니다. 

조정 작업은 Content Moderator Image Moderation API 또는 Text Moderation API를 사용하여 콘텐츠를 검색합니다. 그러면 조정 작업은 워크플로(검토 도구에 정의된)를 사용하여 검토 도구에서 검토를 생성합니다. 

조정자 역할을 맡은 사람이 자동 할당된 태그 및 예측 데이터를 검토하고 최종 조정 결정을 제출하면 Review API가 모든 정보를 API 엔드포인트에 제출합니다.

## <a name="prerequisites"></a>필수 조건

[검토 도구](https://contentmoderator.cognitive.microsoft.com/)로 이동합니다. 아직 등록하지 않은 경우 지금 등록합니다. 검토 도구 내에서, 이 `Job` 작업에 사용할 [사용자 지정 워크플로를 정의](Review-Tool-User-Guide/Workflows.md)합니다.

## <a name="use-the-api-console"></a>API 콘솔 사용
온라인 콘솔을 사용하여 API를 테스트하려면 콘솔에 입력할 몇 가지 값이 필요합니다.
    
- `teamName`: 검토 도구의 자격 증명 화면에서 `Id` 필드를 사용합니다. 
- `ContentId`: 이 문자열은 API로 전달되고 콜백을 통해 반환됩니다. **ContentId**는 내부 식별자 또는 메타데이터를 조정 작업의 결과와 연결하는 데 유용합니다. `Workflowname`: 이전 섹션에서 [만든 워크플로](Review-Tool-User-Guide/Workflows.md)의 이름입니다.
- `Ocp-Apim-Subscription-Key`: **설정** 탭에 있습니다. 자세한 내용은 [개요](overview.md)를 참조하세요.

API 콘솔 액세스는 **자격 증명** 창에서 수행합니다.

### <a name="navigate-to-the-api-reference"></a>API 참조로 이동
**자격 증명** 창에서 [API 참조](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c5)를 선택합니다.

  `Job.Create` 페이지가 열립니다.

### <a name="select-your-region"></a>지역 선택
**개방형 API 테스트 콘솔**의 경우 사용자 위치를 가장 근접하게 설명하는 지역을 선택합니다.
  ![작업 - 페이지 지역 선택 만들기](images/test-drive-job-1.png)

  `Job.Create` API 콘솔이 열립니다. 

### <a name="enter-parameters"></a>매개 변수 입력

필수 쿼리 매개 변수의 값과 구독 키를 입력합니다. **요청 본문** 상자에서 검색할 정보의 위치를 지정합니다. 이 예제에서는 [샘플 이미지](https://moderatorsampleimages.blob.core.windows.net/samples/sample6.png)를 사용하겠습니다.

  ![작업 - 콘솔 쿼리 매개 변수, 헤더 및 요청 본문 상자 만들기](images/job-api-console-inputs.PNG)

### <a name="submit-your-request"></a>요청 제출
**보내기**를 선택합니다. 작업 ID가 생성됩니다. 그 다음 단계에서 사용할 수 있도록 이 ID를 복사합니다.

  `"JobId": "2018014caceddebfe9446fab29056fd8d31ffe"`

### <a name="open-the-get-job-details-page"></a>[가져오기 작업 세부 정보] 페이지가 열림
**가져오기**를 선택한 다음, 지역과 일치하는 단추를 선택하여 API를 엽니다.

  ![작업 - 콘솔 만들기 결과 가져오기](images/test-drive-job-4.png)

### <a name="review-the-response"></a>응답 검토

**teamName** 및 **JobID**의 값을 입력합니다. 구독 키를 입력하고 **보내기**를 선택합니다. 다음 응답은 샘플 작업 상태 및 세부 정보를 보여줍니다.

```
    {
        "Id": "2018014caceddebfe9446fab29056fd8d31ffe",
        "TeamName": "some team name",
        "Status": "InProgress",
        "WorkflowId": "OCR",
        "Type": "Image",
        "CallBackEndpoint": "",
        "ReviewId": "",
        "ResultMetaData": [],
        "JobExecutionReport": 
        [
            {
            "Ts": "2018-01-07T00:38:26.7714671",
                "Msg": "Successfully got hasText response from Moderator"
            },
            {
                "Ts": "2018-01-07T00:38:26.4181346",
                "Msg": "Getting hasText from Moderator"
            },
            {
                "Ts": "2018-01-07T00:38:25.5122828",
                "Msg": "Starting Execution - Try 1"
            }
        ]
    }
```

## <a name="navigate-to-the-review-tool"></a>검토 도구로 이동
Content Moderator 대시보드에서 **검토** > **이미지**를 선택합니다. 스캔한 이미지가 나타나고, 사람이 즉시 검토할 수 있는 상태입니다.

  ![세 명의 자전거 선수로 이루어진 검토 도구 이미지](images/ocr-sample-image.PNG)

## <a name="next-steps"></a>다음 단계

코드에서 REST API를 사용하거나 [Jobs .NET 빠른 시작](moderation-jobs-quickstart-dotnet.md)을 시작하여 응용 프로그램을 통합합니다.
