---
# <a name="mandatory-fields-see-more-on-akamsskyeyemeta"></a>필수 필드. 자세한 내용은 aka.ms/skyeye/meta를 참조하세요.
제목: CDN 통합을 사용하여 콘텐츠 스트림: Azure Media Services 설명: CDN 통합을 사용하여 콘텐츠 스트리밍 및 프리페치 및 원본 지원 CDN 프리페치를 사용하는 방법에 대해 알아봅니다.
services: media-services documentationcenter: '' author: IngridAtMicrosoft manager: femila editor: '' ms.service: media-services ms.workload: ms.topic: conceptual ms.date: 08/31/2020 ms.author: inhenkel
---

# <a name="stream-content-with-cdn-integration"></a>CDN 통합을 사용하여 콘텐츠 스트리밍

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Azure CDN(콘텐츠 전송 네트워크)은 전 세계에 전략적으로 배치된 물리적 노드에서 콘텐츠를 캐싱하여 사용자에게 고대역폭 콘텐츠를 신속하게 전송할 수 있는 글로벌 솔루션을 개발자에게 제공합니다.  

CDN은 코덱별, 스트리밍 프로토콜별, 비트 전송률별, 컨테이너 형식별 및 암호화/DRM별로 Media Services [스트리밍 엔드포인트(원본)](stream-streaming-endpoint-concept.md)에서 스트리밍되는 콘텐츠를 캐시합니다. 코덱-스트리밍 프로토콜-컨테이너 형식-비트 전송률-암호화의 각 조합에 대해 별도의 CDN 캐시가 있습니다.

비디오 조각이 캐시되면, 인기 있는 콘텐츠가 CDN 캐시에서 직접 제공되는 것입니다. 일반적으로 많은 사람들이 정확히 동일한 것을 보게 하므로 라이브 콘텐츠는 캐시될 확률이 높습니다. 인기 있는 콘텐츠도 있고 그렇지 않은 콘텐츠도 있으므로 주문형 콘텐츠는 좀 더 까다로울 수 있습니다. 수백만 개의 비디오 자산이 있고 모두 인기가 없지만(시청자가 1주일에 1명 또는 2명뿐) 수천 명의 사람들이 모두 다른 비디오를 보는 경우 CDN이 훨씬 덜 효과적이게 됩니다.

또한 적응 스트리밍의 작동 방식도 고려해야 합니다. 각 개별 비디오 조각은 자체 엔터티로 캐시됩니다. 예를 들어 특정 비디오가 처음으로 시청될 때를 가정해 보세요. 시청자가 몇 초 동안 이리 저리 건너뛰며 본 경우, 사용자가 시청한 것과 관련된 비디오 조각만 CDN에 캐시됩니다. 적응 스트리밍을 사용하면 비디오의 비트 전송률이 일반적으로 5~ 7 사이의 차이를 보입니다. 한 사람이 한 비트 전송률로 시청 중이고, 다른 사람이 다른 비트 전송률로 시청 중이면 CDN에서 각각 따로 캐시됩니다. 두 사람이 동일한 비트 전송률로 시청 중이라고 해도 다른 프로토콜을 통해 스트리밍될 수 있습니다. 각 프로토콜(HLS, MPEG DASH, 부드러운 스트리밍)은 별도로 캐시됩니다. 따라서 각 비트 전송률 및 프로토콜은 개별적으로 캐시되고, 요청된 비디오 조각만 캐시됩니다.

테스트 환경을 제외하고 표준 및 프리미엄 스트리밍 엔드포인트 모두에 대해 CDN을 사용하도록 설정하는 것이 좋습니다. 각 스트리밍 엔드포인트 유형에는 서로 다르게 지원되는 처리량 제한이 있습니다.
고려해야 하는 요소가 다양하므로 여러 스트리밍 엔드포인트에서 지원되는 최대 동시 스트림 수를 정확하게 계산하는 것은 어려울 수 있습니다. 이러한 위협은 다음과 같습니다.

- 스트리밍에 사용되는 최대 비트 전송률
- 플레이어 사전 버퍼 및 전환 동작. 플레이어는 원본에서 세그먼트 버스트를 시도하고 로드 속도를 사용하여 적응 비트 전송률 전환을 계산합니다. 스트리밍 엔드포인트가 채도에 근접하는 경우 응답 시간은 다를 수 있으며 플레이어는 더 낮은 품질로 전환하기 시작합니다. 스트리밍 엔드포인트 플레이어에서 부하가 줄어들기 때문에 더 높은 품질로 확장하여 원치 않는 전환 트리거를 만듭니다.
대체로 최대 스트리밍 엔드포인트 처리량을 가져와서 최대 동시 스트림을 추정하고 최대 비트 전송률로 나누는 것이 안전합니다(모든 플레이어가 가장 높은 비트 전송률을 사용한다고 가정). 예를 들어, 600Mbps로 제한되고 최고 비트 전송률이 3Mbps인 표준 스트리밍 엔드포인트가 있을 수 있습니다. 이 경우 최고 비트 전송률에서 약 200개의 동시 스트림이 지원됩니다. 오디오 대역폭 요구 사항도 고려해야 합니다. 오디오 스트림은 128kps에서만 스트리밍될 수 있지만, 동시 스트림의 수로 곱하면 전체 스트리밍이 빠르게 증가합니다.

이 항목에서는 [CDN 통합](#enable-azure-cdn-integration)을 사용하도록 설정하는 방법을 설명합니다. 또한 프리페치(활성 캐싱) 및 [원본 지원 CDN 프리페치](#origin-assist-cdn-prefetch) 개념을 설명합니다.

## <a name="considerations"></a>고려 사항

- [스트리밍 엔드포인트](stream-streaming-endpoint-concept.md) `hostname` 및 스트리밍 URL은 CDN을 사용하도록 설정했는지 여부에 관계없이 동일하게 유지됩니다.
- CDN이 있거나 없는 콘텐츠를 테스트하는 기능이 필요하면 CDN이 사용하도록 설정되지 않은 또 다른 스트리밍 엔드포인트를 만듭니다.

## <a name="enable-azure-cdn-integration"></a>Azure CDN 통합 사용

> [!IMPORTANT]
> 평가판 또는 학생용 Azure 계정에 대해 CDN을 사용하도록 설정할 수 없습니다.
>
> CDN 통합은 중국 및 연방 정부 지역을 제외한 모든 Azure 데이터 센터에서 사용됩니다.

CDN을 사용하여 스트리밍 엔드포인트를 프로비전한 후에는 스트리밍 엔드포인트를 CDN 엔드포인트에 매핑하기 위해 DNS 업데이트를 수행하기 전에 Media Services에 정의된 대기 시간이 있습니다.

나중에 CDN을 사용/사용 안 함으로 설정하려면 스트리밍 엔드포인트가 **중지됨** 상태여야 합니다. 스트리밍 엔드포인트가 시작되면, Azure CDN 통합이 사용하도록 설정되고 변경 내용이 모든 CDN POP에서 활성화되는 데 최대 4시간이 걸릴 수 있습니다. 단, 스트리밍 엔드포인트를 시작하고 스트리밍 엔드포인트에서 중단 없이 스트림할 수 있습니다. 통합이 완료되면 CDN에서 스트림이 배달됩니다. 프로비저닝 기간 동안에는 스트리밍 엔드포인트가 **시작** 상태가 되고 성능 저하가 관찰될 수 있습니다.

표준 스트리밍 엔드포인트가 생성될 때 기본적으로 Standard Verizon을 사용하여 구성됩니다. REST API를 사용하여 Premium Verizon 또는 Standard Akamai 공급자를 구성할 수 있습니다.

Azure Media Services와 Azure CDN의 통합은 **Verizon의 Azure CDN** 에서 표준 스트리밍 엔드포인트에 구현됩니다. 프리미엄 스트리밍 엔드포인트는 모든 **Azure CDN 가격 책정 및 공급자** 를 사용하여 구성할 수 있습니다.

> [!NOTE]
> Azure CDN에 대한 자세한 내용은 [CDN 개요](../../cdn/cdn-overview.md)를 참조하세요.

## <a name="determine-if-a-dns-change-was-made"></a>DNS가 변경되었는지 확인

<https://www.digwebinterface.com>을 사용하여 스트리밍 엔드포인트에서 DNS가 변경되었는지 확인할 수 있습니다(트래픽이 Azure CDN으로 디렉션됨). azureedge.net 도메인 이름이 결과에 표시되면 이제 트래픽은 CDN을 가리킵니다.

## <a name="origin-assist-cdn-prefetch"></a>원본 지원 CDN 프리페치

CDN 캐싱은 반응성 프로세스입니다. CDN에서 앞으로 요청될 다음 개체가 무엇인지 예측할 수 있는 경우 CDN은 사전에 다음 개체를 요청하고 캐시할 수 있습니다. 이 프로세스를 사용하면 모든(또는 대부분) 개체에 대해 캐시 적중을 수행하여 성능을 향상시킬 수 있습니다.

프리페칭의 개념은 개체를 “인터넷의 에지”에 배치하기 위한 것으로, 이는 플레이어가 요청할 것을 예상하여 플레이어에 개체를 배달하는 시간을 단축시킵니다.

이러한 목표를 달성하기 위해 스트리밍 엔드포인트(원본)와 CDN은 몇 가지 방법으로 직접 작업해야 합니다.

- 프리페치할 다음 개체를 CDN에 알리기 위해 Media Services 원본에는 “인텔리전스”(원본 지원)가 있어야 합니다.
- CDN은 프리페치 및 캐싱(CDN 프리페치 부분)을 수행합니다. CDN에는 프리페치 또는 일반 페치인지 여부, 404 응답 처리, 무한 프리페치 루프를 방지하는 방법 등을 원본에 알리기 위한 “인텔리전스”도 있어야 합니다.

### <a name="benefits"></a>이점

*원본 지원 CDN 프리페치* 기능의 이점은 다음과 같습니다.

- 프리페치는 재생하는 동안 예상되는 비디오 세그먼트를 에지에 미리 배치하여 비디오 재생 품질을 향상시키고, 뷰어에 대한 대기 시간을 줄이고, 비디오 세그먼트 다운로드 시간을 개선합니다. 이로 인해 비디오 시작 시간이 단축되고 다시 버퍼링 횟수가 줄어듭니다.
- 이 개념은 일반적인 CDN 원본 시나리오에 적용되며 미디어에 제한되지 않습니다.
- Akamai에서 이 기능을 [ACE(Akamai Cloud Embed)](https://learn.akamai.com/en-us/products/media_delivery/cloud_embed.html)에 추가했습니다.

> [!NOTE]
> 이 기능은 Media Services 스트리밍 엔드포인트와 통합된 Akamai CDN에 아직 적용되지 않습니다. 단, 기존 Akamai 계약이 있고 Akamai CDN과 Media Services 원본 간에 사용자 지정 통합이 필요한 Media Services 고객에게 제공됩니다.

### <a name="how-it-works"></a>작동 방식

`Origin-Assist CDN-Prefetch` 헤더(라이브 및 비디오 주문형 스트리밍 모두에 해당)에 대한 CDN 지원이 Akamai CDN을 사용하여 직접 계약을 체결한 고객에게 제공됩니다. 기능에는 Akamai CDN과 Azure Media Services 원본 간의 다음 HTTP 헤더 교환이 포함됩니다.

|HTTP 헤더|값|보낸 사람|받는 사람|목적|
| ---- | ---- | ---- | ---- | ----- |
|`CDN-Origin-Assist-Prefetch-Enabled` | 1(기본값) 또는 0 |CDN|원본|CDN 프리페치가 사용되도록 설정되었음을 표시하기 위함입니다.|
|`CDN-Origin-Assist-Prefetch-Path`| 예제: <br/>Fragments(video=1400000000,format=mpd-time-cmaf)|원본|CDN|CDN에 대한 프리페치 경로를 제공하기 위함입니다.|
|`CDN-Origin-Assist-Prefetch-Request`|1(프리페치 요청) 또는 0(일반 요청)|CDN|원본|CDN의 요청이 프리페치됨을 나타내기 위함입니다.|

헤더 교환의 작동 방식을 확인하려면 다음 단계를 수행합니다.

1. Postman 또는 cURL을 사용하여 오디오 또는 비디오 세그먼트나 조각의 Media Services 원본 요청을 발급합니다. 요청에 `CDN-Origin-Assist-Prefetch-Enabled: 1` 헤더가 추가되었는지 확인합니다.
2. 응답에는 상대 경로를 값으로 사용하는 `CDN-Origin-Assist-Prefetch-Path` 헤더가 표시됩니다.

### <a name="supported-streaming-protocols"></a>지원되는 스트리밍 프로토콜

`Origin-Assist CDN-Prefetch` 기능은 라이브 및 주문형 스트리밍에 대해 다음과 같은 스트리밍 프로토콜을 지원합니다.

* HLS v3
* HLS v4
* HLS CMAF
* DASH(CSF)
* DASH(CMAF)
* 부드러운 스트리밍

### <a name="faqs"></a>FAQ

* 프리페치 경로 URL이 잘못되어 CDN 프리페치에서 404를 가져오는 경우는 어떻게 되나요?

    CDN은 10초(또는 기타 구성된 값) 동안 404 응답만 캐시합니다.

* 주문형 비디오가 있다고 가정합니다. CDN 프리페치를 사용하는 경우 이 기능은 클라이언트가 첫 번째 비디오 세그먼트를 요청하면 프리페치가 동일한 비트 전송률에서 모든 후속 비디오 세그먼트를 프리페치하는 루프를 시작한다는 것을 의미하나요?

    아니요. CDN 프리페치는 클라이언트에서 시작한 요청/응답 후에만 수행됩니다. CDN 프리페치는 프리페치 루프를 방지하기 위해 프리페치에 의해 트리거되지 않습니다.

* 원본 지원 CDN 프리페치 기능은 항상 켜져 있나요? 설정/해제하려면 어떻게 해야 하나요?

    이 기능은 기본적으로 해제되어 있습니다. 고객이 Akamai API를 통해 설정해야 합니다.

* 라이브 스트리밍의 경우 다음 세그먼트 또는 조각을 아직 사용할 수 없는 경우 원본 지원에 어떤 일이 일어나나요?

    이 경우 Media Services 원본은 `CDN-Origin-Assist-Prefetch-Path` 헤더를 제공하지 않고 CDN 프리페치는 발생하지 않습니다.

* `Origin-Assist CDN-Prefetch`는 동적 매니페스트 필터와 어떻게 작동하나요?

    이 기능은 매니페스트 필터와 독립적으로 작동합니다. 다음 조각이 필터 창에 포함되지 않은 경우 해당 URL을 계속 원시 클라이언트 매니페스트에서 찾은 다음, CDN 프리페치 응답 헤더로 반환합니다. 따라서 CDN은 DASH/HLS/Smooth 매니페스트에서 필터링된 조각의 URL을 가져옵니다. 그러나 플레이어가 보유한 DASH/HLS/Smooth 매니페스트에 해당 조각이 포함되어 있지 않기 때문에 플레이어는 CDN에 해당 조각을 페치하기 위한 GET 요청을 하지 않습니다.

* DASH MPD/HLS 재생 목록/Smooth 매니페스트를 프리페치할 수 있나요?

    아니요, DASH MPD, HLS 마스터 재생 목록, HLS 변형 재생 목록 또는 Smooth 매니페스트 URL은 프리페치 헤더에 추가되지 않습니다.

* 프리페치 URL은 상대적인가요 아니면 절대적인가요?

    Akamai CDN은 둘 다 허용하지만, 절대 URL을 사용해도 아무런 이점이 없으므로 Media Services 원본은 프리페치 경로에 대해 상대 URL만 제공합니다.

* 이 기능은 DRM으로 보호된 콘텐츠를 사용하나요?

    예, 이 기능은 HTTP 수준에서 작동하므로 모든 세그먼트/조각을 디코드하거나 구문 분석하지 않습니다. 콘텐츠가 암호화되는지 여부를 고려하지 않습니다.

* 이 기능은 SSAI(서버 쪽 광고 삽입)와 작동하나요?
    
    SSAI는 Media Services 원본에서 원본 콘텐츠의 타임스탬프를 변경하지 않으므로 원본/기본 콘텐츠(광고 삽입 전의 원본 비디오 콘텐츠)가 작동합니다. 이 기능이 광고 콘텐츠와 작동하는지 여부는 광고 원본이 원본 지원을 지원하는지 여부에 따라 달라집니다. 예를 들어 광고 콘텐츠가 Azure Media Services(동일 또는 별도의 원본)에도 호스트되는 경우 광고 콘텐츠가 프리페치됩니다.

* 이 기능은 UHD/HEVC 콘텐츠와 작동하나요?

    예.

## <a name="ask-questions-give-feedback-get-updates"></a>질문, 피드백 제공, 업데이트 받기

[Azure Media Services 커뮤니티](media-services-community.md) 문서를 체크 아웃하여 다양한 방법으로 질문을 하고, 피드백을 제공하고, Media Services에 대한 업데이트를 가져올 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [스트리밍 엔드포인트(원본)](stream-streaming-endpoint-concept.md) 설명서를 검토하세요.
* [이 리포지토리](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs)의 샘플은 .NET을 사용하여 기본 스트리밍 엔드포인트를 시작하는 방법을 보여 줍니다.
