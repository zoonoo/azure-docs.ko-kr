---
title: Azure Front Door로 Web Application Firewall의 정책 설정
description: Azure WAF(Web Application Firewall)를 알아봅니다.
author: vhorne
ms.service: web-application-firewall
ms.topic: article
services: web-application-firewall
ms.date: 08/21/2019
ms.author: victorh
ms.openlocfilehash: 08b21ccd7f7958f00546583f680ecb8cde4a20c8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "75932610"
---
# <a name="policy-settings-for-web-application-firewall-on-azure-front-door"></a>Azure Front Door에서 Web Application Firewall의 정책 설정

WAF(Web Application Firewall) 정책을 사용하면 사용자 지정 및 관리 규칙 집합으로 웹 애플리케이션에 대한 액세스를 제어할 수 있습니다. WAF 정책 이름은 고유해야 합니다. 기존 이름을 사용하려고 하면 유효성 검사 오류가 반환됩니다. 이 문서에 설명된 대로 해당 정책에 대해 지정된 모든 규칙에는 여러 정책 수준 설정이 적용됩니다.

## <a name="waf-state"></a>WAF 상태

Front Door에 대한 WAF 정책은 다음 두 상태 중 하나일 수 있습니다.
- **사용:** 정책을 사용하도록 설정하면 WAF는 들어오는 요청을 적극적으로 검사하고 규칙 정의에 따라 해당하는 작업을 수행합니다.
- **사용 안함:** 정책을 사용하지 않도록 설정하면 WAF 검사가 일시 중지됩니다. 들어오는 요청은 WAF를 우회하고 Front Door 라우팅을 기반으로 백 엔드에 전송됩니다.

## <a name="waf-mode"></a>WAF 모드

WAF 정책은 다음 두 가지 모드에서 실행되도록 구성할 수 있습니다.

- **검색 모드**: 검색 모드에서 실행하는 경우 WAF는 모니터링 이외의 다른 작업을 수행하지 않고, 요청 및 일치하는 WAF 규칙을 WAF 로그에 기록합니다. Front Door의 로깅 진단을 켭니다(Azure Portal을 사용하는 경우 Azure Portal의 **진단** 섹션으로 이동).

- **방지 모드**: 방지 모드에서 실행하도록 구성할 경우, 요청이 규칙과 일치하면 WAF가 지정된 작업을 수행합니다. 일치하는 모든 요청은 WAF 로그에도 기록됩니다.

## <a name="waf-response-for-blocked-requests"></a>블록된 요청에 대한 WAF 응답

기본적으로 WAF가 일치하는 규칙으로 인해 요청을 블록할 경우, **요청이 블록됨** 이라는 메시지와 함께 403 상태 코드를 반환합니다. 또한 로깅에 대한 참조 문자열이 반환됩니다.

WAF에서 요청을 블록하는 경우 사용자 지정 응답 상태 코드 및 응답 메시지를 정의할 수 있습니다. 지원되는 사용자 지정 상태 코드는 다음과 같습니다.

- 200    확인
- 403    사용할 수 없음
- 405    메서드를 사용할 수 없음
- 406    허용되지 않음
- 429    요청이 너무 많음

사용자 지정 응답 상태 코드 및 응답 메시지는 정책 수준 설정입니다. 구성을 마치면 모든 블록된 요청에는 동일한 사용자 지정 응답 상태 및 응답 메시지가 표시됩니다.

## <a name="uri-for-redirect-action"></a>리디렉션 작업에 대한 URI

WAF 정책에 포함된 규칙에 대해 **REDIRECT** 작업을 선택하는 경우 요청을 리디렉션할 URI를 정의해야 합니다. 이 리디렉션 URI는 유효한 HTTP(S) 사이트여야 하며 구성을 마치면 “REDIRECT” 작업이 선택된 규칙과 일치하는 모든 요청이 지정된 사이트로 리디렉션됩니다.


## <a name="next-steps"></a>다음 단계
- WAF [사용자 지정 응답](waf-front-door-configure-custom-response-code.md)을 정의하는 방법 알아보기
