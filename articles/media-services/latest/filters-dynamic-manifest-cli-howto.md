---
title: CLI를 사용하여 Azure Media Services로 필터 만들기 | Microsoft Docs
description: 이 토픽에서는 CLI를 사용하여 Media Services로 필터를 만드는 방법을 보여줍니다.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/03/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: fd581d1dfea850e925909df59f2d4fdd421053fb
ms.sourcegitcommit: 600d5b140dae979f029c43c033757652cddc2029
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66494362"
---
# <a name="creating-filters-with-cli"></a>CLI를 사용하여 필터 만들기 

고객에게 콘텐츠를 제공(라이브 이벤트 또는 주문형 비디오를 스트리밍)하는 경우 클라이언트에게는 기본 자산의 매니페스트 파일에 설명된 내용보다 더 많은 유연성이 필요할 수 있습니다. Azure Media Services를 사용하면 콘텐츠에 사용할 계정 필터 및 자산 필터를 정의할 수 있습니다. 

이 기능 및 사용 된 시나리오의 자세한 설명을 참조 하세요 [동적 매니페스트](filters-dynamic-manifest-overview.md) 하 고 [필터](filters-concept.md)합니다.

이 항목에서는 주문형 비디오 자산에 대한 필터를 구성하는 방법 및 Media Services v3용 CLI를 사용하여 [계정 필터](https://docs.microsoft.com/cli/azure/ams/account-filter?view=azure-cli-latest) 및 [자산 필터](https://docs.microsoft.com/cli/azure/ams/asset-filter?view=azure-cli-latest)를 만드는 방법을 보여 줍니다. 

> [!NOTE]
> 검토 해야 [presentationTimeRange](filters-concept.md#presentationtimerange)합니다.

## <a name="prerequisites"></a>필수 조건 

- [Media Services 계정 만들기](create-account-cli-how-to.md) 리소스 그룹 이름과 Media Services 계정 이름을 기억해 두어야 합니다. 

[!INCLUDE [media-services-cli-instructions](../../../includes/media-services-cli-instructions.md)]

## <a name="define-a-filter"></a>필터 정의 

다음 예제에서는 최종 매니페스트에 추가되는 트랙 선택 조건을 정의합니다. 이 필터는 EC-3인 오디오 트랙 및 비트 전송률이 0-1000000 범위인 비디오 트랙을 모두 포함합니다.

> [!TIP]
> 정의 하려는 경우 **필터** REST, 알 수 있듯이 "속성" 래퍼 JSON 개체를 포함 해야 합니다.  

```json
[
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
                "operation": "NotEqual"
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
```

## <a name="create-account-filters"></a>계정 필터 만들기

다음 [az ams account-filter](https://docs.microsoft.com/cli/azure/ams/account-filter?view=azure-cli-latest) 명령은 [앞에서 정의한](#define-a-filter) 필터 트랙 선택을 사용하여 계정 필터를 만듭니다. 

이 명령을 사용하여 트랙 선택 항목을 나타내는 JSON을 포함하는 선택적 `--tracks` 매개 변수를 전달할 수 있습니다.  @{file}을 사용하여 파일에서 JSON을 로드합니다. Azure CLI를 로컬로 사용하는 경우 다음과 같이 전체 파일 경로를 지정합니다.

```azurecli
az ams account-filter create -a amsAccount -g resourceGroup -n filterName --tracks @tracks.json
```

[필터에 대한 JSON 예제](https://docs.microsoft.com/rest/api/media/accountfilters/createorupdate#create_an_account_filter)도 참조하세요.

## <a name="create-asset-filters"></a>자산 필터 만들기

다음 [az ams asset-filter](https://docs.microsoft.com/cli/azure/ams/asset-filter?view=azure-cli-latest) 명령은 [앞에서 정의한](#define-a-filter) 필터 트랙 선택을 사용하여 자산 필터를 만듭니다. 

```azurecli
az ams asset-filter create -a amsAccount -g resourceGroup -n filterName --asset-name assetName --tracks @tracks.json
```

[필터에 대한 JSON 예제](https://docs.microsoft.com/rest/api/media/assetfilters/createorupdate#create_an_asset_filter)도 참조하세요.


## <a name="associate-filters-with-streaming-locator"></a>스트리밍 로케이터를 사용 하 여 연결 필터

스트리밍 로케이터를 사용 하 여 적용할 자산 또는 계정 필터 목록을 지정할 수 있습니다. 합니다 [동적 Packager (스트리밍 끝점)](dynamic-packaging-overview.md) 이 목록을 함께 클라이언트 URL에 지정 된 필터를 적용 합니다. 이 조합에서는 오류가 발생 하는 [동적 매니페스트](filters-dynamic-manifest-overview.md), URL에 대 한 필터 + 스트리밍 로케이터에 지정 하는 필터를 기준으로 하는 합니다. 필터를 적용 하 고 싶지만 필터 이름을 URL에 노출 하지 않으려는 경우이 기능을 사용 하는 것이 좋습니다.

다음 CLI 코드 스트리밍 로케이터를 만들어 지정 하는 방법을 보여 줍니다 `filters`합니다. 자산 필터 이름 및/또는 계정 필터 이름을 공백으로 구분 된 목록을 사용 하는 선택적 속성입니다.

```azurecli
az ams streaming-locator create -a amsAccount -g resourceGroup -n streamingLocatorName \
                                --asset-name assetName \                               
                                --streaming-policy-name policyName \
                                --filters filterName1 filterName2
                                
```

## <a name="stream-using-filters"></a>필터를 사용 하 여 Stream

필터를 정의하고 나면 클라이언트가 스트리밍 URL에 필터를 사용할 수 있습니다. 적응 비트 전송률 스트리밍 프로토콜에 필터를 적용할 수 있습니다. Apple HLS(HTTP 라이브 스트리밍), MPEG-DASH 및 부드러운 스트리밍

다음 표에서는 필터가 있는 URL의 몇 가지 예제를 보여 줍니다.

|프로토콜|예|
|---|---|
|HLS|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=m3u8-aapl,filter=myAccountFilter)`|
|MPEG DASH|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=mpd-time-csf,filter=myAssetFilter)`|
|부드러운 스트리밍|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(filter=myAssetFilter)`|

## <a name="next-step"></a>다음 단계

[비디오 스트리밍](stream-files-tutorial-with-api.md) 

## <a name="see-also"></a>참고 항목

[Azure CLI](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)
