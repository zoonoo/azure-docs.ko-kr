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
ms.date: 11/26/2018
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 471277433f0fc9a54a28baa158f1e20f1efb613f
ms.sourcegitcommit: da69285e86d23c471838b5242d4bdca512e73853
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/03/2019
ms.locfileid: "54000522"
---
# <a name="creating-filters-with-cli"></a>CLI를 사용하여 필터 만들기 

고객에게 콘텐츠를 제공(라이브 이벤트 또는 주문형 비디오를 스트리밍)하는 경우 클라이언트에게는 기본 자산의 매니페스트 파일에 설명된 내용보다 더 많은 유연성이 필요할 수 있습니다. Azure Media Services를 사용하면 콘텐츠에 사용할 계정 필터 및 자산 필터를 정의할 수 있습니다. 자세한 내용은 [필터 및 동적 매니페스트](filters-dynamic-manifest-overview.md)를 참조하세요.

이 항목에서는 주문형 비디오 자산에 대한 필터를 구성하는 방법 및 Media Services v3용 CLI를 사용하여 [계정 필터](https://docs.microsoft.com/cli/azure/ams/account-filter?view=azure-cli-latest) 및 [자산 필터](https://docs.microsoft.com/cli/azure/ams/asset-filter?view=azure-cli-latest)를 만드는 방법을 보여 줍니다. 

## <a name="prerequisites"></a>필수 조건 

- CLI를 로컬로 설치하여 사용하려면 이 문서에서 Azure CLI 버전 2.0 이상이 필요합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드가 필요한 경우, [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요. 

    현재 일부 [Media Services v3 CLI](https://aka.ms/ams-v3-cli-ref) 명령은 Azure Cloud Shell에서 작동하지 않습니다. CLI를 로컬로 사용하는 것이 좋습니다.
- [Media Services 계정 만들기](create-account-cli-how-to.md) 리소스 그룹 이름과 Media Services 계정 이름을 기억해 두어야 합니다. 
- [필터 및 동적 매니페스트](filters-dynamic-manifest-overview.md)를 검토합니다.

## <a name="define-a-filter"></a>필터 정의 

다음 예제에서는 최종 매니페스트에 추가되는 트랙 선택 조건을 정의합니다. 이 필터는 EC-3인 오디오 트랙 및 비트 전송률이 0-1000000 범위인 비디오 트랙을 모두 포함합니다.

Rest에서 정의된 필터에는 "속성" 래퍼 JSON 개체가 포함됩니다.  

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
az ams account-filter create -a amsAccount -g resourceGroup -n filterName --tracks @c:\tracks.json
```

Azure Cloud Shell을 사용하는 경우 Cloud Shell에 파일을 업로드합니다(셸 창 맨 위에 있는 파일 업로드/다운로드 단추 찾기). 그런 후 다음과 같이 파일을 참조할 수 있습니다.

```azurecli
az ams account-filter create -a amsAccount -g resourceGroup -n filterName --tracks @tracks.json
```

[필터에 대한 JSON 예제](https://docs.microsoft.com/rest/api/media/accountfilters/createorupdate#create_an_account_filter)도 참조하세요.

## <a name="create-asset-filters"></a>자산 필터 만들기

다음 [az ams asset-filter](https://docs.microsoft.com/cli/azure/ams/asset-filter?view=azure-cli-latest) 명령은 [앞에서 정의한](#define-a-filter) 필터 트랙 선택을 사용하여 자산 필터를 만듭니다. 

> [!TIP]
> 이전 섹션에서 파일 이름의 위치 지정 방법에 대한 내용을 참조하세요.

```azurecli
az ams asset-filter create -a amsAccount -g resourceGroup -n filterName --asset-name assetName --tracks @tracks.json
```

[필터에 대한 JSON 예제](https://docs.microsoft.com/rest/api/media/assetfilters/createorupdate#create_an_asset_filter)도 참조하세요.

## <a name="next-step"></a>다음 단계

[비디오 스트리밍](stream-files-tutorial-with-api.md) 

## <a name="see-also"></a>참고 항목

[Azure CLI](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)
