---
title: 스트리밍 끝점(원점)
titleSuffix: Azure Media Services
description: 클라이언트 플레이어 앱 또는 CDN(콘텐츠 전송 네트워크)에 직접 콘텐츠를 전달하는 동적 패키징 및 스트리밍 서비스인 Origin 스트리밍 엔드포인트(Origin)에 대해 알아봅니다.
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
ms.openlocfilehash: 72cfdf172e4524e302ef2e22826d4f78ce32daf0
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/02/2020
ms.locfileid: "80582723"
---
# <a name="streaming-endpoints-origin-in-azure-media-services"></a>Azure 미디어 서비스의 스트리밍 끝점(원본)

Microsoft Azure 미디어 서비스에서 [스트리밍 엔드포인트는](https://docs.microsoft.com/rest/api/media/streamingendpoints) 공통 스트리밍 미디어 프로토콜(HLS 또는 DASH) 중 하나를 사용하여 라이브 및 온디맨드 콘텐츠를 클라이언트 플레이어 앱에 직접 제공할 수 있는 동적(적시에) 패키징 및 원본 서비스를 나타냅니다. 또한 스트리밍 **엔드포인트는** 업계 를 선도하는 DRM에 동적(적시에) 암호화를 제공합니다. 

Media Services 계정을 만들 경우 **기본** 스트리밍 엔드포인트가 중지됨 상태로 만들어집니다. **기본** 스트리밍 끝점을 삭제할 수 없습니다. 더 많은 스트리밍 엔드포인트는 계정으로 만들 수 [있습니다(할당량 및 제한](limits-quotas-constraints.md)참조).

> [!NOTE]
> 비디오 스트리밍을 시작하려면 비디오를 스트리밍하려는 **스트리밍 엔드포인트**를 시작해야 합니다.
>
> 스트리밍 끝점이 실행 중인 상태에 있을 때만 요금이 청구됩니다.

[동적 패키징](dynamic-packaging-overview.md) 항목도 검토해야 합니다. 

## <a name="naming-convention"></a>명명 규칙

스트리밍 URL의 호스트 이름 형식은 `{servicename}-{accountname}-{regionname}.streaming.media.azure.net`다음과 `servicename` 입니다.

기본 스트리밍 끝점을 사용하는 `servicename` 경우 URL이 다음과 `{accountname}-{regionname}.streaming.azure.net`같은 데다가 생략됩니다.

### <a name="limitations"></a>제한 사항

* 스트리밍 끝점 이름에는 최대 값이 24자입니다.
* 이름은 이 [정규식](https://docs.microsoft.com/dotnet/standard/base-types/regular-expression-language-quick-reference) 패턴을 `^[a-zA-Z0-9]+(-*[a-zA-Z0-9])*$`따라야 합니다.

## <a name="types"></a>유형

**스트리밍 엔드포인트** 유형에는 **표준(미리** 보기) 및 **프리미엄**의 두 가지 유형이 있습니다. 이러한 유형은 스트리밍 엔드포인트에 할당하는 배율 단위(`scaleUnits`) 수로 정의됩니다.

다음 표에 해당 유형이 설명되어 있습니다.

|Type|배율 단위|설명|
|--------|--------|--------|  
|**Standard**|0|기본 스트리밍 끝점은 **표준** 유형이며 `scaleUnits`을 조정하여 프리미엄 유형으로 변경할 수 있습니다.|
|**Premium**|>0|**프리미엄 (주)** 스트리밍 엔드포인트는 고급 워크로드에 적합하며 전용 및 확장 가능한 대역폭 용량을 제공합니다. (스트리밍 단위)를 조정하여 `scaleUnits` **프리미엄** 유형으로 이동합니다. `scaleUnits`는 200Mbps 단위로 구입할 수 있는 전용 송신 용량을 제공합니다. **프리미엄** 유형을 사용하는 경우 각 사용 가능한 단위는 앱에 추가 대역폭 용량을 제공합니다. |

> [!NOTE]
> 대규모 인터넷 고객에게 콘텐츠를 제공하려는 고객은 스트리밍 엔드포인트에서 CDN을 사용하도록 설정하는 것이 좋습니다.

SLA 정보는 [가격 및 SLA](https://azure.microsoft.com/pricing/details/media-services/)를 참조하십시오.

## <a name="comparing-streaming-types"></a>스트리밍 유형 비교

기능|Standard|Premium
---|---|---
처리량 |최대 600Mbps이며 CDN을 사용할 때 훨씬 더 높은 효과적인 처리량을 제공할 수 있습니다.|SU(스트리밍 단위)당 200Mbps CDN을 사용할 때 훨씬 더 높은 유효 처리량을 제공할 수 있습니다.
CDN|Azure CDN, 타사 CDN 또는 CDN이 없습니다.|Azure CDN, 타사 CDN 또는 CDN이 없습니다.
청구를 계산합니다.| 매일|매일
동적 암호화|예|예
동적 패키징|예|예
확장|대상 처리량까지 자동으로 확장합니다.|추가 수
IP 필터링/G20/사용자 지정 호스트 <sup>1</sup>|예|예
점진적 다운로드|예|예
권장 사용량 |대부분의 스트리밍 시나리오에 권장됩니다.|전문 사용량입니다.

<sup>1</sup> CDN이 엔드포인트에서 활성화되지 않은 경우에만 스트리밍 엔드포인트에서 직접 사용됩니다.<br/>

## <a name="streaming-endpoint-properties"></a>스트리밍 끝점 속성

이 섹션에서는 스트리밍 끝점의 일부 속성에 대한 세부 정보를 제공합니다. 새 스트리밍 엔드포인트를 만드는 방법의 예제와 모든 속성에 대한 설명을 보려면 [스트리밍 엔드포인트](https://docs.microsoft.com/rest/api/media/streamingendpoints/create)를 참조하세요.

- `accessControl`: 이 스트리밍 끝점에 대해 다음과 같은 보안 설정을 구성하는 데 사용됩니다: 이 끝점에 연결할 수 있는 Akamai 서명 헤더 인증 키 및 IP 주소입니다. 이 속성은 false로 `cdnEnabled` 설정된 경우에만 설정할 수 있습니다.

- `cdnEnabled`: 이 스트리밍 끝점에 대한 Azure CDN 통합이 활성화되어 있는지 를 나타냅니다(기본적으로 비활성화됨). `cdnEnabled`를 true로 설정하면 `customHostNames` 및 `accessControl` 구성이 사용되지 않도록 설정됩니다.

    모든 데이터 센터에서 Azure CDN 통합을 지원하는 것은 아닙니다. 데이터 센터에 Azure CDN 통합을 사용할 수 있는지 확인하려면 다음 단계를 수행합니다.

  - `cdnEnabled`를 true로 설정합니다.
  - "스트리밍 끝점 `HTTP Error Code 412` CdnEnabled 속성은 현재 리전에서 CDN 기능을 사용할 수 없기 때문에 true로 설정할 수 없습니다"라는 메시지가 있는 반환된 결과(PreconditionFailed)를 확인합니다.

    이 오류가 발생하면 데이터 센터에서 지원하지 않습니다. 다른 데이터 센터를 사용해 보십시오.

- `cdnProfile`: `cdnEnabled` true로 설정된 경우 값을 `cdnProfile` 전달할 수도 있습니다. `cdnProfile`은 CDN 엔드포인트이 생성되는 CDN 프로필의 이름입니다. 기존 cdnProfile을 제공하거나 새로 만들 수 있습니다. 값이 NULL이고 `cdnEnabled`가 true이면 기본값 “AzureMediaStreamingPlatformCdnProfile”이 사용됩니다. 제공된 `cdnProfile`이 이미 있으면 그 아래에 엔드포인트가 생성됩니다. 프로필이 없으면 새 프로필이 자동으로 만들어집니다.
- `cdnProvider`: CDN을 사용하도록 설정하면 `cdnProvider` 값을 전달할 수도 있습니다. `cdnProvider`는 사용할 공급자를 제어합니다. 현재, "StandardVerizon", "PremiumVerizon" 및 "StandardAkamai"의 세 가지 값이 지원됩니다. 값이 제공되지 않고 `cdnEnabled` true인 경우 "StandardVerizon"이 사용됩니다(기본값).
- `crossSiteAccessPolicies`: 다양한 클라이언트에 대한 교차 사이트 액세스 정책을 지정하는 데 사용됩니다. 자세한 내용은 [도메인 간 정책 파일 사양](https://www.adobe.com/devnet/articles/crossdomain_policy_file_spec.html) 및 [여러 도메인 간에 서비스를 사용하도록 지정](https://msdn.microsoft.com/library/cc197955\(v=vs.95\).aspx)을 참조하세요. 이 설정은 부드러운 스트리밍에만 적용됩니다.
- `customHostNames`: 사용자 지정 호스트 이름으로 전달되는 트래픽을 허용하도록 스트리밍 끝점을 구성하는 데 사용됩니다. 이 속성은 표준 및 프리미엄 스트리밍 끝점에 대 `cdnEnabled`한 유효 하 고 때 설정할 수 있습니다: false.

    도메인 이름의 소유권은 미디어 서비스에서 확인해야 합니다. 미디어 서비스는 사용 중 도메인에 `CName` 추가할 구성 요소로 Media Services 계정 ID가 포함된 레코드를 요구하여 도메인 이름 소유권을 확인합니다. 예를 들어 “sports.contoso.com”을 스트리밍 엔드포인트에 대한 사용자 지정 호스트 이름으로 사용하려면 Media Services 확인 호스트 이름 중 하나를 가리키도록 `<accountId>.contoso.com`에 대한 레코드를 구성해야 합니다. 확인 호스트 이름은 verifydns.\<mediaservices-dns-zone>으로 구성됩니다.

    다음은 다른 Azure 지역의 확인 레코드에 사용될 예상 DNS 영역입니다.
  
  - 북미, 유럽, 싱가포르, 홍콩 특별 행정구, 일본:

    - `media.azure.net`
    - `verifydns.media.azure.net`

  - 중국:

    - `mediaservices.chinacloudapi.cn`
    - `verifydns.mediaservices.chinacloudapi.cn`

    예를 들어 `CName` "945a4c4e-28ea-45cd-8ccb-a519f6b700ad.contoso.com"를 "verifydns.media.azure.net"로 매핑하는 레코드는 미디어 서비스 ID 945a4c4e-28ea-45ea-45ea-8ccb-a519f6b700ad가 contoso.com 도메인의 소유권을 가지고 있음을 증명하므로 contoso.com 아래의 모든 이름이 해당 계정의 스트리밍 끝점에 대한 사용자 지정 호스트 이름으로 사용될 수 있습니다. 미디어 서비스 ID 값을 찾으려면 [Azure Portal](https://portal.azure.com/)로 이동한 후 미디어 서비스 계정을 선택합니다. **계정 ID는** 페이지 오른쪽 상단에 나타납니다.

    `CName` 레코드를 제대로 확인하지 않고 사용자 지정 호스트 이름을 설정하려는 경우 DNS 응답이 실패한 다음 잠시 동안 캐시됩니다. 적절한 레코드가 준비되고 나면, 캐시된 응답의 유효성이 다시 확인될 때까지 잠시 시간이 소요될 수 있습니다. 사용자 지정 도메인의 DNS 공급자에 따라 레코드를 다시 유효성을 검사하는 데 몇 분에서 1시간 정도 걸립니다.

    `CName` `<accountId>.<parent domain>` `CName` `sports.contoso.com` `amstest-usea.streaming.media.azure.net`에 매핑되는 맵 외에도 사용자 지정 호스트 이름(예: 미디어 서비스 스트리밍 Endpoint)의 호스트 이름(예:)에 사용자 지정 호스트 이름을 매핑하는 다른 호스트를 만들어야 합니다. `verifydns.<mediaservices-dns-zone>`

    > [!NOTE]
    > 동일한 데이터 센터에 있는 스트리밍 끝점은 동일한 사용자 지정 호스트 이름을 공유할 수 없습니다.

    현재 미디어 서비스는 사용자 지정 도메인을 통해 TLS를 지원하지 않습니다.

- `maxCacheAge`- 미디어 조각 및 주문형 매니페스트의 스트리밍 끝점에서 설정한 기본 최대 연령 HTTP 캐시 제어 헤더를 재정의합니다. 이 값은 초 단위로 설정됩니다.
- `resourceState` -

    - 중지됨: 생성 후 스트리밍 끝점의 초기 상태
    - 시작: 실행 중인 상태로 전환
    - 실행 중: 클라이언트에 콘텐츠를 스트리밍할 수 있습니다.
    - 크기 조정: 배율 단위가 증가하거나 감소중입니다.
    - 중지: 중지된 상태로 전환중
    - 삭제: 삭제 중입니다.

- `scaleUnits`: 200Mbps 단위로 구매할 수 있는 전용 송신 용량을 제공합니다. **프리미엄** 유형으로 전환해야 할 경우 `scaleUnits`를 조정합니다.

## <a name="why-use-multiple-streaming-endpoints"></a>여러 스트리밍 엔드포인트를 사용하는 이유는 무엇입니까?

단일 스트리밍 엔드포인트는 라이브 및 주문형 비디오를 모두 스트리밍할 수 있으며 대부분의 고객은 하나의 스트리밍 엔드포인트만 사용합니다. 이 섹션에서는 여러 스트리밍 끝점을 사용해야 하는 이유에 대한 몇 가지 예를 제공합니다.

* 예약된 각 단위는 200Mbps의 대역폭을 허용합니다. 2,000Mbps(2Gbps) 이상의 대역폭이 필요한 경우 두 번째 스트리밍 끝점과 로드 밸런스를 사용하여 추가 대역폭을 제공할 수 있습니다.

    그러나 CDN은 스트리밍 콘텐츠에 대한 확장을 달성하는 가장 좋은 방법이지만 CDN이 2Gbps 이상을 당기는 너무 많은 콘텐츠를 제공하는 경우 스트리밍 끝점(원점)을 추가할 수 있습니다. 이 경우 두 스트리밍 끝점에서 균형 잡힌 콘텐츠 URL을 배포해야 합니다. 이 방법은 트래픽 관리자를 통해 각 원본에 요청을 임의로 보내는 것보다 더 나은 캐싱을 제공합니다. 
    
    > [!TIP]
    > 일반적으로 CDN이 2Gbps 이상을 당기면 뭔가 잘못 구성될 수 있습니다(예: 원원산지 차폐 없음).
    
* 다른 CDN 공급자의 부하 분산. 예를 들어 기본 스트리밍 끝점을 설정하여 Verizon CDN을 사용하고 Akamai를 사용하는 두 번째 스트리밍 엔드포인트를 만들 수 있습니다. 그런 다음 다중 CDN 균형을 달성하기 위해 둘 사이에 약간의 부하 분산을 추가합니다. 

    그러나 고객은 단일 원본을 사용하여 여러 CDN 공급자간에 부하 분산을 수행하는 경우가 많습니다.
* 혼합 콘텐츠 스트리밍: 주문형 라이브 및 비디오. 

    라이브 및 온디맨드 콘텐츠에 대한 액세스 패턴은 매우 다릅니다. 라이브 콘텐츠는 동일한 콘텐츠에 대한 수요를 한 번에 많이 얻는 경향이 있습니다. 비디오 주문형 콘텐츠(예: 긴 꼬리 아카이브 콘텐츠)는 동일한 콘텐츠에 대한 사용량이 낮습니다. 따라서 캐싱은 라이브 콘텐츠에서 매우 잘 작동하지만 긴 꼬리 콘텐츠에서는 잘 작동하지 않습니다.

    고객이 주로 라이브 콘텐츠를 시청하지만 가끔 온디맨드 콘텐츠만 시청하고 동일한 스트리밍 엔드포인트에서 제공되는 시나리오를 생각해 보십시오. 온디맨드 콘텐츠의 사용량이 적으면 라이브 콘텐츠에 더 잘 저장되는 캐시 공간이 차지합니다. 이 시나리오에서는 한 스트리밍 끝점에서 라이브 콘텐츠와 다른 스트리밍 끝점에서 긴 꼬리 콘텐츠를 제공 하는 것이 좋습니다. 이렇게 하면 라이브 이벤트 콘텐츠의 성능이 향상됩니다.
    
## <a name="scaling-streaming-with-cdn"></a>CDN을 사용하여 스트리밍 크기 조정

다음 문서를 참조하세요.

- [CDN 개요](../../cdn/cdn-overview.md)
- [CDN을 사용하여 스트리밍 크기 조정](scale-streaming-cdn.md)

## <a name="ask-questions-and--get-updates"></a>질문하고 업데이트 받기

[Azure Media Services 커뮤니티](media-services-community.md) 문서를 체크 아웃하여 다양한 방법으로 질문을 하고, 피드백을 제공하고, Media Services에 대한 업데이트를 가져올 수 있습니다.

## <a name="see-also"></a>참고 항목

[동적 패키징](dynamic-packaging-overview.md)

## <a name="next-steps"></a>다음 단계

[스트리밍 엔드포인트 관리](manage-streaming-endpoints-howto.md)
