---
title: Azure VM에서 Azure Automation 업데이트 관리 사용
description: 이 문서에서는 Azure VM에서 업데이트 관리를 사용하도록 설정하는 방법을 설명합니다.
services: automation
ms.date: 03/04/2020
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: cab114e50852f293a3d1caf5bdc9a341f75f2557
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/21/2020
ms.locfileid: "83743954"
---
# <a name="enable-update-management-from-an-azure-vm"></a>Azure VM에서 업데이트 관리 사용

이 문서에서는 Azure VM을 사용하여 [업데이트 관리](automation-update-management.md) 기능을 다른 컴퓨터에서 사용하도록 설정하는 방법을 설명합니다. 대규모로 Azure VM을 사용하도록 설정하려면 업데이트 관리를 사용하여 기존 VM을 사용하도록 설정해야 합니다. 

> [!NOTE]
> 업데이트 관리를 사용하도록 설정할 때 특정 Azure 지역에서만 Log Analytics 작업 영역 및 Automation 계정을 연결할 수 있습니다. 지원되는 매핑 쌍 목록은 [Automation 계정의 지역 매핑 및 Log Analytics 작업 영역](how-to/region-mappings.md)을 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

* 동작합니다. 구독이 아직 없는 경우 [MSDN 구독자 혜택을 활성화](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)하거나 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 등록할 수 있습니다.
* 컴퓨터를 관리하기 위한 [Automation 계정](automation-offering-get-started.md)
* [가상 머신](../virtual-machines/windows/quick-create-portal.md).

## <a name="sign-in-to-azure"></a>Azure에 로그인

[https://portal.azure.com](https://portal.azure.com ) 에서 Azure Portal에 로그인합니다.

## <a name="enable-update-management"></a>업데이트 관리 사용

1. [Azure Portal](https://portal.azure.com)에서 **가상 머신**을 선택하거나 홈페이지에서 **가상 머신**을 검색하여 선택합니다.

2. 업데이트 관리를 사용하도록 설정할 VM을 선택합니다. VM은 Automation 계정의 위치와 상관없이 모든 지역에 있을 수 있습니다. 이러한 항목을

3. VM 페이지의 **작업** 아래에서 **업데이트 관리**를 선택합니다.

4. VM이 작업 영역에 대해 사용하도록 설정되었는지 확인하려면 `Microsoft.OperationalInsights/workspaces/read` 권한이 있어야 합니다. 필요한 추가 권한에 대한 자세한 내용은 [컴퓨터를 사용하도록 설정하는 데 필요한 권한](automation-role-based-access-control.md#feature-setup-permissions)을 참조하세요. 한 번에 여러 컴퓨터를 사용하도록 설정하는 방법에 대한 자세한 내용은 [Automation 계정에서 업데이트 관리 사용](automation-onboard-solutions-from-automation-account.md)을 참조하세요.

5. Log Analytics 작업 영역 및 Automation 계정을 선택하고 **사용**을 클릭하여 업데이트 관리를 사용하도록 설정합니다. 설정을 완료하는 데 최대 15분이 걸립니다. 

    ![업데이트 관리 사용](media/automation-tutorial-update-management/manageupdates-update-enable.png)

## <a name="check-the-scope-configuration"></a><a name="scope-configuration"></a>범위 구성 확인

업데이트 관리는 작업 영역 내의 범위 구성을 사용하고 컴퓨터를 대상으로 하여 이 기능을 사용할 수 있도록 설정합니다. 범위 구성은 기능 범위를 특정 컴퓨터로 제한하는 데 사용되는 하나 이상의 저장된 검색 그룹입니다. 자세한 내용은 [업데이트 관리의 범위 구성 작업](automation-scope-configurations-update-management.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

* VM에 업데이트 관리를 사용하려면 [Azure VM의 업데이트 및 패치 관리](automation-tutorial-update-management.md)를 참조하세요.
* 범위 구성은 [업데이트 관리의 범위 구성으로 작업](automation-scope-configurations-update-management.md)을 참조하세요.
* Log Analytics 작업 영역이 더 이상 필요하지 않은 경우 [Unlink workspace from Automation account for Update Management](automation-unlink-workspace-update-management.md)(업데이트 관리를 위해 Automation 계정에서 작업 영역 연결 해제)의 지침을 참조하세요.
* 업데이트 관리에서 VM을 삭제하려면 [Remove VMs from Update Management](automation-remove-vms-from-update-management.md)(업데이트 관리에서 VM 제거)를 참조하세요.
* 일반적인 업데이트 관리 오류를 해결하려면 [업데이트 관리 문제 해결](troubleshoot/update-management.md)을 참조하세요.
* Windows 업데이트 에이전트 문제를 해결하려면 [Windows 업데이트 에이전트 문제 해결](troubleshoot/update-agent-issues.md)을 참조하세요.
* Linux 업데이트 에이전트 문제를 해결하려면 [Linux 업데이트 에이전트 문제 해결](troubleshoot/update-agent-issues-linux.md)을 참조하세요.
