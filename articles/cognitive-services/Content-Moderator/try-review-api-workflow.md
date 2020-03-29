---
title: REST API 콘솔로 중재 워크플로 정의 - 콘텐츠 중재자
titleSuffix: Azure Cognitive Services
description: Azure 콘텐츠 중재자 검토 API를 사용하여 콘텐츠 정책에 따라 사용자 지정 워크플로 및 임계값을 정의할 수 있습니다.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 03/14/2019
ms.author: pafarley
ms.openlocfilehash: 3e58be4b94457d95d28cf6528b9151e4be1802cf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "72754177"
---
# <a name="define-and-use-moderation-workflows-rest"></a>중재 워크플로우 정의 및 사용(REST)

워크플로는 콘텐츠를 보다 효율적으로 처리하는 데 사용할 수 있는 클라우드 기반 사용자 지정 필터입니다. 워크플로는 다양한 서비스에 연결하여 다양한 방식으로 콘텐츠를 필터링한 다음 적절한 조치를 취할 수 있습니다. 이 가이드에서는 API 콘솔을 통해 REST API 워크플로를 사용하여 워크플로를 만들고 사용하는 방법을 보여 주며, 워크플로를 만드는 방법을 보여 주습니다. API의 구조를 이해하면 이러한 호출을 REST 호환 플랫폼에 쉽게 포팅할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

- 콘텐츠 운영자 [검토 도구](https://contentmoderator.cognitive.microsoft.com/) 사이트에서 로그인하거나 계정을 만듭니다.

## <a name="create-a-workflow"></a>워크플로 만들기

워크플로를 만들거나 업데이트하려면 **[워크플로 - API](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b46b3f9b0711b43c4c59)** 참조 만들기 또는 업데이트 페이지로 이동하여 주요 영역에 대한 단추를 선택합니다(검토 [도구의](https://contentmoderator.cognitive.microsoft.com/) **자격 증명** 페이지의 끝점 URL에서 찾을 수 있음). 그러면 REST API 호출을 쉽게 생성하고 실행할 수 있는 API 콘솔이 시작됩니다.

![워크플로 - 만들기 또는 업데이트 페이지 영역 선택](images/test-drive-region.png)

### <a name="enter-rest-call-parameters"></a>REST 호출 매개 변수 입력

**팀,** **워크플로이름**및 **Ocp-Apim-구독 키에**대한 값을 입력합니다.

- **팀**: [검토 도구](https://contentmoderator.cognitive.microsoft.com/) 계정을 설정할 때 생성한 팀 ID입니다(검토 도구의 자격 증명 화면의 **ID** 필드에 있음).
- **워크플로이름**: 추가할 새 워크플로의 이름(또는 기존 워크플로를 업데이트하려는 경우 기존 이름)입니다.
- **Ocp-Apim-구독-키**: 콘텐츠 중재자 키. [검토 도구의](https://contentmoderator.cognitive.microsoft.com) **설정** 탭에서 찾을 수 있습니다.

![워크플로 - 만들기 또는 업데이트 콘솔 쿼리 매개 변수 및 헤더](images/workflow-console-parameters.PNG)

### <a name="enter-a-workflow-definition"></a>워크플로 정의 입력

1. **요청 본문** 상자를 편집하여 **설명** 및 입력(또는)에 **Type** `Image` `Text`대한 세부 정보와 함께 JSON 요청을 입력합니다.
2. **표현식의**경우 기본 워크플로 JSON 식을 복사합니다. 최종 JSON 문자열은 다음과 같아야 합니다.

```json
{
  "Description":"<A description for the Workflow>",
  "Type":"Text",
  "Expression":{
    "Type":"Logic",
    "If":{
      "ConnectorName":"moderator",
      "OutputName":"isAdult",
      "Operator":"eq",
      "Value":"true",
      "Type":"Condition"
    },
    "Then":{
      "Perform":[
        {
          "Name":"createreview",
          "CallbackEndpoint":null,
          "Tags":[

          ]
        }
      ],
      "Type":"Actions"
    }
  }
}
```

> [!NOTE]
> 이 API를 사용하여 워크플로에 대해 간단하고 복잡하며 중첩된 식을 정의할 수 있습니다. [워크플로 - 문서 만들기 또는 업데이트](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b46b3f9b0711b43c4c59) 에는 보다 복잡한 논리의 예가 있습니다.

### <a name="submit-your-request"></a>요청 제출
  
**전송을 선택합니다.** 작업이 성공하면 **응답 상태**가 `200 OK`이고, **응답 콘텐츠** 상자에 `true`가 표시됩니다.

### <a name="examine-the-new-workflow"></a>새 워크플로 검토

검토 [도구에서](https://contentmoderator.cognitive.microsoft.com/) **설정** > **워크플로를 선택합니다.** 새 워크플로가 목록에 나타납니다.

![워크플로의 검토 도구 목록](images/workflow-console-new-workflow.PNG)

워크플로에 대한 **편집** 옵션을 선택하고 **디자이너** 탭으로 이동합니다. 여기서JSON 논리의 직관적인 표현을 볼 수 있습니다.

![선택된 워크플로에 대한 디자이너 탭](images/workflow-console-new-workflow-designer.PNG)

## <a name="get-workflow-details"></a>워크플로 세부 정보 가져오기

기존 워크플로에 대한 세부 정보를 검색하려면 **[워크플로 -](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b44b3f9b0711b43c4c58)** API 참조 페이지로 이동하여 해당 지역의 단추(키가 관리되는 지역)를 선택합니다.

![워크플로 - 지역 선택 가져오기](images/test-drive-region.png)

위의 섹션에서와 같이 REST 호출 매개 변수를 입력합니다. 이번에는 **워크플로 이름이** 기존 워크플로의 이름인지 확인합니다.

![쿼리 매개 변수 및 헤더 가져오기](images/workflow-get-default.PNG)

**전송을 선택합니다.** 작업이 성공하면 응답 **상태는** `200 OK`, 응답 **콘텐츠** 상자에 다음과 같이 JSON 형식으로 워크플로가 표시됩니다.

```json
{
  "Name":"default",
  "Description":"Default",
  "Type":"Image",
  "Expression":{
    "If":{
      "ConnectorName":"moderator",
      "OutputName":"isadult",
      "Operator":"eq",
      "Value":"true",
      "AlternateInput":null,
      "Type":"Condition"
    },
    "Then":{
      "Perform":[
        {
          "Name":"createreview",
          "Subteam":null,
          "CallbackEndpoint":null,
          "Tags":[

          ]
        }
      ],
      "Type":"Actions"
    },
    "Else":null,
    "Type":"Logic"
  }
}
```

## <a name="next-steps"></a>다음 단계

- [콘텐츠 조정 작업](try-review-api-job.md)을 통해 워크플로를 사용하는 방법을 알아봅니다.