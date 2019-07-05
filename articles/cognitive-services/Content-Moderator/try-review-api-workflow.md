---
title: REST API 콘솔-Content Moderator를 사용 하 여 조정 워크플로 정의 합니다.
titlesuffix: Azure Cognitive Services
description: 사용자 지정 워크플로 및 콘텐츠 정책에 따라 임계값을 정의 하는 Azure 콘텐츠 감수 자가 검토 Api를 사용할 수 있습니다.
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: article
ms.date: 03/14/2019
ms.author: sajagtap
ms.openlocfilehash: e150b1321f2fbd348e737222c752203281503643
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60605875"
---
# <a name="define-and-use-moderation-workflows-rest"></a>정의 및 조정 워크플로 (REST)를 사용 합니다.

워크플로 콘텐츠를 보다 효율적으로 처리 하는 데 사용할 수 있는 클라우드 기반 사용자 지정 된 필터입니다. 워크플로 다양 한 가지 방법으로 콘텐츠를 필터링 하 고 적절 한 조치를 취할를 서비스에 연결할 수 있습니다. 이 가이드에서는 API 콘솔을 통해 워크플로 REST Api를 사용 하 여 만들고 워크플로 사용 하는 방법을 보여 줍니다. Api의 구조를 이해 하면 이러한 모든 REST 호환 플랫폼이 호출을 쉽게 이식할 수 있습니다.

## <a name="prerequisites"></a>필수 조건

- 로그인 또는 Content Moderator에서 계정을 만듭니다 [검토 도구](https://contentmoderator.cognitive.microsoft.com/) 사이트입니다.

## <a name="create-a-workflow"></a>워크플로 만들기

만들거나 워크플로 업데이트 하려면로 이동 합니다 **[워크플로-Create 또는 Update](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b46b3f9b0711b43c4c59)** API 페이지를 참조 하 고 키 지역에 대 한 단추를 선택 (에서 끝점 URL에서 찾을 수 있습니다는 **자격 증명**  페이지의 [검토 도구](https://contentmoderator.cognitive.microsoft.com/)). 이 API 콘솔에서 쉽게 생성 하 고 수 있는 REST API 호출 실행을 시작 합니다.

![워크플로 - 만들기 또는 업데이트 페이지 영역 선택](images/test-drive-region.png)

### <a name="enter-rest-call-parameters"></a>REST 호출 매개 변수를 입력 합니다.

에 대 한 값 입력 **team**, **workflowname**, 및 **Ocp Apim-구독 키**:

- **team**: 팀 ID를 설정할 때 만든 프로그램 [검토 도구](https://contentmoderator.cognitive.microsoft.com/) 계정 (에 **Id** 검토 도구 사용자의 자격 증명 화면에서 필드).
- **workflowname**: 추가할 새 워크플로 (또는 기존 이름을 기존 워크플로 업데이트 하려는 경우)의 이름입니다.
- **Ocp-Apim-Subscription-Key**: Content Moderator 키입니다. 이 찾을 수 있습니다는 **설정을** 탭의 [검토 도구](https://contentmoderator.cognitive.microsoft.com)합니다.

![워크플로 - 만들기 또는 업데이트 콘솔 쿼리 매개 변수 및 헤더](images/workflow-console-parameters.PNG)

### <a name="enter-a-workflow-definition"></a>워크플로 정의 입력 합니다.

1. 편집 된 **요청 본문** JSON 요청에 대 한 세부 정보를 사용 하 여 입력 상자 **설명** 및 **형식** (중 하나 `Image` 또는 `Text`).
2. 에 대 한 **식**, JSON 식의 기본 워크플로 복사 합니다. 최종 JSON 문자열은 다음과 같이 표시 됩니다.

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
> 이 API를 사용 하 여 워크플로에 대 한 간단 하 고 복잡 한 중첩 식을 정의할 수 있습니다. 합니다 [워크플로-만들기 또는 업데이트](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b46b3f9b0711b43c4c59) 설명서에는 더 복잡 한 논리의 예입니다.

### <a name="submit-your-request"></a>요청 제출
  
**보내기**를 선택합니다. 작업이 성공하면 **응답 상태**가 `200 OK`이고, **응답 콘텐츠** 상자에 `true`가 표시됩니다.

### <a name="examine-the-new-workflow"></a>새 워크플로 검사 합니다.

에 [검토 도구](https://contentmoderator.cognitive.microsoft.com/)를 선택 **설정** > **워크플로**합니다. 새 워크플로 목록에 표시 됩니다.

![워크플로의 검토 도구 목록](images/workflow-console-new-workflow.PNG)

선택 합니다 **편집** 로 이동 및 워크플로에 대 한 옵션을 **디자이너** 탭 합니다. 여기서는 직관적인 표현을 JSON 논리를 볼 수 있습니다.

![선택된 워크플로에 대한 디자이너 탭](images/workflow-console-new-workflow-designer.PNG)

## <a name="get-workflow-details"></a>워크플로 세부 정보 가져오기

기존 워크플로 대 한 세부 정보를 검색 하려면로 이동 합니다 **[워크플로-Get](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b44b3f9b0711b43c4c58)** API 페이지를 참조 하 고 해당 지역에 대 한 단추를 선택 (키 관리 되는 영역).

![워크플로 - 지역 선택 가져오기](images/test-drive-region.png)

위의 섹션에서와 같이 REST 호출 매개 변수를 입력 합니다. 했는지이 이때 **workflowname** 기존 워크플로의 이름입니다.

![쿼리 매개 변수 및 헤더 가져오기](images/workflow-get-default.PNG)

**보내기**를 선택합니다. 작업에 성공 하면를 **응답 상태** 은 `200 OK`, 및 **응답 콘텐츠** 상자에 워크플로 다음과 같은 JSON 형식으로 표시 됩니다.

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