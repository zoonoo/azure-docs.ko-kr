---
title: Video Indexer API를 사용 하 여 언어 모델 사용자 지정
titlesuffix: Azure Media Services
description: Video Indexer API를 사용 하 여 언어 모델을 사용자 지정 하는 방법을 알아봅니다.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 02/04/2020
ms.author: kumud
ms.openlocfilehash: f373afae03357ffb65eb459f806fe441e29b21b9
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87047077"
---
# <a name="customize-a-language-model-with-the-video-indexer-api"></a>Video Indexer API를 사용 하 여 언어 모델 사용자 지정

Video Indexer를 사용하면 엔진을 적응시킬 어휘가 포함된 도메인의 텍스트인 적응 텍스트를 업로드하여 음성 인식을 사용자 지정하기 위한 사용자 지정 언어 모델을 만들 수 있습니다. 모델을 학습하면 적응 텍스트에 나타나는 새 단어가 인식됩니다.

사용자 지정 언어 모델의 자세한 개요와 모범 사례에 대해서는 [Video Indexer로 언어 모델 사용자 지정](customize-language-model-overview.md)을 참조하세요.

이 항목에 설명된 대로 Video Indexer API를 사용하여 계정에서 사용자 지정 언어 모델을 만들고 편집할 수 있습니다. [Video Indexer 웹 사이트를 사용하여 언어 모델 사용자 지정](customize-language-model-with-api.md)에 설명된 대로 웹 사이트를 사용할 수도 있습니다.

## <a name="create-a-language-model"></a>언어 모델 만들기

[언어 모델 만들기](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Create-Language-Model?) API는 지정 된 계정에 새 사용자 지정 언어 모델을 만듭니다. 이 호출에서 언어 모델에 대한 파일을 업로드할 수 있습니다. 또는 여기에서 언어 모델을 만들고, 나중에 해당 언어 모델을 업데이트하여 모델에 대한 파일을 업로드할 수 있습니다.

> [!NOTE]
> 모델이 파일 내용을 파악하려면 활성화된 파일을 사용하여 모델을 학습해야 합니다. 언어 학습 지침은 다음 섹션에 나와 있습니다.

언어 모델에 추가할 파일을 업로드 하려면 위의 필수 매개 변수에 대 한 값을 제공 하는 것 외에도 FormData를 사용 하 여 본문의 파일을 업로드 해야 합니다. 이 작업을 수행 하는 방법에는 두 가지가 있습니다.

* 키는 파일 이름과 값이 txt 파일이 됩니다.
* 키는 파일 이름이 고 값은 txt 파일의 URL이 됩니다.

### <a name="response"></a>응답

응답은 새로 만든 언어 모델의 메타 데이터와이 예제 JSON 출력의 형식에 따라 각 모델의 파일에 있는 메타 데이터를 제공 합니다.

```json
{
    "id": "dfae5745-6f1d-4edd-b224-42e1ab57a891",
    "name": "TestModel",
    "language": "En-US",
    "state": "None",
    "languageModelId": "00000000-0000-0000-0000-000000000000",
    "files": [
    {
        "id": "25be7c0e-b6a6-4f48-b981-497e920a0bc9",
        "name": "hellofile",
        "enable": true,
        "creator": "John Doe",
        "creationTime": "2018-04-28T11:55:34.6733333"
    },
    {
        "id": "33025f5b-2354-485e-a50c-4e6b76345ca7",
        "name": "worldfile",
        "enable": true,
        "creator": "John Doe",
        "creationTime": "2018-04-28T11:55:34.86"
    }
    ]
}

```

## <a name="train-a-language-model"></a>언어 모델 학습

[언어 모델 학습](https://api-portal.videoindexer.ai/docs/services/operations/operations/Train-Language-Model?&pattern=train) API는 언어 모델에서 업로드 및 사용 하도록 설정 된 파일의 내용으로 지정 된 계정의 사용자 지정 언어 모델을 학습 합니다.

> [!NOTE]
> 먼저 언어 모델을 생성한 후 해당 파일을 업로드해야 합니다. 언어 모델을 만들거나 언어 모델을 업데이트 하 여 파일을 업로드할 수 있습니다.

### <a name="response"></a>응답

응답은 새로 학습 된 언어 모델의 메타 데이터와이 예제 JSON 출력의 형식에 따라 각 모델의 파일에 있는 메타 데이터를 제공 합니다.

```json
{
    "id": "41464adf-e432-42b1-8e09-f52905d7e29d",
    "name": "TestModel",
    "language": "En-US",
    "state": "Waiting",
    "languageModelId": "531e5745-681d-4e1d-b124-12e5ab57a891",
    "files": [
    {
        "id": "84fcf1ac-1952-48f3-b372-18f768eedf83",
        "name": "RenamedFile",
        "enable": false,
        "creator": "John Doe",
        "creationTime": "2018-04-27T20:10:10.5233333"
    },
    {
        "id": "9ac35b4b-1381-49c4-9fe4-8234bfdd0f50",
        "name": "hellofile",
        "enable": true,
        "creator": "John Doe",
        "creationTime": "2018-04-27T20:10:10.68"
    }
    ]
}
```

반환 되는는 `id` 언어 모델을 구별 하는 데 사용 되는 고유 ID 이며,는 비디오 api를 인덱싱하고 `languageModelId` [인덱스를 재 인덱싱하](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?) 는 데 사용 됩니다 Video Indexer ( [reindexing a video](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-index-video?) `linguisticModelId` api 업로드/인덱스 재 인덱싱).

## <a name="delete-a-language-model"></a>언어 모델 삭제

[언어 모델 삭제](https://api-portal.videoindexer.ai/docs/services/operations/operations/Delete-Language-Model?&pattern=delete) API는 지정 된 계정에서 사용자 지정 언어 모델을 삭제 합니다. 삭제 된 언어 모델을 사용 하 던 비디오는 비디오를 다시 인덱싱할 때까지 동일한 인덱스를 유지 합니다. 비디오를 재 인덱싱 하는 경우 비디오에 새 언어 모델을 할당할 수 있습니다. 그렇지 않으면 Video Indexer는 기본 모델을 사용 하 여 비디오를 인덱스를 인덱싱합니다.

### <a name="response"></a>응답

언어 모델을 성공적으로 삭제 하면 반환 된 콘텐츠가 없습니다.

## <a name="update-a-language-model"></a>언어 모델 업데이트

[언어 모델 업데이트](https://api-portal.videoindexer.ai/docs/services/operations/operations/Update-Language-Model?&pattern=update) API는 지정 된 계정에서 사용자 지정 언어 사용자 모델을 업데이트 합니다.

> [!NOTE]
> 언어 모델을 미리 만들었어야 합니다. 이 호출을 사용하여 해당 모델 내의 모든 파일을 사용하거나 사용하지 않도록 설정하고, 언어 모델의 이름을 업데이트하고, 언어 모델에 추가할 파일을 업로드할 수 있습니다.

언어 모델에 추가할 파일을 업로드 하려면 위의 필수 매개 변수에 대 한 값을 제공 하는 것 외에도 FormData를 사용 하 여 본문의 파일을 업로드 해야 합니다. 이 작업을 수행 하는 방법에는 두 가지가 있습니다.

* 키는 파일 이름과 값이 txt 파일이 됩니다.
* 키는 파일 이름이 고 값은 txt 파일의 URL이 됩니다.

### <a name="response"></a>응답

응답은 새로 학습 된 언어 모델의 메타 데이터와이 예제 JSON 출력의 형식에 따라 각 모델의 파일에 있는 메타 데이터를 제공 합니다.

```json
{
    "id": "41464adf-e432-42b1-8e09-f52905d7e29d",
    "name": "TestModel",
    "language": "En-US",
    "state": "Waiting",
    "languageModelId": "531e5745-681d-4e1d-b124-12e5ab57a891",
    "files": [
    {
        "id": "84fcf1ac-1952-48f3-b372-18f768eedf83",
        "name": "RenamedFile",
        "enable": true,
        "creator": "John Doe",
        "creationTime": "2018-04-27T20:10:10.5233333"
    },
    {
        "id": "9ac35b4b-1381-49c4-9fe4-8234bfdd0f50",
        "name": "hellofile",
        "enable": true,
        "creator": "John Doe",
        "creationTime": "2018-04-27T20:10:10.68"
    }
    ]
}
```

`id`응답에 반환 된 파일의를 사용 하 여 파일의 내용을 다운로드 합니다.

## <a name="update-a-file-from-a-language-model"></a>언어 모델에서 파일 업데이트

[파일 업데이트](https://api-portal.videoindexer.ai/docs/services/operations/operations/Update-Language-Model-file?&pattern=update) 를 사용 하면 `enable` 지정 된 계정의 사용자 지정 언어 모델에 있는 파일의 이름 및 상태를 업데이트할 수 있습니다.

### <a name="response"></a>응답

응답은 아래의 예제 JSON 출력 형식에 따라 업데이트한 파일의 메타데이터를 제공합니다.

```json
{
  "id": "84fcf1ac-1952-48f3-b372-18f768eedf83",
  "name": "RenamedFile",
  "enable": false,
  "creator": "John Doe",
  "creationTime": "2018-04-27T20:10:10.5233333"
}
```

`id`응답에서 반환 된 파일의를 사용 하 여 파일의 내용을 다운로드 합니다.

## <a name="get-a-specific-language-model"></a>특정 언어 모델 가져오기

[Get](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Language-Model?&pattern=get) API는 언어 및 언어 모델에 있는 파일과 같은 지정 된 계정에 지정 된 언어 모델에 대 한 정보를 반환 합니다.

### <a name="response"></a>응답

응답은이 예제 JSON 출력의 형식에 따라 각 모델의 파일에 있는 메타 데이터와 함께 지정 된 언어 모델의 메타 데이터를 제공 합니다.

```json
{
    "id": "dfae5745-6f1d-4edd-b224-42e1ab57a891",
    "name": "TestModel",
    "language": "En-US",
    "state": "None",
    "languageModelId": "00000000-0000-0000-0000-000000000000",
    "files": [
    {
        "id": "25be7c0e-b6a6-4f48-b981-497e920a0bc9",
        "name": "hellofile",
        "enable": true,
        "creator": "John Doe",
        "creationTime": "2018-04-28T11:55:34.6733333"
    },
    {
        "id": "33025f5b-2354-485e-a50c-4e6b76345ca7",
        "name": "worldfile",
        "enable": true,
        "creator": "John Doe",
        "creationTime": "2018-04-28T11:55:34.86"
    }
    ]
}
```

`id`응답에서 반환 된 파일의를 사용 하 여 파일의 내용을 다운로드 합니다.

## <a name="get-all-the-language-models"></a>모든 언어 모델 가져오기

[모든 API 가져오기](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Language-Models?&pattern=get) 는 지정 된 계정의 모든 사용자 지정 언어 모델을 목록으로 반환 합니다.

### <a name="response"></a>응답

응답은이 예제 JSON 출력의 형식에 따라 계정의 모든 언어 모델 및 해당 메타 데이터 및 파일의 목록을 제공 합니다.

```json
[
    {
        "id": "dfae5745-6f1d-4edd-b224-42e1ab57a891",
        "name": "TestModel",
        "language": "En-US",
        "state": "None",
        "languageModelId": "00000000-0000-0000-0000-000000000000",
        "files": [
        {
            "id": "25be7c0e-b6a6-4f48-b981-497e920a0bc9",
            "name": "hellofile",
            "enable": true,
            "creator": "John Doe",
            "creationTime": "2018-04-28T11:55:34.6733333"
        },
        {
            "id": "33025f5b-2354-485e-a50c-4e6b76345ca7",
            "name": "worldfile",
            "enable": true,
            "creator": "John Doe",
            "creationTime": "2018-04-28T11:55:34.86"
        }
        ]
    },
    {
        "id": "dfae5745-6f1d-4edd-b224-42e1ab57a892",
        "name": "AnotherTestModel",
        "language": "En-US",
        "state": "None",
        "languageModelId": "00000000-0000-0000-0000-000000000001",
        "files": []
    }
]
```

## <a name="delete-a-file-from-a-language-model"></a>언어 모델에서 파일 삭제

[Delete](https://api-portal.videoindexer.ai/docs/services/operations/operations/Delete-Language-Model-File?&pattern=delete) API는 지정 된 계정의 지정 된 언어 모델에서 지정 된 파일을 삭제 합니다.

### <a name="response"></a>응답

파일이 언어 모델에서 성공적으로 삭제 되 면 반환 된 콘텐츠가 없습니다.

## <a name="get-metadata-on-a-file-from-a-language-model"></a>언어 모델에서 파일의 메타데이터 가져오기

[파일 API의 메타 데이터 가져오기](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Language-Model-File-Data?&pattern=get%20language%20model) 는 지정 된 파일의 내용과 메타 데이터를 계정의 선택 된 언어 모델에서 반환 합니다.

### <a name="response"></a>응답

응답은 다음 예제와 같이 JSON 형식으로 파일의 내용과 메타 데이터를 제공 합니다.

```json
{
    "content": "hello\r\nworld",
    "id": "84fcf1ac-1952-48f3-b372-18f768eedf83",
    "name": "Hello",
    "enable": true,
    "creator": "John Doe",
    "creationTime": "2018-04-27T20:10:10.5233333"
}
```

> [!NOTE]
> 이 예제 파일의 내용은 별도의 두 줄에 단어 "hello"와 "world"를 포함합니다.

## <a name="download-a-file-from-a-language-model"></a>언어 모델에서 파일 다운로드

[파일 다운로드](https://api-portal.videoindexer.ai/docs/services/operations/operations/Download-Language-Model-File-Content?) API는 지정 된 계정의 지정 된 언어 모델에서 지정 된 파일의 내용을 포함 하는 텍스트 파일을 다운로드 합니다. 이 텍스트 파일은 원래 업로드한 텍스트 파일의 내용과 일치해야 합니다.

### <a name="response"></a>응답

응답은 JSON 형식의 파일 내용을 포함하는 텍스트 파일의 다운로드입니다.

## <a name="next-steps"></a>다음 단계

[웹 사이트를 사용하여 언어 모델 사용자 지정](customize-language-model-with-website.md)
