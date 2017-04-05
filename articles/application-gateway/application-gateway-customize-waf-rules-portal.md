---
title: "Azure Application Gateway에서 웹 응용 프로그램 방화벽 규칙 사용자 지정 - 포털 | Microsoft Docs"
description: "이 페이지에서는 포털을 통해 Application Gateway에서 웹 응용 프로그램 방화벽 규칙을 사용자 지정하는 방법을 설명합니다."
documentationcenter: na
services: application-gateway
author: georgewallace
manager: timlt
editor: tysonn
ms.assetid: 1159500b-17ba-41e7-88d6-b96986795084
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.custom: 
ms.workload: infrastructure-services
ms.date: 03/28/2017
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: 432752c895fca3721e78fb6eb17b5a3e5c4ca495
ms.openlocfilehash: b08d5543fd3b680b7d49be6e8d2c95ee71aff10c
ms.lasthandoff: 03/30/2017


---

# <a name="customize-web-application-firewall-rules-through-the-portal"></a>포털을 통해 웹 응용 프로그램 방화벽 규칙 사용자 지정

Application Gateway 웹 응용 프로그램 방화벽은 웹 응용 프로그램을 보호합니다. 이러한 보호 기능은 OWASP CRS 규칙 집합을 통해 제공됩니다. 일부 규칙은 거짓 긍정의 원인이 되어 실제 트래픽을 차단할 수도 있습니다.  이러한 이유로 Application Gateway는 Application Gateway를 지원하는 웹 응용 프로그램 방화벽의 규칙 그룹 및 규칙을 사용자 지정하는 기능을 제공합니다. 특정 규칙 그룹 및 규칙에 대한 자세한 내용은 [웹 응용 프로그램 방화벽 CRS 규칙 그룹 및 규칙](application-gateway-crs-rulegroups-rules.md)을 참조하세요.

>[!NOTE]
> Application Gateway에서 WAF 계층을 사용하지 않는 경우 다음 이미지와 같이 Application Gateway를 WAF 계층으로 업그레이드하는 옵션이 제공됩니다.

![waf 사용][fig1]

## <a name="view-rule-groups-and-rules"></a>규칙 그룹 및 규칙 보기

Application Gateway로 이동하여 **웹 응용 프로그램 방화벽**을 선택합니다.  **고급 규칙 구성**을 클릭합니다.  그러면 선택된 규칙 집합과 함께 모든 규칙 그룹이 표시되는 페이지에 테이블이 하나 표시됩니다.

![사용하지 않는 규칙 구성][1]

## <a name="search-for-rules-to-disable"></a>사용하지 않을 규칙 검색

웹 응용 프로그램 방화벽 설정 블레이드는 텍스트 검색으로 규칙을 필터링하는 기능을 제공합니다. 검색 텍스트가 포함된 규칙 그룹 및 규칙만 결과에 표시됩니다.

![규칙 검색][2]

## <a name="disable-rule-groups-and-rules"></a>규칙 그룹 및 규칙을 사용하지 않도록 설정

규칙을 사용하지 않도록 설정할 때 규칙 그룹 전체를 사용하지 않도록 설정하거나 하나 이상의 규칙 그룹에서 특정 규칙만 사용하지 않도록 설정할 수 있습니다.  사용하지 않을 규칙을 선택 해제한 후 **저장**을 클릭합니다.  그러면 Application Gateway에 변경 내용이 저장됩니다.

![변경 내용 저장][3]

## <a name="next-steps"></a>다음 단계

사용하지 않을 규칙을 구성했으면 [Application Gateway 진단](application-gateway-diagnostics.md#diagnostic-logging)을 방문하여 WAF 로그를 보는 방법에 대해 알아보세요.

[fig1]: ./media/application-gateway-customize-waf-rules-portal/1.png
[1]: ./media/application-gateway-customize-waf-rules-portal/figure1.png
[2]: ./media/application-gateway-customize-waf-rules-portal/figure2.png
[3]: ./media/application-gateway-customize-waf-rules-portal/figure3.png
