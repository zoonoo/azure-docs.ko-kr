---
title: PowerShell을 사용 하 여 Power BI 등록 및 스캔 (미리 보기)
description: PowerShell을 사용 하 여 Azure 부서의 범위에서 Power BI 테 넌 트를 등록 하 고 검색 하는 방법을 알아봅니다.
author: darrenparker
ms.author: hophan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 10/12/2020
ms.openlocfilehash: f0b541baf49307006c18f07d92bd409763a29e3a
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/03/2020
ms.locfileid: "96553508"
---
# <a name="use-powershell-to-register-and-scan-power-bi-in-azure-purview-preview"></a>PowerShell을 사용 하 여 Azure 부서의 범위 (미리 보기)에서 Power BI 등록 및 스캔 

이 문서에서는 PowerShell을 사용 하 여 Azure 부서의 범위 카탈로그에서 Power BI 테 넌 트의 스캔을 설정 하는 방법을 보여 줍니다.

## <a name="power-bi-authentication-background"></a>Power BI 인증 배경

부서의 범위 catalog는 Power BI 테 넌 트에서 아티팩트를 검색 하기 위해 Power BI 관리 API에 연결 해야 합니다. Power BI Admin API는 현재 두 가지 유형의 인증을 지원 합니다.

- MSI (관리 id).
- 위임 된 사용자 인증.

> [!Note]
> 위임 된 사용자 인증이 필요한 경우가 아니면 MSI를 사용 하는 것이 좋습니다.

## <a name="create-a-security-group"></a>보안 그룹 만들기

모든 부서의 범위 카탈로그에는 검색을 사용할 수 있도록 Power BI 테 넌 트에 대 한 액세스 권한을 부여 해야 하는 고유한 시스템 할당 관리 id가 있습니다. 카탈로그 이름은 Azure Portal에서 해당 id를 찾는 데 사용할 수 있습니다.

1. [Azure Portal](https://portal.azure.com)에서 Azure Active Directory를 검색 합니다.
1. [기본 그룹 만들기 및 Azure Active Directory를 사용 하 여 멤버 추가](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-create-azure-portal)를 수행 하 여 Azure Active Directory에서 새 보안 그룹을 만듭니다.

    > [!Tip]
    > 사용할 보안 그룹이 이미 있는 경우이 단계를 건너뛸 수 있습니다.

1. **그룹 유형** 으로 보안을 선택 해야 합니다.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/security-group.png" alt-text="보안 그룹 종류":::

1. 멤버를 선택 하 고 **+ 구성원 추가** 를 선택 하 여 카탈로그의 관리 되는 id를이 보안 그룹에 추가 합니다.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/add-group-member.png" alt-text="그룹에 카탈로그의 관리 되는 인스턴스 추가":::

1. 카탈로그를 검색 하 고 선택 합니다.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/add-catalog-to-group-by-search.png" alt-text="검색 하 여 카탈로그 추가":::

1. 추가 되었음을 나타내는 성공 알림이 표시 됩니다.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/success-add-catalog-msi.png" alt-text="카탈로그 MSI 추가 성공":::

## <a name="associate-the-security-group-with-power-bi"></a>Power BI와 보안 그룹 연결

1. [Power BI 관리 포털](https://app.powerbi.com/admin-portal/tenantSettings?allowServicePrincipalsUseReadAdminAPIsUI=1)에 로그인 합니다. 이 기능 플래그를 추가  `allowServicePrincipalsUseReadAdminAPIsUI=1` 합니다. 이 플래그는 보안 그룹을 연결할 수 있는 기능을 사용 하도록 설정 합니다. 예를 들면 다음과 같습니다.

    ```http
    https://app.powerbi.com/admin-portal/tenantSettings?allowServicePrincipalsUseReadAdminAPIsUI=1`
    ```

    > [!Important]
    > 테 넌 트 설정 페이지를 보려면 Power BI 관리자 여야 합니다.

1. **개발자 설정**  >  **서비스 사용자가 읽기 전용 Power BI api (미리 보기)를 사용 하도록 허용을** 선택 합니다.
1. **특정 보안 그룹** 을 선택 합니다.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/allow-service-principals-power-bi-admin.png" alt-text="서비스 사용자가 읽기 전용 Power BI admin API 권한을 가져오도록 허용 하는 방법을 보여 주는 이미지 ":::

    > [!Caution]
    > 사용자가 만든 보안 그룹 (데이터 카탈로그 관리 되는 id를 구성원으로 포함)을 사용 하 여 읽기 전용 Power BI 관리 Api를 사용 하는 경우이 테 넌 트의 모든 Power BI 아티팩트에 대 한 메타 데이터 (예: 대시보드 및 보고서 이름, 소유자, 설명 등)에 액세스 하도록 허용할 수도 있습니다. 메타 데이터를 Azure 부서의 범위로 끌어온 후에는 Power BI 권한이 없는 부서의 범위의 사용 권한을 통해 해당 메타 데이터를 볼 수 있는 사람을 결정 합니다.

    > [!Note]
    > 개발자 설정에서 보안 그룹을 제거할 수 있지만 이전에 추출 된 메타 데이터는 부서의 범위 계정에서 제거 되지 않습니다. 별도로 삭제할 수 있습니다.

## <a name="register-power-bi-and-set-up-a-scan"></a>Power BI 등록 및 검색 설정

Power BI 테 넌 트의 관리 API에 연결할 수 있는 카탈로그 권한을 제공 했으므로 카탈로그에서 검색을 설정 해야 합니다. 이렇게 하려면 PowerShell 스크립트를 구성 하 고 실행 합니다.

1. ADC PowerShell cmdlet을 다운로드 하 고 압축을 풉니다.
1. 스크립트의 맨 위에 있는 할당에 대 한 값을 제공 하 여 스크립트를 구성 합니다.

    ```PowerShell
    #Purview Account Info
    $azuretenantId = '<Tenant Id>'
    $azuresubscriptionId = '<Tenant Id>'
    $azureResourceGroupName = '<Resource Group Name>'
    $azurePurviewAccountName = '<Catalog Name>'
    $createCatalog = $false #Change to true if you need a new catalog to be created
    $azureCatalogLocation = 'East Us' #The region of your account
    $dataSourceName = 'pbi-msi-datasource01' #provide a unique data source name
    $dataScanName = 'scan01' #provide a unique scan name

    #PowerShell Command Module Path
    $ModulePath = '<Full path to where you extracted the PS zip files>\Microsoft.DataCatalog.Management.Commands.dll'
    Import-Module $ModulePath

    If ($createCatalog -eq $true)
    {
      Connect-AzAccount -Tenant $azuretenantId -SubscriptionId $azuresubscriptionId
      Set-AzDataCatalog -ResourceGroupName $azureResourceGroupName -Name $azurePurviewAccountName -Location $azureCatalogLocation
    }

    Set-AzDataCatalogSessionSettings -DataCatalogSession -UserAuthentication -TenantId $azuretenantId  -DataCatalogAccountName $azurePurviewAccountName

    Set-AzDataCatalogDataSource -Name $dataSourceName -AccountType PowerBI  -Tenant $azuretenantId

    Set-AzDataCatalogScan -DataSourceName $dataSourceName -ScanName $dataScanName -PowerBIManagedInstanceMsi
    ```

    > [!Note]
    > 명령을 실행 하는 구독에 대 한 참가자 또는 소유자 여야 합니다.

1. 다음 스크립트를 실행 하 여 검색을 시작 합니다.

    ```PowerShell
    Start-AzDataCatalogScan -DataSourceName $dataSourceName -Name $dataScanName -AsJob
    ```

## <a name="register-and-scan-power-bi"></a>Power BI 등록 및 검색

권장 되는 인증 방법은 MSI입니다. 그러나 카탈로그와 다른 Azure 테 넌 트에 있는 Power BI 테 넌 트를 검색 하려면 위임 된 인증을 사용 합니다.

위임 된 인증을 수행 하려면 관리자 자격 증명 뿐만 아니라 관리자 자격 증명을 Power BI 해야 합니다. 또한 Azure 앱을 만들고 Power BI 테 넌 트에 부여 해야 합니다. 모든 권한을 ReadAll.

1. [Azure Portal](https://portal.azure.com) 로 이동 하 여 **앱 등록** 을 검색 합니다.

1. **앱 등록** 에서 **+ 새로 만들기 등록** 을 선택 합니다.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/new-app-registration.png" alt-text="새 Azure 앱 등록을 만드는 방법을 보여 주는 이미지":::

1. 앱의 이름을 입력합니다.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/register-new-app.png" alt-text="새 앱 등록":::

1. 앱이 만들어지면 **API 권한** 을 선택 하 고 **+ 권한 추가** 를 선택 합니다.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/app-permissions.png" alt-text="앱에 사용 권한을 추가 하는 방법을 보여 주는 이미지":::

1. **요청 API 권한** 에서 **Power BI 서비스** 를 선택 합니다.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/select-power-bi-service.png" alt-text="PBI 서비스를 선택 하는 방법을 보여 주는 이미지":::

1. **위임 된 권한** 및 **테 넌 트** 를 선택 합니다. 모두 읽기. 그런 다음 **권한 추가** 를 선택합니다.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/request-api-permissions.png" alt-text="API 권한을 요청 하는 방법을 보여 주는 이미지":::

1. **관리자 동의 허용** 을 선택합니다.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/grant-admin-perms.png" alt-text="관리자 동의를 부여 하는 방법을 보여 주는 이미지":::

1. **응용 프로그램 (클라이언트) id** 와 **디렉터리 (테 넌 트) id** 값을 복사 합니다.  검색을 설정할 때 이러한 값을 사용 합니다.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/copy-client-and-tenantid.png" alt-text="클라이언트 및 테 넌 트 Id 복사를 보여 주는 이미지":::

1. PowerShell에서 검색을 구성 합니다. 스크립트에서 자격 증명을 묻는 메시지를 표시 합니다. 사용 하려는 Azure 구독에 대 한 참가자 역할 및 부서의 범위 데이터 원본 관리자 역할이 있어야 합니다.

    ```PowerShell
    # Purview Account Info
    $azuretenantId = '<Tenant Id>'
    $azuresubscriptionId = '<Tenant Id>'
    $azureResourceGroupName = '<Resource Group Name>'
    $azurePurviewAccountName = '<Catalog Name>'
    $createCatalog = $false
    $azureCatalogLocation = 'East Us' #The region of your account
    $dataSourceName = 'pbi-delegated-datasource01' #provide a unique data source name
    $dataScanName = 'scan01' #provide a unique scan name

    # Power BI Tenant Info
    $powerBITenantIdToScan = '<Power BI Tenant ID copied from above steps>'
    $ServicePrincipalId = '<Client ID copied from above steps>'
    $UserName = '<Power BI Admin emil ex: admin@firsttomarket.onmicrosoft.com>'
    $Password = '<Power BI Admin Password>'

    #PowerShell Command Module Path
    $ModulePath = '<Full path to where you extracted the PS zip files>\Microsoft.DataCatalog.Management.Commands.dll'
    Import-Module $ModulePath

    #Commands To Create catalog, Create DataSource, Create Datascan, Start Scan
    If($createCatalog -eq $true)
    {
      Connect-AzAccount -Tenant $azuretenantId -SubscriptionId $azuresubscriptionId
      Set-AzDataCatalog -ResourceGroupName $azureResourceGroupName -Name $azurePurviewAccountName -Location $azureCatalogLocation
    }

    Set-AzDataCatalogSessionSettings -DataCatalogSession -UserAuthentication -TenantId $azuretenantId   -DataCatalogAccountName $azurePurviewAccountName

    Set-AzDataCatalogDataSource -Name $dataSourceName -AccountType PowerBI -Tenant $powerBITenantIdToScan

    Set-AzDataCatalogScan -DataSourceName $dataSourceName -ScanName $dataScanName -PowerBIDelegated -ServicePrincipalId $ServicePrincipalId -UserName $UserName -Password $Password
    ```

1. 검색을 실행 합니다.

      ```PowerShell
      Start-AzDataCatalogScan -DataSourceName $dataSourceName -Name $dataScanName -AsJob
      ```

## <a name="next-steps"></a>다음 단계

Azure 부서의 범위를 시작 하려면 [빠른 시작: Azure 부서의 범위 계정 만들기](create-catalog-portal.md)를 참조 하세요.
