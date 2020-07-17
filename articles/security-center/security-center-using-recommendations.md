---
title: Azure Security Center 권장 사항을 사용하여 보안 강화 | Microsoft Docs
description: " Azure Security Center에서 보안 정책 및 권장 사항을 사용하여 보안 공격을 완화할 수 있는 방법을 알아봅니다. "
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: ''
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/22/2019
ms.author: memildin
ms.openlocfilehash: 8039be7b69444cc32e763e9a1fb074e7dda4a5ba
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84783234"
---
# <a name="use-azure-security-center-recommendations-to-enhance-security"></a>Azure Security Center 권장 사항을 사용하여 보안 강화
보안 정책을 구성한 다음 Azure Security Center에서 제공한 권장 사항을 구현하여 중요한 보안 이벤트의 가능성을 줄일 수 있습니다. 이 문서에서는 Security Center에서 보안 정책 및 권장 사항을 사용하여 보안 공격을 완화할 수 있는 방법을 보여 줍니다. 

Security Center는 자동 검색을 자동으로 실행 하 여 Azure 리소스의 보안 상태를 분석 합니다. Security Center는 잠재적 보안 취약성이 확인되면 필요한 보안 컨트롤을 구성하는 과정을 안내하는 권장 사항을 만듭니다. Security Center 24 시간 이내에 권장 사항을 업데이트 합니다. 단, 다음과 같은 예외가 있습니다.

- 운영 체제 보안 구성 권장 사항은 48 시간 이내에 업데이트 됩니다.
- Endpoint Protection 문제 권장 사항은 8 시간 이내에 업데이트 됩니다.

## <a name="scenario"></a>시나리오
이 시나리오에서는 Security Center 권장 사항을 모니터링하고 조치를 취하여 보안 인시던트가 발생할 가능성을 줄일 수 있도록 Security Center를 사용하는 방법을 보여 줍니다. 시나리오는 Security Center [계획 및 운영 가이드](security-center-planning-and-operations-guide.md#security-roles-and-access-controls)에서 제공된 가상의 회사, Contoso 및 역할을 사용합니다. 이 시나리오에서는 다음 가상 사용자의 역할을 중점적으로 살펴봅니다.

![시나리오 역할](./media/security-center-using-recommendations/scenario-roles.png)

Contoso는 최근 해당 온-프레미스 리소스의 일부를 Azure로 마이그레이션했습니다. Contoso는 클라우드에서 리소스를 보호하고 해당 리소스의 취약성을 줄여줍니다.

## <a name="use-azure-security-center"></a>Azure Security Center 사용
Contoso의 IT 보안 부서에서 일하는 David은 보안 취약성을 감지하고 방지하기 위해 이미 Azure Security Center의 Contoso의 구독에서 Security Center를 온보딩하도록 선택했습니다. 

Security Center는 Contoso Azure 리소스의 보안 상태를 자동으로 분석하고 기본 보안 정책을 적용합니다. Security Center가 잠재적인 보안 취약점을 식별하는 경우 보안 정책에서 설정된 컨트롤에 따라 **권장 사항**을 만듭니다. 

David는 모든 구독에서 Azure 보안 표준 계층을 실행 하 여 사용 가능한 전체 권장 사항 및 보안 기능을 가져옵니다. 또한 Jeff는 [Windows](quick-onboard-windows-computer.md) 및 [Linux](quick-onboard-linux-computer.md) 서버에서 Security Center의 하이브리드 지원을 활용할 수 있도록 아직 클라우드로 마이그레이션되지 않은 기존의 모든 온-프레미스 서버를 보드 합니다.

Jeff는 클라우드 워크로드 소유자입니다. Jeff는 Contoso의 보안 정책에 따라 보안 제어를 적용하는 일을 담당합니다. 

Jeff는 다음 작업을 수행합니다.

- Security Center에서 제공한 보안 권장 사항 모니터링
- 보안 권장 사항을 평가 하 고 권장 사항을 적용 하거나 해제 해야 하는지 결정 합니다.
- 보안 권장 사항 적용

### <a name="remediate-threats-using-recommendations"></a>권장 사항을 사용하여 위협 해결
매일 모니터링 작업의 일부로 Jeff는 Azure에 로그인 하 고 Security Center를 엽니다. 

1. Jeff는 워크 로드의 구독을 선택 합니다.

2. Jeff는 **보안 점수** 를 확인 하 여 구독 보안을 전체적으로 파악 하 고 점수가 548 인지 확인 합니다.

3. Jeff는 먼저 처리할 권장 사항을 결정해야 합니다. 따라서 Jeff는 보안 점수를 클릭 하 고 [보안 점수](secure-score-security-controls.md)를 개선 하는 정도에 따라 권장 사항을 처리 하기 시작 합니다.

4. Jeff에 많은 VM 및 서버에 연결되어 있으므로 **Compute 및 앱**에 집중하기로 결정했습니다.

5. Jeff가 **Compute 및 apps**를 클릭 하면 권장 사항 목록이 표시 되 고 보안 점수 영향에 따라 처리 됩니다.

6. Jeff에는 다양 한 인터넷 연결 Vm이 있으며 해당 포트가 노출 되기 때문에 공격자가 서버를 제어할 수 있습니다. 따라서 Jeff는 [**JUST-IN-TIME VM 액세스**](security-center-just-in-time.md)를 사용 하도록 선택 합니다.

Jeff는 계속해서 높은 우선 순위 및 중간 우선 순위 권장 사항을 살펴보면서 구현할지 결정했습니다. 각 권장 사항에 대해 Jeff는 Security Center에서 제공 하는 세부 정보를 확인 하 여 영향을 받는 리소스, 보안 점수 영향, 각 권장 사항 및 각 문제를 완화 하는 방법에 대 한 수정 단계를 이해 합니다.

## <a name="conclusion"></a>결론
Security Center에서 권장 사항을 모니터링하면 공격이 발생하기 전에 보안 취약점을 제거할 수 있습니다. 권장 사항을 재구성 하면 보안 점수와 워크 로드의 보안 상태가 향상 됩니다. Security Center는 사용자가 배포한 새 리소스를 자동으로 검색하고, 보안 정책에 따라 평가하고, 보안을 유지하기 위한 새 권장 사항을 제공합니다.


## <a name="next-steps"></a>다음 단계
모니터링 프로세스가 제대로 설정되었는지 확인하고, 계속해서 리소스를 안전하게 유지할 수 있도록 Security Center에서 권장 사항을 정기적으로 확인해야 합니다.

이 시나리오에서는 Security Center에서 보안 정책 및 권장 사항을 사용하여 보안 공격을 완화할 수 있는 방법을 보여 줍니다.

[보안 경고를 관리 하 고 대응](security-center-managing-and-responding-alerts.md)하 여 위협에 대응 하는 방법을 알아봅니다.
