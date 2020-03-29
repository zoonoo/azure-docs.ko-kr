---
title: 필요한 매개변수 수집
services: cognitive-services
author: IEvangelist
manager: nitinme
description: 모든 코그너티브 서비스의 컨테이너에 대한 매개 변수
ms.service: cognitive-services
ms.topic: include
ms.date: 10/15/2019
ms.author: dapine
ms.openlocfilehash: bf4e8f06642a4a764794a75e2b49f15958796c61
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "73465911"
---
## <a name="gathering-required-parameters"></a>필요한 매개변수 수집

필요한 모든 코그너티브 서비스의 컨테이너에 대한 세 가지 기본 매개 변수가 있습니다. 최종 사용자 사용권 계약(EULA)은 `accept`의 값과 함께 있어야 합니다. 또한 끝점 URL과 API 키가 모두 필요합니다.

### <a name="endpoint-uri-endpoint_uri"></a>끝점 URI`{ENDPOINT_URI}`

**끝점** URI 값은 해당 인지 서비스 리소스의 Azure 포털 *개요* 페이지에서 사용할 수 있습니다. *개요* 페이지로 이동하여 끝점 위로 마우스를 `Copy to clipboard` <span class="docon docon-edit-copy x-hidden-focus"></span> 가져가면 아이콘이 나타납니다. 필요한 경우 복사하여 사용하십시오.

![나중에 사용할 수 있는 끝점 uri 수집](../media/overview-endpoint-uri.png)

### <a name="keys-api_key"></a>키`{API_KEY}`

이 키는 컨테이너를 시작하는 데 사용되며 해당 인지 서비스 리소스의 Azure 포털의 키 페이지에서 사용할 수 있습니다. *키* 페이지로 이동하여 아이콘을 `Copy to clipboard` <span class="docon docon-edit-copy x-hidden-focus"></span> 클릭합니다.

![나중에 사용할 수 있도록 두 가지 키 중 하나를 가져옵니다.](../media/keys-copy-api-key.png)

> [!IMPORTANT]
> 이러한 구독 키는 코그너티브 서비스 API에 액세스하는 데 사용됩니다. 키를 공유하지 마십시오. 예를 들어 Azure 키 자격 증명 모음을 사용하여 안전하게 저장합니다. 또한 이러한 키를 정기적으로 다시 생성하는 것이 좋습니다. API 호출을 하려면 하나의 키만 필요합니다. 첫 번째 키를 다시 생성할 때 서비스에 대한 지속적인 액세스를 위해 두 번째 키를 사용할 수 있습니다.