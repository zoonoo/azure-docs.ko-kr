---
title: Azure Application Gateway-Azure portal에서에서 웹 응용 프로그램 방화벽 규칙 사용자 지정
description: 이 문서에서는 Azure Portal을 통해 Application Gateway에서 웹 애플리케이션 방화벽 규칙을 사용자 지정하는 방법을 설명합니다.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.date: 2/22/2019
ms.author: victorh
ms.openlocfilehash: f7ffb8d6adfd4afc75618834a3fe82cf9a3d0c9f
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2019
ms.locfileid: "64720389"
---
# <a name="customize-web-application-firewall-rules-through-the-azure-portal"></a>Azure Portal을 통해 웹 애플리케이션 방화벽 규칙 사용자 지정

Azure Application Gateway WAF(웹 애플리케이션 방화벽)는 웹 애플리케이션을 보호합니다. 이러한 보호 기능은 OWASP(Open Web Application Security Project) CRS(코어 규칙 세트)을 통해 제공됩니다. 일부 규칙은 거짓 긍정의 원인이 되어 실제 트래픽을 차단할 수도 있습니다. 이러한 이유로 Application Gateway는 규칙 그룹 및 규칙을 사용자 지정하는 기능을 제공합니다. 특정 규칙 그룹 및 규칙에 대한 자세한 내용은 [웹 애플리케이션 방화벽 CRS 규칙 그룹 및 규칙 목록](application-gateway-crs-rulegroups-rules.md)을 참조하세요.

>[!NOTE]
> Application Gateway에서 WAF 계층을 사용하지 않는 경우 Application Gateway를 WAF 계층으로 업그레이드하는 옵션이 오른쪽 창에 표시됩니다. 

![WAF 사용][fig1]

## <a name="view-rule-groups-and-rules"></a>규칙 그룹 및 규칙 보기

**규칙 그룹 및 규칙을 보려면**
   1. Application Gateway로 이동한 다음, **웹 애플리케이션 방화벽**을 선택합니다.  
   2. **고급 규칙 구성**을 선택합니다.  
   이 보기는 선택된 규칙 집합과 함께 제공되는 모든 규칙 그룹의 페이지에 하나의 테이블을 표시합니다. 모든 규칙의 확인란이 선택되어 있습니다.

![사용하지 않도록 설정된 규칙 구성][1]

## <a name="search-for-rules-to-disable"></a>사용하지 않을 규칙 검색

합니다 **웹 응용 프로그램 방화벽 설정** 페이지 텍스트 검색을 통해 규칙을 필터링 하는 기능을 제공 합니다. 검색 텍스트가 포함된 규칙 그룹 및 규칙만 결과에 표시됩니다.

![규칙 검색][2]

## <a name="disable-rule-groups-and-rules"></a>규칙 그룹 및 규칙을 사용하지 않도록 설정

> [!IMPORTANT]
> 모든 규칙 그룹 또는 규칙을 사용 하지 않도록 설정 하는 경우에 주의 해야 합니다. 이 향상 된 보안 위험에 노출 될 수 있습니다.

규칙을 비활성화 하는 경우에 하나 이상의 규칙 그룹에서 특정 규칙 또는 규칙 그룹 전체를 비활성화할 수 있습니다. 

**규칙 그룹 또는 특정 규칙을 사용하지 않도록 설정하려면**

   1. 사용하지 않도록 설정할 규칙 또는 규칙 그룹을 검색합니다.
   2. 사용하지 않도록 설정하려는 규칙에 대한 확인란을 선택 취소합니다. 
   2. **저장**을 선택합니다. 

![변경 내용 저장][3]

## <a name="mandatory-rules"></a>필수 규칙

다음은 WAF 방지 모드에서 요청을 차단 하도록 유도 하는 조건을 포함 합니다. 검색 모드에서 예외로 기록한 것입니다.

이러한 구성 하거나 비활성화할 수 있습니다.

* 요청 본문을 구문 분석 하지 못하면 차단 되 고 요청에 본문 검사 됩니다 (XML, JSON, 양식 데이터)를 해제 하지 않는다면
* 요청 본문 (파일 없음)와 데이터 길이 구성된 된 제한 보다 큽니다.
* 요청 본문 (파일 포함) 제한 보다 큽니다.
* WAF 엔진에 내부 오류가 발생 했습니다.

CRS 3.x 관련:

* 인바운드 변칙을 초과 하는 점수 임계값

## <a name="next-steps"></a>다음 단계

사용하지 않는 규칙을 구성한 후에 WAF 로그를 보는 방법을 알아볼 수 있습니다. 자세한 내용은 [Application Gateway 진단](application-gateway-diagnostics.md#diagnostic-logging)을 참조하세요.

[fig1]: ./media/application-gateway-customize-waf-rules-portal/1.png
[1]: ./media/application-gateway-customize-waf-rules-portal/figure1.png
[2]: ./media/application-gateway-customize-waf-rules-portal/figure2.png
[3]: ./media/application-gateway-customize-waf-rules-portal/figure3.png
