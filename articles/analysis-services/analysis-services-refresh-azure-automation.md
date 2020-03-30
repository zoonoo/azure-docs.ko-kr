---
title: Azure 자동화를 통한 Azure 분석 서비스 모델 새로 고침 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 자동화를 사용하여 Azure 분석 서비스에 대한 모델 새로 고침을 코딩하는 방법에 대해 설명합니다.
author: chrislound
ms.service: analysis-services
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: chlound
ms.openlocfilehash: a79123d57f80474e1871ef68f9a92ea9417089ac
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73572364"
---
# <a name="refresh-with-azure-automation"></a>Azure Automation을 사용하여 새로 고침

Azure 자동화 및 PowerShell Runbook을 사용하여 Azure 분석 테이블 형식 모델에서 자동화된 데이터 새로 고침 작업을 수행할 수 있습니다.  

이 문서의 예제에서는 [PowerShell SqlServer 모듈을](https://docs.microsoft.com/powershell/module/sqlserver/?view=sqlserver-ps)사용합니다.

모델 새로 고침을 보여 주는 샘플 PowerShell Runbook은 이 문서의 후반부에 제공됩니다.  

## <a name="authentication"></a>인증

모든 호출은 유효한 Azure Active Directory(OAuth 2) 토큰으로 인증되어야 합니다.  이 문서의 예제에서는 SPN(서비스 보안 주체)을 사용하여 Azure 분석 서비스를 인증합니다.

서비스 주체를 만드는 방법에 대해 자세히 알아보려면 [Azure 포털을 사용하여 서비스 주체 만들기를](../active-directory/develop/howto-create-service-principal-portal.md)참조하십시오.

## <a name="prerequisites"></a>사전 요구 사항

> [!IMPORTANT]
> 다음 예제는 Azure 분석 서비스 방화벽이 비활성화된 것으로 가정합니다. 방화벽을 사용하도록 설정한 경우 요청 시작자의 공용 IP 주소를 방화벽에 화이트리스트로 표시해야 합니다.

### <a name="install-sqlserver-modules-from-powershell-gallery"></a>PowerShell 갤러리에서 SqlServer 모듈을 설치합니다.

1. Azure 자동화 계정에서 **모듈을 클릭한**다음 **갤러리를 찾아봅니다.**

2. 검색 표시줄에서 **SqlServer**를 검색합니다.

    ![모듈 검색](./media/analysis-services-refresh-azure-automation/1.png)

3. SqlServer를 선택한 다음 **가져오기**를 클릭합니다.
 
    ![가져오기 모듈](./media/analysis-services-refresh-azure-automation/2.png)

4. **확인**을 클릭합니다.
 
### <a name="create-a-service-principal-spn"></a>SPN(서비스 주체 만들기)

서비스 주체를 만드는 방법에 대해 알아보려면 [Azure portal을 사용하여 서비스 주체 만들기를](../active-directory/develop/howto-create-service-principal-portal.md)참조하십시오.

### <a name="configure-permissions-in-azure-analysis-services"></a>Azure 분석 서비스에서 권한 구성
 
만드는 서비스 주체는 서버에 서버 관리자 권한이 있어야 합니다. 자세히 알아보려면 [서버 관리자 역할에 서비스 사용자 추가](analysis-services-addservprinc-admins.md)를 참조하세요.

## <a name="design-the-azure-automation-runbook"></a>Azure 자동화 실행책 디자인

1. 자동화 계정에서 서비스 주체를 안전하게 저장하는 데 사용할 **자격 증명** 리소스를 만듭니다.

    ![자격 증명 만들기](./media/analysis-services-refresh-azure-automation/6.png)

2. 자격 증명에 대한 세부 정보를 입력합니다.  사용자 **이름에**대 한 **SPN ClientId를**입력 합니다 **SPN Secret** **.**

    ![자격 증명 만들기](./media/analysis-services-refresh-azure-automation/7.png)

3. 자동화 Runbook 가져오기

    ![Runbook 가져오기](./media/analysis-services-refresh-azure-automation/8.png)

4. **새로 고침 모델.ps1** 파일을 찾아보고 **이름과** **설명을**제공한 다음 **을 클릭합니다.**

    ![Runbook 가져오기](./media/analysis-services-refresh-azure-automation/9.png)

5. Runbook을 만들면 자동으로 편집 모드로 전환됩니다.  **게시**를 선택합니다.

    ![Runbook 게시](./media/analysis-services-refresh-azure-automation/10.png)

    > [!NOTE]
    > 이전에 만든 자격 증명 리소스는 **Runbook에서 Get-AutomationPSCredential** 명령을 사용하여 검색됩니다.  그런 다음 이 명령은 **호출-ProcessASADatabase** PowerShell 명령으로 전달되어 Azure 분석 서비스에 대한 인증을 수행합니다.

6. **실행부 시작을**클릭하여 실행책을 테스트합니다.

    ![Runbook 시작](./media/analysis-services-refresh-azure-automation/11.png)

7. **데이터베이스 이름,** **ANALYSISSERVER**및 **새로 고침 입력** 매개 변수를 입력한 다음 **확인을**클릭합니다. Runbook을 수동으로 실행할 때 **WEBHOOKDATA** 매개 변수가 필요하지 않습니다.

    ![Runbook 시작](./media/analysis-services-refresh-azure-automation/12.png)

Runbook이 성공적으로 실행되면 다음과 같은 출력을 받게 됩니다.

![성공적인 실행](./media/analysis-services-refresh-azure-automation/13.png)

## <a name="use-a-self-contained-azure-automation-runbook"></a>자체 포함된 Azure 자동화 Runbook 사용

Runbook은 예약된 기준으로 Azure 분석 서비스 모델 새로 고침을 트리거하도록 구성할 수 있습니다.

다음과 같이 구성할 수 있습니다.

1. 자동화 실행책에서 일정을 **클릭한**다음 **일정을 추가합니다.**
 
    ![일정 만들기](./media/analysis-services-refresh-azure-automation/14.png)

2. **새** > **일정 만들기를**클릭한 다음 세부 정보를 입력합니다.

    ![일정 구성](./media/analysis-services-refresh-azure-automation/15.png)

3. **만들기**를 클릭합니다.

4. 일정에 대한 매개 변수를 입력합니다. 런북이 트리거될 때마다 사용됩니다. **WEBHOOKDATA** 매개 변수는 일정을 통해 실행할 때 비워 두어야 합니다.

    ![매개 변수 구성](./media/analysis-services-refresh-azure-automation/16.png)

5. **확인**을 클릭합니다.

## <a name="consume-with-data-factory"></a>데이터 팩토리에서 사용

Azure 데이터 팩터리를 사용하여 Runbook을 사용하려면 먼저 Runbook에 대한 **웹후크를** 만듭니다. **WebhookAzure** 는 Azure 데이터 팩터리 웹 활동을 통해 호출할 수 있는 URL을 제공합니다.

> [!IMPORTANT]
> **웹 후크를**만들려면 Runbook의 상태가 **게시되어야**합니다.

1. 자동화 실행책에서 웹후크 를 클릭한 다음 **웹후크 추가 를** **클릭합니다.**

   ![웹 후크 추가](./media/analysis-services-refresh-azure-automation/17.png)

2. Webhook에 이름과 만료를 지정합니다.  이름은 자동화 Runbook 내부의 웹후크만 식별하며 URL의 일부를 구성하지 않습니다.

   >[!CAUTION]
   >마법사를 닫은 후에는 다시 가져오지 못하므로 마법사를 닫기 전에 URL을 복사해야 합니다.
    
   ![웹후크 구성](./media/analysis-services-refresh-azure-automation/18.png)

    웹후크의 매개 변수는 비어 있습니다.  Azure Data Factory 웹 활동을 구성할 때 매개 변수를 웹 호출 본문으로 전달할 수 있습니다.

3. 데이터 팩터리에서 **웹 활동** 구성

### <a name="example"></a>예제

   ![웹 활동 예제](./media/analysis-services-refresh-azure-automation/19.png)

**URL은** 웹후크에서 만든 URL입니다.

**본문에는** 다음 속성을 포함 해야 하는 JSON 문서입니다.


|속성  |값  |
|---------|---------|
|**분석서비스데이터베이스**     |Azure 분석 서비스 데이터베이스의 이름 <br/> 예: 어드벤처웍스DB         |
|**분석서비스서버**     |Azure 분석 서비스 서버 이름입니다. <br/> 예: https:\//westus.asazure.windows.net/servers/myserver/models/AdventureWorks/         |
|**데이터베이스 새로 고침 유형**     |수행할 새로 고침 유형입니다. <br/> 예: 전체         |

예 JSON 본문:

```json
{
    "AnalysisServicesDatabaseName": "AdventureWorksDB",
    "AnalysisServicesServer": "asazure://westeurope.asazure.windows.net/MyAnalysisServer",
    "DatabaseRefreshType": "Full"
}
```

이러한 매개 변수는 Runbook PowerShell 스크립트에 정의되어 있습니다.  웹 활동이 실행되면 전달된 JSON 페이로드는 WEBHOOKDATA입니다.

이 역직렬화 하 고 PowerShell 매개 변수로 저장 됩니다., 다음 호출-ProcesASDatabase PowerShell 명령에 의해 사용 됩니다.

![직렬화 된 웹 후크](./media/analysis-services-refresh-azure-automation/20.png)

## <a name="use-a-hybrid-worker-with-azure-analysis-services"></a>Azure 분석 서비스를 사용하여 하이브리드 작업자 사용

정적 공용 IP 주소가 있는 Azure 가상 컴퓨터를 Azure 자동화 하이브리드 작업자로 사용할 수 있습니다.  그런 다음 이 공용 IP 주소를 Azure 분석 서비스 방화벽에 추가할 수 있습니다.

> [!IMPORTANT]
> 가상 컴퓨터 공용 IP 주소가 정적으로 구성되었는지 확인합니다.
>
>Azure 자동화 하이브리드 작업자 구성에 대해 자세히 알아보려면 [하이브리드 Runbook Worker를 사용하여 데이터 센터 또는 클라우드의 리소스 자동화를 참조하세요.](../automation/automation-hybrid-runbook-worker.md#install-a-hybrid-runbook-worker)

하이브리드 작업자가 구성되면 [데이터 팩터리에서 사용 해'](#consume-with-data-factory)섹션에 설명된 대로 웹후크를 만듭니다.  여기서 유일한 차이점은 Webhook을 구성할 때 > **하이브리드 작업자에서** **실행**옵션을 선택하는 것입니다.

하이브리드 작업자를 사용한 웹후크 예제:

![하이브리드 작업자 웹후크 예제](./media/analysis-services-refresh-azure-automation/21.png)

## <a name="sample-powershell-runbook"></a>샘플 파워쉘 런북

다음 코드 조각은 PowerShell Runbook을 사용하여 Azure 분석 서비스 모델 새로 고침을 수행하는 방법의 예입니다.

```powershell
param
(
    [Parameter (Mandatory = $false)]
    [object] $WebhookData,

    [Parameter (Mandatory = $false)]
    [String] $DatabaseName,
    [Parameter (Mandatory = $false)]
    [String] $AnalysisServer,
    [Parameter (Mandatory = $false)]
    [String] $RefreshType
)

$_Credential = Get-AutomationPSCredential -Name "ServicePrincipal"

# If runbook was called from Webhook, WebhookData will not be null.
if ($WebhookData)
{ 
    # Retrieve AAS details from Webhook request body
    $atmParameters = (ConvertFrom-Json -InputObject $WebhookData.RequestBody)
    Write-Output "CredentialName: $($atmParameters.CredentialName)"
    Write-Output "AnalysisServicesDatabaseName: $($atmParameters.AnalysisServicesDatabaseName)"
    Write-Output "AnalysisServicesServer: $($atmParameters.AnalysisServicesServer)"
    Write-Output "DatabaseRefreshType: $($atmParameters.DatabaseRefreshType)"
    
    $_databaseName = $atmParameters.AnalysisServicesDatabaseName
    $_analysisServer = $atmParameters.AnalysisServicesServer
    $_refreshType = $atmParameters.DatabaseRefreshType
 
    Invoke-ProcessASDatabase -DatabaseName $_databaseName -RefreshType $_refreshType -Server $_analysisServer -ServicePrincipal -Credential $_credential
}
else 
{
    Invoke-ProcessASDatabase -DatabaseName $DatabaseName -RefreshType $RefreshType -Server $AnalysisServer -ServicePrincipal -Credential $_Credential
}
```


## <a name="next-steps"></a>다음 단계

[샘플](analysis-services-samples.md)  
[나머지 API](https://docs.microsoft.com/rest/api/analysisservices/servers)
