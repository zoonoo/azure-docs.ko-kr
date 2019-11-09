---
title: 비디오 내 정확한 시점 찾기 - Video Indexer
titleSuffix: Azure Media Services
description: 이 항목에서는 Video Indexer를 사용하여 비디오 내에서 정확한 시점을 찾는 방법을 설명합니다.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: juliako
ms.openlocfilehash: 8ebf7606d1a0932cc2254e14a40a2fff550fd35f
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73833787"
---
# <a name="find-exact-moments-within-videos"></a>비디오 내 정확한 시점 찾기

이 항목에서는 비디오 내에서 정확한 시점을 찾을 수 있는 검색 옵션에 대해 설명합니다.

1. [Video Indexer](https://www.videoindexer.ai/) 웹 사이트로 이동하고 로그인합니다.
2. 계정에 있는 모든 비디오 중에서 검색합니다.

    다음 예제에서는 보안에 대해 설명 하 고 Satya 표시 되는 모든 비디오를 검색 합니다.

    ![검색](./media/video-indexer-search/video-indexer-search01.png)
3. 비디오의 요약된 인사이트를 검색합니다.

    다음으로, 비디오에서 **재생**을 클릭하여 비디오 내에서 검색할 수 있습니다. 그런 다음, **검색** 탭을 선택하여 비디오 내에서 검색할 수 있습니다. 

    다음 예제에서는 선택한 비디오 내에서 "보안"을 검색 합니다.

    ![검색](./media/video-indexer-search/video-indexer-search02.png)

    결과 중 하나를 클릭하면 플레이어에서 비디오의 해당 시점으로 이동합니다. 애플리케이션에서 플레이어/인사이트 보기 및 동기화를 수행할 수 있습니다. 자세한 내용은 [애플리케이션에 Video Indexer 위젯 포함](video-indexer-embed-widgets.md)을 참조하세요. 
4. 비디오에 대한 자세한 분석을 검색합니다.
    
    찾은 비디오를 기준으로 클립을 직접 만들려면 **편집** 단추를 누릅니다. 이 페이지에서는 정보를 필터로 사용 하 여 비디오를 표시 합니다. 자세한 내용은 [Video Indexer 인사이트 보기 및 편집](video-indexer-view-edit.md)을 참조하세요. 

    비디오 내에서 검색 하 여 관심 있는 줄만 표시 하 고, 측면 정보 활용을 사용 하 여 보려는 부분을 필터링 할 수 있습니다. 완료 되 면 클립을 미리 보고 **게시** 를 눌러 갤러리에 표시 되는 새 클립을 만들 수 있습니다.
    
    다음 예제에서는 "mixed reality" 텍스트를 검색 했습니다. 또한 아래 화면과 같이 추가 필터도 적용했습니다.
    
    ![검색](./media/video-indexer-search/video-indexer-search03.png)

## <a name="next-steps"></a>다음 단계 

사용하려는 비디오를 찾으면 다음 항목 중 하나에서 설명한 대로 비디오를 계속 처리할 수 있습니다. 

- [비디오의 심층 통찰력 사용](use-editor-create-project.md)
- [Video Indexer REST API를 사용하여 콘텐츠 처리](video-indexer-use-apis.md)
- [애플리케이션에 시각적 위젯 포함](video-indexer-embed-widgets.md)

## <a name="see-also"></a>참고 항목

[Video Indexer 개요](video-indexer-overview.md)
