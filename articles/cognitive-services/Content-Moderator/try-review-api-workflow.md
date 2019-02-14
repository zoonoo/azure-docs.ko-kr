---
title: API 콘솔의 콘텐츠 조정 워크플로 - Content Moderator
titlesuffix: Azure Cognitive Services
description: Azure Content Moderator의 워크플로 작업을 사용하여 워크플로를 생성 또는 업데이트하거나 검토 API를 사용하여 워크플로 세부 정보를 가져오는 방법에 대해 알아봅니다.
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: sajagtap
ms.openlocfilehash: 1c18544a0fd135eb546660c442b865bf1249dfe5
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55883087"
---
# <a name="workflows-from-the-api-console"></a>API 콘솔의 워크플로

Azure Content Moderator의 [워크플로 작업](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b46b3f9b0711b43c4c59)을 사용하여 워크플로를 만들거나 업데이트하거나 Review API를 사용하여 워크플로 세부 정보를 가져옵니다. 이 API를 사용하여 워크플로에 대한 간단하고 복잡하며 중첩된 식을 정의할 수 있습니다. 워크플로는 팀이 사용하도록 검토 도구에 표시됩니다. 또한 워크플로는 Review API의 작업 조작으로 사용됩니다.

## <a name="prerequisites"></a>필수 조건

1. [검토 도구](https://contentmoderator.cognitive.microsoft.com/)로 이동합니다. 아직 등록하지 않은 경우 등록합니다. 
2. 검토 도구의 **설정**에서 검토 도구의 [워크플로 자습서](Review-Tool-User-Guide/Workflows.md)에 표시된 대로 **워크플로** 탭을 선택합니다.

### <a name="browse-to-the-workflows-screen"></a>워크플로 화면으로 이동

Content Moderator 대시보드에서 **검토** > **설정** > **워크플로**를 선택합니다. 기본 워크플로가 표시됩니다.

  ![기본 워크플로](images/default-workflow-listed.PNG)

### <a name="get-the-json-definition-of-the-default-workflow"></a>기본 워크플로의 JSON 정의 가져오기

워크플로에 대한 **편집** 옵션을 선택한 다음, **JSON** 탭을 선택합니다. 다음 JSON 식이 표시됩니다.

    {
        "Type": "Logic",
        "If": {
            "ConnectorName": "moderator",
            "OutputName": "isAdult",
            "Operator": "eq",
            "Value": "true",
            "Type": "Condition"
            },
        "Then": {
        "Perform": [
        {
            "Name": "createreview",
            "CallbackEndpoint": null,
            "Tags": []
        }
        ],
        "Type": "Actions"
        }
    }

## <a name="get-workflow-details"></a>워크플로 세부 정보 가져오기

**워크플로 - 가져오기** 작업을 사용하여 기존 기본 워크플로에 대한 세부 정보를 가져옵니다.

검토 도구에서 [자격 증명](Review-Tool-User-Guide/credentials.md#the-review-tool) 섹션으로 이동합니다.

### <a name="browse-to-the-api-reference"></a>API 참조 찾아보기

1. **자격 증명** 뷰에서 [API 참조](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b46b3f9b0711b43c4c59)를 선택합니다. 
2. **워크플로 - 만들기 또는 업데이트** 페이지가 열리면 [워크플로 - 가져오기](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b44b3f9b0711b43c4c58) 참조로 이동합니다.

### <a name="select-your-region"></a>사용자의 지역 선택

**개방형 API 테스트 콘솔**의 경우 사용자 위치를 가장 근접하게 설명하는 지역을 선택합니다.

  ![워크플로 - 지역 선택 가져오기](images/test-drive-region.png)

  **워크플로 - 가져오기** API 콘솔이 열립니다.

### <a name="enter-parameters"></a>매개 변수 입력

**team**, **workflowname** 및 **Ocp-Apim-Subscription-Key**(구독 키)에 대한 값을 입력합니다.

- **team**: [검토 도구 계정](https://contentmoderator.cognitive.microsoft.com/)을 설정할 때 만든 팀 ID입니다. 
- **workflowname**: 워크플로의 이름입니다. `default`를 사용합니다.
- **Ocp-Apim-Subscription-Key**: **설정** 탭에 있습니다. 자세한 내용은 [개요](overview.md)를 참조하세요.

  ![쿼리 매개 변수 및 헤더 가져오기](images/workflow-get-default.PNG)

### <a name="submit-your-request"></a>요청 제출
  
**보내기**를 선택합니다. 작업이 성공하면 **응답 상태**가 `200 OK`이고, **응답 콘텐츠** 상자에 다음 JSON 워크플로가 표시됩니다.

    {
        "Name": "default",
        "Description": "Default",
        "Type": "Image",
        "Expression": {
        "If": {
            "ConnectorName": "moderator",
            "OutputName": "isadult",
            "Operator": "eq",
            "Value": "true",
            "AlternateInput": null,
            "Type": "Condition"
            },
        "Then": {
            "Perform": [{
                "Name": "createreview",
                "Subteam": null,
                "CallbackEndpoint": null,
                "Tags": []
            }],
            "Type": "Actions"
            },
            "Else": null,
            "Type": "Logic"
            }
    }


## <a name="create-a-workflow"></a>워크플로 만들기

검토 도구에서 [자격 증명](Review-Tool-User-Guide/credentials.md#the-review-tool) 섹션으로 이동합니다.

### <a name="browse-to-the-api-reference"></a>API 참조 찾아보기

**자격 증명** 뷰에서 [API 참조](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b46b3f9b0711b43c4c59)를 선택합니다. **워크플로 - 만들기 또는 업데이트** 페이지가 열립니다.

### <a name="select-your-region"></a>사용자의 지역 선택

**개방형 API 테스트 콘솔**의 경우 사용자 위치를 가장 근접하게 설명하는 지역을 선택합니다.

  ![워크플로 - 만들기 또는 업데이트 페이지 영역 선택](images/test-drive-region.png)

  **워크플로 - 만들기 또는 업데이트** API 콘솔이 열립니다.

### <a name="enter-parameters"></a>매개 변수 입력

**team**, **workflowname** 및 **Ocp-Apim-Subscription-Key**(구독 키)에 대한 값을 입력합니다.

- **team**: [검토 도구 계정](https://contentmoderator.cognitive.microsoft.com/)을 설정할 때 만든 팀 ID입니다. 
- **workflowname**: 새 워크플로의 이름입니다.
- **Ocp-Apim-Subscription-Key**: **설정** 탭에 있습니다. 자세한 내용은 [개요](overview.md)를 참조하세요.

  ![워크플로 - 만들기 또는 업데이트 콘솔 쿼리 매개 변수 및 헤더](images/workflow-console-parameters.PNG)

### <a name="enter-the-workflow-definition"></a>워크플로 정의 입력

1. **요청 본문** 상자를 편집하여 **설명** 및 **형식**(이미지 또는 텍스트)에 대한 세부 정보가 포함된 JSON 요청을 입력합니다. 
2. **식**의 경우 다음과 같이 이전 섹션에서 기본 워크플로 식을 복사합니다.

        {
            "Description": "Default workflow from API console",
            "Type": "Image",
            "Expression": 
                // Copy the default workflow expression from the preceding section
        }

    요청 본문은 다음과 같은 JSON 요청과 유사합니다.

        {
            "Description": "Default workflow from API console",
            "Type": "Image",
            "Expression": {
                "Type": "Logic",
                "If": {
                    "ConnectorName": "moderator",
                    "OutputName": "isAdult",
                    "Operator": "eq",
                    "Value": "true",
                    "Type": "Condition"
                    },
                "Then": {
                "Perform": [
                {
                    "Name": "createreview",
                    "CallbackEndpoint": null,
                    "Tags": [ ]
                }
                ],
                "Type": "Actions"
                }
            }
        }
 
### <a name="submit-your-request"></a>요청 제출
  
**보내기**를 선택합니다. 작업이 성공하면 **응답 상태**가 `200 OK`이고, **응답 콘텐츠** 상자에 `true`가 표시됩니다.

### <a name="check-out-the-new-workflow"></a>새 워크플로 확인

검토 도구에서 **검토** > **설정** > **워크플로**를 선택합니다. 새 워크플로가 표시되고 사용할 준비가 됩니다.

  ![워크플로의 검토 도구 목록](images/workflow-console-new-workflow.PNG)
  
### <a name="review-your-new-workflow-details"></a>새 워크플로 세부 정보 검토

1. 워크플로에 대한 **편집** 옵션을 선택한 다음, **디자이너** 및 **JSON** 탭을 선택합니다.

   ![선택된 워크플로에 대한 디자이너 탭](images/workflow-console-new-workflow-designer.PNG)

2. 워크플로의 JSON 보기를 보려면 **JSON** 탭을 선택합니다.

## <a name="next-steps"></a>다음 단계

* 더 복잡한 워크플로 예제는 [워크플로 개요](workflow-api.md)를 참조하세요.
* [콘텐츠 조정 작업](try-review-api-job.md)을 통해 워크플로를 사용하는 방법을 알아봅니다.
