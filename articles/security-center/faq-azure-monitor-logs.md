---
title: Azure 보안 센터 FAQ - 기존 MMA에 대한 질문
description: 이 FAQ는 이미 Microsoft 모니터링 에이전트를 사용하고 있으며 위협을 방지, 탐지 및 대응하는 데 도움이 되는 제품인 Azure 보안 센터를 고려하는 고객에 대한 질문에 답합니다.
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
ms.openlocfilehash: 5c433140c3982813e372fd3f63243a96197d220c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77661892"
---
# <a name="faq-for-customers-already-using-azure-monitor-logs"></a>Azure 모니터 로그를 이미 사용 하는 고객에 대 한 자주 묻는 질문<a name="existingloganalyticscust"></a>

## <a name="does-security-center-override-any-existing-connections-between-vms-and-workspaces"></a>Security Center에서 VM과 작업 영역 간의 모든 기존 연결을 재정의하나요?

VM에 Azure 확장으로 Microsoft Monitoring Agent가 이미 설치되어 있으면 Security Center에서 기존 작업 영역 연결을 재정의하지 않습니다. 대신 Security Center에서 기존 작업 영역을 사용합니다. 보고하는 작업 영역에 "보안" 또는 "SecurityCenterFree" 솔루션이 설치된 경우 VM이 보호됩니다. 

보안 센터 솔루션은 데이터 수집 화면에서 선택된 작업 영역에 설치되지 않은 경우 이미 존재하지 않으며 해당 솔루션은 관련 VM에만 적용됩니다. 솔루션을 추가하면 기본적으로 Log Analytics 작업 영역에 연결된 모든 Windows 및 Linux 에이전트에 의해 배포됩니다. [솔루션 대상 지정](../operations-management-suite/operations-management-suite-solution-targeting.md)을 사용하면 솔루션에 범위를 적용할 수 있습니다.

> [!TIP]
> Microsoft 모니터링 에이전트가 Azure 확장이 아닌 VM에 직접 설치되는 경우 보안 센터는 Microsoft 모니터링 에이전트를 설치하지 않으며 보안 모니터링이 제한됩니다.

## <a name="does-security-center-install-solutions-on-my-existing-log-analytics-workspaces-what-are-the-billing-implications"></a>Security Center에서 기존 Log Analytics 작업 영역에 솔루션을 설치하나요? 요금 청구에 영향을 주는 요인은 무엇인가요?
Security Center에서 VM이 만든 작업 영역에 이미 연결되어 있는지를 식별하는 경우 Security Center를 통해 가격 책정 계층에 따라 이 작업 영역에서 솔루션을 사용할 수 있습니다. 솔루션이 [솔루션 대상 지정](../operations-management-suite/operations-management-suite-solution-targeting.md)을 통해 관련 Azure VM에만 적용되므로 청구는 동일하게 유지됩니다.

- **체험 계층** – Security Center는 작업 영역에서 'SecurityCenterFree' 솔루션을 설치합니다. 프리 티어에 대한 요금은 청구되지 않습니다.
- **표준 계층** – Security Center가 작업 영역에 'Security' 솔루션을 설치합니다.

   ![기본 작업 영역의 솔루션](./media/security-center-platform-migration-faq/solutions.png)

## <a name="i-already-have-workspaces-in-my-environment-can-i-use-them-to-collect-security-data"></a>내 환경에 이미 작업 영역이 있는 경우 보안 데이터를 수집하는 데 사용할 수 있나요?
VM에 Azure 확장으로 Microsoft Monitoring Agent가 이미 설치되어 있으면 Security Center에서 기존 연결된 작업 영역 연결을 사용합니다. Security Center 솔루션이 작업 영역에 없는 경우 설치되고 솔루션은 [솔루션 대상 지정](../operations-management-suite/operations-management-suite-solution-targeting.md)을 통해 관련 VM에만 적용됩니다.

Security Center에서 VM에 Microsoft Monitoring Agent를 설치하면 Security Center에서 만든 기본 작업 영역을 사용합니다.

## <a name="i-already-have-security-solution-on-my-workspaces-what-are-the-billing-implications"></a>내 작업 영역에 보안 솔루션이 이미 있습니다. 요금 청구에 영향을 주는 요인은 무엇인가요?
보안 & 감사 솔루션은 Azure VM에 대한 보안 센터 표준 계층 기능을 사용하도록 설정하는 데 사용됩니다. 보안 및 감사 솔루션이 작업 영역에 이미 설치되어 있는 경우 Security Center에서는 기존 솔루션을 사용합니다. 요금 청구는 변하지 않습니다.