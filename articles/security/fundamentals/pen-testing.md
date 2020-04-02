---
title: 펜 테스트 | Microsoft Docs
description: 이 문서는 침투 테스트 (pentest) 프로세스의 개요와 Azure 인프라에서 실행 중인 앱에 대해 pentest를 수행하는 방법을 제공합니다.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 695d918c-a9ac-4eba-8692-af4526734ccc
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/13/2018
ms.author: terrylan
ms.openlocfilehash: 413ec8b121838a4ffac4119421ec3266e141618b
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/02/2020
ms.locfileid: "80549261"
---
# <a name="penetration-testing"></a>침투 테스트
애플리케이션 테스트 및 배포에 Azure를 사용하면 좋은 점 중 하나는 신속하게 환경을 얻을 수 있다는 점입니다. 자신의 온-프레미스 하드웨어 요청, 구입, “래킹과 스태킹(racking and stacking)”에 대해 걱정할 필요가 없습니다.

이것은 좋은 일입니다 – 그렇지만 여전히 일반적인 보안 실사는 수행해야 합니다. Azure에서 배포하는 응용 프로그램을 침투 테스트하는 것 중 하나가 수행하려는 경우입니다.

Microsoft가 [Azure 환경에서의 침투 테스트](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)를 수행하는 것에 관해 어쩌면 이미 알고 계실 것입니다. 이는 Azure 개선에 많은 도움이 됩니다.

우리는 당신을 위해 응용 프로그램을 침투 테스트하지 않습니다, 그러나 우리는 당신이 원하는 것입니다 그리고 자신의 응용 프로그램에서 테스트를 수행해야한다는 것을 이해합니다. 응용 프로그램의 보안을 강화할 때 전체 Azure 생태계를 보다 안전하게 만드는 데 도움이 되므로 좋은 일입니다.

2017년 6월 15일부터 Microsoft는 더 이상 Azure 리소스에 대한 침투 테스트를 수행하기 위해 사전 승인을 요구하지 않습니다. Microsoft Azure에 대해 예정된 보안 테스트를 공식적으로 문서화하려는 고객은 [Azure 서비스 보안 테스트 알림 양식](https://portal.msrc.microsoft.com/en-us/engage/pentest)을 채우는 것이 좋습니다. 이 프로세스는 다른 Microsoft 클라우드 서비스에 적용할 수 없고 Microsoft Azure에만 관련됩니다.

>[!IMPORTANT]
>보안 테스트 작업을 수행한다는 것을 Microsoft에 알리는 작업은 더 이상 필수가 아니지만 고객은 여전히 [Microsoft 클라우드 통합 보안 테스트 지침서](https://technet.microsoft.com/mt784683)를 준수해야 합니다.

여러분이 수행할 수 있는 표준 테스트는 다음과 같습니다.

* [OWASP(Open Web Application Security Project) 취약점 상위 10개](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project)
* [퍼지 테스트](https://cloudblogs.microsoft.com/microsoftsecure/2007/09/20/fuzz-testing-at-microsoft-and-the-triage-process/)
* [포트 검색](https://en.wikipedia.org/wiki/Port_scanner)

여러분이 수행할 수 없는 한가지 유형의 테스트는 모든 종류의 [서비스 거부 (DoS)](https://en.wikipedia.org/wiki/Denial-of-service_attack) 공격입니다. 이는 DoS 공격 자체를 시작하거나, 모든 종류의 DoS 공격을 결정, 시연 또는 시뮬레이션할 수도 있는 관련 테스트를 수행하는 것을 포함합니다.

## <a name="next-steps"></a>다음 단계

- Microsoft Azure에서 호스팅되는 응용 프로그램에 대해 예정된 보급 테스트를 공식적으로 문서화하려면 [참여 테스트 규칙으로](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=2) 이동하여 테스트 알림 양식을 작성하십시오.
