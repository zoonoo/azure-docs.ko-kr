---
title: Video Indexer API를 사용 하 여 브랜드 모델 사용자 지정
titleSuffix: Azure Media Services
description: Video Indexer API를 사용 하 여 브랜드 모델을 사용자 지정 하는 방법을 알아봅니다.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 01/14/2020
ms.author: kumud
ms.openlocfilehash: 5fc565ecc1b501f52e934784695594dcfef2a83a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87047291"
---
# <a name="customize-a-brands-model-with-the-video-indexer-api"></a>Video Indexer API를 사용하여 브랜드 모델 사용자 지정

Video Indexer는 비디오 및 오디오 콘텐츠의 인덱싱 및 재인덱싱 동안 연설 및 시각적 텍스트에서 브랜드를 검색하도록 지원합니다. 브랜드 검색 기능은 Bing의 브랜드 데이터베이스에서 제안하는 제품, 서비스 및 회사의 멘션을 식별합니다. 예를 들어 Microsoft가 비디오 또는 오디오 콘텐츠를 통해 표시 되거나 비디오의 시각적 텍스트에 표시 되는 경우 Video Indexer 콘텐츠에서 브랜드로 검색 합니다. 사용자 지정 브랜드 모델을 사용하면 특정 브랜드가 검색되지 않도록 제외하고, Bing 브랜드 데이터베이스에 없을 수 있으며 모델에 포함되어야 하는 브랜드를 포함할 수 있습니다.

자세한 개요를 보려면 [개요](customize-brands-model-overview.md)를 참조하세요.

Video Indexer API를 사용하여 이 항목에 설명된 것처럼 비디오에서 검색되는 사용자 지정 브랜드 모델을 생성, 사용 및 편집할 수 있습니다. [Video Indexer 웹 사이트를 사용하여 브랜드 모델 사용자 지정](customize-brands-model-with-api.md)에 설명된 대로 Video Indexer 웹 사이트를 사용할 수도 있습니다.

## <a name="create-a-brand"></a>브랜드 만들기

[브랜드 API 만들기](https://api-portal.videoindexer.ai/docs/services/operations/operations/Create-Brand) 는 새 사용자 지정 브랜드를 만들어 지정 된 계정에 대 한 사용자 지정 브랜드 모델에 추가 합니다.

> [!NOTE]
> `enabled`(본문에서)를 true로 설정 하면 검색을 위해 Video Indexer의 *포함* 목록에 해당 브랜드가 배치 됩니다. `enabled`을 false로 설정 하면 해당 브랜드가 *제외* 목록에 배치 되므로 Video Indexer는 검색 되지 않습니다.

본문에서 설정할 수 있는 다른 매개 변수는 다음과 같습니다.

* `referenceUrl`이 값은 해당 위키백과 페이지에 대 한 링크와 같은 브랜드의 모든 참조 웹 사이트 일 수 있습니다.
* `tags`값은 브랜드에 대 한 태그 목록입니다. 이 태그는 Video Indexer 웹 사이트의 브랜드 *범주* 필드에 표시 됩니다. 예를 들어 “Azure” 브랜드는 “클라우드”로 태그를 지정하거나 분류할 수 있습니다.

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

[Brand 삭제](https://api-portal.videoindexer.ai/docs/services/operations/operations/Delete-Brand?) API는 지정 된 계정에 대 한 사용자 지정 브랜드 모델에서 브랜드를 제거 합니다. 계정은 `accountId` 매개 변수에 지정 됩니다. 성공적으로 호출하면 브랜드가 더 이상 ‘포함’ 또는 ‘제외’ 브랜드 목록에 없습니다.****

### <a name="response"></a>응답

브랜드가 성공적으로 삭제 되 면 반환 된 콘텐츠가 없습니다.

## <a name="get-a-specific-brand"></a>특정 브랜드 가져오기

[브랜드 API 가져오기](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Brand?) 를 사용 하면 브랜드 ID를 사용 하 여 지정 된 계정에 대 한 사용자 지정 브랜드 모델의 브랜드 세부 정보를 검색할 수 있습니다.

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
> `enabled` 로 설정 하는 `true` 것은 Video Indexer 검색할 수 있도록 브랜드가 *포함* 목록에 있음을 나타내며 false 인 경우에는 `enabled` 해당 브랜드가 *제외* 목록에 있음을 의미 하므로 Video Indexer는 검색 하지 않습니다.

## <a name="update-a-specific-brand"></a>특정 브랜드 업데이트

[브랜드 업데이트](https://api-portal.videoindexer.ai/docs/services/operations/operations/Update-Brand?) API를 사용 하면 브랜드 ID를 사용 하 여 지정 된 계정에 대 한 사용자 지정 브랜드 모델의 브랜드 세부 정보를 검색할 수 있습니다.

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

[모든 브랜드 가져오기](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Brands?) API는 브랜드가 *포함* 또는 *제외* 브랜드 목록에 있는지 여부에 관계 없이 지정 된 계정에 대 한 사용자 지정 브랜드 모델의 모든 브랜드를 반환 합니다.

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
> 지정 된 브랜드 *예* 는 Video Indexer 검색에 대 한 *포함* 목록에 있으며, 이름이 *Example2* 인 브랜드는 *제외* 목록에 있으므로 Video Indexer는 검색 하지 않습니다.

## <a name="get-brands-model-settings"></a>브랜드 모델 설정 가져오기

[브랜드 설정 가져오기](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Brands) API는 지정 된 계정의 브랜드 모델 설정을 반환 합니다. 브랜드 모델 설정은 Bing 브랜드 데이터베이스에서 검색을 사용할지 여부를 나타냅니다. Bing 브랜드를 사용 하도록 설정 하지 않은 경우 Video Indexer는 지정 된 계정의 사용자 지정 브랜드 모델 에서만 브랜드를 검색 합니다.

### <a name="response"></a>응답

응답은 Bing 브랜드의 사용 여부를 아래 예제 형식으로 표시합니다.

```json
{
  "state": true,
  "useBuiltIn": true
}
```

> [!NOTE]
> `useBuiltIn` true로 설정 하면 Bing 브랜드가 사용 됨을 나타냅니다. `useBuiltin`이 false 이면 Bing 브랜드가 사용 되지 않습니다. `state`이 값은 더 이상 사용 되지 않으므로 무시할 수 있습니다.

## <a name="update-brands-model-settings"></a>브랜드 모델 설정 업데이트

[업데이트 브랜드](https://api-portal.videoindexer.ai/docs/services/operations/operations/Update-Brands-Model-Settings?) API는 지정 된 계정의 브랜드 모델 설정을 업데이트 합니다. 브랜드 모델 설정은 Bing 브랜드 데이터베이스에서 검색을 사용할지 여부를 나타냅니다. Bing 브랜드를 사용 하도록 설정 하지 않은 경우 Video Indexer는 지정 된 계정의 사용자 지정 브랜드 모델 에서만 브랜드를 검색 합니다.

`useBuiltIn`True로 설정 된 플래그는 Bing 브랜드가 사용 됨을 의미 합니다. `useBuiltin`이 false 이면 Bing 브랜드가 사용 되지 않습니다.

### <a name="response"></a>응답

브랜드 모델 설정이 성공적으로 업데이트 되 면 반환 된 콘텐츠가 없습니다.

## <a name="next-steps"></a>다음 단계

[웹 사이트를 사용하여 브랜드 모델 사용자 지정](customize-brands-model-with-website.md)
