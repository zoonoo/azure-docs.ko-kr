---
title: "독립 실행형 Azure Automation 계정 만들기 | Microsoft Docs"
description: "Azure Automation에서 보안 주체 인증을 만들고 테스트하며 예제를 사용하는 과정을 안내하는 자습서입니다."
services: automation
documentationcenter: 
author: georgewallace
manager: carmonm
editor: 
ms.assetid: 2f783441-15c7-4ea0-ba27-d7daa39b1dd3
ms.service: automation
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/18/2017
ms.author: magoedte
ms.openlocfilehash: 0397b45753ea64d1a33916d5e0dff12d6e1d80aa
ms.sourcegitcommit: 9292e15fc80cc9df3e62731bafdcb0bb98c256e1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/10/2018
---
# <a name="create-a-standalone-azure-automation-account"></a>독립 실행형 Azure Automation 계정 만들기
이 항목에서는 Runbook 작업의 고급 모니터링을 제공하기 위해 OMS Log Analytics에서 추가 관리 솔루션 또는 통합을 포함하지 않고 Azure Automation을 평가하고 알아보려는 경우 Azure Portal에서 Automation 계정을 만드는 방법을 보여 줍니다.  해당 관리 솔루션을 추가하거나 나중에 언제든지 Log Analytics를 통합할 수 있습니다.  Automation 계정을 사용하여 Azure Resource Manager 또는 Azure 클래식 배포 중 하나에서 리소스를 관리하는 Runbook을 인증할 수 있습니다.

Azure Portal에서 Automation 계정을 만들 경우 다음을 자동으로 만듭니다.

* Azure Active Directory에서 새 서비스 주체, 인증서를 만들고 Runbook을 사용하여 Resource Manager 리소스를 관리하는 데 사용될 참여자 역할 기반 액세스 제어(RBAC)를 할당하는 실행 계정.   
* Runbook을 사용하여 클래식 리소스를 관리하는 데 사용되는 관리 인증서를 업로드하는 클래식 실행 계정.  

이를 통해 처리를 간편하게 만들고 자동화 요구를 지원하는 Runbook의 구축 및 배포를 신속하게 시작할 수 있습니다.  

## <a name="permissions-required-to-create-automation-account"></a>Automation 계정을 만드는 데 필요한 권한
Automation 계정을 만들거나 업데이트하려면 이 항목을 완료하는 데 필요한 다음과 같은 특정 권한이 있어야 합니다.   
 
* Automation 계정을 만들려면 [Azure Automation에서 역할 기반 액세스 제어](automation-role-based-access-control.md) 문서에 설명된 대로 Microsoft.Automation 리소스에 대한 소유자 역할과 동일한 권한을 가진 역할에 AD 사용자 계정을 추가해야 합니다.  
* 앱 등록 설정이 **예**로 지정되어 있는 경우 Azure AD 테넌트의 관리자가 아닌 사용자는 [AD 응용 프로그램을 등록](../azure-resource-manager/resource-group-create-service-principal-portal.md#check-azure-subscription-permissions)할 수 있습니다.  앱 등록 설정이 **아니요**로 지정되어 있는 경우 이 작업을 수행하는 사용자는 Azure AD의 전역 관리자여야 합니다. 

구독의 전역 관리자/공동 관리자 역할에 추가되기 전에 구독 Active Directory 인스턴스의 멤버가 아닌 경우 Active Directory에 게스트로 추가됩니다. 이 상황에서는 “만들 수 있는 사용 권한이 없습니다…”라는 메시지를 받습니다. **Automation 계정 추가** 블레이드의 경고. 전역 관리자/공동 관리자 역할에 처음 추가된 사용자는 구독 Active Directory 인스턴스에서 제거한 다음 다시 추가하여 Active Directory의 완전한 사용자로 만들 수 있습니다. Azure Portal의 **Azure Active Directory** 창에서 이 상황을 확인하려면 **사용자 및 그룹**을 선택한 다음 **모든 사용자**를 선택하거나 특정 사용자를 선택한 후 **프로필**을 선택합니다. 사용자 프로필에서 **사용자 유형** 속성의 값은 **Guest**와 같지 않아야 합니다.

## <a name="create-a-new-automation-account-from-the-azure-portal"></a>Azure Portal에서 새 Automation 계정 만들기
이 섹션에서는 다음 단계를 수행하여 Azure Portal에서 Azure Automation 계정을 만듭니다.    

1. 구독 관리자 역할의 멤버이자 구독의 공동 관리자인 계정으로 Azure Portal에 로그인합니다.
2. **새로 만들기**를 클릭합니다.<br><br> ![Azure Portal에서 새 옵션을 선택합니다.](media/automation-offering-get-started/automation-portal-martketplacestart.png)<br>  
3. **Automation**을 검색한 다음 검색 결과에서 **Automation 및 컨트롤***을 선택합니다.<br><br> ![Marketplace에서 Automation을 검색하고 선택합니다.](media/automation-create-standalone-account/automation-marketplace-select-create-automationacct.png)<br> 
3. Automation 계정 블레이드에서 **추가**를 클릭합니다.<br><br>![Automation 계정 추가](media/automation-create-standalone-account/automation-create-automationacct-properties.png)


   > [!NOTE]
   > 계정이 구독 관리자 역할의 멤버이자 구독의 공동 관리자가 아니기 때문에 **Automation 계정 추가** 블레이드에서 다음과 같은 경고 메시지가 표시됩니다.<br><br>![Automation 계정 경고 추가](media/automation-create-standalone-account/create-account-without-perms.png)
   > 
   > 
4. **Automation 계정 추가** 블레이드의 **이름** 상자에 새 Automation 계정에 대한 이름을 입력합니다.
5. 구독이 둘 이상인 경우 새 계정의 구독을 지정하고 새로운 또는 기존 **리소스 그룹** 및 Azure 데이터 센터 **위치**를 지정합니다.
6. **Azure 실행 계정 만들기** 옵션에 **예** 값을 선택했는지 확인하고 **만들기** 단추를 클릭합니다.  
   
   > [!NOTE]
   > **아니요** 옵션을 선택하여 실행 계정을 만들지 않는 경우 **Automation 계정 추가** 블레이드에 경고 메시지가 나타납니다.  Azure Portal에서 계정이 생성되는 동안, 클래식 또는 리소스 관리자 구독 디렉터리 서비스 내에 해당하는 인증 ID가 없으므로 구독의 리소스에 대한 액세스 권한도 없습니다.  이렇게 하면 이 계정을 참조하는 Runbook이 그러한 배포 모델의 리소스에 대해 작업을 인증하고 수행하지 못하도록 방지합니다.
   > 
   > ![Automation 계정 경고 추가](media/automation-create-standalone-account/create-account-decline-create-runas-msg.png)<br>
   > 서비스 주체가 만들어지지 않은 경우 참여자 역할은 할당되지 않습니다.
   > 

7. Azure에서 Automation 계정을 만드는 동안 메뉴의 **알림** 에서 진행률을 추적할 수 있습니다.

### <a name="resources-included"></a>포함된 리소스
Automation 계정이 성공적으로 만들어지면 몇 가지 리소스가 자동으로 만들어집니다.  다음 표에는 실행 계정에 대한 리소스가 요약되어 있습니다.<br>

| 리소스 | 설명 |
| --- | --- |
| AzureAutomationTutorial Runbook |실행 계정을 사용하여 인증하고 Resource Manager 리소스를 모두 가져오는 방법을 보여주는 예제 그래픽 Runbook입니다. |
| AzureAutomationTutorialScript Runbook |실행 계정을 사용하여 인증하고 Resource Manager 리소스를 모두 가져오는 방법을 보여주는 예제 PowerShell Runbook입니다. |
| AzureAutomationTutorialPython2 Runbook |실행 계정을 사용하여 인증하는 방법을 보여 준 다음 지정된 구독에 있는 리소스 그룹을 나열하는 예제 python Runbook입니다. |
| AzureRunAsCertificate |Automation 계정을 만드는 동안 또는 기존 계정에 대해 아래의 PowerShell 스크립트를 사용한 경우 생성되는 인증서 자산입니다.  Runbook에서 Azure Resource Manager 리소스를 관리할 수 있도록 Azure로 인증할 수 있도록 해줍니다.  이 인증서는 수명이 1년입니다. |
| AzureRunAsConnection |Automation 계정을 만드는 동안 또는 기존 계정에 대해 아래의 PowerShell 스크립트를 사용한 경우 생성되는 연결 자산입니다. |

다음 표에는 클래식 실행 계정에 대한 리소스가 요약되어 있습니다.<br>

| 리소스 | 설명 |
| --- | --- |
| AzureClassicAutomationTutorial Runbook |클래식 실행 계정(인증서)을 사용하여 구독의 모든 클래식 VM을 가져온 다음 VM 이름 및 상태를 출력하는 예제 그래픽 Runbook입니다. |
| AzureClassicAutomationTutorial Script Runbook |클래식 실행 계정(인증서)을 사용하여 구독의 모든 클래식 VM을 가져온 다음 VM 이름 및 상태를 출력하는 예제 PowerShell Runbook입니다. |
| AzureClassicRunAsCertificate |Runbook의 Azure 클래식 리소스를 관리할 수 있도록 Azure를 통해 인증하는 데 사용되는 자동 생성 인증서 자산입니다.  이 인증서는 수명이 1년입니다. |
| AzureClassicRunAsConnection |Runbook의 Azure 클래식 리소스를 관리할 수 있도록 Azure를 통해 인증하는 데 사용되는 자동 생성 연결 자산입니다. |


## <a name="next-steps"></a>다음 단계
* 그래픽 작성에 대해 자세히 알아보려면 [Azure Automation에서 그래픽 작성](automation-graphical-authoring-intro.md)을 참조하세요.
* PowerShell Runbook을 시작하려면 [내 첫 번째 PowerShell Runbook](automation-first-runbook-textual-powershell.md)을 참조하세요.
* PowerShell 워크플로 Runbook을 시작하려면 [내 첫 번째 PowerShell 워크플로 Runbook](automation-first-runbook-textual.md)을 참조하세요.
* Python2 Runbook을 시작하려면 [내 첫 번째 Python2 Runbook](automation-first-runbook-textual-python2.md)을 참조하세요.
