---
title: 스트리밍 엔드포인트 관리
description: 이 문서에서는 Azure Media Services v3를 사용하여 스트리밍 엔드포인트를 관리하는 방법을 보여줍니다.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
writer: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: devx-track-azurecli, devx-track-csharp
ms.openlocfilehash: 6cc48de93affb8873bff1264dd012d2a7933e99f
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/01/2021
ms.locfileid: "106108186"
---
# <a name="manage-streaming-endpoints-with--media-services-v3"></a>Media Services v3를 통해 스트리밍 엔드포인트 관리

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Azure Media Services 계정이 만들어지면 **기본** [스트리밍 엔드포인트](streaming-endpoint-concept.md)가 **중지됨** 상태에 있는 계정에 추가됩니다. 콘텐츠 스트리밍을 시작하고 [동적 패키징](encode-dynamic-packaging-concept.md) 및 [동적 암호화](drm-content-protection-concept.md)를 활용하려면 콘텐츠를 스트리밍하려는 스트리밍 엔드포인트가 **실행** 상태에 있어야 합니다.

이 문서에서는 다양한 기술을 사용하여 스트리밍 엔드포인트에서 [시작](/rest/api/media/streamingendpoints/start) 명령을 실행하는 방법을 보여줍니다. 
 
> [!NOTE]
> 스트리밍 엔드포인트가 실행 중인 상태일 때만 요금이 청구됩니다.
    
## <a name="prerequisites"></a>필수 구성 요소

검토: 

* [Media Services 개념](concepts-overview.md)
* [스트리밍 엔드포인트 개념](streaming-endpoint-concept.md)
* [동적 패키징](encode-dynamic-packaging-concept.md)

## <a name="use-rest"></a>REST 사용

```rest
POST https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaservices/slitestmedia10/streamingEndpoints/myStreamingEndpoint1/start?api-version=2018-07-01
```

자세한 내용은 다음을 참조하세요. 

* [StreamingEndpoint 시작](/rest/api/media/streamingendpoints/start) 참조 설명서.
* 스트리밍 엔드포인트를 시작하는 것은 비동기 작업입니다. 

    장기 실행 작업을 모니터링하는 방법에 관한 자세한 내용은 [장기 실행 작업](media-services-apis-overview.md)을 참조하세요.
* 이 [Postman 컬렉션](https://github.com/Azure-Samples/media-services-v3-rest-postman/blob/master/Postman/Media%20Services%20v3.postman_collection.json)에는 스트리밍 엔드포인트를 시작하는 방법을 비롯하여 여러 REST 작업의 예가 포함되어 있습니다.

## <a name="use-the-azure-portal"></a>Azure Portal 사용 
 
1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
1. Azure Media Services 계정으로 이동합니다.
1. 왼쪽 창에서 **스트리밍 엔드포인트** 를 선택합니다.
1. 시작하려는 스트리밍 엔드포인트를 선택한 다음, **시작** 을 선택합니다.

## <a name="use-the-azure-cli"></a>Azure CLI 사용

```cli
az ams streaming-endpoint start [--account-name]
                                [--ids]
                                [--name]
                                [--no-wait]
                                [--resource-group]
                                [--subscription]
```

자세한 내용은 [az ams streaming-endpoint start](/cli/azure/ams/streaming-endpoint#az-ams-streaming-endpoint-start)를 참조하세요.

## <a name="use-sdks"></a>SDK 사용

### <a name="java"></a>Java
    
```java
if (streamingEndpoint != null) {
// Start The Streaming Endpoint if it is not running.
if (streamingEndpoint.resourceState() != StreamingEndpointResourceState.RUNNING) {
    manager.streamingEndpoints().startAsync(config.getResourceGroup(), config.getAccountName(), STREAMING_ENDPOINT_NAME).await();
}
```

전체 [Java 코드 샘플](https://github.com/Azure-Samples/media-services-v3-java/blob/master/DynamicPackagingVODContent/StreamHLSAndDASH/src/main/java/sample/StreamHLSAndDASH.java#L128)을 참조하세요.

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

전체 [.NET 코드 샘플](https://github.com/Azure-Samples/media-services-v3-dotnet/blob/main/Streaming/StreamHLSAndDASH/Program.cs#L112)을 참조하세요.

---

## <a name="next-steps"></a>다음 단계

* [Media Services v3 OpenAPI 사양(Swagger)](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01)
* [스트리밍 엔드포인트 작업](/rest/api/media/streamingendpoints)
