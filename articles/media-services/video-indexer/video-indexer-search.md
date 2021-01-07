---
title: Video Indexer를 사용 하 여 비디오에서 정확한 순간 검색
titleSuffix: Azure Media Services
description: Video Indexer를 사용 하 여 비디오에서 정확한 시간을 검색 하는 방법을 알아봅니다.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 11/23/2019
ms.author: juliako
ms.openlocfilehash: a1ca7c677e5f1eb55d74cca45e757676674f303c
ms.sourcegitcommit: 2e9643d74eb9e1357bc7c6b2bca14dbdd9faa436
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/25/2020
ms.locfileid: "96030531"
---
# <a name="search-for-exact-moments-in-videos-with-video-indexer"></a>Video Indexer를 사용 하 여 비디오에서 정확한 순간 검색

이 항목에서는 Video Indexer 웹 사이트를 사용 하 여 비디오에서 정확한 시간을 검색 하는 방법을 보여 줍니다.

1. [Video Indexer](https://www.videoindexer.ai/) 웹 사이트로 이동 하 여 로그인 합니다.
1. 검색 키워드를 지정 하면 계정 라이브러리의 모든 비디오에서 검색이 수행 됩니다. 

    **필터** 를 선택 하 여 검색을 필터링 할 수 있습니다. 아래 예제에서는 화면에 있는 텍스트만 (OCR)으로 표시 되는 "Microsoft"를 검색 합니다.

    :::image type="content" source="./media/video-indexer-search/filter.png" alt-text="필터, 텍스트만":::
1. **검색** 을 눌러 결과를 확인 합니다.

    :::image type="content" source="./media/video-indexer-search/results.png" alt-text="비디오 검색 결과":::

    결과 중 하나를 선택 하면 플레이어는 비디오에서 정확한 순간을 표시 합니다.
1. 비디오에서 **재생** 을 클릭 하거나 원래 검색 결과 중 하나를 선택 하 여 비디오에 대 한 요약 된 정보를 확인 하 고 검색 합니다. 

    **정보** 를 보고, 검색 하 고, 편집할 수 있습니다. 정보 중 하나를 선택 하는 경우 플레이어는 비디오에서 정확한 순간을 가져옵니다.  

    :::image type="content" source="./media/video-indexer-search/insights.png" alt-text="비디오에 대 한 정보 보기, 검색 및 편집":::

    Video Indexer 위젯을 통해 비디오를 포함 하는 경우 앱에서 플레이어/통찰력 보기 및 동기화를 달성할 수 있습니다. 자세한 내용은 [앱에 Video Indexer 위젯 포함](video-indexer-embed-widgets.md)을 참조 하세요.
1. **타임 라인** 탭을 클릭 하 여 기록을 보고, 검색 하 고, 편집할 수 있습니다. 

    :::image type="content" source="./media/video-indexer-search/timeline.png" alt-text="비디오의 증명서 보기, 검색 및 편집":::

    텍스트를 편집 하려면 오른쪽 위 모서리에서 **편집** 을 선택 하 고 필요에 따라 텍스트를 변경 합니다. 

    오른쪽 위 모서리에서 적절 한 옵션을 선택 하 여 기록을 변환 하 고 다운로드할 수도 있습니다. 

## <a name="embed-download-create-projects"></a>프로젝트 포함, 다운로드, 만들기

비디오에서 **</>포함** 을 선택 하 여 비디오를 포함할 수 있습니다. 자세한 내용은 [응용 프로그램에 시각적 위젯 포함](video-indexer-embed-widgets.md)을 참조 하세요.

비디오에서 **다운로드** 를 클릭 하 여 원본 비디오, 비디오에 대 한 정보를 다운로드할 수 있습니다.

**편집기에서 열기** 를 클릭 하 여 특정 선 및 분의 비디오를 기반으로 클립을 만들 수 있습니다. 그런 다음 비디오를 편집 하 고 프로젝트를 저장 합니다. 자세한 내용은 [비디오의 심층 정보 사용](use-editor-create-project.md)을 참조 하세요.

:::image type="content" source="./media/video-indexer-search/embed-download-create-projects.png" alt-text="비디오의 포함, 다운로드, 프로젝트 만들기":::

## <a name="next-steps"></a>다음 단계

[Video Indexer REST API를 사용 하 여 콘텐츠 처리](video-indexer-use-apis.md)
