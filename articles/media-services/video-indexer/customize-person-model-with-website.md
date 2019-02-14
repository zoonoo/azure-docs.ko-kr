---
title: Video Indexer 웹 사이트를 사용하여 개인 모델 사용자 지정 - Azure
titlesuffix: Azure Media Services
description: 이 문서에서는 Video Indexer 웹 사이트를 사용하여 개인 모델을 사용자 지정하는 방법을 설명합니다.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.topic: article
ms.date: 02/10/2019
ms.author: anzaman
ms.openlocfilehash: 29c32126a5bbe792dd6fe88ae189e09c511d2a22
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/11/2019
ms.locfileid: "55997069"
---
# <a name="customize-a-person-model-with-the-video-indexer-website"></a>Video Indexer 웹 사이트를 사용하여 개인 모델 사용자 지정

Video Indexer는 비디오 콘텐츠에 대해 얼굴 감지 및 유명인 인식 기능을 지원합니다. 유명인 인식 기능은 IMDB, Wikipedia, 상위 LinkedIn 영향력 행사자 등 일반적으로 요청되는 데이터 원본을 기준으로 하는 약 백만 개의 얼굴을 처리합니다. 유명인 인식 기능으로 인식되지 않는 얼굴은 감지되지만 이름 없이 유지됩니다. Video Indexer에 비디오를 업로드하고 결과를 얻은 후 돌아가서 인식되지 않은 얼굴에 이름을 지정할 수 있습니다. 얼굴에 이름 레이블을 지정하면 얼굴과 이름이 계정의 개인 모델에 추가됩니다. 그러면 Video Indexer가 향후 비디오 및 이전 비디오에서 이 얼굴을 인식합니다.

Video Indexer 웹 사이트를 사용하여 이 항목에 설명된 대로 비디오에서 감지된 얼굴을 편집할 수 있습니다. [API를 사용하여 개인 모델 사용자 지정](customize-person-model-with-api.md)에 설명된 대로 API를 사용할 수도 있습니다.

## <a name="edit-a-face"></a>얼굴 편집

> [!NOTE]
> 이름은 개인 모델에서 고유하므로 두 개의 다른 얼굴에 동일한 이름을 지정하는 경우 Video Indexer는 얼굴을 동일한 개인으로 보고, 비디오가 다시 인덱싱될 때 통합합니다. Video Indexer가 동일한 얼굴의 여러 다른 발생을 감지하는 경우 동일한 이름을 지정하고 비디오를 다시 인덱싱합니다.
> Video Indexer에서 유명인으로 인식된 얼굴을 새 이름으로 업데이트할 수 있습니다. 사용자가 지정한 새 이름이 기본 제공 유명인 인식보다 우선 적용됩니다.

1. [Video Indexer](https://www.videoindexer.ai/) 웹 사이트로 이동하고 로그인합니다.
2. 계정에서 보고 편집하려는 비디오를 검색합니다.
3. 비디오에서 얼굴을 편집하려면 **인사이트** 탭으로 이동한 다음, 창의 오른쪽 위에 있는 연필 아이콘을 클릭합니다.

    ![개인 모델 사용자 지정](./media/customize-face-model/customize-face-model.png)

4. 감지된 얼굴을 클릭하고 “알 수 없는 #X”(또는 이전에 얼굴에 할당된 이름)에서 이름을 변경합니다.
5. 새 이름을 입력한 후 새 이름 옆에 있는 확인 아이콘을 클릭합니다. 이렇게 하면 새 이름이 저장되고 다른 현재 비디오와 향후에 업로드하는 비디오에서 이 얼굴의 모든 발생이 인식되고 이름이 지정됩니다. 다른 현재 비디오의 얼굴 인식은 배치 프로세스이므로 적용되는 데 다소 시간이 걸릴 수 있습니다. 

    ![업데이트 저장](./media/customize-face-model/save-update.png)

## <a name="delete-a-face"></a>얼굴 삭제

비디오에서 감지된 얼굴을 삭제하려면 **인사이트** 창으로 이동한 다음, 창의 오른쪽 위에 있는 연필 아이콘을 클릭합니다. 얼굴 이름 아래에 있는 **삭제** 옵션을 클릭합니다. 이렇게 하면 삭제한 얼굴이 비디오에서 제거됩니다. 얼굴이 표시되는 다른 비디오에서는 얼굴이 계속 감지되지만, 얼굴이 인덱싱된 후 해당 비디오에서도 얼굴을 삭제할 수 있습니다. 삭제하기 전에 이름을 지정한 경우 계정의 개인 모델에도 얼굴이 계속 존재합니다.

## <a name="next-steps"></a>다음 단계

[API를 사용하여 개인 모델 사용자 지정](customize-person-model-with-api.md)
