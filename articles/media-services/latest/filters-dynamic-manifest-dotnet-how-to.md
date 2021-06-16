---
title: Azure Media Services v3 .NET SDK로 필터 만들기
description: 이 항목에서는 클라이언트가 스트림의 특정 섹션을 스트리밍하는 데 사용할 수 있는 필터를 생성하는 방법을 설명합니다. Media Services v3 .NET SDK는 동적 매니페스트를 생성하여 이 선택적 스트리밍을 수행합니다.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: c81f6ed1517146efdd60c293d0c4840f6c61f7f5
ms.sourcegitcommit: eb20dcc97827ef255cb4ab2131a39b8cebe21258
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/03/2021
ms.locfileid: "111372592"
---
# <a name="create-filters-with-media-services-net-sdk"></a>Media Services .NET SDK로 필터 만들기

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

고객에게 콘텐츠를 제공(라이브 이벤트 또는 주문형 비디오를 스트리밍)하는 경우 클라이언트에게 기본 자산의 매니페스트 파일에 설명된 내용보다 더 많은 유연성이 필요할 수 있습니다. Azure Media Services를 사용하면 콘텐츠에 사용할 계정 필터 및 자산 필터를 정의할 수 있습니다.

이 기능과 이 기능이 사용되는 시나리오에 대한 자세한 설명은 [동적 매니페스트](filters-dynamic-manifest-concept.md) 및 [필터](filters-concept.md)를 참조하세요.

이 항목에서는 주문형 비디오 자산의 필터를 정의하고 REST API를 사용하여 [계정 필터](/dotnet/api/microsoft.azure.management.media.models.accountfilter) 및 [자산 필터](/dotnet/api/microsoft.azure.management.media.models.assetfilter)를 만드는 방법을 보여 줍니다. 

> [!NOTE]
> [presentationTimeRange](filters-concept.md#presentationtimerange)를 검토해야 합니다.

## <a name="prerequisites"></a>필수 구성 요소 

- [필터 및 동적 매니페스트](filters-dynamic-manifest-concept.md)를 검토합니다.
- [Media Services 계정 만들기](./account-create-how-to.md) 리소스 그룹 이름과 Media Services 계정 이름을 기억해 두어야 합니다. 
- [API에 액세스](./access-api-howto.md)하는 데 필요한 정보 가져오기
- [Azure Media Services를 사용하여 업로드, 인코딩 및 스트리밍](stream-files-tutorial-with-api.md)을 검토하여 [.NET SDK 사용을 시작](stream-files-tutorial-with-api.md#start-using-media-services-apis-with-the-net-sdk)하는 방법을 확인합니다.

## <a name="define-a-filter"></a>필터 정의  

.NET에서는 [FilterTrackSelection](/dotnet/api/microsoft.azure.management.media.models.filtertrackselection) 및 [FilterTrackPropertyCondition](/dotnet/api/microsoft.azure.management.media.models.filtertrackpropertycondition) 클래스를 사용하여 트랙 선택 영역을 구성합니다. 

다음 코드는 EC-3인 오디오 트랙 및 비트 전송률이 0-1000000 범위인 비디오 트랙을 모두 포함하는 필터를 정의합니다.

```csharp
var audioConditions = new List<FilterTrackPropertyCondition>()
{
    new FilterTrackPropertyCondition(FilterTrackPropertyType.Type, "Audio", FilterTrackPropertyCompareOperation.Equal),
    new FilterTrackPropertyCondition(FilterTrackPropertyType.FourCC, "EC-3", FilterTrackPropertyCompareOperation.Equal)
};

var videoConditions = new List<FilterTrackPropertyCondition>()
{
    new FilterTrackPropertyCondition(FilterTrackPropertyType.Type, "Video", FilterTrackPropertyCompareOperation.Equal),
    new FilterTrackPropertyCondition(FilterTrackPropertyType.Bitrate, "0-1000000", FilterTrackPropertyCompareOperation.Equal)
};

List<FilterTrackSelection> includedTracks = new List<FilterTrackSelection>()
{
    new FilterTrackSelection(audioConditions),
    new FilterTrackSelection(videoConditions)
};
```

## <a name="create-account-filters"></a>계정 필터 만들기

다음 코드는 .NET를 사용하여 [위에 정의된](#define-a-filter) 모든 트랙 선택 영역을 포함하는 계정 필터를 만드는 방법을 보여 줍니다. 

```csharp
AccountFilter accountFilterParams = new AccountFilter(tracks: includedTracks);
client.AccountFilters.CreateOrUpdate(config.ResourceGroup, config.AccountName, "accountFilterName1", accountFilter);
```

## <a name="create-asset-filters"></a>자산 필터 만들기

다음 코드는 .NET를 사용하여 [위에 정의된](#define-a-filter) 모든 트랙 선택 영역을 포함하는 자산 필터를 만드는 방법을 보여 줍니다. 

```csharp
AssetFilter assetFilterParams = new AssetFilter(tracks: includedTracks);
client.AssetFilters.CreateOrUpdate(config.ResourceGroup, config.AccountName, encodedOutputAsset.Name, "assetFilterName1", assetFilterParams);
```

## <a name="associate-filters-with-streaming-locator"></a>필터를 스트리밍 로케이터와 연결

스트리밍 로케이터에 적용되는 자산 또는 계정 필터 목록을 지정할 수 있습니다. [Dynamic Packager(스트리밍 엔드포인트)](encode-dynamic-packaging-concept.md)는 클라이언트가 URL에 지정하는 필터와 함께 이 필터 목록을 적용합니다. 이 조합은 스트리밍 로케이터에서 지정한 URL + 필터의 필터를 기반으로 [동적 매니페스트](filters-dynamic-manifest-concept.md)를 생성합니다. 필터를 적용하지만, URL에 필터 이름을 표시하지 않으려는 경우 이 기능을 사용하는 것이 좋습니다.

다음 C# 코드는 스트리밍 로케이터를 만들고 `StreamingLocator.Filters`를 지정하는 방법을 보여 줍니다. 이 속성은 필터 이름의 `IList<string>`을 사용하는 선택적 속성입니다.

```csharp
IList<string> filters = new List<string>();
filters.Add("filterName");

StreamingLocator locator = await client.StreamingLocators.CreateAsync(
    resourceGroup,
    accountName,
    locatorName,
    new StreamingLocator
    {
        AssetName = assetName,
        StreamingPolicyName = PredefinedStreamingPolicy.ClearStreamingOnly,
        Filters = filters
    });
```
      
## <a name="stream-using-filters"></a>필터를 사용하여 스트림

필터를 정의하고 나면 클라이언트가 스트리밍 URL에 필터를 사용할 수 있습니다. Apple HLS(HTTP 라이브 스트리밍), MPEG-DASH, 부드러운 스트리밍 등의 적응 비트 전송률 스트리밍 프로토콜에 필터를 적용할 수 있습니다.

다음 표에서는 필터가 있는 URL의 몇 가지 예제를 보여 줍니다.

|프로토콜|예제|
|---|---|
|HLS|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=m3u8-aapl,filter=myAccountFilter)`|
|MPEG DASH|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=mpd-time-csf,filter=myAssetFilter)`|
|부드러운 스트리밍|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(filter=myAssetFilter)`|

## <a name="next-steps"></a>다음 단계

[비디오 스트리밍](stream-files-tutorial-with-api.md) 
