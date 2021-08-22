---
title: Azure Media Services의 스트리밍 정책
description: 이 문서에서는 스트리밍 정책이란 무엇이며 Azure Media Services에서 스트리밍 정책을 사용하는 방법을 설명합니다.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: conceptual
ms.date: 05/28/2019
ms.author: inhenkel
ms.openlocfilehash: c6cd9c5c56f261bce4c04f6e441023c1b1457bbc
ms.sourcegitcommit: 9f1a35d4b90d159235015200607917913afe2d1b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/21/2021
ms.locfileid: "122634223"
---
# <a name="streaming-policies"></a>스트리밍 정책

Azure Media Services v3에서 [스트리밍 정책](/rest/api/media/streamingpolicies)을 사용하여 [스트리밍 로케이터](stream-streaming-locators-concept.md)의 스트리밍 프로토콜 및 암호화 옵션을 정의할 수 있습니다. Media Services v3에서는 미리 정의된 스트리밍 정책을 제공하므로 평가판 또는 프로덕션에 직접 사용할 수 있습니다. 

현재 사용 가능한 미리 정의된 스트리밍 정책:<br/>
* 'Predefined_DownloadOnly'
* 'Predefined_ClearStreamingOnly'
* 'Predefined_DownloadAndClearStreaming'
* 'Predefined_ClearKey'
* 'Predefined_MultiDrmCencStreaming' 
* 'Predefined_MultiDrmStreaming'

다음 "의사 결정 트리"는 시나리오에 대해 미리 정의된 스트리밍 정책을 선택하는 데 도움이 됩니다.

> [!IMPORTANT]
> * 날짜/시간 형식의 **스트리밍 정책** 속성은 언제나 UTC 형식입니다.
> * Media Services 계정에 대한 제한된 정책 세트를 설계하고 동일한 옵션이 필요할 때마다 스트리밍 로케이터에 해당 세트를 다시 사용하는 것이 좋습니다. 자세한 내용은 [할당량 및 한도](limits-quotas-constraints-reference.md)를 참조하세요.

## <a name="decision-tree"></a>의사 결정 트리

전체 크기로 보려면 이미지를 클릭합니다.  

[![시나리오에 대해 미리 정의된 스트리밍 정책을 선택할 수 있도록 설계된 의사 결정 트리를 보여주는 다이어그램.](./media/streaming-policy/large.png)](./media/streaming-policy/large.png#lightbox)

콘텐츠를 암호화하는 경우 [콘텐츠 키 정책](drm-content-key-policy-concept.md)을 만들어야 합니다. 일반 스트리밍 또는 다운로드에는 **콘텐츠 키 정책** 이 필요하지 않습니다. 

특별한 요구 사항이 있는 경우(예: 다른 프로토콜을 지정하거나 사용자 지정 키 전달 서비스를 사용해야 하거나 일반 오디오 트랙을 사용해야 하는 경우) 사용자 지정 스트리밍 정책을 [생성](/rest/api/media/streamingpolicies/create)할 수 있습니다. 

## <a name="get-a-streaming-policy-definition"></a>스트리밍 정책 정의 가져오기  

스트리밍 정책의 정의를 확인하려면 [Get](/rest/api/media/streamingpolicies/get)을 사용하여 정책 이름을 지정합니다. 예를 들면 다음과 같습니다.

### <a name="rest"></a>REST

요청:

```
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Media/mediaServices/contosomedia/streamingPolicies/clearStreamingPolicy?api-version=2018-07-01
```

응답:

```
{
  "name": "clearStreamingPolicy",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Media/mediaservices/contosomedia/streamingPolicies/clearStreamingPolicy",
  "type": "Microsoft.Media/mediaservices/streamingPolicies",
  "properties": {
    "created": "2018-08-08T18:29:30.8501486Z",
    "noEncryption": {
      "enabledProtocols": {
        "download": true,
        "dash": true,
        "hls": true,
        "smoothStreaming": true
      }
    }
  }
}
```

## <a name="filtering-ordering-paging"></a>필터링, 정렬, 페이징

[Media Services 엔터티 필터링, 순서 지정, 페이징](filter-order-page-entities-how-to.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

* [파일 스트리밍](stream-files-dotnet-quickstart.md)
* [AES-128 동적 암호화 및 키 전달 서비스 사용](drm-playready-license-template-concept.md)
* [DRM 동적 암호화 및 라이선스 배달 서비스 사용](drm-protect-with-drm-tutorial.md)
