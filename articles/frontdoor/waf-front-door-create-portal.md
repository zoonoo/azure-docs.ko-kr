---
title: Azure Portal를 사용 하 여 Azure Front 문에 대 한 웹 응용 프로그램 방화벽 정책 만들기
titlesuffix: Azure web application firewall
description: Azure Portal를 사용 하 여 WAF (웹 응용 프로그램 방화벽) 정책을 만드는 방법에 대해 알아봅니다.
services: frontdoor
documentationcenter: na
author: KumudD
manager: twooley
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/31/2019
ms.author: kumud
ms.reviewer: tyao
ms.openlocfilehash: abaef0fb521d848134885a06591b0656c60c67e6
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/12/2019
ms.locfileid: "67846383"
---
# <a name="create-a-waf-policy-for-azure-front-door-by-using-the-azure-portal"></a>Azure Portal를 사용 하 여 Azure Front 문에 대 한 WAF 정책 만들기

이 문서에서는 기본 Azure WAF (웹 응용 프로그램 방화벽) 정책을 만들고 Azure Front 도어의 프런트 엔드 호스트에 적용 하는 방법을 설명 합니다.

## <a name="prerequisites"></a>전제 조건

[빠른 시작: Front Door 프로필 만들기](quickstart-create-front-door.md)에 설명된 지침에 따라 Front Door 프로필을 만듭니다. 

## <a name="create-a-waf-policy"></a>WAF 정책 만들기

먼저, 포털을 사용 하 여 관리 되는 기본 규칙 집합 (DRS)으로 기본 WAF 정책을 만듭니다. 

1. 화면 왼쪽 상단에서 **리소스 만들기**를 선택 하 > **waf**를 검색 > **웹 응용 프로그램 방화벽 (미리 보기)** > 선택 하 고 **만들기**를 선택 합니다.
2. **WAF 정책 만들기** 페이지의 **기본 사항** 탭에서 다음 정보를 입력 하거나 선택 하 고 나머지 설정에 대 한 기본값을 적용 한 다음 **검토 + 만들기**를 선택 합니다.

    | 설정                 | 값                                              |
    | ---                     | ---                                                |
    | 구독            |프런트 도어 구독 이름을 선택 합니다.|
    | 리소스 그룹          |프런트 도어 리소스 그룹 이름을 선택 합니다.|
    | 정책 이름             |WAF 정책의 고유한 이름을 입력 합니다.|

   ![WAF 정책 만들기](./media/waf-front-door-create-portal/basic.png)

3. **WAF 정책 만들기** 페이지의 **연결** 탭에서 **프런트 엔드 호스트 추가**를 선택 하 고 다음 설정을 입력 한 후 **추가**를 선택 합니다.

    | 설정                 | 값                                              |
    | ---                     | ---                                                |
    | 전방 도어              | 프런트 도어 프로필 이름을 선택 합니다.|
    | 프런트 엔드 호스트           | 프런트 도어 호스트의 이름을 선택 하 고 **추가**를 선택 합니다.|
    
    > [!NOTE]
    > 프런트 엔드 호스트가 WAF 정책에 연결 된 경우 회색으로 표시 됩니다. 먼저 연결 된 정책에서 프런트 엔드 호스트를 제거한 후 프런트 엔드 호스트를 새 WAF 정책에 다시 연결 해야 합니다.
1. **검토 + 만들기**를 선택한 다음 **만들기**를 선택 합니다.

## <a name="configure-waf-rules-optional"></a>WAF 규칙 구성 (선택 사항)

### <a name="change-mode"></a>모드 변경

WAF 정책을 만들 때 기본 WAF 정책은 **검색** 모드입니다. **검색** 모드에서는 waf가 모든 요청을 차단 하지 않고 waf 규칙에 일치 하는 요청이 waf 로그에 기록 됩니다.
작동 중인 WAF를 확인 하려면 모드 설정을 **검색** 에서 **방지**로 변경할 수 있습니다. **방지** 모드에서는 DRS (기본 규칙 집합)에 정의 된 규칙과 일치 하는 요청이 waf 로그에서 차단 되 고 기록 됩니다.

 ![WAF 정책 모드 변경](./media/waf-front-door-create-portal/policy.png)

### <a name="custom-rules"></a>사용자 지정 규칙

사용자 지정 **규칙 섹션에서** 사용자 **지정 규칙 추가** 를 선택 하 여 사용자 지정 규칙을 만들 수 있습니다. 그러면 사용자 지정 규칙 구성 페이지가 시작 됩니다. 다음은 쿼리 문자열이 **blockme**를 포함 하는 경우 요청을 차단 하는 사용자 지정 규칙을 구성 하는 예입니다.

![WAF 정책 모드 변경](./media/waf-front-door-create-portal/customquerystring2.png)

### <a name="default-rule-set-drs"></a>DRS (기본 규칙 집합)

Azure 관리 되는 기본 규칙 집합은 기본적으로 사용 하도록 설정 되어 있습니다. 규칙 그룹 내에서 개별 규칙을 사용 하지 않도록 설정 하려면 해당 규칙 그룹 내의 규칙을 확장 하 고, 규칙 번호 앞의 **확인란** 을 선택 하 고, 위의 탭에서 **사용 안 함** 을 선택 합니다. 규칙 집합 내에서 개별 규칙의 작업 유형을 변경 하려면 규칙 번호 앞의 확인란을 선택 하 고 위의 **변경 작업** 탭을 선택 합니다.

 ![WAF 규칙 집합 변경](./media/waf-front-door-create-portal/managed2.png)

## <a name="next-steps"></a>다음 단계

- [Azure 웹 애플리케이션 방화벽](waf-overview.md)에 대해 알아봅니다.
- [Azure Front 도어](front-door-overview.md)에 대해 자세히 알아보세요.
