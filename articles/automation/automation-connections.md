---
title: Azure Automation에서 연결 관리
description: Azure Automation의 연결 자산은 외부 서비스 또는 runbook이나 DSC 구성의 애플리케이션을 연결하는데 필요한 정보를 포함합니다. 이 문서에서는 연결에 대해 자세히 알아보고 텍스트 작성과 그래픽 작성 모두에서 연결을 사용하는 방법을 설명합니다.
services: automation
ms.subservice: shared-capabilities
ms.date: 01/13/2020
ms.topic: conceptual
ms.openlocfilehash: 39a41a60f4cabe995ebd458c4b906438d1e31bde
ms.sourcegitcommit: 086d7c0cf812de709f6848a645edaf97a7324360
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2020
ms.locfileid: "82097118"
---
# <a name="manage-connections-in-azure-automation"></a>Azure Automation에서 연결 관리

Automation 연결 자산은 외부 서비스 또는 runbook의 애플리케이션이나 DSC 구성 연결에 필요한 정보를 포함합니다. 여기에는 URL 또는 포트와 같은 연결 정보 외에 사용자 이름 및 암호와 같은 인증에 필요한 정보가 포함될 수 있습니다. 연결 값은 여러 변수를 만드는 대신, 특정 애플리케이션에 연결하기 위한 모든 속성을 하나의 자산에 유지합니다. 사용자는 한 위치의 연결에 대한 값을 편집할 수 있고 단일 매개 변수에서 연결 이름을 runbook 이나 DSC구성에 전달할 수 있습니다. `Get-AutomationConnection` 작업을 사용 하 여 RUNBOOK 또는 DSC 구성에서 연결에 대 한 속성에 액세스할 수 있습니다.

연결을 만들 때는 연결 유형을 지정해야 합니다. 연결 형식은 속성 집합을 정의하는 템플릿입니다. 연결은 해당 연결 형식에 정의된 각 속성의 값을 정의합니다. 연결 형식은 통합 모듈의 Azure Automation에 추가되어 있거나 통합 모듈에 연결 형식이 포함되어 있고 이 유형을 Automation 계정으로 가져올 경우 [Azure Automation API](/previous-versions/azure/reference/mt163818(v=azure.100))를 사용하여 만들어집니다. 그렇지 않으면 자동화 연결 형식을 지정 하는 메타 데이터 파일을 만들어야 합니다. 이에 대 한 자세한 내용은 [통합 모듈](automation-integration-modules.md)을 참조 하세요.

>[!NOTE]
>Azure Automation의 안전한 자산에는 자격 증명, 인증서, 연결, 암호화된 변수 등이 있습니다. 이러한 자산은 각 자동화 계정에 대해 생성 되는 고유 키를 사용 하 여 암호화 되 고 Azure Automation 저장 됩니다. 이 키는 시스템에서 관리하는 Key Vault에 저장됩니다. 보안 자산을 저장하기 전에 Key Vault에서 키가 로드된 다음, 자산을 암호화하는 데 사용됩니다. Azure Automation에서 이 프로세스를 관리합니다.

>[!NOTE]
>이 문서는 새 Azure PowerShell Az 모듈을 사용하도록 업데이트되었습니다. AzureRM 모듈은 적어도 2020년 12월까지 버그 수정을 수신할 예정이므로 계속 사용하셔도 됩니다. 새 Az 모듈 및 AzureRM 호환성에 대한 자세한 내용은 [새 Azure PowerShell Az 모듈 소개](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)를 참조하세요. Hybrid Runbook Worker에 대 한 Az module 설치 지침은 [Azure PowerShell 모듈 설치](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)를 참조 하세요. Automation 계정의 경우 [Azure Automation에서 Azure PowerShell 모듈을 업데이트 하는 방법을](automation-update-azure-modules.md)사용 하 여 모듈을 최신 버전으로 업데이트할 수 있습니다.

## <a name="connection-types"></a>연결 형식

Azure Automation에서 사용할 수 있는 기본 제공 연결에는 다음 세 가지 유형이 있습니다.

* **Azure** - 이 연결을 사용하여 클래식 리소스를 관리할 수 있습니다.
* **AzureClassicCertificate** - 이 연결은 **AzureClassicRunAs** 계정에 사용됩니다.
* **AzureServicePrincipal** - 이 연결은 **AzureRunAs** 계정에 사용됩니다.

대부분의 경우에는 [실행 계정을](manage-runas-account.md)만들 때 생성 되기 때문에 연결 리소스를 만들 필요가 없습니다.

## <a name="windows-powershell-cmdlets"></a>Windows PowerShell cmdlet

다음 테이블의 cmdlet은 Windows PowerShell을 사용하여 Automation 연결을 만들고 관리하는 데 사용됩니다. 자동화 runbook 및 DSC 구성에서 사용할 수 있는 [Azure PowerShell 모듈](/powershell/azure/overview)의 일부로 제공 됩니다.

|Cmdlet|Description|
|---|---|
|[AzAutomationConnection](https://docs.microsoft.com/powershell/module/az.automation/get-azautomationconnection?view=azps-3.7.0)|연결을 검색합니다. 연결 필드의 값을 사용 하 여 해시 테이블을 포함 합니다.|
|[AzAutomationConnection](https://docs.microsoft.com/powershell/module/az.automation/new-azautomationconnection?view=azps-3.7.0)|새 연결을 만듭니다.|
|[AzAutomationConnection](https://docs.microsoft.com/powershell/module/Az.Automation/Remove-AzAutomationConnection?view=azps-3.7.0)|기존 연결을 제거 합니다.|
|[AzAutomationConnectionFieldValue](https://docs.microsoft.com/powershell/module/Az.Automation/Set-AzAutomationConnectionFieldValue?view=azps-3.7.0)|기존 연결의 특정 필드 값을 설정합니다.|

## <a name="activities"></a>활동

다음 표의 활동은 runbook 또는 DSC 구성의 연결에 액세스하는데 사용됩니다.

|활동|Description|
|---|---|
|`Get-AutomationConnection` | 사용할 연결을 가져옵니다. 연결 속성이 포함된 해시 테이블을 반환합니다.|

>[!NOTE]
>의 `Name` `Get-AutomationConnection`매개 변수를 사용 하 여 변수를 사용 하지 마십시오. 이 매개 변수를 사용 하면 runbook과 DSC 구성 간의 종속성을 검색 하 고 디자인 타임에 연결 자산을 복잡 하 게 검색할 수 있습니다.

## <a name="python-2-functions"></a>Python 2 함수

다음 표의 함수는 Python 2 runbook의 연결에 액세스 하는 데 사용 됩니다.

| 함수 | Description |
|:---|:---|
| `automationassets.get_automation_connection` | 연결을 검색합니다. 연결의 속성이 있는 사전을 반환합니다. |

> [!NOTE]
> 자산 기능에 액세스 `automationassets` 하려면 Python runbook의 맨 위에 있는 모듈을 가져와야 합니다.

## <a name="creating-a-new-connection"></a>새 연결 만들기

### <a name="create-a-new-connection-with-the-azure-portal"></a>Azure Portal를 사용 하 여 새 연결을 만듭니다.

1. Automation 계정에서 **자산** 파트를 클릭 하 여 **자산** 블레이드를 엽니다.
2. **연결** 파트를 클릭하여 **연결** 블레이드를 엽니다.
3. 블레이드의 위쪽에서 **연결 추가** 를 클릭합니다.
4. **형식** 드롭다운에서 만들려는 연결 형식을 선택합니다. 양식에 해당 특정 형식에 대한 속성이 표시됩니다.
5. 양식을 완료하고 **만들기** 를 클릭하여 새 연결을 저장합니다.

### <a name="create-a-new-connection-with-windows-powershell"></a>Windows PowerShell을 사용 하 여 새 연결 만들기

Cmdlet을 `New-AzAutomationConnection` 사용 하 여 Windows PowerShell을 사용 하 여 새 연결을 만듭니다. 이 cmdlet에는 연결 형식 `ConnectionFieldValues` 으로 정의 된 각 속성에 대 한 값을 정의 하는 [해시 테이블이](https://technet.microsoft.com/library/hh847780.aspx) 필요한 라는 매개 변수가 있습니다.

포털에서 실행 계정을 만드는 대신 다음 예제 명령을 사용 하 여 새 연결 자산을 만들 수 있습니다.

```powershell
$ConnectionAssetName = "AzureRunAsConnection"
$ConnectionFieldValues = @{"ApplicationId" = $Application.ApplicationId; "TenantId" = $TenantID.TenantId; "CertificateThumbprint" = $Cert.Thumbprint; "SubscriptionId" = $SubscriptionId}
New-AzAutomationConnection -ResourceGroupName $ResourceGroup -AutomationAccountName $AutomationAccountName -Name $ConnectionAssetName -ConnectionTypeName AzureServicePrincipal -ConnectionFieldValues $ConnectionFieldValues
```

Automation 계정을 만들 때 연결 자산을 만들기 `AzureServicePrincipal` `AzureRunAsConnection` 위한 연결 유형과 함께 기본적으로 몇 가지 전역 모듈을 포함 합니다. 다른 인증 방법을 사용 하 여 서비스 또는 응용 프로그램에 연결 하기 위해 새 연결 자산을 만들려고 하면 자동화 계정에 연결 형식이 아직 정의 되지 않았기 때문에 작업이 실패 합니다. 사용자 지정 [PowerShell 갤러리](https://www.powershellgallery.com) 모듈에 대해 고유한 연결 유형을 만드는 방법에 대 한 자세한 내용은 [통합 모듈](automation-integration-modules.md)을 참조 하세요.

## <a name="using-a-connection-in-a-runbook-or-dsc-configuration"></a>runbook 또는 DSC 구성에서 연결 사용하기

`Get-AutomationConnection` Cmdlet을 사용 하 여 RUNBOOK 또는 DSC 구성에서 연결을 검색 합니다. 활동을 `Get-AzAutomationConnection` 사용할 수 없습니다. 이 활동은 연결의 여러 필드 값을 검색 하 고이를 [hashtable](https://go.microsoft.com/fwlink/?LinkID=324844)로 반환 합니다. 그런 다음이 해시 테이블을 runbook 또는 DSC 구성의 적절 한 명령과 함께 사용할 수 있습니다.

### <a name="textual-runbook-sample"></a>텍스트 Runbook 샘플

다음 샘플 명령에서는 앞에서 언급한 실행 계정을 사용하여 runbook에서 Azure Resource Manager 리소스를 인증하는 방법을 보여 줍니다. 여기서는 자격 증명이 아닌 인증서 기반 서비스 주체를 참조하는 실행 계정을 나타내는 연결 자산을 사용합니다.

```powershell
$Conn = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
```

> [!NOTE]
> 비그래픽 PowerShell runbook의 경우 `Add-AzAccount` 및 `Add-AzureRMAccount` 는 [AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-3.5.0)에 대 한 별칭입니다. 이러한 cmdlet을 사용 하거나 Automation 계정의 모듈을 최신 버전으로 [업데이트할](automation-update-azure-modules.md) 수 있습니다. 새 Automation 계정을 방금 만든 경우에도 모듈을 업데이트 해야 할 수 있습니다.

### <a name="graphical-runbook-samples"></a>그래픽 Runbook 샘플

그래픽 편집기의 `Get-AutomationConnection` 라이브러리 창에서 연결을 마우스 오른쪽 단추로 클릭 하 고 **캔버스에 추가**를 선택 하 여 그래픽 runbook에 활동을 추가 합니다.

![캔버스에 추가](media/automation-connections/connection-add-canvas.png)

다음 그림에서는 그래픽 Runbook에서 연결을 사용하는 예제를 보여 줍니다. 텍스트 runbook을 사용 하 여 실행 계정을 사용 하 여 인증 하기 위해 위와 동일한 예제입니다. 이 예에서는 인증 `Constant value` 을 위해 connection 개체 `Get RunAs Connection` 를 사용 하는 활동에 대 한 데이터 집합을 사용 합니다. 이 매개 변수 집합에 `ServicePrincipalCertificate` 는 단일 개체가 필요 하므로 여기에 [파이프라인 링크가](automation-graphical-authoring-intro.md#links-and-workflow) 사용 됩니다.

![연결 가져오기](media/automation-connections/automation-get-connection-object.png)

### <a name="python-2-runbook-sample"></a>Python 2 runbook 샘플

다음 샘플에서는 Python 2 runbook에서 실행 연결을 사용 하 여 인증 하는 방법을 보여 줍니다.

```python
""" Tutorial to show how to authenticate against Azure resource manager resources """
import azure.mgmt.resource
import automationassets

def get_automation_runas_credential(runas_connection):
    """ Returns credentials to authenticate against Azure resoruce manager """
    from OpenSSL import crypto
    from msrestazure import azure_active_directory
    import adal

    # Get the Azure Automation Run As service principal certificate
    cert = automationassets.get_automation_certificate("AzureRunAsCertificate")
    pks12_cert = crypto.load_pkcs12(cert)
    pem_pkey = crypto.dump_privatekey(
        crypto.FILETYPE_PEM, pks12_cert.get_privatekey())

    # Get Run As connection information for the Azure Automation service principal
    application_id = runas_connection["ApplicationId"]
    thumbprint = runas_connection["CertificateThumbprint"]
    tenant_id = runas_connection["TenantId"]

    # Authenticate with service principal certificate
    resource = "https://management.core.windows.net/"
    authority_url = ("https://login.microsoftonline.com/" + tenant_id)
    context = adal.AuthenticationContext(authority_url)
    return azure_active_directory.AdalAuthentication(
        lambda: context.acquire_token_with_client_certificate(
            resource,
            application_id,
            pem_pkey,
            thumbprint)
    )


# Authenticate to Azure using the Azure Automation Run As service principal
runas_connection = automationassets.get_automation_connection(
    "AzureRunAsConnection")
azure_credential = get_automation_runas_credential(runas_connection)
```

## <a name="next-steps"></a>다음 단계

- [그래픽 작성의 링크](automation-graphical-authoring-intro.md#links-and-workflow)를 검토하여 runbook의 논리 흐름을 지시하고 제어하는 방법을 이해합니다.
* PowerShell cmdlet 참조는 [Az. Automation](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation
)을 참조 하십시오.
- Azure Automation에서 PowerShell 모듈을 사용하는 방법 및 Azure Automation 내에서 통합 모듈로 작동하도록 고유한 PowerShell 모듈을 만드는 모범 사례에 대한 자세한 내용은 [통합 모듈](automation-integration-modules.md)을 참조하세요.