---
title: "경로 기반 규칙 만들기 - Azure Application Gateway - Azure Portal | Microsoft Docs"
description: "포털을 사용하여 응용 프로그램 게이트웨이에 대한 경로 기반 규칙을 만드는 방법을 알아봅니다."
services: application-gateway
documentationcenter: na
author: georgewallace
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 87bd93bc-e1a6-45db-a226-555948f1feb7
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/03/2017
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: 303cb9950f46916fbdd58762acd1608c925c1328
ms.openlocfilehash: df167435757b2d9d2d25b58b1b548a811b490eb5
ms.lasthandoff: 04/04/2017


---
# <a name="create-a-path-based-rule-for-an-application-gateway-by-using-the-portal"></a>포털을 사용하여 응용 프로그램 게이트웨이에 대한 경로 기반 규칙 만들기

> [!div class="op_single_selector"]
> * [Azure 포털](application-gateway-create-url-route-portal.md)
> * [Azure Resource Manager PowerShell](application-gateway-create-url-route-arm-ps.md)

URL 경로 기반 라우팅을 사용하여 Http 요청의 URL 경로에 따라 경로를 연결할 수 있습니다. Application Gateway에 나열된 URL에 대해 구성된 백 엔드 풀에 대한 경로가 있는지 확인하고 정의된 백 엔드 풀로 네트워크 트래픽을 전송합니다. URL 기반 라우팅의 일반적인 용도는 여러 콘텐츠 형식에 대한 요청의 부하를 여러 백 엔드 서버 풀에 분산하는 것입니다.

URL 기반 라우팅에서는 응용 프로그램 게이트웨이에 새로운 규칙 형식을 제공합니다. 응용 프로그램 게이트웨이에는 두 가지 규칙 형식(기본 및 경로 기반 규칙)이 있습니다. 기본 규칙 형식은 백 엔드 풀에 대한 라운드 로빈 서비스를 제공하는 반면 경로 기반 규칙은 해당 백 엔드 풀을 선택하는 동안 라운드 로빈 배포 외에도 계정에 요청 URL의 경로 패턴을 사용합니다.

## <a name="scenario"></a>시나리오

다음 시나리오에서는 기존 응용 프로그램 게이트웨이에서 경로 기반 규칙을 만드는 과정을 살펴봅니다.
이 시나리오에서는 [응용 프로그램 게이트웨이 만들기](application-gateway-create-gateway-portal.md)단계를 이미 수행한 것으로 가정합니다.

![url 경로][scenario]

## <a name="createrule"></a>경로 기반 규칙 만들기

경로 기반 규칙에는 고유한 수신기가 필요합니다. 규칙을 만들기 전에 사용할 수 있는 수신기가 있는지 반드시 확인해야 합니다.

### <a name="step-1"></a>1단계:

[Azure Portal](http://portal.azure.com)로 이동하여 기존 Application Gateway를 선택합니다. **규칙**

![응용 프로그램 게이트웨이 개요][1]

### <a name="step-2"></a>2단계

**경로 기반** 단추를 클릭하여 새 경로 기반 규칙을 추가합니다.

### <a name="step-3"></a>3단계

**경로 기반 규칙 추가** 블레이드에는 다음 두 섹션이 있습니다. 두 번째 섹션에서는 수신기, 규칙의 이름 및 기본 경로 설정을 정의합니다. 기본 경로 설정은 사용자 지정 경로 기반 경로에 속하지 않는 경로에 대한 설정입니다. **경로 기반 규칙 추가** 블레이드의 두 번째 섹션에서는 경로 기반 규칙을 직접 정의합니다.

**기본 설정**

* **이름** - 포털에서 액세스할 수 있는 규칙의 이름입니다.
* **수신기** - 규칙에 사용되는 수신기입니다.
* **기본 백 엔드 풀** - 기본 규칙에 사용할 백 엔드를 정의하는 설정입니다
* **기본 HTTP 설정** - 기본 규칙에 사용할 HTTP 설정을 정의하는 설정입니다.

**경로 기반 규칙**

* **이름** - 경로 기반 규칙의 이름입니다.
* **경로** - 이 설정은 트래픽을 전달할 때 규칙이 찾는 경로를 정의합니다.
* **백 엔드 풀** - 규칙에 사용할 백 엔드를 정의하는 설정입니다
* **HTTP 설정** - 규칙에 사용할 HTTP 설정을 정의하는 설정입니다.

> [!IMPORTANT]
> 경로: 일치하는 경로 패턴의 목록입니다. 각각은 /로 시작해야 하고 "\*"는 끝에만 올 수 있습니다. 사용 가능한 예는 /xyz, /xyz* 또는 /xyz/*입니다.  

![정보가 입력된 경로 기반 규칙 추가 블레이드][2]

기존 응용 프로그램 게이트웨이에 경로 기반 규칙을 추가하는 작업은 포털을 통해 쉽게 수행할 수 있습니다. 경로 기반 규칙을 만들면 추가 규칙을 쉽게 추가하도록 편집할 수 있습니다. 

![추가 경로 기반 규칙 추가][3]

이렇게 하면 경로 기반 경로가 구성됩니다. 응용 프로그램에 요청이 들어 올 때 요청은 다시 작성되지 않고, 게이트웨이가 요청 및 URL 패턴에 대한 기본 사항을 검사한 후 해당 백 엔드로 요청을 보냅니다.

## <a name="next-steps"></a>다음 단계

Azure 응용 프로그램 게이트웨이를 사용하여 SSL 오프로드를 구성하는 방법은 [SSL 오프로드 구성](application-gateway-ssl-portal.md)

[1]: ./media/application-gateway-create-url-route-portal/figure1.png
[2]: ./media/application-gateway-create-url-route-portal/figure2.png
[3]: ./media/application-gateway-create-url-route-portal/figure3.png
[scenario]: ./media/application-gateway-create-url-route-portal/scenario.png

