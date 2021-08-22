---
title: Azure Video Analyzer for Media(이전의 Video Indexer) API를 통해 언어 모델 사용자 지정
titlesuffix: Azure Video Analyzer for Media
description: Azure Video Analyzer for Media(이전의 Video Indexer) API를 사용하여 언어 모델을 사용자 지정하는 방법을 알아봅니다.
services: azure-video-analyzer
author: anikaz
manager: johndeu
ms.topic: article
ms.subservice: azure-video-analyzer-media
ms.date: 02/04/2020
ms.author: kumud
ms.openlocfilehash: b3239e9af0a8ba3218d511f872dcbd117295add0
ms.sourcegitcommit: 0af634af87404d6970d82fcf1e75598c8da7a044
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/15/2021
ms.locfileid: "112123221"
---
# <a name="customize-a-language-model-with-the-video-analyzer-for-media-api"></a>Video Analyzer for Media API를 사용하여 언어 모델 사용자 지정

Azure Video Analyzer for Media(이전의 Video Indexer)를 사용하면 엔진을 적응시킬 어휘가 포함된 도메인의 텍스트인 적응 텍스트를 업로드하여 음성 인식을 사용자 지정하기 위한 사용자 지정 언어 모델을 만들 수 있습니다. 모델을 학습하면 적응 텍스트에 나타나는 새 단어가 인식됩니다.

사용자 지정 언어 모델의 자세한 개요와 모범 사례에 대해서는 [Video Analyzer for Media를 사용하여 언어 모델 사용자 지정](customize-language-model-overview.md)을 참조하세요.

이 항목에 설명된 대로 Video Analyzer for Media API를 사용하여 계정에서 사용자 지정 언어 모델을 만들고 편집할 수 있습니다. [Video Analyzer for Media 웹 사이트를 사용하여 언어 모델 사용자 지정](customize-language-model-with-api.md)에 설명된 대로 웹 사이트를 사용할 수도 있습니다.

## <a name="create-a-language-model"></a>언어 모델 만들기

[언어 모델 만들기](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Create-Language-Model) API에서는 지정된 계정에 새 사용자 지정 언어 모델을 만듭니다. 이 호출에서 언어 모델에 대한 파일을 업로드할 수 있습니다. 또는 여기에서 언어 모델을 만들고, 나중에 해당 언어 모델을 업데이트하여 모델에 대한 파일을 업로드할 수 있습니다.

> [!NOTE]
> 모델이 파일 내용을 파악하려면 활성화된 파일을 사용하여 모델을 학습해야 합니다. 언어 학습 지침은 다음 섹션에 나와 있습니다.

언어 모델에 추가할 파일을 업로드하려면 위의 필수 매개 변수 값을 제공하는 것 외에도 FormData를 사용하여 본문에 파일을 업로드해야 합니다. 이 작업을 수행하는 방법은 다음 두 가지입니다.

* 키는 파일 이름이고 값은 txt 파일입니다.
* 키는 파일 이름이고 값은 txt 파일의 URL입니다.

### <a name="response"></a>응답

응답에서는 이 예제 JSON 출력 형식에 따라 각 모델 파일의 메타데이터와 새로 만든 언어 모델의 메타데이터를 제공합니다.

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

[언어 모델 학습](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Train-Language-Model) API에서는 언어 모델에서 업로드되고 사용하도록 설정된 파일의 콘텐츠를 사용하여 지정된 계정의 사용자 지정 언어 모델을 학습합니다.

> [!NOTE]
> 먼저 언어 모델을 생성한 후 해당 파일을 업로드해야 합니다. 언어 모델을 만들 때 파일을 업로드하거나 언어 모델을 업데이트하여 파일을 업로드할 수 있습니다.

### <a name="response"></a>응답

응답에서는 이 예제 JSON 출력 형식에 따라 각 모델 파일의 메타데이터와 새로 학습한 언어 모델의 메타데이터를 제공합니다.

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

반환되는 `id`는 언어 모델 간 구별에 사용되는 고유한 ID이지만 `languageModelId`는 [인덱스에 비디오 업로드](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Upload-Video) 및 [비디오 다시 인덱싱](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Re-Index-Video) API 둘 다에 사용됩니다(Video Analyzer for Media 업로드/다시 인덱싱 API에서는 `linguisticModelId`).

## <a name="delete-a-language-model"></a>언어 모델 삭제

[언어 모델 삭제](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Delete-Language-Model) API에서는 사용자 지정 언어 모델을 지정된 계정에서 삭제합니다. 삭제된 언어 모델을 사용하던 모든 비디오는 비디오를 다시 인덱싱할 때까지 동일한 인덱스를 유지합니다. 비디오를 다시 인덱싱하는 경우 비디오에 새 언어 모델을 할당할 수 있습니다. 그렇지 않으면 Video Analyzer for Media는 기본 모델을 사용하여 비디오를 다시 인덱싱합니다.

### <a name="response"></a>응답

언어 모델이 성공적으로 삭제되면 반환되는 콘텐츠가 없습니다.

## <a name="update-a-language-model"></a>언어 모델 업데이트

[언어 모델 업데이트](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Update-Language-Model) API에서는 지정된 계정의 사용자 지정 언어 개인 모델을 업데이트합니다.

> [!NOTE]
> 언어 모델을 미리 만들었어야 합니다. 이 호출을 사용하여 해당 모델 내의 모든 파일을 사용하거나 사용하지 않도록 설정하고, 언어 모델의 이름을 업데이트하고, 언어 모델에 추가할 파일을 업로드할 수 있습니다.

언어 모델에 추가할 파일을 업로드하려면 위의 필수 매개 변수 값을 제공하는 것 외에도 FormData를 사용하여 본문에 파일을 업로드해야 합니다. 이 작업을 수행하는 방법은 다음 두 가지입니다.

* 키는 파일 이름이고 값은 txt 파일입니다.
* 키는 파일 이름이고 값은 txt 파일의 URL입니다.

### <a name="response"></a>응답

응답에서는 이 예제 JSON 출력 형식에 따라 각 모델 파일의 메타데이터와 새로 학습한 언어 모델의 메타데이터를 제공합니다.

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

응답으로 반환된 파일의 `id`를 사용하여 파일의 콘텐츠를 다운로드합니다.

## <a name="update-a-file-from-a-language-model"></a>언어 모델에서 파일 업데이트

[파일 업데이트](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Update-Language-Model-file)를 사용하면 지정된 계정의 사용자 지정 언어 모델에 있는 파일의 이름 및 `enable` 상태를 업데이트할 수 있습니다.

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

응답으로 반환된 파일의 `id`를 사용하여 파일의 콘텐츠를 다운로드합니다.

## <a name="get-a-specific-language-model"></a>특정 언어 모델 가져오기

[가져오기](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Get-Language-Model) API에서는 언어와 같은 지정된 계정의 지정된 언어 모델에 대한 정보와 언어 모델에 있는 파일을 반환합니다.

### <a name="response"></a>응답

응답에서는 다음 예제 JSON 출력 형식에 따라 각 모델 파일의 메타데이터와 지정된 언어 모델의 메타데이터를 제공합니다.

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

응답으로 반환된 파일의 `id`를 사용하여 파일의 콘텐츠를 다운로드합니다.

## <a name="get-all-the-language-models"></a>모든 언어 모델 가져오기

[모두 가져오기](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Get-Language-Models) API에서는 지정된 계정의 모든 사용자 지정 언어 모델을 목록으로 반환합니다.

### <a name="response"></a>응답

응답에서는 다음 예제 JSON 출력 형식에 따라 계정의 모든 언어 모델 목록과 각각의 메타데이터 및 파일을 제공합니다.

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

[삭제](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Delete-Language-Model-File) API에서는 지정된 계정의 지정된 언어 모델에서 지정된 파일을 삭제합니다.

### <a name="response"></a>응답

파일이 언어 모델에서 성공적으로 삭제되면 반환되는 콘텐츠가 없습니다.

## <a name="get-metadata-on-a-file-from-a-language-model"></a>언어 모델에서 파일의 메타데이터 가져오기

[파일의 메타데이터 가져오기](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Get-Language-Model-File-Data) API에서는 계정의 선택한 언어 모델에서 지정된 파일의 콘텐츠 및 메타데이터를 반환합니다.

### <a name="response"></a>응답

응답에서는 다음 예제와 유사하게 JSON 형식으로 파일의 콘텐츠 및 메타데이터를 제공합니다.

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

[파일 다운로드](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Download-Language-Model-File-Content) API에서는 지정된 계정의 지정된 언어 모델에서 지정된 파일의 콘텐츠가 포함된 텍스트 파일을 다운로드합니다. 이 텍스트 파일은 원래 업로드한 텍스트 파일의 내용과 일치해야 합니다.

### <a name="response"></a>응답

응답은 JSON 형식의 파일 내용을 포함하는 텍스트 파일의 다운로드입니다.

## <a name="next-steps"></a>다음 단계

[웹 사이트를 사용하여 언어 모델 사용자 지정](customize-language-model-with-website.md)
