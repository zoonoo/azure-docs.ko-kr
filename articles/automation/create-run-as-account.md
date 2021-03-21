---
title: Azure Automation 실행 계정 만들기
description: 이 문서에서는 PowerShell 또는 Azure Portal에서 실행 계정을 만드는 방법을 설명 합니다.
services: automation
ms.subservice: process-automation
ms.date: 01/06/2021
ms.topic: conceptual
ms.openlocfilehash: ef6afff30da48b79b42e5fb4b3c72c3500f22dd1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102172306"
---
# <a name="how-to-create-an-azure-automation-run-as-account"></a>Azure Automation 실행 계정을 만드는 방법

Automation runbook 및 기타 자동화 기능을 사용 하 여 Azure Resource Manager 또는 Azure 클래식 배포 모델에서 리소스를 관리 하기 위한 인증을 제공 하 Azure Automation의 실행 계정. 이 문서에서는 Azure Portal 또는 Azure PowerShell에서 실행 계정 또는 클래식 실행 계정을 만드는 방법을 설명 합니다.

## <a name="create-account-in-azure-portal"></a>Azure Portal에서 계정 만들기

다음 단계를 수행하여 Azure Portal에서 Azure Automation 계정을 업데이트합니다. 실행 계정 및 클래식 실행 계정은 별도로 생성 됩니다. 클래식 리소스를 관리할 필요가 없으면 Azure 실행 계정만 만들면 됩니다.

1. 구독 관리자 역할의 멤버이자 구독의 공동 관리자인 계정으로 Azure Portal에 로그인합니다.

2. **Automation 계정** 을 검색하여 선택합니다.

3. **Automation 계정** 페이지의 목록에서 automation 계정을 선택 합니다.

4. 왼쪽 창의 **계정 설정** 섹션에서 **실행 계정** 을 선택 합니다.

    :::image type="content" source="media/create-run-as-account/automation-account-properties-pane.png" alt-text="실행 계정 옵션을 선택 합니다.":::

5. 필요한 계정에 따라 **+ Azure 실행 계정** 또는 **+ Azure 클래식 실행 계정** 창을 사용 합니다. 개요 정보를 검토한 후 **만들기** 를 클릭합니다.

    :::image type="content" source="media/create-run-as-account/automation-account-create-run-as.png" alt-text="실행 계정 만들기 옵션을 선택 합니다.":::

6. Azure에서 Automation 계정을 만드는 동안 메뉴의 **알림** 아래에서 진행 상황을 추적할 수 있습니다. 계정을 만드는 중임을 나타내는 배너도 표시됩니다. 이 프로세스를 완료하는 데 몇 분이 걸릴 수 있습니다.

## <a name="create-account-using-powershell"></a>PowerShell을 사용 하 여 계정 만들기

다음 목록에서는 제공 된 스크립트를 사용 하 여 PowerShell에서 실행 계정을 만들기 위한 요구 사항을 설명 합니다. 해당 요구 사항은 두 가지 실행 계정에 모두 적용됩니다.

* Azure Resource Manager 모듈 3.4.1 이상이 설치된 Windows 10 또는 Windows Server 2016. PowerShell 스크립트는 이전 버전의 Windows를 지원하지 않습니다.
* Azure PowerShell PowerShell 6.2.4 이상 자세한 내용은 [Azure PowerShell를 설치 하 고 구성 하는 방법](/powershell/azure/install-az-ps)을 참조 하세요.
* `AutomationAccountName` 및 `ApplicationDisplayName` 매개 변수의 값으로 참조되는 Automation 계정입니다.
* [실행 계정을 구성하는 데 필요한 권한](automation-security-overview.md#permissions)에 나열된 것과 동일한 권한입니다.

`AutomationAccountName` `SubscriptionId` PowerShell 스크립트의 필수 매개 변수인, 및에 대 한 값을 가져오려면 `ResourceGroupName` 다음 단계를 완료 합니다.

1. Azure Portal에 로그인합니다.

1. **Automation 계정** 을 검색하여 선택합니다.

1. Automation 계정 페이지의 목록에서 Automation 계정을 선택합니다.

1. 왼쪽 창에서 **속성** 을 선택합니다.

1. **속성** 페이지의 **이름**, **구독 ID** 및 **리소스 그룹** 에 대 한 값을 확인 합니다.

   ![Automation 계정 속성 창](media/create-run-as-account/automation-account-properties.png)

### <a name="powershell-script-to-create-a-run-as-account"></a>실행 계정을 만드는 PowerShell 스크립트

PowerShell 스크립트는 여러 구성에 대 한 지원을 포함 합니다.

* 자체 서명된 인증서를 사용하여 실행 계정을 만듭니다.
* 자체 서명 된 인증서를 사용 하 여 실행 계정 및/또는 클래식 실행 계정을 만듭니다.
* 엔터프라이즈 CA (인증 기관)에서 발급 한 인증서를 사용 하 여 실행 계정 및/또는 클래식 실행 계정을 만듭니다.
* Azure Government 클라우드에서 자체 서명 된 인증서를 사용 하 여 실행 계정 및/또는 클래식 실행 계정을 만듭니다.

1. 다음 명령을 사용 하 여 스크립트를 다운로드 하 여 로컬 폴더에 저장 합니다.

    ```powershell
    wget https://raw.githubusercontent.com/azureautomation/runbooks/master/Utility/AzRunAs/Create-RunAsAccount.ps1 -outfile Create-RunAsAccount.ps1
    ```

2. 관리자 권한으로 PowerShell을 시작 하 고 스크립트가 포함 된 폴더로 이동 합니다.

3. 다음 명령 중 하나를 실행 하 여 요구 사항에 따라 실행 및/또는 클래식 실행 계정을 만듭니다.

    * 자체 서명 된 인증서를 사용 하 여 실행 계정을 만듭니다.

        ```powershell
        .\Create-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $false
        ```

    * 자체 서명된 인증서를 사용하여 실행 계정 및 클래식 실행 계정을 만듭니다.

        ```powershell
        .\Create-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true
        ```

    * 엔터프라이즈 인증서를 사용하여 실행 계정 및 클래식 실행 계정을 만듭니다.

        ```powershell
        .\Create-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication>  -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true -EnterpriseCertPathForRunAsAccount <EnterpriseCertPfxPathForRunAsAccount> -EnterpriseCertPlainPasswordForRunAsAccount <StrongPassword> -EnterpriseCertPathForClassicRunAsAccount <EnterpriseCertPfxPathForClassicRunAsAccount> -EnterpriseCertPlainPasswordForClassicRunAsAccount <StrongPassword>
        ```

        엔터프라이즈 공용 인증서( .cer 파일)를 사용하여 클래식 실행 계정을 만든 경우 이 인증서를 사용합니다. 이 스크립트는 `%USERPROFILE%\AppData\Local\Temp` PowerShell 세션을 실행 하는 데 사용한 사용자 프로필에서 컴퓨터의 임시 파일 폴더에 파일을 만들어 저장 합니다. [관리 API 인증서를 Azure Portal에 업로드](../cloud-services/cloud-services-configure-ssl-certificate-portal.md)를 참조하세요.

    * Azure Government 클라우드에서 자체 서명된 인증서를 사용하여 실행 계정 및 클래식 실행 계정을 만듭니다.

        ```powershell
        .\Create-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true -EnvironmentName AzureUSGovernment
        ```

4. 스크립트를 실행한 후에 Azure에 인증할지 묻는 메시지가 표시됩니다. 구독 관리자 역할의 구성원 인 계정으로 로그인 합니다. 클래식 실행 계정을 만드는 경우 해당 계정은 구독의 공동 관리자 여야 합니다.

## <a name="next-steps"></a>다음 단계

* 그래픽 작성에 대해 자세히 알아보려면 [Azure Automation에서 그래픽 Runbook 작성](automation-graphical-authoring-intro.md)을 참조하세요.
* PowerShell Runbook을 시작하려면 [자습서: PowerShell Runbook 만들기](learn/automation-tutorial-runbook-textual-powershell.md)를 참조하세요.
* Python 3 runbook을 시작 하려면 [자습서: python 3 Runbook 만들기](learn/automation-tutorial-runbook-textual-python-3.md)를 참조 하세요.
