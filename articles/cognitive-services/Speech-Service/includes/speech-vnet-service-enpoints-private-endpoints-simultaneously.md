---
author: alexeyo26
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 03/19/2021
ms.author: alexeyo
ms.openlocfilehash: 8a1253e7ac88d2fe8b557c48dd846b48de59fba3
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/07/2021
ms.locfileid: "110059223"
---
## <a name="simultaneous-use-of-private-endpoints-and-vnet-service-endpoints"></a>프라이빗 엔드포인트 및 VNet 서비스 엔드포인트 동시 사용

[프라이빗 엔드포인트](../speech-services-private-link.md) 및 [VNet 서비스 엔드포인트](../speech-service-vnet-service-endpoint.md)를 사용하여 동일한 음성 리소스에 동시에 액세스할 수 있습니다. 그러나 프라이빗 엔드포인트와 VNet 서비스 엔드포인트를 동시에 사용하도록 설정하려면 Azure Portal에 있는 음성 리소스의 네트워킹 설정에서 **선택한 네트워크 및 프라이빗 엔드포인트** 옵션을 사용해야 합니다. 이 시나리오에서는 다른 모든 옵션이 지원되지 않습니다.
