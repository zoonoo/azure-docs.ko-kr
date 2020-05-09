---
title: 웹후크를 사용하여 Azure Automation Runbook 시작
description: 클라이언트가 Azure Automation에서 HTTP 호출을 통해 runbook을 시작하는 데 사용되는 webhook입니다.  이 문서는 webhook를 만드는 방법 및 webhook를 호출하여 runbook을 시작하는 방법에 대해 설명합니다.
services: automation
ms.subservice: process-automation
ms.date: 01/16/2020
ms.topic: conceptual
ms.openlocfilehash: cbe43b298c57d266f0b031b5192f25fe3df07c05
ms.sourcegitcommit: b9d4b8ace55818fcb8e3aa58d193c03c7f6aa4f1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/29/2020
ms.locfileid: "82582443"
---
# <a name="starting-an-azure-automation-runbook-with-a-webhook"></a>웹후크를 사용하여 Azure Automation Runbook 시작

웹 후크를 통해 외부 서비스는 단일 HTTP 요청을 통해 Azure Automation에서 특정 runbook을 시작할 수 있습니다. 외부 서비스에는 Azure DevOps Services, GitHub, Azure Monitor 로그 및 사용자 지정 응용 프로그램이 포함 됩니다. 이러한 서비스는 Azure Automation API를 사용 하 여 전체 솔루션을 구현 하지 않고 webhook를 사용 하 여 runbook을 시작할 수 있습니다. [Azure Automation에서 runbook을 시작](automation-starting-a-runbook.md)하는 동안 웹 후크를 runbook을 시작 하는 다른 방법과 비교할 수 있습니다.

> [!NOTE]
> 웹후크를 사용하여 Python Runbook을 시작하는 것은 지원되지 않습니다.

![WebhooksOverview](media/automation-webhooks/webhook-overview-image.png)

>[!NOTE]
>이 문서는 새 Azure PowerShell Az 모듈을 사용하도록 업데이트되었습니다. AzureRM 모듈은 적어도 2020년 12월까지 버그 수정을 수신할 예정이므로 계속 사용하셔도 됩니다. 새 Az 모듈 및 AzureRM 호환성에 대한 자세한 내용은 [새 Azure PowerShell Az 모듈 소개](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)를 참조하세요. Hybrid Runbook Worker에 대한 Az 모듈 설치 지침은 [Azure PowerShell 모듈 설치](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)를 참조하세요. Automation 계정의 경우 [Azure Automation에서 Azure PowerShell 모듈을 업데이트하는 방법](automation-update-azure-modules.md)을 사용하여 모듈을 최신 버전으로 업데이트할 수 있습니다.

## <a name="webhook-properties"></a>Webhook 속성

다음 표에서는 Webhook에 대해 구성해야 하는 속성을 설명합니다.

| 속성 | Description |
|:--- |:--- |
| 속성 |Webhook의 이름입니다. 클라이언트에 노출 되지 않으므로 원하는 이름을 제공할 수 있습니다. Azure Automation에서 Runbook을 식별하는 용도로만 사용됩니다. 가장 좋은 방법은 webhook를 사용할 클라이언트와 관련된 이름을 지정하는 것입니다. |
| URL |Webhook의 URL입니다. 클라이언트에서 웹 후크에 연결 된 runbook을 시작 하기 위해 HTTP POST를 사용 하 여 호출 하는 고유한 주소입니다. webhook를 만들 때 자동으로 생성됩니다. 사용자 지정 URL을 지정할 수 없습니다. <br> <br> URL에는 타사 시스템에서 추가 인증 없이 runbook을 호출할 수 있도록 하는 보안 토큰이 포함 되어 있습니다. 따라서 암호와 같은 URL을 처리 해야 합니다. 보안상의 이유로 webhook를 만들 때 Azure Portal의 URL만 볼 수 있습니다. 이 URL을 나중에 사용할 수 있도록 안전한 위치에 기록해 둡니다. |
| 만료 날짜 | 더 이상 사용할 수 없는 webhook의 만료 날짜입니다. 웹 후크를 만든 후에는 webhook가 만료 되지 않은 한 만료 날짜를 수정할 수 있습니다. |
| 사용 | Webhook를 만들 때 기본적으로 사용 하도록 설정 되는지 여부를 나타내는 설정입니다. 이 속성을 사용 안 함으로 설정 하면 클라이언트에서 webhook을 사용할 수 없습니다. Webhook를 만들 때 또는 다른 시간을 만든 후에이 속성을 설정할 수 있습니다. |

## <a name="parameters-used-when-the-webhook-starts-a-runbook"></a>Webhook가 runbook을 시작할 때 사용 되는 매개 변수

Webhook는 runbook이 시작 될 때 사용 되는 runbook 매개 변수에 대 한 값을 정의할 수 있습니다. 웹 후크는 필수 runbook 매개 변수에 대 한 값을 포함 해야 하며 선택적 매개 변수에 대 한 값을 포함할 수 있습니다. Webhook에 구성 된 매개 변수 값은 webhook를 만든 후에도 수정할 수 있습니다. 단일 runbook에 연결 된 여러 웹 후크는 각각 서로 다른 runbook 매개 변수 값을 사용할 수 있습니다. 클라이언트는 webhook를 사용하여 Runbook을 시작할 때 webhook에 정의된 매개 변수 값을 재정의할 수 없습니다.

클라이언트에서 데이터를 수신 하기 위해 runbook은 라는 `WebhookData`단일 매개 변수를 지원 합니다. 이 매개 변수는 클라이언트가 POST 요청에 포함 하는 데이터를 포함 하는 개체를 정의 합니다.

![WebhookData 속성](media/automation-webhooks/webhook-data-properties.png)

`WebhookData` 매개 변수에는 다음 속성이 있습니다.

| 속성 | 설명 |
|:--- |:--- |
| `WebhookName` | Webhook의 이름입니다. |
| `RequestHeader` | 들어오는 POST 요청의 헤더를 포함 하는 해시 테이블입니다. |
| `RequestBody` | 들어오는 POST 요청의 본문입니다. 이 본문은 문자열, JSON, XML 또는 폼 인코딩 등의 모든 데이터 형식을 유지 합니다. Runbook은 예상되는 데이터 형식으로 작동하도록 작성되어야 합니다. |

`WebhookData` 매개 변수를 지 원하는 데 필요한 webhook의 구성이 없으며 runbook이이를 수락할 필요가 없습니다. Runbook에서 매개 변수를 정의 하지 않으면 클라이언트에서 보낸 요청에 대 한 세부 정보가 무시 됩니다.

> [!NOTE]
> 웹 후크를 호출할 때 클라이언트는 호출이 실패 하는 경우 항상 모든 매개 변수 값을 저장 해야 합니다. 네트워크 중단 또는 연결 문제가 있는 경우 응용 프로그램은 실패 한 webhook 호출을 검색할 수 없습니다.

Webhook를 만들 때 값을 `WebhookData` 지정 하면 웹 후크가 클라이언트 POST 요청의 데이터로 runbook을 시작할 때 해당 값이 재정의 됩니다. 이는 응용 프로그램에 요청 본문의 데이터가 포함 되지 않은 경우에도 마찬가지입니다. 

Webhook 이외의 메커니즘을 사용 하 여 `WebhookData` 정의 하는 runbook을 시작 하는 경우 runbook에서 인식 `WebhookData` 하는 값을 제공할 수 있습니다. 이 값은 `WebhookData` 매개 변수와 동일한 `WebhookData` [속성](#webhook-properties) 을 사용 하는 개체 여야 합니다. 그러면 runbook은 웹 후크에 전달 된 실제 개체에서 작동 하는 것 처럼 작업을 수행할 수 있습니다.

예를 들어 Azure Portal에서 다음 runbook을 시작 하 고 테스트를 위해 일부 샘플 webhook 데이터를 전달 하려는 경우 JSON의 데이터를 사용자 인터페이스에 전달 해야 합니다.

![UI에서의 WebhookData 매개 변수](media/automation-webhooks/WebhookData-parameter-from-UI.png)

다음 runbook 예제에서는에 대해 `WebhookData`다음과 같은 속성을 정의 하겠습니다.

* **WebhookName**: mywebhook
* **Requestbody**:`*[{'ResourceGroup': 'myResourceGroup','Name': 'vm01'},{'ResourceGroup': 'myResourceGroup','Name': 'vm02'}]*`

이제 다음 JSON 개체를 UI에서 `WebhookData` 매개 변수에 전달 합니다. 캐리지 리턴 및 줄 바꿈 문자를 사용 하는이 예제는 webhook에서 전달 되는 형식과 일치 합니다.

```json
{"WebhookName":"mywebhook","RequestBody":"[\r\n {\r\n \"ResourceGroup\": \"vm01\",\r\n \"Name\": \"vm01\"\r\n },\r\n {\r\n \"ResourceGroup\": \"vm02\",\r\n \"Name\": \"vm02\"\r\n }\r\n]"}
```

![UI에서 WebhookData 매개 변수 시작](media/automation-webhooks/Start-WebhookData-parameter-from-UI.png)

> [!NOTE]
> Azure Automation는 runbook 작업에 모든 입력 매개 변수의 값을 기록 합니다. 따라서 웹 후크 요청에서 클라이언트에서 제공 하는 모든 입력이 기록 되 고 automation 작업에 액세스할 수 있는 모든 사용자에 게 제공 됩니다. 따라서 webhook 호출에 중요한 정보를 포함할 때는 주의해야 합니다.

## <a name="webhook-security"></a>Webhook 보안

웹 후크 보안은 웹 후크를 호출할 수 있도록 허용 하는 보안 토큰을 포함 하는 해당 URL의 개인 정보에 의존 합니다. Azure Automation은 올바른 URL에 대 한 요청에 대 한 인증을 수행 하지 않습니다. 이러한 이유로 클라이언트는 요청의 유효성을 검사 하는 다른 방법을 사용 하지 않고 매우 중요 한 작업을 수행 하는 runbook에 대해 웹 후크를 사용 하지 않아야 합니다.

Runbook에 논리를 포함 하 여 webhook에서 호출 되었는지 여부를 확인할 수 있습니다. Runbook이 `WebhookData` 매개 변수의 속성 `WebhookName` 을 확인 하도록 합니다. Runbook은 `RequestHeader` 및 `RequestBody` 속성에서 특정 정보를 검색 하 여 추가 유효성 검사를 수행할 수 있습니다.

또 다른 전략은 runbook에서 webhook 요청을 받을 때 외부 조건의 유효성 검사를 수행 하도록 하는 것입니다. 예를 들어 github 리포지토리에 새 커밋이 있을 때마다 GitHub에 의해 호출 되는 runbook을 생각해 볼 수 있습니다. Runbook은 계속 하기 전에 GitHub에 연결 하 여 새 커밋이 발생 했는지 확인할 수 있습니다.

## <a name="create-a-webhook"></a>웹후크 만들기

Azure 포털에서 runbook에 연결된 새 webhook를 만들려면 다음 절차를 수행합니다.

1. Azure Portal의 runbook 페이지에서 웹 후크가 runbook을 클릭 하 여 runbook 세부 정보를 확인 합니다. Runbook **상태** 필드가 **게시**됨으로 설정 되어 있는지 확인 합니다.
2. 페이지 맨 위에서 **웹후크**를 클릭하여 웹후크 추가 페이지를 엽니다.
3. **새 Webhook 만들기** 를 클릭 하 여 webhook 만들기 페이지를 엽니다.
4. Webhook에 대 한 **이름** 및 **만료 날짜** 필드를 입력 하 고 사용 하도록 설정 해야 하는지 여부를 지정 합니다. 이러한 속성에 대 한 자세한 내용은 [Webhook 속성](#webhook-properties) 을 참조 하세요.
5. 복사 아이콘을 클릭하고 Ctrl+C를 눌러 webhook의 URL을 복사합니다. 그런 다음 안전한 곳에 기록합니다. 

    > [!NOTE]
    > webhook를 만들고 나면 URL을 다시 검색할 수 없습니다.

   ![Webhook URL](media/automation-webhooks/copy-webhook-url.png)

1. **매개 변수** 를 클릭하여 runbook 매개 변수의 값을 제공합니다. Runbook에 필수 매개 변수가 있는 경우 값을 제공 하지 않으면 webhook을 만들 수 없습니다.
1. **만들기** 를 클릭하여 webhook를 만듭니다.

## <a name="use-a-webhook"></a>Webhook 사용

Webhook를 만든 후에 사용 하려면 클라이언트에서 웹 후크에 대 한 URL을 사용 `POST` 하 여 HTTP 요청을 실행 해야 합니다. 구문은 다음과 같습니다.

```http
http://<Webhook Server>/token?=<Token Value>
```

클라이언트는 `POST` 요청에서 다음 반환 코드 중 하나를 받습니다.

| 코드 | Text | 설명 |
|:--- |:--- |:--- |
| 202 |수락됨 |요청이 수락되었고 runbook에서 대기합니다. |
| 400 |잘못된 요청 |다음 이유 중 하나로 인해 요청이 수락되지 않았습니다. <ul> <li>webhook가 만료되었습니다.</li> <li>webhook가 비활성화되었습니다.</li> <li>URL의 토큰이 잘못되었습니다.</li>  </ul> |
| 404 |찾을 수 없음 |다음 이유 중 하나로 인해 요청이 수락되지 않았습니다. <ul> <li>webhook를 찾을 수 없습니다.</li> <li>runbook을 찾을 수 없습니다.</li> <li>계정을 찾을 수 없습니다.</li>  </ul> |
| 500 |내부 서버 오류 |URL은 유효했지만 오류가 발생했습니다. 요청을 다시 제출하십시오. |

요청이 성공 했다고 가정할 때 webhook 응답에는 아래와 같이 JSON 형식의 작업 ID가 포함 됩니다. 단일 작업 ID를 포함 하지만 JSON 형식을 사용 하면 향후 향상 된 기능을 향상 시킬 수 있습니다.

```json
{"JobIds":["<JobId>"]}
```

클라이언트는 Runbook 작업의 완료 여부 또는 완료 상태를 webhook에서 확인할 수 없습니다. [Windows PowerShell](https://docs.microsoft.com/powershell/module/servicemanagement/azure/get-azureautomationjob) 또는 [Azure Automation API](/rest/api/automation/job)와 같은 다른 메커니즘을 사용 하 여 작업 ID를 사용 하 여이 정보를 찾을 수 있습니다.

## <a name="renew-a-webhook"></a>webhook 갱신

웹 후크를 만들면 유효 기간이 10 년이 되며, 그 후에는 자동으로 만료 됩니다. 웹 후크가 만료 된 후에는 다시 활성화할 수 없습니다. 제거 하 고 다시 만들 수만 있습니다. 

만료 시간에 도달 하지 않은 webhook를 확장할 수 있습니다. Webhook를 확장 하려면:

1. Webhook가 포함 된 runbook으로 이동 합니다. 
2. **리소스** 아래에서 **Webhook**를 선택합니다. 
3. 확장 하려는 webhook를 클릭 합니다. 
4. 웹 후크 페이지에서 새 만료 날짜 및 시간을 선택 하 고 **저장**을 클릭 합니다.

## <a name="sample-runbook"></a>샘플 Runbook

다음 샘플 Runbook은 webhook 데이터를 수락하고 요청 본문에 지정된 가상 머신을 시작합니다. 이 runbook을 테스트 **하려면 runbook의**Automation 계정에서 **runbook 만들기**를 클릭 합니다. Runbook을 만드는 방법을 모르는 경우 [Runbook 만들기](automation-quickstart-create-runbook.md)를 참조하세요.

> [!NOTE]
> 비그래픽 PowerShell runbook의 경우 `Add-AzAccount` 및 `Add-AzureRMAccount` 는 [AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-3.5.0)에 대 한 별칭입니다. 이러한 cmdlet을 사용하거나 Automation 계정의 [모듈을 최신 버전으로 업데이트](automation-update-azure-modules.md)할 수 있습니다. 새 Automation 계정을 만든 경우에도 모듈을 업데이트해야 할 수 있습니다.

```powershell
param
(
    [Parameter (Mandatory = $false)]
    [object] $WebhookData
)

# If runbook was called from Webhook, WebhookData will not be null.
if ($WebhookData) {

    # Check header for message to validate request
    if ($WebhookData.RequestHeader.message -eq 'StartedbyContoso')
    {
        Write-Output "Header has required information"}
    else
    {
        Write-Output "Header missing required information";
        exit;
    }

    # Retrieve VMs from Webhook request body
    $vms = (ConvertFrom-Json -InputObject $WebhookData.RequestBody)

    # Authenticate to Azure by using the service principal and certificate. Then, set the subscription.

    Write-Output "Authenticating to Azure with service principal and certificate"
    $ConnectionAssetName = "AzureRunAsConnection"
    Write-Output "Get connection asset: $ConnectionAssetName"

    $Conn = Get-AutomationConnection -Name $ConnectionAssetName
            if ($Conn -eq $null)
            {
                throw "Could not retrieve connection asset: $ConnectionAssetName. Check that this asset exists in the Automation account."
            }
            Write-Output "Authenticating to Azure with service principal."
            Add-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint | Write-Output

        # Start each virtual machine
        foreach ($vm in $vms)
        {
            $vmName = $vm.Name
            Write-Output "Starting $vmName"
            Start-AzVM -Name $vm.Name -ResourceGroup $vm.ResourceGroup
        }
}
else {
    # Error
    write-Error "This runbook is meant to be started from an Azure alert webhook only."
}
```

## <a name="test-the-sample"></a>샘플 테스트

다음 예제는 Windows PowerShell을 사용하여 webhook로 runbook을 시작합니다. HTTP 요청을 만들 수 있는 모든 언어는 웹 후크를 사용할 수 있습니다. 여기에서는 Windows PowerShell을 사용 합니다.

Runbook에는 요청 본문에 JSON으로 서식이 지정된 가상 머신의 목록이 있어야 합니다. Runbook은 webhook 호출자가 유효한 지 확인 하는 정의 된 메시지를 헤더에 포함 하는 것도 유효성을 검사 합니다.

```azurepowershell-interactive
$uri = "<webHook Uri>"

$vms  = @(
            @{ Name="vm01";ResourceGroup="vm01"},
            @{ Name="vm02";ResourceGroup="vm02"}
        )
$body = ConvertTo-Json -InputObject $vms
$header = @{ message="StartedbyContoso"}
$response = Invoke-WebRequest -Method Post -Uri $uri -Body $body -Headers $header
$jobid = (ConvertFrom-Json ($response.Content)).jobids[0]
```

다음 예에서는의 `RequestBody` `WebhookData`속성에서 runbook에 사용할 수 있는 요청의 본문을 보여 줍니다. 이 값은 요청의 본문에 포함 된 형식과 호환 되도록 JSON 형식으로 지정 됩니다.

```json
[
    {
        "Name":  "vm01",
        "ResourceGroup":  "myResourceGroup"
    },
    {
        "Name":  "vm02",
        "ResourceGroup":  "myResourceGroup"
    }
]
```

다음 이미지는 Windows PowerShell 및 결과 응답에서 전송되는 요청을 표시합니다. 작업 ID는 응답에서 추출되고 문자열로 변환됩니다.

![Webhook 단추](media/automation-webhooks/webhook-request-response.png)

## <a name="next-steps"></a>다음 단계

* Azure Automation를 사용 하 여 Azure 경고에 대 한 조치를 수행 하는 방법을 알아보려면 경고를 사용 하 여 [Azure Automation runbook 트리거](automation-create-alert-triggered-runbook.md)를 참조 하세요.
