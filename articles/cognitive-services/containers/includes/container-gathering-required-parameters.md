---
title: 필수 매개 변수 수집
services: cognitive-services
author: aahill
manager: nitinme
description: 모든 Cognitive Services의 컨테이너에 대한 매개 변수
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 85c67384c38376dc6f5701cf26e879d6e1b76c7f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "95996509"
---
## <a name="gathering-required-parameters"></a>필수 매개 변수 수집

필요한 모든 Cognitive Services의 컨테이너에는 세 가지 기본 매개 변수가 있습니다. EULA(최종 사용자 라이선스 계약)는 `accept` 값과 함께 제공되어야 합니다. 또한 엔드포인트 URL과 API 키가 모두 필요합니다.

### <a name="endpoint-uri-endpoint_uri"></a>엔드포인트 URI `{ENDPOINT_URI}`

**엔드포인트** URI 값은 해당 Cognitive Service 리소스의 Azure Portal *개요* 페이지에서 사용할 수 있습니다. *개요* 페이지로 이동하여 엔드포인트를 마우스로 가리키면 `Copy to clipboard` <span class="docon docon-edit-copy x-hidden-focus"></span> 아이콘이 나타납니다. 필요에 따라 복사하여 사용합니다.

![나중에 사용할 엔드포인트 uri 수집](../media/overview-endpoint-uri.png)

### <a name="keys-api_key"></a>키 `{API_KEY}`

이 키는 컨테이너를 시작하는 데 사용되며 해당 Cognitive Service 리소스의 Azure Portal 키 페이지에서 사용할 수 있습니다. *키* 페이지로 이동하여 `Copy to clipboard` <span class="docon docon-edit-copy x-hidden-focus"></span> 아이콘을 클릭합니다.

![나중에 사용할 두 키 중 하나를 가져옵니다.](../media/keys-copy-api-key.png)

> [!IMPORTANT]
> 이러한 구독 키는 Cognitive Service API에 액세스하는 데 사용됩니다. 키를 공유하지 마세요. 예를 들어 Azure Key Vault를 사용하여 안전하게 저장합니다. 또한 이러한 키는 정기적으로 다시 생성하는 것이 좋습니다. API 호출을 수행하는 데는 하나의 키만 필요합니다. 첫 번째 키를 다시 생성하는 경우 두 번째 키를 사용하여 서비스에 계속 액세스할 수 있습니다.