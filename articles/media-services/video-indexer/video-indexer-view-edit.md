---
title: Video Indexer 정보 보기 및 편집
titlesuffix: Azure Media Services
description: 이 항목에서는 Video Indexer의 인사이트를 보고 편집하는 방법을 설명합니다.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.topic: article
ms.date: 02/10/2019
ms.author: juliako
ms.openlocfilehash: c1b26b8a59ac0306fc06bf90f01642d4585a45be
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/11/2019
ms.locfileid: "55991578"
---
# <a name="view-and-edit-video-indexer-insights"></a>Video Indexer 정보 보기 및 편집

이 항목에서는 비디오에 대한 Video Indexer 인사이트를 보고 편집하는 방법에 대해 설명합니다.

1. [Video Indexer](https://www.videoindexer.ai/) 웹 사이트로 이동하고 로그인합니다.
2. Video Indexer 인사이트를 만들려는 비디오를 찾습니다. 자세한 내용은 [비디오 내 정확한 시점 찾기](video-indexer-search.md)를 참조하세요.
3. **재생**을 누릅니다.

    페이지에는 비디오의 요약된 인사이트가 표시됩니다. 

    ![자세한 정보](./media/video-indexer-view-edit/video-indexer-summarized-insights.png)

4. 비디오의 요약된 인사이트를 봅니다. 

    요약된 인사이트는 얼굴, 키워드, 감정 등의 데이터에 대한 집계된 보기를 보여 줍니다. 예를 들어 사람의 얼굴, 각 얼굴에 표시되는 시간 범위 및 표시된 시간의 비율(%)을 볼 수 있습니다.

    플레이어와 인사이트가 동기화됩니다. 예를 들어 키워드 또는 전사 줄을 클릭하면 플레이어에서 비디오의 해당 시점으로 이동합니다. 애플리케이션에서 플레이어/인사이트 보기 및 동기화를 수행할 수 있습니다. 자세한 내용은 [애플리케이션에 Video Indexer 위젯 포함](video-indexer-embed-widgets.md)을 참조하세요. 

3. Video Indexer 인사이트를 편집합니다.

    비디오 아래에서 [편집]을 누릅니다. 비디오의 전체 분석을 보여 주는 페이지가 표시됩니다. 분석은 블록으로 구분됩니다. 블록을 사용하면 데이터를 더 쉽게 살펴볼 수 있습니다. 예를 들어 블록은 화자가 변경되거나 오랫동안 일시 중지되는 경우를 기준으로 나눌 수 있습니다. 원하는 줄만 포함된 고유한 재생 목록을 만들 수 있습니다. 원본 비디오의 특정 부분만 표시하려면 주제/키워드, 감정, 사람, 화자를 기준으로 필터링할 수 있습니다. 비디오의 전사 또는 OCR만 표시하도록 선택할 수 있습니다.  

    ![자세한 정보](./media/video-indexer-view-edit/video-indexer-create-new-playlist.png)

## <a name="next-steps"></a>다음 단계

[다른 비디오에 기반하여 사용자 고유의 Video Indexer 인사이트를 만드는 방법에 대해 알아봅니다](video-indexer-create-new.md).

## <a name="see-also"></a>참고 항목

[Video Indexer 개요](video-indexer-overview.md)

