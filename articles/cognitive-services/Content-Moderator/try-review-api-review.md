---
title: REST API 콘솔로 검토 검토 만들기 - 콘텐츠 중재자
titleSuffix: Azure Cognitive Services
description: Azure 콘텐츠 중재자 검토 API를 사용하여 사용자 중재를 위한 이미지 또는 텍스트 검토를 만듭니다.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 03/18/2019
ms.author: pafarley
ms.openlocfilehash: a9726e41a84926d00d48b51e31f534a3d8c2fe0c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "72757134"
---
# <a name="create-human-reviews-rest"></a>인간 리뷰 만들기(REST)

검토는 인간 [운영자가](./review-api.md#reviews) 평가할 콘텐츠를 저장하고 표시합니다. 사용자가 검토를 완료하면 결과가 지정된 콜백 끝점으로 전송됩니다. 이 가이드에서는 API 콘솔을 통해 REST API 검토를 사용하여 리뷰를 설정하는 방법을 알아봅니다. API의 구조를 이해하면 이러한 호출을 REST 호환 플랫폼에 쉽게 포팅할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

- 콘텐츠 운영자 [검토 도구](https://contentmoderator.cognitive.microsoft.com/) 사이트에서 로그인하거나 계정을 만듭니다.

## <a name="create-a-review"></a>검토 만들기

검토를 만들려면 **[검토 -](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c4)** API 참조 만들기 페이지로 이동하여 주요 지역의 단추를 선택합니다(검토 [도구의](https://contentmoderator.cognitive.microsoft.com/) **자격 증명** 페이지의 끝점 URL에서 찾을 수 있음). 그러면 REST API 호출을 쉽게 생성하고 실행할 수 있는 API 콘솔이 시작됩니다.

![검토 - 지역 선택 받기](images/test-drive-region.png)

### <a name="enter-rest-call-parameters"></a>REST 호출 매개 변수 입력

**팀 이름**및 **Ocp-Apim-구독 키에**대 한 값을 입력 합니다.

- **teamName**: [검토 도구](https://contentmoderator.cognitive.microsoft.com/) 계정을 설정할 때 생성한 팀 ID입니다(검토 도구의 자격 증명 화면의 **ID** 필드에 있음).
- **Ocp-Apim-구독-키**: 콘텐츠 중재자 키. [검토 도구의](https://contentmoderator.cognitive.microsoft.com) **설정** 탭에서 찾을 수 있습니다.

### <a name="enter-a-review-definition"></a>검토 정의 입력

**요청 본문** 상자를 편집하여 다음 필드를 사용하여 JSON 요청을 입력합니다.

- **메타데이터**: 사용자 지정 키-값 쌍을 콜백 끝점으로 반환합니다. 키가 [검토 도구에](https://contentmoderator.cognitive.microsoft.com)정의된 짧은 코드인 경우 태그로 나타납니다.
- **내용**: 이미지 및 비디오 콘텐츠의 경우 콘텐츠를 가리키는 URL 문자열입니다. 텍스트 콘텐츠의 경우 실제 텍스트 문자열입니다.
- **ContentId**: 사용자 지정 식별자 문자열입니다. 이 문자열은 API로 전달되고 콜백을 통해 반환됩니다. 내부 식별자 또는 메타데이터를 조정 작업의 결과와 연결하는 데 유용합니다.
- **콜백엔드포인트**: (선택 사항) 검토가 완료되면 콜백 정보를 수신하는 URL입니다.

기본 요청 본문에는 만들 수 있는 다양한 유형의 리뷰의 예가 표시됩니다.

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
  
**전송을 선택합니다.** 작업이 성공하면 응답 **상태가** `200 OK`이고 응답 **콘텐츠** 상자에 검토할 ID가 표시됩니다. 다음 단계에서 사용할 수 있도록 ID를 복사합니다.

![검토 - 콘솔 만들기 응답 콘텐츠 상자에 검토 ID 표시](images/test-drive-review-2.PNG)

### <a name="examine-the-new-review"></a>새 검토 검토

검토 [도구에서](https://contentmoderator.cognitive.microsoft.com)**이미지**/**텍스트**/**비디오** **검토를** > 선택합니다(사용한 콘텐츠에 따라 다름). 업로드한 콘텐츠가 표시되어야 하며, 사람의 검토를 위해 준비된 것입니다.

![축구공 이미지 검토 도구](images/test-drive-review-5.PNG)

## <a name="get-review-details"></a>검토 세부 정보 가져오기

기존 검토에 대한 세부 정보를 검색하려면 검토 - API 참조 [페이지로](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c2) 이동하여 해당 지역의 단추(키가 관리되는 지역)를 선택합니다.

![워크플로 - 지역 선택 가져오기](images/test-drive-region.png)

위의 섹션에서와 같이 REST 호출 매개 변수를 입력합니다. 이 단계에서 **는 reviewId는** 검토를 만들 때 받은 고유 ID 문자열입니다.

![검토 - 콘솔 만들기 결과 가져오기](images/test-drive-review-3.PNG)
  
**전송을 선택합니다.** 작업이 성공하면 응답 **상태는** `200 OK`, 응답 **내용** 상자에 다음과 같이 JSON 형식으로 검토 세부 정보가 표시됩니다.

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

응답에서 다음 필드를 기록해 둡을 기록합니다.

- **상태**
- **검토자ResultTags**: 인간 검토 팀이 태그가 수동으로 추가된 경우 **나타납니다(createdBy** 필드 표시).
- **메타데이터**: 휴먼 리뷰 팀이 변경하기 전에 검토에 처음 추가된 태그를 보여 주는 것입니다.

## <a name="next-steps"></a>다음 단계

이 가이드에서는 REST API를 사용하여 콘텐츠 검토 검토를 만드는 방법을 배웠습니다. 그런 다음 검토를 [전자 상거래 중재](./ecommerce-retail-catalog-moderation.md) 자습서와 같은 종단 간 중재 시나리오에 통합합니다.