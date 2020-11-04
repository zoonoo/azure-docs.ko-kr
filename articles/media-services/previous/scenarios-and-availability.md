---
title: Microsoft Azure Media Services 일반적인 시나리오 | Microsoft Docs
description: 이 문서에서는 Microsoft Azure Media Services 시나리오에 대 한 개요를 제공 합니다.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/3/2020
ms.author: inhenkel
ms.openlocfilehash: 001c535a2b39898673f2d587ee807d43b4d5f60a
ms.sourcegitcommit: 99955130348f9d2db7d4fb5032fad89dad3185e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2020
ms.locfileid: "93348545"
---
# <a name="microsoft-azure-media-services-common-scenarios"></a>Microsoft Azure Media Services 일반적인 시나리오

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> Media Services v2에는 새로운 특징 또는 기능이 추가되지 않습니다. [Media Services v3](../latest/media-services-overview.md)의 최신 버전을 확인하세요. 또한 [v2에서 v3로의 마이그레이션 지침](../latest/migrate-from-v2-to-v3.md)을 참조하세요.

Microsoft AMS(Azure Media Services)는 다양한 클라이언트(예: TV, PC 및 모바일 디바이스)로의 주문형 및 라이브 스트리밍 배달을 위해 비디오 또는 오디오 콘텐츠를 안전하게 업로드, 저장, 인코딩 및 패키지할 수 있습니다.

이 문서에서는 콘텐츠를 라이브 또는 주문형으로 배달 하는 일반적인 시나리오를 보여 줍니다.

## <a name="overview"></a>개요

### <a name="prerequisites"></a>사전 요구 사항

* Azure 계정. 계정이 없는 경우 몇 분 만에 평가판 계정을 만들 수 있습니다. 자세한 내용은 [Azure 평가판](https://azure.microsoft.com)을 참조하세요.
* Azure Media Services 계정. 자세한 내용은 [계정 만들기](media-services-portal-create-account.md)를 참조하세요.
* 콘텐츠를 스트리밍하려는 스트리밍 엔드포인트가 **실행** 상태에 있어야 합니다.

    AMS 계정이 만들어질 때 **기본** 스트리밍 끝점은 **중지** 됨 상태에서 계정에 추가 됩니다. 콘텐츠 스트리밍을 시작하고 동적 패키징 및 동적 암호화를 활용하려면 스트리밍 엔드포인트가 **실행** 상태에 있어야 합니다.

### <a name="commonly-used-objects-when-developing-against-the-ams-odata-model"></a>AMS OData 모델에 대해 개발할 때 일반적으로 사용되는 개체

다음 이미지에서는 Media Services OData 모델에 대해 개발할 때 가장 일반적으로 사용되는 개체 중 일부를 보여 줍니다.

전체 크기로 보려면 이미지를 클릭합니다.  

[![Azure Media Services 개체 데이터 모델에 대해 개발할 때 가장 일반적으로 사용 되는 개체 중 일부를 보여 주는 다이어그램입니다.](./media/media-services-overview/media-services-overview-object-model-small.png)](./media/media-services-overview/media-services-overview-object-model.png#lightbox)

전체 모델은 [여기](https://media.windows.net/API/$metadata?api-version=2.15)서 볼 수 있습니다 .  

## <a name="protect-content-in-storage-and-deliver-streaming-media-in-the-clear-non-encrypted"></a>스토리지에서 콘텐츠 보호 및 암호화하지 않고 스트리밍 미디어 배달(암호화되지 않음)

![VoD 워크플로](./media/scenarios-and-availability/scenarios-and-availability01.png)

1. 자산에 고품질 미디어 파일을 업로드합니다.

    업로드 중에 콘텐츠를 보호 하기 위해 저장소 암호화 옵션을 자산에 적용 하는 것이 좋습니다.

1. 적응 비트 전송률 MP4 파일 집합으로 인코딩합니다.

    휴지 상태의 콘텐츠를 보호 하기 위해 출력 자산에 저장소 암호화 옵션을 적용 하는 것이 좋습니다.

1. 자산 배달 정책(동적 패키징에서 사용)을 구성합니다.

    자산이 암호화 된 저장소 인 경우 자산 배달 정책을 구성 **해야 합니다** .
1. 주문형 로케이터를 만들어 자산을 게시합니다.
1. 게시된 콘텐츠를 스트리밍합니다.

## <a name="protect-content-in-storage-deliver-dynamically-encrypted-streaming-media"></a>스토리지에서 콘텐츠를 보호하고 암호화된 스트리밍 미디어를 동적으로 배달합니다.

![PlayReady로 보호](./media/media-services-content-protection-overview/media-services-content-protection-with-multi-drm.png)

1. 자산에 고품질 미디어 파일을 업로드합니다. 스토리지 암호화 옵션을 자산에 적용합니다.
1. 적응 비트 전송률 MP4 파일 집합으로 인코딩합니다. 스토리지 암호화 옵션을 출력 자산에 적용합니다.
1. 재생하는 동안 동적으로 암호화하려는 경우 자산에 대한 암호화 콘텐츠 키를 만듭니다.
1. 콘텐츠 키 인증 정책을 구성합니다.
1. 자산 배달 정책(동적 패키징 및 동적 암호화에서 사용)을 구성합니다.
1. 주문형 로케이터를 만들어 자산을 게시합니다.
1. 게시된 콘텐츠를 스트리밍합니다.

## <a name="deliver-progressive-download"></a>점진적 다운로드 제공

1. 자산에 고품질 미디어 파일을 업로드합니다.
1. 하나의 MP4 파일로 인코딩합니다.
1. 주문형 또는 SAS 로케이터를 만들어 자산을 게시합니다. SAS 로케이터를 사용하는 경우 콘텐츠는 Azure Blob Storage에서 다운로드됩니다. 시작 됨 상태에 스트리밍 끝점이 필요 하지 않습니다.
1. 콘텐츠를 점진적으로 다운로드합니다.

## <a name="delivering-live-streaming-events"></a>라이브 스트리밍 이벤트 배달

1. 다양한 라이브 스트리밍 프로토콜(예: RTMP 또는 부드러운 스트리밍)을 사용하여 라이브 콘텐츠를 수집합니다.
1. (선택 사항)스트림을 적응 비트 전송률 스트림으로 인코딩합니다.
1. 라이브 스트림을 미리 봅니다.
1. 다음을 통해 콘텐츠를 배달 합니다.
    1. 일반적인 스트리밍 프로토콜 (예: MPEG 대시, 부드러운, HLS)을 고객에 게 직접
    1. 추가 배포를 위해 CDN (Content Delivery Network)으로 또는
    1. 나중에 스트리밍할 수집 콘텐츠를 기록 하 고 저장 합니다 (주문형 비디오).

라이브 스트리밍을 수행할 때 다음 경로 중 하나를 선택할 수 있습니다.

### <a name="working-with-channels-that-receive-multi-bitrate-live-stream-from-on-premises-encoders-pass-through"></a>온-프레미스 인코더(통과)에서 다중 비트 전송률 라이브 스트림을 받는 채널 작업

다음 다이어그램에서는 **통과** 워크플로에 관련된 AMS 플랫폼의 주요 부분을 보여 줍니다.

!["통과" 워크플로와 관련 된 M S 플랫폼의 주요 부분을 보여 주는 다이어그램입니다.](./media/scenarios-and-availability/media-services-live-streaming-current.png)

자세한 내용은 [온-프레미스 인코더의 다중 비트 전송률 라이브 스트림을 수신하는 채널 사용](media-services-live-streaming-with-onprem-encoders.md)을 참조하세요.

### <a name="working-with-channels-that-are-enabled-to-perform-live-encoding-with-azure-media-services"></a>Azure Media Services를 사용하여 라이브 인코딩을 수행할 수 있는 채널 작업

다음 다이어그램에서는 라이브 스트리밍 워크플로에 관련 된 AMS 플랫폼의 주요 부분을 보여 줍니다 .이는 채널이 Media Services를 사용 하 여 라이브 인코딩을 수행 하도록 설정 되어 있습니다.

![라이브 워크플로](./media/scenarios-and-availability/media-services-live-streaming-new.png)

자세한 내용은 [Azure Media Services를 사용하여 Live Encoding을 수행할 수 있는 채널 작업](media-services-manage-live-encoder-enabled-channels.md)을 참조하세요.

## <a name="consuming-content"></a>콘텐츠 사용

Azure Media Services는 iOS 디바이스, Android 디바이스, Windows, Windows Phone, Xbox 및 셋톱 박스를 포함한 대부분의 플랫폼에서 풍부한 동적 클라이언트 플레이어 애플리케이션을 만드는 데 필요한 도구를 제공합니다.

## <a name="enabling-azure-cdn"></a>Azure CDN 사용하기

Media Services는 Azure CDN과의 통합을 지원합니다. Azure CDN을 사용하도록 설정하는 방법에 대한 자세한 내용은 [Media Services 계정에서 스트리밍 엔드포인트를 관리하는 방법](media-services-portal-manage-streaming-endpoints.md)을 참조하세요.

## <a name="scaling-a-media-services-account"></a>Media Services 계정 크기 조정하기

AMS 고객은 해당 AMS 계정에서 스트리밍 엔드포인트, 미디어 처리 및 스토리지의 크기를 조정할 수 있습니다.

* Media Services 고객은 **표준** 스트리밍 엔드포인트나 **프리미엄** 스트리밍 엔드포인트를 선택할 수 있습니다. **표준** 스트리밍 엔드포인트는 대부분의 스트리밍 워크로드에 적합합니다. **프리미엄** 스트리밍 단위와 동일한 기능을 포함하고 아웃바운드 대역폭을 자동으로 확장합니다.

    **프리미엄** 스트리밍 엔드포인트는 고급 워크로드에 적합하며, 확장성 있는 전용 대역폭 용량을 제공합니다. **프리미엄** 스트리밍 엔드포인트가 있는 고객은 기본적으로 하나의 SU(스트리밍 단위)를 가져옵니다. SU를 추가하여 스트리밍 엔드포인트의 크기를 조정할 수 있습니다. 각 SU는 애플리케이션에 추가 대역폭 수용작업량을 제공합니다. **프리미엄** 스트리밍 엔드포인트의 크기를 조정하는 방법에 대한 자세한 내용은 [스트리밍 엔드포인트 크기 조정](media-services-portal-scale-streaming-endpoints.md) 항목을 참조하세요.

* Media Services 계정은 미디어 처리 작업을 처리하는 속도를 결정하는 예약 단위 형식과 연결됩니다. **S1** , **S2** 또는 **S3** 예약 단위 유형 중에서 선택할 수 있습니다. 예를 들어 **S2** 예약 단위 유형을 사용하는 경우 **S1** 유형에 비해 동일한 인코딩 작업이 더 빠르게 실행됩니다.

    예약 단위 유형을 지정 하는 것 외에도를 지정 하 여 RUs ( **예약 단위** )로 계정을 프로 비전 할 수 있습니다. 프로비전되는 RU의 수에 따라 특정 계정에서 동시에 처리할 수 있는 미디어 작업의 수가 결정됩니다.

    > [!NOTE]
    > RU는 Azure Media Indexer를 사용하는 인덱싱 작업을 비롯하여 모든 미디어 처리 병렬화에 대해 작동합니다. 그러나 인코딩과 달리 인덱싱 작업은 예약 단위가 더 빠르게 실행되어도 더 빨리 처리되지 않습니다.

    자세한 내용은 [미디어 처리 크기 조정](media-services-portal-scale-media-processing.md)을 참조 하세요.

* 또한 스토리지 계정을 추가하여 Media Services 계정을 확장할 수 있습니다. 각 스토리지 계정은 500TB로 제한됩니다. 자세한 내용은 [스토리지 계정 관리](./media-services-managing-multiple-storage-accounts.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

[Media Services v3로 마이그레이션](../latest/media-services-overview.md)

## <a name="provide-feedback"></a>피드백 제공
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]