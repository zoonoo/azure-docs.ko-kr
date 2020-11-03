---
title: Azure Media Services v3를 사용할 수 있는 클라우드 및 지역
description: 이 문서에서는 영역에 대 한 끝점 및 코드에 사용 되는 Url에 대해 설명 합니다.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: reference
ms.date: 10/28/2020
ms.author: inhenkel
ms.openlocfilehash: 462cf6cf850885b31588123bac46ff99bf319ee8
ms.sourcegitcommit: 80034a1819072f45c1772940953fef06d92fefc8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2020
ms.locfileid: "93243143"
---
# <a name="regional-code-names-and-endpoints"></a>지역 코드 이름 및 끝점

### <a name="region-code-name"></a>Azure 지역 코드 이름

명령 또는 요청에서 **location** 매개 변수를 사용 하는 경우 지역 코드 이름을 **위치** 값으로 제공 해야 합니다. 계정이 있는 지역의 코드 이름을 가져오고 호출을 라우팅해야 하는 경우 Azure CLI에서 다음 줄을 실행할 수 있습니다.

```azurecli-interactive
az account list-locations
```

일단 위에 표시된 줄을 실행하면 모든 Azure 지역 목록이 표시됩니다. 찾으려는 *displayName* 이 있는 Azure 지역으로 이동한 후 **location** 매개 변수에 대해 해당 *name* 값을 사용합니다.

예를 들어 미국 서부 2의 Azure 지역에서(아래에 표시됨)는 **location** 매개 변수에 "westus2"를 사용합니다.

```json
   {
      "displayName": "West US 2",
      "id": "/subscriptions/00000000-23da-4fce-b59c-f6fb9513eeeb/locations/westus2",
      "latitude": "47.233",
      "longitude": "-119.852",
      "name": "westus2",
      "subscriptionId": null
    }
```

## <a name="endpoints"></a>엔드포인트  

다른 국가별 Azure 클라우드에서 Media Services 계정에 연결할 때 다음 엔드포인트를 알아야 합니다.

### <a name="global-azure"></a>글로벌 Azure

| 서비스 | 엔드포인트 |
| ------- | -------- |
| Azure Resource Manager |  `https://management.azure.com/` |
| 인증 | `https://login.microsoftonline.com/` |
| 토큰 대상 그룹 | `https://management.core.windows.net/` |

### <a name="azure-government"></a>Azure Government

| 서비스 | 엔드포인트 |
| ------- | -------- |
| Azure Resource Manager |  `https://management.usgovcloudapi.net/` |
| 인증 | `https://login.microsoftonline.us/` |
| 토큰 대상 그룹 | `https://management.core.usgovcloudapi.net/` |

[!INCLUDE [Widevine is not available in the GovCloud region.](./includes/widevine-not-available-govcloud.md)]

### <a name="azure-germany"></a>Azure 독일

| 서비스 | 엔드포인트 |
| ------- | -------- |
| Azure Resource Manager | `https://management.cloudapi.de/` |
| 인증 | `https://login.microsoftonline.de/` |
| 토큰 대상 그룹 | `https://management.core.cloudapi.de/`|

### <a name="azure-china-21vianet"></a>Azure 중국 21Vianet

| 서비스 | 엔드포인트 |
| ------- | -------- |
| Azure Resource Manager | `https://management.chinacloudapi.cn/` |
| 인증 | `https://login.chinacloudapi.cn/` |
| 토큰 대상 그룹 |  `https://management.core.chinacloudapi.cn/` |

## <a name="see-also"></a>참고 항목

* [Azure 지역](https://azure.microsoft.com/global-infrastructure/regions/)
* [지역 코드 이름 및 끝점](azure-regions-code-names.md)
* [Azure 지리적 위치](https://azure.microsoft.com/global-infrastructure/geographies/)
* [Azure 위치](https://azure.microsoft.com/global-infrastructure/locations/)

## <a name="next-steps"></a>다음 단계

[Media Services v3 개요](media-services-overview.md)
