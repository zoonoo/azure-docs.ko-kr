---
title: 비디오 인덱서 API로 브랜드 모델 사용자 지정
titleSuffix: Azure Media Services
description: 비디오 인덱서 API를 사용하여 브랜드 모델을 사용자 지정하는 방법을 알아봅니다.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 01/14/2020
ms.author: anzaman
ms.openlocfilehash: 79c3a7934e9152a4908f895c20ee6fbdc0f360cf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80128001"
---
# <a name="customize-a-brands-model-with-the-video-indexer-api"></a>Video Indexer API를 사용하여 브랜드 모델 사용자 지정

Video Indexer는 비디오 및 오디오 콘텐츠의 인덱싱 및 재인덱싱 동안 연설 및 시각적 텍스트에서 브랜드를 검색하도록 지원합니다. 브랜드 검색 기능은 Bing의 브랜드 데이터베이스에서 제안하는 제품, 서비스 및 회사의 멘션을 식별합니다. 예를 들어 Microsoft가 비디오 또는 오디오 콘텐츠에 언급되어 있거나 비디오의 시각적 텍스트에 표시되는 경우 비디오 인덱서가 콘텐츠의 브랜드로 검색합니다. 사용자 지정 브랜드 모델을 사용하면 특정 브랜드가 검색되지 않도록 제외하고, Bing 브랜드 데이터베이스에 없을 수 있으며 모델에 포함되어야 하는 브랜드를 포함할 수 있습니다.

자세한 개요를 보려면 [개요](customize-brands-model-overview.md)를 참조하세요.

Video Indexer API를 사용하여 이 항목에 설명된 것처럼 비디오에서 검색되는 사용자 지정 브랜드 모델을 생성, 사용 및 편집할 수 있습니다. [Video Indexer 웹 사이트를 사용하여 브랜드 모델 사용자 지정](customize-brands-model-with-api.md)에 설명된 대로 Video Indexer 웹 사이트를 사용할 수도 있습니다.

## <a name="create-a-brand"></a>브랜드 만들기

[브랜드 만들기](https://api-portal.videoindexer.ai/docs/services/operations/operations/Create-Brand) API는 새 사용자 지정 브랜드를 만들고 지정된 계정의 사용자 지정 브랜드 모델에 추가합니다.

> [!NOTE]
> 본체에서 true로 설정하면 `enabled` 브랜드가 비디오 인덱서의 *포함* 목록에 표시됩니다. 거짓으로 설정하면 `enabled` 브랜드가 *제외* 목록에 포함되므로 동영상 인덱서에서 브랜드를 감지하지 못합니다.

본문에서 설정할 수 있는 몇 가지 다른 매개 변수:

* 이 `referenceUrl` 값은 위키백과 페이지에 대한 링크와 같은 브랜드에 대한 모든 참조 웹 사이트일 수 있습니다.
* 값은 `tags` 브랜드의 태그 목록입니다. 이 태그는 동영상 인덱서 웹사이트의 브랜드 *카테고리* 필드에 표시됩니다. 예를 들어 “Azure” 브랜드는 “클라우드”로 태그를 지정하거나 분류할 수 있습니다.

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

[브랜드 API 삭제는](https://api-portal.videoindexer.ai/docs/services/operations/operations/Delete-Brand?) 지정된 계정의 사용자 지정 브랜드 모델에서 브랜드를 제거합니다. 계정은 매개 변수에 `accountId` 지정됩니다. 성공적으로 호출하면 브랜드가 더 이상 ‘포함’ 또는 ‘제외’ 브랜드 목록에 없습니다.****

### <a name="response"></a>응답

브랜드가 성공적으로 삭제되면 반환된 콘텐츠가 없습니다.

## <a name="get-a-specific-brand"></a>특정 브랜드 가져오기

브랜드 API 를 가져오기 를 사용하면 [브랜드 ID를](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Brand?) 사용하여 지정된 계정에 대한 사용자 지정 브랜드 모델에서 브랜드의 세부 정보를 검색할 수 있습니다.

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
> `enabled`브랜드가 `true` 비디오 인덱서에 대한 *포함* 목록에 있음을 나타내고, `enabled` 브랜드가 *제외* 목록에 있음을 잘못 표시하므로 비디오 인덱서가 이를 감지하지 못합니다.

## <a name="update-a-specific-brand"></a>특정 브랜드 업데이트

브랜드 API [업데이트를](https://api-portal.videoindexer.ai/docs/services/operations/operations/Update-Brand?) 통해 브랜드 ID를 사용하여 지정된 계정에 대한 사용자 지정 브랜드 모델에서 브랜드의 세부 정보를 검색할 수 있습니다.

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

[Get 모든 브랜드](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Brands?) API는 브랜드가 브랜드 *포함* 또는 *제외* 목록에 포함되는지 여부에 관계없이 지정된 계정에 대한 사용자 지정 브랜드 모델의 모든 브랜드를 반환합니다.

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
> *예제라는* 브랜드는 검색할 비디오 인덱서의 *포함* 목록에 있고 *Example2라는* 브랜드는 *제외* 목록에 있으므로 비디오 인덱서가 이를 감지하지 못합니다.

## <a name="get-brands-model-settings"></a>브랜드 모델 설정 가져오기

[브랜드 설정](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Brands) API는 지정된 계정의 브랜드 모델 설정을 반환합니다. 브랜드 모델 설정은 Bing 브랜드 데이터베이스에서 검색을 사용할지 여부를 나타냅니다. Bing 브랜드를 사용하도록 설정하지 않으면 비디오 인덱서에서는 지정된 계정의 맞춤 브랜드 모델에서만 브랜드를 검색합니다.

### <a name="response"></a>응답

응답은 Bing 브랜드의 사용 여부를 아래 예제 형식으로 표시합니다.

```json
{
  "state": true,
  "useBuiltIn": true
}
```

> [!NOTE]
> `useBuiltIn`true로 설정되려면 Bing 브랜드가 활성화되어 있음을 나타냅니다. 거짓인 경우 `useBuiltin` Bing 브랜드는 사용할 수 없습니다. `state` 더 이상 사용되지 않으므로 값을 무시할 수 있습니다.

## <a name="update-brands-model-settings"></a>브랜드 모델 설정 업데이트

[업데이트 브랜드](https://api-portal.videoindexer.ai/docs/services/operations/operations/Update-Brands-Model-Settings?) API는 지정된 계정의 브랜드 모델 설정을 업데이트합니다. 브랜드 모델 설정은 Bing 브랜드 데이터베이스에서 검색을 사용할지 여부를 나타냅니다. Bing 브랜드를 사용하도록 설정하지 않으면 비디오 인덱서에서는 지정된 계정의 맞춤 브랜드 모델에서만 브랜드를 검색합니다.

true로 설정된 플래그는 `useBuiltIn` Bing 브랜드가 활성화되어 있음을 의미합니다. 거짓인 경우 `useBuiltin` Bing 브랜드는 사용할 수 없습니다.

### <a name="response"></a>응답

브랜드 모델 설정이 성공적으로 업데이트되면 반환되는 콘텐츠가 없습니다.

## <a name="next-steps"></a>다음 단계

[웹 사이트를 사용하여 브랜드 모델 사용자 지정](customize-brands-model-with-website.md)
