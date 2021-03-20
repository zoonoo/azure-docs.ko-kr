---
title: Azure 센티널의 automation 소개 | Microsoft Docs
description: 이 문서에서는 Azure 센티널의 보안 오케스트레이션, 자동화 및 응답 (대화 충성도) 기능을 소개 하 고 대화 충성도 구성 요소 (Automation 규칙 및 플레이 북)에 대해 설명 합니다.
services: sentinel
cloud: na
documentationcenter: na
author: yelevin
manager: rkarlin
ms.assetid: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/14/2021
ms.author: yelevin
ms.openlocfilehash: 54d7c997ce17c927a692e84f6094e3a08f707af7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104609801"
---
# <a name="security-orchestration-automation-and-response-soar-in-azure-sentinel"></a>Azure 센티널의 보안 오케스트레이션, 자동화 및 응답 (대화 충성도)

이 문서에서는 Azure 센티널의 보안 오케스트레이션, 자동화 및 응답 (대화 충성도) 기능에 대해 설명 하 고 보안 위협에 대 한 응답으로 Automation 규칙과 플레이 북를 사용 하는 방법을 보여 줍니다 .이를 통해 SOC의 효율성을 높이고 시간과 리소스를 절약할 수 있습니다.

## <a name="azure-sentinel-as-a-soar-solution"></a>대화 충성도 솔루션인 Azure 센티널

### <a name="the-problem"></a>문제

SIEM/SOC 팀은 일반적으로 정기적으로 보안 경고 및 인시던트를 사용 하 여 사용 가능한 인력이 다양. 이는 대부분의 경고가 무시 되 고 많은 인시던트가 조사 되지 않는 상황에서 대부분의 경우를 발생 시킬 수 있습니다 .이는 조직이 발견 되지 않는 공격에 취약 한 상태로 유지 됩니다.

### <a name="the-solution"></a>솔루션

SIEM (보안 정보 및 이벤트 관리) 시스템 뿐만 아니라 Azure 센티널도 보안 오케스트레이션, 자동화 및 응답 (대화 충성도)을 위한 플랫폼입니다. 주요 목적 중 하나는 보안 운영 센터 및 직원 (SOC/SecOps)의 책임이 되는 되풀이 및 예측 가능한 보강, 대응 및 수정 작업을 자동화 하 고, 고급 위협에 대 한 심층 조사 및 심층 조사를 위한 시간과 리소스를 확보 하는 것입니다. Automation은 인시던트 처리 및 응답의 자동화를 중앙에서 관리 하는 automation 규칙부터, 위협 대응 작업에 강력 하 고 유연한 고급 자동화를 제공 하기 위해 미리 결정 된 작업 시퀀스를 실행 하는 플레이 북까지 다양 한 형태의 Azure 센티널을 사용 합니다.

## <a name="automation-rules"></a>Automation 규칙

Automation 규칙은 Azure 센티널의 새로운 개념입니다. 이 기능을 통해 사용자는 인시던트 처리의 자동화를 중앙에서 관리할 수 있습니다. 이전 처럼 경고가 아닌 인시던트에 플레이 북을 할당 하는 것 외에도, 자동화 규칙을 사용 하 여 한 번에 여러 분석 규칙에 대 한 응답을 자동화 하 고, 플레이 북이 없어도 자동으로 인시던트를 태그 지정, 할당 또는 종결 하 고, 실행 되는 작업의 순서를 제어할 수 있습니다. Automation 규칙은 Azure 센티널에서 automation 사용을 간소화 하 고 인시던트 오케스트레이션 프로세스에 대 한 복잡 한 워크플로를 단순화할 수 있습니다.

이에 [대 한 자세한 내용은 자동화 규칙의 전체 설명을](automate-incident-handling-with-automation-rules.md)확인 하세요.

> [!IMPORTANT]
>
> - **Automation 규칙** 기능은 현재 **미리 보기** 상태입니다. 베타, 미리 보기 또는 아직 일반 공급으로 출시 되지 않은 Azure 기능에 적용 되는 추가 약관은 [Microsoft Azure 미리 보기에](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) 대 한 추가 사용 약관을 참조 하세요.

## <a name="playbooks"></a>플레이북

플레이 북는 Azure 센티널에서 루틴으로 실행할 수 있는 응답 및 수정 작업 및 논리의 모음입니다. 플레이 북는 위협 응답을 자동화 하 고 오케스트레이션 하는 데 도움이 될 수 있으며, 내부 및 외부의 다른 시스템과 통합할 수 있으며, 각각 분석 규칙 또는 자동화 규칙에 의해 트리거되는 경우 특정 경고나 인시던트에 따라 자동으로 실행 되도록 설정할 수 있습니다. 인시던트 페이지에서 경고에 대 한 응답으로 요청 시 수동으로 실행할 수도 있습니다.

Azure 센티널의 플레이 북은 기업 전체 시스템에서 작업 및 워크플로를 예약, 자동화 및 오케스트레이션 하는 데 도움이 되는 클라우드 서비스인 [Azure Logic Apps](../logic-apps/logic-apps-overview.md)에서 빌드된 워크플로를 기반으로 합니다. 즉, 플레이 북은 Logic Apps의 통합 및 오케스트레이션 기능과 사용 하기 쉬운 디자인 도구, 그리고 계층 1 Azure 서비스의 확장성, 안정성 및 서비스 수준에 대 한 모든 기능과 사용자 지정 가능성을 활용할 수 있습니다.

이에 대 한 자세한 내용은 플레이 [북의 전체 설명을](automate-responses-with-playbooks.md)참조 하세요.

## <a name="next-steps"></a>다음 단계

이 문서에서는 Azure 센티널에서 자동화를 사용 하 여 SOC가 보다 효과적이 고 효율적으로 작동 하는 방법을 배웠습니다.

- 인시던트 처리 자동화에 대 한 자세한 내용은 [Azure 센티널에서 인시던트 처리 자동화](automate-incident-handling-with-automation-rules.md)를 참조 하세요.
- 고급 자동화 옵션에 대 한 자세한 내용은 [Azure 센티널에서 플레이 북을 사용 하 여 위협 대응 자동화](automate-responses-with-playbooks.md)를 참조 하세요.
- Automation 규칙 및 플레이 북 구현에 대 한 도움말은 [자습서: 플레이 북를 사용 하 여 Azure 센티널에서 위협 응답 자동화](tutorial-respond-threats-playbook.md)를 참조 하세요.
