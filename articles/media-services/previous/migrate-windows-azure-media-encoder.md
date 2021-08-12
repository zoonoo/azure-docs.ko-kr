---
title: Windows Azure Media Encoder에서 Media Encoder Standard로 마이그레이션 | Microsoft Docs
description: 이 항목에서는 Windows Azure Media Encoder에서 Media Encoder Standard 미디어 프로세서로 마이그레이션하는 방법에 대해 설명합니다.
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
ms.date: 3/10/2021
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: 0a80d25145162c81ef999f8af1015cd590d5a090
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103011122"
---
# <a name="migrate-from-windows-azure-media-encoder-to-media-encoder-standard"></a>Windows Azure Media Encoder에서 Media Encoder Standard로 마이그레이션

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

이 문서에서는 기존 WAME(Windows Azure Media Encoder) 미디어 프로세서(사용 중지)에서 Media Encoder Standard 미디어 프로세서로 마이그레이션하는 단계에 대해 설명합니다. 사용 중지 날짜는 이 [레거시 구성 요소](legacy-components.md) 항목을 참조하세요.

WAME로 파일을 인코딩할 때 고객은 일반적으로 `H264 Adaptive Bitrate MP4 Set 1080p`와 같은 명명된 사전 설정 문자열을 사용했습니다. 마이그레이션하려면 WAME 대신 **Media Encoder Standard** 미디어 프로세서 및 `H264 Multiple Bitrate 1080p`와 같은 동일한 [시스템 사전 설정](media-services-mes-presets-overview.md) 중 하나를 사용하도록 코드를 업데이트해야 합니다. 

## <a name="migrating-to-media-encoder-standard"></a>Media Encoder Standard로 마이그레이션

레거시 구성 요소를 사용하는 일반적인 C# 코드 샘플은 다음과 같습니다. 

```csharp
// Declare a new job. 
IJob job = _context.Jobs.Create("WAME Job"); 
// Get a media processor reference, and pass to it the name of the  
// processor to use for the specific task. 
IMediaProcessor processor = GetLatestMediaProcessorByName("Windows Azure Media Encoder"); 

// Create a task with the encoding details, using a string preset. 
// In this case " H264 Adaptive Bitrate MP4 Set 1080p" preset is used. 
ITask task = job.Tasks.AddNew("My encoding task", 
    processor, 
    "H264 Adaptive Bitrate MP4 Set 1080p", 
    TaskOptions.None); 
```

Media Encoder Standard를 사용하는 업데이트 버전은 다음과 같습니다.

```csharp
// Declare a new job. 
IJob job = _context.Jobs.Create("Media Encoder Standard Job"); 
// Get a media processor reference, and pass to it the name of the  
// processor to use for the specific task. 
IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard"); 

// Create a task with the encoding details, using a string preset. 
// In this case " H264 Multiple Bitrate 1080p" preset is used. 
ITask task = job.Tasks.AddNew("My encoding task", 
    processor, 
    "H264 Multiple Bitrate 1080p", 
    TaskOptions.None); 
```

### <a name="advanced-scenarios"></a>고급 시나리오 

WAME 스키마를 사용하여 고유한 WAME용 인코딩 사전 설정을 만든 경우 [Media Encoder Standard에 대해 동일한 스키마](media-services-mes-schema.md)가 있습니다.

## <a name="known-differences"></a>알려진 차이점 

Media Encoder Standard는 레거시 WAME 인코더보다 강력하고 안정적이고 성능이 우수하며 우수한 품질의 출력을 제공합니다. 또한, 

* Media Encoder Standard는 WAME와 다른 명명 규칙을 사용하여 출력 파일을 생성합니다.
* Media Encoder Standard는 [입력 파일 메타데이터](media-services-input-metadata-schema.md) 및 [출력 파일 메타데이터](media-services-output-metadata-schema.md)를 포함하는 파일과 같은 아티팩트를 생성합니다.
* [가격 책정 페이지](https://azure.microsoft.com/pricing/details/media-services/#encoding)(특히 FAQ 섹션)에 설명된 대로 Media Encoder Standard를 사용하여 비디오를 인코딩하면 출력으로 생성되는 파일의 기간에 따라 요금이 청구됩니다. WAME를 사용하면 입력 비디오 파일과 출력 비디오 파일의 크기에 따라 요금이 청구됩니다.

## <a name="need-help"></a>도움 필요 시

[새 지원 요청](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)으로 이동하여 지원 티켓을 열 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [레거시 구성 요소](legacy-components.md)
* [가격 책정 페이지](https://azure.microsoft.com/pricing/details/media-services/#encoding)
