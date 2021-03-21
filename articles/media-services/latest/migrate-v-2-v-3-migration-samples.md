---
title: Media Services v2에서 v3 마이그레이션 샘플 비교
description: Azure Media Services v 2와 v3 간의 코드 차이점을 비교 하는 데 도움이 되는 샘플 집합입니다.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: conceptual
ms.workload: media
ms.date: 1/14/2021
ms.author: inhenkel
ms.openlocfilehash: 640b9b40295ae9b9aea865f7b6159da6ff4a3251
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98898310"
---
# <a name="media-services-migration-code-sample-comparison"></a>Media Services 마이그레이션 코드 샘플 비교

![마이그레이션 가이드 로고](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

<hr color="#5ea0ef" size="10">

몇 가지 코드 샘플을 사용 하 여 Sdk 간에 작업을 수행 하는 방법을 비교할 수 있습니다.

## <a name="samples-for-comparison"></a>비교를 위한 샘플

다음 표는 일반적인 시나리오에 대 한 v2와 v3의 비교에 대 한 샘플 목록입니다.

|시나리오|v2 API|v3 API|
|---|---|---|
|자산 만들기 및 파일 업로드 |[v2 .NET 예제](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-aes/blob/master/DynamicEncryptionWithAES/DynamicEncryptionWithAES/Program.cs#L113)|[v3 .NET 예제](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#L169)|
|작업 제출|[v2 .NET 예제](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-aes/blob/master/DynamicEncryptionWithAES/DynamicEncryptionWithAES/Program.cs#L146)|[v3 .NET 예제](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#L298)<br/><br/>먼저 변환을 만든 후 작업을 제출하는 방법을 보여줍니다.|
|AES 암호화를 사용하여 자산 게시 |1. 만들기 `ContentKeyAuthorizationPolicyOption`<br/>2. 만들기 `ContentKeyAuthorizationPolicy`<br/>3. 만들기 `AssetDeliveryPolicy`<br/>4. `Asset` 콘텐츠 만들기 및 업로드 또는 제출 `Job` 및 사용 `OutputAsset`<br/>5. `AssetDeliveryPolicy` 와 연결 `Asset`<br/>6. 만들기 `ContentKey`<br/>7. 연결 `ContentKey` 대상 `Asset`<br/>8. 만들기 `AccessPolicy`<br/>9. 만들기 `Locator`<br/><br/>[v2 .NET 예제](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-aes/blob/master/DynamicEncryptionWithAES/DynamicEncryptionWithAES/Program.cs#L64)|1. 만들기 `ContentKeyPolicy`<br/>2. 만들기 `Asset`<br/>3. 콘텐츠를 업로드 하거나 다음 `Asset` 으로 사용 `JobOutput`<br/>4. 만들기 `StreamingLocator`<br/><br/>[v3 .NET 예제](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithAES/Program.cs#L105)|
|작업 세부 정보 가져오기 및 작업 관리 |[V2를 사용 하 여 작업 관리](../previous/media-services-dotnet-manage-entities.md#get-a-job-reference) |[V3로 작업 관리](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#L546)|

## <a name="next-steps"></a>다음 단계

[!INCLUDE [migration guide next steps](./includes/migration-guide-next-steps.md)]
