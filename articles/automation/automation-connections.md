---
title: Azure Automation에서 연결 관리
description: Azure Automation의 연결 자산은 외부 서비스 또는 runbook이나 DSC 구성의 애플리케이션을 연결하는데 필요한 정보를 포함합니다. 이 문서에서는 연결에 대해 자세히 알아보고 텍스트 작성과 그래픽 작성 모두에서 연결을 사용하는 방법을 설명합니다.
services: automation
ms.subservice: shared-capabilities
ms.date: 01/13/2020
ms.topic: conceptual
ms.custom: has-adal-ref
ms.openlocfilehash: b00dd226306ed639757666cc4f826b0d7a0e5711
ms.sourcegitcommit: d662eda7c8eec2a5e131935d16c80f1cf298cb6b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/01/2020
ms.locfileid: "82651819"
---
# <a name="manage-connections-in-azure-automation"></a>Azure Automation에서 연결 관리

Azure Automation 연결 자산에는 아래 나열 된 정보가 포함 되어 있습니다. 이 정보는 runbook 또는 DSC 구성에서 외부 서비스 또는 응용 프로그램에 연결 하는 데 필요 합니다. 

* 인증에 필요한 정보 (예: 사용자 이름 및 암호)
* URL 또는 포트와 같은 연결 정보

연결 자산은 특정 응용 프로그램에 연결 하는 데 필요한 모든 속성을 유지 하므로 여러 변수를 만들 필요가 없습니다. 한 곳에서 연결에 대 한 값을 편집 하 고 단일 매개 변수에서 runbook 또는 DSC 구성에 대 한 연결 이름을 전달할 수 있습니다. Runbook 또는 구성에서는 내부 `Get-AutomationConnection` cmdlet을 사용 하 여 연결의 속성에 액세스 합니다.

연결을 만들 때는 연결 유형을 지정해야 합니다. 연결 형식은 속성 집합을 정의하는 템플릿입니다. 메타 데이터 파일이 포함 된 통합 모듈을 사용 하 여 Azure Automation에 연결 유형을 추가할 수 있습니다. 통합 모듈에 연결 형식이 포함 되어 있고 Automation 계정으로 가져올 수 있는 경우 [AZURE AUTOMATION API](/previous-versions/azure/reference/mt163818(v=azure.100)) 를 사용 하 여 연결 형식을 만들 수도 있습니다. 

>[!NOTE]
>Azure Automation의 안전한 자산에는 자격 증명, 인증서, 연결, 암호화된 변수 등이 있습니다. 이러한 자산은 각 자동화 계정에 대해 생성 되는 고유 키를 사용 하 여 암호화 되 고 Azure Automation 저장 됩니다. Azure Automation은 시스템 관리 Key Vault에 키를 저장 합니다. 보안 자산을 저장 하기 전에 Automation은 Key Vault에서 키를 로드 한 다음이를 사용 하 여 자산을 암호화 합니다. 

>[!NOTE]
>이 문서는 새 Azure PowerShell Az 모듈을 사용하도록 업데이트되었습니다. AzureRM 모듈은 적어도 2020년 12월까지 버그 수정을 수신할 예정이므로 계속 사용하셔도 됩니다. 새 Az 모듈 및 AzureRM 호환성에 대한 자세한 내용은 [새 Azure PowerShell Az 모듈 소개](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)를 참조하세요. Hybrid Runbook Worker에 대한 Az 모듈 설치 지침은 [Azure PowerShell 모듈 설치](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)를 참조하세요. Automation 계정의 경우 [Azure Automation에서 Azure PowerShell 모듈을 업데이트하는 방법](automation-update-azure-modules.md)을 사용하여 모듈을 최신 버전으로 업데이트할 수 있습니다.

## <a name="connection-types"></a>연결 형식

Azure Automation는 다음과 같은 기본 제공 연결 형식을 사용할 수 있습니다.

* `Azure`-클래식 리소스를 관리 하는 데 사용 되는 연결을 나타냅니다.
* `AzureServicePrincipal`-Azure 실행 계정에서 사용 하는 연결을 나타냅니다.
* `AzureClassicCertificate`-클래식 Azure 실행 계정에서 사용 하는 연결을 나타냅니다.

대부분의 경우에는 [실행 계정을](manage-runas-account.md)만들 때 생성 되기 때문에 연결 리소스를 만들 필요가 없습니다.

## <a name="powershell-cmdlets-to-access-connections"></a>연결에 액세스 하는 PowerShell cmdlet

다음 표의 cmdlet은 PowerShell을 사용 하 여 Automation 연결을 만들고 관리 합니다. [Az 모듈](shared-resources/modules.md#az-modules)의 일부로 제공 됩니다.

|Cmdlet|설명|
|---|---|
|[AzAutomationConnection](https://docs.microsoft.com/powershell/module/az.automation/get-azautomationconnection?view=azps-3.7.0)|연결에 대 한 정보를 검색 합니다.|
|[AzAutomationConnection](https://docs.microsoft.com/powershell/module/az.automation/new-azautomationconnection?view=azps-3.7.0)|새 연결을 만듭니다.|
|[AzAutomationConnection](https://docs.microsoft.com/powershell/module/Az.Automation/Remove-AzAutomationConnection?view=azps-3.7.0)|기존 연결을 제거 합니다.|
|[AzAutomationConnectionFieldValue](https://docs.microsoft.com/powershell/module/Az.Automation/Set-AzAutomationConnectionFieldValue?view=azps-3.7.0)|기존 연결의 특정 필드 값을 설정합니다.|

## <a name="internal-cmdlets-to-access-connections"></a>연결에 액세스 하는 내부 cmdlet

다음 표의 내부 cmdlet은 runbook 및 DSC 구성의 연결에 액세스 하는 데 사용 됩니다. 이 cmdlet은 전역 모듈과 `Orchestrator.AssetManagement.Cmdlets`함께 제공 됩니다. 자세한 내용은 [내부 cmdlet](shared-resources/modules.md#internal-cmdlets)을 참조 하세요.

|내부 Cmdlet|설명|
|---|---|
|`Get-AutomationConnection` | 연결의 여러 필드 값을 검색 하 여 [해시](https://go.microsoft.com/fwlink/?LinkID=324844)테이블로 반환 합니다. 그런 다음 runbook 또는 DSC 구성의 적절 한 명령과 함께이 해시 테이블을 사용할 수 있습니다.|

>[!NOTE]
>의 `Name` `Get-AutomationConnection`매개 변수를 사용 하 여 변수를 사용 하지 마십시오. 이 경우 변수를 사용 하면 디자인 타임에 runbook 또는 DSC 구성과 연결 자산 간의 종속성을 검색 하기가 복잡할 수 있습니다.

## <a name="python-2-functions-to-access-connections"></a>연결에 액세스 하는 Python 2 함수

다음 표의 함수는 Python 2 runbook의 연결에 액세스 하는 데 사용 됩니다.

| 함수 | 설명 |
|:---|:---|
| `automationassets.get_automation_connection` | 연결을 검색합니다. 연결의 속성이 있는 사전을 반환합니다. |

> [!NOTE]
> 자산 기능에 액세스 `automationassets` 하려면 Python runbook의 맨 위에 있는 모듈을 가져와야 합니다.

## <a name="create-a-new-connection"></a>새 연결 만들기

### <a name="create-a-new-connection-with-the-azure-portal"></a>Azure Portal를 사용 하 여 새 연결을 만듭니다.

Azure Portal에서 새 연결을 만들려면 다음을 수행 합니다.

1. Automation 계정에서 **공유 리소스**아래에 있는 **연결** 을 클릭 합니다.
2. 연결 페이지에서 **+ 연결 추가** 를 클릭 합니다.
4. 새 연결 창의 **유형** 필드에서 만들 연결 유형을 선택 합니다. 선택 항목은 `Azure`, `AzureServicePrincipal`및 `AzureClassicCertificate`입니다. 
5. 이 폼은 사용자가 선택한 연결 형식에 대 한 속성을 제공 합니다. 양식을 완료하고 **만들기** 를 클릭하여 새 연결을 저장합니다.

### <a name="create-a-new-connection-with-windows-powershell"></a>Windows PowerShell을 사용 하 여 새 연결 만들기

Cmdlet을 `New-AzAutomationConnection` 사용 하 여 Windows PowerShell을 사용 하 여 새 연결을 만듭니다. 이 cmdlet에는 `ConnectionFieldValues` 연결 형식으로 정의 된 각 속성에 대 한 값을 정의 하는 해시 테이블이 필요한 매개 변수가 있습니다.

포털에서 실행 계정을 만드는 대신 다음 예제 명령을 사용 하 여 새 연결 자산을 만들 수 있습니다.

```powershell
$ConnectionAssetName = "AzureRunAsConnection"
$ConnectionFieldValues = @{"ApplicationId" = $Application.ApplicationId; "TenantId" = $TenantID.TenantId; "CertificateThumbprint" = $Cert.Thumbprint; "SubscriptionId" = $SubscriptionId}
New-AzAutomationConnection -ResourceGroupName $ResourceGroup -AutomationAccountName $AutomationAccountName -Name $ConnectionAssetName -ConnectionTypeName AzureServicePrincipal -ConnectionFieldValues $ConnectionFieldValues
```

Automation 계정을 만들 때 연결 자산을 만들기 `AzureServicePrincipal` `AzureRunAsConnection` 위한 연결 유형과 함께 기본적으로 몇 가지 전역 모듈을 포함 합니다. 다른 인증 방법을 사용 하 여 서비스 또는 응용 프로그램에 연결 하기 위해 새 연결 자산을 만들려고 하면 자동화 계정에 연결 형식이 아직 정의 되지 않았기 때문에 작업이 실패 합니다. 사용자 지정 모듈에 대 한 사용자 고유의 연결 형식을 만드는 방법에 대 한 자세한 내용은 [연결 형식 추가](#add-a-connection-type)를 참조 하세요.

## <a name="add-a-connection-type"></a>연결 형식 추가

Runbook 또는 DSC 구성이 외부 서비스에 연결 되는 경우 통합 모듈 이라는 [사용자 지정 모듈](shared-resources/modules.md#custom-modules) 에서 연결 형식을 정의 해야 합니다. 이 모듈에는 압축 된 **.zip** 파일의 module 폴더에 있는 ** &lt;ModuleName&gt;-Automation. json**이라는 연결 유형 속성을 지정 하는 메타 데이터 파일이 포함 되어 있습니다. 이 파일은 모듈이 나타내는 시스템 또는 서비스에 연결 하는 데 필요한 연결의 필드를 포함 합니다. 이 파일을 사용 하 여 연결 형식에 대 한 필드 이름, 데이터 형식, 암호화 상태 및 선택적 상태를 설정할 수 있습니다. 

다음 예제는 라는 `MyModuleConnection`사용자 지정 연결 형식에 대 한 사용자 이름 및 암호 속성을 정의 하는 **json** 파일 형식의 템플릿입니다.

```json
{
   "ConnectionFields": [
   {
      "IsEncrypted":  false,
      "IsOptional":  true,
      "Name":  "Username",
      "TypeName":  "System.String"
   },
   {
      "IsEncrypted":  true,
      "IsOptional":  false,
      "Name":  "Password",
      "TypeName":  "System.String"
   }
   ],
   "ConnectionTypeName":  "MyModuleConnection",
   "IntegrationModuleName":  "MyModule"
}
```

## <a name="get-a-connection-in-a-runbook-or-dsc-configuration"></a>Runbook 또는 DSC 구성에서 연결 가져오기

내부 `Get-AutomationConnection` cmdlet을 사용 하 여 RUNBOOK 또는 DSC 구성에서 연결을 검색 합니다. 이 cmdlet은 연결에 대 `Get-AzAutomationConnection` 한 정보 대신 연결 값을 검색 하므로 cmdlet 보다 선호 됩니다. 

### <a name="textual-runbook-example"></a>텍스트 runbook 예

다음 예제에서는 실행 계정을 사용 하 여 runbook에서 Azure Resource Manager 리소스를 인증 하는 방법을 보여 줍니다. 인증서 기반 서비스 주체를 참조 하는 실행 계정을 나타내는 연결 자산을 사용 합니다.

```powershell
$Conn = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
```

### <a name="graphical-runbook-examples"></a>그래픽 runbook 예

내부 `Get-AutomationConnection` cmdlet에 대 한 활동을 그래픽 runbook에 추가할 수 있습니다. 그래픽 편집기의 라이브러리 창에서 연결을 마우스 오른쪽 단추로 클릭 하 고 **캔버스에 추가를**선택 합니다.

![캔버스에 추가](media/automation-connections/connection-add-canvas.png)

다음 그림에서는 그래픽 runbook에서 connection 개체를 사용 하는 예를 보여 줍니다. 이 예에서는 인증 `Constant value` 을 위해 연결 개체 `Get RunAs Connection` 를 사용 하는 활동에 대 한 데이터 집합을 사용 합니다. 이 매개 변수 집합에 `ServicePrincipalCertificate` 는 단일 개체가 필요 하므로 여기에 [파이프라인 링크가](automation-graphical-authoring-intro.md#links-and-workflow) 사용 됩니다.

![연결 가져오기](media/automation-connections/automation-get-connection-object.png)

### <a name="python-2-runbook-example"></a>Python 2 runbook 예제

다음 예제에서는 Python 2 runbook에서 실행 연결을 사용 하 여 인증 하는 방법을 보여 줍니다.

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

* 연결에 액세스 하는 데 사용 되는 cmdlet에 대 한 자세한 내용은 [Azure Automation 모듈 관리](shared-resources/modules.md)를 참조 하세요.
* Runbook에 대 한 일반 정보는 [Azure Automation에서 runbook 실행](automation-runbook-execution.md)을 참조 하세요.
* DSC 구성에 대 한 자세한 내용은 [상태 구성 개요](automation-dsc-overview.md)를 참조 하세요.