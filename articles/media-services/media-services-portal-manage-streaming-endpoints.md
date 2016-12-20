---
title: "Azure Portal을 통해 스트리밍 끝점 관리 | Microsoft 문서"
description: "이 항목에서는 Azure 포털을 사용하여 스트리밍 끝점을 관리하는 방법을 설명합니다."
services: media-services
documentationcenter: 
author: Juliako
writer: juliako
manager: erikre
editor: 
ms.assetid: 097ab5e5-24e1-4e8e-b112-be74172c2701
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2016
ms.author: juliako
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: e0351e286ae4f36c6c6fba25e5eaf4c135b21549


---
# <a name="manage-streaming-endpoints-with-the-azure-portal"></a>Azure 포털을 통해 스트리밍 끝점 관리
## <a name="overview"></a>개요
> [!NOTE]
> 이 자습서를 완료하려면 Azure 계정이 필요합니다. 자세한 내용은 [Azure 무료 체험](https://azure.microsoft.com/pricing/free-trial/)을 참조하세요. 
> 
> 

Microsoft Azure 미디어 서비스에서 **스트리밍 끝점** 은 추가 배포를 위해 CDN(콘텐츠 배달 네트워크) 또는 클라이언트 플레이어 응용 프로그램에 직접 콘텐츠를 배달할 수 있는 스트리밍 서비스를 나타냅니다. 미디어 서비스는 매끄러운 Azure CDN 통합도 제공합니다. StreamingEndpoint 서비스의 아웃바운드 스트림은 라이브 스트림이거나 미디어 서비스 계정의 주문형 비디오 자산일 수 있습니다.

또한 스트리밍 단위를 조정하여 증가하는 대역폭 요구를 처리하기 위해 스트리밍 끝점 서비스의 용량을 제어할 수 있습니다. 프로덕션 환경에서 응용 프로그램에 대한 하나 이상의 확장 단위를 할당하는 것이 좋습니다. 스트리밍 단위는 200Mbps 단위로 구입할 수 있는 전용 송신 용량 및 [동적 패키징](media-services-dynamic-packaging-overview.md), CDN 통합, 고급 구성을 포함하는 추가 기능을 모두 제공합니다.

> [!NOTE]
> 스트리밍 끝점이 실행 중인 상태일 때만 요금이 청구됩니다.
> 
> 

이 항목에서는 스트리밍 끝점이 제공하는 기본 기능을 간략하게 설명합니다. 또한 Azure 포털을 사용하여 스트리밍 끝점을 관리하는 방법도 설명합니다. 스트리밍 끝점의 크기를 조정하는 방법에 대한 자세한 내용은 [이 항목](media-services-portal-scale-streaming-endpoints.md) 을 참조하세요.

## <a name="start-managing-streaming-endpoints"></a>스트리밍 끝점 관리 시작
계정의 스트리밍 끝점 관리를 시작하려면 다음을 수행합니다.

1. [Azure Portal](https://portal.azure.com/)에서 Azure Media Services 계정을 선택합니다.
2. **설정** 창에서 **스트리밍 끝점**을 선택합니다.
   
    ![스트리밍 끝점](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints1.png)

## <a name="adddelete-a-streaming-endpoint"></a>스트리밍 끝점 추가/삭제
Azure 포털을 사용하여 스트리밍 끝점을 추가/삭제하려면 다음을 수행합니다.

1. 스트리밍 끝점을 추가하려면 페이지 위쪽의 **+ 끝점** 을 클릭합니다. 
2. 스트리밍 끝점을 삭제하려면 **삭제** 단추를 누릅니다. 
   
    기본 스트리밍 끝점은 삭제할 수 없습니다.
3. 스트리밍 끝점을 시작하려면 **시작** 단추를 클릭합니다.
   
    ![스트리밍 끝점](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints2.png)

기본적으로 최대 2개의 스트리밍 끝점을 가질 수 있습니다. 더 많이 요청해야 하는 경우 [할당량 및 제한 사항](media-services-quotas-and-limitations.md)을 참조하세요.

## <a name="a-idconfigurestreamingendpointsaconfiguring-the-streaming-endpoint"></a><a id="configure_streaming_endpoints"></a>스트리밍 끝점 구성
스트림이 끝점을 사용하면 최소 1개의 크기 조정 단위가 있을 때 다음 속성을 구성할 수 있습니다. 

* 액세스 제어
* 캐시 제어
* 교차 사이트 액세스 정책

이러한 속성에 대한 자세한 정보는 [StreamingEndpoint](https://msdn.microsoft.com/library/azure/dn783468.aspx)를 참조하세요.

다음을 수행하여 스트리밍 끝점을 구성할 수 있습니다.

1. 구성하려는 스트리밍 끝점을 선택합니다.
2. **설정**을 클릭합니다.

필드에 대한 간략한 설명은 다음과 같습니다.

![스트리밍 끝점](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints4.png)

1. 최대 캐시 정책: 이 스트리밍 끝점을 통해 제공되는 자산의 캐시 수명 주기를 구성하는 데 사용됩니다. 값을 설정하지 않으면 기본값이 사용됩니다. Azure Storage에서 기본값을 직접 정의할 수도 있습니다. 스트리밍 끝점에 대해 Azure CDN을 사용하도록 설정한 경우에는 캐시 정책 값을 600초보다 작게 설정하면 안 됩니다.  
2. 허용된 IP 주소: 게시된 스트리밍 끝점에 연결할 수 있는 IP 주소를 지정하는 데 사용됩니다. IP 주소를 지정하지 않은 경우 모든 IP 주소에서 연결할 수 있습니다. IP 주소는 단일 IP 주소(예: '10.0.0.1'), IP 주소 및 CIDR 서브넷 마스크를 사용하는 IP 범위(예: '10.0.0.1/22') 또는 IP 주소와 점으로 구분된 십진수 서브넷 마스크를 사용하는 IP 범위(예: '10.0.0.1(255.255.255.0)')로 지정할 수 있습니다.
3. Akamai 서명 헤더 인증에 대한 구성: Akamai 서버의 서명 헤더 인증 요청을 구성하는 방법을 지정하는 데 사용됩니다. 만료 시간은 UTC 단위입니다.

## <a name="a-idenablecdnaenable-azure-cdn-integration"></a><a id="enable_cdn"></a>Azure CDN 통합 사용
스트리밍 끝점에 Azure CDN 통합을 사용하도록 지정할 수 있습니다(기본 설정은 사용 안 함).

Azure CDN 통합을 true로 설정하는 방법:

* 스트리밍 끝점에는 하나 이상의 스트리밍 단위가 있어야 합니다. 나중에 크기 조정 단위를 0으로 설정하려는 경우 먼저 CDN 통합을 사용 안 함으로 설정해야 합니다. 기본적으로 새 스트리밍 끝점을 만들 때 한 스트리밍 단위가 자동으로 설정됩니다.
* 스트리밍 끝점은 중지된 상태에 있어야 합니다. CDN을 사용함으로 설정하면 스트림이 끝점을 시작할 수 있습니다. 

Azure CDN 통합을 사용함으로 설정하는 데에는 최대 90분 가량 소요될 수 있습니다.  모든 CDN POP에서 변경 내용이 활성화되는 데는 최대 두 시간이 걸립니다.

CDN 통합은 모든 Azure 데이터 센터(미국 서부, 미국 동부, 북유럽, 서유럽, 일본 서부, 일본 동부, 동남 아시아 및 동아시아)에서 사용하도록 설정됩니다.

CDN 통합을 사용하도록 설정하면 **액세스 제어** 구성은 사용할 수 없게 됩니다.

![스트리밍 끝점](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints5.png)

> [!IMPORTANT]
> Azure Media Services와 Azure CDN의 통합은 **Verizon의 Azure CDN**에서 구현됩니다.  Azure Media Services에 **Akamai의 Azure CDN** 을 사용하려면 [끝점을 수동으로 구성](../cdn/cdn-create-new-endpoint.md)해야 합니다.  Azure CDN 기능에 대한 자세한 내용은 [CDN 개요](../cdn/cdn-overview.md)를 참조하세요.
> 
> 

### <a name="additional-considerations"></a>추가 고려 사항
* 스트리밍 끝점에 CDN이 사용되면 클라이언트에서는 원점으로부터 직접 콘텐츠를 요청할 수 없습니다. CDN을 사용하거나 사용하지 않고 콘텐츠를 테스트하는 기능이 필요하면 CDN이 사용하도록 설정되지 않은 또 다른 스트리밍 끝점을 만들 수 있습니다.
* 스트리밍 끝점 호스트 이름은 CDN을 사용하도록 설정한 후에도 동일하게 유지됩니다. CDN을 사용하도록 설정한 후 미디어 서비스 워크플로에 변경 내용을 적용할 필요가 없습니다. 예를 들어 스트리밍 끝점 호스트 이름이 strasbourg.streaming.mediaservices.windows.net이면 CDN을 사용하도록 설정한 후에 똑같은 호스트 이름이 사용됩니다.
* 새 스트리밍 끝점의 경우 새 끝점을 만들어서 CDN을 사용하도록 설정할 수 있습니다. 기본 스트리밍 끝점의 경우 먼저 끝점을 중지하고 CDN을 사용하도록 설정해야 합니다.

자세한 내용은 [Azure CDN(콘텐츠 배달 네트워크)과 Azure 미디어 서비스 통합 발표](http://azure.microsoft.com/blog/2015/03/17/announcing-azure-media-services-integration-with-azure-cdn-content-delivery-network/)(영문)를 참조하세요.

## <a name="next-steps"></a>다음 단계
미디어 서비스 학습 경로를 검토합니다.

[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>피드백 제공
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]




<!--HONumber=Nov16_HO3-->


