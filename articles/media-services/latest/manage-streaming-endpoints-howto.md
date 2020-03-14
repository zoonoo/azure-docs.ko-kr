---
title: Azure Media Services v3을 사용 하 여 스트리밍 끝점 관리
description: 이 문서에서는 Azure Media Services v3을 사용 하 여 스트리밍 끝점을 관리 하는 방법을 보여 줍니다.
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
ms.openlocfilehash: 5dd3cc1efd25f7ec09f897c67bebebedb84d9570
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/14/2020
ms.locfileid: "79370548"
---
# <a name="manage-streaming-endpoints-with--media-services-v3"></a>Media Services v3을 사용 하 여 스트리밍 끝점 관리

Azure Media Services 계정이 만들어지면 **기본** 스트리밍 엔드포인트가 **중지됨** 상태에 있는 계정에 추가됩니다. 콘텐츠 스트리밍을 시작 하 고 [동적 패키징](dynamic-packaging-overview.md) 및 [동적 암호화](content-protection-overview.md)를 활용 하려면 콘텐츠를 스트리밍 하려는 스트리밍 끝점이 **실행** 상태에 있어야 합니다.

이 문서에서는 스트리밍 끝점을 시작 하는 방법을 보여 줍니다.
 
> [!NOTE]
> 스트리밍 엔드포인트가 실행 중인 상태일 때만 요금이 청구됩니다.
    
## <a name="prerequisites"></a>사전 요구 사항

검토용 

* [Media Services 개념](concepts-overview.md)
* [스트리밍 끝점 개념](streaming-endpoint-concept.md)
* [동적 패키징](dynamic-packaging-overview.md)

## <a name="use-the-azure-portal"></a>Azure Portal 사용

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
1. Azure Media Services 계정으로 이동 합니다.
1. 왼쪽에서 **스트리밍 끝점**을 선택 합니다.
1. 시작 하려는 스트리밍 끝점을 선택 하 고 **시작**을 클릭 합니다.

## <a name="use-the-java-sdk"></a>Java SDK 사용

```java
if (streamingEndpoint != null) {
// Start The Streaming Endpoint if it is not running.
if (streamingEndpoint.resourceState() != StreamingEndpointResourceState.RUNNING) {
    manager.streamingEndpoints().startAsync(config.getResourceGroup(), config.getAccountName(), STREAMING_ENDPOINT_NAME).await();
}
```

전체 [Java 코드 샘플](https://github.com/Azure-Samples/media-services-v3-java/blob/master/DynamicPackagingVODContent/StreamHLSAndDASH/src/main/java/sample/StreamHLSAndDASH.java#L128)을 참조 하세요.

## <a name="use-the-net-sdk"></a>.NET SDK 사용

```csharp
StreamingEndpoint streamingEndpoint = await client.StreamingEndpoints.GetAsync(config.ResourceGroup, config.AccountName, DefaultStreamingEndpointName);

if (streamingEndpoint != null)
{
    if (streamingEndpoint.ResourceState != StreamingEndpointResourceState.Running)
    {
        await client.StreamingEndpoints.StartAsync(config.ResourceGroup, config.AccountName, DefaultStreamingEndpointName);
    }
```

전체 [.net 코드 샘플](https://github.com/Azure-Samples/media-services-v3-dotnet/blob/master/DynamicPackagingVODContent/StreamHLSAndDASH/Program.cs#L112)을 참조 하세요.

## <a name="use-cli"></a>CLI 사용

```cli
az ams streaming-endpoint start [--account-name]
                                [--ids]
                                [--name]
                                [--no-wait]
                                [--resource-group]
                                [--subscription]
```

자세한 내용은 [az ams streaming-endpoint start](https://docs.microsoft.com/cli/azure/ams/streaming-endpoint?view=azure-cli-latest#az-ams-streaming-endpoint-start)를 참조 하세요.

## <a name="use-rest"></a>REST 사용

```rest
POST https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaservices/slitestmedia10/streamingEndpoints/myStreamingEndpoint1/start?api-version=2018-07-01
```

자세한 내용은 다음을 참조하세요. 

* [StreamingEndpoint 참조 시작](https://docs.microsoft.com/rest/api/media/streamingendpoints/start) 설명서입니다.
* 스트리밍 끝점을 시작 하는 작업은 비동기 작업입니다. 

    장기 실행 작업을 모니터링 하는 방법에 대 한 자세한 내용은 [장기 실행 작업](media-services-apis-overview.md) 을 참조 하세요.
* 이 [Postman 컬렉션](https://github.com/Azure-Samples/media-services-v3-rest-postman/blob/master/Postman/Media%20Services%20v3.postman_collection.json) 에는 스트리밍 끝점을 시작 하는 방법을 비롯 하 여 여러 REST 작업의 예가 포함 되어 있습니다.

## <a name="next-steps"></a>다음 단계

* [Media Services v3 OpenAPI 사양 (Swagger)](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01)
* [스트리밍 끝점 작업](https://docs.microsoft.com/rest/api/media/streamingendpoints)
