---
title: Video Indexer API를 사용 하 여 개인 모델 사용자 지정
titleSuffix: Azure Media Services
description: Video Indexer API를 사용 하 여 사용자 모델을 사용자 지정 하는 방법을 알아봅니다.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 01/14/2020
ms.author: kumud
ms.openlocfilehash: 09366dea1a0d77052b6f99e9f5ab52c270e341b4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87047018"
---
# <a name="customize-a-person-model-with-the-video-indexer-api"></a>Video Indexer API를 사용하여 개인 모델 사용자 지정

Video Indexer는 비디오 콘텐츠에 대해 얼굴 감지 및 유명인 인식 기능을 지원합니다. 유명인 인식 기능은 IMDB, 위키백과 및 상위 LinkedIn 영향 요인을 비롯 하 여 일반적으로 요청 된 데이터 원본을 기반으로 하는 100만 얼굴을 다룹니다. 유명인 인식 기능에서 인식 되지 않는 얼굴은 검색 되지만 명명 되지 않은 상태로 남아 있습니다. Video Indexer에 비디오를 업로드 하 고 결과를 다시 얻으려면 뒤로 돌아가 인식 되지 않은 얼굴의 이름을 지정할 수 있습니다. 얼굴에 이름 레이블을 지정하면 얼굴과 이름이 계정의 개인 모델에 추가됩니다. 그러면 Video Indexer가 향후 비디오 및 이전 비디오에서 이 얼굴을 인식합니다.

Video Indexer API를 사용하여 이 항목에 설명된 대로 비디오에서 감지된 얼굴을 편집할 수 있습니다. [Video Indexer 웹 사이트를 사용하여 개인 모델 사용자 지정](customize-person-model-with-api.md)에 설명된 대로 Video Indexer 웹 사이트를 사용할 수도 있습니다.

## <a name="managing-multiple-person-models"></a>여러 개의 개인 모델 관리

Video Indexer는 계정당 여러 개의 개인 모델을 지원합니다. 이 기능은 현재 Video Indexer API를 통해서만 사용할 수 있습니다.

계정이 다른 사용 사례 시나리오를 지원하는 경우 계정당 여러 개의 개인 모델을 만드는 것이 좋습니다. 예를 들어 콘텐츠가 스포츠와 관련 되어 있는 경우 각 스포츠 (축구, 농구, 축구 등)에 대 한 별도의 사람 모델을 만들 수 있습니다.

모델이 생성되면, 비디오를 업로드/인덱싱하거나 다시 인덱싱할 때 특정 개인 모델의 모델 ID를 제공하여 모델을 사용할 수 있습니다. 비디오에 대해 새로운 얼굴을 학습하면 비디오와 연결된 특정 사용자 지정 모델이 업데이트됩니다.

각 계정이 50개의 개인 모델로 제한됩니다. 다중 사용자 모델 지원이 필요 하지 않은 경우 업로드/인덱싱 또는 인덱스를 사용할 때 사용자 모델 ID를 비디오에 할당 하지 마세요. 이 경우 Video Indexer는 사용자 계정의 기본 사용자 지정 개인 모델을 사용합니다.

## <a name="create-a-new-person-model"></a>새 개인 모델 만들기

지정 된 계정에 새 Person 모델을 만들려면 [사용자 모델 만들기](https://api-portal.videoindexer.ai/docs/services/operations/operations/Create-Person-Model?) API를 사용 합니다.

응답은 방금 만든 개인 모델의 이름 및 생성된 모델 ID를 아래 예제 형식으로 제공합니다.

```json
{
    "id": "227654b4-912c-4b92-ba4f-641d488e3720",
    "name": "Example Person Model"
}
```

그런 다음 비디오를 [업로드 하 여](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?) [비디오를 인덱싱하고](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-index-video?)인덱싱할 때 **personModelId** 매개 변수에 대 한 **id** 값을 사용 합니다.

## <a name="delete-a-person-model"></a>개인 모델 삭제

지정 된 계정에서 사용자 지정 사용자 모델을 삭제 하려면 [Person 모델 삭제](https://api-portal.videoindexer.ai/docs/services/operations/operations/Delete-Person-Model?) API를 사용 합니다.

개인 모델을 성공적으로 삭제한 후에도 삭제된 모델을 사용 중인 현재 비디오의 인덱스는 다시 인덱싱할 때까지 변경되지 않습니다. 인덱스를 인덱싱할 때 삭제 된 모델에서 이름이 지정 된 얼굴은 해당 모델을 사용 하 여 인덱싱되는 현재 비디오의 Video Indexer에서 인식 되지 않지만 얼굴은 계속 검색 됩니다. 삭제된 모델을 사용하여 인덱싱된 현재 비디오는 이제 계정의 기본 개인 모델을 사용합니다. 삭제된 모델의 얼굴이 계정의 기본 모델에서도 이름이 지정된 경우에는 해당 얼굴이 비디오에서 계속 인식됩니다.

Person 모델을 성공적으로 삭제 하면 반환 된 콘텐츠가 없습니다.

## <a name="get-all-person-models"></a>모든 개인 모델 가져오기

지정 된 계정에서 모든 사용자 모델을 가져오려면 [사용자 모델 가져오기](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Person-Models?) API를 사용 합니다.

응답은 계정에 있는 모든 사람 모델 (지정 된 계정의 기본 사용자 모델 포함)의 목록과 아래 예제 형식에 따라 각각의 이름과 Id를 제공 합니다.

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

비디오를 업로드 하 여 비디오를 `id` `personModelId` [인덱싱하고](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-index-video?) [인덱스를](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?) 만들 때 매개 변수에 대 한 Person 모델의 값을 사용 하 여 비디오에 사용할 모델을 선택할 수 있습니다.

## <a name="update-a-face"></a>얼굴 업데이트

이 명령을 사용하면 비디오 ID와 얼굴 ID를 사용하는 이름으로 비디오의 얼굴을 업데이트할 수 있습니다. 그런 다음이 작업은 업로드/인덱싱 또는 다시 인덱싱 시 비디오가 연결 된 사람 모델을 업데이트 합니다. 개인 모델이 할당되지 않은 경우 계정의 기본 개인 모델이 업데이트됩니다.

그러면 시스템은 동일한 사람 모델을 공유 하는 다른 현재 비디오에서 동일한 얼굴의 발생을 인식 합니다. 다른 현재 비디오의 얼굴 인식은 배치 프로세스이므로 적용되는 데 다소 시간이 걸릴 수 있습니다.

Video Indexer에서 유명인으로 인식된 얼굴을 새 이름으로 업데이트할 수 있습니다. 사용자가 지정한 새 이름이 기본 제공 유명인 인식보다 우선 적용됩니다.

얼굴을 업데이트 하려면 [video face API 업데이트](https://api-portal.videoindexer.ai/docs/services/operations/operations/Update-Video-Face?) 를 사용 합니다.

이름은 개인 모델에 대해 고유 하므로 동일한 사람에 게 동일한 매개 변수 값을 제공 하는 두 개의 얼굴을 동일한 사람에 게 제공 하는 경우 `name` Video Indexer는 얼굴을 동일한 사람으로 표시 하 고 비디오를 재 인덱싱합니다.

## <a name="next-steps"></a>다음 단계

[Video Indexer 웹 사이트를 사용하여 개인 모델 사용자 지정](customize-person-model-with-website.md)
