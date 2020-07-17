---
title: Azure Front Door
description: 이 문서에서는 Azure Front Door에서 규칙 엔진을 통해 보안 헤더를 구성하는 방법을 설명합니다.
services: frontdoor
documentationcenter: ''
author: megan-beatty
editor: ''
ms.service: frontdoor
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 6/22/2020
ms.author: mebeatty
ms.openlocfilehash: d489186dc1c012fe8c181f17e00bcdb999e230dd
ms.sourcegitcommit: f7e160c820c1e2eb57dc480b2a8fd6bef7053e91
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/10/2020
ms.locfileid: "86232137"
---
# <a name="add-security-headers-with-rules-engine"></a>규칙 엔진을 사용하여 보안 헤더 추가

보안 헤더를 구현하여 HSTS(HTTP Strict-Transport-Security), X-XSS 보호, 콘텐츠 보안 정책 또는 X-Frame-Options와 같은 브라우저 기반 취약성을 방지합니다. 쿠키를 사용하여 보안 기반 특성을 정의할 수도 있습니다.

다음 예제에서는 규칙 엔진 구성이 연결된 경로에 정의된 경로와 일치하는 모든 들어오는 요청에 콘텐츠 보안 정책 헤더를 추가하는 방법을 보여 줍니다. 여기서는 신뢰할 수 있는 사이트, **https://apiphany.portal.azure-api.net** 의 스크립트만 허용하여 애플리케이션에서 실행합니다.

## <a name="add-a-content-security-policy-header-in-azure-portal"></a>Azure Portal에서 콘텐츠 보안 정책 헤더 추가

1. 이 특정 규칙을 만들기 전에 먼저 AFD 또는 규칙 엔진 기능을 처음으로 사용하는 경우 [Front Door 만들기](quickstart-create-front-door.md) 방법 또는 [규칙 엔진 만들기](front-door-tutorial-rules-engine.md) 방법을 알아봅니다.

2. **추가**를 클릭하여 새 규칙을 추가합니다. 규칙 이름을 입력한 다음, **작업 추가** > **응답 헤더**를 클릭합니다.

3. 이 헤더를 이 경로에 들어오는 모든 요청에 대한 응답으로 추가하려면 연산자를 **추가**로 설정합니다.

4. 헤더 이름: **콘텐츠 보안 정책**을 추가하고 이 헤더에 허용되는 값을 정의합니다. 이 시나리오에서는 *"script-src 'self' https://apiphany.portal.azure-api.net."* 을 선택합니다.

5. 구성에 원하는 규칙을 모두 추가한 후에는 기본 경로로 이동하여 규칙 엔진 구성을 경로 규칙에 연결해야 합니다. 이 단계는 규칙이 작동하도록 설정하는 데 필요합니다. 

![포털 샘플](./media/front-door-rules-engine/rules-engine-security-header-example.png)

> [!NOTE]
> 이 시나리오에서는 [일치 조건](front-door-rules-engine-match-conditions.md)을 규칙에 추가하지 않았습니다. 경로 규칙에 정의된 경로와 일치하는 모든 들어오는 요청에는 이 규칙이 적용됩니다. 이러한 요청의 하위 집합에만 적용하려면 특정 일치 조건을 이 규칙에 추가해야 합니다.


## <a name="next-steps"></a>다음 단계

- [AFD 규칙 엔진](front-door-rules-engine.md)에 대해 자세히 알아봅니다. 
- [Front Door를 만드는](quickstart-create-front-door.md) 방법을 알아봅니다.
- [Front Door의 작동 원리](front-door-routing-architecture.md)를 알아봅니다.
- 규칙 엔진 [일치 조건](front-door-rules-engine-match-conditions.md)에 대해 자세히 알아봅니다.
- AFD 규칙 엔진 [CLI 참조](https://docs.microsoft.com/cli/azure/ext/front-door/network/front-door/rules-engine?view=azure-cli-latest)에서 자세한 내용을 알아봅니다. 
- AFD 규칙 엔진 [PowerShell 참조](https://docs.microsoft.com/powershell/module/az.frontdoor/?view=azps-3.8.0)에서 자세한 내용을 알아봅니다. 
