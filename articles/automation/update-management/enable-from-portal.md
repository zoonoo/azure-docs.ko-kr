---
title: Azure Portal에서 Azure Automation 업데이트 관리 사용
description: 이 문서에서는 Azure Portal에서 업데이트 관리를 사용하도록 설정하는 방법을 설명합니다.
services: automation
ms.date: 04/11/2019
ms.topic: article
ms.custom: mvc
ms.openlocfilehash: 681c5e169acc30cc6708b56b5ba180d2729919e5
ms.sourcegitcommit: 8d8deb9a406165de5050522681b782fb2917762d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92222711"
---
# <a name="enable-update-management-from-the-azure-portal"></a>Azure Portal에서 업데이트 관리 사용

이 문서에서는 Azure Portal을 탐색하여 VM에 [업데이트 관리](overview.md) 기능을 사용하도록 설정하는 방법을 설명합니다. 대규모로 Azure VM을 사용하도록 설정하려면 업데이트 관리를 사용하여 기존 VM을 사용하도록 설정해야 합니다.

VM 관리에 사용할 수 있는 리소스 그룹 수는 [Resource Manager 배포 제한](../../azure-resource-manager/templates/cross-scope-deployment.md)으로 제한됩니다. Resource Manager 배포(업데이트 배포와 혼동하지 말 것)는 배포당 5개의 리소스 그룹으로 제한됩니다. 이러한 리소스 그룹 중 2개는 Log Analytics 작업 영역, Automation 계정 및 관련 리소스를 구성하기 위해 예약되어 있습니다. 3개의 리소스 그룹을 선택하여 업데이트 관리에서 관리할 수 있습니다. 이 제한은 Automation 기능으로 관리할 수 있는 리소스 그룹의 수가 아니라 동시 설정에만 적용됩니다.

> [!NOTE]
> 업데이트 관리를 사용하도록 설정할 때 특정 Azure 지역에서만 Log Analytics 작업 영역 및 Automation 계정을 연결할 수 있습니다. 지원되는 매핑 쌍 목록은 [Automation 계정 및 Log Analytics 작업 영역에 대한 지역 매핑](../how-to/region-mappings.md)을 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

* 동작합니다. 구독이 아직 없는 경우 [MSDN 구독자 혜택을 활성화](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)하거나 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 등록할 수 있습니다.
* 컴퓨터를 관리하기 위한 [Automation 계정](../index.yml)
* [가상 머신](../../virtual-machines/windows/quick-create-portal.md).

## <a name="sign-in-to-azure"></a>Azure에 로그인

https://portal.azure.com 에서 Azure에 로그인합니다.

## <a name="enable-update-management"></a>업데이트 관리 사용

1. Azure Portal에서 **가상 머신**으로 이동합니다.

2. 확인란을 사용하여 업데이트 관리에 추가할 VM을 선택합니다. 한 번에 최대 세 가지의 리소스 그룹을 추가할 수 있습니다. Azure VM은 Automation 계정의 위치에 관계없이 모든 지역에 있을 수 있습니다.

    ![VM 목록](media/enable-from-portal/vmlist.png)

    > [!TIP]
    > 필터 컨트롤을 사용하여 여러 구독, 위치 및 리소스 그룹의 VM을 선택할 수 있습니다. 상단의 확인란을 클릭하여 목록에서 모든 가상 머신을 선택할 수 있습니다.

    [![업데이트 관리 사용](./media/enable-from-portal/onboard-feature.png)](./media/enable-from-portal/onboard-feature-expanded.png#lightbox)

3. **서비스** 를 선택 하 고 업데이트 관리 기능에 대해 **업데이트 관리** 를 선택 합니다.

4. 가상 머신 목록은 동일한 구독 및 위치에 있는 가상 머신만 표시하도록 필터링됩니다. 가상 머신이 3개 초과 리소스 그룹에 있는 경우 처음 3개 리소스 그룹이 선택됩니다.

5. 기존 Log Analytics 작업 영역 및 Automation 계정은 기본적으로 선택됩니다. 다른 Log Analytics 작업 영역 및 Automation 계정을 사용 하려는 경우 **사용자 지정을 선택 하 여** 사용자 지정 구성 페이지에서 선택 합니다. Log Analytics 작업 영역을 선택하면 Automation 계정에 연결되어 있는지 확인됩니다. 연결된 Automation 계정이 있으면 다음과 같은 화면이 표시됩니다. 완료되면 **확인**을 선택합니다.

    [![작업 영역 및 계정 선택](./media/enable-from-portal/select-workspace-and-account.png)](./media/enable-from-portal/select-workspace-and-account-expanded.png#lightbox)

6. 선택한 작업 영역이 Automation 계정에 연결되지 않은 경우 다음과 같은 화면이 표시됩니다. Automation 계정을 선택 하 고 완료 되 면 **확인을** 선택 합니다.

    ![작업 영역 없음](media/enable-from-portal/no-workspace.png)

7. 사용 하지 않을 가상 컴퓨터를 선택 취소 합니다. 사용할 수 없는 VM은 이미 선택이 취소되어 있습니다.

8. **사용** 을 선택 하 여 기능을 사용 하도록 설정 합니다. 업데이트 관리를 사용 하도록 설정 하 고 나면 업데이트 평가를 확인 하기 전에 약 15 분이 걸릴 수 있습니다.

## <a name="next-steps"></a>다음 단계

* Vm에 대 한 업데이트 관리를 사용 하려면 [vm에 대 한 업데이트 및 패치 관리](manage-updates-for-vm.md)를 참조 하세요.
* 일반적인 업데이트 관리 오류를 해결하려면 [업데이트 관리 문제 해결](../troubleshoot/update-management.md)을 참조하세요.
* Windows 업데이트 에이전트 문제를 해결하려면 [Windows 업데이트 에이전트 문제 해결](../troubleshoot/update-agent-issues.md)을 참조하세요.
* Linux 업데이트 에이전트 관련 문제를 해결하려면 [Linux 업데이트 에이전트 문제 해결](../troubleshoot/update-agent-issues-linux.md)을 참조하세요.
