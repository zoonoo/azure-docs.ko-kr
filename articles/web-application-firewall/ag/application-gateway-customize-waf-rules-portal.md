---
title: 포털을 사용하여 규칙 사용자 지정 - Azure Web Application Firewall
description: 이 문서에서는 Azure Portal을 통해 Application Gateway에서 Web Application Firewall 규칙을 사용자 지정하는 방법을 설명합니다.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 04/21/2021
ms.author: victorh
ms.topic: article
ms.openlocfilehash: 0ab122d178e5390a53e5a3a39f1b7763b298dc6d
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107878329"
---
# <a name="customize-web-application-firewall-rules-using-the-azure-portal"></a>Azure Portal을 사용하여 Web Application Firewall 규칙 사용자 지정

Azure Application Gateway WAF(Web Application Firewall)는 웹 애플리케이션을 보호합니다. 이러한 보호 기능은 OWASP(Open Web Application Security Project) CRS(코어 규칙 세트)을 통해 제공됩니다. 일부 규칙은 거짓 긍정의 원인이 되어 실제 트래픽을 차단할 수도 있습니다. 이러한 이유로 Application Gateway는 규칙 그룹 및 규칙을 사용자 지정하는 기능을 제공합니다. 특정 규칙 그룹 및 규칙에 대한 자세한 내용은 [Web Application Firewall CRS 규칙 그룹 및 규칙 목록](application-gateway-crs-rulegroups-rules.md)을 참조하세요.

>[!NOTE]
> Application Gateway에서 WAF 계층을 사용하지 않는 경우 Application Gateway를 WAF 계층으로 업그레이드하는 옵션이 오른쪽 창에 표시됩니다. 

:::image type="content" source="../media/application-gateway-customize-waf-rules-portal/1.png" alt-text="WAF 사용"::: 

## <a name="view-rule-groups-and-rules"></a>규칙 그룹 및 규칙 보기

**규칙 그룹 및 규칙을 보려면**
1. Application Gateway로 이동한 다음, **웹 애플리케이션 방화벽** 을 선택합니다.  
2. **WAF 정책** 을 선택합니다.
2. **관리 규칙** 을 선택합니다.

   이 보기는 선택된 규칙 집합과 함께 제공되는 모든 규칙 그룹의 페이지에 하나의 테이블을 표시합니다. 모든 규칙의 확인란이 선택되어 있습니다.

## <a name="disable-rule-groups-and-rules"></a>규칙 그룹 및 규칙을 사용하지 않도록 설정

> [!IMPORTANT]
> 규칙 그룹 또는 규칙을 사용하지 않도록 설정할 때 주의해야 합니다. 이렇게 하면 보안 위험을 증가시킬 수 있습니다.

**규칙 그룹 또는 특정 규칙을 사용하지 않도록 설정하려면**

   1. 사용하지 않도록 설정할 규칙 또는 규칙 그룹을 검색합니다.
   2. 사용하지 않도록 설정하려는 규칙에 대한 확인란을 선택합니다. 
   3. 선택한 규칙에 대해 페이지 상단에서 작업(사용/사용 안 함)을 선택합니다.
   2. **저장** 을 선택합니다.
    :::image type="content" source="../media/application-gateway-customize-waf-rules-portal/figure3.png" alt-text="비활성화된 규칙 저장"::: 

## <a name="mandatory-rules"></a>필수 규칙

다음 목록에는 WAF가 방지 모드에 있는 동안 요청을 차단하도록 하는 조건이 포함되어 있습니다. 검색 모드에서는 예외로 기록됩니다.

구성하거나 사용하지 않도록 설정할 수 없습니다.

* 본문 검사가 꺼져 있지 않은 경우 요청 본문을 구문 분석하지 못하면 요청이 차단됩니다(XML, JSON, 양식 데이터).
* 파일이 없는 요청 본문 데이터 길이가 구성된 제한보다 큽니다.
* 파일을 포함한 요청 본문이 제한보다 큽니다.
* WAF 엔진

CR 3.x 특정:

* 인바운드 변칙 점수가 임계값을 초과했습니다.

## <a name="next-steps"></a>다음 단계

사용하지 않는 규칙을 구성한 후에 WAF 로그를 보는 방법을 알아볼 수 있습니다. 자세한 내용은 [Application Gateway 진단](../../application-gateway/application-gateway-diagnostics.md#diagnostic-logging)을 참조하세요.