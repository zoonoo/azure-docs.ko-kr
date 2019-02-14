---
title: 비디오 내 정확한 시점 찾기 - Video Indexer
titlesuffix: Azure Media Services
description: 이 항목에서는 Video Indexer를 사용하여 비디오 내에서 정확한 시점을 찾는 방법을 설명합니다.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.topic: article
ms.date: 02/10/2019
ms.author: juliako
ms.openlocfilehash: d2c2e87b3dade68dc311aa38743c11a60a69f68b
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/11/2019
ms.locfileid: "56004555"
---
# <a name="find-exact-moments-within-videos"></a>비디오 내 정확한 시점 찾기

이 항목에서는 비디오 내에서 정확한 시점을 찾을 수 있는 검색 옵션에 대해 설명합니다.

1. [Video Indexer](https://www.videoindexer.ai/) 웹 사이트로 이동하고 로그인합니다.
2. 계정에 있는 모든 비디오 중에서 검색합니다.

    다음 예에서는 Scott Hanelman이 Channel9에서 만든 모든 비디오를 검색했습니다.

    ![검색](./media/video-indexer-search/video-indexer-search01.png)
3. 비디오의 요약된 인사이트를 검색합니다.

    다음으로, 비디오에서 **재생**을 클릭하여 비디오 내에서 검색할 수 있습니다. 그런 다음, **검색** 탭을 선택하여 비디오 내에서 검색할 수 있습니다. 예를 들어 "ID 보호" 텍스트가 사용되는 모든 위치를 검색했습니다. 

    ![검색](./media/video-indexer-search/video-indexer-search02.png)

    결과 중 하나를 클릭하면 플레이어에서 비디오의 해당 시점으로 이동합니다. 애플리케이션에서 플레이어/인사이트 보기 및 동기화를 수행할 수 있습니다. 자세한 내용은 [애플리케이션에 Video Indexer 위젯 포함](video-indexer-embed-widgets.md)을 참조하세요. 
4. 비디오에 대한 자세한 분석을 검색합니다.

    찾은 비디오를 기반으로 하여 사용자 고유의 분석을 만들려면 **편집** 단추를 누릅니다. 이 페이지에는 비디오에 대한 전체 분석이 표시됩니다. 분석 내에서 검색하여 관심 있는 줄만 표시할 수 있습니다. 자세한 내용은 [Video Indexer 인사이트 보기 및 편집](video-indexer-view-edit.md)을 참조하세요.

    이 예에서는 "ID 보호" 텍스트를 검색했습니다. 또한 아래 화면과 같이 추가 필터도 적용했습니다.

    ![검색](./media/video-indexer-search/video-indexer-search03.png)

## <a name="next-steps"></a>다음 단계 

사용하려는 비디오를 찾으면 다음 항목 중 하나에서 설명한 대로 비디오를 계속 처리할 수 있습니다. 

- [기존 비디오에 기반하여 새 비디오 인사이트 만들기](video-indexer-create-new.md)
- [Video Indexer REST API를 사용하여 콘텐츠 처리](video-indexer-use-apis.md)
- [애플리케이션에 시각적 위젯 포함](video-indexer-embed-widgets.md)

## <a name="see-also"></a>참고 항목

[Video Indexer 개요](video-indexer-overview.md)
