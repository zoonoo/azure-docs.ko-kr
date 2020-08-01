---
title: Azure Automation에서 Vm에 대 한 업데이트 및 패치 관리
description: 이 문서에서는 Azure 및 비 Azure Vm에 대 한 업데이트 및 패치를 관리 하기 위해 업데이트 관리를 사용 하는 방법을 설명 합니다.
services: automation
ms.subservice: update-management
ms.topic: conceptual
ms.date: 07/28/2020
ms.custom: mvc
ms.openlocfilehash: e4f630931e3cd79a46a539955b45d72e5b573271
ms.sourcegitcommit: cee72954f4467096b01ba287d30074751bcb7ff4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/30/2020
ms.locfileid: "87450528"
---
# <a name="manage-updates-and-patches-for-your-vms"></a>Vm에 대 한 업데이트 및 패치 관리

Azure Automation 업데이트 관리의 소프트웨어 업데이트는 Azure 및 하이브리드 클라우드의 컴퓨터에 대 한 소프트웨어 업데이트를 추적 하 고 적용 하는 복잡 한 작업을 관리 하는 데 도움이 되는 도구 및 리소스 집합을 제공 합니다. 운영 효율성을 유지 하 고, 보안 문제를 극복 하 고, 사이버 보안 위협이 증가 하는 위험을 줄이기 위해서는 효과적인 소프트웨어 업데이트 관리 프로세스가 필요 합니다. 그러나 변화하는 기술의 특성과 지속적으로 출현하는 새로운 보안 위협 때문에 소프트웨어 업데이트를 효과적으로 관리하려면 일관되고 꾸준한 주의를 기울여야 합니다.

> [!NOTE]
> 업데이트 관리는 자사 업데이트의 배포와 해당 업데이트의 사전 다운로드를 지원 합니다. 이 지원을 사용 하려면 업데이트 되는 시스템을 변경 해야 합니다. 시스템에 이러한 설정을 구성하는 방법을 알아보려면 [Azure Automation 업데이트 관리에 대한 Windows 업데이트 설정 구성](update-mgmt-configure-wuagent.md)을 참조하세요.

Vm에 대 한 업데이트 관리를 시도 하기 전에 다음 방법 중 하나를 사용 하 여 업데이트 관리를 사용 하도록 설정 했는지 확인 합니다.

* [Automation 계정에서 업데이트 관리 사용](update-mgmt-enable-automation-account.md)
* [Azure Portal을 탐색하여 업데이트 관리 사용](update-mgmt-enable-portal.md)
* [Runbook에서 업데이트 관리 사용](update-mgmt-enable-runbook.md)
* [Azure VM에서 업데이트 관리 사용](update-mgmt-enable-vm.md)

## <a name="limit-the-scope-for-the-deployment"></a><a name="scope-configuration"></a>배포 범위 제한

업데이트 관리는 작업 영역 내의 범위 구성을 사용하여 업데이트를 수신할 대상 컴퓨터를 지정합니다. 자세한 내용은 [업데이트 관리 배포 범위 제한](update-mgmt-scope-configuration.md)을 참조하세요.

## <a name="compliance-assessment"></a>규정 준수 평가

컴퓨터에 소프트웨어 업데이트를 배포 하기 전에 지원 되는 컴퓨터에 대 한 업데이트 준수 평가 결과를 검토 합니다. 각 소프트웨어 업데이트에 대해 해당 호환성 상태가 기록 되 고 평가가 완료 된 후에는 대량으로 수집 되 고 Azure Monitor 로그에 전달 됩니다.

Windows 머신에서는 기본적으로 12시간마다 준수 검사가 실행됩니다. 예약 된 검사 외에도 업데이트 준수에 대 한 검색은 Windows를 다시 시작 하 고 업데이트를 설치 하기 전에, 업데이트 설치 후 15 Log Analytics 분 이내에 시작 됩니다. 업데이트 관리를 사용 하 여 [Windows 업데이트 클라이언트를 구성](update-mgmt-configure-wuagent.md) 하는 방법에 대 한 권장 사항을 검토 하 여 올바르게 관리 되지 않는 문제를 방지 하는 것도 중요 합니다.

Linux 머신에서는 기본적으로 1시간마다 준수 검사가 이루어집니다. Linux 용 Log Analytics agent를 다시 시작 하는 경우 15 분 내에 준수 검사가 시작 됩니다.

규정 준수 결과는 각 컴퓨터에서 평가 되는 업데이트 관리 표시 됩니다. 관리에 사용할 수 있는 새 컴퓨터의 경우 대시보드가 업데이트 된 데이터를 표시 하는 데 최대 30 분이 걸릴 수 있습니다.

[소프트웨어 업데이트 모니터링](update-mgmt-view-update-assessments.md) 을 검토 하 여 준수 결과를 보는 방법을 알아보세요.

## <a name="deploy-updates"></a>업데이트 배포

준수 결과를 검토 한 후 소프트웨어 업데이트 배포 단계는 소프트웨어 업데이트를 배포 하는 프로세스입니다. 업데이트를 설치하려면 릴리스 일정 및 서비스 기간 이후로 배포를 예약합니다. 배포에 포함할 업데이트 형식을 선택할 수 있습니다. 예를 들어 중요 업데이트나 보안 업데이트를 포함하고 업데이트 롤업은 제외할 수 있습니다.

업데이트 배포를 예약 하는 방법을 알아보려면 [소프트웨어 업데이트 배포](update-mgmt-deploy-updates.md) 를 검토 합니다.

## <a name="review-update-deployments"></a>업데이트 배포 검토

배포가 완료 되 면 프로세스를 검토 하 여 컴퓨터 또는 대상 그룹별 업데이트 배포의 성공 여부를 확인 합니다. 배포 상태를 모니터링 하는 방법을 알아보려면 [배포 상태 검토](update-mgmt-deploy-updates.md#check-deployment-status) 를 참조 하세요.

## <a name="next-steps"></a>다음 단계

* 업데이트 배포 결과에 대해 알리도록 경고를 만드는 방법을 알아보려면 [업데이트 관리에 대 한 경고 만들기](update-mgmt-configure-alerts.md)를 참조 하세요.

* [Azure Monitor 로그를 쿼리하여](update-mgmt-query-logs.md) 업데이트 평가, 배포 및 기타 관련 관리 작업을 분석할 수 있습니다. 시작 하는 데 도움이 되는 미리 정의 된 쿼리를 포함 합니다.