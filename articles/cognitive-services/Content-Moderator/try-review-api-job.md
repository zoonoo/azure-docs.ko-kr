---
title: REST API 콘솔-Content Moderator를 사용 하 여 사용 하 여 조정 작업
titlesuffix: Azure Cognitive Services
description: Review API의 작업 운영을 사용하여 Azure Content Moderator에서 이미지 또는 텍스트 콘텐츠에 대한 엔드투엔드 콘텐츠 조정 작업을 시작할 수 있습니다.
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: article
ms.date: 03/18/2019
ms.author: sajagtap
ms.openlocfilehash: 7827cee2af2dfc0c1fddc407c1d146dc9a66c514
ms.sourcegitcommit: 563f8240f045620b13f9a9a3ebfe0ff10d6787a2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/01/2019
ms.locfileid: "58756089"
---
# <a name="define-and-use-moderation-jobs-rest"></a>정의 및 조정 작업 (REST)를 사용 합니다.

조정 작업은 특정 유형의 콘텐츠 조정, 워크플로 및 검토의 기능에 대 한 래퍼 역할도합니다. 이 가이드에서는 작업 REST Api를 사용 하 여 시작 하 고 콘텐츠 조정 작업을 확인 하는 방법을 보여 줍니다. Api의 구조를 이해 하면 이러한 모든 REST 호환 플랫폼이 호출을 쉽게 이식할 수 있습니다.

## <a name="prerequisites"></a>필수 조건

- 로그인 또는 Content Moderator에서 계정을 만듭니다 [검토 도구](https://contentmoderator.cognitive.microsoft.com/) 사이트입니다.
- (선택 사항) [사용자 지정 워크플로 정의](./Review-Tool-User-Guide/Workflows.md) ; 작업을 사용 하는 기본 워크플로 사용할 수도 있습니다.

## <a name="create-a-job"></a>작업 만들기

조정 작업을 만들려면로 이동 합니다 [작업-만들기](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c5) API 페이지를 참조 하 고 키 지역에 대 한 단추를 선택 (에서 끝점 URL에서 찾을 수 있습니다는 **자격 증명** 페이지를 [검토 도구](https://contentmoderator.cognitive.microsoft.com/)). 이 API 콘솔에서 쉽게 생성 하 고 수 있는 REST API 호출 실행을 시작 합니다.

![작업-만들기 페이지 지역 선택](images/test-drive-job-1.png)

### <a name="enter-rest-call-parameters"></a>REST 호출 매개 변수를 입력 합니다.

REST 호출을 생성 하는 다음 값을 입력 합니다.

- **teamName**: 팀 ID를 설정할 때 만든 프로그램 [검토 도구](https://contentmoderator.cognitive.microsoft.com/) 계정 (에 **Id** 검토 도구 사용자의 자격 증명 화면에서 필드).
- **ContentType**: "Image", "Text" 또는 "비디오" 수 있습니다.
- **ContentId**: 사용자 지정 식별자 문자열입니다. 이 문자열은 API로 전달되고 콜백을 통해 반환됩니다. 조정 작업의 결과 사용 하 여 내부 식별자 또는 메타 데이터를 연결 하는 데 유용 합니다.
- **workflowname**: 이전에 만든 워크플로 (또는 기본 워크플로에 대 한 "기본")의 이름입니다.
- **CallbackEndpoint**: (선택 사항) 정보를 받을 콜백 검토가 완료 되 면 URL입니다.
- **Ocp-Apim-Subscription-Key**: Content Moderator 키입니다. 이 찾을 수 있습니다는 **설정을** 탭의 [검토 도구](https://contentmoderator.cognitive.microsoft.com)합니다.

### <a name="fill-in-the-request-body"></a>요청 본문의 채우기

REST 호출의 본문에 단일 필드를 포함 **ContentValue**합니다. 텍스트를 중재 하는 경우 원시 텍스트 콘텐츠를 붙여 넣거나 이미지/비디오를 중재 하는 경우 이미지 또는 비디오 URL을 입력 합니다. 다음 샘플 이미지 URL을 사용할 수 있습니다. [https://moderatorsampleimages.blob.core.windows.net/samples/sample2.jpg](https://moderatorsampleimages.blob.core.windows.net/samples/sample2.jpg)

![작업 - 콘솔 쿼리 매개 변수, 헤더 및 요청 본문 상자 만들기](images/job-api-console-inputs.PNG)

### <a name="submit-your-request"></a>요청 제출

**보내기**를 선택합니다. 작업에 성공 하면 합니다 **응답 상태** 됩니다 `200 OK`, 및 **응답 콘텐츠** 작업에 대 한 ID 상자에 표시 됩니다. 다음 단계에서 사용할 수 있도록 ID를 복사합니다.

![검토 - 콘솔 만들기 응답 콘텐츠 상자에 검토 ID 표시](images/test-drive-job-3.PNG)

## <a name="get-job-status"></a>작업 상태 가져오기

상태 및 실행 중이거나 완료 된 작업의 세부 정보를 얻으려면로 이동 합니다 [작업-가져오기](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c3) API 페이지를 참조 하 고 해당 지역에 대 한 단추를 선택 (키 관리 되는 영역).

![작업-Get 지역 선택](images/test-drive-region.png)

위의 섹션에서와 같이 REST 호출 매개 변수를 입력 합니다. 이 단계에서는 **JobId** 작업을 만들 때 받은 고유 ID 문자열입니다. **보내기**를 선택합니다. 작업에 성공 하면 합니다 **응답 상태** 됩니다 `200 OK`, 및 **응답 콘텐츠** 상자는 다음과 같은 JSON 형식으로 작업을 표시:

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

![작업-REST 호출 응답 가져오기](images/test-drive-job-5.png)

### <a name="examine-the-new-reviews"></a>새 review(s) 검사

검토를 만드는 콘텐츠 작업 결과 볼 수 있습니다에 [검토 도구](https://contentmoderator.cognitive.microsoft.com)합니다. 선택 **검토** > **이미지**/**텍스트**/**비디오** (에 따라 새로운 콘텐츠 사용). 콘텐츠가 표시 하는 사용자 검토 준비가 되었습니다. 자동 할당 된 태그와 예측 데이터를 검토 하 고 최종 조정 결정을 제출 하는 사람이 중재자, 후 지정 된 콜백 끝점 끝점에이 정보는 모두 작업 API를 제출 합니다.

## <a name="next-steps"></a>다음 단계

이 가이드에서는 만들고 REST API를 사용 하 여 콘텐츠 조정 작업을 쿼리 하는 방법을 알아보았습니다. 같은 엔드-투-엔드 조정 시나리오에 작업을 다음으로 통합 합니다 [상거래 조정](./ecommerce-retail-catalog-moderation.md) 자습서입니다.