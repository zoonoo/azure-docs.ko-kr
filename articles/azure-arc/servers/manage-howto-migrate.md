---
title: 지역에서 Azure Arc 사용 서버를 마이그레이션하는 방법
description: 한 지역에서 다른 지역으로 Azure Arc 사용 서버를 마이그레이션하는 방법에 대해 알아봅니다.
ms.date: 02/10/2021
ms.topic: conceptual
ms.openlocfilehash: d4e0f1e41e928ab489f7c2c167eea31785d9bc21
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100417769"
---
# <a name="how-to-migrate-azure-arc-enabled-servers-across-regions"></a>지역에서 Azure Arc 사용 서버를 마이그레이션하는 방법

기존 Azure Arc 사용 서버를 한 지역에서 다른 지역으로 이동 하려는 시나리오가 있습니다. 예를 들어 컴퓨터가 잘못 된 지역에 등록 된 것으로 인식 하 여 관리 효율성을 향상 시키거나 거 버 넌 스 이유로 이동할 수 있습니다.

Azure Arc 사용 서버를 한 Azure 지역에서 다른 지역으로 마이그레이션하려면 VM 확장을 제거 하 고, Azure에서 리소스를 삭제 하 고, 다른 지역에서 다시 만들어야 합니다. 이러한 단계를 수행 하기 전에 컴퓨터를 감사 하 여 설치 된 VM 확장을 확인 해야 합니다.

> [!NOTE]
> 설치 된 확장은 계속 실행 되 고이 절차가 완료 된 후 정상적인 작업을 수행 하는 동안에는 관리할 수 없습니다. 컴퓨터에 확장을 다시 배포 하려고 하면 예기치 않은 동작이 발생할 수 있습니다.

## <a name="move-machine-to-other-region"></a>다른 지역으로 컴퓨터 이동

> [!NOTE]
> 이 작업을 수행 하는 동안 마이그레이션하는 동안 가동 중지 시간이 발생 합니다.

1. [Azure CLI](manage-vm-extensions-cli.md#remove-an-installed-extension)또는 [Azure PowerShell](manage-vm-extensions-powershell.md#remove-an-installed-extension)를 사용 하 여 [Azure Portal](manage-vm-extensions-portal.md#uninstall-extension)에서 설치 된 VM 확장을 제거 합니다.

2. **Azcmagent** 도구를 [연결 끊기](manage-agent.md#disconnect) 매개 변수와 함께 사용 하 여 azure Arc에서 컴퓨터의 연결을 끊고 azure에서 컴퓨터 리소스를 삭제 합니다. Arc 사용 서버에서 컴퓨터 연결을 끊으면 연결 된 컴퓨터 에이전트는 제거 되지 않으므로이 프로세스의 일부로 에이전트를 제거할 필요가 없습니다. 대화형으로 로그온 하는 동안이를 수동으로 실행 하거나, 여러 에이전트를 등록 하는 데 사용한 것과 동일한 서비스 주체를 사용 하거나 Microsoft id 플랫폼 [액세스 토큰](../../active-directory/develop/access-tokens.md)을 사용 하 여 자동화할 수 있습니다. 서비스 주체를 사용 하 여 Azure Arc 사용 서버에 컴퓨터를 등록 하지 않은 경우 서비스 주체를 만들려면 다음 [문서](onboard-service-principal.md#create-a-service-principal-for-onboarding-at-scale) 를 참조 하세요.

3. 다른 지역의 Arc 사용 서버에 연결 된 컴퓨터 에이전트를 다시 등록 합니다. `azcmagent` [Connect](manage-agent.md#connect) 매개 변수를 사용 하 여이 도구를 실행 합니다.

4. 원래 컴퓨터에 배포 된 VM 확장을 Arc 사용 서버에서 다시 배포 합니다. Azure 정책을 사용 하 여 VM용 Azure Monitor (insights) 에이전트 또는 Log Analytics 에이전트를 배포한 경우 다음 [평가 주기](../../governance/policy/how-to/get-compliance-data.md#evaluation-triggers)후에 에이전트가 다시 배포 됩니다.

## <a name="next-steps"></a>다음 단계

* 문제 해결 정보는 [연결 된 컴퓨터 에이전트 문제 해결 가이드](troubleshoot-agent-onboard.md)에서 찾을 수 있습니다.

* [Azure Policy](../../governance/policy/overview.md)를 사용 하 여 컴퓨터를 관리 하는 방법, 컴퓨터에서 예상 Log Analytics 작업 영역에 보고 하는지 [확인,](../../governance/policy/concepts/guest-configuration.md) [vm 정책으로 Azure Monitor](../../azure-monitor/insights/vminsights-enable-policy.md) 모니터링 사용 등의 작업을 수행 하는 방법을 알아봅니다.
