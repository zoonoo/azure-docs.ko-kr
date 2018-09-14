---
title: Azure Media Services의 스트리밍 엔드포인트 | Microsoft Docs
description: 이 문서에서는 스트리밍 엔드포인트란 무엇이고 Azure Media Services가 어떤 스트리밍 엔드포인트를 사용하는지 설명합니다.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 09/02/2018
ms.author: juliako
ms.openlocfilehash: c91a538acda44efe55777ec76c5804075a43c322
ms.sourcegitcommit: 31241b7ef35c37749b4261644adf1f5a029b2b8e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/04/2018
ms.locfileid: "43670082"
---
# <a name="streaming-endpoints"></a>스트리밍 엔드포인트

Microsoft AMS(Azure Media Services)에서 [스트리밍 엔드포인트](https://docs.microsoft.com/rest/api/media/streamingendpoints) 엔터티는 추가 배포를 위해 CDN(Content Delivery Network) 또는 클라이언트 플레이어 응용 프로그램에 직접 콘텐츠를 배달할 수 있는 스트리밍 서비스를 나타냅니다. 스트리밍 엔드포인트 서비스의 아웃 바운드 스트림은 라이브 스트림 또는 Media Services 계정에 주문형 비디오 자산이 될 수 있습니다. Media Services 계정을 만들 경우 **기본** 스트리밍 엔드포인트가 중지됨 상태로 만들어집니다. 기본 스트리밍 엔드포인트는 삭제할 수 없습니다. 계정에서 추가 스트리밍 엔드포인트를 만들 수 있습니다. 비디오 스트리밍을 시작하려면 스트리밍 엔드포인트를 시작해야 합니다. 

## <a name="streamingendpoint-types"></a>StreamingEndpoint 유형  

**StreamingEndpoint** 유형으로는 **표준** 및 **프리미엄** 두 가지가 있습니다. 이러한 유형은 스트리밍 엔드포인트에 할당하는 배율 단위(`scaleUnits`) 수로 정의됩니다. 

다음 표에 해당 유형이 설명되어 있습니다.  

|type|배율 단위|설명|
|--------|--------|--------|  
|**표준 스트리밍 엔드포인트**(권장)|0|**표준** 유형은 거의 모든 스트리밍 시나리오 및 대상 사용자 크기에 권장되는 옵션입니다. **표준** 유형은 아웃바운드 대역폭을 자동으로 확장합니다. <br/>매우 까다로운 요구를 갖는 고객을 위해, Media Services는 가장 큰 인터넷 대상 사용자를 위해 용량을 스케일 아웃하는 데 사용될 수 있는 **프리미엄** 스트리밍 엔드포인트를 제공합니다. 대규모의 대상 사용자 및 동시 독자가 예상될 경우 amsstreaming@microsoft.com에서 **프리미엄** 유형으로 전환해야 할지 여부에 대한 지침을 얻으세요. |
|**프리미엄 스트리밍 엔드포인트**|>0|**프리미엄** 스트리밍 엔드포인트는 고급 워크로드에 적합하며, 확장성 있는 전용 대역폭 용량을 제공합니다. `scaleUnits`를 조정하여 **프리미엄** 유형으로 이동합니다. `scaleUnits`는 200Mbps 단위로 구입할 수 있는 전용 송신 용량을 제공합니다. **프리미엄** 유형을 사용할 경우 설정된 각 단위가 응용 프로그램에 대한 추가 대역폭 용량을 제공합니다. |

## <a name="working-with-cdn"></a>CDN 사용

대부분의 경우 CDN이 사용되도록 설정되어 있을 것입니다. 그러나 최대 동시성이 500명보다 적은 수의 독자로 예상될 경우 CDN이 동시성에 따라 가장 잘 확장되므로 CDN을 사용하지 않도록 설정하는 것이 좋습니다.

### <a name="detailed-explanation-of-how-caching-works"></a>캐싱 작동 방식에 대한 자세한 설명

CDN 지원 스트리밍 엔드포인트에 필요한 대역폭 양이 다르므로 CDN을 추가할 때 구체적인 대역폭 값은 없습니다. 콘텐츠 형식, 인기 정도, 비트 전송률 및 프로토콜 유형에 따라 많은 영향을 받습니다. CDN은 요청되는 항목만 캐시합니다. 즉, 비디오 조각이 캐시되면, 인기 있는 콘텐츠가 CDN에서 직접 제공되는 것입니다. 일반적으로 많은 사람들이 정확히 동일한 것을 보게 하므로 라이브 콘텐츠는 캐시될 확률이 높습니다. 인기 있는 콘텐츠도 있고 그렇지 않은 콘텐츠도 있으므로 주문형 콘텐츠를 좀 더 까다로울 수 있습니다. 수백만 개의 비디오 자산이 있고 모두 인기가 없지만(1주일에 1명 또는 2명의 독자) 수천 명의 사람들이 모두 다른 비디오를 보는 경우 CDN이 훨씬 덜 효과적입니다. 이 캐시가 손실되면 스트리밍 엔드포인트에 대한 부하를 늘립니다.
 
또한 적응 스트리밍의 작동 방식도 고려해야 합니다. 각 개별 비디오 조각은 자체 엔터티로 캐시됩니다. 예를 들어 특정 비디오가 처음 시청될 때 사용자가 여기저기를 이동하면서 몇 초 동안만 시청하게 되고, CDN에서 시청한 내용과 연결된 비디오 조각만 CDN에 캐시됩니다. 적응 스트리밍을 사용하면 비디오의 비트 전송률이 일반적으로 5~ 7 사이의 차이를 보입니다. 한 사람이 한 비트 전송률로 시청 중이고, 다른 사람이 다른 비트 전송률로 시청 중이면 CDN에서 각각 따로 캐시됩니다. 두 사람이 동일한 비트 전송률로 시청 중이면 다른 프로토콜을 통해 스트리밍할 수 있습니다. 각 프로토콜(HLS, MPEG DASH, 부드러운 스트리밍)은 별도로 캐시됩니다. 따라서 각 비트 전송률 및 프로토콜은 개별적으로 캐시되고, 요청된 비디오 조각만 캐시됩니다.
 
## <a name="streamingendpoint-properties"></a>StreamingEndpoint 속성 

이 섹션에서는 StreamingEndpoint의 속성 중 일부에 대한 자세히 설명합니다. 새 스트리밍 엔드포인트를 만드는 방법의 예제와 모든 속성에 대한 설명을 보려면 [스트리밍 엔드포인트](https://docs.microsoft.com/rest/api/media/streamingendpoints/create)를 참조하세요. 

|자산|설명|  
|--------------|----------|
|`accessControl`|이 스트리밍 엔드포인트에 대한 보안 설정(Akamai 서명 헤더 인증 키 및 이 엔드포인트에 연결되도록 허용되는 IP 주소)을 구성하는 데 사용됩니다.<br />이 속성은 `cdnEnabled`"'가 false로 설정되면 설정할 수 있습니다.|  
|`cdnEnabled`|이 스트리밍 엔드포인트에 대한 Azure CDN 통합이 사용되도록 설정되어 있는지 여부를 나타냅니다(기본적으로 사용되지 않도록 설정됨).<br /><br /> `cdnEnabled`를 true로 설정하면 `customHostNames` 및 `accessControl` 구성이 사용되지 않도록 설정됩니다.<br /><br />모든 데이터 센터에서 Azure CDN 통합을 지원하는 것은 아닙니다. 데이터 센터에서 Azure CDN 통합을 사용할 수 있는지를 확인하려면 다음을 수행합니다.<br /><br /> - `cdnEnabled`를 true로 설정해봅니다.<br /><br /> - “현재 지역에서 CDN 기능을 사용할 수 없으므로 스트리밍 엔드포인트 CdnEnabled 속성을 true로 설정할 수 없습니다.” 메시지를 나타내며 `HTTP Error Code 412`(PreconditionFailed)에 대해 반환된 결과를 확인합니다.<br /><br /> 이 오류가 발생하면 데이터 센터가 통합을 지원하지 않는 것입니다. 다른 데이터 센터를 시도해보세요.|  
|`cdnProfile`|`cdnEnabled`가 true로 설정되면 `cdnProfile` 값도 전달할 수 있습니다. `cdnProfile`은 CDN 끝점이 생성되는 CDN 프로필의 이름입니다. 기존 cdnProfile을 제공하거나 새로 만들 수 있습니다. 값이 NULL이고 `cdnEnabled`가 true이면 기본값 “AzureMediaStreamingPlatformCdnProfile”이 사용됩니다. 제공된 `cdnProfile`이 이미 있으면 그 아래에 엔드포인트가 생성됩니다. 프로필이 없으면 새 프로필이 자동으로 생성됩니다.|
|`cdnProvider`|CDN이 사용되도록 설정되면 `cdnProvider` 값을 전달할 수도 있습니다. `cdnProvider`는 사용할 공급자를 제어합니다. 현재, "StandardVerizon", "PremiumVerizon" 및 "StandardAkamai"의 세 가지 값이 지원됩니다. 값이 제공되지 않고 `cdnEnabled`가 true인 경우 “StandardVerizon”(기본값)이 사용됩니다.|
|`crossSiteAccessPolicies`|다양한 클라이언트에 대한 사이트 간 액세스 정책을 지정하는 데 사용됩니다. 자세한 내용은 [도메인 간 정책 파일 사양](http://www.adobe.com/devnet/articles/crossdomain_policy_file_spec.html) 및 [여러 도메인 간에 서비스를 사용하도록 지정](http://msdn.microsoft.com/library/cc197955\(v=vs.95\).aspx)을 참조하세요.|  
|`customHostNames`|사용자 지정 호스트 이름으로 리디렉션되는 트래픽을 허용하도록 스트리밍 엔드포인트를 구성하는 데 사용됩니다. 이렇게 하면 GTM(Global Traffic Manager)을 통해 트래픽 관리 구성을 보다 쉽게 수행할 수 있고 브랜드 도메인 이름을 스트리밍 엔드포인트 이름으로 사용할 수 있습니다.<br /><br /> Azure Media Services에서 도메인 이름 소유권을 확인해야 합니다. Azure Media Services는 Azure Media Services 계정을 포함하는 `CName` 레코드를 사용 중인 도메인에 추가할 구성 요소로 요구하여 도메인 이름 소유권을 확인합니다. 예를 들어 “sports.contoso.com”을 스트리밍 엔드포인트에 대한 사용자 지정 호스트 이름으로 사용하려면 Media Services 확인 호스트 이름 중 하나를 가리키도록 `<accountId>.contoso.com`에 대한 레코드를 구성해야 합니다. 확인 호스트 이름은 verifydns.\<mediaservices-dns-zone>으로 구성됩니다. 다음 표에는 다른 Azure 지역에 대한 확인 레코드에서 사용해야 하는 DNS 영역을 보여 줍니다.<br /><br /> 북아메리카, 유럽, 싱가포르, 홍콩, 일본:<br /><br /> - mediaservices.windows.net<br /><br /> - verifydns.mediaservices.windows.net<br /><br /> 중국:<br /><br /> - mediaservices.chinacloudapi.cn<br /><br /> - verifydns.mediaservices.chinacloudapi.cn<br /><br /> 예를 들어 “945a4c4e-28ea-45cd-8ccb-a519f6b700ad.contoso.com”을 “verifydns.mediaservices.windows.net”에 매핑하는 `CName` 레코드는 Azure Media Services ID 945a4c4e-28ea-45cd-8ccb-a519f6b700ad가 contoso.com 도메인의 소유권을 가지므로 contoso.com 아래의 모든 이름을 해당 계정 아래의 스트리밍 엔드포인트에 대한 사용자 지정 호스트 이름으로 사용할 수 있음을 입증합니다.<br /><br /> 미디어 서비스 ID 값을 찾으려면 [Azure Portal](https://portal.azure.com/)로 이동한 후 미디어 서비스 계정을 선택합니다. 미디어 서비스 ID는 대시보드 페이지의 오른쪽에 나타납니다.<br /><br /> **경고**: `CName` 레코드를 적절히 확인하지 않고 사용자 지정 호스트 이름을 설정하려고 하면 DNS 응답이 실패하고 일정 시간 동안 캐시됩니다. 적절한 레코드가 준비되고 나면, 캐시된 응답의 유효성이 다시 확인될 때까지 잠시 시간이 소요될 수 있습니다. 사용자 지정 도메인에 대한 DNS 공급자에 따라, 레코드의 유효성이 다시 확인되기까지 몇 분에서 1시간 정도 걸릴 수 있습니다.<br /><br /> `<accountId>.<parent domain>`을 `verifydns.<mediaservices-dns-zone>`에 매핑하는 `CName` 외에도, 사용자 지정 호스트 이름(예: sports.contoso.com)을 Media Services StreamingEndpont 호스트 이름(예: `sports.contoso.com`)을 Media Services StreamingEndpont의 호스트 이름(예: `amstest.streaming.mediaservices.windows.net`)에 매핑하는 다른 `CName`을 만들어야 합니다.<br /><br /> **참고**: 동일한 데이터 센터에 있는 스트리밍 엔드포인트는 동일한 사용자 지정 호스트 이름을 공유할 수 없습니다.<br /> 이 속성은 표준 및 프리미엄 스트리밍 엔드포인트에 대해 유효하며 "cdnEnabled": false일 때 설정할 수 있습니다.<br/><br/> 현재 AMS는 사용자 지정 도메인을 사용하는 SSL을 지원하지 않습니다.  |  
|`maxCacheAge`|미디어 조각 및 주문형 매니페스트에서 스트리밍 엔드포인트가 설정한 기본 max-age HTTP 캐시 제어 헤더를 재정의합니다. 이 값은 초 단위로 설정됩니다.|
|`resourceState`|해당 속성의 값에는 다음이 포함됩니다.<br /><br /> - Stopped. 만든 후 스트리밍 엔드포인트의 초기 상태입니다.<br /><br /> - Starting. 스트리밍 엔드포인트가 실행 중 상태로 전환되고 있습니다.<br /><br /> - Running. 스트리밍 엔드포인트가 클라이언트로 콘텐츠를 스트리밍할 수 있습니다.<br /><br /> - Scaling. 배율 단위가 증가 또는 감소되고 있습니다.<br /><br /> - Stopping. 스트리밍 엔드포인트가 중지됨 중 상태로 전환되고 있습니다.<br/><br/> - Deleting. 스트리밍 엔드포인트를 삭제하는 중입니다.|
|`scaleUnits `|scaleUnits는 200Mbps 단위로 구입할 수 있는 전용 송신 용량을 제공합니다. **프리미엄** 유형으로 전환해야 할 경우 `scaleUnits`를 조정합니다. |

## <a name="next-steps"></a>다음 단계

[이 리포지토리](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs)의 샘플은 .NET을 사용하여 기본 스트리밍 엔드포인트를 시작하는 방법을 보여 줍니다.

