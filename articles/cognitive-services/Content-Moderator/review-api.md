---
title: 검토, 워크플로, 작업의 개념 - Content Moderator
titleSuffix: Azure Cognitive Services
description: 이 문서에서는 검토 도구의 핵심 개념에 대해 설명합니다(검토, 워크플로 및 작업).
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 03/14/2019
ms.author: pafarley
ms.openlocfilehash: c43cc081316a26065143640a29cebdb90177ebc7
ms.sourcegitcommit: c05e595b9f2dbe78e657fed2eb75c8fe511610e7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/11/2021
ms.locfileid: "112031640"
---
# <a name="content-moderation-reviews-workflows-and-jobs"></a>콘텐츠 조정 검토, 워크플로 및 작업

[!INCLUDE [deprecation notice](includes/tool-deprecation.md)]

Content Moderator는 머신 지원 조정과 휴먼 참여형 기능을 결합하여 실제 시나리오에 대한 최적의 조정 프로세스를 만듭니다. 클라우드 기반의 [검토 도구](https://contentmoderator.cognitive.microsoft.com)를 통해 이를 수행합니다. 이 가이드에서는 검토 도구의 핵심 개념에 대해 알아봅니다(검토, 워크플로 및 작업).

## <a name="reviews"></a>검토

검토에서 콘텐츠는 검토 도구에 업로드됩니다. 대시보드의 **검토** 탭 아래에서 해당 콘텐츠 형식을 클릭하여 볼 수 있습니다. 검토 화면에서 적용된 태그를 변경하고 사용자 지정 태그를 적절하게 적용할 수 있습니다. 검토를 제출할 때 결과가 지정된 콜백 엔드포인트로 전송되고 사이트에서 콘텐츠가 제거됩니다.

> [!div class="mx-imgBorder"]
> ![검토 드롭다운 메뉴가 강조 표시됩니다. 콘텐츠 형식이 표시됩니다(이미지, 텍스트 및 비디오).](./Review-Tool-user-Guide/images/review-tab.png)

### <a name="manage-reviews"></a>검토 관리

대시보드에서 **관리자** -> **검토 관리** 로 이동하여 관리자 화면을 봅니다. 여기에서 모든 검토(보류 중 및 완료)의 목록을 볼 수 있습니다.

각 검토에서 3개의 점으로 된 **작업** 단추를 사용하면 검토 화면으로 이동하거나 해당 검토의 기록을 검사할 수 있습니다.

> [!div class="mx-imgBorder"]
> ![검토 화면의 검토 도구 웹 사이트](./Review-Tool-user-Guide/images/manage-reviews.png)

**검색** 도구 모음을 사용하여 검토 상태, 태그, 콘텐츠 형식, 하위 팀, 할당된 사용자, 생성/수정 날짜 등의 다양한 범주로 검토를 정렬할 수 있습니다.

> [!div class="mx-imgBorder"]
> ![검색 도구 모음이 표시됩니다. 여기에는 검토 상태 및 태그와 같은 검색 조건을 입력하는 다양한 콤보 상자가 있습니다.](./Review-Tool-user-Guide/images/review-search.png)

[검토 도구 가이드](./review-tool-user-guide/review-moderated-images.md)를 참조하여 검토를 만들기 시작하거나 [API 콘솔 가이드](./try-review-api-review.md)를 참조하여 프로그래밍 방식으로 이 작업을 수행하는 방법을 알아봅니다.

## <a name="workflows"></a>워크플로

워크플로는 콘텐츠에 대한 클라우드 기반의 사용자 지정 필터입니다. 워크플로는 여러 가지 서비스에 연결하여 다양한 방식으로 콘텐츠를 필터링한 다음 적절한 조치를 취할 수 있습니다. Content Moderator 커넥터를 사용하여 워크플로는 자동으로 조정 태그를 적용하고 제출된 콘텐츠로 검토를 만들 수 있습니다.

### <a name="view-workflows"></a>워크플로 보기

기존 워크플로를 보려면 [검토 도구](https://contentmoderator.cognitive.microsoft.com/)로 이동하여 **관리자** > **워크플로** 를 선택합니다.

> [!div class="mx-imgBorder"]
> ![기본 워크플로](images/default-workflow-list.png)

워크플로는 JSON 문자열로 정의되어 프로그래밍 방식으로 액세스할 수 있습니다. 워크플로에 대해 **편집** 옵션을 선택한 다음 **JSON** 탭을 선택하면 다음과 같은 JSON 식이 표시됩니다.

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

[검토 도구 가이드](./review-tool-user-guide/workflows.md)를 참조하여 워크플로를 만들고 사용하기 시작하거나 [API 콘솔 가이드](./try-review-api-workflow.md)를 참조하여 프로그래밍 방식으로 이 작업을 수행하는 방법을 알아봅니다.

## <a name="jobs"></a>작업

조정 작업은 콘텐츠 조정, 워크플로, 검토 기능에 대한 일종의 래퍼 역할을 합니다. 작업은 Content Moderator 이미지 조정 API 또는 텍스트 조정 API를 사용하여 콘텐츠를 검사한 다음 지정된 워크플로에 대하여 확인합니다. 워크플로 결과에 따라 [검토 도구](./review-tool-user-guide/human-in-the-loop.md)에 콘텐츠에 대한 검토가 만들어지거나 만들어지지 않을 수 있습니다. 검토와 워크플로 모두 각각의 해당 API를 사용하여 만들고 구성할 수 있지만 작업 API를 사용하면 지정된 콜백 엔드포인트에 보낼 수 있는 전체 프로세스에 대한 자세한 보고서를 가져올 수 있습니다.

작업을 사용하기 시작하려면 [API 콘솔 가이드](./try-review-api-job.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

* [작업 API 콘솔](try-review-api-job.md)을 시험 사용하고 REST API 코드 샘플을 사용합니다. Visual Studio 및 C#에 익숙한 경우 [작업 .NET 빠른 시작](moderation-jobs-quickstart-dotnet.md)을 확인하세요. 
* 검토의 경우 [검토 API 콘솔](try-review-api-review.md)로 시작하고 REST API 코드 샘플을 사용합니다. 그런 다음 [.NET 빠른 시작](./client-libraries.md?pivots=programming-language-csharp%253fpivots%253dprogramming-language-csharp)의 검토 섹션을 참조하세요.
* 비디오 검토의 경우 [비디오 검토 빠른 시작](video-reviews-quickstart-dotnet.md)을 사용하여 [비디오 검토에 전사를 추가](video-transcript-reviews-quickstart-dotnet.md)하는 방법을 알아봅니다.