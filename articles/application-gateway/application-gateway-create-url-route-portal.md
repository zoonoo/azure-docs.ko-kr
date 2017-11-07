---
title: "응용 프로그램 게이트웨이용 경로 기반 규칙 만들기 - Azure Portal | Microsoft Docs"
description: "Azure Portal을 사용하여 응용 프로그램 게이트웨이용 경로 기반 규칙을 만드는 방법을 알아봅니다."
services: application-gateway
documentationcenter: na
author: davidmu1
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
ms.author: davidmu
ms.openlocfilehash: b207e7e7bd83e56db68288190c7bedafa8b5b7fa
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-path-based-rule-for-an-application-gateway-by-using-the-azure-portal"></a>Azure Portal을 사용하여 응용 프로그램 게이트웨이용 경로 기반 규칙 만들기

> [!div class="op_single_selector"]
> * [Azure 포털](application-gateway-create-url-route-portal.md)
> * [Azure Resource Manager PowerShell](application-gateway-create-url-route-arm-ps.md)
> * [Azure CLI 2.0](application-gateway-create-url-route-cli.md)

URL 경로 기반 라우팅을 사용하면 HTTP 요청의 URL 경로에 따라 경로를 연결할 수 있습니다. URL 경로 기반 라우팅에서는 응용 프로그램 게이트웨이에 나열되어 있는 URL용으로 구성된 백 엔드 서버 풀에 대한 경로가 있는지 확인한 다음 정의된 풀로 네트워크 트래픽을 전송합니다. URL 경로 기반 라우팅은 다양한 콘텐츠 형식에 대한 요청을 여러 백 엔드 서버 풀로 부하 분산하는 데 흔히 사용됩니다.

응용 프로그램 게이트웨이에는 기본 규칙과 URL 경로 기반 규칙의 두 가지 규칙 유형이 있습니다. 기본 규칙 유형에서는 백 엔드 풀용 라운드 로빈 서비스가 제공됩니다. 경로 기반 규칙에서는 적절한 백 엔드 풀을 선택할 때 라운드 로빈 배포뿐 아니라 요청 URL의 경로 패턴도 사용합니다.

## <a name="scenario"></a>시나리오

다음 시나리오에서는 기존 응용 프로그램 게이트웨이에서 경로 기반 규칙을 만듭니다.
이 시나리오에서는 [Portal을 사용하여 응용 프로그램 게이트웨이 만들기](application-gateway-create-gateway-portal.md)의 단계를 이미 수행한 것으로 가정합니다.

![URL 경로][scenario]

## <a name="createrule"></a>경로 기반 규칙 만들기

경로 기반 규칙에는 자체 수신기가 필요합니다. 규칙을 만들기 전에 사용할 수 있는 수신기가 있는지 확인하세요.

### <a name="step-1"></a>1단계

[Azure Portal](http://portal.azure.com)로 이동하여 기존 응용 프로그램 게이트웨이를 선택합니다. **규칙**을 클릭합니다.

![Application Gateway 개요][1]

### <a name="step-2"></a>2단계

**경로 기반** 단추를 클릭하여 새 경로 기반 규칙을 추가합니다.

### <a name="step-3"></a>3단계

**경로 기반 규칙 추가** 블레이드에는 다음 두 섹션이 있습니다. 첫 번째 섹션에서는 수신기, 규칙의 이름 및 기본 경로 설정을 정의합니다. 기본 경로 설정은 사용자 지정 경로 기반 경로에 속하지 않는 경로에 대한 설정입니다. **경로 기반 규칙 추가** 블레이드의 두 번째 섹션에서는 경로 기반 규칙을 직접 정의합니다.

**기본 설정**

* **이름**: 포털에서 액세스할 수 있는 규칙의 표시 이름입니다.
* **수신기**: 규칙에 사용되는 수신기입니다.
* **기본 백 엔드 풀**: 기본 규칙에 사용할 백 엔드입니다.
* **기본 HTTP 설정**: 기본 규칙에 사용할 HTTP 설정입니다.

**경로 기반 규칙 설정**

* **이름**: 경로 기반 규칙의 표시 이름입니다.
* **경로**: 트래픽을 전달할 때 규칙이 찾는 경로입니다.
* **백 엔드 풀**: 규칙에 사용할 백 엔드입니다.
* **HTTP 설정**: 규칙에 사용할 HTTP 설정입니다.

> [!IMPORTANT]
> **경로** 설정은 일치를 확인할 경로 패턴의 목록입니다. 각 패턴은 슬래시로 시작해야 하며 별표는 경로 끝에만 추가할 수 있습니다. 예를 들면 /xyz, /xyz*, /xyz/* 등을 사용할 수 있습니다.  

![정보가 입력된 경로 기반 규칙 추가 블레이드][2]

Azure Portal을 통해 기존 응용 프로그램 게이트웨이에 경로 기반 규칙을 쉽게 추가할 수 있습니다. 경로 기반 규칙을 만든 후에는 추가 규칙을 포함하도록 규칙을 편집할 수 있습니다. 

![경로 기반 규칙 더 추가][3]

이 단계는 경로 기반 경로를 구성합니다. 요청은 다시 작성되지 않습니다. 요청이 들어오면 응용 프로그램 게이트웨이는 URL 패턴에 따라 요청을 검사한 후 적절한 백 엔드 풀로 전송합니다.

## <a name="next-steps"></a>다음 단계

Azure Application Gateway를 사용하여 SSL 오프로드를 구성하는 방법에 대해 알아보려면 [Azure Portal을 사용하여 SSL 오프로드에 대한 응용 프로그램 게이트웨이 구성](application-gateway-ssl-portal.md)을 참조하세요.

[1]: ./media/application-gateway-create-url-route-portal/figure1.png
[2]: ./media/application-gateway-create-url-route-portal/figure2.png
[3]: ./media/application-gateway-create-url-route-portal/figure3.png
[scenario]: ./media/application-gateway-create-url-route-portal/scenario.png
