---
title: Azure 웹 응용 프로그램 방화벽 및 Azure Policy
description: Azure WAF (웹 응용 프로그램 방화벽)를 Azure Policy와 결합 하면 조직 표준을 적용 하 고 WAF 리소스에 대 한 규모에 맞게 규정을 평가할 수 있습니다.
author: tremansdoerfer
ms.service: web-application-firewall
services: web-application-firewall
ms.topic: conceptual
ms.date: 07/07/2020
ms.author: rimansdo
ms.openlocfilehash: 937f220980d602b755b6329da4d93df0e4b372ab
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "86224063"
---
# <a name="azure-web-application-firewall-and-azure-policy"></a>Azure 웹 응용 프로그램 방화벽 및 Azure Policy

Azure WAF (웹 응용 프로그램 방화벽)를 Azure Policy와 결합 하면 조직 표준을 적용 하 고 WAF 리소스에 대 한 호환성을 평가 하는 데 도움이 될 수 있습니다. Azure policy는 리소스 별, 정책 별 세분성으로 드릴 다운할 수 있는 기능을 통해 전반적인 환경 상태를 평가 하는 집계 된 보기를 제공 하는 거 버 넌 스 도구입니다. Azure 정책을 사용 하면 기존 리소스에 대 한 대량 수정 및 새 리소스에 대 한 자동 수정을 통해 리소스를 준수 하도록 할 수 있습니다.

## <a name="azure-policies-for-web-application-firewall"></a>웹 응용 프로그램 방화벽에 대 한 Azure 정책

WAF 리소스를 관리 하는 몇 가지 기본 제공 Azure 정책이 있습니다. 정책 및 해당 기능에 대 한 분석은 다음과 같습니다.

1. **Waf (웹 응용 프로그램 방화벽)는 Azure Front 도어 서비스에서 사용 하도록 설정 해야 합니다**. Azure Front 도어 서비스는 리소스를 만들 때 waf가 있는 경우에 평가 됩니다. 정책에는 감사, 거부 및 사용 안 함 이라는 세 가지 효과가 있습니다. 감사 트랙은 Azure Front 도어 서비스에 WAF가 없고 사용자에 게 Azure Front 도어 서비스가 준수 하지 않는 것을 볼 수 있도록 합니다. Deny는 WAF가 연결 되지 않은 경우 Azure Front 도어 서비스를 만들지 못하도록 합니다. 사용 안 함이 정책을 해제 합니다.

2. **WAF (웹 응용 프로그램 방화벽)는 Application Gateway에 대해 사용 하도록 설정 해야 합니다**. 리소스를 만들 때 waf가 있는 경우 응용 프로그램 게이트웨이가 평가 됩니다. 정책에는 감사, 거부 및 사용 안 함 이라는 세 가지 효과가 있습니다. 감사는 Application Gateway에 WAF가 없고 사용자가 준수 하지 않는 Application Gateway을 볼 수 있는 경우 추적 합니다. Deny를 설정 하면 WAF가 연결 되지 않은 경우 Application Gateway 생성 되지 않습니다. 사용 안 함이 정책을 해제 합니다.

3. **WAF (웹 응용 프로그램 방화벽)는 Azure Front 도어 서비스에 대해 지정 된 모드를 사용 해야**합니다. Azure Front 도어 서비스에 대 한 모든 웹 응용 프로그램 방화벽 정책에서 ' 검색 ' 또는 ' 방지 ' 모드의 사용을 활성으로 지정 합니다. 정책에는 감사, 거부 및 사용 안 함 이라는 세 가지 효과가 있습니다. WAF가 지정 된 모드에 맞지 않는 경우 감사 추적입니다. Deny는 올바른 모드가 아닌 경우 WAF를 만들지 않도록 합니다. 사용 안 함이 정책을 해제 합니다.

4. **WAF (웹 응용 프로그램 방화벽)는 Application Gateway에 대해 지정 된 모드를 사용 해야**합니다. Application Gateway에 대 한 모든 웹 응용 프로그램 방화벽 정책에서 ' 검색 ' 또는 ' 방지 ' 모드의 사용을 활성화 하도록 지정 합니다. 정책에는 감사, 거부 및 사용 안 함 이라는 세 가지 효과가 있습니다. WAF가 지정 된 모드에 맞지 않는 경우 감사 추적입니다. Deny는 올바른 모드가 아닌 경우 WAF를 만들지 않도록 합니다. 사용 안 함이 정책을 해제 합니다.


## <a name="launch-an-azure-policy"></a>Azure Policy 시작


1.  Azure 홈 페이지의 검색 표시줄에 Policy를 입력 하 고 Azure Policy 아이콘을 클릭 합니다.

2.  Azure 정책 서비스의 **제작**에서 **할당**을 선택 합니다.

[!div class="mx-imgBorder"]
![Azure 웹 응용 프로그램 방화벽](../media/waf-azure-policy/policy-home.png)

3.  할당 페이지의 맨 위에 있는 **정책 할당** 아이콘을 선택 합니다.

[!div class="mx-imgBorder"]
![Azure 웹 응용 프로그램 방화벽](../media/waf-azure-policy/assign-policy.png)

4.  정책 페이지 기본 사항 할당 탭에서 다음 필드를 업데이트 합니다.
    1.  **범위**: Azure Policy에 영향을 줄 수 있는 Azure 구독 및 리소스 그룹을 선택 합니다.
    2.  **제외**: 정책에서 제외할 범위에서 리소스를 선택 합니다. 
    3.  **정책 정의**: 제외를 사용 하 여 범위에 적용할 Azure Policy를 선택 합니다. 검색 표시줄에 "웹 응용 프로그램 방화벽"을 입력 하 여 Azure Policy 관련 웹 응용 프로그램 방화벽을 선택 합니다.

[!div class="mx-imgBorder"]
![Azure 웹 응용 프로그램 방화벽](../media/waf-azure-policy/policy-listing.png)


5.  **매개 변수** 탭을 선택 하 고 정책 매개 변수를 업데이트 합니다. 매개 변수를 추가로 명확 하 게 설명 하기 위해 매개 변수 이름 옆에 있는 정보 아이콘을 마우스로 가리켜 추가 설명을 확인 합니다.

6.  **검토 + 만들기** 를 선택 하 여 Azure 정책을 마무리 합니다. Azure 정책은 새 리소스에 대해 활성 상태가 될 때까지 약 15 분이 걸립니다.
