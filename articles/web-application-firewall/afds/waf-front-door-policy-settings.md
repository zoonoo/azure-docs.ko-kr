---
title: Azure Front 도어를 사용 하는 웹 응용 프로그램 방화벽에 대 한 정책 설정
description: WAF (웹 응용 프로그램 방화벽)에 대해 알아봅니다.
author: vhorne
ms.service: web-application-firewall
ms.topic: article
services: web-application-firewall
ms.date: 08/21/2019
ms.author: victorh
ms.openlocfilehash: 08b21ccd7f7958f00546583f680ecb8cde4a20c8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "75932610"
---
# <a name="policy-settings-for-web-application-firewall-on-azure-front-door"></a>Azure 전면 도어의 웹 응용 프로그램 방화벽에 대 한 정책 설정

WAF (웹 응용 프로그램 방화벽) 정책을 사용 하면 사용자 지정 및 관리 되는 규칙 집합을 사용 하 여 웹 응용 프로그램에 대 한 액세스를 제어할 수 있습니다. WAF 정책 이름은 고유 해야 합니다. 기존 이름을 사용 하려고 하면 유효성 검사 오류가 표시 됩니다. 이 문서에 설명 된 대로 해당 정책에 대해 지정 된 모든 규칙에 적용 되는 여러 정책 수준 설정이 있습니다.

## <a name="waf-state"></a>WAF 상태

전면 도어에 대 한 WAF 정책은 다음 두 상태 중 하나일 수 있습니다.
- **사용:** 정책을 사용 하도록 설정 하면 WAF는 들어오는 요청을 적극적으로 검사 하 고 규칙 정의에 따라 해당 하는 작업을 수행 합니다.
- **사용 안 함:** -정책을 사용 하지 않도록 설정 하면 waf 검사가 일시 중지 됩니다. 들어오는 요청은 WAF를 우회 하 고 전면 도어 라우팅을 기반으로 백 엔드에 전송 됩니다.

## <a name="waf-mode"></a>WAF 모드

WAF 정책은 다음 두 가지 모드에서 실행되도록 구성할 수 있습니다.

- **검색 모드** 검색 모드에서 실행 하는 경우 WAF는 모니터를 제외한 다른 작업을 수행 하지 않으며 요청 및 일치 하는 WAF 규칙을 WAF 로그에 기록 합니다. 프런트 도어에 대 한 로깅 진단을 켭니다 (포털을 사용 하는 경우 Azure Portal의 **진단** 섹션으로 이동 하 여이를 수행할 수 있음).

- **방지 모드** 방지 모드에서 실행 되도록 구성 된 경우 WAF는 요청이 규칙과 일치 하는 경우 지정 된 작업을 수행 합니다. 일치하는 모든 요청은 WAF 로그에도 기록됩니다.

## <a name="waf-response-for-blocked-requests"></a>차단 된 요청에 대 한 WAF 응답

기본적으로 WAF는 일치 하는 규칙으로 인해 요청을 차단할 때- **요청이 차단** 됨 메시지를 포함 하는 403 상태 코드를 반환 합니다. 로깅에 대 한 참조 문자열도 반환 됩니다.

요청이 WAF에 의해 차단 되는 경우 사용자 지정 응답 상태 코드 및 응답 메시지를 정의할 수 있습니다. 지원 되는 사용자 지정 상태 코드는 다음과 같습니다.

- 200 정상
- 403 사용할 수 없음
- 405 메서드를 사용할 수 없습니다.
- 406 허용 되지 않음
- 429 요청 수가 너무 많음

사용자 지정 응답 상태 코드 및 응답 메시지는 정책 수준 설정입니다. 구성 된 모든 차단 된 요청은 동일한 사용자 지정 응답 상태 및 응답 메시지를 가져옵니다.

## <a name="uri-for-redirect-action"></a>리디렉션 작업에 대 한 URI

WAF 정책에 포함 된 규칙에 대해 **리디렉션** 작업을 선택 하는 경우 요청을 리디렉션할 URI를 정의 해야 합니다. 이 리디렉션 URI는 유효한 HTTP (S) 사이트 여야 하 고 일단 구성 되 면 "리디렉션" 작업과 일치 하는 모든 요청은 지정 된 사이트로 리디렉션됩니다.


## <a name="next-steps"></a>다음 단계
- WAF [사용자 지정 응답](waf-front-door-configure-custom-response-code.md) 을 정의 하는 방법 알아보기
