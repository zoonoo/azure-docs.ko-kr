---
title: 펜 테스트 | Microsoft Docs
description: 이 문서는 침투 테스트 (pentest) 프로세스의 개요와 Azure 인프라에서 실행 중인 앱에 대해 pentest를 수행하는 방법을 제공합니다.
services: security
documentationcenter: na
author: TerryLanfear
manager: mbaldwin
editor: TomSh
ms.assetid: 695d918c-a9ac-4eba-8692-af4526734ccc
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/13/2018
ms.author: barclayn
ms.openlocfilehash: 4096cf3a44b7c32ed94fdd2ef5dcbad9db08a386
ms.sourcegitcommit: 0fcd6e1d03e1df505cf6cb9e6069dc674e1de0be
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2018
ms.locfileid: "42142170"
---
# <a name="pen-testing"></a>펜 테스트
애플리케이션 테스트 및 배포에 Azure를 사용하면 좋은 점 중 하나는 신속하게 환경을 얻을 수 있다는 점입니다.  자신의 온-프레미스 하드웨어 요청, 구입, “래킹과 스태킹(racking and stacking)”에 대해 걱정할 필요가 없습니다.

이것은 좋은 일입니다 – 그렇지만 여전히 일반적인 보안 실사는 수행해야 합니다. 수행해야 할 일 중 하나는 Azure에서 배포하는 애플리케이션에 대한 침투 테스트를 하는 것입니다.

Microsoft가 [Azure 환경에서의 침투 테스트](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)를 수행하는 것에 관해 어쩌면 이미 알고 계실 것입니다. 이는 Azure 개선에 많은 도움이 됩니다.

우리는 여러분을 위해 애플리케이션을 펜 테스트하지는 않지만, 여러분이 자신의 애플리케이션에 펜 테스트를 수행하기를 원하며 그럴 필요가 있다는 것을 이해합니다. 애플리케이션의 보안을 강화할 때 Azure 전체 에코시스템을 보다 안전하게 보호하는데 도움이 되기 때문에 그렇게 하는 것이 좋습니다.

그렇다면 어떻게 해야 할까요?

Microsoft에는 2017년 6월 15일부터 Azure 리소스에 대한 보안 테스트를 수행하기 위해 더 이상 사전 승인이 필요하지 않습니다. Microsoft Azure에 대해 예정된 보안 테스트를 공식적으로 문서화하려는 고객은 [Azure 서비스 보안 테스트 알림 양식](https://portal.msrc.microsoft.com/en-us/engage/pentest)을 채우는 것이 좋습니다. 이 프로세스는 다른 Microsoft 클라우드 서비스에 적용할 수 없고 Microsoft Azure에만 관련됩니다.

>[!IMPORTANT]
>보안 테스트 작업을 수행한다는 것을 Microsoft에 알리는 작업은 더 이상 필수가 아니지만 고객은 여전히 [Microsoft 클라우드 통합 보안 테스트 지침서](https://technet.microsoft.com/mt784683)를 준수해야 합니다.

여러분이 수행할 수 있는 표준 테스트는 다음과 같습니다.

*  [OWASP(Open Web Application Security Project) 취약점 상위 10개](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project)
* [퍼지 테스트](https://blogs.microsoft.com/cybertrust/2007/09/20/fuzz-testing-at-microsoft-and-the-triage-process/) 
* [포트 검색](https://en.wikipedia.org/wiki/Port_scanner) 

여러분이 수행할 수 없는 한가지 유형의 테스트는 모든 종류의 [서비스 거부 (DoS)](https://en.wikipedia.org/wiki/Denial-of-service_attack) 공격입니다. 이는 DoS 공격 자체를 시작하거나, 모든 종류의 DoS 공격을 결정, 시연 또는 시뮬레이션할 수도 있는 관련 테스트를 수행하는 것을 포함합니다.

## <a name="next-steps"></a>다음 단계

- Microsoft Azure에서 호스팅된 애플리케이션의 펜 테스트를 시작할 준비가 되셨습니까? 그렇다면 [참여의 침투 테스트 규칙](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=2)으로 이동하여 테스트 알림 양식을 작성합니다.
