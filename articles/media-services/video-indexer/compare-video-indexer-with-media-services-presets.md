---
title: Video Indexer 및 Azure Media Services v3 미리 설정 비교
description: 이 문서에서는 Video Indexer 기능과 Azure Media Services v3 미리 설정을 비교 합니다.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.subservice: video-indexer
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/24/2020
ms.author: juliako
ms.openlocfilehash: 0961aa3e573e511a6941bc59ddc335f64799abb4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87047336"
---
# <a name="compare-azure-media-services-v3-presets-and-video-indexer"></a>Azure Media Services v3 미리 설정 및 Video Indexer 비교 

이 문서에서는 **Video Indexer API**와 **Media Services v3 API**의 기능을 비교합니다. 

현재 [Video Indexer api](https://api-portal.videoindexer.ai/) 및 [Media Services v3 api](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01/Encoding.json)에서 제공 하는 기능 사이에 중복이 있습니다. 다음 표에는 차이점과 유사점을 이해하기 위한 현재의 지침이 나와 있습니다. 

## <a name="compare"></a>비교

|기능|Video Indexer API |Media Services v3 API의<br/>비디오 분석기 및 오디오 분석기 미리 설정|
|---|---|---|
|미디어 인사이트|[향상된](video-indexer-output-json-v2.md) |[기본 사항](../latest/analyzing-video-audio-files-concept.md)|
|환경|지원되는 기능의 전체 목록 보기: <br/> [개요](video-indexer-overview.md)|비디오 인사이트만 반환합니다.|
|결제|[Media Services 가격 책정](https://azure.microsoft.com/pricing/details/media-services/#analytics)|[Media Services 가격 책정](https://azure.microsoft.com/pricing/details/media-services/#analytics)|
|규정 준수|최신 규정 준수 업데이트는 [Azure 준수 Offerings.pdf](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942/file/178110/23/Microsoft%20Azure%20Compliance%20Offerings.pdf) 를 방문 하 고 "Video Indexer"를 검색 하 여 관심 있는 인증서를 준수 하는지 확인 합니다.|최신 규정 준수 업데이트는 [Azure 준수 Offerings.pdf](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942/file/178110/23/Microsoft%20Azure%20Compliance%20Offerings.pdf) 를 방문 하 고 "Media Services"를 검색 하 여 관심 있는 인증서를 준수 하는지 확인 합니다.|
|무료 평가판|미국 동부|사용할 수 없음|
|지역 가용성|[지역별 Cognitive Services 가용성](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services) 참조|[지역별 Media Services 가용성을](https://azure.microsoft.com/global-infrastructure/services/?products=media-services)참조 하세요.|

## <a name="next-steps"></a>다음 단계

[Video Indexer 개요](video-indexer-overview.md)

[Media Services v3 개요](../latest/media-services-overview.md)
