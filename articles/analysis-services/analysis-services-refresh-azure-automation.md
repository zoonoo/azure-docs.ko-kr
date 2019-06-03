---
title: Azure Automation을 사용 하 여 Azure Analysis Services 모델 새로 고침 | Microsoft Docs
description: Azure Automation을 사용 하 여 모델 새로 고침을 코딩 하는 방법에 알아봅니다.
author: chrislound
manager: kfile
ms.service: analysis-services
ms.topic: conceptual
ms.date: 04/26/2019
ms.author: chlound
ms.openlocfilehash: 4cae93cff594ad561973f8029ea7335dc4c60263
ms.sourcegitcommit: 8e76be591034b618f5c11f4e66668f48c090ddfd
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/29/2019
ms.locfileid: "66356993"
---
# <a name="refresh-with-azure-automation"></a>Azure Automation을 사용하여 새로 고침

Azure Automation 및 PowerShell Runbook을 사용 하 여 Azure Analysis 테이블 형식 모델에서 자동된 데이터 새로 고침 작업을 수행할 수 있습니다.  

이 문서의 예제에서에서 사용 하는 [PowerShell SqlServer 모듈](https://docs.microsoft.com/powershell/module/sqlserver/?view=sqlserver-ps)합니다.

샘플 모델을 새로 고침 하는 방법을 보여 주는 PowerShell Runbook은이 문서의 뒷부분에 제공 됩니다.  

## <a name="authentication"></a>Authentication

유효한 Azure Active Directory (OAuth 2) 토큰을 사용 하 여 모든 호출을 인증 되어야 합니다.  이 문서의 예제에에서는 SPN (서비스 사용자)를 사용 하 여 Azure Analysis Services에 인증할 수 됩니다.

서비스 주체 만들기에 대 한 자세한 내용은 참조 하세요 [Azure portal을 사용 하 여 서비스 주체를 만들려면](../active-directory/develop/howto-create-service-principal-portal.md)합니다.

## <a name="prerequisites"></a>필수 조건

> [!IMPORTANT]
> 다음 예제에서는 Azure Analysis Services 방화벽을 비활성화 하는 것으로 가정 합니다. 방화벽이 설정 되어 요청 초기자의 공용 IP 주소를 방화벽에서 허용 목록에 추가 해야 합니다.

### <a name="install-sqlserver-modules-from-powershell-gallery"></a>PowerShell 갤러리의 SqlServer 모듈을 설치 합니다.

1. Azure Automation 계정에서 클릭 **모듈**, 한 다음 **갤러리 찾아보기**합니다.

2. 검색 표시줄에서 검색 **SqlServer**합니다.

    ![검색 모듈](./media/analysis-services-refresh-azure-automation/1.png)

3. Sql Server를 선택한 다음 클릭 **가져오기**합니다.
 
    ![모듈 가져오기](./media/analysis-services-refresh-azure-automation/2.png)

4. **확인**을 클릭합니다.
 
### <a name="create-a-service-principal-spn"></a>서비스 주체 (SPN) 만들기

서비스 주체 만들기에 대 한 자세한 참조 [Azure portal을 사용 하 여 서비스 주체를 만들려면](../active-directory/develop/howto-create-service-principal-portal.md)합니다.

### <a name="configure-permissions-in-azure-analysis-services"></a>Azure Analysis Services에서 사용 권한 구성
 
만든 서비스 주체는 서버의 서버 관리자 권한이 있어야 합니다. 자세히 알아보려면 [서버 관리자 역할에 서비스 사용자 추가](analysis-services-addservprinc-admins.md)를 참조하세요.

## <a name="design-the-azure-automation-runbook"></a>Azure Automation Runbook을 디자인 합니다.

1. Automation 계정을 만듭니다는 **자격 증명** 리소스 서비스 주체를 안전 하 게 저장 하는 데 사용할입니다.

    ![자격 증명 만들기](./media/analysis-services-refresh-azure-automation/6.png)

2. 자격 증명에 대 한 세부 정보를 입력 합니다.  에 대 한는 **사용자 이름**, 입력는 **SPN ClientId**에 대 한를 **암호**, 입력 합니다 **SPN 비밀**.

    ![자격 증명 만들기](./media/analysis-services-refresh-azure-automation/7.png)

3. Automation Runbook 가져오기

    ![Runbook 가져오기](./media/analysis-services-refresh-azure-automation/8.png)

4. 찾아보기 합니다 **새로 고침 Model.ps1** 파일을 제공을 **이름** 및 **설명**, 클릭 하 고 **만들기**합니다.

    ![Runbook 가져오기](./media/analysis-services-refresh-azure-automation/9.png)

5. Runbook을 만든 경우 자동으로 편집 모드로 전환 됩니다.  **게시**를 선택합니다.

    ![Runbook 게시](./media/analysis-services-refresh-azure-automation/10.png)

    > [!NOTE]
    > 생성 된 자격 증명 리소스 이전에 runbook에서 사용 하 여 검색 합니다 **Get-automationpscredential** 명령입니다.  이 명령은 전달 되어는 **Invoke ProcessASADatabase** Azure Analysis Services에 대 한 인증을 수행 하려면 PowerShell 명령입니다.

6. 클릭 하 여 runbook을 테스트할 **시작**합니다.

    ![Runbook 시작](./media/analysis-services-refresh-azure-automation/11.png)

7. 입력 합니다 **DATABASENAME**, **ANALYSISSERVER**, 및 **REFRESHTYPE** 매개 변수 및 클릭 한 다음 **확인**합니다. 합니다 **WEBHOOKDATA** Runbook이 수동으로 실행 하는 경우 매개 변수가 필요 하지 않습니다.

    ![Runbook 시작](./media/analysis-services-refresh-azure-automation/12.png)

Runbook이 성공적으로 실행 하는 경우 다음과 유사한 출력이 표시 됩니다.

![성공적으로 실행](./media/analysis-services-refresh-azure-automation/13.png)

## <a name="use-a-self-contained-azure-automation-runbook"></a>자체 포함 된 Azure Automation Runbook 사용

Runbook 일정에 따라 Azure Analysis Services 모델 새로 고침 트리거를 구성할 수 있습니다.

이 다음과 같이 구성할 수 있습니다.

1. Automation Runbook을 클릭 **일정**, 한 다음 **일정 추가**합니다.
 
    ![일정 만들기](./media/analysis-services-refresh-azure-automation/14.png)

2. 클릭 **일정** > **새 일정을 만들려면**, 다음 세부 정보를 입력 합니다.

    ![일정 구성](./media/analysis-services-refresh-azure-automation/15.png)

3. **만들기**를 클릭합니다.

4. 일정에 대 한 매개 변수를 설정 합니다. 이러한 사용할 때마다 Runbook을 트리거합니다. 합니다 **WEBHOOKDATA** 매개 변수는 비워 둘 수는 일정을 통해 실행 하는 경우.

    ![매개 변수 구성](./media/analysis-services-refresh-azure-automation/16.png)

5. **확인**을 클릭합니다.

## <a name="consume-with-data-factory"></a>Data Factory를 사용 하 여 사용

Azure Data Factory를 사용 하 여 runbook을 사용을 먼저 만듭니다는 **웹 후크** runbook에 대 한 합니다. 합니다 **웹 후크** Azure Data Factory 웹 작업을 통해 호출할 수 있는 URL을 제공 합니다.

> [!IMPORTANT]
> 만들려는 **웹 후크**, Runbook의 상태 여야 합니다 **게시**합니다.

1. Automation Runbook에서 클릭 **웹 후크**를 클릭 하 고 **웹 후크 추가**합니다.

   ![웹 후크 추가](./media/analysis-services-refresh-azure-automation/17.png)

2. 이름 및 만료 매개 변수로 웹 후크를 제공 합니다.  Automation Runbook 내에서 웹 후크만 하 게 식별 하는 이름, URL의 일부를 형성 하지 않습니다.

   >[!CAUTION]
   >URL을 확인할 수 없는 것 다시 한 번 닫을 하므로 마법사를 닫기 전에 복사한 확인 합니다.
    
   ![웹 후크 구성](./media/analysis-services-refresh-azure-automation/18.png)

    Webhook에 대 한 매개 변수를 비워 둘 수 있습니다.  Azure Data Factory 웹 작업을 구성할 때 웹 호출의 본문에 매개 변수를 전달할 수 있습니다.

3. Data Factory에서 구성 된 **웹 작업**

### <a name="example"></a>예

   ![예제 웹 작업](./media/analysis-services-refresh-azure-automation/19.png)

합니다 **URL** Webhook에서 만든 URL입니다.

합니다 **본문** 은 다음 속성을 포함 하는 JSON 문서:


|자산  |Value  |
|---------|---------|
|**AnalysisServicesDatabase**     |Azure Analysis Services 데이터베이스의 이름 <br/> 예제: AdventureWorksDB         |
|**AnalysisServicesServer**     |Azure Analysis Services 서버 이름입니다. <br/> 예: https:\//westus.asazure.windows.net/servers/myserver/models/AdventureWorks/         |
|**DatabaseRefreshType**     |새로 고침 수행을 형식입니다. <br/> 예제: 전체         |

예제 JSON 본문:

```json
{
    "AnalysisServicesDatabaseName": "AdventureWorksDB",
    "AnalysisServicesServer": "asazure://westeurope.asazure.windows.net/MyAnalysisServer",
    "DatabaseRefreshType": "Full"
}
```

이러한 매개 변수는 PowerShell 스크립트 runbook에 정의 됩니다.  웹 작업 실행 될 때 전달 된 JSON 페이로드 WEBHOOKDATA는 합니다.

이 deserialize 및 다음 Invoke ProcesASDatabase PowerShell 명령에서 사용 되는 PowerShell 매개 변수로 저장 합니다.

![Deserialize 된 웹 후크](./media/analysis-services-refresh-azure-automation/20.png)

## <a name="use-a-hybrid-worker-with-azure-analysis-services"></a>Hybrid Worker를 사용 하 여 Azure Analysis Services를 사용 하 여

고정 공용 IP 주소를 사용 하 여 Azure Virtual Machine는 Azure Automation Hybrid Worker로 사용할 수 있습니다.  이 공용 IP 주소는 다음 Azure Analysis Services 방화벽에 추가할 수 있습니다.

> [!IMPORTANT]
> 가상 머신에 공용 IP 주소를 정적으로 구성 되었는지 확인 합니다.
>
>Azure Automation Hybrid Worker를 구성 하는 방법에 대 한 자세한 내용은 참조 하세요 [Hybrid Runbook Worker를 사용 하 여 데이터 센터 또는 클라우드 리소스를 자동화할](../automation/automation-hybrid-runbook-worker.md#install-a-hybrid-runbook-worker)합니다.

Hybrid Worker 구성 되 면 섹션에 설명 된 대로 웹 후크를 만듭니다 [Consume Data Factory를 사용 하 여](#consume-with-data-factory)입니다.  여기에서 유일한 차이점은 선택 하는 **에서 실행** > **Hybrid Worker** 웹 후크를 구성 하는 경우 옵션입니다.

Hybrid Worker를 사용 하 여 웹 후크 예제:

![예제 하이브리드 작업자 웹 후크](./media/analysis-services-refresh-azure-automation/21.png)

## <a name="sample-powershell-runbook"></a>예제 PowerShell Runbook

다음 코드 조각은 PowerShell Runbook을 사용 하 여 Azure Analysis Services 모델 새로 고침을 수행 하는 방법의 예시입니다.

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
