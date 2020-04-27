---
title: Azure Automation Runbook에서 이메일 보내기
description: SendGrid를 사용하여 Runbook 내에서 이메일을 보내는 방법에 대해 알아봅니다.
services: automation
ms.subservice: process-automation
ms.date: 07/15/2019
ms.topic: tutorial
ms.openlocfilehash: d4b35458c76da82b33dfcb530cfdc71ee3da3bb6
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/17/2020
ms.locfileid: "81604776"
---
# <a name="tutorial-send-an-email-from-an-azure-automation-runbook"></a>자습서: Azure Automation Runbook에서 이메일 보내기

PowerShell을 통해 [SendGrid](https://sendgrid.com/solutions)를 사용하여 Runbook에서 이메일을 보낼 수 있습니다. 이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
>
> * Azure Key Vault를 만듭니다.
> * 키 자격 증명 모음에 `SendGrid` API 키를 저장합니다.
> * [Azure Key Vault](/azure/key-vault/)에 저장된 API 키를 사용하여 API 키를 검색하고 이메일을 보내는 재사용 가능한 Runbook을 만듭니다.

>[!NOTE]
>이 문서는 새 Azure PowerShell Az 모듈을 사용하도록 업데이트되었습니다. AzureRM 모듈은 적어도 2020년 12월까지 버그 수정을 수신할 예정이므로 계속 사용하셔도 됩니다. 새 Az 모듈 및 AzureRM 호환성에 대한 자세한 내용은 [새 Azure PowerShell Az 모듈 소개](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)를 참조하세요. Hybrid Runbook Worker에 대한 Az 모듈 설치 지침은 [Azure PowerShell 모듈 설치](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)를 참조하세요. Automation 계정의 경우 [Azure Automation에서 Azure PowerShell 모듈을 업데이트하는 방법](automation-update-azure-modules.md)을 사용하여 모듈을 최신 버전으로 업데이트할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

이 자습서를 완료하려면 다음 항목이 필요합니다.

* Azure 구독: 구독이 아직 없는 경우 [MSDN 구독자 혜택을 활성화](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)하거나 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 등록할 수 있습니다.
* [SendGrid 계정 만들기](/azure/sendgrid-dotnet-how-to-send-email#create-a-sendgrid-account)
* Runbook을 저장하고 실행하기 위한 **Az** 모듈이 포함된 [Automation 계정](automation-offering-get-started.md) 및 [실행 연결](automation-create-runas-account.md)

## <a name="create-an-azure-key-vault"></a>Azure Key Vault 만들기

다음 PowerShell 스크립트를 사용하여 Azure Key Vault를 만들 수 있습니다. 변수 값을 사용자 환경에 맞는 값으로 바꿉니다. 코드 블록의 오른쪽 위에 있는 **사용해 보세요.** 단추를 통해 포함된 Azure Cloud Shell을 사용합니다. [Azure PowerShell 모듈](/powershell/azure/install-az-ps)이 로컬 머신에 설치되어 있는 경우 로컬에서 코드를 복사하고 실행할 수도 있습니다.

> [!NOTE]
> API 키를 검색하려면 [SendGrid API 찾기](/azure/sendgrid-dotnet-how-to-send-email#to-find-your-sendgrid-api-key)에 있는 단계를 사용합니다.

```azurepowershell-interactive
$SubscriptionId  =  "<subscription ID>"

# Sign in to your Azure account and select your subscription
# If you omit the SubscriptionId parameter, the default subscription is selected.
Connect-AzAccount -SubscriptionId $SubscriptionId

# Use Get-AzLocation to see your available locations.
$region = "southcentralus"
$KeyVaultResourceGroupName  = "mykeyvaultgroup"
$VaultName = "<Enter a universally unique vault name>"
$SendGridAPIKey = "<SendGrid API key>"
$AutomationAccountName = "testaa"

# Create new Resource Group, or omit this step if you already have a resource group.
New-AzResourceGroup -Name $KeyVaultResourceGroupName -Location $region

# Create the new key vault
$newKeyVault = New-AzKeyVault -VaultName $VaultName -ResourceGroupName $KeyVaultResourceGroupName -Location $region
$resourceId = $newKeyVault.ResourceId

# Convert the SendGrid API key into a SecureString
$Secret = ConvertTo-SecureString -String $SendGridAPIKey -AsPlainText -Force
Set-AzKeyVaultSecret -VaultName $VaultName -Name 'SendGridAPIKey' -SecretValue $Secret

# Grant access to the Key Vault to the Automation Run As account.
$connection = Get-AzAutomationConnection -ResourceGroupName $KeyVaultResourceGroupName -AutomationAccountName $AutomationAccountName -Name AzureRunAsConnection
$appID = $connection.FieldDefinitionValues.ApplicationId
Set-AzKeyVaultAccessPolicy -VaultName $VaultName -ServicePrincipalName $appID -PermissionsToSecrets Set, Get
```

Azure Key Vault를 만들고 비밀을 저장하는 다른 방법은 [Key Vault 빠른 시작](/azure/key-vault/)을 참조하세요.

## <a name="import-required-modules-to-your-automation-account"></a>Automation 계정에 필요한 모듈 가져오기

Runbook 내에서 Azure Key Vault를 사용하려면 Automation 계정에 다음 모듈이 필요합니다.

* [Az.Profile](https://www.powershellgallery.com/packages/Az.Profile)
* [Az.KeyVault](https://www.powershellgallery.com/packages/Az.KeyVault)

**설치 옵션** 아래의 Azure Automation 탭에서 **Azure Automation에 배포**를 클릭합니다. 이 작업을 수행하면 Azure Portal이 열립니다. 가져오기 페이지에서 Automation 계정을 선택하고 **확인**을 클릭합니다.

필요한 모듈을 추가하는 방법에 대한 자세한 내용은 [모듈 가져오기](/azure/automation/shared-resources/modules#importing-modules)를 참조하세요.

## <a name="create-the-runbook-to-send-an-email"></a>이메일을 보내는 Runbook 만들기

키 자격 증명 모음을 만들고 `SendGrid` API 키를 저장한 후에는 API 키를 검색하고 이메일을 보내는 Runbook을 만들 수 있습니다.

이 Runbook은 `AzureRunAsConnection`을 [실행 계정](automation-create-runas-account.md)으로 사용하여 Azure Key Vault에서 비밀을 검색하도록 Azure를 인증합니다.

이 예제를 사용하여 Runbook에서 호출한 **Send-GridMailMessage**를 만듭니다. PowerShell 스크립트를 수정하여 다른 시나리오에서 재사용할 수 있습니다.

1. Azure Automation 계정으로 이동합니다.
2. **프로세스 자동화** 아래에서 **Runbook**을 선택합니다.
3. Runbook의 목록 맨 위에서 **+ Runbook 만들기**를 선택합니다.
4. **Runbook 추가** 페이지에서 Runbook 이름에 **Send-GridMailMessage**를 입력합니다. Runbook 형식으로 **PowerShell**을 선택합니다. 그런 다음 **만들기**를 선택합니다.
   ![Runbook 만들기](./media/automation-send-email/automation-send-email-runbook.png)
5. Runbook이 만들어지고 **PowerShell Runbook 편집** 페이지가 열립니다.
   ![Runbook 편집](./media/automation-send-email/automation-send-email-edit.png)
6. 다음 PowerShell 예제를 **편집** 페이지에 복사합니다. `$VaultName`이 키 자격 증명 모음을 만들 때 지정한 이름인지 확인합니다.

    ```powershell-interactive
    Param(
      [Parameter(Mandatory=$True)]
      [String] $destEmailAddress,
      [Parameter(Mandatory=$True)]
      [String] $fromEmailAddress,
      [Parameter(Mandatory=$True)]
      [String] $subject,
      [Parameter(Mandatory=$True)]
      [String] $content
    )

    $Conn = Get-AutomationConnection -Name AzureRunAsConnection
    Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint | Out-Null
    $VaultName = "<Enter your vault name>"
    $SENDGRID_API_KEY = (Get-AzKeyVaultSecret -VaultName $VaultName -Name "SendGridAPIKey").SecretValueText
    $headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]"
    $headers.Add("Authorization", "Bearer " + $SENDGRID_API_KEY)
    $headers.Add("Content-Type", "application/json")

    $body = @{
    personalizations = @(
        @{
            to = @(
                    @{
                        email = $destEmailAddress
                    }
            )
        }
    )
    from = @{
        email = $fromEmailAddress
    }
    subject = $subject
    content = @(
        @{
            type = "text/plain"
            value = $content
        }
    )
    }

    $bodyJson = $body | ConvertTo-Json -Depth 4

    $response = Invoke-RestMethod -Uri https://api.sendgrid.com/v3/mail/send -Method Post -Headers $headers -Body $bodyJson
    ```

7. **게시**를 선택하여 Runbook을 저장하고 게시합니다.

Runbook이 성공적으로 실행되는지 확인하려면 [Runbook 테스트](manage-runbooks.md#testing-a-runbook) 또는 [Runbook 시작](start-runbooks.md) 아래의 단계를 수행할 수 있습니다.
처음에 테스트 이메일이 보이지 않으면 **정크** 및 **스팸** 폴더를 확인하세요.

## <a name="clean-up"></a>정리

더 이상 필요하지 않은 경우 Runbook을 삭제합니다. 이렇게 하려면 Runbook 목록에서 Runbook을 선택하고 **삭제**를 클릭합니다.

[Remove-AzKeyVault](https://docs.microsoft.com/powershell/module/az.keyvault/remove-azkeyvault?view=azps-3.7.0) cmdlet을 사용하여 키 자격 증명 모음을 삭제합니다.

```azurepowershell-interactive
$VaultName = "<your KeyVault name>"
$ResourceGroupName = "<your ResourceGroup name>"
Remove-AzKeyVault -VaultName $VaultName -ResourceGroupName $ResourceGroupName
```

## <a name="next-steps"></a>다음 단계

* Runbook 만들기 또는 시작과 관련된 문제는 [Runbook 오류 문제 해결](./troubleshoot/runbooks.md)을 참조하세요.
* Automation 계정에서 모듈을 업데이트하려면 [Azure Automation에서 Azure PowerShell 모듈을 업데이트하는 방법](automation-update-azure-modules.md)을 참조하세요.
* Runbook 실행을 모니터링하려면 [Automation에서 Azure Monitor 로그로 작업 상태 및 작업 스트림 전달](automation-manage-send-joblogs-log-analytics.md)을 참조하세요.
* 경고를 사용하여 Runbook을 트리거하려면 [경고를 사용하여 Azure Automation Runbook 트리거](automation-create-alert-triggered-runbook.md)를 참조하세요.
