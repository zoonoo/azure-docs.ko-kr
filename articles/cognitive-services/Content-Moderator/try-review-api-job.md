---
title: REST API 콘솔에서 중재 작업 사용 - 콘텐츠 중재자
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
ms.openlocfilehash: 83ee8e0c0583cba72da8702e196f0f38128f8d8a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "72935964"
---
# <a name="define-and-use-moderation-jobs-rest"></a>조정 작업 정의 및 사용(REST)

중재 작업은 콘텐츠 조정, 워크플로 및 리뷰의 기능을 위한 일종의 래퍼 역할을 합니다. 이 가이드에서는 REST API를 사용하여 콘텐츠 조정 작업을 시작하고 확인하는 방법을 보여 주며, API의 구조를 이해하면 이러한 호출을 REST 호환 플랫폼에 쉽게 포팅할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

- 콘텐츠 운영자 [검토 도구](https://contentmoderator.cognitive.microsoft.com/) 사이트에서 로그인하거나 계정을 만듭니다.
- (선택 사항) 작업에 사용할 [사용자 지정 워크플로를 정의합니다.](./Review-Tool-User-Guide/Workflows.md) 기본 워크플로를 사용할 수도 있습니다.

## <a name="create-a-job"></a>작업 만들기

조정 작업을 만들려면 [작업 -](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c5) API 참조 만들기 페이지로 이동하여 구독 지역의 단추를 선택합니다(검토 [도구의](https://contentmoderator.cognitive.microsoft.com/) **자격 증명** 페이지의 끝점 URL에서 찾을 수 있음). 그러면 REST API 호출을 쉽게 생성하고 실행할 수 있는 API 콘솔이 시작됩니다.

![작업 - 페이지 지역 선택 만들기](images/test-drive-job-1.png)

### <a name="enter-rest-call-parameters"></a>REST 호출 매개 변수 입력

REST 호출을 생성하려면 다음 값을 입력합니다.

- **teamName**: [검토 도구](https://contentmoderator.cognitive.microsoft.com/) 계정을 설정할 때 생성한 팀 ID입니다(검토 도구의 자격 증명 화면의 **ID** 필드에 있음).
- **콘텐츠 유형**: "이미지", "텍스트" 또는 "비디오"일 수 있습니다.
- **ContentId**: 사용자 지정 식별자 문자열입니다. 이 문자열은 API로 전달되고 콜백을 통해 반환됩니다. 내부 식별자 또는 메타데이터를 조정 작업의 결과와 연결하는 데 유용합니다.
- **워크플로이름:** 이전에 만든 워크플로의 이름(또는 기본 워크플로의 "기본값")입니다.
- **콜백엔드포인트**: (선택 사항) 검토가 완료되면 콜백 정보를 수신하는 URL입니다.
- **Ocp-Apim-구독-키**: 콘텐츠 중재자 키. [검토 도구의](https://contentmoderator.cognitive.microsoft.com) **설정** 탭에서 찾을 수 있습니다.

### <a name="fill-in-the-request-body"></a>요청 본문 채우기

REST 호출의 본문에는 하나의 필드인 **ContentValue**가 포함되어 있습니다. 텍스트를 조정하는 경우 원시 텍스트 콘텐츠에 붙여넣거나 이미지/비디오를 조정하는 경우 이미지 또는 비디오 URL을 입력합니다. 다음 샘플 이미지 URL을 사용할 수 있습니다.[https://moderatorsampleimages.blob.core.windows.net/samples/sample2.jpg](https://moderatorsampleimages.blob.core.windows.net/samples/sample2.jpg)

![작업 - 콘솔 쿼리 매개 변수, 헤더 및 요청 본문 상자 만들기](images/job-api-console-inputs.PNG)

### <a name="submit-your-request"></a>요청 제출

**전송을 선택합니다.** 작업이 성공하면 응답 **상태가** `200 OK`이고 응답 **콘텐츠** 상자에 작업에 대한 ID가 표시됩니다. 다음 단계에서 사용할 수 있도록 ID를 복사합니다.

![검토 - 콘솔 만들기 응답 콘텐츠 상자에 검토 ID 표시](images/test-drive-job-3.PNG)

## <a name="get-job-status"></a>작업 상태 가져오기

실행 중이거나 완료된 작업의 상태 및 세부 정보를 얻으려면 Job - API 참조 [페이지로](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c3) 이동하여 해당 지역의 단추(키가 관리되는 지역)를 선택합니다.

![작업 - 지역 선택 받기](images/test-drive-region.png)

위의 섹션에서와 같이 REST 호출 매개 변수를 입력합니다. 이 단계에서 **JobId는** 작업을 만들 때 받은 고유 ID 문자열입니다. **전송을 선택합니다.** 작업이 성공하면 응답 **상태는** `200 OK`, 응답 **콘텐츠** 상자에 다음과 같이 JSON 형식으로 작업이 표시됩니다.

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

![작업 - REST 콜 응답 받기](images/test-drive-job-5.png)

### <a name="examine-the-new-reviews"></a>새 검토 검토

콘텐츠 작업으로 인해 검토가 생성된 경우 [검토 도구에서](https://contentmoderator.cognitive.microsoft.com)볼 수 있습니다. 사용한 콘텐츠에 따라**이미지**/**텍스트**/**비디오** **검토를** > 선택합니다. 콘텐츠가 표시되어야 하며, 사람의 검토를 위해 준비된 것입니다. 사용자 중재자가 자동 할당된 태그 및 예측 데이터를 검토하고 최종 조정 결정을 제출한 후 작업 API는 지정된 콜백 끝점에 이 모든 정보를 제출합니다.

## <a name="next-steps"></a>다음 단계

이 가이드에서는 REST API를 사용하여 콘텐츠 조정 작업을 만들고 쿼리하는 방법을 배웠습니다. 그런 다음 전자 [상거래 중재](./ecommerce-retail-catalog-moderation.md) 자습서와 같은 종단 간 중재 시나리오에 작업을 통합합니다.