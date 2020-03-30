---
title: Azure 미디어 서비스 v3로 스트리밍 끝점 관리
description: 이 문서에서는 Azure Media Services v3를 사용하여 스트리밍 끝점을 관리하는 방법을 보여 줍니다.
services: media-services
documentationcenter: ''
author: Juliako
writer: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/11/2020
ms.author: juliako
ms.openlocfilehash: 75ba2ad87eabd7ff6b0625ad95ab24a8ae58dd0f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79461047"
---
# <a name="manage-streaming-endpoints-with--media-services-v3"></a>미디어 서비스 v3로 스트리밍 엔드포인트 관리

미디어 서비스 계정이 생성되면 **기본** [스트리밍 엔드포인트가](streaming-endpoint-concept.md) **중지된** 상태의 계정에 추가됩니다. 콘텐츠 스트리밍을 시작하고 [동적 패키징](dynamic-packaging-overview.md) 및 [동적 암호화를](content-protection-overview.md)활용하려면 콘텐츠를 스트리밍하려는 스트리밍 끝점이 **실행 중** 상태여야 합니다.

이 문서에서는 다른 기술을 사용하여 스트리밍 끝점에서 [시작](https://docs.microsoft.com/rest/api/media/streamingendpoints/start) 명령을 실행하는 방법을 보여 줍니다. 
 
> [!NOTE]
> 스트리밍 엔드포인트가 실행 중인 상태일 때만 요금이 청구됩니다.
    
## <a name="prerequisites"></a>사전 요구 사항

검토: 

* [Media Services 개념](concepts-overview.md)
* [스트리밍 엔드포인트 개념](streaming-endpoint-concept.md)
* [동적 패키징](dynamic-packaging-overview.md)

## <a name="use-rest"></a>REST 사용

```rest
POST https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaservices/slitestmedia10/streamingEndpoints/myStreamingEndpoint1/start?api-version=2018-07-01
```

자세한 내용은 다음을 참조하세요. 

* [스트리밍Endpoint](https://docs.microsoft.com/rest/api/media/streamingendpoints/start) 참조 설명서를 시작합니다.
* 스트리밍 끝점을 시작하는 것은 비동기 작업입니다. 

    장기 실행 작업을 모니터링하는 방법에 대한 자세한 내용은 [장기 실행 작업을](media-services-apis-overview.md)참조하십시오.
* 이 [Postman 컬렉션에는](https://github.com/Azure-Samples/media-services-v3-rest-postman/blob/master/Postman/Media%20Services%20v3.postman_collection.json) 스트리밍 끝점을 시작하는 방법을 비롯하여 여러 REST 작업의 예제가 포함되어 있습니다.

## <a name="use-the-azure-portal"></a>Azure Portal 사용 
 
1. [Azure 포털에](https://portal.azure.com/)로그인합니다.
1. Azure 미디어 서비스 계정으로 이동합니다.
1. 왼쪽 창에서 스트리밍 **끝점을**선택합니다.
1. 시작할 스트리밍 끝점을 선택한 다음 **시작을**선택합니다.

## <a name="use-the-azure-cli"></a>Azure CLI 사용

```cli
az ams streaming-endpoint start [--account-name]
                                [--ids]
                                [--name]
                                [--no-wait]
                                [--resource-group]
                                [--subscription]
```

자세한 내용은 [az ams 스트리밍-엔드포인트 시작을](https://docs.microsoft.com/cli/azure/ams/streaming-endpoint?view=azure-cli-latest#az-ams-streaming-endpoint-start)참조하십시오.

## <a name="use-sdks"></a>SDK 사용

### <a name="java"></a>Java
    
```java
if (streamingEndpoint != null) {
// Start The Streaming Endpoint if it is not running.
if (streamingEndpoint.resourceState() != StreamingEndpointResourceState.RUNNING) {
    manager.streamingEndpoints().startAsync(config.getResourceGroup(), config.getAccountName(), STREAMING_ENDPOINT_NAME).await();
}
```

전체 [Java 코드 샘플을](https://github.com/Azure-Samples/media-services-v3-java/blob/master/DynamicPackagingVODContent/StreamHLSAndDASH/src/main/java/sample/StreamHLSAndDASH.java#L128)참조하십시오.

### <a name="net"></a>.NET

```csharp
StreamingEndpoint streamingEndpoint = await client.StreamingEndpoints.GetAsync(config.ResourceGroup, config.AccountName, DefaultStreamingEndpointName);

if (streamingEndpoint != null)
{
    if (streamingEndpoint.ResourceState != StreamingEndpointResourceState.Running)
    {
        await client.StreamingEndpoints.StartAsync(config.ResourceGroup, config.AccountName, DefaultStreamingEndpointName);
    }
```

전체 [.NET 코드 샘플을](https://github.com/Azure-Samples/media-services-v3-dotnet/blob/master/DynamicPackagingVODContent/StreamHLSAndDASH/Program.cs#L112)참조하십시오.

---

## <a name="next-steps"></a>다음 단계

* [미디어 서비스 v3 오픈API 사양 (스웨거)](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01)
* [스트리밍 엔드포인트 작업](https://docs.microsoft.com/rest/api/media/streamingendpoints)
