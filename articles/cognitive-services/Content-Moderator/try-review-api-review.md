---
title: REST API 콘솔-Content Moderator를 사용 하 여 조정 검토 만들기
titlesuffix: Azure Cognitive Services
description: 휴먼 조정에 대 한 이미지 또는 텍스트 검토를 만들려면 Azure Content Moderator 검토 Api를 사용 합니다.
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: article
ms.date: 03/18/2019
ms.author: sajagtap
ms.openlocfilehash: 254269ccedc92b9dfc164cc4665a8a8513682773
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/18/2019
ms.locfileid: "58882020"
---
# <a name="create-human-reviews-rest"></a>사용자 검토 (REST) 만들기

[검토](./review-api.md#reviews) 저장 하 고 평가 하는 조정자에 대 한 콘텐츠를 표시 합니다. 사용자 검토를 완료 되 면 결과 지정 된 콜백 끝점에 전송 됩니다. 이 가이드에서는 API 콘솔을 통해 검토 REST Api를 사용 하 여 리뷰를 설정 하는 방법에 알아봅니다. Api의 구조를 이해 하면 이러한 모든 REST 호환 플랫폼이 호출을 쉽게 이식할 수 있습니다.

## <a name="prerequisites"></a>필수 조건

- 로그인 또는 Content Moderator에서 계정을 만듭니다 [검토 도구](https://contentmoderator.cognitive.microsoft.com/) 사이트입니다.

## <a name="create-a-review"></a>검토 만들기

검토를 만들려면로 이동 합니다 **[검토-만들기](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c4)** API 페이지를 참조 하 고 키 지역에 대 한 단추를 선택 (에서 끝점 URL에서 찾을 수 있습니다 합니다 **자격 증명** 페이지 [검토 도구](https://contentmoderator.cognitive.microsoft.com/)). 이 API 콘솔에서 쉽게 생성 하 고 수 있는 REST API 호출 실행을 시작 합니다.

![검토-Get 지역 선택](images/test-drive-region.png)

### <a name="enter-rest-call-parameters"></a>REST 호출 매개 변수를 입력 합니다.

에 대 한 값 입력 **teamName**, 및 **Ocp Apim-구독 키**:

- **teamName**: 팀 ID를 설정할 때 만든 프로그램 [검토 도구](https://contentmoderator.cognitive.microsoft.com/) 계정 (에 **Id** 검토 도구 사용자의 자격 증명 화면에서 필드).
- **Ocp-Apim-Subscription-Key**: Content Moderator 키입니다. 이 찾을 수 있습니다는 **설정을** 탭의 [검토 도구](https://contentmoderator.cognitive.microsoft.com)합니다.

### <a name="enter-a-review-definition"></a>검토 정의 입력 합니다.

편집 합니다 **요청 본문** 상자는 다음 필드가 있는 JSON 요청을 입력 합니다.

- **메타데이터**: 콜백 끝점에 반환할 사용자 지정 키-값 쌍입니다. 키에 정의 된 짧은 코드 인지 합니다 [검토 도구](https://contentmoderator.cognitive.microsoft.com)를 태그로 표시 됩니다.
- **콘텐츠**: 이미지 및 비디오 콘텐츠를 경우 콘텐츠를 가리키는 URL 문자열입니다. 텍스트 콘텐츠에 대 한 실제 텍스트 문자열입니다.
- **ContentId**: 사용자 지정 식별자 문자열입니다. 이 문자열은 API로 전달되고 콜백을 통해 반환됩니다. 조정 작업의 결과 사용 하 여 내부 식별자 또는 메타 데이터를 연결 하는 데 유용 합니다.
- **CallbackEndpoint**: (선택 사항) 정보를 받을 콜백 검토가 완료 되 면 URL입니다.

기본 요청 본문은 다양 한 검토를 만들 수 있습니다 예를 보여 줍니다.

```json
[Image]
[
  {
    "Metadata": [
      {
        "Key": "string",
        "Value": "string"
      }
    ],
    "Type": "Image",
    "Content": "<Content Url>",
    "ContentId": "<Your identifier for this content>",
    "CallbackEndpoint": "<Url where you would receive callbacks>"
  }
]
[Text]
[
  {
    "Metadata": [
      {
        "Key": "string",
        "Value": "string"
      }
    ],
    "Type": "Text",
    "Content": "<Your Text Content>",
    "ContentId": "<Your identifier for this content>",
    "CallbackEndpoint": "<Url where you would receive callbacks>"
  }
]
[Video]
[
  {
    "VideoFrames":[
      {
          "Id": "<Frame Id>",
          "Timestamp": "<Frame Timestamp",
          "FrameImage":"<Frame Image URL",
          "Metadata": [
            {
              "Key": "<Key>",
              "Value": "<Value"
            }
          ],
          "ReviewerResultTags": [
          ]
    ], 
    "Metadata": [
      {
        "Key": "string",
        "Value": "string"
      },
      //For encrypted Videos
        {
          "Key": "protectedType",
          "Value": "AES or FairPlay or Widevine or Playready"
        },
        {
          "Key": "authenticationToken",
          "Value": "your viewtoken(In case of Video Indexer AES encryption type, this value is viewtoken from breakdown json)"
        },
      //For FairPlay encrypted type video include certificateUrl as well
        {
          "Key": "certificateUrl",
          "Value": "your certificate url"
        }
    ],
    "Type": "Video",
    "Content": "<Stream Url>",
    "ContentId": "<Your identifier for this content>",
    "CallbackEndpoint": "<Url where you would receive callbacks>",
    [Optional]
    "Timescale": "<Timescale of the video>
  }
]
```

### <a name="submit-your-request"></a>요청 제출
  
**보내기**를 선택합니다. 작업에 성공 하면 합니다 **응답 상태** 됩니다 `200 OK`, 및 **응답 콘텐츠** 검토 ID 상자에 표시 됩니다. 다음 단계에서 사용할 수 있도록 ID를 복사합니다.

![검토 - 콘솔 만들기 응답 콘텐츠 상자에 검토 ID 표시](images/test-drive-review-2.PNG)

### <a name="examine-the-new-review"></a>새 검토를 검사 합니다.

에 [검토 도구](https://contentmoderator.cognitive.microsoft.com)를 선택 **검토** > **이미지**/**텍스트** / **비디오** (어떤 콘텐츠에 따라 사용한). 업로드 한 콘텐츠 나타나야 사람이 검토를 위해 준비 합니다.

![축구공 이미지 검토 도구](images/test-drive-review-5.PNG)

## <a name="get-review-details"></a>검토 세부 정보 가져오기

기존 검토에 대 한 정보를 검색 하려면로 이동 합니다 [검토-가져오기](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c2) API 페이지를 참조 하 고 해당 지역에 대 한 단추를 선택 (키 관리 되는 영역).

![워크플로 - 지역 선택 가져오기](images/test-drive-region.png)

위의 섹션에서와 같이 REST 호출 매개 변수를 입력 합니다. 이 단계에서는 **reviewId** 검토를 만들 때 받은 고유 ID 문자열입니다.

![검토 - 콘솔 만들기 결과 가져오기](images/test-drive-review-3.PNG)
  
**보내기**를 선택합니다. 작업에 성공 하면 합니다 **응답 상태** 됩니다 `200 OK`, 및 **응답 콘텐츠** 상자 검토 세부 정보를 다음과 같이 JSON 형식으로 표시:

```json
{  
  "reviewId":"201712i46950138c61a4740b118a43cac33f434",
  "subTeam":"public",
  "status":"Complete",
  "reviewerResultTags":[  
    {  
      "key":"a",
      "value":"False"
    },
    {  
      "key":"r",
      "value":"True"
    },
    {  
      "key":"sc",
      "value":"True"
    }
  ],
  "createdBy":"<teamname>",
  "metadata":[  
    {  
      "key":"sc",
      "value":"true"
    }
  ],
  "type":"Image",
  "content":"https://reviewcontentprod.blob.core.windows.net/<teamname>/IMG_201712i46950138c61a4740b118a43cac33f434",
  "contentId":"0",
  "callbackEndpoint":"<callbackUrl>"
}
```

응답에 다음 필드를 기록해 둡니다.

- **status**
- **reviewerResultTags**: 태그는 사용자 검토 팀에서 수동으로 추가 된 경우이 표시 됩니다 (표시 합니다 **createdBy** 필드).
- **메타데이터**: 사용자 검토 팀 사항 변경 하기 전에 검토에 처음 추가 된 태그를 보여 줍니다.

## <a name="next-steps"></a>다음 단계

이 가이드에서는 REST API를 사용 하 여 콘텐츠 조정 리뷰를 만드는 방법을 알아보았습니다. 그런 다음 검토를 엔드-투-엔드 조정 시나리오로 같은 통합 된 [상거래 조정](./ecommerce-retail-catalog-moderation.md) 자습서입니다.