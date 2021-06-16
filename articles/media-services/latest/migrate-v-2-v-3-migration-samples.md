---
title: Media Services v2에서 v3 마이그레이션 샘플 비교
description: Azure Media Services v2와 v3 간의 코드 차이점을 비교하는 데 도움이 되는 샘플 세트입니다.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: conceptual
ms.workload: media
ms.date: 05/25/2021
ms.author: inhenkel
ms.openlocfilehash: cdfad63bbf70d40ddb78ac5708ce684d12f57698
ms.sourcegitcommit: 7f59e3b79a12395d37d569c250285a15df7a1077
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/02/2021
ms.locfileid: "110797582"
---
# <a name="media-services-migration-code-sample-comparison"></a>Media Services 마이그레이션 코드 샘플 비교

![마이그레이션 가이드 로고](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

<hr color="#5ea0ef" size="10">

## <a name="compare-the-sdks"></a>SDK 비교

몇 가지 코드 샘플을 사용하여 SDK 간에 작업을 수행하는 방법을 비교할 수 있습니다.

## <a name="samples-for-comparison"></a>비교를 위한 샘플

다음 표는 일반적인 시나리오에 대한 v2와 v3의 비교 샘플 목록입니다.

|시나리오|v2 API|v3 API|
|---|---|---|
|자산 만들기 및 파일 업로드 |[v2 .NET 예제](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-aes/blob/master/DynamicEncryptionWithAES/DynamicEncryptionWithAES/Program.cs#L113)|[v3 .NET 예제](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/main/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#L169)|
|작업 제출|[v2 .NET 예제](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-aes/blob/master/DynamicEncryptionWithAES/DynamicEncryptionWithAES/Program.cs#L146)|[v3 .NET 예제](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/main/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#L298)<br/><br/>먼저 변환을 만든 후 작업을 제출하는 방법을 보여줍니다.|
|AES 암호화를 사용하여 자산 게시 |1. `ContentKeyAuthorizationPolicyOption` 만들기<br/>2. `ContentKeyAuthorizationPolicy` 만들기<br/>3. `AssetDeliveryPolicy` 만들기<br/>4. `Asset` 만들기 및 콘텐츠 업로드 또는 `Job` 제출 및 `OutputAsset` 사용<br/>5. `AssetDeliveryPolicy`를 `Asset`과 연결<br/>6. `ContentKey` 만들기<br/>7. `ContentKey`를 `Asset`에 연결<br/>8. `AccessPolicy` 만들기<br/>9. `Locator` 만들기<br/><br/>[v2 .NET 예제](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-aes/blob/master/DynamicEncryptionWithAES/DynamicEncryptionWithAES/Program.cs#L64)|1. `ContentKeyPolicy` 만들기<br/>2. `Asset` 만들기<br/>3. 콘텐츠를 업로드하거나 `Asset`을 `JobOutput`으로 사용<br/>4. `StreamingLocator` 만들기<br/><br/>[v3 .NET 예제](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/main/AMSV3Tutorials/EncryptWithAES/Program.cs#L105)|
|작업 세부 정보 가져오기 및 작업 관리 |[V2를 사용하여 작업 관리](../previous/media-services-dotnet-manage-entities.md#get-a-job-reference) |[V3로 작업 관리](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/main/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#L546)|
