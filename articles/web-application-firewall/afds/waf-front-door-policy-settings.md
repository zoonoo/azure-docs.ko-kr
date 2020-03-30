---
title: Azure 정문이 있는 웹 응용 프로그램 방화벽에 대한 정책 설정
description: WAF(웹 응용 프로그램 방화벽)에 대해 알아봅니다.
author: vhorne
ms.service: web-application-firewall
ms.topic: article
services: web-application-firewall
ms.date: 08/21/2019
ms.author: victorh
ms.openlocfilehash: 08b21ccd7f7958f00546583f680ecb8cde4a20c8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75932610"
---
# <a name="policy-settings-for-web-application-firewall-on-azure-front-door"></a>Azure 정문에서 웹 응용 프로그램 방화벽에 대한 정책 설정

WAF(웹 응용 프로그램 방화벽) 정책을 사용하면 사용자 지정 및 관리되는 규칙 집합을 통해 웹 응용 프로그램에 대한 액세스를 제어할 수 있습니다. WAF 정책 이름은 고유해야 합니다. 기존 이름을 사용하려고 하면 유효성 검사 오류가 표시됩니다. 이 문서에서 설명한 대로 해당 정책에 대해 지정된 모든 규칙에 적용되는 여러 정책 수준 설정이 있습니다.

## <a name="waf-state"></a>WAF 주

정문에 대한 WAF 정책은 다음 두 상태 중 하나에 있을 수 있습니다.
- **사용 설정:** 정책이 활성화되면 WAF는 들어오는 요청을 적극적으로 검사하고 규칙 정의에 따라 해당 작업을 수행합니다.
- **사용 안 함:** - 정책이 비활성화되면 WAF 검사가 일시 중지됩니다. 들어오는 요청은 WAF를 우회하고 프런트 도어 라우팅을 기반으로 백 엔드로 전송됩니다.

## <a name="waf-mode"></a>WAF 모드

WAF 정책은 다음 두 가지 모드에서 실행되도록 구성할 수 있습니다.

- **감지 모드** 검색 모드에서 실행될 때 WAF는 요청 및 일치하는 WAF 규칙을 WAF 로그에 모니터링하고 로그하는 것 이외의 작업을 수행하지 않습니다. Front Door에 대한 로깅 진단을 켜십시오(포털을 사용하는 경우 Azure 포털의 **진단** 섹션으로 이동하여 이 작업을 수행할 수 있음).

- **예방 모드** 방지 모드에서 실행되도록 구성된 경우 요청이 규칙과 일치하는 경우 WAF는 지정된 작업을 수행합니다. 일치하는 모든 요청은 WAF 로그에도 기록됩니다.

## <a name="waf-response-for-blocked-requests"></a>차단된 요청에 대한 WAF 응답

기본적으로 WAF가 일치하는 규칙으로 인해 요청을 차단하면 403 상태 코드가 함께 **반환됩니다.** 로깅을 위해 참조 문자열도 반환됩니다.

WAF에 의해 요청이 차단될 때 사용자 지정 응답 상태 코드 및 응답 메시지를 정의할 수 있습니다. 다음 사용자 지정 상태 코드가 지원됩니다.

- 200 OK
- 403 금지됨
- 405 메서드가 허용되지 않습니다.
- 406 허용되지 않음
- 429 요청이 너무 많습니다.

사용자 지정 응답 상태 코드 및 응답 메시지는 정책 수준 설정입니다. 구성되면 차단된 모든 요청은 동일한 사용자 지정 응답 상태 및 응답 메시지를 받습니다.

## <a name="uri-for-redirect-action"></a>리디렉션 작업에 대한 URI

WAF 정책에 포함된 규칙에 대해 **리디렉션** 작업이 선택된 경우 요청을 리디렉션할 URI를 정의해야 합니다. 이 리디렉션 URI는 유효한 HTTP(S) 사이트여야 하며 일단 구성되면 "REDIRECT" 작업과 일치하는 모든 요청이 지정된 사이트로 리디렉션됩니다.


## <a name="next-steps"></a>다음 단계
- WAF [사용자 지정 응답을](waf-front-door-configure-custom-response-code.md) 정의하는 방법 알아보기
