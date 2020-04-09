---
title: Azure 미디어 서비스 v3의 라이브 이벤트 및 라이브 출력 개념
titleSuffix: Azure Media Services
description: 이 항목에서는 Azure Media Services v3의 라이브 이벤트 및 라이브 출력에 대한 개요를 제공합니다.
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
ms.date: 04/08/2020
ms.author: juliako
ms.openlocfilehash: 8a00f7c0ec76510cc521966acf98b7250e723697
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/09/2020
ms.locfileid: "80985901"
---
# <a name="live-events-and-live-outputs-in-media-services"></a>미디어 서비스의 라이브 이벤트 및 라이브 출력

Azure 미디어 서비스를 사용하면 Azure 클라우드에서 고객에게 라이브 이벤트를 제공할 수 있습니다. Media Services v3에서 라이브 스트리밍 이벤트를 설정하려면 이 문서에서 설명하는 개념을 이해해야 합니다.

> [!TIP]
> Media Services v2 API에서 마이그레이션하는 고객의 경우 **라이브 이벤트** 엔터티가 v2의 **채널을** 대체하고 **라이브 출력이** **프로그램을**대체합니다.

## <a name="live-events"></a>라이브 이벤트

[라이브 이벤트](https://docs.microsoft.com/rest/api/media/liveevents)는 라이브 비디오 피드 수집 및 처리를 담당합니다. Live 이벤트를 만들 때 원격 인코더에서 라이브 신호를 보내는 데 사용할 수 있는 기본 및 보조 입력 끝점이 만들어집니다. 원격 라이브 인코더는 [RTMP](https://www.adobe.com/devnet/rtmp.html) 또는 [스무스](https://msdn.microsoft.com/library/ff469518.aspx) 스트리밍(조각화-MP4) 입력 프로토콜을 사용하여 해당 입력 끝점으로 기여 피드를 보냅니다. RTMP 인제스트 프로토콜의 경우, 컨텐츠를`rtmp://`클리어(clear) 또는 와이어()에서`rtmps://`안전하게 암호화할 수 있다. 부드러운 스트리밍 수집 프로토콜의 경우 지원되는 URL 체계는 `http://` 또는 `https://`입니다.  

## <a name="live-event-types"></a>라이브 이벤트 유형

[라이브 이벤트는](https://docs.microsoft.com/rest/api/media/liveevents) 통과(온-프레미스 라이브 인코더가 여러 비트 레이트 스트림을 전송) 또는 *라이브 인코딩(온-프레미스* 라이브 인코더가 단일 비트 레이트 스트림을 보냅니다)으로 설정할 수 있습니다. *pass-through* 형식은 [LiveEventEncodingType을](https://docs.microsoft.com/rest/api/media/liveevents/create#liveeventencodingtype)사용 하 여 만드는 동안 설정 됩니다.

* **LiveEventEncodingType.None**: 온-프레미스 라이브 인코더는 여러 비트 전송률 스트림을 보냅니다. 인제스트 스트림은 추가 처리 없이 라이브 이벤트를 통과합니다. 통과 모드라고도 합니다.
* **LiveEventEncodingType.Standard**: 온-프레미스 라이브 인코더는 단일 비트 레이트 스트림을 라이브 이벤트로 보내고 미디어 서비스는 여러 비트 전송률 스트림을 만듭니다. 기여 피드가 720p 이상인 경우 **Default720p** 사전 설정은 6개의 해상도/비트레이트 쌍 세트를 인코딩합니다.
* **LiveEventEncodingType.Premium1080p**: 온-프레미스 라이브 인코더는 라이브 이벤트에 단일 비트 레이트 스트림을 보내고 미디어 서비스는 여러 비트 레이트 스트림을 만듭니다. Default1080p 사전 설정은 해상도/비트 레이트 쌍의 출력 집합을 지정합니다.

### <a name="pass-through"></a>통과

![미디어 서비스 예제 다이어그램을 통한 통과 라이브 이벤트](./media/live-streaming/pass-through.svg)

통과 **라이브 이벤트**를 사용하는 경우 온-프레미스 라이브 인코더에 의존하여 다중 비트 전송률이 있는 비디오 스트림을 생성하고 이 스트림을 기여 피드로 하여 LiveEvent에 전송(RTMP 또는 조각난 MP4 프로토콜 사용)할 수 있습니다. 그러면 라이브 이벤트는 들어오는 비디오 스트림을 추가 처리 없이 제공합니다. 이러한 패스스루 라이브 이벤트는 장기 실행 라이브 이벤트 또는 24x365 선형 라이브 스트리밍에 최적화되어 있습니다. 이 형식의 라이브 이벤트를 만들 경우 None(LiveEventEncodingType.None)을 지정합니다.

H.264/AVC 또는 H.265/HEVC 비디오 코덱 및 AAC(AAC-LC, HE-AACv1 또는 HE-AACv2) 오디오 코덱을 사용하여 최대 4K의 해상도 및 초당 60프레임의 프레임 속도로 기여 피드를 전송할 수 있습니다. 자세한 내용은 [라이브 이벤트 유형 비교를](live-event-types-comparison.md)참조하십시오.

> [!NOTE]
> 패스스루 방법을 사용하면 오랜 기간 동안 여러 이벤트를 수행하고 온-프레미스 인코더에 이미 투자한 경우 라이브 스트리밍을 수행하는 가장 경제적인 방법입니다. [가격 책정](https://azure.microsoft.com/pricing/details/media-services/) 세부 정보를 참조하세요.
>

[MediaV3LiveApp](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/blob/master/NETCore/Live/MediaV3LiveApp/Program.cs#L126)의 .NET 코드 예제를 참조하세요.

### <a name="live-encoding"></a>라이브 인코딩  

![미디어 서비스 예제 다이어그램을 통해 라이브 인코딩](./media/live-streaming/live-encoding.svg)

Media Services에서 라이브 인코딩을 사용하는 경우 온-프레미스 라이브 인코더를 구성하여 단일 비트 전송률 비디오를 라이브 이벤트에 대한 기여 피드로 보내도록 구성합니다(RTMP 또는 조각형 Mp4 프로토콜 사용). 그런 다음 들어오는 단일 비트 전송률 스트림을 [여러 비트 레이트 비디오 스트림으로](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming)인코딩하고 출력을 MPEG-DASH, HLS 및 부드러운 스트리밍과 같은 프로토콜을 통해 장치를 재생할 수 있도록 라이브 이벤트를 설정합니다.

라이브 인코딩을 사용하는 경우 H.264/AVC 비디오 코덱 및 AAC(AAC-LC, HE-AACv1 또는 HE-AACv2) 오디오 코덱을 사용하여 초당 30프레임의 프레임 속도로 최대 1080p 해상도의 해상도로만 기여 피드를 보낼 수 있습니다. 통과 라이브 이벤트는 초당 60프레임에서 최대 4K해상도를 지원할 수 있습니다. 자세한 내용은 [라이브 이벤트 유형 비교를](live-event-types-comparison.md)참조하십시오.

라이브 인코더의 출력에 포함된 해상도 및 비트 레이트는 사전 설정에 의해 결정됩니다. **표준** 라이브 인코더(LiveEventEncodingType.Standard)를 사용하는 경우 *Default720p* 사전 설정은 6개의 해상도/비트 레이트 쌍 세트를 지정하여 3.5Mbps에서 200kbps에서 192p로 줄입니다. 그렇지 않으면 **Premium1080p** 라이브 인코더(LiveEventEncodingType.Premium1080p)를 사용하는 경우 *Default1080p* 사전 설정은 1080p에서 3.5Mbps에서 200kbps로 내려가는 6개의 해상도/비트 레이트 쌍 세트를 지정합니다. 자세한 내용은 [시스템 미리 설정](live-event-types-comparison.md#system-presets)을 참조하세요.

> [!NOTE]
> 라이브 인코딩 사전 설정을 사용자 지정해야 하는 경우 Azure 포털을 통해 지원 티켓을 엽니다. 원하는 해상도 및 비트 레이트 테이블을 지정합니다. 720p(표준 라이브 인코더에 대한 사전 설정을 요청하는 경우) 또는 1080p(Premium1080p 라이브 인코더에 대한 사전 설정을 요청하는 경우) 레이어가 하나뿐인지 확인하고 6개의 레이어를 최대한 사용합니다.

## <a name="creating-live-events"></a>라이브 이벤트 만들기

### <a name="options"></a>옵션

라이브 이벤트를 만들 때 다음 옵션을 지정할 수 있습니다.

* 라이브 이벤트의 스트리밍 프로토콜(현재 RTMP 및 부드러운 스트리밍 프로토콜이 지원됨).<br/>라이브 이벤트 또는 연결된 라이브 출력이 실행 중인 동안에는 프로토콜 옵션을 변경할 수 없습니다. 다른 프로토콜이 필요한 경우 각 스트리밍 프로토콜에 대해 별도의 라이브 이벤트를 만듭니다.  
* 이벤트를 만들 때 자동 시작을 지정할 수 있습니다. <br/>Autostart가 true로 설정되어 있는 경우 Live Event가 생성 후 시작됩니다. 라이브 이벤트가 실행되는 즉시 청구가 시작됩니다. 추가 청구를 중지하려면 라이브 이벤트 리소스에 대해 명시적으로 Stop을 호출해야 합니다. 또는 스트리밍을 시작할 준비가 되면 이벤트를 시작할 수 있습니다.

    자세한 내용은 [라이브 이벤트 상태 및 청구](live-event-states-billing.md)를 참조하세요.

* 수집 및 미리 보기에서 IP 제한입니다. 이 라이브 이벤트에 비디오를 수집하도록 허용된 IP 주소를 정의할 수 있습니다. 허용된 IP 주소는 단일 IP 주소(예: '10.0.0.1'), IP 주소 및 CIDR 서브넷 마스크를 사용하는 IP 범위(예: '10.0.0.1/22') 또는 IP 주소와 점으로 구분된 십진수 서브넷 마스크를 사용하는 IP 범위(예: '10.0.0.1(255.255.252.0)')로 지정할 수 있습니다.<br/>지정된 IP 주소가 없고 정의된 규칙이 없는 경우, IP 주소가 허용되지 않습니다. 모든 IP 주소를 허용하려면 규칙을 만들고 0.0.0.0/0으로 설정합니다.<br/>IP 주소는 네 개의 숫자 또는 CIDR 주소 범위가 있는 IpV4 주소 중 하나여야 합니다.

    자체 방화벽에서 특정 IP를 사용하도록 설정하거나 라이브 이벤트에 대한 입력을 Azure IP 주소로 제한하려는 경우 Azure 데이터 센터 IP 주소 범위에서 JSON 파일을 [다운로드합니다.](https://www.microsoft.com/download/details.aspx?id=41653) 이 파일에 대한 자세한 내용은 페이지의 **세부 정보** 섹션을 선택합니다.
    
* 이벤트를 만들 때 라이브 전사를 설정하도록 선택할 수 있습니다. <br/> 기본적으로 라이브 전사는 비활성화됩니다. 라이브 이벤트 또는 연결된 라이브 출력이 실행되는 동안에는 이 속성을 변경할 수 없습니다. 

### <a name="naming-rules"></a>이름 지정 규칙

* 최대 라이브 이벤트 이름은 32자입니다.
* 이름은 이 [정규식](https://docs.microsoft.com/dotnet/standard/base-types/regular-expression-language-quick-reference) 패턴을 `^[a-zA-Z0-9]+(-*[a-zA-Z0-9])*$`따라야 합니다.

또한 [스트리밍 끝점 이름 지정 규칙을](streaming-endpoint-concept.md#naming-convention)참조하십시오.

> [!TIP]
> 라이브 이벤트 이름의 고유성을 보장하려면 GUID를 생성한 다음 모든 하이픈과 곱슬 괄호(있는 경우)를 제거할 수 있습니다. 문자열은 모든 라이브 이벤트에서 고유하며 길이는 32로 보장됩니다.

## <a name="live-event-ingest-urls"></a>라이브 이벤트 수집 URL

라이브 이벤트가 만들어지면 라이브 온-프레미스 인코더에 제공할 URL을 얻을 수 있습니다. 라이브 인코더는 이러한 URL을 사용하여 라이브 스트림을 입력합니다. 자세한 내용은 [권장 온-프레미스 라이브 인코더](recommended-on-premises-live-encoders.md)를 참조하세요.

비베니티 URL 또는 베니티 URL을 사용할 수 있습니다.

> [!NOTE] 
> 수집 URL을 예측하려면 "베니티" 모드를 설정합니다.

* 비베니티 URL

    비허인 URL은 미디어 서비스 v3의 기본 모드입니다. 잠재적으로 라이브 이벤트를 빠르게 가져오지만 수집 URL은 라이브 이벤트를 시작할 때 알려집니다. URL은 라이브 이벤트를 중지/시작하는 경우 변경됩니다. <br/>Non-Vanity는 최종 사용자가 앱이 최대한 빨리 라이브 이벤트를 얻고자 하는 앱을 사용하여 스트리밍하려는 시나리오에서 유용하며 동적 인제스트 URL이 문제가 되지 않는 경우 유용합니다.

    라이브 이벤트를 생성하기 전에 클라이언트 앱에서 인제스트 URL을 미리 생성할 필요가 없는 경우 Media Services에서 라이브 이벤트에 대한 액세스 토큰을 자동으로 생성하도록 합니다.

* 베니티 URL

    허영 모드는 하드웨어 브로드캐스트 인코더를 사용하고 라이브 이벤트를 시작할 때 인코더를 다시 구성하지 않으려는 대형 미디어 브로드캐스터가 선호합니다. 시간이 지남에 따라 변경되지 않는 예측 인제스트 URL을 원합니다.
    
    > [!NOTE]
    > Azure 포털에서 허영 URL의 이름은 *"영구 입력*URL"입니다.

    API에서 이 모드를 지정하려면 `vanityUrl` `true` 생성 시(기본값)로 `false`설정합니다. 또한 생성 시 자신의 액세스`LiveEventInput.accessToken`토큰 ()을 전달해야합니다. URL에서 임의의 토큰을 피하기 위해 토큰 값을 지정합니다. 액세스 토큰은 하이픈 유무에 관계없이 유효한 GUID 문자열이어야 합니다. 모드가 설정되면 업데이트할 수 없습니다.

    액세스 토큰은 데이터 센터에서 고유해야 합니다. 앱에서 허영 URL을 사용해야 하는 경우 기존 GUID를 재사용하는 대신 액세스 토큰에 대해 항상 새 GUID 인스턴스를 만드는 것이 좋습니다.

    다음 API를 사용하여 허영 URL을 활성화하고 액세스 토큰을 유효한 `"accessToken": "1fce2e4b-fb15-4718-8adc-68c6eb4c26a7"`GUID(예: )로 설정합니다.  

    |언어|허영 URL 사용|액세스 토큰 설정|
    |---|---|---|
    |REST (영문)|[속성.허영Url](https://docs.microsoft.com/rest/api/media/liveevents/create#liveevent)|[라이브 이벤트 입력.액세스 토큰](https://docs.microsoft.com/rest/api/media/liveevents/create#liveeventinput)|
    |CLI|[--허영 URL](https://docs.microsoft.com/cli/azure/ams/live-event?view=azure-cli-latest#az-ams-live-event-create)|[--액세스 토큰](https://docs.microsoft.com/cli/azure/ams/live-event?view=azure-cli-latest#optional-parameters)|
    |.NET|[라이브 이벤트.허영Url](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.liveevent.vanityurl?view=azure-dotnet#Microsoft_Azure_Management_Media_Models_LiveEvent_VanityUrl)|[라이브 이벤트 입력.액세스 토큰](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.liveeventinput.accesstoken?view=azure-dotnet#Microsoft_Azure_Management_Media_Models_LiveEventInput_AccessToken)|
    
### <a name="live-ingest-url-naming-rules"></a>라이브 수집 URL 명명 규칙

* 아래 *임의* 문자열은 128비트 16진수 숫자입니다(0-9 a-f의 32문자로 구성됨).
* *액세스 토큰*: 허영 모드를 사용할 때 설정한 유효한 GUID 문자열입니다. `"1fce2e4b-fb15-4718-8adc-68c6eb4c26a7"`)을 입력합니다.
* *스트림 이름*: 특정 연결의 스트림 이름을 나타냅니다. 스트림 이름 값은 일반적으로 사용하는 라이브 인코더에 의해 추가됩니다. 라이브 인코더를 구성하여 "video1_audio1", "video2_audio1", "stream"과 같은 연결을 설명하기 위해 모든 이름을 사용할 수 있습니다.

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

라이브 이벤트가 기여 피드수신을 시작하면 미리 보기 끝점을 사용하여 추가 게시전에 라이브 스트림을 수신하고 있는지 미리 보고 확인할 수 있습니다. 미리 보기 스트림이 양호한지 확인한 후 Live Event를 사용하여 하나 이상의(미리 생성된) 스트리밍 엔드포인트를 통해 라이브 스트림을 배달할 수 있도록 할 수 있습니다. 이렇게 하려면 라이브 이벤트에서 새 [라이브 출력을](https://docs.microsoft.com/rest/api/media/liveoutputs) 만듭니다.

> [!IMPORTANT]
> 계속하기 전에 비디오가 미리 보기 URL로 전달되고 있는지 확인합니다.

## <a name="live-event-long-running-operations"></a>라이브 이벤트 장기 실행 작업

자세한 내용은 [장기 실행 작업을](media-services-apis-overview.md#long-running-operations)참조하십시오.

## <a name="live-outputs"></a>라이브 출력

라이브 이벤트로 들어오는 스트림이 있으면 [자산](https://docs.microsoft.com/rest/api/media/assets), [라이브 출력](https://docs.microsoft.com/rest/api/media/liveoutputs) 및 [스트리밍 로케이터](https://docs.microsoft.com/rest/api/media/streaminglocators)를 만들어 스트리밍 이벤트를 시작할 수 있습니다. 라이브 출력은 스트림을 보관하고 [스트리밍 엔드포인트](https://docs.microsoft.com/rest/api/media/streamingendpoints)를 통해 시청자가 스트림을 사용할 수 있도록 합니다.  

라이브 출력에 대한 자세한 내용은 [클라우드 DVR 사용을](live-event-cloud-dvr.md)참조하십시오.

## <a name="frequently-asked-questions"></a>질문과 대답

자주 [묻는 질문](frequently-asked-questions.md#live-streaming) 문서를 참조하십시오.

## <a name="ask-questions-and-get-updates"></a>질문하고 업데이트 받기

[Azure Media Services 커뮤니티](media-services-community.md) 문서를 체크 아웃하여 다양한 방법으로 질문을 하고, 피드백을 제공하고, Media Services에 대한 업데이트를 가져올 수 있습니다.

## <a name="next-steps"></a>다음 단계

[라이브 스트리밍 자습서](stream-live-tutorial-with-api.md)
