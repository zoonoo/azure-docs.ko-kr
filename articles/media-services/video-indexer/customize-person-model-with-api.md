---
title: 비디오 인덱서 API를 통해 사람 모델 사용자 지정
titleSuffix: Azure Media Services
description: 비디오 인덱서 API를 사용하여 사람 모델을 사용자 지정하는 방법을 알아봅니다.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 01/14/2020
ms.author: anzaman
ms.openlocfilehash: fa41fca7f8ad96cf507aa6f04059b1254c8c3961
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80127901"
---
# <a name="customize-a-person-model-with-the-video-indexer-api"></a>Video Indexer API를 사용하여 개인 모델 사용자 지정

Video Indexer는 비디오 콘텐츠에 대해 얼굴 감지 및 유명인 인식 기능을 지원합니다. 유명 인사 인식 기능은 IMDB, 위키백과 및 최고 링크드 인 영향력과 같은 일반적으로 요청되는 데이터 소스를 기반으로 약 백만 명의 얼굴을 다룹니다. 유명 인사 인식 기능으로 인식되지 않는 얼굴은 감지되지만 이름이 지정되지 않은 상태로 남아 있습니다. 동영상을 비디오 인덱서에 업로드하고 결과를 다시 확인하면 다시 돌아가서 인식되지 않은 얼굴의 이름을 지정할 수 있습니다. 얼굴에 이름 레이블을 지정하면 얼굴과 이름이 계정의 개인 모델에 추가됩니다. 그러면 Video Indexer가 향후 비디오 및 이전 비디오에서 이 얼굴을 인식합니다.

Video Indexer API를 사용하여 이 항목에 설명된 대로 비디오에서 감지된 얼굴을 편집할 수 있습니다. [Video Indexer 웹 사이트를 사용하여 개인 모델 사용자 지정](customize-person-model-with-api.md)에 설명된 대로 Video Indexer 웹 사이트를 사용할 수도 있습니다.

## <a name="managing-multiple-person-models"></a>여러 개의 개인 모델 관리

Video Indexer는 계정당 여러 개의 개인 모델을 지원합니다. 이 기능은 현재 Video Indexer API를 통해서만 사용할 수 있습니다.

계정이 다른 사용 사례 시나리오를 지원하는 경우 계정당 여러 개의 개인 모델을 만드는 것이 좋습니다. 예를 들어 콘텐츠가 스포츠와 관련된 경우 각 스포츠(축구, 농구, 축구 등)에 대해 별도의 Person 모델을 만들 수 있습니다.

모델이 생성되면, 비디오를 업로드/인덱싱하거나 다시 인덱싱할 때 특정 개인 모델의 모델 ID를 제공하여 모델을 사용할 수 있습니다. 비디오에 대해 새로운 얼굴을 학습하면 비디오와 연결된 특정 사용자 지정 모델이 업데이트됩니다.

각 계정이 50개의 개인 모델로 제한됩니다. 여러 사람 모델 지원이 필요하지 않은 경우 업로드/인덱싱 또는 다시 인덱싱할 때 동영상에 사람 모델 ID를 할당하지 마세요. 이 경우 Video Indexer는 사용자 계정의 기본 사용자 지정 개인 모델을 사용합니다.

## <a name="create-a-new-person-model"></a>새 개인 모델 만들기

지정된 계정에서 새 사람 모델을 만들려면 사람 모델 API [만들기를](https://api-portal.videoindexer.ai/docs/services/operations/operations/Create-Person-Model?) 사용합니다.

응답은 방금 만든 개인 모델의 이름 및 생성된 모델 ID를 아래 예제 형식으로 제공합니다.

```json
{
    "id": "227654b4-912c-4b92-ba4f-641d488e3720",
    "name": "Example Person Model"
}
```

그런 다음 비디오를 색인화하거나 비디오를 다시 [인덱싱할](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?) 때 **personModelId** 매개 [변수에](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-index-video?)대한 **ID** 값을 사용합니다.

## <a name="delete-a-person-model"></a>개인 모델 삭제

지정된 계정에서 사용자 지정 Person 모델을 삭제하려면 사람 모델 API [삭제를](https://api-portal.videoindexer.ai/docs/services/operations/operations/Delete-Person-Model?) 사용합니다.

개인 모델을 성공적으로 삭제한 후에도 삭제된 모델을 사용 중인 현재 비디오의 인덱스는 다시 인덱싱할 때까지 변경되지 않습니다. 다시 인덱싱하면 삭제된 모델의 이름이 지정된 얼굴은 해당 모델을 사용하여 인덱싱되었지만 얼굴은 여전히 감지된 현재 동영상의 비디오 인덱서에서 인식되지 않습니다. 삭제된 모델을 사용하여 인덱싱된 현재 비디오는 이제 계정의 기본 개인 모델을 사용합니다. 삭제된 모델의 얼굴이 계정의 기본 모델에서도 이름이 지정된 경우에는 해당 얼굴이 비디오에서 계속 인식됩니다.

Person 모델을 성공적으로 삭제하면 반환되는 콘텐츠가 없습니다.

## <a name="get-all-person-models"></a>모든 개인 모델 가져오기

지정된 계정의 모든 Person 모델을 얻으려면 [사람 모델 API를 가져옵니다.](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Person-Models?)

응답은 계정의 모든 Person 모델(지정된 계정의 기본 Person 모델 포함)과 아래 예제의 형식에 따라 각 이름과 ID의 목록을 제공합니다.

```json
[
    {
        "id": "59f9c326-b141-4515-abe7-7d822518571f",
        "name": "Default"
    }, 
    {
        "id": "9ef2632d-310a-4510-92e1-cc70ae0230d4",
        "name": "Test"
    }
]
```

[동영상을 색인에 업로드하거나](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?) [비디오를 다시 인덱싱할](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-index-video?)때 `id` `personModelId` 매개 변수에 대한 Person 모델 값을 사용하여 비디오에 사용할 모델을 선택할 수 있습니다.

## <a name="update-a-face"></a>얼굴 업데이트

이 명령을 사용하면 비디오 ID와 얼굴 ID를 사용하는 이름으로 비디오의 얼굴을 업데이트할 수 있습니다. 그런 다음 업로드/인덱싱 또는 다시 인덱싱할 때 동영상이 연관된 사람 모델을 업데이트합니다. 개인 모델이 할당되지 않은 경우 계정의 기본 개인 모델이 업데이트됩니다.

그런 다음 시스템은 동일한 사람 모델을 공유하는 다른 현재 동영상에서 동일한 얼굴의 발생을 인식합니다. 다른 현재 비디오의 얼굴 인식은 배치 프로세스이므로 적용되는 데 다소 시간이 걸릴 수 있습니다.

Video Indexer에서 유명인으로 인식된 얼굴을 새 이름으로 업데이트할 수 있습니다. 사용자가 지정한 새 이름이 기본 제공 유명인 인식보다 우선 적용됩니다.

얼굴을 업데이트하려면 비디오 [얼굴](https://api-portal.videoindexer.ai/docs/services/operations/operations/Update-Video-Face?) API 업데이트를 사용합니다.

이름은 사람 모델에 고유하므로 동일한 사람 모델에서 동일한 `name` 매개 변수 값을 두 개의 서로 다른 얼굴을 지정하면 Video Indexer는 동일한 사람으로 얼굴을 보고 비디오를 다시 인덱싱하면 수렴됩니다.

## <a name="next-steps"></a>다음 단계

[Video Indexer 웹 사이트를 사용하여 개인 모델 사용자 지정](customize-person-model-with-website.md)
