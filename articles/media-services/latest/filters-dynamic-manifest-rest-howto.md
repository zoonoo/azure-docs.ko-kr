---
title: Azure Media Services v3 REST API로 필터 만들기
description: 이 항목에서는 클라이언트가 스트림의 특정 섹션을 스트리밍하는 데 사용할 수 있는 필터를 생성하는 방법을 설명합니다. Media Services v3 REST API는 이 선택적 스트리밍을 달성하기 위해 동적 매니페스트를 생성합니다.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: how-to
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: 7ec7213caa3d35fda7c637930e1cb950443a3f55
ms.sourcegitcommit: 3941df51ce4fca760797fa4e09216fcfb5d2d8f0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/23/2021
ms.locfileid: "122642231"
---
# <a name="creating-filters-with-media-services-rest-api"></a>Media Services REST API를 사용하여 필터 만들기

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

고객에게 콘텐츠를 제공(라이브 이벤트 또는 주문형 비디오를 스트리밍)하는 경우 클라이언트에게 기본 자산의 매니페스트 파일에 설명된 내용보다 더 많은 유연성이 필요할 수 있습니다. Azure Media Services를 사용하면 콘텐츠에 사용할 계정 필터 및 자산 필터를 정의할 수 있습니다. 

[!INCLUDE [warning-rest-api-retry-policy.md](./includes/warning-rest-api-retry-policy.md)]

이 기능과 이 기능을 사용하는 시나리오에 대한 자세한 설명은 [동적 매니페스트](filters-dynamic-manifest-concept.md) 및 [필터](filters-concept.md)를 참조하세요.

이 항목에서는 주문형 비디오 자산에 대한 필터를 정의하고 REST API를 사용하여 [계정 필터](/rest/api/media/accountfilters) 및 [자산 필터](/rest/api/media/assetfilters)를 만드는 방법을 보여 줍니다. 

> [!NOTE]
> [presentationTimeRange](filters-concept.md#presentationtimerange)를 검토해야 합니다.

## <a name="prerequisites"></a>필수 구성 요소 

이 항목에 설명된 단계를 완료하려면 다음을 수행해야 합니다.

- [필터 및 동적 매니페스트](filters-dynamic-manifest-concept.md)를 검토합니다.
- [Azure Media Services REST API 호출에 대해 Postman 구성](setup-postman-rest-how-to.md)

    [Azure AD 토큰 가져오기](setup-postman-rest-how-to.md#get-azure-ad-token) 항목의 마지막 단계를 수행해야 합니다. 

## <a name="define-a-filter"></a>필터 정의  

다음은 매니페스트에 추가되는 트랙 선택 조건을 정의하는 **요청 본문** 예제입니다. 이 필터는 EC-3인 오디오 트랙 및 비트 전송률이 0-1000000 범위인 비디오 트랙을 모두 포함합니다.

```json
{
    "properties": {
        "tracks": [
          {
                "trackSelections": [
                    {
                        "property": "Type",
                        "value": "Audio",
                        "operation": "Equal"
                    },
                    {
                        "property": "FourCC",
                        "value": "EC-3",
                        "operation": "Equal"
                    }
                ]
            },
            {
                "trackSelections": [
                    {
                        "property": "Type",
                        "value": "Video",
                        "operation": "Equal"
                    },
                    {
                        "property": "Bitrate",
                        "value": "0-1000000",
                        "operation": "Equal"
                    }
                ]
            }
        ]
     }
}
```

## <a name="create-account-filters"></a>계정 필터 만들기

다운로드한 Postman 컬렉션에서 **계정 필터**->**Create or update an Account Filter**(계정 필터 만들기 또는 업데이트)를 선택합니다.

**PUT** HTTP 요청 메서드는 다음과 유사합니다.

```
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Media/mediaServices/{accountName}/accountFilters/{filterName}?api-version=2018-07-01
```

**본문** 탭을 선택하고 [이전에 정의](#define-a-filter)한 json 코드를 붙여넣습니다.

**보내기** 를 선택합니다. 

필터를 만들었습니다.

자세한 내용은 [만들기 또는 업데이트](/rest/api/media/accountfilters/createorupdate)를 참조하세요. [필터에 대한 JSON 예제](/rest/api/media/accountfilters/createorupdate#create-an-account-filter)도 참조하세요.

## <a name="create-asset-filters"></a>자산 필터 만들기  

다운로드한 “Media Services v3” Postman 컬렉션에서 **자산**->**자산 필터 만들기 또는 업데이트** 를 선택합니다.

**PUT** HTTP 요청 메서드는 다음과 유사합니다.

```
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Media/mediaServices/{accountName}/assets/{assetName}/assetFilters/{filterName}?api-version=2018-07-01
```

**본문** 탭을 선택하고 [이전에 정의](#define-a-filter)한 json 코드를 붙여넣습니다.

**보내기** 를 선택합니다. 

자산 필터를 만들었습니다.

자산 필터 만들기 또는 업데이트 방법에 대한 자세한 내용은 [만들기 또는 업데이트](/rest/api/media/assetfilters/createorupdate)를 참조하세요. [필터에 대한 JSON 예제](/rest/api/media/assetfilters/createorupdate#create-an-asset-filter)도 참조하세요. 

## <a name="associate-filters-with-streaming-locator"></a>스트리밍 로케이터와 필터 연결

스트리밍 로케이터에 적용되는 자산 또는 계정 필터 목록을 지정할 수 있습니다. [Dynamic Packager(스트리밍 엔드포인트)](encode-dynamic-packaging-concept.md)는 클라이언트가 URL에 지정하는 필터와 함께 이 필터 목록을 적용합니다. 이 조합은 스트리밍 로케이터에서 지정한 URL + 필터의 필터를 기반으로 [동적 매니페스트](filters-dynamic-manifest-concept.md)를 생성합니다. 필터를 적용하되 URL에서 필터 이름을 표시하지 않으려면 이 기능을 사용하는 것이 좋습니다.

REST를 사용하여 스트리밍 로케이터에서 필터를 만들고 연결하려면 [스트리밍 로케이터 - 만들기](/rest/api/media/streaminglocators/create) API를 사용하고 [요청 본문](/rest/api/media/streaminglocators/create#request-body)에 `properties.filters`를 지정합니다.
                                
## <a name="stream-using-filters"></a>필터를 사용하여 스트리밍

필터를 정의하고 나면 클라이언트가 스트리밍 URL에 필터를 사용할 수 있습니다. Apple HLS(HTTP 라이브 스트리밍), MPEG-DASH, 부드러운 스트리밍 등의 적응 비트 전송률 스트리밍 프로토콜에 필터를 적용할 수 있습니다.

다음 표에서는 필터가 있는 URL의 몇 가지 예제를 보여 줍니다.

|프로토콜|예제|
|---|---|
|HLS|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=m3u8-aapl,filter=myAccountFilter)`|
|MPEG DASH|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=mpd-time-csf,filter=myAssetFilter)`|
|부드러운 스트리밍|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(filter=myAssetFilter)`|

## <a name="next-steps"></a>다음 단계

[비디오 스트리밍](stream-files-tutorial-with-rest.md) 
