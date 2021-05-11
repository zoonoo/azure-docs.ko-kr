---
title: Azure DDoS 빠른 응답
description: 활성 공격 도중 DDoS 전문가를 참여시켜 전문 지원을 받는 방법을 알아봅니다.
services: ddos-protection
documentationcenter: na
author: aletheatoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/28/2020
ms.author: yitoh
ms.openlocfilehash: f551f40a9bd70c4a3b54c1d2756cfb94caf79590
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/08/2021
ms.locfileid: "107103065"
---
# <a name="azure-ddos-rapid-response"></a>Azure DDoS 빠른 응답

Azure DDoS Protection Standard 고객은 활성 액세스 상태에서 공격 중 조사 및 공격 후 분석에 도움을 주는 DRR(DDoS 빠른 응답) 팀에 액세스할 수 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

- 이 자습서의 단계를 완료하려면 먼저 [Azure DDoS Standard 보호 계획](manage-ddos-protection.md)을 만들어야 합니다.

## <a name="when-to-engage-drr"></a>DRR을 참여시키는 경우

다음의 경우에만 DRR을 참여시킬 수 있습니다. 

- DDoS 공격이 진행되는 동안 보호되는 리소스의 성능이 심각하게 저하되거나 리소스를 사용할 수 없게 되는 경우. 
- 리소스가 DDoS 공격을 받고 있다고 생각하지만 DDoS Protection 서비스가 공격을 효과적으로 완화하지 않습니다.
- 네트워크 트래픽을 크게 증가시키는 바이럴 이벤트를 계획하고 있습니다.
- 비즈니스에 중대한 영향을 미치는 공격의 경우.

## <a name="engage-drr-during-an-active-attack"></a>활성 공격 중 DRR 참여

1. 새 지원 요청을 만드는 동안 Azure Portal에서 **문제 유형** 을 기술로 선택합니다.
2. **서비스** 를 **DDOS 보호** 로 선택합니다.
3. 리소스 드롭다운 메뉴에서 리소스를 선택합니다. DRR을 참여시키려면 DDoS Protection Standard에서 보호되는 가상 네트워크에 연결된 DDoS 플랜을 선택해야 합니다.

    ![리소스 선택](./media/ddos-rapid-response/choose-resource.png)

4. **문제** 페이지에서 **심각도** 를 A - 중대한 영향으로 선택하고 **문제 유형** ‘공격 받고 있음’을 선택합니다.

    ![PSeverity 및 문제 유형](./media/ddos-rapid-response/severity-and-problem-type.png)

5. 추가 세부 정보를 완료하고 지원 요청을 제출합니다.

DRR은 Azure Rapid Response 지원 모델을 따릅니다. Rapid Response 관련 추가 정보는 [지원 범위 및 응답성](https://azure.microsoft.com/en-us/support/plans/response/)을 참조하세요.

자세히 알아보려면 [DDoS Protection Standard 설명서](./ddos-protection-overview.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

- [시뮬레이션을 통한 테스트](test-through-simulations.md) 방법을 알아봅니다.
- [DDoS 보호 원격 분석을 보고 구성](telemetry.md)하는 방법을 알아봅니다.
- [DDoS 진단 로깅을 보고 구성](diagnostic-logging.md)하는 방법을 알아봅니다.
