---
title: '자습서: Azure Front Door에 대한 WAF 정책 만들기 - Azure Portal'
description: 이 자습서에서는 Azure Portal을 사용하여 WAF(웹 애플리케이션 방화벽) 정책을 만드는 방법을 알아봅니다.
author: vhorne
ms.service: web-application-firewall
services: web-application-firewall
ms.topic: tutorial
ms.date: 03/10/2020
ms.author: victorh
ms.openlocfilehash: be66a93ea4a518b26d973d222caf58e73b6986a3
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/24/2020
ms.locfileid: "79475844"
---
# <a name="tutorial-create-a-web-application-firewall-policy-on-azure-front-door-using-the-azure-portal"></a>자습서: Azure Portal을 사용하여 Azure Front Door에 대한 웹 애플리케이션 방화벽 정책 만들기

이 자습서는 기본 Azure WAF(웹 애플리케이션 방화벽) 정책을 만들고 Azure Front Door의 프런트 엔드 호스트에 적용하는 방법을 안내합니다.

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
> * WAF 정책 만들기
> * 프런트 엔드 호스트에 연결
> * WAF 규칙 구성

## <a name="prerequisites"></a>사전 요구 사항

[빠른 시작: Front Door 프로필 만들기](../../frontdoor/quickstart-create-front-door.md)에 설명된 지침에 따라 Front Door 프로필을 만듭니다. 

## <a name="create-a-web-application-firewall-policy"></a>웹 애플리케이션 방화벽 정책 만들기

먼저, 포털을 사용하여 관리형 DRS(기본 규칙 집합)로 기본 WAF 정책을 만드세요. 

1. 화면 왼쪽 상단에서 **리소스 만들기**를 선택하고, **WAF**를 검색하고, **웹 애플리케이션 방화벽(미리 보기)** 을 선택한 다음, **만들기**를 선택합니다.
2. **WAF 정책 만들기** 페이지의 **기본** 탭에서 다음 정보를 입력하거나 선택하고, 나머지 설정은 기본값을 그대로 유지한 다음, **검토 + 만들기**를 선택합니다.

    | 설정                 | 값                                              |
    | ---                     | ---                                                |
    | Subscription            |Front Door 구독 이름을 선택합니다.|
    | Resource group          |Front Door 리소스 그룹 이름을 선택합니다.|
    | 정책 이름             |WAF 정책의 고유한 이름을 입력합니다.|

   ![WAF 정책 만들기](../media/waf-front-door-create-portal/basic.png)

3. **WAF 정책 만들기** 페이지의 **연결** 탭에서 **프런트 엔드 호스트 추가**를 선택하고, 다음 설정을 입력한 다음, **추가**를 선택합니다.

    | 설정                 | 값                                              |
    | ---                     | ---                                                |
    | Front Door              | Front Door 프로필 이름을 선택합니다.|
    | 프런트 엔드 호스트           | Front Door 호스트의 이름을 선택한 다음, **추가**를 선택합니다.|
    
    > [!NOTE]
    > 프런트 엔드 호스트가 WAF 정책에 연결되면 회색으로 표시됩니다. 먼저, 연결된 정책에서 프런트 엔드 호스트를 제거한 다음, 프런트 엔드 호스트를 새 WAF 정책에 다시 연결해야 합니다.
1. **검토 + 만들기**를 선택한 다음, **만들기**를 선택합니다.

## <a name="configure-web-application-firewall-rules-optional"></a>웹 애플리케이션 방화벽 규칙 구성(선택 사항)

### <a name="change-mode"></a>모드 변경

WAF 정책을 만드는 경우 기본적으로 WAF 정책은 **검색** 모드에 있습니다. **검색** 모드에서 WAF는 아무 요청도 차단하지 않으며, WAF 규칙과 일치하는 요청은 WAF 로그에 기록됩니다.
동작 중인 WAF를 확인하기 위해 모드 설정을 **검색**에서 **방지**로 변경할 수 있습니다. **방지** 모드에서는 DRS(기본 규칙 집합)에 정의된 규칙과 일치하는 요청이 차단되고 WAF 로그에 기록됩니다.

 ![WAF 정책 모드 변경](../media/waf-front-door-create-portal/policy.png)

### <a name="custom-rules"></a>사용자 지정 규칙

**사용자 지정 규칙** 섹션에서 **사용자 지정 규칙 추가**를 선택하여 사용자 지정 규칙을 만들 수 있습니다. 그러면 사용자 지정 규칙 구성 페이지가 시작됩니다. 다음은 쿼리 문자열에 **blockme**가 포함될 경우 요청을 차단하는 사용자 지정 규칙을 구성하는 예입니다.

![WAF 정책 모드 변경](../media/waf-front-door-create-portal/customquerystring2.png)

### <a name="default-rule-set-drs"></a>DRS(기본 규칙 집합)

Azure 관리형 기본 규칙 집합은 기본적으로 사용하도록 설정되어 있습니다. 규칙 그룹 내에 있는 개별 규칙을 사용하지 않도록 설정하려면 규칙 번호 앞의 **확인란**을 선택하고, 위에 있는 탭에서 **사용 안 함**을 선택하세요. 규칙 집합 내에 있는 개별 규칙의 동작 유형을 변경하려면 규칙 번호 앞의 확인란을 선택한 다음, 위에 있는 **동작 변경** 탭을 선택하세요.

 ![WAF 규칙 집합 변경](../media/waf-front-door-create-portal/managed2.png)

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Azure 웹 애플리케이션 방화벽에 대해 자세히 알아보기](../overview.md)
> [Azure Front Door에 대해 자세히 알아보기](../../frontdoor/front-door-overview.md)