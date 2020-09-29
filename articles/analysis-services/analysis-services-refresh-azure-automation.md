---
title: Azure Automation을 사용하여 Azure Analysis Services 모델 새로 고침 | Microsoft Docs
description: 이 문서에서는 Azure Automation을 사용하여 Azure Analysis Services의 모델 새로 고침을 코딩하는 방법을 설명합니다.
author: chrislound
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 05/07/2020
ms.author: chlound
ms.openlocfilehash: 61679e1ee3843ed866ef12138a0edeff8877b1e6
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91448592"
---
# <a name="refresh-with-azure-automation"></a>Azure Automation을 사용하여 새로 고침

Azure Automation과 PowerShell Runbook을 사용하여 Azure Analysis 테이블 형식 모델에서 자동 데이터 새로 고침 작업을 수행할 수 있습니다.  

이 문서의 예제에서는 [SqlServer PowerShell 모듈](https://docs.microsoft.com/powershell/module/sqlserver/?view=sqlserver-ps)을 사용 합니다. 모델 새로 고침을 보여주는 샘플 PowerShell Runbook이 이 문서의 뒷부분에서 제공됩니다.  

## <a name="authentication"></a>인증

모든 호출은 유효한 Azure Active Directory(OAuth 2) 토큰을 사용하여 인증되어야 합니다.  이 문서의 예제에서는 SPN (서비스 주체)을 사용 하 여 Azure Analysis Services에 인증 합니다. 자세히 알아보려면 [Azure Portal를 사용 하 여 서비스 주체 만들기](../active-directory/develop/howto-create-service-principal-portal.md)를 참조 하세요.

## <a name="prerequisites"></a>사전 요구 사항

> [!IMPORTANT]
> 다음 예에서는 Azure Analysis Services 방화벽을 사용하지 않는다고 가정합니다. 방화벽이 사용 되는 경우 요청 개시자의 공용 IP 주소를 방화벽 규칙에 포함 해야 합니다.

### <a name="install-sqlserver-modules-from-powershell-gallery"></a>PowerShell 갤러리에서 SqlServer 모듈을 설치합니다.

1. Azure Automation 계정에서 **모듈**과 **갤러리 찾아보기**를 차례로 클릭합니다.

2. 검색 창에서 **SqlServer**를 검색합니다.

    ![검색 모듈](./media/analysis-services-refresh-azure-automation/1.png)

3. SqlServer를 선택한 다음, **가져오기**를 클릭합니다.
 
    ![가져오기 모듈](./media/analysis-services-refresh-azure-automation/2.png)

4. **확인**을 클릭합니다.
 
### <a name="create-a-service-principal-spn"></a>SPN(서비스 사용자 이름) 만들기

서비스 사용자 만들기에 대해 알아보려면 [Azure Portal을 사용하여 서비스 사용자 만들기](../active-directory/develop/howto-create-service-principal-portal.md)를 참조하세요.

### <a name="configure-permissions-in-azure-analysis-services"></a>Azure Analysis Services에서 권한 구성
 
직접 만든 서비스 사용자에게는 서버에 대한 서버 관리자 권한이 있어야 합니다. 자세히 알아보려면 [서버 관리자 역할에 서비스 사용자 추가](analysis-services-addservprinc-admins.md)를 참조하세요.

## <a name="design-the-azure-automation-runbook"></a>Azure Automation Runbook 디자인

1. Automation 계정에서 서비스 사용자를 안전하게 저장하는 데 사용할 **자격 증명** 리소스를 만듭니다.

    !["자격 증명 추가" 작업이 선택 된 "자격 증명" 페이지를 보여 주는 스크린샷](./media/analysis-services-refresh-azure-automation/6.png)

2. 자격 증명에 대한 세부 정보를 입력합니다. **사용자 이름**에 서비스 사용자 응용 프로그램 ID (appid)를 입력 하 고 **암호**에 서비스 사용자 암호를 입력 합니다.

    ![자격 증명 만들기](./media/analysis-services-refresh-azure-automation/7.png)

3. Automation Runbook을 가져옵니다.

    !["Runbook 가져오기" 작업을 선택 하 여 "Runbook" 페이지를 보여 주는 스크린샷](./media/analysis-services-refresh-azure-automation/8.png)

4. [Refresh-Model.ps1](#sample-powershell-runbook) 파일을 찾고 **이름**과 **설명**을 입력한 다음, **만들기**를 클릭합니다.

    > [!NOTE]
    > 이 문서의 맨 아래에 있는 [샘플 Powershell Runbook](#sample-powershell-runbook) 의 스크립트 섹션을 사용 하 여 Refresh-Model.ps1 라는 파일을 만들고 로컬 컴퓨터에 저장 하 여 Runbook으로 가져옵니다.

    ![Runbook 가져오기](./media/analysis-services-refresh-azure-automation/9.png)

5. Runbook이 생성되면 편집 모드로 자동 전환됩니다.  **게시**를 선택합니다.

    ![Runbook 게시](./media/analysis-services-refresh-azure-automation/10.png)

    > [!NOTE]
    > 이전에 만든 자격 증명 리소스는 **Get-AutomationPSCredential** 명령을 사용하여 Runbook을 통해 검색됩니다.  그런 다음, 이 명령은 **Invoke-ProcessASADatabase** PowerShell 명령으로 전달되어 Azure Analysis Services에 대한 인증을 수행합니다.

6. **시작**을 클릭하여 Runbook을 테스트합니다.

    !["시작" 작업이 선택 된 "개요" 페이지를 보여 주는 스크린샷](./media/analysis-services-refresh-azure-automation/11.png)

7. **DATABASENAME**, **ANALYSISSERVER** 및 **REFRESHTYPE** 매개 변수를 입력한 다음, **확인**을 클릭합니다. Runbook을 수동으로 실행하는 경우에는 **WEBHOOKDATA** 매개 변수가 필요하지 않습니다.

    ![Runbook 시작](./media/analysis-services-refresh-azure-automation/12.png)

Runbook 실행에 성공하면 다음과 같은 출력이 표시됩니다.

![실행 성공](./media/analysis-services-refresh-azure-automation/13.png)

## <a name="use-a-self-contained-azure-automation-runbook"></a>자체 포함 Azure Automation Runbook 사용

예정된 시간에 Azure Analysis Services 모델 새로 고침을 트리거하도록 Runbook을 구성할 수 있습니다.

다음과 같이 구성할 수 있습니다.

1. Automation Runbook에서 **일정**과 **일정 추가**를 차례로 클릭합니다.
 
    ![일정 만들기](./media/analysis-services-refresh-azure-automation/14.png)

2. **일정** > **새 일정 만들기**를 클릭한 다음, 세부 정보를 입력합니다.

    ![일정 구성](./media/analysis-services-refresh-azure-automation/15.png)

3. **만들기**를 클릭합니다.

4. 일정에 대한 매개 변수를 입력합니다. Runbook이 트리거될 때마다 사용됩니다. 일정을 통해 실행할 때는 **WEBHOOKDATA** 매개 변수를 비워둬야 합니다.

    ![매개 변수 구성](./media/analysis-services-refresh-azure-automation/16.png)

5. **확인**을 클릭합니다.

## <a name="consume-with-data-factory"></a>Data Factory에서 사용

Azure Data Factory를 사용하여 Runbook을 사용하려면 먼저 Runbook에 대한 **Webhook**를 만듭니다. **Webhook**은 Azure Data Factory 웹 작업을 통해 호출할 수 있는 URL을 제공합니다.

> [!IMPORTANT]
> **Webhook**을 만들려면 Runbook의 상태가 **게시됨**이어야 합니다.

1. Automation Runbook에서 **Webhook**를 클릭한 후 **Webhook 추가**를 클릭합니다.

   ![Webhook 추가](./media/analysis-services-refresh-azure-automation/17.png)

2. Webhook에 이름과 만료 정보를 지정합니다.  이름은 Automation Runbook 내부에서 Webhook만 식별하며 URL의 일부를 구성하지는 않습니다.

   >[!CAUTION]
   >마법사를 닫기 전에 URL을 복사해야 합니다. 닫은 후에는 되돌릴 수 없습니다.
    
   ![Webhook 구성](./media/analysis-services-refresh-azure-automation/18.png)

    Webhook에 대한 매개 변수는 비워둘 수 있습니다.  Azure Data Factory 웹 작업을 구성할 때 매개 변수는 웹 호출의 본문으로 전달될 수 있습니다.

3. Data Factory에서 **웹 작업**을 구성합니다.

### <a name="example"></a>예제

   ![웹 작업 예제](./media/analysis-services-refresh-azure-automation/19.png)

**URL**은 Webhook에서 만든 URL입니다.

**본문**은 JSON 문서이며, 다음 속성을 포함해야 합니다.


|속성  |값  |
|---------|---------|
|**AnalysisServicesDatabase**     |Azure Analysis Services 데이터베이스의 이름 <br/> 예제: AdventureWorksDB         |
|**AnalysisServicesServer**     |Azure Analysis Services 서버 이름 <br/> 예: https:\//westus.asazure.windows.net/servers/myserver/models/AdventureWorks/         |
|**DatabaseRefreshType**     |수행할 새로 고침 유형 <br/> 예제: 전체         |

JSON 본문 예:

```json
{
    "AnalysisServicesDatabaseName": "AdventureWorksDB",
    "AnalysisServicesServer": "asazure://westeurope.asazure.windows.net/MyAnalysisServer",
    "DatabaseRefreshType": "Full"
}
```

이러한 매개 변수는 Runbook PowerShell 스크립트에 정의되어 있습니다.  웹 작업이 실행될 때 전달된 JSON 페이로드는 WEBHOOKDATA입니다.

이것은 역직렬화되어 PowerShell 매개 변수로 저장된 다음, Invoke-ProcesASDatabase PowerShell 명령에 사용됩니다.

![역직렬화된 Webhook](./media/analysis-services-refresh-azure-automation/20.png)

## <a name="use-a-hybrid-worker-with-azure-analysis-services"></a>Azure Analysis Services에서 Hybrid Worker 사용

고정 공용 IP 주소를 사용하는 Azure Virtual Machine은 Azure Automation Hybrid Worker로 사용할 수 있습니다.  이러한 공용 IP 주소는 Azure Analysis Services 방화벽에 추가할 수 있습니다.

> [!IMPORTANT]
> Virtual Machine 공용 IP 주소가 고정으로 구성되어 있는지 확인하십시오.
>
>Azure Automation Hybrid Worker 구성에 대해 알아보려면 [Hybrid Runbook Worker 설치](../automation/automation-hybrid-runbook-worker.md#hybrid-runbook-worker-installation)를 참조하세요.

Hybrid Worker가 구성되면 [Data Factory에서 사용](#consume-with-data-factory) 섹션의 설명에 따라 Webhook를 만듭니다.  여기서 유일한 차이점은 Webhook를 구성할 때 **실행 위치** > **Hybrid Worker** 옵션을 선택하는 것입니다.

Hybrid Worker를 사용하는 Webhook 예:

![Hybrid Worker Webhook 예](./media/analysis-services-refresh-azure-automation/21.png)

## <a name="sample-powershell-runbook"></a>샘플 PowerShell Runbook

다음 코드 조각은 PowerShell Runbook을 사용하여 Azure Analysis Services 모델 새로 고침을 수행하는 방법의 예입니다.

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
[REST API](https://docs.microsoft.com/rest/api/analysisservices/servers)
