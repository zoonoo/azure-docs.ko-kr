---
title: Video Indexer를 사용할 수 있는 지역 - Azure
titleSuffix: Azure Media Services
description: 이 문서에서는 Azure Media Services Video Indexer을 사용할 수 있는 Azure 지역에 대해 설명 합니다.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 09/14/2020
ms.author: juliako
ms.openlocfilehash: 6ebdb22f50efbefc695f9752c6e6fc333571828c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "90530943"
---
# <a name="azure-regions-in-which-video-indexer-exists"></a>Video Indexer가 있는 Azure 지역

Video Indexer API에는 호출을 라우팅할 Azure 지역으로 설정해야 하는 **location** 매개 변수가 있습니다. 이 매개 변수는 [Video Indexer를 사용할 수 있는 Azure 지역](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services&regions=all)이어야 합니다.

## <a name="locations"></a>위치

`location`매개 변수에는 Azure 지역 코드 이름을 해당 값으로 지정 해야 합니다. 미리 보기 모드에서 Video Indexer를 사용 하는 경우 값으로을 입력 해야 합니다 `"trial"` . `trial``location`매개 변수의 기본값입니다. 그렇지 않고, 계정이 있는 Azure 지역의 코드 이름을 가져오고 호출을 라우팅해야 하는 경우 Azure Portal를 사용 하거나 [Azure CLI](/cli/azure) 명령을 실행할 수 있습니다.

### <a name="azure-portal"></a>Azure portal

1. [Video Indexer](https://www.videoindexer.ai/) 웹 사이트에서 로그인합니다.
1. 페이지의 오른쪽 위 모서리에서 **사용자 계정** 을 선택 합니다.
1. 오른쪽 위 모서리에서 계정의 위치를 찾습니다.  

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/location/location1.png" alt-text="위치":::
    
###  <a name="cli-command"></a>CLI 명령

```azurecli-interactive
az account list-locations
```

일단 위에 표시된 줄을 실행하면 모든 Azure 지역 목록이 표시됩니다. 찾으려는 *displayName*이 있는 Azure 지역으로 이동한 후 **location** 매개 변수에 대해 해당 *name* 값을 사용합니다.

예를 들어 미국 서부 2의 Azure 지역에서(아래에 표시됨)는 **location** 매개 변수에 "westus2"를 사용합니다.

```json
   {
      "displayName": "West US 2",
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/locations/westus2",
      "latitude": "47.233",
      "longitude": "-119.852",
      "name": "westus2",
      "subscriptionId": null
    }
```

## <a name="next-steps"></a>다음 단계

- [API를 사용하여 언어 모델 사용자 지정](customize-language-model-with-api.md)
- [API를 사용하여 브랜드 모델 사용자 지정](customize-brands-model-with-api.md)
- [API를 사용하여 개인 모델 사용자 지정](customize-person-model-with-api.md)
