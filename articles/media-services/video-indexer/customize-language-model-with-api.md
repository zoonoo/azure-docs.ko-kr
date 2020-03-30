---
title: 비디오 인덱서 API를 사용 하 고 언어 모델 사용자 지정
titlesuffix: Azure Media Services
description: 비디오 인덱서 API를 사용하여 언어 모델을 사용자 지정하는 방법을 알아봅니다.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 02/04/2020
ms.author: anzaman
ms.openlocfilehash: 19067bbbaf93c9abc9a9220b09dd482ce9115655
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80127974"
---
# <a name="customize-a-language-model-with-the-video-indexer-api"></a>비디오 인덱서 API를 사용 하 고 언어 모델 사용자 지정

Video Indexer를 사용하면 엔진을 적응시킬 어휘가 포함된 도메인의 텍스트인 적응 텍스트를 업로드하여 음성 인식을 사용자 지정하기 위한 사용자 지정 언어 모델을 만들 수 있습니다. 모델을 학습하면 적응 텍스트에 나타나는 새 단어가 인식됩니다.

사용자 지정 언어 모델의 자세한 개요와 모범 사례에 대해서는 [Video Indexer로 언어 모델 사용자 지정](customize-language-model-overview.md)을 참조하세요.

이 항목에 설명된 대로 Video Indexer API를 사용하여 계정에서 사용자 지정 언어 모델을 만들고 편집할 수 있습니다. [Video Indexer 웹 사이트를 사용하여 언어 모델 사용자 지정](customize-language-model-with-api.md)에 설명된 대로 웹 사이트를 사용할 수도 있습니다.

## <a name="create-a-language-model"></a>언어 모델 만들기

[언어 모델](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Create-Language-Model?) 만들기 API는 지정된 계정에 새 사용자 지정 언어 모델을 만듭니다. 이 호출에서 언어 모델에 대한 파일을 업로드할 수 있습니다. 또는 여기에서 언어 모델을 만들고, 나중에 해당 언어 모델을 업데이트하여 모델에 대한 파일을 업로드할 수 있습니다.

> [!NOTE]
> 모델이 파일 내용을 파악하려면 활성화된 파일을 사용하여 모델을 학습해야 합니다. 언어 학습 지침은 다음 섹션에 나와 있습니다.

언어 모델에 추가할 파일을 업로드하려면 위의 필수 매개 변수에 대한 값을 제공하는 것 외에도 FormData를 사용하여 본문에 파일을 업로드해야 합니다. 이 작업을 수행하는 방법에는 두 가지가 있습니다.

* 키는 파일 이름이 되고 값은 txt 파일이 됩니다.
* 키는 파일 이름이 되고 값은 txt 파일에 대한 URL이 됩니다.

### <a name="response"></a>응답

응답은 이 예제 JSON 출력의 형식에 따라 모델의 각 파일에 대한 메타데이터와 함께 새로 만든 언어 모델에 대한 메타데이터를 제공합니다.

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

언어 모델 API를 [학습하면](https://api-portal.videoindexer.ai/docs/services/operations/operations/Train-Language-Model?&pattern=train) 언어 모델에 업로드되고 활성화된 파일의 내용이 있는 지정된 계정의 사용자 지정 언어 모델을 학습합니다.

> [!NOTE]
> 먼저 언어 모델을 생성한 후 해당 파일을 업로드해야 합니다. 언어 모델을 만들거나 언어 모델을 업데이트하여 파일을 업로드할 수 있습니다.

### <a name="response"></a>응답

응답은 이 예제 JSON 출력의 형식에 따라 모델의 각 파일에 대한 메타데이터와 함께 새로 학습된 언어 모델의 메타데이터를 제공합니다.

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

반환된 `id` ID는 언어 모델을 구분하는 데 `languageModelId` 사용되는 고유 ID이며, [동영상 인덱싱을 위해 동영상을 업로드하고](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?) 비디오 API를 [다시 인덱싱하는](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-index-video?) 데 모두 사용됩니다(비디오 인덱서 업로드/API재인덱싱이라고도 함). `linguisticModelId`

## <a name="delete-a-language-model"></a>언어 모델 삭제

[언어 모델 API 삭제는](https://api-portal.videoindexer.ai/docs/services/operations/operations/Delete-Language-Model?&pattern=delete) 지정된 계정에서 사용자 지정 언어 모델을 삭제합니다. 삭제된 언어 모델을 사용하던 모든 동영상은 비디오를 다시 인덱싱할 때까지 동일한 인덱스를 유지합니다. 비디오를 다시 인덱싱하는 경우 비디오에 새 언어 모델을 할당할 수 있습니다. 그렇지 않으면 비디오 인덱서가 기본 모델을 사용하여 비디오를 다시 인덱싱합니다.

### <a name="response"></a>응답

언어 모델이 성공적으로 삭제되면 반환되는 콘텐츠가 없습니다.

## <a name="update-a-language-model"></a>언어 모델 업데이트

[업데이트 언어 모델](https://api-portal.videoindexer.ai/docs/services/operations/operations/Update-Language-Model?&pattern=update) API는 지정된 계정에서 사용자 지정 언어 사람 모델을 업데이트합니다.

> [!NOTE]
> 언어 모델을 미리 만들었어야 합니다. 이 호출을 사용하여 해당 모델 내의 모든 파일을 사용하거나 사용하지 않도록 설정하고, 언어 모델의 이름을 업데이트하고, 언어 모델에 추가할 파일을 업로드할 수 있습니다.

언어 모델에 추가할 파일을 업로드하려면 위의 필수 매개 변수에 대한 값을 제공하는 것 외에도 FormData를 사용하여 본문에 파일을 업로드해야 합니다. 이 작업을 수행하는 방법에는 두 가지가 있습니다.

* 키는 파일 이름이 되고 값은 txt 파일이 됩니다.
* 키는 파일 이름이 되고 값은 txt 파일에 대한 URL이 됩니다.

### <a name="response"></a>응답

응답은 이 예제 JSON 출력의 형식에 따라 모델의 각 파일에 대한 메타데이터와 함께 새로 학습된 언어 모델의 메타데이터를 제공합니다.

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

응답에서 `id` 반환된 파일을 사용하여 파일의 내용을 다운로드합니다.

## <a name="update-a-file-from-a-language-model"></a>언어 모델에서 파일 업데이트

[파일을 업데이트하면](https://api-portal.videoindexer.ai/docs/services/operations/operations/Update-Language-Model-file?&pattern=update) 지정된 계정의 사용자 `enable` 지정 언어 모델에서 파일의 이름과 상태를 업데이트할 수 있습니다.

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

응답에서 `id` 반환된 파일을 사용하여 파일의 내용을 다운로드합니다.

## <a name="get-a-specific-language-model"></a>특정 언어 모델 가져오기

[get](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Language-Model?&pattern=get) API는 언어 및 언어 모델에 있는 파일과 같은 지정된 계정에서 지정된 언어 모델에 대한 정보를 반환합니다.

### <a name="response"></a>응답

응답은 지정된 언어 모델의 메타데이터와 이 예제 JSON 출력의 형식에 따라 모델의 각 파일에 대한 메타데이터를 제공합니다.

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

응답에서 `id` 반환된 파일을 사용하여 파일의 내용을 다운로드합니다.

## <a name="get-all-the-language-models"></a>모든 언어 모델 가져오기

[Get all](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Language-Models?&pattern=get) API는 목록의 지정된 계정에 있는 모든 사용자 지정 언어 모델을 반환합니다.

### <a name="response"></a>응답

응답은 계정의 모든 언어 모델 목록과 이 예제 JSON 출력의 형식에 따라 해당 메타데이터 및 파일의 각 목록을 제공합니다.

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

[삭제](https://api-portal.videoindexer.ai/docs/services/operations/operations/Delete-Language-Model-File?&pattern=delete) API는 지정된 계정의 지정된 언어 모델에서 지정된 파일을 삭제합니다.

### <a name="response"></a>응답

언어 모델에서 파일이 성공적으로 삭제되면 반환되는 콘텐츠가 없습니다.

## <a name="get-metadata-on-a-file-from-a-language-model"></a>언어 모델에서 파일의 메타데이터 가져오기

[파일 API의 get 메타데이터는](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Language-Model-File-Data?&pattern=get%20language%20model) 계정에서 선택한 언어 모델에서 지정된 파일의 내용과 메타데이터를 반환합니다.

### <a name="response"></a>응답

응답은 이 예제와 마찬가지로 파일의 내용과 메타데이터를 JSON 형식으로 제공합니다.

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

[다운로드 파일](https://api-portal.videoindexer.ai/docs/services/operations/operations/Download-Language-Model-File-Content?) API는 지정된 계정의 지정된 언어 모델에서 지정된 파일의 내용을 포함하는 텍스트 파일을 다운로드합니다. 이 텍스트 파일은 원래 업로드한 텍스트 파일의 내용과 일치해야 합니다.

### <a name="response"></a>응답

응답은 JSON 형식의 파일 내용을 포함하는 텍스트 파일의 다운로드입니다.

## <a name="next-steps"></a>다음 단계

[웹 사이트를 사용하여 언어 모델 사용자 지정](customize-language-model-with-website.md)
