---
title: 검토, 워크플로 및 작업 개념-Content Moderator
titlesuffix: Azure Cognitive Services
description: 검토, 워크플로 및 작업에 알아봅니다
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 03/14/2019
ms.author: sajagtap
ms.openlocfilehash: c1d4ef640e2ae072dacba7a665b6689e3224c55c
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60607281"
---
# <a name="content-moderation-reviews-workflows-and-jobs"></a>콘텐츠 조정 검토, 워크플로 및 작업

Content Moderator는 기계 지원 조정 실제 시나리오에 대 한 최적의 조정 프로세스를 만들려는 사람이 루프 기능을 사용 하 여 결합 합니다. 클라우드 기반를 통해 이렇게 [검토 도구](https://contentmoderator.cognitive.microsoft.com)합니다. 검토 도구의 핵심 개념에 대해 배웁니다이 가이드에서는: 검토, 워크플로 및 작업.

## <a name="reviews"></a>검토

검토, 콘텐츠 검토 도구에 업로드 되 고 아래에 표시 됩니다는 **검토** 탭 합니다. 여기에서 사용자가 적용 된 태그를 변경 하 고 적절 하 게 자신의 사용자 지정 태그를 적용할 수 있습니다. 사용자가 검토를 전송 하는 경우 결과 지정 된 콜백 끝점으로 전송 됩니다 하 고 콘텐츠는 사이트에서 제거 됩니다.

![브라우저에서의 검토 탭에서 검토 도구 웹 사이트 열기](./Review-Tool-user-Guide/images/image-workflow-review.png)

참조를 [검토 도구 가이드](./review-tool-user-guide/review-moderated-images.md) 검토, 만들기를 시작 하거나 참조를 [REST API 가이드](./try-review-api-review.md) 를 프로그래밍 방식으로 수행 하는 방법에 알아봅니다.

## <a name="workflows"></a>Workflows

워크플로 콘텐츠에 대 한 클라우드 기반 사용자 지정 된 필터입니다. 워크플로 다양 한 가지 방법으로 콘텐츠를 필터링 하 고 적절 한 조치를 취할를 서비스에 연결할 수 있습니다. Content Moderator 커넥터를 사용 하 여 워크플로 수 자동으로 조정 태그를 적용 고 제출 된 콘텐츠를 사용 하 여 검토를 만듭니다.

### <a name="view-workflows"></a>워크플로 보기

기존 워크플로 보려면로 이동 합니다 [검토 도구](https://contentmoderator.cognitive.microsoft.com/) 선택한 **설정** > **워크플로**합니다.

![기본 워크플로](images/default-workflow-listed.PNG)

워크플로는 프로그래밍 방식으로 액세스할 수 있도록 JSON 문자열로 완전히 설명할 수 있습니다. 선택 하는 경우는 **편집** 워크플로에 대 한 옵션을 선택 합니다 **JSON** 탭 다음과 같은 JSON 식을 표시:

```json
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
```

참조를 [검토 도구 가이드](./review-tool-user-guide/workflows.md) 만들기 및 워크플로 사용 하 여 시작 하거나 참조를 [REST API 가이드](./try-review-api-workflow.md) 를 프로그래밍 방식으로 수행 하는 방법에 알아봅니다.

## <a name="jobs"></a>교육

조정 작업은 특정 유형의 콘텐츠 조정, 워크플로 및 검토의 기능에 대 한 래퍼 역할도합니다. 작업은 Content Moderator 이미지 조정 API 또는 텍스트 조정 API를 사용 하 여 콘텐츠를 검색 하 고 지정 된 워크플로에 대해 확인 합니다. 워크플로 결과 따라 그렇지의 콘텐츠에 대해 검토를 만들 수 없습니다는 [검토 도구](./review-tool-user-guide/human-in-the-loop.md)합니다. 검토 및 워크플로 모두 만들고 수는 해당 Api를 사용 하 여 구성, 하는 동안 작업 API를 사용 하면 (지정 된 콜백 끝점으로 전송 가능)는 전체 프로세스의 자세한 보고서를 가져올 수 있습니다.

참조 된 [REST API 가이드](./try-review-api-job.md) 작업을 사용 하 여 시작 하려면.

## <a name="next-steps"></a>다음 단계

* [작업 API 콘솔](try-review-api-job.md)을 시험 사용하고 REST API 코드 샘플을 사용합니다. Visual Studio 및 C#에 익숙한 경우 [작업 .NET 빠른 시작](moderation-jobs-quickstart-dotnet.md)을 확인하세요. 
* 검토의 경우 [검토 API 콘솔](try-review-api-review.md)로 시작하고 REST API 코드 샘플을 사용합니다. 그런 다음, [검토 .NET 빠른 시작](moderation-reviews-quickstart-dotnet.md)을 참조하세요.
* 비디오 검토의 경우 [비디오 검토 빠른 시작](video-reviews-quickstart-dotnet.md)을 사용하여 [비디오 검토에 전사를 추가](video-transcript-reviews-quickstart-dotnet.md)하는 방법을 알아봅니다.
