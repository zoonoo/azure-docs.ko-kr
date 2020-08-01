---
title: Automation 계정에서 Azure Automation 업데이트 관리 사용
description: 이 문서에서는 Automation 계정에서 업데이트 관리를 사용하도록 설정하는 방법을 설명합니다.
services: automation
ms.date: 07/28/2020
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: 930861c61843c5963c83d8fa6dc1efdce20853f4
ms.sourcegitcommit: cee72954f4467096b01ba287d30074751bcb7ff4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/30/2020
ms.locfileid: "87450659"
---
# <a name="enable-update-management-from-an-automation-account"></a>Automation 계정에서 업데이트 관리 사용

이 문서에서는 Automation 계정을 사용하여 사용자 환경의 VM에 [업데이트 관리](update-mgmt-overview.md) 기능을 사용하도록 설정하는 방법을 설명합니다. 대규모로 Azure VM을 사용하도록 설정하려면 업데이트 관리를 사용하여 기존 VM을 사용하도록 설정해야 합니다.

> [!NOTE]
> 업데이트 관리를 사용하도록 설정할 때 특정 Azure 지역에서만 Log Analytics 작업 영역 및 Automation 계정을 연결할 수 있습니다. 지원되는 매핑 쌍 목록은 [Automation 계정의 지역 매핑 및 Log Analytics 작업 영역](../how-to/region-mappings.md)을 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

* 동작합니다. 구독이 아직 없는 경우 [MSDN 구독자 혜택을 활성화](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)하거나 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 등록할 수 있습니다.
* 컴퓨터를 관리하기 위한 [Automation 계정](../index.yml)
* [가상 머신](../../virtual-machines/windows/quick-create-portal.md).

## <a name="sign-in-to-azure"></a>Azure에 로그인

[Azure Portal](https://portal.azure.com)에 로그인합니다.

## <a name="enable-update-management"></a>업데이트 관리 사용

1. Automation 계정의 **업데이트 관리** 아래에서 **업데이트 관리**를 선택합니다.

2. Log Analytics 작업 영역 및 Automation 계정을 선택 하 고 **사용** 을 선택 하 여 업데이트 관리를 사용 하도록 설정 합니다. 설정을 완료하는 데 최대 15분이 걸립니다.

    ![업데이트 관리 사용](media/update-mgmt-enable-automation-account/onboardsolutions2.png)

## <a name="enable-azure-vms"></a>Azure VM을 사용하도록 설정

1. Automation 계정의 **업데이트 관리** 아래에서 **업데이트 관리**를 선택합니다.

2. **+ Azure Vm 추가** 를 선택 하 고 목록에서 vm을 하나 이상 선택 합니다. 사용할 수 없는 가상 머신은 회색으로 표시되어 있으며 선택할 수 없습니다. Azure VM은 Automation 계정 위치와 상관없이 모든 지역에 있을 수 있습니다.

3. **사용** 을 선택 하 여 선택한 vm을 해당 기능에 대 한 컴퓨터 그룹 저장 된 검색에 추가 합니다.

    ![Azure VM을 사용하도록 설정](media/update-mgmt-enable-automation-account/enable-azure-vms.png)

## <a name="enable-non-azure-vms"></a>비 Azure VM 사용

Azure에 없는 컴퓨터는 수동으로 추가해야 합니다.

1. Automation 계정의 **업데이트 관리** 아래에서 **업데이트 관리**를 선택합니다.

2. **비 Azure 컴퓨터 추가**를 선택 합니다. 이 작업을 수행 하면 컴퓨터가 업데이트 관리에 대 한 보고를 시작할 수 있도록 [Windows 용 Log Analytics 에이전트를 설치 하 고 구성 하는 지침이](../../azure-monitor/platform/log-analytics-agent.md) 포함 된 새 브라우저 창이 열립니다. 현재 Operations Manager에서 관리 하는 컴퓨터를 사용 하도록 설정 하는 경우 새 에이전트가 필요 하지 않습니다. 작업 영역 정보는 에이전트 구성에 추가 됩니다.

## <a name="enable-machines-in-the-workspace"></a>작업 영역에서 컴퓨터 사용

수동으로 설치된 컴퓨터나, 이미 사용자의 작업 영역에 보고하는 컴퓨터는 Azure Automation에 추가해야 업데이트 관리를 사용할 수 있습니다.

1. Automation 계정의 **업데이트 관리** 아래에서 **업데이트 관리**를 선택합니다.

2. **컴퓨터 관리**를 선택합니다. 이전에 **사용 가능한 모든 향후 머신에서 사용** 옵션을 선택한 경우 **머신 관리** 단추가 회색으로 표시될 수 있습니다.

    ![저장된 검색](media/update-mgmt-enable-automation-account/managemachines.png)

3. 사용 가능한 모든 컴퓨터에 업데이트 관리를 사용하려면 컴퓨터 관리 페이지에서 **사용 가능한 모든 컴퓨터에서 사용**을 선택합니다. 이 작업은 머신을 개별적으로 추가할 수 있는 컨트롤을 비활성화합니다. 이 작업은 작업 영역에 보고하는 컴퓨터의 모든 이름을 컴퓨터 그룹 저장된 검색 쿼리에 추가합니다. 이 작업을 선택하면 **컴퓨터 관리** 단추를 사용할 수 없게 됩니다.

4. 사용 가능한 모든 향후 머신에서 기능을 사용하려면 **사용 가능한 모든 향후 머신에서 사용**을 선택합니다. 이 옵션은 저장된 검색과 범위 구성을 작업 영역에서 삭제하고, 해당 작업 영역에 보고하는 모든 Azure 및 비 Azure 머신에 대한 기능을 엽니다. 이 작업을 선택하면 남아 있는 범위 구성이 없으므로 **머신 관리** 단추가 영구적으로 비활성화됩니다.

5. 필요한 경우 최초 저장된 검색을 다시 추가하여 범위 구성을 다시 추가할 수 있습니다. 자세한 내용은 [업데이트 관리 배포 범위 제한](update-mgmt-scope-configuration.md)을 참조하세요.

6. 하나 이상의 컴퓨터에 대해이 기능을 사용 하도록 설정 하려면 **선택한 컴퓨터에서 사용** 을 선택 하 고 각 컴퓨터 옆에 있는 **추가** 를 선택 합니다. 이 작업은 선택한 머신 이름을 컴퓨터 그룹에서 저장된 기능 검색 쿼리에 추가합니다.

## <a name="next-steps"></a>다음 단계

* Vm에 대 한 업데이트 관리를 사용 하려면 [vm에 대 한 업데이트 및 패치 관리](update-mgmt-manage-updates-for-vm.md)를 참조 하세요.

* 일반적인 업데이트 관리 오류를 해결하려면 [업데이트 관리 문제 해결](../troubleshoot/update-management.md)을 참조하세요.