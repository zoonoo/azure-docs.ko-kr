---
title: Azure 미디어 서비스 레거시 구성 요소 | 마이크로 소프트 문서
description: 이 항목에서는 Azure Media Services 레거시 구성 요소에 대해 설명합니다.
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
ms.date: 02/27/2020
ms.author: juliako
ms.openlocfilehash: 94a70a1234d902787f248890f0cb538a4ba9c2f9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77921082"
---
# <a name="azure-media-services-legacy-components"></a>Azure 미디어 서비스 레거시 구성 요소

시간이 지남에 따라 미디어 서비스 구성 요소가 꾸준히 개선되고 개선되었습니다. 따라서 일부 레거시 구성 요소는 폐기되었습니다. 다음 문서에서 레거시 구성 요소에서 현재 구성 요소로 응용 프로그램을 마이그레이션하는 방법에 대한 지침을 찾을 수 있습니다.
 
## <a name="retirement-plans-of-legacy-components-and-migration-guidance"></a>레거시 구성 요소 및 마이그레이션 지침의 폐기 계획

Windows Azure 미디어 인코더(WAME) 및 Azure 미디어 *인코더(AME)* 미디어 프로세서의 사용 중단을 발표합니다. *Windows Azure Media Encoder* 이러한 프로세서는 2020년 3월 31일에 폐기됩니다.

* [Windows Azure 미디어 인코더에서 미디어 인코더 표준으로 마이그레이션](migrate-windows-azure-media-encoder.md)
* [Azure 미디어 인코더에서 미디어 인코더 표준으로 마이그레이션](migrate-azure-media-encoder.md)

또한 다음과 같은 미디어 분석 미디어 프로세서의 은퇴를 발표합니다. 
 
|미디어 프로세서 이름|사용 중지 날짜|추가적인 참고 사항|
|---|---|
|[Azure Media Indexer 2](media-services-process-content-with-indexer2.md)|2020년 1월 1일|이 미디어 프로세서는 [Azure 미디어 서비스 비디오 인덱서로](https://docs.microsoft.com/azure/media-services/video-indexer/)대체됩니다. 자세한 내용은 [Azure 미디어 인덱서 2에서 Azure 미디어 서비스 비디오 인덱서로 마이그레이션을](migrate-indexer-v1-v2.md)참조하십시오.|
|[Azure Media Indexer](media-services-index-content.md)|2023년 3월 1일|이 미디어 프로세서는 [Azure 미디어 서비스 비디오 인덱서로](https://docs.microsoft.com/azure/media-services/video-indexer/)대체됩니다. 자세한 내용은 [Azure 미디어 인덱서에서 Azure 미디어 서비스 비디오 인덱서로 마이그레이션을](migrate-indexer-v1-v2.md) 참조하십시오.|
|[모션 감지](media-services-motion-detection.md)|2020년 6월 1일|현재 교체 계획이 없습니다.|
|[비디오 요약](media-services-video-summarization.md)|2020년 6월 1일|현재 교체 계획이 없습니다.|
|[비디오 광학 문자 인식](media-services-video-optical-character-recognition.md)|2020년 6월 1일|이 미디어 프로세서는 [Azure 미디어 서비스 비디오 인덱서로](https://docs.microsoft.com/azure/media-services/video-indexer/)대체됩니다. 또한 Azure [미디어 서비스 v3 API를](https://docs.microsoft.com/azure/media-services/latest/analyzing-video-audio-files-concept)사용하는 것이 좋습니다. <br/>[Azure 미디어 서비스 v3 사전 설정 및 비디오 인덱서 비교](https://docs.microsoft.com/azure/media-services/video-indexer/compare-video-indexer-with-media-services-presets) 를 참조하십시오.|
|[얼굴 감지기](media-services-face-and-emotion-detection.md)|2020년 6월 1일|이 미디어 프로세서는 [Azure 미디어 서비스 비디오 인덱서로](https://docs.microsoft.com/azure/media-services/video-indexer/)대체됩니다. 또한 Azure [미디어 서비스 v3 API를](https://docs.microsoft.com/azure/media-services/latest/analyzing-video-audio-files-concept)사용하는 것이 좋습니다. <br/>[Azure 미디어 서비스 v3 사전 설정 및 비디오 인덱서 비교](https://docs.microsoft.com/azure/media-services/video-indexer/compare-video-indexer-with-media-services-presets) 를 참조하십시오.|
|[콘텐츠 진행자](media-services-content-moderation.md)|2020년 6월 1일|이 미디어 프로세서는 [Azure 미디어 서비스 비디오 인덱서로](https://docs.microsoft.com/azure/media-services/video-indexer/)대체됩니다. 또한 Azure [미디어 서비스 v3 API를](https://docs.microsoft.com/azure/media-services/latest/analyzing-video-audio-files-concept)사용하는 것이 좋습니다. <br/>[Azure 미디어 서비스 v3 사전 설정 및 비디오 인덱서 비교](https://docs.microsoft.com/azure/media-services/video-indexer/compare-video-indexer-with-media-services-presets) 를 참조하십시오.|

## <a name="next-steps"></a>다음 단계

[Media Services v2에서 v3로 이동하기 위한 마이그레이션 지침](../latest/migrate-from-v2-to-v3.md)
