---
title: Azure Automation의 VM에 대한 업데이트 및 패치 관리
description: 이 문서에서는 업데이트 관리를 사용하여 Azure VM 및 비 Azure VM에 대한 업데이트 및 패치를 관리하는 방법을 설명합니다.
services: automation
ms.subservice: update-management
ms.topic: conceptual
ms.date: 01/27/2021
ms.openlocfilehash: c86c9049bc0afc81f5dfd8553d2aa98cfd4b1a46
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98915985"
---
# <a name="manage-updates-and-patches-for-your-vms"></a>VM에 대한 업데이트 및 패치 관리

Azure Automation 업데이트 관리의 소프트웨어 업데이트는 Azure 및 하이브리드 클라우드의 컴퓨터에 소프트웨어 업데이트를 추적하고 적용하는 복잡한 작업을 관리할 수 있는 도구 및 리소스 집합을 제공합니다. 작업의 효율성을 유지하고 보안 문제를 극복하고 사이버 보안 위협의 위험성을 줄이려면 효과적인 소프트웨어 업데이트 관리 프로세스가 필요합니다. 그러나 변화하는 기술의 특성과 지속적으로 출현하는 새로운 보안 위협 때문에 소프트웨어 업데이트를 효과적으로 관리하려면 일관되고 꾸준한 주의를 기울여야 합니다.

> [!NOTE]
> 업데이트 관리는 자사 업데이트 배포와 미리 다운로드를 지원합니다. 이 지원을 사용하려면 업데이트할 시스템을 변경해야 합니다. 시스템에 이러한 설정을 구성하는 방법을 알아보려면 [Azure Automation 업데이트 관리에 대한 Windows 업데이트 설정 구성](configure-wuagent.md)을 참조하세요.

VM에 대한 업데이트 관리를 시도하기 전에 다음 방법 중 하나를 사용하여 업데이트 관리를 사용하도록 설정했는지 확인합니다.

* [Automation 계정에서 업데이트 관리 사용](enable-from-automation-account.md)
* [Azure Portal을 탐색하여 업데이트 관리 사용](enable-from-portal.md)
* [Runbook에서 업데이트 관리 사용](enable-from-runbook.md)
* [Azure VM에서 업데이트 관리 사용](enable-from-vm.md)

## <a name="limit-the-scope-for-the-deployment"></a><a name="scope-configuration"></a>배포 범위 제한

업데이트 관리는 작업 영역 내의 범위 구성을 사용하여 업데이트를 수신할 대상 컴퓨터를 지정합니다. 자세한 내용은 [업데이트 관리 배포 범위 제한](scope-configuration.md)을 참조하세요.

## <a name="compliance-assessment"></a>규정 준수 평가

컴퓨터에 소프트웨어 업데이트를 배포하기 전에 지원되는 컴퓨터에 대한 업데이트 규정 준수 평가 결과를 검토합니다. 각 소프트웨어 업데이트에 대해 해당 호환성 상태가 기록되고 평가가 완료된 후에는 대량으로 수집되고 Azure Monitor 로그에 전달됩니다.

Windows 컴퓨터에서 규정 준수 검사는 기본적으로 12시간마다 실행되며 Windows용 Log Analytics 에이전트가 다시 시작될 때 15분 이내에 시작됩니다. 그런 다음 평가 데이터가 작업 영역으로 전달되고 **업데이트** 테이블이 새로 고쳐집니다. 업데이트 설치 전후에 업데이트 규정 준수 검사를 수행하여 누락된 업데이트를 식별하지만 이 결과는 테이블의 평가 데이터를 업데이트하는 데 사용되지 않습니다.

업데이트 관리를 사용하여 [Windows 업데이트 클라이언트를 구성](configure-wuagent.md)하는 방법에 대한 권장 사항을 검토하여 올바르게 관리되지 않는 문제를 방지하는 것이 중요합니다.

Linux 머신에서는 기본적으로 1시간마다 준수 검사가 이루어집니다. Linux용 Log Analytics 에이전트가 다시 시작되면 15분 이내에 규정 준수 검사가 시작됩니다.

규정 준수 결과는 각 평가된 컴퓨터의 업데이트 관리에 표시됩니다. 대시보드가 관리를 위해 사용하도록 설정된 새 컴퓨터에서 업데이트된 데이터를 표시하는 데 최대 30분이 걸릴 수 있습니다.

규정 준수 결과를 보는 방법을 알아보려면 [소프트웨어 업데이트 모니터링](view-update-assessments.md)을 검토합니다.

## <a name="deploy-updates"></a>업데이트 배포

규정 준수 결과를 검토한 후의 소프트웨어 업데이트 배포 단계는 소프트웨어 업데이트를 배포하는 프로세스입니다. 업데이트를 설치하려면 릴리스 일정 및 서비스 기간 이후로 배포를 예약합니다. 배포에 포함할 업데이트 형식을 선택할 수 있습니다. 예를 들어 중요 업데이트나 보안 업데이트를 포함하고 업데이트 롤업은 제외할 수 있습니다.

업데이트 배포를 예약하는 방법을 알아보려면 [소프트웨어 업데이트 배포](deploy-updates.md)를 검토합니다.

## <a name="review-update-deployments"></a>업데이트 배포 검토

배포가 완료되면 프로세스를 검토하여 컴퓨터 또는 대상 그룹별 업데이트 배포의 성공 여부를 확인합니다. 배포 상태를 모니터링하는 방법을 알아보려면 [배포 상태 검토](deploy-updates.md#check-deployment-status)를 참조하세요.

## <a name="next-steps"></a>다음 단계

* 업데이트 배포 결과에 대해 알리는 경고를 만드는 방법을 알아보려면 [업데이트 관리에 대한 경고 만들기](configure-alerts.md)를 참조하세요.

* [Azure Monitor 로그를 쿼리](query-logs.md)하여 업데이트 평가, 배포 및 기타 관련 관리 작업을 분석할 수 있습니다. 시작하는 데 도움이 되는 미리 정의된 쿼리를 포함합니다.