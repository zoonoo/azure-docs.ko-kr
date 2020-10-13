---
title: Automation 계정에서 Azure Automation 업데이트 관리 사용
description: 이 문서에서는 Automation 계정에서 업데이트 관리를 사용하도록 설정하는 방법을 설명합니다.
services: automation
ms.date: 09/09/2020
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: 787338be06c2e30aabb6421a42e7cb3aaabf8a2a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "89669503"
---
# <a name="enable-update-management-from-an-automation-account"></a>Automation 계정에서 업데이트 관리 사용

이 문서에서는 Automation 계정을 사용 하 여 [Azure Arc 사용 서버](../../azure-arc/servers/overview.md) (미리 보기)에 등록 된 컴퓨터 또는 서버를 포함 하 여 사용자 환경에서 vm에 대 한 [업데이트 관리](update-mgmt-overview.md) 기능을 사용 하도록 설정 하는 방법을 설명 합니다. 대규모로 Azure Vm을 사용 하도록 설정 하려면 업데이트 관리를 사용 하 여 기존 Azure VM을 사용 하도록 설정 해야 합니다.

> [!NOTE]
> 업데이트 관리를 사용하도록 설정할 때 특정 Azure 지역에서만 Log Analytics 작업 영역 및 Automation 계정을 연결할 수 있습니다. 지원되는 매핑 쌍 목록은 [Automation 계정의 지역 매핑 및 Log Analytics 작업 영역](../how-to/region-mappings.md)을 참조하세요.

## <a name="prerequisites"></a>필수 구성 요소

* 동작합니다. 구독이 아직 없는 경우 [MSDN 구독자 혜택을 활성화](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)하거나 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 등록할 수 있습니다.
* 컴퓨터를 관리하기 위한 [Automation 계정](../index.yml)
* [Azure 가상 머신](../../virtual-machines/windows/quick-create-portal.md)또는 Arc 사용 서버 (미리 보기)에 등록 된 VM 또는 서버. 비 Azure Vm 또는 서버에는 Windows 또는 Linux 용 [Log Analytics 에이전트가](../../azure-monitor/platform/log-analytics-agent.md) 설치 되어 있고에서 사용 하도록 설정 된 Automation 업데이트 관리 계정에 연결 된 작업 영역에 보고 해야 합니다. Azure Arc를 사용 하 여 [azure LOG ANALYTICS VM 확장](../../azure-arc/servers/manage-vm-extensions.md) 을 배포 하 여 arc 사용 서버에 에이전트를 설치할 수 있습니다.

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

3. 작업 영역에 보고 하는 모든 사용 가능한 컴퓨터에 대해 업데이트 관리를 사용 하도록 설정 하려면 컴퓨터 관리 페이지에서 사용 **가능한 모든 컴퓨터에서 사용** 을 선택 합니다. 이 작업은 머신을 개별적으로 추가할 수 있는 컨트롤을 비활성화합니다. 이 작업은 작업 영역에 보고 하는 컴퓨터의 모든 이름을 컴퓨터 그룹 저장 된 검색 쿼리에 추가 합니다 `MicrosoftDefaultComputerGroup` . 이 작업을 선택하면 **컴퓨터 관리** 단추를 사용할 수 없게 됩니다.

4. 사용 가능한 모든 향후 머신에서 기능을 사용하려면 **사용 가능한 모든 향후 머신에서 사용**을 선택합니다. 이 옵션은 작업 영역에서 저장 된 검색 및 범위 구성을 삭제 하 고, 현재 또는 향후에 보고 되는 모든 Azure 및 비 Azure 컴퓨터를 작업 영역에 포함 하는 기능을 허용 합니다. 이 작업을 선택 하면 사용 가능한 범위 구성이 없으므로 **컴퓨터 관리** 단추가 영구적으로 사용 하지 않도록 설정 됩니다.

    > [!NOTE]
    > 이 옵션은 Log Analytics 내에서 저장 된 검색 및 범위 구성을 삭제 하므로이 옵션을 선택 하기 전에 Log Analytics 작업 영역에서 삭제 잠금을 제거 하는 것이 중요 합니다. 그렇지 않은 경우이 옵션을 선택 하면 구성이 제거 되지 않으므로 수동으로 제거 해야 합니다.

5. 필요한 경우 초기 저장 된 검색 쿼리를 다시 추가 하 여 범위 구성을 다시 추가할 수 있습니다. 자세한 내용은 [업데이트 관리 배포 범위 제한](update-mgmt-scope-configuration.md)을 참조하세요.

6. 하나 이상의 컴퓨터에 대해이 기능을 사용 하도록 설정 하려면 **선택한 컴퓨터에서 사용** 을 선택 하 고 각 컴퓨터 옆에 있는 **추가** 를 선택 합니다. 이 작업은 선택한 머신 이름을 컴퓨터 그룹에서 저장된 기능 검색 쿼리에 추가합니다.

## <a name="next-steps"></a>다음 단계

* Vm에 대 한 업데이트 관리를 사용 하려면 [vm에 대 한 업데이트 및 패치 관리](update-mgmt-manage-updates-for-vm.md)를 참조 하세요.

* 업데이트 관리를 사용 하 여 Vm 또는 서버를 더 이상 관리할 필요가 없는 경우 [업데이트 관리에서 vm 제거](update-mgmt-remove-vms.md)를 참조 하세요.

* 일반적인 업데이트 관리 오류를 해결하려면 [업데이트 관리 문제 해결](../troubleshoot/update-management.md)을 참조하세요.
