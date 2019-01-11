---
title: Azure Media Services 엔터티 개요 - Azure | Microsoft Docs
description: 이 문서에서는 Azure Media Services 엔터티에 대해 간략히 설명합니다.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 12/20/2018
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 1d309bb550282d5245a2fbf74f14a931cf5c2279
ms.sourcegitcommit: 21466e845ceab74aff3ebfd541e020e0313e43d9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/21/2018
ms.locfileid: "53745729"
---
# <a name="azure-media-services-entities-overview"></a>Azure Media Services 엔터티 개요

이 문서에서는 Azure Media Services 엔터티에 대해 간략히 설명하며, 각 엔터티에 대한 자세한 내용이 있는 문서를 가리킵니다. 

| 항목 | 설명 |
|---|---|
| [계정 필터 및 자산 필터](filters-dynamic-manifest-overview.md)|고객에게 콘텐츠를 전송하는 경우(라이브 이벤트 또는 주문형 비디오 스트리밍) 클라이언트에는 기본 자산의 매니페스트 파일에 설명된 것보다 더 많은 유연성이 필요할 수 있습니다. Azure Media Services를 사용하면 [계정 필터](https://docs.microsoft.com/rest/api/media/accountfilters) 및 [자산 필터](https://docs.microsoft.com/rest/api/media/assetfilters)를 정의할 수 있습니다. 그런 다음, 미리 정의된 필터 기반의 **동적 매니페스트**를 사용합니다. |
| [Assets](assets-concept.md)|[자산](https://docs.microsoft.com/rest/api/media/assets) 엔터티에는 디지털 파일(비디오, 오디오, 이미지, 썸네일 컬렉션, 텍스트 트랙 및 선택 자막 파일 포함)과 이러한 파일에 대한 메타데이터가 포함되어 있습니다. 디지털 파일이 자산에 업로드되면 Media Services 콘텐츠 워크플로 인코딩, 스트리밍, 분석에 사용할 수 있습니다.|
| [콘텐츠 키 정책](content-key-policy-concept.md)|Media Services를 사용하여 컴퓨터에서 떠날 때부터 저장, 처리 및 전송에 이르기까지 미디어를 보호할 수 있습니다. Media Services를 사용하면 Advanced Encryption Standard(AES-128) 또는 Microsoft PlayReady, Google Widevine 및 Apple FairPlay 등 세 가지 주요 DRM(디지털 권한 관리) 시스템 중 하나로 동적 암호화된 라이브 콘텐츠 및 주문형 콘텐츠를 제공할 수 있습니다. 또한 Media Services는 인증된 클라이언트에게 AES 키 및DRM(PlayReady, Widevine 및 FairPlay) 라이선스를 배달하는 서비스를 제공합니다.|
| [LiveEvents 및 LiveOutputs](live-events-outputs-concept.md)|Media Services를 사용하면 Azure 클라우드에서 고객에게 라이브 이벤트를 전송할 수 있습니다. Media Services v3에서 라이브 스트리밍 이벤트를 구성하려면 [라이브 이벤트](https://docs.microsoft.com/rest/api/media/liveevents) 및 [라이브 출력](https://docs.microsoft.com/rest/api/media/liveoutputs)에 대해 알고 있어야 합니다.|
| [스트리밍 엔드포인트](streaming-endpoint-concept.md)|[스트리밍 엔드포인트](https://docs.microsoft.com/rest/api/media/streamingendpoints) 엔터티는 추가 배포를 위해 CDN(Content Delivery Network) 또는 클라이언트 플레이어 애플리케이션에 콘텐츠를 직접 전송할 수 있는 스트리밍 서비스를 나타냅니다. 스트리밍 엔드포인트 서비스의 아웃 바운드 스트림은 라이브 스트림 또는 Media Services 계정에 주문형 비디오 자산이 될 수 있습니다. Media Services 계정을 만들 경우 **기본** 스트리밍 엔드포인트가 중지됨 상태로 만들어집니다. **기본** 스트리밍 엔드포인트는 삭제할 수 없습니다. 계정에서 추가 스트리밍 엔드포인트를 만들 수 있습니다. 비디오 스트리밍을 시작하려면 비디오를 스트림하려는 스트리밍 엔드포인트를 시작해야 합니다. |
| [스트리밍 로케이터](streaming-locators-concept.md)|인코딩된 비디오 또는 오디오 파일을 재생하는 데 사용할 수 있는 URL을 클라이언트에 제공하려면 [스트리밍 로케이터](https://docs.microsoft.com/rest/api/media/streaminglocators)를 만들고 스트리밍 URL을 작성해야 합니다.|
| [스트리밍 정책](streaming-policy-concept.md)| [스트리밍 정책](https://docs.microsoft.com/rest/api/media/streamingpolicies)을 사용하면 StreamingLocators에 대한 스트리밍 프로토콜 및 암호화 옵션을 정의할 수 있습니다. 만든 사용자 지정 스트리밍 정책의 이름을 지정하거나 Media Services에서 제공하는 미리 정의된 스트리밍 정책 중 하나를 사용할 수 있습니다. <br/><br/>사용자 지정 스트리밍 정책을 사용하는 경우 동일한 암호화 옵션 및 프로토콜이 필요할 때마다 Media Service 계정에 대해 이러한 정책의 제한된 세트를 설계하고 스트리밍 로케이터에 해당 정책을 다시 사용해야 합니다. 각 스트리밍 로케이터에 대해 새 스트리밍 정책을 만들지 않아야 합니다.|
| [Transforms 및 Jobs](transforms-jobs-concept.md)|[Transforms](https://docs.microsoft.com/rest/api/media/transforms)는 비디오 인코딩 또는 분석에 대한 일반적인 작업을 구성하는 데 사용할 수 있습니다. 각 **변환**은 비디오 또는 오디오 파일을 처리하는 작업의 작성법 또는 워크플로를 설명합니다.<br/><br/>[작업](https://docs.microsoft.com/rest/api/media/jobs)은 **변환**을 특정 입력 비디오 또는 오디오 콘텐츠에 적용하기 위한 Azure Media Services에 대한 실제 요청입니다. **작업**은 입력 비디오의 위치 및 출력 위치와 같은 정보를 지정합니다. 입력 비디오의 위치는 HTTPS URL, SAS URL 또는 자산을 사용하여 지정할 수 있습니다.|

## <a name="next-steps"></a>다음 단계

[파일 스트리밍](stream-files-dotnet-quickstart.md)
