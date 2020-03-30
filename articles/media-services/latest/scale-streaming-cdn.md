---
title: CDN 통합을 통해 콘텐츠 스트리밍
titleSuffix: Azure Media Services
description: CDN 통합을 통해 콘텐츠를 스트리밍하는 것, 프리페칭 및 오리진 어시스트 CDN-Prefetch에 대해 알아보십시오.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 02/13/2020
ms.author: juliako
ms.openlocfilehash: 4ed8ada306720b7a8b44ddd59cefe399238c906a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80128063"
---
# <a name="stream-content-with-cdn-integration"></a>CDN 통합을 통해 콘텐츠 스트리밍

Azure CDN(콘텐츠 전송 네트워크)은 전 세계에 전략적으로 배치된 물리적 노드에서 콘텐츠를 캐싱하여 사용자에게 고대역폭 콘텐츠를 신속하게 전송할 수 있는 글로벌 솔루션을 개발자에게 제공합니다.  

CDN은 코덱당, 스트리밍 프로토콜, 비트 전송률, 컨테이너 형식별 및 암호화/DRM당 미디어 서비스 [스트리밍 엔드포인트(ORIGIN)에서](streaming-endpoint-concept.md) 스트리밍된 콘텐츠를 캐시합니다. 코덱 스트리밍 프로토콜-컨테이너 형식-비트 레이트-암호화의 각 조합에 대해 별도의 CDN 캐시가 있습니다.

인기 있는 콘텐츠는 비디오 조각이 캐시되는 한 CDN 캐시에서 직접 제공됩니다. 일반적으로 많은 사람들이 정확히 동일한 것을 보게 하므로 라이브 콘텐츠는 캐시될 확률이 높습니다. 인기 있는 콘텐츠와 그렇지 않은 콘텐츠가 있을 수 있기 때문에 주문형 콘텐츠가 다소 까다로워질 수 있습니다. 수백만 개의 비디오 자산이 있는데 그 중 어느 것도 인기가 없지만(일주일에 한두 명의 시청자만) 수천 명의 사람들이 다른 동영상을 시청하는 경우 CDN의 효율성이 훨씬 낮아집니다.

또한 적응 스트리밍의 작동 방식도 고려해야 합니다. 각 개별 비디오 조각은 자체 엔터티로 캐시됩니다. 예를 들어 특정 동영상이 처음 시청될 때를 상상해 보겠습니다. 시청자가 여기저기에서 몇 초만 시청하는 경우, 본 사람과 관련된 비디오 조각만 CDN에 캐시됩니다. 적응 스트리밍을 사용하면 비디오의 비트 전송률이 일반적으로 5~ 7 사이의 차이를 보입니다. 한 사람이 한 비트 전송률을 보고 다른 사람이 다른 비트 전송률을 보고 있는 경우 각 사람이 CDN에서 별도로 캐시됩니다. 두 사람이 동일한 비트 전송률을 보고 있더라도 다른 프로토콜을 통해 스트리밍될 수 있습니다. 각 프로토콜(HLS, MPEG DASH, 부드러운 스트리밍)은 별도로 캐시됩니다. 따라서 각 비트 전송률 및 프로토콜은 개별적으로 캐시되고, 요청된 비디오 조각만 캐시됩니다.

미디어 서비스 [스트리밍 엔드포인트에서](streaming-endpoint-concept.md)CDN을 사용하도록 설정할지 여부를 결정할 때는 예상 뷰어 수를 고려합니다. CDN은 콘텐츠에 대해 많은 시청자를 기대하는 경우에만 도움이 됩니다. 뷰어의 최대 동시성보다 낮은 경우 CDN이 동시성에서 가장 잘 조정되므로 CDN을 사용하지 않도록 설정하는 것이 좋습니다.

이 항목에서는 [CDN 통합을](#enable-azure-cdn-integration)사용하도록 설정하는 것에 대해 설명합니다. 또한 프리페칭(액티브 캐칭)과 [오리진 어시스트 CDN-프레페치](#origin-assist-cdn-prefetch) 컨셉에 대해서도 설명합니다.

## <a name="considerations"></a>고려 사항

* [스트리밍 끝점과](streaming-endpoint-concept.md) `hostname` 스트리밍 URL은 CDN을 사용하도록 설정하든 관계없이 동일하게 유지됩니다.
* CDN 유무에 관계없이 콘텐츠를 테스트할 수 있는 기능이 필요한 경우 CDN이 활성화되지 않은 다른 스트리밍 끝점을 만듭니다.

## <a name="enable-azure-cdn-integration"></a>Azure CDN 통합 사용

> [!IMPORTANT]
> 평가판 또는 학생 Azure 계정에는 CDN을 사용하도록 설정할 수 없습니다.
>
> CDN 통합은 연방 정부 및 중국 지역을 제외한 모든 Azure 데이터 센터에서 활성화됩니다.

스트리밍 끝점을 CDN 사용하도록 프로비전한 후 스트리밍 끝점을 CDN 끝점에 매핑하기 위해 DNS 업데이트가 수행되기 전에 Media Services에서 정의된 대기 시간이 있습니다.

나중에 CDN을 사용/사용 안 함으로 설정하려면 스트리밍 엔드포인트가 **중지됨** 상태여야 합니다. Azure CDN 통합이 사용하도록 설정되고 변경 내용이 모든 CDN POP에서 활성화되려면 최대 2시간이 걸릴 수 있습니다. 그러나 스트리밍 끝점의 중단 없이 스트리밍 끝점을 시작하고 스트리밍할 수 있습니다. 통합이 완료되면 스트림은 CDN에서 전달됩니다. 프로비저닝 기간 동안 스트리밍 끝점은 **시작** 상태에 있으며 성능이 저하되는 것을 관찰할 수 있습니다.

표준 스트리밍 끝점이 만들어지면 기본적으로 표준 버라이존으로 구성됩니다. REST API를 사용하여 프리미엄 버라이존 또는 표준 Akamai 공급자를 구성할 수 있습니다.

Azure Media Services와 Azure CDN의 통합은 **Verizon의 Azure CDN**에서 표준 스트리밍 엔드포인트에 구현됩니다. 프리미엄 스트리밍 엔드포인트는 모든 **Azure CDN 가격 책정 및 공급자**를 사용하여 구성할 수 있습니다.

> [!NOTE]
> Azure CDN에 대한 자세한 내용은 [CDN 개요를](../../cdn/cdn-overview.md)참조하십시오.

## <a name="determine-if-a-dns-change-was-made"></a>DNS 변경 이 었는지 확인

을 사용하여 <https://www.digwebinterface.com>스트리밍 끝점(트래픽이 Azure CDN으로 전달됨)에서 DNS 변경이 이루어졌는지 확인할 수 있습니다. 결과에 azureedge.net 도메인 이름이 표시되면 트래픽이 CDN을 가리키고 있습니다.

## <a name="origin-assist-cdn-prefetch"></a>오리진 어시스트 CDN-프레페치

CDN 캐싱은 반응성 프로세스입니다. CDN이 다음 개체를 요청할 대상을 예측할 수 있는 경우 CDN은 다음 개체를 사전에 요청하고 캐시할 수 있습니다. 이 프로세스를 사용하면 개체의 전부(또는 대부분)에 대해 캐시 적중을 달성하여 성능을 향상시킬 수 있습니다.

프리페칭의 개념은 플레이어가 곧 요청할 것이라는 기대로 "인터넷의 가장자리"에 객체를 배치하여 플레이어에게 해당 객체를 전달하는 시간을 줄입니다.

이 목표를 달성하려면 스트리밍 끝점(원점) 및 CDN이 몇 가지 방법으로 함께 작업해야 합니다.

- 미디어 서비스 원본에는 CDN에 다음 개체를 미리 가져오기 위해 "인텔리전스"(Origin-Assist)가 있어야 합니다.
- CDN은 프리페치 및 캐칭(CDN-프리페치 부분)을 수행합니다. 또한 CDN은 프리페치 또는 일반 페치 여부, 404 응답 처리, 끝없는 프리페치 루프를 피할 수 있는 방법을 알리기 위해 "인텔리전스"가 있어야 합니다.

### <a name="benefits"></a>이점

오리진 어시스트 *CDN-프레페치* 기능의 이점은 다음과 같습니다.

- Prefetch는 재생 중에 예상되는 비디오 세그먼트를 에지에서 미리 배치하여 시청자에게 대기 시간을 줄이고 비디오 세그먼트 다운로드 시간을 개선하여 비디오 재생 품질을 개선합니다. 이로 인해 비디오 시작 시간이 빨라지고 재버퍼링 발생시간이 단축됩니다.
- 이 개념은 일반적인 CDN 원본 시나리오에 적용되며 미디어에만 국한되지 않습니다.
- [Akamai클라우드임베데(ACE)에](https://learn.akamai.com/en-us/products/media_delivery/cloud_embed.html)이 기능을 추가했습니다.

> [!NOTE]
> 이 기능은 미디어 서비스 스트리밍 엔드포인트와 통합된 Akamai CDN에는 아직 적용되지 않습니다. 그러나 기존 Akamai 계약을 맺고 Akamai CDN과 미디어 서비스 원산지 간의 사용자 지정 통합이 필요한 미디어 서비스 고객은 사용할 수 있습니다.

### <a name="how-it-works"></a>작동 방법

Akamai CDN과 `Origin-Assist CDN-Prefetch` 직접 계약을 맺은 고객은 헤더(라이브 및 비디오 주문형 스트리밍 모두)에 대한 CDN 지원을 사용할 수 있습니다. 이 기능은 Akamai CDN과 미디어 서비스 출처 간의 다음과 같은 HTTP 헤더 교환을 포함합니다.

|HTTP 헤더|값|보낸 사람|받는 사람|목적|
| ---- | ---- | ---- | ---- | ----- |
|`CDN-Origin-Assist-Prefetch-Enabled` | 1(기본값) 또는 0 |CDN|원본|CDN이 프리페치가 활성화되었음을 나타냅니다.|
|`CDN-Origin-Assist-Prefetch-Path`| 예제: <br/>조각(비디오=14000000000,포맷=mpd 시간-cmaf)|원본|CDN|CDN에 대한 프리페치 경로를 제공합니다.|
|`CDN-Origin-Assist-Prefetch-Request`|1 (프리페치 요청) 또는 0(일반 요청)|CDN|원본|CDN의 요청을 나타내는 것은 프리페치입니다.|

헤더 교환의 일부를 확인하려면 다음 단계를 시도해 볼 수 있습니다.

1. Postman 또는 cURL을 사용하여 오디오 또는 비디오 세그먼트 또는 조각에 대한 미디어 서비스 출처에 요청을 발행합니다. 요청에 헤더를 `CDN-Origin-Assist-Prefetch-Enabled: 1` 추가해야 합니다.
2. 응답에서 상대 경로가 있는 `CDN-Origin-Assist-Prefetch-Path` 헤더를 해당 값으로 표시해야 합니다.

### <a name="supported-streaming-protocols"></a>지원되는 스트리밍 프로토콜

이 `Origin-Assist CDN-Prefetch` 기능은 라이브 및 온디맨드 스트리밍을 위한 다음과 같은 스트리밍 프로토콜을 지원합니다.

* HLS v3
* HLS v4
* HLS CMAF
* 대시 (CSF)
* 대시 (CMAF)
* 부드러운 스트리밍

### <a name="faqs"></a>FAQ

* CDN 프리페치가 404를 가져오므로 프리페치 경로 URL이 유효하지 않은 경우 어떻게 해야 합니까?

    CDN은 10초 동안 404응답(또는 다른 구성된 값)만 캐시합니다.

* 주문형 비디오가 있다고 가정합니다. CDN-prefetch가 활성화된 경우, 이 기능은 클라이언트가 첫 번째 비디오 세그먼트를 요청하면 프리페치가 동일한 비트 레이트에서 모든 후속 비디오 세그먼트를 프리페치하는 루프를 시작한다는 것을 의미합니까?

    아니요, CDN-프리페치는 클라이언트가 시작한 요청/응답 후에만 수행됩니다. CDN 프리페치는 프리페치 루프를 피하기 위해 프리페치에 의해 트리거되지 않습니다.

* 오리진 어시스트 CDN-프리페치 기능이 항상 켜져 있습니까? 어떻게 켜고 끌 수 있습니까?

    이 기능은 기본적으로 해제되어 있습니다. 고객은 Akamai API를 통해 켜야 합니다.

* 라이브 스트리밍의 경우 다음 세그먼트 또는 조각을 아직 사용할 수 없는 경우 Origin-Assist는 어떻게 됩니까?

    이 경우 미디어 서비스 원본헤더를 `CDN-Origin-Assist-Prefetch-Path` 제공하지 않으며 CDN 프리페치가 발생하지 않습니다.

* 동적 `Origin-Assist CDN-Prefetch` 매니페스트 필터는 어떻게 작동합니까?

    이 기능은 매니페스트 필터와 독립적으로 작동합니다. 다음 조각이 필터 창밖으로 나오면 원시 클라이언트 매니페스트를 검색한 다음 CDN 프리페치 응답 헤더로 반환하여 URL을 계속 찾습니다. 따라서 CDN은 DASH /HLS / 스무딩 매니페스트에서 필터링 된 조각의 URL을 얻을 것입니다. 그러나 플레이어는 해당 조각이 플레이어가 보유한 DASH/HLS/Smooth 매니페스트에 포함되지 않기 때문에 해당 조각을 가져오도록 CDN에 GET 요청을 하지 않습니다(플레이어는 해당 조각의 존재를 알지 못합니다).

* DASH MPD/HLS 재생 목록/스무스 매니페스트를 미리 페치할 수 있습니까?

    아니요, DASH MPD, HLS 마스터 재생 목록, HLS 변형 재생 목록 또는 부드러운 매니페스트 URL은 프리페치 헤더에 추가되지 않습니다.

* 프리페치 URL은 상대적이거나 절대적입니까?

    Akamai CDN은 둘 다 허용하지만 절대 URL을 사용할 때 명백한 이점이 없기 때문에 미디어 서비스 원본은 사전 가져오기 경로에 대한 상대 URL만 제공합니다.

* 이 기능은 DRM으로 보호된 콘텐츠에서 작동합니까?

    예. 이 기능은 HTTP 수준에서 작동하므로 세그먼트/조각을 디코딩하거나 구문 분석하지 않습니다. 콘텐츠가 암호화되었는지 여부는 중요하지 않습니다.

* 이 기능은 서버 측 광고 삽입(SSAI)에서 작동합니까?
    
    SSAI는 미디어 서비스 출처에서 원본 콘텐츠의 타임스탬프를 변경하지 않으므로 원본/기본 콘텐츠(광고 삽입 전 원본 동영상 콘텐츠)에 대해 작동합니다. 이 기능이 광고 콘텐츠와 함께 작동하는지 여부는 광고 오리진이 Origin-Assist를 지원하는지 여부에 따라 달라집니다. 예를 들어 광고 콘텐츠가 Azure Media Services(동일하거나 별도의 출처)에서도 호스팅되는 경우 광고 콘텐츠도 미리 전송됩니다.

* 이 기능은 UHD/HEVC 콘텐츠와 함께 작동합니까?

    예.

## <a name="ask-questions-give-feedback-get-updates"></a>질문, 피드백 제공, 업데이트 받기

[Azure Media Services 커뮤니티](media-services-community.md) 문서를 체크 아웃하여 다양한 방법으로 질문을 하고, 피드백을 제공하고, Media Services에 대한 업데이트를 가져올 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [스트리밍 끝점(원본)](streaming-endpoint-concept.md) 문서를 검토해야 합니다.
* [이 리포지토리](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs)의 샘플은 .NET을 사용하여 기본 스트리밍 엔드포인트를 시작하는 방법을 보여 줍니다.
