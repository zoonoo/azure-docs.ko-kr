---
title: Azure DDoS 빠른 응답
description: 전문 지원에 대해 활성 공격에 DDoS 전문가를 참여 하는 방법에 대해 알아봅니다.
services: ddos-protection
documentationcenter: na
author: yitoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/28/2020
ms.author: yitoh
ms.openlocfilehash: 8e860bf47420f2b58c44df695da7761bcc2aa0ce
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100521784"
---
# <a name="azure-ddos-rapid-response"></a>Azure DDoS 빠른 응답

Azure DDoS Protection Standard 고객은 활성 액세스 중에, 공격 및 공격 후 분석 중에 공격 조사에 도움을 주는 DRR (DDoS 신속한 응답) 팀에 액세스할 수 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

- 이 자습서의 단계를 완료 하기 전에 먼저 [Azure DDoS Standard 보호 계획](manage-ddos-protection.md)을 만들어야 합니다.

## <a name="when-to-engage-drr"></a>DRR에 참여 하는 경우

다음의 경우에만 DRR을 사용할 수 있습니다. 

- DDoS 공격 중에는 보호 된 리소스의 성능이 심각 하 게 저하 되거나 리소스를 사용할 수 없는 경우에 발생 합니다. 
- 리소스가 DDoS 공격을 받고 있다고 생각하지만 DDoS Protection 서비스가 공격을 효과적으로 완화하지 않습니다.
- 네트워크 트래픽을 크게 증가시키는 바이럴 이벤트를 계획하고 있습니다.
- 비즈니스에 중요 한 영향을 주는 공격의 경우

## <a name="engage-drr-during-an-active-attack"></a>활성 공격 중 DRR 참여

1. 새 지원 요청을 만드는 동안 Azure Portal에서 기술으로 **문제 유형** 을 선택 합니다.
2. **서비스** 를 **DDOS 보호** 로 선택 합니다.
3. 리소스 드롭다운 메뉴에서 리소스를 선택 합니다. _DRR을 이용 하려면 DDoS Protection Standard에서 보호 되는 가상 네트워크에 연결 된 DDoS 계획을 선택 해야 합니다._

    ![리소스 선택](./media/ddos-rapid-response/choose-resource.png)

4. 다음 **문제** 페이지에서 **심각도** 가 중요 한 영향 및 **문제 유형** 을 ' 공격에서 '로 선택 합니다.

    ![PSeverity 및 문제 유형](./media/ddos-rapid-response/severity-and-problem-type.png)

5. 추가 정보를 완료 하 고 지원 요청을 제출 합니다.

DRR은 Azure Rapid Response 지원 모델을 따릅니다. 빠른 응답에 대 한 자세한 내용은 [지원 범위 및 응답성](https://azure.microsoft.com/en-us/support/plans/response/) 을 참조 하세요.

자세히 알아보려면 [DDoS Protection 표준 설명서](./ddos-protection-overview.md)를 참조 하세요.

## <a name="next-steps"></a>다음 단계

- [시뮬레이션을 통해 테스트](test-through-simulations.md)하는 방법을 알아봅니다.
- [DDoS protection 원격 분석을 보고 구성](telemetry.md)하는 방법에 대해 알아봅니다.
- [DDoS 진단 로깅을 보고 구성](diagnostic-logging.md)하는 방법에 대해 알아봅니다.
