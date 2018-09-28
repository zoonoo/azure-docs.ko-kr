---
title: Azure Application Gateway의 웹 응용 프로그램 방화벽 요청 크기 제한 및 제외 목록 - Azure Portal | Microsoft Docs
description: 이 문서에서는 Azure Portal을 사용하는 Application Gateway의 웹 응용 프로그램 방화벽 요청 크기 제한 및 제외 목록 구성에 대해 설명합니다.
documentationcenter: na
services: application-gateway
author: vhorne
manager: jpconnock
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.custom: ''
ms.workload: infrastructure-services
ms.date: 09/24/2018
ms.author: victorh
ms.openlocfilehash: 995e003422d5a94fe57174dc9733c870e4e003aa
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46965508"
---
# <a name="web-application-firewall-request-size-limits-and-exclusion-lists-public-preview"></a>웹 응용 프로그램 방화벽 요청 크기 제한 및 제외 목록(공개 미리 보기)

Azure Application Gateway WAF(웹 응용 프로그램 방화벽)는 웹 응용 프로그램을 보호합니다. 이 문서에서는 WAF 요청 크기 제한 및 제외 목록 구성에 대해 설명합니다.

> [!IMPORTANT]
> WAF 요청 크기 제한 및 제외 목록의 구성은 현재 공개 미리 보기로 제공됩니다. 이 미리 보기는 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure 미리 보기에 대한 보충 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="waf-request-size-limits"></a>WAF 요청 크기 제한
사용자는 웹 응용 프로그램 방화벽을 통해 상한과 하한 사이의 범위에서 요청 크기 제한을 구성할 수 있습니다. 사용 가능한 크기 제한 구성은 다음의 두 가지입니다.
- KB 단위로 지정되는 최대 요청 본문 크기 필드는 파일 업로드를 제외한 전체 요청 크기 제한을 제어합니다. 이 필드에는 1KB~128KB 사이의 값을 지정할 수 있습니다. 요청 본문 크기의 기본값은 128KB입니다.
- MB 단위로 지정되는 파일 업로드 제한 필드는 허용되는 최대 파일 업로드 크기를 제어합니다. 이 필드에는 1MB~500MB 사이의 값을 지정할 수 있습니다. 파일 업로드 제한의 기본값은 100MB입니다.

WAF는 요청 본문 검사를 설정하거나 해제할 수 있는 구성 가능한 노브도 제공합니다. 요청 본문 검사는 기본적으로 사용됩니다. 요청 본문 검사를 해제하면 WAF는 HTTP 메시지 본문의 내용을 평가하지 않습니다. 이러한 경우에도 헤더, 쿠키 및 URI에는 WAF 규칙이 계속 적용됩니다. 요청 본문 검사를 해제하는 경우에는 최대 요청 본문 크기 필드가 적용되지 않으며 설정할 수 없습니다. 요청 본문 검사를 해제하면 128KB보다 큰 메시지를 WAF로 전송할 수 있습니다. 그러나 메시지 본문에서 취약성을 검사하지는 않습니다.

## <a name="waf-exclusion-lists"></a>WAF 제외 목록

사용자는 WAF 제외 목록을 통해 WAF 평가에서 특정 요청 특성을 생략할 수 있습니다. 일반적인 예로는 인증 또는 암호 필드에 사용되는 Active Directory 삽입 토큰이 있습니다. 이러한 특성은 WAF 규칙에서 가양성을 트리거할 수 있는 특수 문자를 포함하는 경우가 많습니다. WAF 제외 목록에 추가된 특성은 구성된 활성 WAF 규칙에서 고려되지 않습니다. 제외 목록의 범위는 전역입니다.
요청 헤더, 요청 쿠키 또는 요청 쿼리 문자열 인수를 WAF 제외 목록에 추가할 수 있습니다. 정확히 일치하는 요청 헤더, 쿠키 또는 쿼리 문자열 특성을 지정할 수도 있고 필요한 경우 부분 일치를 지정할 수도 있습니다. 지원되는 일치 기준 연산자는 다음과 같습니다. 
- **equals**: 정확한 일치에 사용됩니다. 예를 들어 **bearerToken**”**라는 헤더를 선택하려면 선택기가 **bearerToken**로 설정된 equals 연산자를 사용합니다. 
- **starts with**: 지정된 선택기 값으로 시작하는 모든 필드를 일치 항목으로 반환합니다. 
- **ends with**: 지정된 선택기 값으로 끝나는 모든 요청 필드를 일치 항목으로 반환합니다. 
- **contains**: 지정된 선택기 값을 포함하는 모든 요청 필드를 일치 항목으로 반환합니다.

어떤 경우든 일치에서는 대/소문자를 구분하지 않으며, 정규식은 선택기로 사용할 수 없습니다.

## <a name="next-steps"></a>다음 단계

WAF 설정을 구성한 후에는 WAF 로그 확인 방법을 살펴볼 수 있습니다. 자세한 내용은 [Application Gateway 진단](application-gateway-diagnostics.md#diagnostic-logging)을 참조하세요.