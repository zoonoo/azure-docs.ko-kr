---
title: Azure 미디어 인코더에서 미디어 인코더 표준으로 마이그레이션 | 마이크로 소프트 문서
description: 이 항목에서는 Azure 미디어 인코더에서 미디어 인코더 표준 미디어 프로세서로 마이그레이션하는 방법에 대해 설명합니다.
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
ms.date: 08/21/2019
ms.author: juliako
ms.openlocfilehash: f8fe1b13db6473e80f0d7cdc638b775a0c8062c7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76513504"
---
# <a name="migrate-from-azure-media-encoder-to-media-encoder-standard"></a>Azure 미디어 인코더에서 미디어 인코더 표준으로 마이그레이션

이 문서에서는 레거시 Azure 미디어 인코더(AME) 미디어 프로세서(사용 중지 중)에서 미디어 인코더 표준 미디어 프로세서로 마이그레이션하는 단계에 대해 설명합니다. 사용 중지 날짜에 대 한이 [레거시 구성 요소](legacy-components.md) 항목을 참조 합니다.

AME로 파일을 인코딩할 때 고객은 일반적으로 와 `H264 Adaptive Bitrate MP4 Set 1080p`같은 명명된 사전 설정 문자열을 사용했습니다. 마이그레이션하려면 AME 대신 **미디어 인코더 표준** 미디어 프로세서와 와 같은 `H264 Multiple Bitrate 1080p`동일한 [시스템 사전 설정](media-services-mes-presets-overview.md) 중 하나를 사용하려면 코드를 업데이트해야 합니다. 

## <a name="migrating-to-media-encoder-standard"></a>미디어 인코더 표준으로 마이그레이션

레거시 미디어 프로세서를 사용하는 일반적인 C# 코드 샘플은 다음과 같습니다. 

```csharp
// Declare a new job. 
IJob job = _context.Jobs.Create("AME Job"); 
// Get a media processor reference, and pass to it the name of the  
// processor to use for the specific task. 
IMediaProcessor processor = GetLatestMediaProcessorByName("Azure Media Encoder"); 

// Create a task with the encoding details, using a string preset. 
// In this case " H264 Adaptive Bitrate MP4 Set 1080p" preset is used. 
ITask task = job.Tasks.AddNew("My encoding task", 
    processor, 
    " H264 Adaptive Bitrate MP4 Set 1080p", 
    TaskOptions.None); 
```

다음은 미디어 인코더 표준을 사용하는 업데이트된 버전입니다.

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

스키마를 사용하여 AME에 대한 고유한 인코딩 사전 설정을 만든 경우 [미디어 인코더 표준에 해당하는 스키마가](media-services-mes-schema.md)있습니다. 이전 설정을 새 인코더에 매핑하는 방법에 대한 질문이 있는 경우 다음을 통해 문의하시기 바랍니다.mailto:amshelp@microsoft.com  
## <a name="known-differences"></a>알려진 차이점 

미디어 인코더 표준은 더 강력하고 신뢰할 수 있으며 성능이 우수하며 레거시 AME 인코더보다 더 나은 품질의 출력을 생성합니다. 또한, 

* 미디어 인코더 표준은 AME와 다른 명명 규칙을 가진 출력 파일을 생성합니다.
* 미디어 인코더 표준은 입력 파일 [메타데이터](media-services-input-metadata-schema.md) 및 출력 [파일 메타데이터를](media-services-output-metadata-schema.md)포함하는 파일과 같은 아티팩트를 생성합니다.

## <a name="next-steps"></a>다음 단계

* [레거시 구성 요소](legacy-components.md)
* [가격 책정 페이지](https://azure.microsoft.com/pricing/details/media-services/#encoding)
