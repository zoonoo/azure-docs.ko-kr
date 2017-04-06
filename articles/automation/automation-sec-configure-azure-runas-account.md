---
title: "Azure 실행 계정 구성 | Microsoft Docs"
description: "이 자습서는 Azure Automation에서 보안 주체 인증을 만들고 테스트하며 예제를 사용하는 과정을 설명합니다."
services: automation
documentationcenter: 
author: mgoedtel
manager: carmonm
editor: 
keywords: "서비스 주체 이름, setspn, azure 인증"
ms.assetid: 2f783441-15c7-4ea0-ba27-d7daa39b1dd3
ms.service: automation
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 04/06/2017
ms.author: magoedte
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: fbca3d195290551d37606e231b997a40a602351f
ms.lasthandoff: 03/29/2017


---

# <a name="authenticate-runbooks-with-an-azure-run-as-account"></a>Azure 실행 계정으로 Runbook 인증
이 문서에서는 Azure Portal에서 Azure Automation 계정을 구성하는 방법을 보여 줍니다. 이렇게 하려면 Azure Resource Manager 또는 Azure Service Management 중 하나에서 Runbook 관리 리소스를 인증하기 위해 실행 계정 기능을 사용합니다.

Azure Portal에서 Automation 계정을 만들 경우 두 개의 계정을 자동으로 만듭니다.

* 실행 계정 이 계정은 Azure AD(Azure Active Directory)의 서비스 주체 및 인증서를 만듭니다. 또한 Runbook을 사용하여 Resource Manager 리소스를 관리하는 참가자 역할 기반 액세스 제어(RBAC)를 할당합니다.
* 클래식 실행 계정 이 계정은 Runbook을 사용하여 Service Management 또는 클래식 리소스를 관리하는 데 사용되는 관리 인증서를 업로드합니다.

Automation 계정을 만들면 처리를 간편하게 만들고 자동화 요구를 지원하는 Runbook의 구축 및 배포를 신속하게 시작할 수 있습니다.

실행 계정 및 클래식 실행 계정을 사용하여 다음 작업을 수행할 수 있습니다.

* Azure Portal에서 Runbook의 Resource Manager 또는 Service Management 리소스를 관리하는 경우 Azure로 인증하는 표준화된 방법을 제공합니다.
* 전역적인 Runbook의 사용을 자동화하면 Azure 경고에 구성될 수 있습니다.

> [!NOTE]
> Automation 글로벌 Runbook을 포함한 [Azure 경고 통합 기능](../monitoring-and-diagnostics/insights-receive-alert-notifications.md)에는 실행 계정 및 클래식 실행 계정이 구성된 Automation 계정이 필요합니다. 실행 및 클래식 실행 계정을 이미 정의한 Automation 계정을 선택하거나 새로운 Automation 계정을 만들 수 있습니다.
>  

이 문서에서는 Azure Portal에서 Automation 계정을 만들고, Azure PowerShell을 사용하여 Automation 계정을 업데이트하고, 계정 구성을 관리하고, Runbook에서 인증하는 방법을 보여 줍니다.

Automation 계정을 만들기 전에 다음 항목을 이해하고 고려하는 것이 좋습니다.

* Automation 계정을 만드는 작업은 클래식 또는 Resource Manager 배포 모델에서 이미 만든 Automation 계정에는 영향을 주지 않습니다.
* 이 프로세스는 Azure Portal에서 만든 Automation 계정에만 적용됩니다. Azure 클래식 포털에서 계정을 만들려는 시도는 실행 계정 구성을 복제하지 않습니다.
* Runbook과 자산(예: 일정 또는 변수)을 배치하여 클래식 리소스를 관리하고 새 클래식 실행 계정을 사용하여 Runbook을 인증하려는 경우 다음 중 하나를 수행합니다.

  * 클래식 실행 계정을 만들려면 "관리 계정으로 실행" 섹션의 지침을 따릅니다. 
  * 기존 계정을 업데이트하려면 "PowerShell을 사용하여 Automation 계정 업데이트" 섹션에 있는 PowerShell 스크립트를 사용합니다.
* 새 실행 계정 및 클래식 실행 Automation 계정을 사용하여 인증하려면 [인증 코드 예제](#authentication-code-examples) 섹션에서 제공된 예제 코드를 사용하여 기존 Runbook을 수정해야 합니다.

    >[!NOTE]
    >실행 계정은 인증서 기반 서비스 주체를 사용하여 Resource Manager 리소스에 인증하는 데 사용됩니다. 클래식 실행 계정은 관리 인증서를 사용하여 Service Management 리소스에 대해 인증하는 데 사용됩니다.

## <a name="create-an-automation-account-from-the-azure-portal"></a>Azure Portal에서 Automation 계정 만들기
이 섹션에서는 Azure Portal에서 Azure Automation 계정을 만듭니다. 그러면 실행 계정 및 클래식 실행 계정이 모두 만들어집니다.

>[!NOTE]
>Automation 계정을 만들려면 서비스 관리자 역할의 구성원이거나 구독에 대한 액세스 권한을 부여하는 구독의 공동 관리자여야 합니다. 또한 해당 구독의 기본 Active Directory 인스턴스에 사용자로 추가되어야 합니다. 이 계정은 권한 있는 역할에 할당할 필요가 없습니다.
>
>구독의 공동 관리자 역할에 추가되기 전에 구독 Active Directory 인스턴스의 구성원이 아닌 경우 Active Directory에 게스트로 추가됩니다. 이 인스턴스에서는 **Automation 계정 추가** 블레이드에 대해 "만들 수 있는 사용 권한이 없습니다…" 경고를 받습니다.
>
>공동 관리자 역할에 처음 추가된 사용자는 구독 Active Directory 인스턴스에서 제거한 다음 다시 추가하여 Active Directory의 완전한 사용자로 만들 수 있습니다. Azure Portal의 **Azure Active Directory** 창에서 이 상황을 확인하려면 **사용자 및 그룹**을 선택한 다음 **모든 사용자**를 선택하거나 특정 사용자를 선택한 후 **프로필**을 선택합니다. 사용자 프로필에서 **사용자 유형** 속성의 값은 **Guest**와 같지 않아야 합니다.
>

1. 구독 관리자 역할의 멤버이자 구독의 공동 관리자인 계정으로 Azure Portal에 로그인합니다.

2. **자동화 계정**을 선택합니다.

3. **Automation 계정** 블레이드에서 **추가**를 클릭합니다.
**Automation 계정 추가** 블레이드가 열립니다.

 !["Automation 계정 추가" 블레이드](media/automation-sec-configure-azure-runas-account/create-automation-account-properties-b.png)

   > [!NOTE]
   > 사용자의 계정이 구독 관리자 역할의 구성원이자 구독의 공동 관리자가 아닌 경우 **Automation 계정 추가** 블레이드에서 다음과 같은 경고가 표시됩니다.
   >
   >![자동화 계정 경고 추가](media/automation-sec-configure-azure-runas-account/create-account-without-perms.png)
   >
   >

4. **Automation 계정 추가** 블레이드의 **이름** 상자에 새 Automation 계정에 대한 이름을 입력합니다.

5. 구독이 두 개 이상인 경우 다음을 수행합니다.

    a. **구독** 아래에서 새 계정의 구독을 지정합니다.

    b. **리소스 그룹** 아래에서 **새로 만들기** 또는 **기존 항목 사용**을 클릭합니다.

    c. **위치** 아래에서 Azure 데이터 센터를 지정합니다.

6. **Azure 실행 계정 만들기** 아래에서 **예**를 선택하고 **만들기**를 클릭합니다.

   > [!NOTE]
   > **아니요**를 선택하여 실행 계정을 만들지 않는 경우 **Automation 계정 추가** 블레이드에 경고 메시지가 나타납니다. Azure Portal에서 계정이 생성되는 반면 클래식 또는 Resource Manager 구독 디렉터리 서비스 내에 해당하는 인증 ID가 없습니다. 따라서 계정에는 구독의 리소스에 대한 액세스 권한이 없습니다. 이 시나리오는 이 계정을 참조하는 Runbook이 그러한 배포 모델의 리소스에 대해 작업을 인증하고 수행하지 않도록 방지합니다.
   >
   > !["Automation 계정 추가" 블레이드의 경고 메시지](media/automation-sec-configure-azure-runas-account/create-account-decline-create-runas-msg.png)
   >
   > 또한 서비스 주체가 만들어지지 않은 경우 참여자 역할은 할당되지 않습니다.
   >

7. Azure에서 자동화 계정을 만드는 동안 메뉴의 **알림** 에서 진행률을 추적할 수 있습니다.

### <a name="resources"></a>리소스
자동화 계정이 성공적으로 만들어지면 몇 가지 리소스가 자동으로 만들어집니다. 리소스는 다음과 같은 두 개의 테이블에 요약되어 있습니다.

#### <a name="run-as-account-resources"></a>실행 계정 리소스

| 리소스 | 설명 |
| --- | --- |
| AzureAutomationTutorial Runbook | 실행 계정을 사용하여 인증하고 Resource Manager 리소스를 모두 가져오는 방법을 보여주는 예제 그래픽 Runbook입니다. |
| AzureAutomationTutorialScript Runbook | 실행 계정을 사용하여 인증하고 Resource Manager 리소스를 모두 가져오는 방법을 보여주는 예제 PowerShell Runbook입니다. |
| AzureRunAsCertificate | Automation 계정을 만들거나 기존 계정에 대해 다음 PowerShell 스크립트를 사용하는 경우 자동으로 만들어지는 인증서 자산입니다. 인증서를 사용하면 Runbook에서 Azure Resource Manager 리소스를 관리할 수 있도록 Azure로 인증할 수 있습니다. 인증서의 수명은 1년입니다. |
| AzureRunAsConnection | Automation 계정을 만들거나 기존 계정에 대해 PowerShell 스크립트를 사용하는 경우 자동으로 만들어지는 연결 자산입니다. |

#### <a name="classic-run-as-account-resources"></a>클래식 실행 계정 리소스

| 리소스 | 설명 |
| --- | --- |
| AzureClassicAutomationTutorial Runbook | 클래식 실행 계정(인증서)을 사용하여 구독의 모든 클래식 배포 모델을 사용하여 만든 VM을 가져온 다음 VM 이름 및 상태를 작성하는 예제 그래픽 Runbook입니다. |
| AzureClassicAutomationTutorial Script Runbook | 클래식 실행 계정(인증서)을 사용하여 구독의 모든 클래식 VM을 가져온 다음 VM 이름 및 상태를 작성하는 예제 PowerShell Runbook입니다. |
| AzureClassicRunAsCertificate | Runbook에서 Azure 클래식 리소스를 관리할 수 있도록 Azure에 인증하는 데 사용되는 자동으로 생성된 인증서 자산입니다. 인증서의 수명은 1년입니다. |
| AzureClassicRunAsConnection | Runbook에서 Azure 클래식 리소스를 관리할 수 있도록 Azure에 인증하는 데 사용되는 자동으로 생성된 연결 자산입니다. |

## <a name="verify-run-as-authentication"></a>실행 인증 확인
작은 테스트를 수행하여 새 실행 계정을 사용하여 성공적으로 인증할 수 있는지 확인합니다.

1. Azure 포털에서 이전에 만든 자동화 계정을 엽니다.

2. **Runbook** 타일을 클릭하여 Runbook 목록을 엽니다.

3. **AzureAutomationTutorialScript** Runbook을 선택한 다음 **시작**을 클릭하여 Runbook을 시작합니다. 다음 이벤트가 발생합니다.
 * [Runbook 작업](automation-runbook-execution.md)이 만들어지고, **작업** 블레이드가 표시되며, **작업 요약** 타일에 작업 상태가 표시됩니다.
 * 작업 상태는 클라우드의 Runbook worker를 사용할 수 있을 때까지 기다리고 있음을 나타내는 **대기 중**으로 시작됩니다.
 * 작업자가 작업을 클레임하는 경우 상태는 **시작 중**이 됩니다.
 * Runbook이 실행되기 시작할 때 상태는 **실행 중**이 됩니다.
 * Runbook 작업의 실행이 완료되면 **완료됨**이라는 상태가 표시됩니다.

       ![Security Principal Runbook Test](media/automation-sec-configure-azure-runas-account/job-summary-automationtutorialscript.png)
4. Runbook의 자세한 결과를 보려면 **출력** 타일을 클릭합니다.  
**출력** 블레이드가 표시되고 Runbook이 리소스 그룹에서 사용할 수 있는 모든 리소스의 목록이 성공적으로 인증되고 반환되는 것을 볼 수 있습니다.

5. **출력** 블레이드를 닫으면 **작업 요약** 블레이드로 돌아갑니다.

6. **작업 요약** 블레이드 및 해당 **AzureAutomationTutorialScript** Runbook 블레이드를 닫습니다.

## <a name="verify-classic-run-as-authentication"></a>클래식 실행 인증 확인
새 클래식 실행 계정을 사용하여 성공적으로 인증될 수 있는지 확인하는 유사한 작은 테스트를 수행합니다.

1. Azure 포털에서 이전에 만든 자동화 계정을 엽니다.

2. **Runbook** 타일을 클릭하여 Runbook 목록을 엽니다.

3. **AzureClassicAutomationTutorialScript** Runbook을 선택한 다음 **시작**을 클릭하여 Runbook을 시작합니다. 다음 이벤트가 발생합니다.

 * [Runbook 작업](automation-runbook-execution.md)이 만들어지고, **작업** 블레이드가 표시되며, **작업 요약** 타일에 작업 상태가 표시됩니다.
 * 작업 상태는 클라우드의 Runbook worker를 사용할 수 있을 때까지 기다리고 있음을 나타내는 **대기 중**으로 시작됩니다.
 * 작업자가 작업을 클레임하는 경우 상태는 **시작 중**이 됩니다.
 * Runbook이 실행되기 시작할 때 상태는 **실행 중**이 됩니다.
 * Runbook 작업의 실행이 완료되면 **완료됨**이라는 상태가 표시됩니다.

    ![보안 주체 Runbook 테스트](media/automation-sec-configure-azure-runas-account/job-summary-automationclassictutorialscript.png)<br>
4. Runbook의 자세한 결과를 보려면 **출력** 타일을 클릭합니다.  
**출력** 블레이드가 표시되고 Runbook이 구독에서 모든 클래식 VM 목록을 성공적으로 인증하고 반환한 것을 볼 수 있습니다.

5. **출력** 블레이드를 닫으면 **작업 요약** 블레이드로 돌아갑니다.

6. **작업 요약** 블레이드 및 해당 **AzureAutomationTutorialScript** Runbook 블레이드를 닫습니다.

## <a name="managing-your-run-as-account"></a>Azure 실행 계정 관리
Automation 계정 만료되기 전인 어떤 시점에서 인증서를 갱신해야 합니다. 실행 계정이 손상되었다고 생각하시면 삭제하고 다시 만들 수 있습니다. 이 섹션에서는 이러한 작업을 수행하는 방법을 설명합니다.

### <a name="self-signed-certificate-renewal"></a>자체 서명된 인증서 갱신
실행 계정에 만든 자체 서명된 인증서는 생성 날짜로부터 1년이 되는 날에 만료됩니다. 만료되기 전에 언제든지 갱신할 수 있습니다. 인증서를 갱신하면 큐에 대기하거나 활발하게 실행 중이며 실행 계정으로 인증되는 모든 Runbook이 부정적인 영향을 받지 않도록 현재 유효한 인증서가 보존됩니다. 인증서는 만료 날짜까지 유효합니다.

> [!NOTE]
> 엔터프라이즈 인증 기관에서 발급한 인증서를 사용하도록 Automation 실행 계정을 구성하고 이 옵션을 사용하는 경우 엔터프라이즈 인증서는 자체 서명된 인증서로 교체됩니다.

인증서를 갱신하려면 다음을 수행합니다.

1. Azure Portal에서 Automation 계정을 엽니다.

2. **Automation 계정** 블레이드의 **계정 속성** 창에서 **계정 설정** 섹션 아래에 있는 **실행 계정**을 선택합니다.

    ![Automation 계정 속성 창](media/automation-sec-configure-azure-runas-account/automation-account-properties-pane.png)
3. **실행 계정** 속성 블레이드에서 인증서를 갱신하려는 실행 계정 또는 클래식 실행 계정을 선택합니다.

4. 선택한 계정의 **속성** 블레이드에서 **인증서 갱신**을 클릭합니다.

    ![실행 계정용 인증서 갱신](media/automation-sec-configure-azure-runas-account/automation-account-renew-runas-certificate.png)

5. 인증서가 갱신되는 동안 메뉴의 **알림**에서 진행률을 추적할 수 있습니다.

### <a name="delete-a-run-as-or-classic-run-as-account"></a>실행 또는 클래식 실행 계정 삭제
이 섹션에서는 실행 또는 클래식 실행 계정을 삭제하고 다시 만드는 방법을 설명합니다. 이 작업을 실행하는 경우 Automation 계정이 보존됩니다. 실행 또는 클래식 실행 계정을 삭제한 후에 Azure Portal에서 계정을 다시 만들 수 있습니다.

1. Azure Portal에서 Automation 계정을 엽니다.

2. **Automation 계정** 블레이드의 계정 속성 창에서 **실행 계정**을 선택합니다.

3. **실행 계정** 속성 블레이드에서 삭제하려는 실행 계정 또는 클래식 실행 계정을 선택합니다. 그런 다음 선택한 계정의 **속성** 블레이드에서 **삭제**를 클릭합니다.

 ![실행 계정 삭제](media/automation-sec-configure-azure-runas-account/automation-account-delete-runas.png)

4. 계정이 삭제되는 동안 메뉴의 **알림** 에서 진행률을 추적할 수 있습니다.

5. 계정이 삭제되면 **실행 계정** 속성 블레이드에서 **Azure 실행 계정** 만들기 옵션을 선택하여 계정을 다시 만들 수 있습니다.

 ![Automation 실행 계정 다시 만들기](media/automation-sec-configure-azure-runas-account/automation-account-create-runas.png)

### <a name="misconfiguration"></a>잘못된 구성
실행 또는 클래식 실행 계정이 제대로 작동하는 데 필요한 일부 구성 항목이 초기 설정 중에 제대로 삭제되거나 생성되지 않았습니다. 항목은 다음과 같습니다.

* 인증서 자산
* 연결 자산
* 실행 계정이 참여자 역할에서 제거됨
* Azure AD의 서비스 주체 또는 응용 프로그램

잘못된 구성의 이전 및 다른 인스턴스에서 Automation 계정은 변경 사항을 감지하고 계정의 **실행 계정** 속성 블레이드에서 *불완전*이라는 상태를 표시합니다.

![불완전 실행 계정 구성 상태](media/automation-sec-configure-azure-runas-account/automation-account-runas-incomplete-config.png)

실행 계정을 선택하면 계정 **속성** 창은 다음과 같은 오류 메시지를 표시합니다.

![불완전 실행 계정 구성 경고 메시지](media/automation-sec-configure-azure-runas-account/automation-account-runas-incomplete-config-msg.png)등 4가지 유형의 클러스터가 제공됩니다.

계정을 삭제하고 다시 만들어서 이러한 실행 계정 문제를 신속하게 해결할 수 있습니다.

## <a name="update-your-automation-account-by-using-powershell"></a>PowerShell을 사용하여 Automation 계정 업데이트
다음과 같은 경우 기존 Automation 계정을 업데이트하기 위해 PowerShell을 사용할 수 있습니다.

* Automation 계정을 만들었지만 실행 계정 생성이 거부되었습니다.
* Automation 계정을 이미 사용하여 Resource Manager 리소스를 관리하고 Runbook 인증을 위한 실행 계정을 포함하도록 계정을 업데이트하려고 합니다.
* Automation 계정을 사용하여 클래식 리소스를 관리하며 새 계정을 만들어서 Runbook 및 자산을 마이그레이션하는 대신 클래식 실행 계정을 사용하여 업데이트해야 합니다.   
* 엔터프라이즈 CA(인증 기관)에서 발급한 인증서를 사용하여 실행 및 클래식 실행 계정을 만들려고 합니다.

스크립트에는 다음과 같은 필수 구성 요소가 필요합니다.

* 스크립트는 Azure Resource Manager 모듈 2.01 이상이 설치되어 있는 Windows 10 및Windows Server 2016에서만 실행될 수 있습니다. 이전 버전의 Windows에서는 지원되지 않습니다.
* Azure PowerShell 1.0 이상 PowerShell 1.0 릴리스에 대한 정보는 [Azure PowerShell 설치 및 구성 방법](/powershell/azureps-cmdlets-docs)을 참조하세요.
* Automation 계정은 다음 PowerShell 스크립트에서 *–AutomationAccountName* 및 *-ApplicationDisplayName* 매개 변수의 값으로 참조됩니다.

스크립트의 필수 매개 변수인 *SubscriptionID*, *ResourceGroup* 및 *AutomationAccountName*의 값을 가져오려면 다음을 수행합니다.
1. Azure Portal의 **Automation 계정** 블레이드에서 Automation 계정을 선택한 다음 **모든 설정**을 선택합니다. 
2. **모든 설정** 블레이드의 **계정 설정** 아래에서 **속성**을 선택합니다. 
3. **속성** 블레이드에 대한 값을 적어둡니다.

 ![Automation 계정 "속성" 블레이드](media/automation-sec-configure-azure-runas-account/automation-account-properties.png)  

### <a name="create-a-run-as-account-powershell-script"></a>실행 계정 PowerShell 스크립트 만들기
이 PowerShell 스크립트는 다음 구성에 대한 지원을 포함합니다.

* 자체 서명된 인증서를 사용하여 실행 계정을 만듭니다.
* 자체 서명된 인증서를 사용하여 실행 계정 및 클래식 실행 계정을 만듭니다.
* 엔터프라이즈 인증서를 사용하여 실행 계정 및 클래식 실행 계정을 만듭니다.
* Azure Government 클라우드에서 자체 서명된 인증서를 사용하여 실행 계정 및 클래식 실행 계정을 만듭니다.

선택한 구성 옵션에 따라 스크립트는 다음 항목을 만듭니다.

**실행 계정의 경우:**

* 자체 서명된 인증서 또는 엔터프라이즈 인증서 공개 키로 내보낼 Azure AD 응용 프로그램을 만들고, Azure AD에서 응용 프로그램의 서비스 주체 계정을 만들며, 현재 구독에서 이 계정의 참여자 역할을 할당합니다. 이 설정을 소유자 또는 다른 어떤 역할로든 변경할 수 있습니다. 자세한 내용은 [Azure Automation의 역할 기반 액세스 제어](automation-role-based-access-control.md)를 참조하세요.
* 지정된 Automation 계정에서 *AzureRunAsCertificate*라는 Automation 인증서 자산을 만듭니다. 인증서 자산은 Azure AD 응용 프로그램에서 사용되는 인증서 개인 키를 보유합니다.
* 지정된 Automation 계정에서 *AzureRunAsConnection*이라는 Automation 연결 자산을 만듭니다. 연결 자산은 applicationId, tenantId, subscriptionId 및 인증서 지문을 보유합니다.

**클래식 실행 계정의 경우:**

* 지정된 Automation 계정에서 *AzureClassicRunAsCertificate*라는 Automation 인증서 자산을 만듭니다. 인증서 자산은 관리 인증서에서 사용되는 인증서 개인 키를 보유합니다.
* 지정된 Automation 계정에서 *AzureClassicRunAsConnection*이라는 Automation 연결 자산을 만듭니다. 연결 자산은 구독 이름, subscriptionId 및 인증서 자산 이름을 보유합니다.

>[!NOTE]
> 클래식 실행 계정을 만드는 옵션을 선택한 경우 실행 스크립트를 실행한 후에 Automation 계정이 만들어진 구독의 관리 저장소에 공용 인증서(.cer 파일 이름 확장)를 업로드합니다.
> 

스크립트를 실행하고 인증서를 업로드하려면 다음을 수행합니다.

1. 컴퓨터에 다음 스크립트를 저장합니다. 이 예에서는 이를 *New-RunAsAccount.ps1*이라는 파일 이름으로 저장합니다.

        #Requires -RunAsAdministrator
         Param (
        [Parameter(Mandatory=$true)]
        [String] $ResourceGroup,

        [Parameter(Mandatory=$true)]
        [String] $AutomationAccountName,

        [Parameter(Mandatory=$true)]
        [String] $ApplicationDisplayName,

        [Parameter(Mandatory=$true)]
        [String] $SubscriptionId,

        [Parameter(Mandatory=$true)]
        [Boolean] $CreateClassicRunAsAccount,

        [Parameter(Mandatory=$true)]
        [String] $SelfSignedCertPlainPassword,

        [Parameter(Mandatory=$false)]
        [String] $EnterpriseCertPathForRunAsAccount,

        [Parameter(Mandatory=$false)]
        [String] $EnterpriseCertPlainPasswordForRunAsAccount,

        [Parameter(Mandatory=$false)]
        [String] $EnterpriseCertPathForClassicRunAsAccount,

        [Parameter(Mandatory=$false)]
        [String] $EnterpriseCertPlainPasswordForClassicRunAsAccount,

        [Parameter(Mandatory=$false)]
        [ValidateSet("AzureCloud","AzureUSGovernment")]
        [string]$EnvironmentName="AzureCloud",

        [Parameter(Mandatory=$false)]
        [int] $SelfSignedCertNoOfMonthsUntilExpired = 12
        )

        function CreateSelfSignedCertificate([string] $keyVaultName, [string] $certificateName, [string] $selfSignedCertPlainPassword,
                                      [string] $certPath, [string] $certPathCer, [string] $selfSignedCertNoOfMonthsUntilExpired ) {
        $Cert = New-SelfSignedCertificate -DnsName $certificateName -CertStoreLocation cert:\LocalMachine\My `
           -KeyExportPolicy Exportable -Provider "Microsoft Enhanced RSA and AES Cryptographic Provider" `
           -NotAfter (Get-Date).AddMonths($selfSignedCertNoOfMonthsUntilExpired)

        $CertPassword = ConvertTo-SecureString $selfSignedCertPlainPassword -AsPlainText -Force
        Export-PfxCertificate -Cert ("Cert:\localmachine\my\" + $Cert.Thumbprint) -FilePath $certPath -Password $CertPassword -Force | Write-Verbose
        Export-Certificate -Cert ("Cert:\localmachine\my\" + $Cert.Thumbprint) -FilePath $certPathCer -Type CERT | Write-Verbose
        }

        function CreateServicePrincipal([System.Security.Cryptography.X509Certificates.X509Certificate2] $PfxCert, [string] $applicationDisplayName) {  
        $CurrentDate = Get-Date
        $keyValue = [System.Convert]::ToBase64String($PfxCert.GetRawCertData())
        $KeyId = (New-Guid).Guid

        $KeyCredential = New-Object  Microsoft.Azure.Commands.Resources.Models.ActiveDirectory.PSADKeyCredential
        $KeyCredential.StartDate = $CurrentDate
        $KeyCredential.EndDate= [DateTime]$PfxCert.GetExpirationDateString()
        $KeyCredential.EndDate = $KeyCredential.EndDate.AddDays(-1)
        $KeyCredential.KeyId = $KeyId
        $KeyCredential.CertValue  = $keyValue

        # Use key credentials and create an Azure AD application
        $Application = New-AzureRmADApplication -DisplayName $ApplicationDisplayName -HomePage ("http://" + $applicationDisplayName) -IdentifierUris ("http://" + $KeyId) -KeyCredentials $KeyCredential
        $ServicePrincipal = New-AzureRMADServicePrincipal -ApplicationId $Application.ApplicationId
        $GetServicePrincipal = Get-AzureRmADServicePrincipal -ObjectId $ServicePrincipal.Id

        # Sleep here for a few seconds to allow the service principal application to become active (ordinarily takes a few seconds)
        Sleep -s 15
        $NewRole = New-AzureRMRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $Application.ApplicationId -ErrorAction SilentlyContinue
        $Retries = 0;
        While ($NewRole -eq $null -and $Retries -le 6)
        {
           Sleep -s 10
           New-AzureRMRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $Application.ApplicationId | Write-Verbose -ErrorAction SilentlyContinue
           $NewRole = Get-AzureRMRoleAssignment -ServicePrincipalName $Application.ApplicationId -ErrorAction SilentlyContinue
           $Retries++;
        }
           return $Application.ApplicationId.ToString();
        }

        function CreateAutomationCertificateAsset ([string] $resourceGroup, [string] $automationAccountName, [string] $certifcateAssetName,[string] $certPath, [string] $certPlainPassword, [Boolean] $Exportable) {
        $CertPassword = ConvertTo-SecureString $certPlainPassword -AsPlainText -Force   
        Remove-AzureRmAutomationCertificate -ResourceGroupName $resourceGroup -AutomationAccountName $automationAccountName -Name $certifcateAssetName -ErrorAction SilentlyContinue
        New-AzureRmAutomationCertificate -ResourceGroupName $resourceGroup -AutomationAccountName $automationAccountName -Path $certPath -Name $certifcateAssetName -Password $CertPassword -Exportable:$Exportable  | write-verbose
        }

        function CreateAutomationConnectionAsset ([string] $resourceGroup, [string] $automationAccountName, [string] $connectionAssetName, [string] $connectionTypeName, [System.Collections.Hashtable] $connectionFieldValues ) {
        Remove-AzureRmAutomationConnection -ResourceGroupName $resourceGroup -AutomationAccountName $automationAccountName -Name $connectionAssetName -Force -ErrorAction SilentlyContinue
        New-AzureRmAutomationConnection -ResourceGroupName $ResourceGroup -AutomationAccountName $automationAccountName -Name $connectionAssetName -ConnectionTypeName $connectionTypeName -ConnectionFieldValues $connectionFieldValues
        }

        Import-Module AzureRM.Profile
        Import-Module AzureRM.Resources

        $AzureRMProfileVersion= (Get-Module AzureRM.Profile).Version
        if (!(($AzureRMProfileVersion.Major -ge 2 -and $AzureRMProfileVersion.Minor -ge 1) -or ($AzureRMProfileVersion.Major -gt 2)))
        {
           Write-Error -Message "Please install the latest Azure PowerShell and retry. Relevant doc url : https://docs.microsoft.com/powershell/azureps-cmdlets-docs/ "
           return
        }

        Login-AzureRmAccount -EnvironmentName $EnvironmentName
        $Subscription = Select-AzureRmSubscription -SubscriptionId $SubscriptionId

        # Create a Run As account by using a service principal
        $CertifcateAssetName = "AzureRunAsCertificate"
        $ConnectionAssetName = "AzureRunAsConnection"
        $ConnectionTypeName = "AzureServicePrincipal"

        if ($EnterpriseCertPathForRunAsAccount -and $EnterpriseCertPlainPasswordForRunAsAccount) {
        $PfxCertPathForRunAsAccount = $EnterpriseCertPathForRunAsAccount
        $PfxCertPlainPasswordForRunAsAccount = $EnterpriseCertPlainPasswordForRunAsAccount
        } else {
          $CertificateName = $AutomationAccountName+$CertifcateAssetName
          $PfxCertPathForRunAsAccount = Join-Path $env:TEMP ($CertificateName + ".pfx")
          $PfxCertPlainPasswordForRunAsAccount = $SelfSignedCertPlainPassword
          $CerCertPathForRunAsAccount = Join-Path $env:TEMP ($CertificateName + ".cer")
          CreateSelfSignedCertificate $KeyVaultName $CertificateName $PfxCertPlainPasswordForRunAsAccount $PfxCertPathForRunAsAccount $CerCertPathForRunAsAccount $SelfSignedCertNoOfMonthsUntilExpired
        }

        # Create a service principal
        $PfxCert = New-Object -TypeName System.Security.Cryptography.X509Certificates.X509Certificate2 -ArgumentList @($PfxCertPathForRunAsAccount, $PfxCertPlainPasswordForRunAsAccount)
        $ApplicationId=CreateServicePrincipal $PfxCert $ApplicationDisplayName

        # Create the Automation certificate asset
        CreateAutomationCertificateAsset $ResourceGroup $AutomationAccountName $CertifcateAssetName $PfxCertPathForRunAsAccount $PfxCertPlainPasswordForRunAsAccount $true

        # Populate the ConnectionFieldValues
        $SubscriptionInfo = Get-AzureRmSubscription -SubscriptionId $SubscriptionId
        $TenantID = $SubscriptionInfo | Select TenantId -First 1
        $Thumbprint = $PfxCert.Thumbprint
        $ConnectionFieldValues = @{"ApplicationId" = $ApplicationId; "TenantId" = $TenantID.TenantId; "CertificateThumbprint" = $Thumbprint; "SubscriptionId" = $SubscriptionId}


        # Create an Automation connection asset named AzureRunAsConnection in the Automation account. This connection uses the service principal.
        CreateAutomationConnectionAsset $ResourceGroup $AutomationAccountName $ConnectionAssetName $ConnectionTypeName $ConnectionFieldValues

        if ($CreateClassicRunAsAccount) {
            # Create a Run As account by using a service principal
            $ClassicRunAsAccountCertifcateAssetName = "AzureClassicRunAsCertificate"
            $ClassicRunAsAccountConnectionAssetName = "AzureClassicRunAsConnection"
            $ClassicRunAsAccountConnectionTypeName = "AzureClassicCertificate "
            $UploadMessage = "Please upload the .cer format of #CERT# to the Management store by following the steps below." + [Environment]::NewLine +
                    "Log in to the Microsoft Azure Management portal (https://manage.windowsazure.com) and select Settings -> Management Certificates." + [Environment]::NewLine +
                    "Then click Upload and upload the .cer format of #CERT#"

             if ($EnterpriseCertPathForClassicRunAsAccount -and $EnterpriseCertPlainPasswordForClassicRunAsAccount ) {
             $PfxCertPathForClassicRunAsAccount = $EnterpriseCertPathForClassicRunAsAccount
             $PfxCertPlainPasswordForClassicRunAsAccount = $EnterpriseCertPlainPasswordForClassicRunAsAccount
             $UploadMessage = $UploadMessage.Replace("#CERT#", $PfxCertPathForClassicRunAsAccount)
        } else {
             $ClassicRunAsAccountCertificateName = $AutomationAccountName+$ClassicRunAsAccountCertifcateAssetName
             $PfxCertPathForClassicRunAsAccount = Join-Path $env:TEMP ($ClassicRunAsAccountCertificateName + ".pfx")
             $PfxCertPlainPasswordForClassicRunAsAccount = $SelfSignedCertPlainPassword
             $CerCertPathForClassicRunAsAccount = Join-Path $env:TEMP ($ClassicRunAsAccountCertificateName + ".cer")
             $UploadMessage = $UploadMessage.Replace("#CERT#", $CerCertPathForClassicRunAsAccount)
             CreateSelfSignedCertificate $KeyVaultName $ClassicRunAsAccountCertificateName $PfxCertPlainPasswordForClassicRunAsAccount $PfxCertPathForClassicRunAsAccount $CerCertPathForClassicRunAsAccount $SelfSignedCertNoOfMonthsUntilExpired
        }

        # Create the Automation certificate asset
        CreateAutomationCertificateAsset $ResourceGroup $AutomationAccountName $ClassicRunAsAccountCertifcateAssetName $PfxCertPathForClassicRunAsAccount $PfxCertPlainPasswordForClassicRunAsAccount $false

        # Populate the ConnectionFieldValues
        $SubscriptionName = $subscription.Subscription.SubscriptionName
        $ClassicRunAsAccountConnectionFieldValues = @{"SubscriptionName" = $SubscriptionName; "SubscriptionId" = $SubscriptionId; "CertificateAssetName" = $ClassicRunAsAccountCertifcateAssetName}

        # Create an Automation connection asset named AzureRunAsConnection in the Automation account. This connection uses the service principal.
        CreateAutomationConnectionAsset $ResourceGroup $AutomationAccountName $ClassicRunAsAccountConnectionAssetName $ClassicRunAsAccountConnectionTypeName $ClassicRunAsAccountConnectionFieldValues

        Write-Host -ForegroundColor red $UploadMessage
        }

2. 사용자 컴퓨터에서 **시작**을 클릭한 다음 관리자 권한으로**Windows PowerShell**을 시작합니다.

3. 승격된 PowerShell 명령줄 셸에서 1단계에서 만든 스크립트가 포함된 폴더로 이동합니다.

4. 필요한 구성에 대한 매개 변수 값을 사용하여 스크립트를 실행합니다.

    **자체 서명된 인증서를 사용하여 실행 계정 만들기**  
    `.\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $false`

    **자체 서명된 인증서를 사용하여 실행 계정 및 클래식 실행 계정 만들기**  
    `.\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true`

    **엔터프라이즈 인증서를 사용하여 실행 계정 및 클래식 실행 계정 만들기**  
    `.\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication>  -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true -EnterpriseCertPathForRunAsAccount <EnterpriseCertPfxPathForRunAsAccount> -EnterpriseCertPlainPasswordForRunAsAccount <StrongPassword> -EnterpriseCertPathForClassicRunAsAccount <EnterpriseCertPfxPathForClassicRunAsAccount> -EnterpriseCertPlainPasswordForClassicRunAsAccount <StrongPassword>`

    **Azure Government 클라우드에서 자체 서명된 인증서를 사용하여 실행 계정 및 클래식 실행 계정 만들기**  
    `.\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true  -EnvironmentName AzureUSGovernment`

    > [!NOTE]
    > 스크립트를 실행한 후에 Azure에 인증할지를 묻는 메시지가 표시됩니다. 구독 관리자 역할의 구성원이자 구독의 공동 관리자인 계정으로 로그인합니다.
    >
    >

스크립트가 성공적으로 실행된 후에 다음을 적어둡니다.
* 자체 서명된 공용 인증서(.cer 파일)를 사용하여 클래식 실행 계정을 만든 경우 스크립트는 해당 계정을 만들고 PowerShell 세션을 실행하는 데 사용된 사용자 프로필(*%USERPROFILE%\AppData\Local\Temp*)의 컴퓨터에 있는 임시 파일 폴더에 저장합니다.
* 엔터프라이즈 공용 인증서(.cer 파일)를 사용하여 클래식 실행 계정을 만든 경우 이 인증서를 사용합니다. [Azure 클래식 포털에 관리 API 인증서를 업로드](../azure-api-management-certs.md)하는 지침을 수행한 다음 [Service Management 리소스에 인증하기 위한 샘플 코드](#sample-code-to-authenticate-with-service-management-resources)를 사용하여 Service Management 리소스에서 자격 증명 구성의 유효성을 검사합니다. 
* 클래식 실행 계정을 만들지 *않은* 경우 [Service Management 리소스에 인증하기 위한 샘플 코드](#sample-code-to-authenticate-with-resource-manager-resources)를 사용하여 Resource Manager 리소스에 인증하고 자격 증명 구성의 유효성을 검사합니다.

## <a name="sample-code-to-authenticate-with-resource-manager-resources"></a>Resource Manager 리소스를 사용하여 인증하는 샘플 코드
Runbook으로 Resource Manager 리소스를 관리하는 실행 계정을 사용하여 인증하기 위해 *AzureAutomationTutorialScript* 예제 Runbook에서 가져온 다음의 업데이트된 샘플 코드를 사용할 수 있습니다.

    $connectionName = "AzureRunAsConnection"
    $SubId = Get-AutomationVariable -Name 'SubscriptionId'
    try
    {
       # Get the connection "AzureRunAsConnection "
       $servicePrincipalConnection=Get-AutomationConnection -Name $connectionName         

       "Signing in to Azure..."
       Add-AzureRmAccount `
         -ServicePrincipal `
         -TenantId $servicePrincipalConnection.TenantId `
         -ApplicationId $servicePrincipalConnection.ApplicationId `
         -CertificateThumbprint $servicePrincipalConnection.CertificateThumbprint
       "Setting context to a specific subscription"     
       Set-AzureRmContext -SubscriptionId $SubId              
    }
    catch {
        if (!$servicePrincipalConnection)
        {
           $ErrorMessage = "Connection $connectionName not found."
           throw $ErrorMessage
         } else{
            Write-Error -Message $_.Exception
            throw $_.Exception
         }
    }

여러 구독 간에 쉽게 작업할 수 있도록 스크립트에는 구독 컨텍스트를 참조하기 위해 지원되는 두 개의 코드 줄이 추가로 포함됩니다. *SubscriptionId*라는 변수 자산에는 구독의 ID가 포함됩니다. `Add-AzureRmAccount` cmdlet 문 다음에 [`Set-AzureRmContext`](https://msdn.microsoft.com/library/mt619263.aspx) cmdlet은 매개 변수 집합 *-SubscriptionId*로 시작됩니다. 변수 이름이 너무 일반적인 경우 식별하기 쉽도록 수정하여 접두사를 포함하거나 다른 명명 규칙을 사용할 수 있습니다. 또한 해당하는 변수 자산이 있는 *-SubscriptionId* 대신 *-SubscriptionName* 매개 변수 집합을 사용할 수 있습니다.

Runbook `Add-AzureRmAccount`에서 인증에 사용되는 cmdlet은 *ServicePrincipalCertificate* 매개 변수 집합을 사용합니다. 사용자 자격 증명이 아니라 서비스 주체 인증서를 사용하여 인증합니다.

## <a name="sample-code-to-authenticate-with-service-management-resources"></a>Service Management 리소스를 사용하여 인증하는 샘플 코드
Runbook으로 클래식 리소스를 관리하는 클래식 실행 계정을 사용하여 인증하기 위해 *AzureClassicAutomationTutorialScript* 예제 Runbook에서 가져온 다음의 업데이트된 샘플 코드를 사용할 수 있습니다.

    $ConnectionAssetName = "AzureClassicRunAsConnection"
    # Get the connection
    $connection = Get-AutomationConnection -Name $connectionAssetName        

    # Authenticate to Azure with certificate
    Write-Verbose "Get connection asset: $ConnectionAssetName" -Verbose
    $Conn = Get-AutomationConnection -Name $ConnectionAssetName
    if ($Conn -eq $null)
    {
       throw "Could not retrieve connection asset: $ConnectionAssetName. Assure that this asset exists in the Automation account."
    }

    $CertificateAssetName = $Conn.CertificateAssetName
    Write-Verbose "Getting the certificate: $CertificateAssetName" -Verbose
    $AzureCert = Get-AutomationCertificate -Name $CertificateAssetName
    if ($AzureCert -eq $null)
    {
       throw "Could not retrieve certificate asset: $CertificateAssetName. Assure that this asset exists in the Automation account."
    }

    Write-Verbose "Authenticating to Azure with certificate." -Verbose
    Set-AzureSubscription -SubscriptionName $Conn.SubscriptionName -SubscriptionId $Conn.SubscriptionID -Certificate $AzureCert
    Select-AzureSubscription -SubscriptionId $Conn.SubscriptionID

## <a name="next-steps"></a>다음 단계
* [Azure AD의 응용 프로그램 및 서비스 주체 개체](../active-directory/active-directory-application-objects.md)
* [Azure Automation의 역할 기반 액세스 제어](automation-role-based-access-control.md)
* [Azure Cloud Services의 인증서 개요](../cloud-services/cloud-services-certs-create.md)

