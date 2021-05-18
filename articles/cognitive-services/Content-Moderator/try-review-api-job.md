---
title: REST API 콘솔에서 조정 작업 사용 - Content Moderator
titleSuffix: Azure Cognitive Services
description: Review API의 작업 운영을 사용하여 Azure Content Moderator에서 이미지 또는 텍스트 콘텐츠에 대한 엔드투엔드 콘텐츠 조정 작업을 시작할 수 있습니다.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: pafarley
ms.openlocfilehash: 924c21037a464770fac13c9b45ddcf261ff5a058
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "96905176"
---
# <a name="define-and-use-moderation-jobs-api-console"></a>조정 작업 정의 및 사용(API 콘솔)

조정 작업은 콘텐츠 조정, 워크플로, 검토 기능에 대한 일종의 래퍼 역할을 합니다. 이 가이드에서는 작업 REST API를 사용하여 콘텐츠 조정 작업을 시작 및 확인하는 방법을 보여 줍니다. API의 구조를 이해하고 나면 이러한 호출을 모든 REST 호환 플랫폼으로 쉽게 이식할 수 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

- Content Moderator [검토 도구](https://contentmoderator.cognitive.microsoft.com/) 사이트에 로그인하거나 계정을 만듭니다.
- (선택 사항) 작업과 함께 사용할 [사용자 지정 워크플로를 정의](./Review-Tool-User-Guide/Workflows.md)합니다. 기본 워크플로를 사용할 수도 있습니다.

## <a name="create-a-job"></a>작업 만들기

조정 작업을 만들려면 [작업 - 만들기](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c5) API 참조 페이지로 이동하여 구독 지역 단추를 선택합니다. [검토 도구](https://contentmoderator.cognitive.microsoft.com/)의 **자격 증명** 페이지에 있는 엔드포인트 URL에서 지역을 찾을 수 있습니다. 이렇게 하면 REST API 호출을 쉽게 구성하고 실행할 수 있는 API 콘솔이 시작됩니다.

![작업 - 페이지 지역 선택 만들기](images/test-drive-job-1.png)

### <a name="enter-rest-call-parameters"></a>REST 호출 매개 변수 입력

REST 호출을 구성하려면 다음 값을 입력합니다.

- **teamName**: [검토 도구](https://contentmoderator.cognitive.microsoft.com/) 계정(검토 도구의 자격 증명 화면에서 **Id** 필드에 있음)을 설정할 때 만든 팀 ID입니다.
- **ContentType**: "Image", "Text" 또는 "Video"일 수 있습니다.
- **ContentId**: 사용자 지정 식별자 문자열입니다. 이 문자열은 API로 전달되고 콜백을 통해 반환됩니다. 이는 내부 식별자 또는 메타데이터를 수정 작업의 결과와 연결하는 데 유용합니다.
- **Workflowname**: 이전에 만든 워크플로의 이름(또는 기본 워크플로에 대한 "기본값")입니다.
- **CallbackEndpoint**: (선택 사항) 검토가 완료될 때 콜백 정보를 수신하는 URL입니다.
- **Ocp-Apim-Subscription-Key**: Content Moderator 키입니다. [검토 도구 **의** 설정](https://contentmoderator.cognitive.microsoft.com) 탭에서 이 키를 찾을 수 있습니다.

### <a name="fill-in-the-request-body"></a>요청 본문 채우기

REST 호출의 본문은 **ContentValue** 필드 하나만 포함합니다. 텍스트를 조정하는 경우 원시 텍스트 콘텐츠에 붙여넣거나 이미지나 비디오를 조정하는 경우 이미지 또는 비디오 URL을 입력합니다. 다음 샘플 이미지 URL을 사용할 수 있습니다. [https://moderatorsampleimages.blob.core.windows.net/samples/sample2.jpg](https://moderatorsampleimages.blob.core.windows.net/samples/sample2.jpg)

![작업 - 콘솔 쿼리 매개 변수, 헤더 및 요청 본문 상자 만들기](images/job-api-console-inputs.PNG)

### <a name="submit-your-request"></a>요청 제출

**보내기** 를 선택합니다. 작업이 성공하면 **응답 상태** 가 `200 OK`이고, **응답 콘텐츠** 상자는 작업용 ID를 표시합니다. 다음 단계에서 사용할 수 있도록 ID를 복사합니다.

![검토 - 콘솔 만들기 응답 콘텐츠 상자에 검토 ID 표시](images/test-drive-job-3.PNG)

## <a name="get-job-status"></a>작업 상태 가져오기

실행 중이거나 완료된 작업의 상태 및 세부 정보를 가져오려면 [작업 - 가져오기](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c3) API 참조 페이지로 이동하여 해당 지역 단추(키가 관리되는 지역)를 선택합니다.

![작업 - 지역 선택 가져오기](images/test-drive-region.png)

위의 섹션과 같이 REST 호출 매개 변수를 입력합니다. 이 단계에서 **JobId** 는 작업을 만들 때 수신한 고유 ID 문자열입니다. **보내기** 를 선택합니다. 작업이 성공하면 **응답 상태** 가 `200 OK`이고, **응답 콘텐츠** 상자는 다음과 같이 JSON 형식으로 작업을 표시합니다.

```json
{  
  "Id":"2018014caceddebfe9446fab29056fd8d31ffe",
  "TeamName":"some team name",
  "Status":"Complete",
  "WorkflowId":"OCR",
  "Type":"Image",
  "CallBackEndpoint":"",
  "ReviewId":"201801i28fc0f7cbf424447846e509af853ea54",
  "ResultMetaData":[  
    {  
      "Key":"hasText",
      "Value":"True"
    },
    {  
      "Key":"ocrText",
      "Value":"IF WE DID \r\nALL \r\nTHE THINGS \r\nWE ARE \r\nCAPABLE \r\nOF DOING, \r\nWE WOULD \r\nLITERALLY \r\nASTOUND \r\nOURSELVE \r\n"
    }
  ],
  "JobExecutionReport":[  
    {  
      "Ts":"2018-01-07T00:38:29.3238715",
      "Msg":"Posted results to the Callbackendpoint: https://requestb.in/vxke1mvx"
    },
    {  
      "Ts":"2018-01-07T00:38:29.2928416",
      "Msg":"Job marked completed and job content has been removed"
    },
    {  
      "Ts":"2018-01-07T00:38:29.0856472",
      "Msg":"Execution Complete"
    },
    {  
      "Ts":"2018-01-07T00:38:26.7714671",
      "Msg":"Successfully got hasText response from Moderator"
    },
    {  
      "Ts":"2018-01-07T00:38:26.4181346",
      "Msg":"Getting hasText from Moderator"
    },
    {  
      "Ts":"2018-01-07T00:38:25.5122828",
      "Msg":"Starting Execution - Try 1"
    }
  ]
}
```

![작업 - REST 호출 응답 가져오기](images/test-drive-job-5.png)

### <a name="examine-the-new-reviews"></a>새 검토 검사

콘텐츠 작업에서 검토를 만든 경우 [검토 도구](https://contentmoderator.cognitive.microsoft.com)에서 볼 수 있습니다. 사용하는 콘텐츠에 따라 **검토** > **이미지**/**텍스트**/**비디오** 를 선택합니다. 콘텐츠는 사용자가 검토할 수 있도록 준비 완료된 상태로 표시되어야 합니다. 인간 조정자가 자동 할당된 태그 및 예측 데이터를 검토하고 최종 조정 결정을 제출하면 작업 API가 이 모든 정보를 지정된 콜백 엔드포인트로 제출합니다.

## <a name="next-steps"></a>다음 단계

이 가이드에서는 REST API를 사용하여 콘텐츠 조정 작업을 만들고 쿼리하는 방법을 알아보았습니다. 다음으로 [전자상거래 조정](./ecommerce-retail-catalog-moderation.md) 자습서와 같은 엔드투엔드 조정 시나리오에 작업을 통합합니다.