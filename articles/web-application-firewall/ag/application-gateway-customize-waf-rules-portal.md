---
title: 포털을 사용 하 여 규칙 사용자 지정-Azure 웹 응용 프로그램 방화벽
description: 이 문서에서는 Azure Portal Application Gateway에서 웹 응용 프로그램 방화벽 규칙을 사용자 지정 하는 방법에 대 한 정보를 제공 합니다.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 11/14/2019
ms.author: victorh
ms.topic: article
ms.openlocfilehash: c4635333614ee1c0fd0322c29a659380fb4315c9
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "74048372"
---
# <a name="customize-web-application-firewall-rules-using-the-azure-portal"></a>Azure Portal를 사용 하 여 웹 응용 프로그램 방화벽 규칙 사용자 지정

Azure 애플리케이션 게이트웨이 WAF (웹 응용 프로그램 방화벽)는 웹 응용 프로그램에 대 한 보호를 제공 합니다. 이러한 보호 기능은 OWASP(Open Web Application Security Project) CRS(코어 규칙 세트)을 통해 제공됩니다. 일부 규칙은 거짓 긍정의 원인이 되어 실제 트래픽을 차단할 수도 있습니다. 이러한 이유로 Application Gateway는 규칙 그룹 및 규칙을 사용자 지정하는 기능을 제공합니다. 특정 규칙 그룹 및 규칙에 대 한 자세한 내용은 [웹 응용 프로그램 방화벽 CRS 규칙 그룹 및 규칙 목록](application-gateway-crs-rulegroups-rules.md)을 참조 하세요.

>[!NOTE]
> Application Gateway에서 WAF 계층을 사용하지 않는 경우 Application Gateway를 WAF 계층으로 업그레이드하는 옵션이 오른쪽 창에 표시됩니다. 

![WAF 사용][fig1]

## <a name="view-rule-groups-and-rules"></a>규칙 그룹 및 규칙 보기

**규칙 그룹 및 규칙을 보려면**
1. Application Gateway로 이동한 다음, **웹 애플리케이션 방화벽**을 선택합니다.  
2. **Waf 정책을**선택 합니다.
2. **관리 되는 규칙**을 선택 합니다.

   이 보기는 선택된 규칙 집합과 함께 제공되는 모든 규칙 그룹의 페이지에 하나의 테이블을 표시합니다. 모든 규칙의 확인란이 선택되어 있습니다.

## <a name="disable-rule-groups-and-rules"></a>규칙 그룹 및 규칙을 사용하지 않도록 설정

> [!IMPORTANT]
> 규칙 그룹 또는 규칙을 사용 하지 않도록 설정할 때는 주의 해야 합니다. 이렇게 하면 보안 위험을 증가 시킬 수 있습니다.

규칙을 사용 하지 않도록 설정 하는 경우 하나 이상의 규칙 그룹에서 전체 규칙 그룹 또는 특정 규칙을 사용 하지 않도록 설정할 수 있습니다. 

**규칙 그룹 또는 특정 규칙을 사용하지 않도록 설정하려면**

   1. 사용하지 않도록 설정할 규칙 또는 규칙 그룹을 검색합니다.
   2. 사용 하지 않을 규칙에 대 한 확인란을 선택 합니다. 
   3. 선택한 규칙에 대 한 페이지 위쪽의 동작 (설정/해제)을 선택 합니다.
   2. **저장**을 선택합니다. 

![변경 내용 저장][3]

## <a name="mandatory-rules"></a>필수 규칙

다음 목록에는 방지 모드에서 WAF가 요청을 차단 하는 조건이 포함 되어 있습니다. 검색 모드에서는 예외로 기록 됩니다.

구성 하거나 사용 하지 않도록 설정할 수 없습니다.

* 본문 검사를 해제 하지 않는 한 요청 본문을 구문 분석 하지 못하면 요청이 차단 됩니다 (XML, JSON, 폼 데이터).
* 요청 본문 (파일 없음) 데이터 길이가 구성 된 제한 보다 큽니다.
* 요청 본문 (파일 포함)이 제한 보다 큽니다.
* WAF 엔진에서 내부 오류가 발생 했습니다.

CR 3.x 특정:

* 인바운드 변칙 점수가 임계값을 초과 했습니다.

## <a name="next-steps"></a>다음 단계

사용하지 않는 규칙을 구성한 후에 WAF 로그를 보는 방법을 알아볼 수 있습니다. 자세한 내용은 [Application Gateway 진단](../../application-gateway/application-gateway-diagnostics.md#diagnostic-logging)을 참조하세요.

[fig1]: ../media/application-gateway-customize-waf-rules-portal/1.png
[3]: ../media/application-gateway-customize-waf-rules-portal/figure3.png
