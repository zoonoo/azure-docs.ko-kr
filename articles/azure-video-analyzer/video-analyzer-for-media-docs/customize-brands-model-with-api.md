---
title: Azure Video Analyzer for Media(이전의 Video Indexer) API를 통해 브랜드 모델 사용자 지정
titleSuffix: Azure Media Services
description: Azure Video Analyzer for Media(이전의 Video Indexer) API를 통해 브랜드 모델을 사용자 지정하는 방법을 알아봅니다.
services: media-services
author: anikaz
manager: johndeu
ms.topic: article
ms.date: 01/14/2020
ms.author: kumud
ms.openlocfilehash: 3f3e2356a9c9101763f9b944993b9a96b22b8a0e
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/25/2021
ms.locfileid: "110386691"
---
# <a name="customize-a-brands-model-with-the-video-analyzer-for-media-api"></a>Video Analyzer for Media API로 브랜드 모델 사용자 지정

Azure Video Analyzer for Media(이전의 Video Indexer)는 비디오 및 오디오 콘텐츠의 인덱싱 및 재인덱싱 동안 연설 및 시각적 텍스트에서 브랜드를 검색하도록 지원합니다. 브랜드 검색 기능은 Bing의 브랜드 데이터베이스에서 제안하는 제품, 서비스 및 회사의 멘션을 식별합니다. 예를 들어 Microsoft가 비디오 또는 오디오 콘텐츠에 멘션되거나, 비디오의 시각적 텍스트에 표시되는 경우 Video Analyzer for Media는 콘텐츠에서 해당 항목을 브랜드로 검색합니다. 사용자 지정 브랜드 모델을 사용하면 특정 브랜드가 검색되지 않도록 제외하고, Bing 브랜드 데이터베이스에 없을 수 있으며 모델에 포함되어야 하는 브랜드를 포함할 수 있습니다. 자세한 내용은 [개요](customize-brands-model-overview.md)를 참조하세요.

> [!NOTE]
> 브랜드를 추가하기에 앞서 비디오가 인덱싱된 경우 다시 인덱싱해야 합니다.

Video Analyzer for Media API를 사용하여 이 항목에 설명된 것처럼 비디오에서 검색되는 사용자 지정 브랜드 모델을 생성, 사용 및 편집할 수 있습니다. [Video Analyzer for Media 웹 사이트를 사용하여 브랜드 모델 사용자 지정](customize-brands-model-with-api.md)에 설명된 대로 Video Analyzer for Media 웹 사이트를 사용할 수도 있습니다.

## <a name="create-a-brand"></a>브랜드 만들기

[브랜드 만들기](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Create-Brand) API는 새 사용자 지정 브랜드를 만들어 지정된 계정의 사용자 지정 브랜드 모델에 추가합니다.

> [!NOTE]
> `enabled`(본문 내)를 true로 설정하면 Video Analyzer for Media에서 검색할 *포함* 목록에 브랜드가 배치됩니다. `enabled`를 false로 설정하면 브랜드가 *제외* 목록에 배치되므로 Video Analyzer for Media에서 검색하지 않습니다.

다음은 본문에서 설정할 수 있는 몇 가지 다른 매개 변수입니다.

* `referenceUrl` 값은 해당 Wikipedia 페이지 링크와 같은 브랜드에 대한 참조 웹 사이트일 수 있습니다.
* `tags` 값은 브랜드의 태그 목록입니다. 이 태그는 Video Analyzer for Media 웹 사이트에 있는 브랜드의 *범주* 필드에 표시됩니다. 예를 들어 “Azure” 브랜드는 “클라우드”로 태그를 지정하거나 분류할 수 있습니다.

### <a name="response"></a>응답

응답은 방금 만든 브랜드에 대한 정보를 아래 예제 형식으로 제공합니다.

```json
{
  "referenceUrl": "https://en.wikipedia.org/wiki/Example",
  "id": 97974,
  "name": "Example",
  "accountId": "SampleAccountId",
  "lastModifierUserName": "SampleUserName",
  "created": "2018-04-25T14:59:52.7433333",
  "lastModified": "2018-04-25T14:59:52.7433333",
  "enabled": true,
  "description": "This is an example",
  "tags": [
    "Tag1",
    "Tag2"
  ]
}
```

## <a name="delete-a-brand"></a>브랜드 삭제

[브랜드 삭제](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Delete-Brand) API는 지정된 계정에 대해 사용자 지정 브랜드 모델에서 브랜드를 제거합니다. 계정은 `accountId` 매개 변수에서 지정됩니다. 성공적으로 호출하면 브랜드가 더 이상 ‘포함’ 또는 ‘제외’ 브랜드 목록에 없습니다.

### <a name="response"></a>응답

브랜드를 성공적으로 삭제한 경우 반환되는 콘텐츠가 없습니다.

## <a name="get-a-specific-brand"></a>특정 브랜드 가져오기

[브랜드 가져오기](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Get-Brand) API를 사용하면 브랜드 ID를 통해 지정된 계정의 사용자 지정 브랜드 모델에서 브랜드 세부 정보를 검색할 수 있습니다.

### <a name="response"></a>응답

응답은 브랜드 ID를 사용하여 검색한 브랜드에 대한 정보를 아래 예제 형식으로 제공합니다.

```json
{
  "referenceUrl": "https://en.wikipedia.org/wiki/Example",
  "id": 128846,
  "name": "Example",
  "accountId": "SampleAccountId",
  "lastModifierUserName": "SampleUserName",
  "created": "2018-01-06T13:51:38.3666667",
  "lastModified": "2018-01-11T13:51:38.3666667",
  "enabled": true,
  "description": "This is an example",
  "tags": [
    "Tag1",
    "Tag2"
  ]
}
```

> [!NOTE]
> `enabled`를 `true`로 설정하면 브랜드가 Video Analyzer for Media에서 검색할 *포함* 목록에 배치되고, `enabled`를 false로 설정하면 브랜드가 *제외* 목록에 배치되어 Video Analyzer for Media에서 검색하지 않습니다.

## <a name="update-a-specific-brand"></a>특정 브랜드 업데이트

[브랜드 업데이트](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Update-Brand) API를 사용하면 브랜드 ID를 통해 지정된 계정의 사용자 지정 브랜드 모델에서 브랜드 세부 정보를 검색할 수 있습니다.

### <a name="response"></a>응답

응답은 업데이트한 브랜드에 대한 업데이트된 정보를 아래 예제 형식으로 제공합니다.

```json
{
  "referenceUrl": null,
  "id": 97974,
  "name": "Example",
  "accountId": "SampleAccountId",
  "lastModifierUserName": "SampleUserName",
  "Created": "2018-04-25T14:59:52.7433333",
  "lastModified": "2018-04-25T15:37:50.67",
  "enabled": false,
  "description": "This is an update example",
  "tags": [
    "Tag1",
    "NewTag2"
  ]
}
```

## <a name="get-all-of-the-brands"></a>모든 브랜드 가져오기

[모든 브랜드 가져오기](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Get-Brands) API를 사용하면 브랜드가 *포함* 또는 *제외* 브랜드 목록에 배치되는지에 관계없이 지정된 계정의 사용자 지정 브랜드 모델에 있는 모든 브랜드가 반환됩니다.

### <a name="response"></a>응답

응답은 계정의 모든 브랜드 목록과 각 세부 정보를 아래 예제의 형식으로 제공합니다.

```json
[
    {
        "ReferenceUrl": null,
        "id": 97974,
        "name": "Example",
        "accountId": "AccountId",
        "lastModifierUserName": "UserName",
        "Created": "2018-04-25T14:59:52.7433333",
        "LastModified": "2018-04-25T14:59:52.7433333",
        "enabled": true,
        "description": "This is an example",
        "tags": ["Tag1", "Tag2"]
    },
    {
        "ReferenceUrl": null,
        "id": 97975,
        "name": "Example2",
        "accountId": "AccountId",
        "lastModifierUserName": "UserName",
        "Created": "2018-04-26T14:59:52.7433333",
        "LastModified": "2018-04-26T14:59:52.7433333",
        "enabled": false,
        "description": "This is another example",
        "tags": ["Tag1", "Tag2"]
    },
]
```

> [!NOTE]
> *Example* 브랜드는 Video Analyzer for Media에서 검색할 *포함* 목록에 있고, *Example2* 브랜드는 *제외* 목록에 있으므로 Video Analyzer for Media에서 검색하지 않습니다.

## <a name="get-brands-model-settings"></a>브랜드 모델 설정 가져오기

[브랜드 설정 가져오기](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Get-Brands) API는 지정된 계정의 브랜드 모델 설정을 반환합니다. 브랜드 모델 설정은 Bing 브랜드 데이터베이스에서 검색을 사용할지 여부를 나타냅니다. Bing 브랜드를 사용하도록 설정하지 않은 경우 Video Analyzer for Media는 지정된 계정의 사용자 지정 브랜드 모델에서 브랜드를 검색만 합니다.

### <a name="response"></a>응답

응답은 Bing 브랜드의 사용 여부를 아래 예제 형식으로 표시합니다.

```json
{
  "state": true,
  "useBuiltIn": true
}
```

> [!NOTE]
> `useBuiltIn`을 true로 설정하면 Bing 브랜드가 사용됩니다. `useBuiltin`이 false인 경우 Bing 브랜드가 사용하지 않도록 설정됩니다. `state` 값은 사용되지 않았으므로 무시해도 됩니다.

## <a name="update-brands-model-settings"></a>브랜드 모델 설정 업데이트

[브랜드 업데이트](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Update-Brands-Model-Settings) API는 지정된 계정의 브랜드 모델 설정을 업데이트합니다. 브랜드 모델 설정은 Bing 브랜드 데이터베이스에서 검색을 사용할지 여부를 나타냅니다. Bing 브랜드를 사용하도록 설정하지 않은 경우 Video Analyzer for Media는 지정된 계정의 사용자 지정 브랜드 모델에서 브랜드를 검색만 합니다.

True로 설정된 `useBuiltIn` 플래그는 Bing 브랜드를 사용하도록 설정되어 있음을 의미합니다. `useBuiltin`이 false인 경우 Bing 브랜드가 사용하지 않도록 설정됩니다.

### <a name="response"></a>응답

브랜드 모델 설정이 성공적으로 업데이트되었을 때는 반환되는 콘텐츠가 없습니다.

## <a name="next-steps"></a>다음 단계

[웹 사이트를 사용하여 브랜드 모델 사용자 지정](customize-brands-model-with-website.md)
