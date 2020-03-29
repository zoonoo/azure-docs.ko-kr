---
title: 리뷰, 워크플로 및 채용 컨셉 - 콘텐츠 중재자
titleSuffix: Azure Cognitive Services
description: 이 문서에서는 검토 도구의 핵심 개념에 대해 알아봅니다. 리뷰, 워크플로 및 작업을 할 수 있습니다.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 03/14/2019
ms.author: pafarley
ms.openlocfilehash: 1aba86efb9ea76fbf060e80b47f9f2f6cdf8ee71
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79221540"
---
# <a name="content-moderation-reviews-workflows-and-jobs"></a>콘텐츠 검토, 워크플로 및 작업

콘텐츠 중재자는 기계 지원 적당도와 휴먼-인-더-루프 기능을 결합하여 실제 시나리오에 대한 최적의 중재 프로세스를 만듭니다. 클라우드 기반 [검토 도구를](https://contentmoderator.cognitive.microsoft.com)통해 이 작업을 수행합니다. 이 가이드에서는 검토 도구의 핵심 개념인 리뷰, 워크플로 및 작업에 대해 알아봅니다.

## <a name="reviews"></a>검토

검토에서 콘텐츠가 검토 도구에 업로드되고 **검토** 탭 아래에 표시됩니다. 여기에서 사용자는 적용된 태그를 변경하고 자신의 사용자 지정 태그를 적절하게 적용할 수 있습니다. 사용자가 리뷰를 제출하면 결과가 지정된 콜백 끝점으로 전송되고 콘텐츠가 사이트에서 제거됩니다.

![브라우저에서 열려 있는 도구 웹 사이트 검토 탭에서 검토](./Review-Tool-user-Guide/images/image-workflow-review.png)

리뷰 [작성을](./review-tool-user-guide/review-moderated-images.md) 시작하려면 검토 도구 가이드를 참조하거나 [REST API 가이드를](./try-review-api-review.md) 참조하여 프로그래밍 방식으로 검토하는 방법을 알아보십시오.

## <a name="workflows"></a>워크플로

워크플로는 콘텐츠에 대한 클라우드 기반 사용자 지정 필터입니다. 워크플로는 다양한 서비스에 연결하여 다양한 방식으로 콘텐츠를 필터링한 다음 적절한 조치를 취할 수 있습니다. 콘텐츠 중재자 커넥터를 사용하면 워크플로가 자동으로 중재 태그를 적용하고 제출된 콘텐츠로 리뷰를 작성할 수 있습니다.

### <a name="view-workflows"></a>워크플로보기

기존 워크플로를 보려면 검토 [도구로](https://contentmoderator.cognitive.microsoft.com/) 이동하여 **설정** > **워크플로를 선택합니다.**

![기본 워크플로](images/default-workflow-listed.PNG)

워크플로는 완전히 JSON 문자열로 설명할 수 있어 프로그래밍 방식으로 액세스할 수 있습니다. 워크플로에 대한 **편집** 옵션을 선택한 다음 **JSON** 탭을 선택하면 다음과 같은 JSON 표현식이 표시됩니다.

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

워크플로 작성 및 사용을 시작하려면 [검토 도구 가이드를](./review-tool-user-guide/workflows.md) 참조하거나 [REST API 가이드를](./try-review-api-workflow.md) 참조하여 프로그래밍 방식으로 수행하는 방법을 알아봅니다.

## <a name="jobs"></a>작업

중재 작업은 콘텐츠 조정, 워크플로 및 리뷰의 기능을 위한 일종의 래퍼 역할을 합니다. 작업은 콘텐츠 중재자 이미지 중재 API 또는 텍스트 중재 API를 사용하여 콘텐츠를 검색한 다음 지정된 워크플로에 대해 확인합니다. 워크플로 결과에 따라 [검토 도구의](./review-tool-user-guide/human-in-the-loop.md)콘텐츠에 대한 검토를 만들 거나 만들지 않을 수 있습니다. 검토및 워크플로를 각각의 API로 만들고 구성할 수 있지만 작업 API를 사용하면 전체 프로세스에 대한 자세한 보고서를 얻을 수 있습니다(지정된 콜백 끝점으로 전송가능).

작업 사용을 시작하려면 [REST API 가이드를](./try-review-api-job.md) 참조하십시오.

## <a name="next-steps"></a>다음 단계

* [작업 API 콘솔](try-review-api-job.md)을 시험 사용하고 REST API 코드 샘플을 사용합니다. Visual Studio 및 C#에 익숙한 경우 [작업 .NET 빠른 시작](moderation-jobs-quickstart-dotnet.md)을 확인하세요. 
* 검토의 경우 [검토 API 콘솔](try-review-api-review.md)로 시작하고 REST API 코드 샘플을 사용합니다. 그런 다음 [.NET 빠른 시작의](dotnet-sdk-quickstart.md)리뷰 섹션을 참조하십시오.
* 비디오 검토의 경우 [비디오 검토 빠른 시작](video-reviews-quickstart-dotnet.md)을 사용하여 [비디오 검토에 전사를 추가](video-transcript-reviews-quickstart-dotnet.md)하는 방법을 알아봅니다.
