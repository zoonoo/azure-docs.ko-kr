---
title: Video Indexer와 Azure Media Services v3 미리 설정 비교 | Microsoft Docs
description: 이 항목에서는 Video Indexer와 Azure Media Services v3 미리 설정을 비교합니다.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/07/2019
ms.author: juliako
ms.openlocfilehash: 2c98f6d12f4868e5f90874fe3210fe5368f7ca2d
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/08/2019
ms.locfileid: "59270339"
---
# <a name="compare-azure-media-services-v3-presets-and-video-indexer"></a>Azure Media Services v3 미리 설정 및 Video Indexer 비교 

이 문서에서는 **Video Indexer API**와 **Media Services v3 API**의 기능을 비교합니다. 

현재는에서 제공 하는 기능 간에 중복 된 [Video Indexer Api](https://api-portal.videoindexer.ai/) 및 [Media Services v3 Api](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01/Encoding.json)합니다. 다음 표에는 차이점과 유사점을 이해하기 위한 현재의 지침이 나와 있습니다. 

## <a name="compare"></a>비교

|기능|Video Indexer API |Media Services v3 API의<br/>비디오 분석기 및 오디오 분석기 미리 설정|
|---|---|---|
|미디어 인사이트|[향상된](video-indexer-output-json-v2.md) |[기본 사항](../latest/intelligence-concept.md)|
|환경|지원되는 기능의 전체 목록 보기: <br/> [개요](video-indexer-overview.md)|비디오 인사이트만 반환합니다.|
|결제|[Media Services 가격](https://azure.microsoft.com/pricing/details/media-services/#analytics)|[Media Services 가격](https://azure.microsoft.com/pricing/details/media-services/#analytics)|
|규정 준수|[ISO 27001](https://www.microsoft.com/TrustCenter/Compliance/ISO-IEC-27001), [ISO 27018](https://www.microsoft.com/trustcenter/Compliance/ISO-IEC-27018), [SOC 1,2,3](https://www.microsoft.com/TrustCenter/Compliance/SOC)를 [HIPAA](https://www.microsoft.com/trustcenter/compliance/hipaa)를 [FedRAMP](https://www.microsoft.com/TrustCenter/Compliance/fedramp)를 [PCI](https://www.microsoft.com/trustcenter/compliance/pci), 및 [ HITRUST](https://www.microsoft.com/TrustCenter/Compliance/hitrust) 인증 합니다. 최신 업데이트를 방문 [Video Indexer의 인증 상태를 현재](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942)합니다.|Media Services는 많은 인증을 준수합니다. [Azure Compliance Offerings.pdf](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942/file/178110/23/Microsoft%20Azure%20Compliance%20Offerings.pdf)를 살펴보고, "Media Services"를 검색하여 관심 인증서를 준수하는지 확인합니다.|
|평가판|미국 동부|사용할 수 없음|
|지역 가용성|미국 동부 2, 미국 중남부, 미국 서 부 2, 북유럽, 서유럽, 동남 아시아, 동남 아시아 및 오스트레일리아 동부입니다.  최신 업데이트에 대 한 참조를 [지역별 제품](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services) 페이지입니다.|[Azure 상태](https://azure.microsoft.com/global-infrastructure/services/?products=media-services) 참조|

## <a name="next-steps"></a>다음 단계

[비디오 인덱서 개요](video-indexer-overview.md)

[Media Services v3 개요](../latest/media-services-overview.md)
