---
title: Azure Media Services 레거시 구성 요소 | Microsoft Docs
description: 이 항목에서는 레거시 구성 요소 Azure Media Services 설명 합니다.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/21/2020
ms.author: inhenkel
ms.openlocfilehash: d85cbeb99264b5b730fe585fd39f658e6448467f
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/24/2020
ms.locfileid: "92515766"
---
# <a name="azure-media-services-legacy-components"></a>Azure Media Services 레거시 구성 요소

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

시간이 지남에 따라 Media Service 구성 요소를 개선 하 고 레거시 구성 요소를 사용 중지 합니다. 이 문서는 레거시 구성 요소에서 현재 구성 요소로 응용 프로그램을 마이그레이션하는 데 도움이 됩니다.
 
## <a name="retirement-plans-of-legacy-components-and-migration-guidance"></a>레거시 구성 요소 및 마이그레이션 지침의 사용 중지 계획

*Windows Azure Media Encoder* (wame) 및 *Azure Media Encoder* (AME) 미디어 프로세서는 더 이상 사용 되지 않습니다.

* [Windows Azure Media Encoder에서 Media Encoder Standard로 마이그레이션](migrate-windows-azure-media-encoder.md)
* [Azure Media Encoder에서 Media Encoder Standard로 마이그레이션](migrate-azure-media-encoder.md)

다음 미디어 분석 미디어 프로세서는 더 이상 사용 되지 않거나 더 이상 사용 되지 않을 예정입니다.

  
 
| **미디어 프로세서 이름** | **사용 중지 날짜** | **추가 참고 사항** |
| --- | --- | ---|
| Azure Media Indexer 2 | 2020 년 1 월 1 일 | 이 미디어 프로세서는 [Media Services V3 AudioAnalyzerPreset 기본 모드로](../latest/analyzing-video-audio-files-concept.md)바뀝니다. 자세한 내용은 [Azure Media Indexer 2에서 Azure Media Services Video Indexer로 마이그레이션](migrate-indexer-v1-v2.md)을 참조 하세요. |
| Azure Media Indexer | 2023 년 3 월 1 일 | 이 미디어 프로세서는 [Media Services V3 AudioAnalyzerPreset 기본 모드로](../latest/analyzing-video-audio-files-concept.md)바뀝니다. 자세한 내용은 [Azure Media Indexer 2에서 Azure Media Services Video Indexer로 마이그레이션](migrate-indexer-v1-v2.md)을 참조 하세요. |
| 동작 검색 | 6 월 1 일, 2020|지금은 교체 계획이 없습니다. |
| 비디오 요약 |6 월 1 일, 2020|지금은 교체 계획이 없습니다.|
| 비디오 광학 문자 인식 | 6 월 1 일, 2020 |이 미디어 프로세서는 [Video Indexer Azure Media Services](../video-indexer/index.yml)으로 대체 되었습니다. 또한 [Azure Media Services V3 API](../latest/analyzing-video-audio-files-concept.md)를 사용 하는 것이 좋습니다. <br/>[Azure Media Services v3 사전 설정 및 Video Indexer 비교를](../video-indexer/compare-video-indexer-with-media-services-presets.md)참조 하세요. |
| 얼굴 감지기 | 6 월 1 일, 2020 | 이 미디어 프로세서는 [Video Indexer Azure Media Services](../video-indexer/index.yml)으로 대체 되었습니다. 또한 [Azure Media Services V3 API](../latest/analyzing-video-audio-files-concept.md)를 사용 하는 것이 좋습니다. <br/>[Azure Media Services v3 사전 설정 및 Video Indexer 비교를](../video-indexer/compare-video-indexer-with-media-services-presets.md)참조 하세요. |
| Content Moderator | 6 월 1 일, 2020 |이 미디어 프로세서는 [Video Indexer Azure Media Services](../video-indexer/index.yml)으로 대체 되었습니다. 또한 [Azure Media Services V3 API](../latest/analyzing-video-audio-files-concept.md)를 사용 하는 것이 좋습니다. <br/>[Azure Media Services v3 사전 설정 및 Video Indexer 비교를](../video-indexer/compare-video-indexer-with-media-services-presets.md)참조 하세요. |

## <a name="next-steps"></a>다음 단계

[Media Services v2에서 v3로 이동하기 위한 마이그레이션 지침](../latest/migrate-from-v2-to-v3.md)
