---
title: Azure Media Services의 라이브 스트리밍 개념 - 라이브 이벤트 및 라이브 출력 | Microsoft Docs
description: 이 문서에서는 Azure Media Services v3의 라이브 스트리밍 개념에 대해 간략히 설명합니다.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 08/26/2019
ms.author: juliako
ms.openlocfilehash: c81c2de180a2c5734f3896d4b6843f2ccccdf45f
ms.sourcegitcommit: 2aefdf92db8950ff02c94d8b0535bf4096021b11
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/03/2019
ms.locfileid: "70231204"
---
# <a name="live-events-and-live-outputs"></a>라이브 이벤트 및 라이브 출력

Azure Media Services를 사용하면 Azure 클라우드에서 고객에게 라이브 이벤트를 전달할 수 있습니다. Media Services v3에서 라이브 스트리밍 이벤트를 구성 하려면이 문서에서 설명 하는 개념을 이해 해야 합니다.

> [!TIP]
> Media Services v2 Api에서 마이그레이션하는 고객의 경우 **라이브 이벤트** 엔터티는 v 2의 **채널** 을 바꾸고 **라이브 출력** 은 **프로그램**을 대체 합니다.

## <a name="live-events"></a>라이브 이벤트

[라이브 이벤트](https://docs.microsoft.com/rest/api/media/liveevents)는 라이브 비디오 피드 수집 및 처리를 담당합니다. 라이브 이벤트를 만들 때 원격 인코더에서 라이브 신호를 보내는 데 사용할 수 있는 기본 및 보조 입력 끝점이 생성 됩니다. 원격 라이브 인코더는 [RTMP](https://www.adobe.com/devnet/rtmp.html) 또는 [부드러운 스트리밍](https://msdn.microsoft.com/library/ff469518.aspx) (조각화 된 MP4) 입력 프로토콜을 사용 하 여 해당 입력 끝점에 기여 피드를 보냅니다. RTMP 수집 프로토콜의 경우 콘텐츠를 일반`rtmp://`()으로 전송 하거나 통신 (`rtmps://`)에서 안전 하 게 암호화할 수 있습니다. 부드러운 스트리밍 수집 프로토콜의 경우 지원되는 URL 체계는 `http://` 또는 `https://`입니다.  

## <a name="live-event-types"></a>라이브 이벤트 유형

[라이브 이벤트](https://docs.microsoft.com/rest/api/media/liveevents)는 통과 및 라이브 인코딩의 두 가지 형식 중 하나일 수 있습니다. 형식은 [LiveEventEncodingType](https://docs.microsoft.com/rest/api/media/liveevents/create#liveeventencodingtype)를 사용 하 여 만드는 동안 설정 됩니다.

* **LiveEventEncodingType** -온-프레미스 라이브 인코더가 다중 비트 전송률 스트림을 보냅니다. 수집 스트림은 추가 처리 없이 라이브 이벤트를 통과 합니다. 
* **LiveEventEncodingType** -온-프레미스 라이브 인코더는 단일 비트 전송률 스트림을 라이브 이벤트로 보내고 Media Services 여러 비트 전송률 스트림을 만듭니다. 기여 피드가 720p 이상 해상도 인 경우 **Default720p** 사전 설정은 6 가지 해상도/비트 전송률 쌍 집합을 인코딩합니다.
* **LiveEventEncodingType Premium1080p** -온-프레미스 라이브 인코더는 단일 비트 전송률 스트림을 라이브 이벤트로 보내고 Media Services 여러 비트 전송률 스트림을 만듭니다. Default1080p 사전 설정은 해상도/비트 전송률 쌍의 출력 집합을 지정 합니다. 

### <a name="pass-through"></a>통과

![pass-through(통과)](./media/live-streaming/pass-through.svg)

통과 **라이브 이벤트**를 사용하는 경우 온-프레미스 라이브 인코더에 의존하여 다중 비트 전송률이 있는 비디오 스트림을 생성하고 이 스트림을 기여 피드로 하여 LiveEvent에 전송(RTMP 또는 조각난 MP4 프로토콜 사용)할 수 있습니다. 그러면 라이브 이벤트는 들어오는 비디오 스트림을 추가 처리 없이 제공합니다. 이러한 통과 라이브 이벤트는 장기 실행 라이브 이벤트 또는 24x365 선형 라이브 스트리밍을 위해 최적화 되었습니다. 이 형식의 라이브 이벤트를 만들 경우 None(LiveEventEncodingType.None)을 지정합니다.

H.264/AVC 또는 H.265/HEVC 비디오 코덱 및 AAC(AAC-LC, HE-AACv1 또는 HE-AACv2) 오디오 코덱을 사용하여 최대 4K의 해상도 및 초당 60프레임의 프레임 속도로 기여 피드를 전송할 수 있습니다.  자세한 내용은 [라이브 이벤트 유형 비교](live-event-types-comparison.md) 문서를 참조하세요.

> [!NOTE]
> 통과 방법을 사용하면 긴 기간 동안 여러 이벤트를 수행하고 온-프레미스 인코더에 이미 투자한 경우 라이브 스트리밍을 수행하는 가장 경제적인 방법입니다. [가격 책정](https://azure.microsoft.com/pricing/details/media-services/) 세부 정보를 참조하세요.
> 

[MediaV3LiveApp](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/blob/master/NETCore/Live/MediaV3LiveApp/Program.cs#L126)의 .NET 코드 예제를 참조하세요.

### <a name="live-encoding"></a>라이브 인코딩  

![라이브 인코딩](./media/live-streaming/live-encoding.svg)

Media Services에서 라이브 인코딩을 사용하는 경우 단일 비트 전송률 비디오를 기여 피드로 하여 라이브 이벤트에 전송(RTMP 또는 조각난 Mp4 프로토콜 사용)하도록 온-프레미스 라이브 인코더를 구성합니다. 그런 다음 들어오는 단일 비트 전송률 스트림을 [다중 비트 전송률 비디오 스트림으로](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming)인코드 하도록 라이브 이벤트를 설정 하 고, MPEG-2, HLS 및 부드러운 스트리밍 같은 프로토콜을 통해 장치를 재생 하기 위해 출력을 제공할 수 있도록 합니다.

Live encoding을 사용 하는 경우, AAC/AVC 비디오 코덱 및 (AAC, He-aacv1 또는 He-aacv2) 오디오 코덱을 사용 하 여 프레임 속도로 30 프레임/초 프레임 속도로 최대 1080p 해상도로만 기여 피드를 보낼 수 있습니다. 통과 라이브 이벤트는 60 프레임/초에 최대 4K의 해상도를 지원할 수 있습니다. 자세한 내용은 [라이브 이벤트 유형 비교](live-event-types-comparison.md) 문서를 참조하세요.

라이브 인코더의 출력에 포함 된 해상도 및 비트 전송률이 미리 설정에 따라 결정 됩니다. **표준** 라이브 인코더 (LiveEventEncodingType)를 사용 하는 경우 *Default720p* 기본 설정은 63.5 mbps의 720p에서 200 kbps로 이동 하 여 6 해상도/비트 전송률 쌍의 집합을 지정 합니다. 그렇지 않고 **Premium1080p** 라이브 인코더 (LiveEventEncodingType)를 사용 하는 경우 *Default1080p* 사전 설정은 200 kbps에서 180p에서 180p로 이동 하는 6 해상도/비트 전송률 쌍의 집합을 지정 합니다. 자세한 내용은 [시스템 미리 설정](live-event-types-comparison.md#system-presets)을 참조하세요.

> [!NOTE]
> 라이브 인코딩 사전 설정을 사용자 지정 해야 하는 경우 Azure Portal를 통해 지원 티켓을 여세요. 원하는 해상도 및 비트 전송률 표를 지정해야 합니다. 표준 라이브 인코더에 대 한 미리 설정을 요청 하는 경우 720p (표준 라이브 인코더에 대 한 미리 설정을 요청 하는 경우) 또는 1080p (Premium1080p live 인코더에 대 한 사전 설정을 요청 하는 경우)에 계층이 하나만 있는지 확인 합니다.

## <a name="live-event-creation-options"></a>라이브 이벤트 만들기 옵션

라이브 이벤트를 만들 때 다음 옵션을 지정할 수 있습니다.

* 라이브 이벤트의 스트리밍 프로토콜(현재 RTMP 및 부드러운 스트리밍 프로토콜이 지원됨).<br/>라이브 이벤트 또는 연결된 라이브 출력이 실행 중인 동안에는 프로토콜 옵션을 변경할 수 없습니다. 다른 프로토콜을 요청하는 경우 각각의 스트리밍 프로토콜에 대한 별도의 라이브 이벤트를 만들어야 합니다.  
* 수집 및 미리 보기에서 IP 제한입니다. 이 라이브 이벤트에 비디오를 수집하도록 허용된 IP 주소를 정의할 수 있습니다. 허용된 IP 주소는 단일 IP 주소(예: '10.0.0.1'), IP 주소 및 CIDR 서브넷 마스크를 사용하는 IP 범위(예: '10.0.0.1/22') 또는 IP 주소와 점으로 구분된 십진수 서브넷 마스크를 사용하는 IP 범위(예: '10.0.0.1(255.255.252.0)')로 지정할 수 있습니다.<br/>지정된 IP 주소가 없고 정의된 규칙이 없는 경우, IP 주소가 허용되지 않습니다. 모든 IP 주소를 허용하려면 규칙을 만들고 0.0.0.0/0으로 설정합니다.<br/>IP 주소가 다음 형식 중 하나에 있어야 합니다. 4개의 숫자를 사용하는 IpV4 주소, CIDR 주소 범위.
* 이벤트를 만들 때 자동 시작을 지정할 수 있습니다. <br/>Autostart가 true로 설정되어 있는 경우 Live Event가 생성 후 시작됩니다. 라이브 이벤트가 실행되는 즉시 청구가 시작됩니다. 추가 청구를 중지하려면 라이브 이벤트 리소스에 대해 명시적으로 Stop을 호출해야 합니다. 또는 스트리밍을 시작할 준비가 되었을 때 이벤트를 시작해도 됩니다. 

    자세한 내용은 [라이브 이벤트 상태 및 청구](live-event-states-billing.md)를 참조하세요.

## <a name="live-event-ingest-urls"></a>라이브 이벤트 수집 URL

라이브 이벤트가 생성되면 라이브 온-프레미스 인코더에 제공할 수집 URL을 구할 수 있습니다. 라이브 인코더는 이러한 URL을 사용하여 라이브 스트림을 입력합니다. 자세한 내용은 [권장 온-프레미스 라이브 인코더](recommended-on-premises-live-encoders.md)를 참조하세요. 

비베니티 URL 또는 베니티 URL을 사용할 수 있습니다. 

> [!NOTE] 
> 수집 URL을 예측하려면 "베니티" 모드를 설정합니다.

* 비베니티 URL

    베 니 티 URL은 Media Services v3의 기본 모드입니다. 잠재적으로 라이브 이벤트를 빠르게 가져오지만 수집 URL은 라이브 이벤트를 시작할 때 알려집니다. URL은 라이브 이벤트를 중지/시작하는 경우 변경됩니다. <br/>비베니티는 최종 사용자가 앱에서 라이브 이벤트를 가능한 빨리 가져오고 동적 수집 URL이 문제가 되지 않는 앱을 사용하여 스트리밍하려는 경우 시나리오에서 유용합니다.
    
    라이브 이벤트가 만들어지기 전에 클라이언트 응용 프로그램이 수집 URL을 미리 생성할 필요가 없는 경우 라이브 이벤트에 대 한 액세스 토큰을 자동으로 생성 하도록 Media Services 수 있습니다.
* 베니티 URL

    베니티 모드는 하드웨어 브로드캐스트 인코더를 사용하고 라이브 이벤트를 시작할 때 해당 인코더를 다시 구성하지 않으려는 대형 미디어 브로드캐스터에서 선호합니다. 시간이 지나도 변경되지 않는 예측 수집 URL을 원합니다.
    
    이 모드를 지정 하려면를 만들 `vanityUrl` 때 `true` 로 설정 합니다 (기본값은 `false`). 또한 만들 때 사용자 고유의 액세스 토큰 (`LiveEventInput.accessToken`)을 전달 해야 합니다. 토큰 값을 지정 하 여 URL에서 임의의 토큰을 방지 합니다. 액세스 토큰은 하이픈을 포함 하거나 포함 하지 않는 유효한 GUID 문자열 이어야 합니다. 모드가 설정 된 후에는 업데이트할 수 없습니다.

    액세스 토큰은 데이터 센터에서 고유 해야 합니다. 응용 프로그램에서 베 니 티 URL을 사용 해야 하는 경우 기존 GUID를 다시 사용 하는 대신 항상 액세스 토큰에 대 한 새 GUID 인스턴스를 만드는 것이 좋습니다. 

    다음 Api를 사용 하 여 베 니 티 URL을 사용 하도록 설정 하 고 액세스 토큰을 유효한 GUID (예 `"accessToken": "1fce2e4b-fb15-4718-8adc-68c6eb4c26a7"`:)로 설정 합니다.  
    
    |언어|베 니 티 URL 사용|액세스 토큰 설정|
    |---|---|---|
    |REST(영문)|[properties.vanityUrl](https://docs.microsoft.com/rest/api/media/liveevents/create#liveevent)|[LiveEventInput.accessToken](https://docs.microsoft.com/rest/api/media/liveevents/create#liveeventinput)|
    |CLI|[--vanity-url](https://docs.microsoft.com/cli/azure/ams/live-event?view=azure-cli-latest#az-ams-live-event-create)|[--access-token](https://docs.microsoft.com/cli/azure/ams/live-event?view=azure-cli-latest#optional-parameters)|
    |.NET|[LiveEvent.VanityUrl](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.liveevent.vanityurl?view=azure-dotnet#Microsoft_Azure_Management_Media_Models_LiveEvent_VanityUrl)|[LiveEventInput.AccessToken](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.liveeventinput.accesstoken?view=azure-dotnet#Microsoft_Azure_Management_Media_Models_LiveEventInput_AccessToken)|
    
### <a name="live-ingest-url-naming-rules"></a>라이브 수집 URL 명명 규칙

* 아래 *임의* 문자열은 128비트 16진수 숫자입니다(0-9 a-f의 32문자로 구성됨).
* *액세스 토큰* -베 니 티 모드를 사용할 때 설정 하는 유효한 GUID 문자열입니다. `"1fce2e4b-fb15-4718-8adc-68c6eb4c26a7"` )을 입력합니다.
* *스트림 이름* -특정 연결의 스트림 이름을 나타냅니다. 스트림 이름 값은 일반적으로 사용 하는 라이브 인코더에 의해 추가 됩니다. 연결을 설명 하는 이름을 사용 하도록 라이브 인코더를 구성할 수 있습니다 (예: "video1_audio1", "video2_audio1", "stream").

#### <a name="non-vanity-url"></a>비베니티 URL

##### <a name="rtmp"></a>RTMP

`rtmp://<random 128bit hex string>.channel.media.azure.net:1935/live/<auto-generated access token>/<stream name>`<br/>
`rtmp://<random 128bit hex string>.channel.media.azure.net:1936/live/<auto-generated access token>/<stream name>`<br/>
`rtmps://<random 128bit hex string>.channel.media.azure.net:2935/live/<auto-generated access token>/<stream name>`<br/>
`rtmps://<random 128bit hex string>.channel.media.azure.net:2936/live/<auto-generated access token>/<stream name>`<br/>

##### <a name="smooth-streaming"></a>부드러운 스트리밍

`http://<random 128bit hex string>.channel.media.azure.net/<auto-generated access token>/ingest.isml/streams(<stream name>)`<br/>
`https://<random 128bit hex string>.channel.media.azure.net/<auto-generated access token>/ingest.isml/streams(<stream name>)`<br/>

#### <a name="vanity-url"></a>베니티 URL

##### <a name="rtmp"></a>RTMP

`rtmp://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net:1935/live/<your access token>/<stream name>`<br/>
`rtmp://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net:1936/live/<your access token>/<stream name>`<br/>
`rtmps://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net:2935/live/<your access token>/<stream name>`<br/>
`rtmps://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net:2936/live/<your access token>/<stream name>`<br/>

##### <a name="smooth-streaming"></a>부드러운 스트리밍

`http://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net/<your access token>/ingest.isml/streams(<stream name>)`<br/>
`https://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net/<your access token>/ingest.isml/streams(<stream name>)`<br/>

## <a name="live-event-preview-url"></a>라이브 이벤트 미리 보기 URL

라이브 이벤트가 기여 피드를 수신 하기 시작 하면 미리 보기 끝점을 사용 하 여 추가 게시 전에 라이브 스트림을 수신 하 고 있는지 확인 하 고 유효성을 검사할 수 있습니다. 미리 보기 스트림이 양호 하다 고 확인 한 후 라이브 이벤트를 사용 하 여 하나 이상의 (미리 만든) 스트리밍 끝점을 통해 라이브 스트림을 배달할 수 있도록 설정할 수 있습니다. 이를 위해 라이브 이벤트에 새 [라이브 출력](https://docs.microsoft.com/rest/api/media/liveoutputs) 을 만듭니다. 

> [!IMPORTANT]
> 계속하기 전에 비디오가 미리 보기 URL로 전달되고 있는지 확인합니다.

## <a name="live-event-long-running-operations"></a>라이브 이벤트 장기 실행 작업

자세한 내용은 [장기 실행 작업](media-services-apis-overview.md#long-running-operations) 을 참조 하세요.

## <a name="live-outputs"></a>라이브 출력

라이브 이벤트로 들어오는 스트림이 있으면 [자산](https://docs.microsoft.com/rest/api/media/assets), [라이브 출력](https://docs.microsoft.com/rest/api/media/liveoutputs) 및 [스트리밍 로케이터](https://docs.microsoft.com/rest/api/media/streaminglocators)를 만들어 스트리밍 이벤트를 시작할 수 있습니다. 라이브 출력은 스트림을 보관하고 [스트리밍 엔드포인트](https://docs.microsoft.com/rest/api/media/streamingendpoints)를 통해 시청자가 스트림을 사용할 수 있도록 합니다.  

라이브 출력에 대 한 자세한 내용은 [클라우드 DVR 사용](live-event-cloud-dvr.md)을 참조 하세요.

## <a name="ask-questions-give-feedback-get-updates"></a>질문, 피드백 제공, 업데이트 받기

[Azure Media Services 커뮤니티](media-services-community.md) 문서를 체크 아웃하여 다양한 방법으로 질문을 하고, 피드백을 제공하고, Media Services에 대한 업데이트를 가져올 수 있습니다.

## <a name="next-steps"></a>다음 단계

[라이브 스트리밍 자습서](stream-live-tutorial-with-api.md)
