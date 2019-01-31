---
title: 조정 작업 및 사람이 참여하는 검토 - Content Moderator
titlesuffix: Azure Cognitive Services
description: Azure Content Moderator 검토 API를 사용하여 사람이 참여하는 기능과 기계 지원 조정을 결합하면 업무를 가장 효율적으로 완료할 수 있습니다.
services: cognitive-services
author: sanjeev3
manager: cgronlun
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: sajagtap
ms.openlocfilehash: dd7a78f1033a00935346216ae1f80e5400deea07
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55206360"
---
# <a name="content-moderation-jobs-and-reviews"></a>콘텐츠 조정 작업 및 검토

사용자 비즈니스를 위한 최상의 결과를 얻기 위해 Azure Content Moderator [검토 API](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c5)를 사용하여 사람이 참여하는 기능과 기계 지원 조정을 결합합니다.

검토 API는 사람의 감독을 콘텐츠 조정 프로세스에 포함시키는 다음과 같은 방법을 제공합니다.

* `Job` 작업은 한 단계로 기계 지원 조정과 사용자 검토 생성을 시작하는 데 사용됩니다.
* `Review` 작업은 조정 단계 외에 사용자 검토 만들기에 사용됩니다.
* `Workflow` 작업은 검토 만들기에 대한 임계값을 사용하여 검사를 자동화하는 워크플로를 관리하는 데 사용됩니다.

`Job` 및 `Review` 작업은 상태 및 결과를 받기 위한 콜백 엔드포인트를 수락합니다.

이 문서에는 `Job` 및 `Review` 작업이 포함됩니다. 워크플로 정의를 만들고, 편집하고, 가져오는 방법에 대한 내용은 [워크플로 개요](workflow-api.md)를 읽어보세요.

## <a name="job-operations"></a>업무 작업

### <a name="start-a-job"></a>작업 시작
`Job.Create` 작업을 사용하여 조정 및 사용자 검토 만들기 작업을 시작합니다. Content Moderator는 콘텐츠를 검사하고 지정된 워크플로를 평가합니다. 워크플로 결과에 따라 검토를 만들거나 단계를 건너뜁니다. 또한 이후 조정 및 사후 검토 태그를 콜백 엔드포인트에 제출합니다.

입력에는 다음 정보가 포함됩니다.

- 검토 팀 ID.
- 중재할 콘텐츠.
- 워크플로 이름. (기본값은 “default” 워크플로.)
- 알림에 대한 API 콜백 지점.
 
다음과 같은 응답은 시작된 작업의 식별자를 보여 줍니다. 작업 식별자를 사용하여 작업 상태를 가져오고 세부 정보를 수신합니다.

    {
        "JobId": "2018014caceddebfe9446fab29056fd8d31ffe"
    }

### <a name="get-job-status"></a>작업 상태 가져오기

`Job.Get` 작업 및 작업 식별자를 사용하여 실행 중이거나 완료된 작업의 세부 정보를 가져옵니다. 작업은 조정 작업이 비동기로 실행하는 동안 즉시 반환됩니다. 결과는 콜백 엔드포인트를 통해 반환됩니다.

입력에는 다음 정보가 포함됩니다.

- 검토 팀 ID: 이전 작업에서 반환된 검토 식별자

응답에는 다음과 같은 정보가 포함됩니다.

- 만든 검토의 식별자. (최종적 검토 결과를 얻으려면 이 ID를 사용합니다.)
- 작업의 상태(완료 또는 진행 중): 할당된 조정 태그(키-값 쌍)
- 작업 실행 보고서입니다.
 
 
        {
            "Id": "2018014caceddebfe9446fab29056fd8d31ffe",
            "TeamName": "some team name",
            "Status": "Complete",
            "WorkflowId": "OCR",
            "Type": "Image",
            "CallBackEndpoint": "",
            "ReviewId": "201801i28fc0f7cbf424447846e509af853ea54",
            "ResultMetaData":[
            {
            "Key": "hasText",
            "Value": "True"
            },
            {
            "Key": "ocrText",
            "Value": "IF WE DID \r\nALL \r\nTHE THINGS \r\nWE ARE \r\nCAPABLE \r\nOF DOING, \r\nWE WOULD \r\nLITERALLY \r\nASTOUND \r\nOURSELVE \r\n"
            }
            ],
            "JobExecutionReport": [
            {
                "Ts": "2018-01-07T00:38:29.3238715",
                "Msg": "Posted results to the Callbackendpoint: https://requestb.in/vxke1mvx"
                },
                {
                "Ts": "2018-01-07T00:38:29.2928416",
                "Msg": "Job marked completed and job content has been removed"
                },
                {
                "Ts": "2018-01-07T00:38:29.0856472",
                "Msg": "Execution Complete"
                },
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
 
![사용자 중재자를 위한 이미지 검토](images/ocr-sample-image.PNG)

## <a name="review-operations"></a>검토 작업

### <a name="create-reviews"></a>검토 만들기

`Review.Create` 작업을 사용하여 사용자 검토를 만듭니다. 다른 곳에서 조정하거나 사용자 지정 논리를 사용하여 조정 태그를 할당합니다.

이 작업에 대한 입력은 다음을 포함합니다.

- 중재할 콘텐츠.
- 사용자 중재자에 의해 할당된 검토용 태그(키 값 쌍).

다음과 같은 응답은 검토 식별자를 보여 줍니다.

    [
        "201712i46950138c61a4740b118a43cac33f434",
    ]


### <a name="get-review-status"></a>검토 상태 가져오기
`Review.Get` 작업을 사용하여 조정된 이미지의 사용자 검토를 완료한 후 결과를 가져옵니다. 제공된 콜백 엔드포인트를 통해 알림을 받습니다. 

작업은 두 가지 태그 집합을 반환합니다. 

* 조정 서비스에 의해 할당된 태그
* 사용자 검토가 완료된 후 태그

입력에는 최소한 다음이 포함됩니다.

- 검토 팀 이름
- 이전 작업에서 반환되는 검토 식별자

응답에는 다음과 같은 정보가 포함됩니다.

- 검토 상태
- 사용자 검토자가 확인하는 태그(키-값 쌍)
- 조정 서비스에 의해 할당된 태그(키-값 쌍)

두 검토자 할당 태그(**reviewerResultTags**) 및 초기 태그(**metadata**)를 다음 샘플 응답에서 확인합니다.

    {
        "reviewId": "201712i46950138c61a4740b118a43cac33f434",
        "subTeam": "public",
        "status": "Complete",
        "reviewerResultTags": [
        {
            "key": "a",
            "value": "False"
        },
        {
            "key": "r",
            "value": "True"
        },
        {
            "key": "sc",
            "value": "True"
        }
        ],
        "createdBy": "{teamname}",
        "metadata": [
        {
            "key": "sc",
            "value": "true"
        }
        ],
        "type": "Image",
        "content": "https://reviewcontentprod.blob.core.windows.net/{teamname}/IMG_201712i46950138c61a4740b118a43cac33f434",
        "contentId": "0",
        "callbackEndpoint": "{callbackUrl}"
    }

## <a name="next-steps"></a>다음 단계

* [작업 API 콘솔](try-review-api-job.md)을 시험 사용하고 REST API 코드 샘플을 사용합니다. Visual Studio 및 C#에 익숙한 경우 [작업 .NET 빠른 시작](moderation-jobs-quickstart-dotnet.md)을 확인하세요. 
* 검토의 경우 [검토 API 콘솔](try-review-api-review.md)로 시작하고 REST API 코드 샘플을 사용합니다. 그런 다음, [검토 .NET 빠른 시작](moderation-reviews-quickstart-dotnet.md)을 참조하세요.
* 비디오 검토의 경우 [비디오 검토 빠른 시작](video-reviews-quickstart-dotnet.md)을 사용하여 [비디오 검토에 전사를 추가](video-transcript-reviews-quickstart-dotnet.md)하는 방법을 알아봅니다.
