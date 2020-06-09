---
title: Azure Automation 작업 시간 외 VM 시작/중지 사용
description: 이 문서에서는 Azure VM에서 작업 시간 외 VM 시작/중지 기능을 사용하도록 설정하는 방법을 설명합니다.
services: automation
ms.subservice: process-automation
ms.date: 04/01/2020
ms.topic: conceptual
ms.openlocfilehash: dde2c3e4cf496bb15ca91c72d9a41936af7051c5
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/21/2020
ms.locfileid: "83743749"
---
# <a name="enable-startstop-vms-during-off-hours"></a>작업 시간 외 VM 시작/중지 사용

이 토픽에 나와 있는 단계를 순서대로 수행하여 신규 또는 기존 Automation 계정 및 연결된 Log Analytics 작업 영역을 사용하는 VM에서 작업 시간 외 VM 시작/중지 기능을 사용하도록 설정합니다. 설정 프로세스를 완료한 후에는 변수를 구성하여 기능을 사용자 지정합니다.

>[!NOTE]
>클래식 VM에서 작업 시간 외 VM 시작/중지 기능을 사용하려면 클래식 실행 계정이 필요합니다. 클래식 실행 계정은 기본적으로 생성되지 않습니다. [클래식 실행 계정 만들기](automation-create-standalone-account.md#create-a-classic-run-as-account)를 참조하세요.
>

## <a name="create-resources-for-the-feature"></a>작업 시간 외 VM 시작/중지 기능을 위한 리소스 만들기

1. Azure [Portal](https://portal.azure.com)에 로그인합니다.
2. **Automation 계정**을 검색하여 선택합니다.
3. Automation 계정 페이지의 목록에서 Automation 계정을 선택합니다.
4. Automation 계정의 **관련 리소스** 아래에서 **VM 시작/중지**를 선택합니다. 여기서 **자세히 알아보고 솔루션을 사용하도록 설정하세요**를 클릭할 수 있습니다. 작업 시간 외 VM 시작/중지 기능이 이미 배포되어 있으면 **솔루션 관리**를 클릭하여 목록에서 찾을 수 있습니다.

   ![Automation 계정에서 사용하도록 설정](./media/automation-solution-vm-management/enable-from-automation-account.png)

   > [!NOTE]
   > Azure Portal의 어디서나 **리소스 만들기**를 클릭하여 리소스를 만들 수도 있습니다. Marketplace 페이지에서 **시작** 또는 **시작/중지**와 같은 키워드를 입력합니다. 입력을 시작하면 입력한 내용을 바탕으로 목록이 필터링됩니다. 또는, 이 기능의 전체 이름에서 하나 이상의 키워드를 입력한 후에 **Enter** 키를 누르는 방법도 있습니다. 검색 결과에서 **작업 시간 외 VM 시작/중지**를 선택합니다.

5. 선택한 배포에 대한 작업 시간 외 VM 시작/중지 페이지에서 요약 정보를 검토한 다음 **만들기**를 클릭합니다.

   ![Azure portal](media/automation-solution-vm-management/azure-portal-01.png)

## <a name="configure-the-feature"></a>기능 구성

리소스를 만든 후에는 솔루션 추가 페이지가 나타납니다. Automation 구독으로 이 기능을 가져오기에 앞서 이 기능을 구성하라는 메시지가 표시됩니다. [작업 시간 외 VM 시작/중지 구성](automation-solution-vm-management-config.md)을 참조하세요.

   ![VM 관리 솔루션 추가 페이지](media/automation-solution-vm-management/azure-portal-add-solution-01.png)

## <a name="select-a-log-analytics-workspace"></a>Log Analytics 작업 영역 선택

1. 솔루션 추가 페이지에서 **작업 영역**을 선택합니다. Automation 계정에서 사용하는 Azure 구독에 연결된 Log Analytics 작업 영역을 선택합니다. 

2. 작업 영역이 없으면 **새 작업 영역 만들기**를 선택합니다. Log Analytics 작업 영역 페이지에서 다음 단계를 수행합니다.

   - 새 Log Analytics 작업 영역의 이름을 지정합니다(예: **ContosoLAWorkspace**).
   - 기본으로 선택된 값이 적절하지 않으면 드롭다운 목록에서 **구독**을 선택하여 연결합니다.
   - **리소스 그룹**의 경우, 새 리소스 그룹을 만들거나 기존 리소스 그룹을 선택할 수 있습니다.
   - **위치**를 선택합니다.
   - **가격 책정 계층**을 선택합니다. **GB당(독립 실행형)** 옵션을 선택합니다. Azure Monitor 로그에서 [가격 책정](https://azure.microsoft.com/pricing/details/log-analytics/)이 업데이트되어 GB당 계층이 유일한 옵션이 되었습니다.

   > [!NOTE]
   > 기능을 사용하도록 설정할 때 특정 Azure 지역에서만 Log Analytics 작업 영역 및 Automation 계정을 연결할 수 있습니다. 지원되는 매핑 쌍 목록은 [Automation 계정의 지역 매핑 및 Log Analytics 작업 영역](how-to/region-mappings.md)을 참조하세요.

3. Log Analytics 작업 영역 페이지에서 필수 정보를 입력한 후 **만들기**를 클릭합니다. 메뉴의 **알림**에서 진행률을 추적할 수 있습니다. 완료한 후에는 솔루션 추가 페이지가 표시됩니다.

## <a name="add-automation-account"></a>Automation 계정 추가

다시 솔루션 추가 페이지로 돌아가서 **Automation 계정**을 선택합니다. 아직 Log Analytics 작업 영역에 연결되지 않은 기존 Automation 계정을 선택할 수 있습니다. 새 Log Analytics 작업 영역을 만드는 경우 작업 영역을 연결할 새 Automation 계정을 만들 수 있습니다. 기존 Automation 계정을 선택하거나 **Automation 계정 만들기**를 클릭하고, Automation 계정 추가 페이지의 **이름** 필드에 Automation 계정의 이름을 입력합니다.

다른 모든 옵션은 선택한 Log Analytics 작업 영역을 기반으로 자동으로 채워집니다. 이러한 옵션은 수정할 수 없습니다. Azure 실행 계정은 이 기능에 포함된 Runbook의 기본 인증 방법입니다. 

**확인**을 클릭하면 구성 옵션의 유효성이 검사되고 Automation 계정이 생성됩니다. 메뉴의 **알림**에서 진행 상황을 추적할 수 있습니다.

## <a name="define-feature-parameters"></a>기능 매개 변수 정의

1. 솔루션 추가 페이지에서 **구성**을 선택합니다. 매개 변수 페이지가 나타납니다.

    ![솔루션에 대한 매개 변수 페이지](media/automation-solution-vm-management/azure-portal-add-solution-02.png)

2. **대상 리소스 그룹 이름** 필드에 값을 지정합니다. 이 필드는 이 기능에 대해 관리해야 할 VM을 포함하는 그룹 이름을 정의합니다. 이름은 두 개 이상 입력할 수 있으며 각 이름을 쉼표로 구분해야 하고 대/소문자는 구분되지 않습니다. 구독 내 모든 리소스 그룹의 VM을 대상으로 하려는 경우에는 와일드카드 사용이 지원됩니다. 값은 `External_Start_ResourceGroupNames` 및 `External_Stop_ResourceGroupNames` 변수에 저장됩니다.

    > [!IMPORTANT]
    > **대상 ResourceGroup 이름**의 기본값은 **&ast;** 입니다. 이 설정은 구독의 모든 VM을 대상으로 합니다. 기능에서 구독의 모든 VM을 대상으로 지정하지 않으려면 일정을 선택하기 전에 리소스 그룹 이름 목록을 제공해야 합니다.
  
3. **VM 제외 목록(문자열)** 필드에 값을 지정합니다. 이 값은 대상 리소스 그룹에 있는 하나 이상의 가상 머신 이름입니다. 이름은 두 개 이상 입력할 수 있으며 각 이름을 쉼표로 구분해야 하고 대/소문자는 구분되지 않습니다. 와일드카드를 사용할 수 있습니다. 이 값은 `External_ExcludeVMNames` 변수에 저장됩니다.
  
4. **일정** 필드를 사용하여 이 기능에서 VM을 관리할 일정을 선택합니다. 선택한 시간에서 시작하는 반복 일일 일정을 만들려면 일정의 시작 날짜와 시간을 선택합니다. 다른 지역을 선택할 수는 없습니다. 기능을 구성한 후 일정을 특정 표준 시간대로 구성하려면 [시작 및 종료 일정 수정](automation-solution-vm-management-config.md#modify-the-startup-and-shutdown-schedules)을 참조하세요.

5. [작업 그룹](../azure-monitor/platform/action-groups.md)에서 메일 알림을 받으려면 **메일 알림** 필드에서 기본값인 **예**를 그대로 두고 유효한 메일 주소를 입력합니다. **아니요**를 선택하지만 나중에 메일 알림을 수신하려면 쉼표로 구분된 유효한 메일 주소로 만들어진 작업 그룹을 업데이트하면 됩니다. 

6. 다음 경고 규칙을 사용하도록 설정합니다.

   - `AutoStop_VM_Child`
   - `Scheduled_StartStop_Parent`
   - `Sequenced_StartStop_Parent`

## <a name="create-alerts"></a>경고 만들기

작업 시간 외 VM 시작/중지에는 미리 정의된 경고 세트가 포함되지 않습니다. [Azure Monitor를 사용하여 로그 경고 만들기](../azure-monitor/platform/alerts-log.md)를 검토하여 DevOps 또는 운영 프로세스 및 절차를 지원하는 작업 실패 경고를 만드는 방법을 알아보세요.

## <a name="deploy-the-feature"></a>기능 배포

1. 기능에 필요한 초기 설정을 구성한 후에 **확인**을 클릭하여 매개 변수 페이지를 닫습니다.

2. **만들기**를 클릭합니다. 모든 설정이 확인되면 기능이 구독에 배포됩니다. 이 프로세스는 완료하는 데 수 초가 소요되며 메뉴의 **알림**에서 진행 상황을 추적할 수 있습니다.

    > [!NOTE]
    > Azure CSP(Azure 클라우드 솔루션 공급자) 구독이 있는 경우, 배포가 완료된 후에 Automation 계정에서 **공유 리소스** 아래의 **변수**로 이동하여 [External_EnableClassicVMs](automation-solution-vm-management.md#variables) 변수를 **False**로 설정합니다. 이렇게 하면 솔루션이 클래식 VM 리소스를 검색하지 않습니다.

## <a name="next-steps"></a>다음 단계

* 기능을 설정하려면 [작업 시간 외 VM 시작/중지 구성](automation-solution-vm-management-config.md)을 참조하세요.
* 기능 오류를 해결하려면 [작업 시간 외 VM 시작/중지 문제 해결](troubleshoot/start-stop-vm.md)을 참조하세요.