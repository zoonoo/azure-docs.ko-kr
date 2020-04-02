---
title: 오프 시간 솔루션 동안 VM 시작/중지 사용
description: 이 문서에서는 Azure 가상 시스템에 대한 Azure 자동화 시작/중지 VM 솔루션을 사용하도록 설정하는 방법에 대해 설명합니다.
services: automation
ms.subservice: process-automation
ms.date: 04/01/2020
ms.topic: conceptual
ms.openlocfilehash: 4f4b0805e2a9247aff881a9e34b0bd1cd4c6ca27
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/02/2020
ms.locfileid: "80550410"
---
# <a name="enable-azure-startstop-vms-solution"></a>Azure 시작/중지 VM 솔루션 사용

다음 단계를 수행하여 근무 외 솔루션 중에 시작/중지 VM을 새 또는 기존 자동화 계정 및 연결된 Log Analytics 작업 영역에 추가합니다. 온보딩 프로세스를 완료한 후 변수를 구성하여 솔루션을 사용자 지정합니다.

>[!NOTE]
>클래식 VM에서 이 솔루션을 사용하려면 기본적으로 만들어지지 않은 클래식 RunAs 계정이 필요합니다. 클래식 RunAs 계정 만들기에 대한 지침은 [클래식 실행-로 계정](automation-create-standalone-account.md#classic-run-as-accounts)을 참조하십시오.
>

## <a name="enable-solution"></a>솔루션 사용

1. Azure [포털에](https://portal.azure.com)로그인합니다.

2. **자동화 계정을**검색하고 선택합니다.

3. 자동화 **계정** 페이지에서 목록에서 자동화 계정을 선택합니다.

4. 자동화 계정에서 **관련 리소스에서** **VM 시작/중지를** 선택합니다. 여기서 **자세히 알아보고 솔루션을 사용하도록 설정하세요**를 클릭할 수 있습니다. VM 시작/중지 솔루션이 이미 표시되어 있으면 **솔루션 관리**를 클릭하여 선택하고 목록에서 찾을 수 있습니다.

   ![Automation 계정에서 사용하도록 설정](./media/automation-solution-vm-management/enable-from-automation-account.png)

   > [!NOTE]
   > Azure Portal의 어디서나 **리소스 만들기**를 클릭하여 만들 수도 있습니다. Marketplace 페이지에서 **시작** 또는 **시작/중지**와 같은 키워드를 입력합니다. 입력을 시작하면 입력한 내용을 바탕으로 목록이 필터링됩니다. 또는 솔루션의 전체 이름에서 하나 이상의 키워드를 입력한 다음 Enter 키를 누를 수도 있습니다. 검색 결과에서 **작업 시간 외 VM 시작/중지**를 선택합니다.

5. 선택한 솔루션에 대한 **작업 시간 외 VM 시작/중지** 페이지에서 요약 정보를 검토한 다음, **만들기**를 클릭합니다.

   ![Azure portal](media/automation-solution-vm-management/azure-portal-01.png)

6. **솔루션 추가** 페이지가 나타납니다. Automation 구독으로 솔루션을 가져오려면 솔루션을 구성하라는 메시지가 표시됩니다.

   ![VM 관리 솔루션 추가 페이지](media/automation-solution-vm-management/azure-portal-add-solution-01.png)

7. **솔루션 추가** 페이지에서 **작업 영역**을 선택합니다. Automation 계정이 속하는 동일한 Azure 구독에 연결된 Log Analytics 작업 영역을 선택합니다. 작업 영역이 없으면 **새 작업 영역 만들기**를 선택합니다. **로그 분석 작업 영역** 페이지에서 다음 단계를 수행합니다.

   - "ContosoLAWorkspace"와 같은 새 **로그 분석 작업 영역의**이름을 지정합니다.
   - 기본으로 선택된 값이 적절하지 않으면 드롭다운 목록에서 선택하여 연결할 **구독**을 선택합니다.
   - **리소스 그룹**의 경우, 새 리소스 그룹을 만들거나 기존 리소스 그룹을 선택할 수 있습니다.
   - **위치**를 선택합니다.
   - 가격 **책정 계층을**선택합니다. **GB당(독립 실행형)** 옵션을 선택합니다. Azure Monitor 로그가 [가격을](https://azure.microsoft.com/pricing/details/log-analytics/) 업데이트했으며 GB당 계층이 유일한 옵션입니다.

   > [!NOTE]
   > 솔루션을 사용하도록 설정할 때 특정 Azure 지역에서만 Log Analytics 작업 영역 및 Automation 계정을 연결할 수 있습니다.
   >
   > 지원되는 매핑 쌍 목록은 자동화 [계정 및 로그 분석 작업 영역에 대한 지역 매핑을](how-to/region-mappings.md)참조하십시오.

8. **Log Analytics 작업 영역** 페이지에서 필수 정보를 입력한 후 **만들기**를 클릭합니다. 메뉴의 **알림**에서 진행률을 추적할 수 있습니다. 완료한 후에는 **솔루션 추가** 페이지로 돌아갑니다.

9. **솔루션 추가** 페이지에서 **Automation 계정**을 선택합니다. 새 Log Analytics 작업 영역을 만드는 경우 연결될 새 Automation 계정을 만들거나 아직 Log Analytics 작업 영역에 연결되지 않은 기존 Automation 계정을 선택할 수 있습니다. 기존 Automation 계정을 선택하거나 **Automation 계정 만들기**를 클릭하고, **Automation 계정 추가** 페이지에서 다음 정보를 제공합니다.
 
   - **이름** 필드에서 Automation 계정의 이름을 입력합니다.

     다른 모든 옵션은 선택한 Log Analytics 작업 영역을 기반으로 자동으로 채워집니다. 이러한 옵션은 수정할 수 없습니다. Azure 실행 계정은 이 솔루션에 포함된 Runbook에 대한 기본 인증 방법입니다. **확인**을 클릭하면 구성 옵션의 유효성이 검사되고 Automation 계정이 생성됩니다. 메뉴의 **알림**에서 진행 상황을 추적할 수 있습니다.

10. 마지막으로 **솔루션 추가** 페이지에서 **구성**을 선택합니다. **매개 변수** 페이지가 나타납니다.

    ![솔루션에 대한 매개 변수 페이지](media/automation-solution-vm-management/azure-portal-add-solution-02.png)

   여기에서는 다음을 묻는 메시지가 표시됩니다.
  
   - **대상 ResourceGroup 이름**을 지정합니다. 이 값은 이 솔루션에서 관리되는 VM을 포함하는 리소스 그룹 이름입니다. 이름은 두 개 이상 입력할 수 있으며 각 이름을 쉼표로 구분해야 하고 대/소문자는 구분되지 않습니다. 구독 내 모든 리소스 그룹의 VM을 대상으로 하려는 경우에는 와일드카드 사용이 지원됩니다. 이 값은 **External_Start_ResourceGroupNames** 및 **External_Stop_ResourceGroupNames** 변수에 저장됩니다.
  
   - **VM 제외 목록(문자열)** 을 지정합니다. 이 값은 대상 리소스 그룹에 있는 하나 이상의 가상 머신 이름입니다. 이름은 두 개 이상 입력할 수 있으며 각 이름을 쉼표로 구분해야 하고 대/소문자는 구분되지 않습니다. 와일드카드를 사용할 수 있습니다. 이 값은 **External_ExcludeVMNames** 변수에 저장됩니다.
  
   - **일정**을 선택합니다. 일정의 날짜와 시간을 선택합니다. 선택한 시간부터 일일 일정이 다시 생성됩니다. 다른 지역을 선택할 수는 없습니다. 솔루션을 구성한 후 일정을 특정 표준 시간대로 구성하려면 [시작 및 종료 일정 수정](automation-solution-vm-management-config.md#modify-the-startup-and-shutdown-schedules)을 참조하세요.
  
   - 작업 그룹에서 **메일 알림**을 받으려면 기본값인 **예**를 그대로 두고 유효한 메일 주소를 제공합니다. **아니요**를 선택하지만 나중에 메일 알림을 수신하려면 쉼표로 구분된 유효한 메일 주소로 만들어진 [작업 그룹](../azure-monitor/platform/action-groups.md)을 업데이트하면 됩니다. 또한 다음 경고 규칙을 사용하도록 설정해야 합니다.

     - AutoStop_VM_Child
     - Scheduled_StartStop_Parent
     - Sequenced_StartStop_Parent

     > [!IMPORTANT]
     > **대상 ResourceGroup 이름**의 기본값은 **&ast;** 입니다. 이것은 구독에 포함된 모든 VM을 대상으로 합니다. 솔루션에서 구독의 모든 VM을 대상으로 지정하지 않으려면 일정을 사용하기 전에 이 값을 리소스 그룹 이름 목록으로 업데이트해야 합니다.

11. 솔루션에 필요한 초기 설정을 구성한 후에 **확인**을 클릭하여 **매개 변수** 페이지를 닫고 **만들기**를 선택합니다. 

모든 설정이 확인되면 솔루션이 구독에 배포됩니다. 이 프로세스는 완료하는 데 수 초가 소요되며 메뉴의 **알림**에서 진행 상황을 추적할 수 있습니다.

> [!NOTE]
> Azure 클라우드 솔루션 공급자(Azure CSP) 구독이 있는 경우 배포가 완료된 후 자동화 계정에서 **공유 리소스** **아래의 변수로** 이동하여 [**External_EnableClassicVMs**](automation-solution-vm-management.md#variables) 변수를 **False로**설정합니다. 이렇게 하면 솔루션이 클래식 VM 리소스를 검색하지 않습니다.

## <a name="next-steps"></a>다음 단계

이제 솔루션을 사용하도록 설정되었으므로 VM 관리 요구 사항을 지원하도록 [구성할](automation-solution-vm-management-config.md) 수 있습니다.