---
title: 조정 워크플로 - Content Moderator
titlesuffix: Azure Cognitive Services
description: 검토 API의 Job 작업에 워크플로를 사용하여 콘텐츠 정책 및 임계값에 따라 사람이 참여하는 검토를 자동화합니다.
services: cognitive-services
author: sanjeev3
manager: cgronlun
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: sajagtap
ms.openlocfilehash: 3a3ff5ed325205c5ae17aaff053127f4e84504bf
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/14/2019
ms.locfileid: "54262662"
---
# <a name="automate-moderation-reviews-with-workflows"></a>워크플로 사용하여 조정 검토 자동화

Content Moderator는 워크플로를 관리하는 도구 및 API를 포함합니다. [검토 API의 Job 작업](review-api.md)으로 워크플로를 사용하여 콘텐츠 정책 및 임계값에 따라 사람이 참여하는 검토 만들기를 자동화합니다.

검토 API는 사람의 감독을 콘텐츠 조정 프로세스에 포함시키는 다음과 같은 방법을 제공합니다.

1. **Job** 작업은 한 단계로 기계 지원 조정과 사용자 검토 생성 시작에 대한 것입니다.
1. **Review** 작업은 조정 단계 외에 사람이 참여하는 검토 만들기에 대한 것입니다.
1. **Workflow** 작업은 검토 만들기에 대한 임계값을 사용하여 검사를 자동화하는 워크플로 관리에 대한 것입니다.

이 문서에서는 **Workflow** 작업을 다룹니다. 콘텐츠 조정 작업 및 검토에 대해 알아보려면 [작업 및 검토](review-api.md) 개요를 참조하세요.

**기본** 워크플로를 확인하는 것은 Content Moderator에서 워크플로 해석을 시작하는 가장 좋은 방법입니다.

## <a name="your-first-workflow"></a>첫 번째 워크플로

첫 번째 워크플로는 [검토 도구 팀](https://contentmoderator.cognitive.microsoft.com/)과 함께 번들로 제공됩니다. 아직 등록하지 않은 경우 지금 등록합니다.

설정 탭 아래의 [검토 도구의 워크플로](Review-Tool-User-Guide/Workflows.md) 화면으로 이동합니다. 다음 이미지에 표시된 것과 같이 **기본** 워크플로가 표시됩니다.

![Content Moderator 워크플로](Review-Tool-User-Guide/images/2-workflows-1.png)

### <a name="open-the-default-workflow"></a>기본 워크플로 열기

**편집** 옵션을 사용하여 이미지 ![Content Moderator 기본 워크플로](images/default-workflow-listed.PNG)에 표시된 것과 같이 워크플로 편집 페이지를 엽니다.

### <a name="the-designer-view"></a>디자이너 보기

워크플로에 대한 **디자이너** 탭이 표시됩니다. 디자이너 보기는 다음 단계를 보여줍니다.

1. 평가될 워크플로에 대한 **조건**입니다. 이 경우 워크플로는 Content Moderator의 이미지 API를 호출하고 `isAdult` 출력이 `true`와 같은지 여부를 확인합니다.
1. 조건을 만족하는 경우 수행될 **작업**입니다. 이 경우 워크플로는 `isAdult` 출력이 `true`이면 검토 도구에서 검토를 만듭니다.

![Content Moderator 기본 워크플로 - 디자이너](images/default-workflow-designer.png)

### <a name="the-json-view"></a>JSON 보기

워크플로의 JSON 정의를 보려면 **JSON** 탭을 선택합니다.

    {
        "Type": "Logic",
        If": {
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

### <a name="key-learning"></a>주요 학습

Content Moderator에서 워크플로는 구성하기 쉽고 유연합니다. 기본 제공 디자이너가 요구 사항을 충족하지 않는 경우 **JSON** 형식으로 워크플로 정의를 작성합니다. 그런 다음, [워크플로 API](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b46b3f9b0711b43c4c59)로 JSON 정의를 사용하여 애플리케이션에서 워크플로를 만들고 관리합니다.

## <a name="define-a-custom-workflow"></a>사용자 지정 워크플로 정의

Content Moderator의 워크플로 기능은 사용자 지정 워크플로의 정의 및 사용을 허용합니다. [검토 도구 워크플로 방법](Review-Tool-User-Guide/Workflows.md) 문서를 사용하여 사용자 지정 워크플로를 정의합니다. 이 워크플로는 Content Moderator의 OCR 기능을 사용하여 샘플 이미지에서 텍스트를 추출합니다. 그런 다음, 검토 도구에서 검토를 만듭니다.

### <a name="the-sample-image"></a>샘플 이미지

[샘플 이미지](https://moderatorsampleimages.blob.core.windows.net/samples/sample5.png)를 로컬 드라이브에 저장합니다. 연습을 위해 이 이미지가 필요합니다.

### <a name="the-designer-view"></a>디자이너 보기

**디자이너** 탭 및 [워크플로 만들기 자습서](Review-Tool-User-Guide/Workflows.md)를 선택하여 사용자 지정 워크플로를 정의합니다. 다음 이미지는 디자이너의 **조건** 보기를 보여줍니다. 나머지 단계를 확인하려면 자습서를 참조하세요.

![Content Moderator - 워크플로 조건](Review-Tool-User-Guide/images/ocr-workflow-step-2-condition.PNG)

### <a name="the-json-view"></a>JSON 보기

사용자 지정 워크플로의 다음 JSON 정의를 보려면 **JSON** 탭을 선택합니다. JSON 정의에서 **If-Then** 문이 디자이너 보기를 사용하여 정의한 단계에 해당하는 방법을 주의합니다.

    {
        "Type": "Logic",
        "If": {
            "ConnectorName": "moderator",
            "OutputName": "hasText",
            "Operator": "eq",
            "Value": "true",
            "Type": "Condition"
        },
        "Then": {
        "Perform": [
        {
            "Name": "createreview",
            "CallbackEndpoint": null,
            "Tags": [
            {
                "Tag": "a",
                "IfCondition": {
                    "ConnectorName": "moderator",
                    "OutputName": "hasText",
                    "Operator": "eq",
                    "Value": "true",
                    "Type": "Condition"
                }
            }
            ]
        }
        ],
        "Type": "Actions"
        }
    }

### <a name="workflow-result"></a>워크플로 결과

워크플로 화면에서 워크플로를 테스트하면 다음 검토가 생성됩니다. 검토를 확인하려면 **검토** 아래의 **이미지** 탭으로 이동합니다.
주요 조건은 텍스트의 현재 상태에 대해 긍정을 테스트했으므로 워크플로는 검토를 만들었습니다. 또한 검토는 이미지 검토에서 **`a`** 태그를 강조 표시했습니다.

![Content Moderator - 간단한 워크플로 출력](images/ocr-sample-image-workflow1.PNG)


## <a name="advanced-workflow-with-combination"></a>조합을 사용하여 고급 워크플로

### <a name="the-sample-image"></a>샘플 이미지

이전 섹션에서 사용한 동일한 [샘플 이미지](https://moderatorsampleimages.blob.core.windows.net/samples/sample5.png)를 사용합니다.

그러나 이번에는 주요 조건을 두 가지 검사의 조합으로 변경합니다. 텍스트를 검사하는 것 외에도 텍스트에 욕설이 있는지 여부를 확인합니다. 워크플로는 텍스트를 찾**고** 욕설을 검색하는 경우 검토를 생성합니다.

### <a name="the-designer-view"></a>디자이너 보기

**조건**을 **조합**으로 변경하려면 워크플로를 수정합니다. 다음 이미지는 디자이너에 표시되는 새 보기를 보여줍니다.

![Content Moderator - 수정된 워크플로 조건](images/ocr-workflow-2-designer.PNG)

### <a name="the-json-view"></a>JSON 보기

수정된 사용자 지정 워크플로의 다음 JSON 정의를 보려면 **JSON** 탭을 선택합니다. JSON 정의에서 **If-Then** 문이 워크플로에 추가한 새 단계에 해당하는 방법을 주의합니다.

    {
        "Type": "Logic",
        "If": {
        "Left": {
            "ConnectorName": "moderator",
            "OutputName": "hasText",
            "Operator": "eq",
            "Value": "true",
            "Type": "Condition"
            },
        "Right": {
            "ConnectorName": "moderator",
            "OutputName": "text.HasProfanity",
            "Operator": "eq",
            "Value": "true",
            "Type": "Condition",
            "AlternateInput": "moderator.ocrText"
            },
        "Combine": "AND",
        "Type": "Combine"
        },
        "Then": {
        "Perform": [
        {
            "Name": "createreview",
            "CallbackEndpoint": null,
            "Tags": [
            {
                "Tag": "a",
                "IfCondition": {
                    "ConnectorName": "moderator",
                    "OutputName": "hasText",
                    "Operator": "eq",
                    "Value": "true",
                    "Type": "Condition"
                }
            }
            ]
        }
        ],
        "Type": "Actions"
        }
    }

    
### <a name="workflow-result"></a>워크플로 결과

워크플로를 다시 테스트하면 검토가 생성되지 않습니다. 모든 검토의 부재를 확인하려면 **검토** 아래의 **이미지** 탭으로 이동합니다.
워크플로는 추출된 텍스트에서 욕설을 검색하는데 실패했으므로 검토를 생성하지 않았습니다.

![Content Moderator - 수정된 워크플로 출력](images/ocr-workflow-2-result.PNG)


## <a name="the-workflow-api"></a>워크플로 API

[워크플로 작업](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b46b3f9b0711b43c4c59)은 워크플로 기능에 프로그래밍 인터페이스를 제공합니다. 워크플로 API를 사용하여 워크플로를 만들고, 워크플로 세부 정보를 가져오고, 워크플로 정의를 업데이트합니다.

### <a name="get-all-workflow-details"></a>[모든] 워크플로 세부 정보 가져오기

**Workflow-Get** 작업은 다음과 같은 입력을 허용합니다.

- **team**: [검토 도구 계정](https://contentmoderator.cognitive.microsoft.com/)을 설정할 때 만든 팀 ID입니다. 
- **workflowname**: 워크플로의 이름입니다. `default`를 사용하여 시작합니다.
- **Ocp-Apim-Subscription-Key**: **설정** 탭에 있습니다. 자세한 내용은 [개요](overview.md)를 참조하세요.

작업이 성공하면 **응답 상태**가 `200 OK`이고, **응답 콘텐츠** 상자는 JSON 형식으로 워크플로 정의를 표시합니다.
자세히 알아보려면 [워크플로 API 콘솔 빠른 시작](try-review-api-job.md)을 참조하세요.

### <a name="create-or-update-workflow"></a>워크플로 만들기 또는 업데이트

만들기 및 업데이트 작업은 API에서 워크플로 만들기를 허용합니다.

**Workflow-Create or Update** 작업은 다음과 같은 입력을 허용합니다.

- **team**: [검토 도구 계정](https://contentmoderator.cognitive.microsoft.com/)을 설정할 때 만든 팀 ID입니다. 
- **workflowname**: 워크플로의 이름입니다. `default`를 사용하여 시작합니다.
- **Ocp-Apim-Subscription-Key**: **설정** 탭에 있습니다. 자세한 내용은 [개요](overview.md)를 참조하세요.

작업이 성공하면 **응답 상태**가 `200 OK`이고, **응답 콘텐츠** 상자는 `true`를 표시합니다. 자세히 알아보려면 [`Create` 작업을 시험 사용](try-review-api-job.md)합니다.

## <a name="next-steps"></a>다음 단계

사용자 지정 워크플로를 만드는 방법을 알아보려면 [검토 도구의 워크플로 자습서](Review-Tool-User-Guide/Workflows.md)를 참조하세요. 

[워크플로 API 콘솔](try-review-api-job.md)을 시험 사용하고 REST API 코드 샘플을 사용합니다. 

마지막으로 [작업 API 콘솔](try-review-api-job.md) 및 [작업 .NET 빠른 시작](moderation-jobs-quickstart-dotnet.md)에 표시된 것처럼 **Job** 작업으로 사용자 지정 워크플로를 사용합니다.
