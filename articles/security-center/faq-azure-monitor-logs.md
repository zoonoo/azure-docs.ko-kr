---
title: Azure Security Center FAQ-기존 Log Analytics 에이전트에 대 한 질문
description: 이 FAQ는 이미 Log Analytics 에이전트를 사용 하 고 있으며 위협을 예방, 감지 및 대응 하는 데 도움이 되는 제품 Azure Security Center를 고려 하 고 있는 고객에 대 한 질문에 답변 합니다.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: be2ab6d5-72a8-411f-878e-98dac21bc5cb
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/25/2020
ms.author: memildin
ms.openlocfilehash: f6384c1e9e14e38b4c44c5ac79a674839b43b4ca
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80436159"
---
# <a name="faq-for-customers-already-using-azure-monitor-logs"></a>이미 Azure Monitor 로그를 사용 하는 고객에 대 한 FAQ<a name="existingloganalyticscust"></a>

## <a name="does-security-center-override-any-existing-connections-between-vms-and-workspaces"></a>Security Center에서 VM과 작업 영역 간의 모든 기존 연결을 재정의하나요?

VM에 이미 Azure 확장으로 설치 된 Log Analytics 에이전트가 있는 경우 Security Center는 기존 작업 영역 연결을 재정의 하지 않습니다. 대신 Security Center에서 기존 작업 영역을 사용합니다. "보안" 또는 "보안 강화" 솔루션이 보고 하는 작업 영역에 설치 되어 있으면 VM이 보호 됩니다. 

Security Center 솔루션은 아직 없는 경우 데이터 수집 화면에서 선택한 작업 영역에 설치 되 고 솔루션은 관련 Vm에만 적용 됩니다. 솔루션을 추가하면 기본적으로 Log Analytics 작업 영역에 연결된 모든 Windows 및 Linux 에이전트에 의해 배포됩니다. [솔루션 대상 지정](../operations-management-suite/operations-management-suite-solution-targeting.md)을 사용하면 솔루션에 범위를 적용할 수 있습니다.

> [!TIP]
> Log Analytics 에이전트가 Azure Security Center 확장이 아닌 VM에 직접 설치 된 경우에는 Log Analytics 에이전트를 설치 하지 않고 보안 모니터링이 제한 됩니다.

## <a name="does-security-center-install-solutions-on-my-existing-log-analytics-workspaces-what-are-the-billing-implications"></a>Security Center에서 기존 Log Analytics 작업 영역에 솔루션을 설치하나요? 요금 청구에 영향을 주는 요인은 무엇인가요?
Security Center에서 VM이 만든 작업 영역에 이미 연결되어 있는지를 식별하는 경우 Security Center를 통해 가격 책정 계층에 따라 이 작업 영역에서 솔루션을 사용할 수 있습니다. 솔루션이 [솔루션 대상 지정](../operations-management-suite/operations-management-suite-solution-targeting.md)을 통해 관련 Azure VM에만 적용되므로 청구는 동일하게 유지됩니다.

- **체험 계층** – Security Center는 작업 영역에서 'SecurityCenterFree' 솔루션을 설치합니다. 무료 계층에 대해서는 요금이 청구 되지 않습니다.
- **표준 계층** – Security Center가 작업 영역에 'Security' 솔루션을 설치합니다.

   ![기본 작업 영역의 솔루션](./media/security-center-platform-migration-faq/solutions.png)

## <a name="i-already-have-workspaces-in-my-environment-can-i-use-them-to-collect-security-data"></a>내 환경에 이미 작업 영역이 있는 경우 보안 데이터를 수집하는 데 사용할 수 있나요?
VM에 이미 Azure 확장으로 설치 된 Log Analytics 에이전트가 있는 경우 Security Center는 기존 연결 된 작업 영역을 사용 합니다. Security Center 솔루션이 작업 영역에 없는 경우 설치되고 솔루션은 [솔루션 대상 지정](../operations-management-suite/operations-management-suite-solution-targeting.md)을 통해 관련 VM에만 적용됩니다.

Security Center Vm에 Log Analytics 에이전트를 설치 하는 경우 Security Center에서 만든 기본 작업 영역을 사용 합니다.

## <a name="i-already-have-security-solution-on-my-workspaces-what-are-the-billing-implications"></a>내 작업 영역에 보안 솔루션이 이미 있습니다. 요금 청구에 영향을 주는 요인은 무엇인가요?
보안 & 감사 솔루션은 Azure Vm에 대 한 Security Center 표준 계층 기능을 사용 하도록 설정 하는 데 사용 됩니다. 보안 및 감사 솔루션이 작업 영역에 이미 설치되어 있는 경우 Security Center에서는 기존 솔루션을 사용합니다. 요금 청구는 변하지 않습니다.