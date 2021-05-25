---
title: Azure Media Encoder에서 Media Encoder Standard로 마이그레이션
description: 이 항목에서는 Azure Media Encoder에서 Media Encoder Standard 미디어 프로세서로 마이그레이션하는 방법에 대해 설명합니다.
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
ms.openlocfilehash: 4f528cea9b475c0158524ad9b46623a78df5761d
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103016205"
---
# <a name="migrate-from-azure-media-encoder-to-media-encoder-standard"></a>Azure Media Encoder에서 Media Encoder Standard로 마이그레이션

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

이 문서에서는 기존 Azure Media Encoder(AME) 미디어 프로세서(사용 중지)에서 Media Encoder Standard 미디어 프로세서로 마이그레이션하는 단계에 대해 설명합니다. 사용 중지 날짜는 이 [레거시 구성 요소](legacy-components.md) 항목을 참조하세요.

AME로 파일을 인코딩할 때 고객은 일반적으로 `H264 Adaptive Bitrate MP4 Set 1080p`와 같은 명명된 사전 설정 문자열을 사용했습니다. 마이그레이션하려면 AME 대신 **Media Encoder Standard** 미디어 프로세서 및 `H264 Multiple Bitrate 1080p`와 같은 동일한 [시스템 사전 설정](media-services-mes-presets-overview.md) 중 하나를 사용하도록 코드를 업데이트해야 합니다. 

## <a name="migrating-to-media-encoder-standard"></a>Media Encoder Standard로 마이그레이션

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

AME 스키마를 사용하여 고유한 AME 인코딩 사전 설정을 생성한 경우 [Media Encoder Standard에 대해 동일한 스키마](media-services-mes-schema.md)가 있습니다. 이전 설정을 새 인코더에 매핑하는 방법에 대해 궁금한 점이 있으면 mailto:amshelp@microsoft.com을 통해 문의하세요.  
## <a name="known-differences"></a>알려진 차이 

Media Encoder Standard는 레거시 AME 인코더보다 강력하고 안정적이고 성능이 우수하며 우수한 품질의 출력을 제공합니다. 다음 액세스 권한도 필요합니다. 

* Media Encoder Standard는 AME와 다른 명명 규칙을 사용하여 출력 파일을 생성합니다.
* Media Encoder Standard는 [입력 파일 메타데이터](media-services-input-metadata-schema.md) 및 [출력 파일 메타데이터](media-services-output-metadata-schema.md)를 포함하는 파일과 같은 아티팩트를 생성합니다.

## <a name="next-steps"></a>다음 단계

* [레거시 구성 요소](legacy-components.md)
* [가격 책정 페이지](https://azure.microsoft.com/pricing/details/media-services/#encoding)
