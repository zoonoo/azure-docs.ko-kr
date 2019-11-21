---
title: Enable Azure Active Directory for Azure-SSIS Integration Runtime
description: 이 문서에서는 Azure Data Factory에 대해 관리 ID와 함께 Azure Active Directory 인증을 사용하도록 설정하여 Azure-SSIS Integration Runtime을 만드는 방법을 설명합니다.
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: conceptual
author: swinarko
ms.author: sawinark
manager: mflasko
ms.date: 5/14/2019
ms.openlocfilehash: 6973e72b06d51241e883038936270fd0931365d7
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/20/2019
ms.locfileid: "74217707"
---
# <a name="enable-azure-active-directory-authentication-for-azure-ssis-integration-runtime"></a>Azure-SSIS Integration Runtime을 위한 Azure Active Directory 인증 활성화

This article shows you how to enable Azure Active Directory (Azure AD) authentication with the managed identity for your Azure Data Factory (ADF) and use it instead of conventional authentication methods (like SQL authentication) to:

- Create an Azure-SSIS Integration Runtime (IR) that will in turn provision SSIS catalog database (SSISDB) in Azure SQL Database server/Managed Instance on your behalf.

- Connect to various Azure resources when running SSIS packages on Azure-SSIS IR.

For more info about the managed identity for your ADF, see [Managed identiy for Data Factory](https://docs.microsoft.com/azure/data-factory/data-factory-service-identity).

> [!NOTE]
>-  In this scenario, Azure AD authentication with the managed identity for your ADF is only used in the creation and subsequent starting operations of your SSIS IR that will in turn provision and connect to SSISDB. For SSIS package executions, your SSIS IR will still connect to SSISDB using SQL authentication with fully managed accounts that are created during SSISDB provisioning.
>-  If you have already created your SSIS IR using SQL authentication, you can not reconfigure it to use Azure AD authentication via PowerShell at this time, but you can do so via Azure portal/ADF app. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="enable-azure-ad-on-azure-sql-database"></a>Azure SQL Database에서 Azure AD 활성화

Azure SQL Database 서버는 Azure AD 사용자로 데이터베이스 만들기를 지원합니다. 먼저 구성원으로 ADF에 대한 관리 ID를 사용하여 Azure AD 그룹을 만들어야 합니다. 다음으로, Azure SQL Database 서버에 대한 Active Directory 관리자로 Azure AD 사용자를 설정한 다음, SSMS(SQL Server Management Studio)에서 해당 사용자를 사용하여 연결합니다. 마지막으로, Azure-SSIS IR에서 ADF에 대한 관리 ID를 사용하여 사용자 대신 SSISDB를 만들도록 Azure AD 그룹을 나타내는 포함된 사용자를 만들어야 합니다.

### <a name="create-an-azure-ad-group-with-the-managed-identity-for-your-adf-as-a-member"></a>구성원으로 ADF에 대한 관리 ID를 사용하여 Azure AD 그룹을 만듭니다.

기존 Azure AD 그룹을 사용하거나 Azure AD PowerShell을 사용하여 새로 만들 수 있습니다.

1.  [Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2) 모듈을 설치합니다.

2.   `Connect-AzureAD`를 사용하여 로그인하고, 다음 cmdlet을 실행하여 그룹을 만들고, 변수에 저장합니다.

    ```powershell
    $Group = New-AzureADGroup -DisplayName "SSISIrGroup" `
                              -MailEnabled $false `
                              -SecurityEnabled $true `
                              -MailNickName "NotSet"
    ```

    결과는 다음 예제와 같으며 변수 값을 표시합니다.

    ```powershell
    $Group

    ObjectId DisplayName Description
    -------- ----------- -----------
    6de75f3c-8b2f-4bf4-b9f8-78cc60a18050 SSISIrGroup
    ```

3.  ADF의 관리 ID를 그룹에 추가합니다. You can follow the article [Managed identiy for Data Factory](https://docs.microsoft.com/azure/data-factory/data-factory-service-identity) to get the principal Managed Identity Object ID (e.g. 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc, but do not use Managed Identity Application ID for this purpose).

    ```powershell
    Add-AzureAdGroupMember -ObjectId $Group.ObjectId -RefObjectId 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc
    ```

    또한 나중에 그룹 구성원을 검사할 수도 있습니다.

    ```powershell
    Get-AzureAdGroupMember -ObjectId $Group.ObjectId
    ```

### <a name="configure-azure-ad-authentication-for-azure-sql-database-server"></a>Azure SQL Database 서버를 위한 Azure AD 인증 구성

다음 단계를 사용하여  [SQL을 통해 Azure AD 인증을 구성하고 관리](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure)할 수 있습니다.

1.  Azure Portal의 왼쪽 탐색 영역에서 **모든 서비스** -> **SQL 서버**를 선택합니다.

2.  Azure AD 인증을 사용하여 구성할 Azure SQL Database 서버를 선택합니다.

3.  블레이드의 **설정** 섹션에서 **Active Directory 관리자**를 선택합니다.

4.  명령 모음에서 **관리자 설정**을 선택합니다.

5.  Select an Azure AD user account to be made administrator of the server, and then select **Select.**

6.  명령 모음에서 **저장**을 선택합니다.

### <a name="create-a-contained-user-in-azure-sql-database-server-representing-the-azure-ad-group"></a>Azure AD 그룹을 나타내는 Azure SQL Database 서버에 포함된 사용자 만들기

이 다음 단계를 위해 [Microsoft SSMS(SQL Server Management Studio)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)가 필요합니다.

1. SSMS를 시작합니다.

2. In the **Connect to Server** dialog, enter your Azure SQL Database server name in the **Server name** field.

3. In the **Authentication** field, select **Active Directory - Universal with MFA support** (you can also use the other two Active Directory authentication types, see [Configure and manage Azure AD authentication with SQL](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure)).

4. In the **User name** field, enter the name of Azure AD account that you set as the server administrator, e.g. testuser@xxxonline.com.

5. select **Connect** and complete the sign-in process.

6. **개체 탐색기**에서 **데이터베이스** -> **시스템 데이터베이스** 폴더를 확장합니다.

7. **마스터** 데이터베이스를 마우스 오른쪽 단추로 클릭하고 **새 쿼리**를 선택합니다.

8. In the query window, enter the following T-SQL command, and select **Execute** on the toolbar.

   ```sql
   CREATE USER [SSISIrGroup] FROM EXTERNAL PROVIDER
   ```

   명령이 성공적으로 완료되고 그룹을 나타내는 포함된 사용자가 만들어집니다.

9. 쿼리 창을 지우고 다음 T-SQL 명령을 입력한 후, 도구 모음에서 **실행**을 선택합니다.

   ```sql
   ALTER ROLE dbmanager ADD MEMBER [SSISIrGroup]
   ```

   명령이 성공적으로 완료되면 포함된 사용자에게 데이터베이스(SSISDB)를 만들 수 있는 기능이 부여됩니다.

10. If your SSISDB was created using SQL authentication and you want to switch to use Azure AD authentication for your Azure-SSIS IR to access it, right-click on **SSISDB** database and select **New query**.

11. In the query window, enter the following T-SQL command, and select **Execute** on the toolbar.

    ```sql
    CREATE USER [SSISIrGroup] FROM EXTERNAL PROVIDER
    ```

    명령이 성공적으로 완료되고 그룹을 나타내는 포함된 사용자가 만들어집니다.

12. 쿼리 창을 지우고 다음 T-SQL 명령을 입력한 후, 도구 모음에서 **실행**을 선택합니다.

    ```sql
    ALTER ROLE db_owner ADD MEMBER [SSISIrGroup]
    ```

    명령이 성공적으로 완료되면 포함된 사용자에게 SSISDB에 액세스할 수 있는 기능이 부여됩니다.

## <a name="enable-azure-ad-on-azure-sql-database-managed-instance"></a>Azure SQL Database Managed Instance에서 Azure AD 활성화

Azure SQL Database Managed Instance는 직접 ADF에 대한 관리 ID로 데이터베이스 만들기를 지원합니다. Azure AD 그룹에 ADF에 대한 관리 ID를 조인하거나 Managed Instance에서 해당 그룹을 나타내는 포함된 사용자를 만들 필요는 없습니다.

### <a name="configure-azure-ad-authentication-for-azure-sql-database-managed-instance"></a>Azure SQL Database Managed Instance를 위한 Azure AD 인증 구성

Follow the steps in [Provision an Azure Active Directory administrator for your Managed Instance](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure#provision-an-azure-active-directory-administrator-for-your-managed-instance).

### <a name="add-the-managed-identity-for-your-adf-as-a-user-in-azure-sql-database-managed-instance"></a>ADF에 대한 관리 ID를 Azure SQL Database Managed Instance의 사용자로 추가

이 다음 단계를 위해 [Microsoft SSMS(SQL Server Management Studio)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)가 필요합니다.

1.  SSMS를 시작합니다.

2.  Connect to your Managed Instance using a SQL Server account that is a **sysadmin**. This is a temporary limitation that will be removed once Azure AD server principals (logins) for Azure SQL Database Managed Instance becomes GA. You will see the following error if you try to use an Azure AD admin account to create the login: Msg 15247, Level 16, State 1, Line 1 User does not have permission to perform this action.

3.  **개체 탐색기**에서 **데이터베이스** -> **시스템 데이터베이스** 폴더를 확장합니다.

4.  **마스터** 데이터베이스를 마우스 오른쪽 단추로 클릭하고 **새 쿼리**를 선택합니다.

5.  In the query window, execute the following T-SQL script to add the managed identity for your ADF as a user

    ```sql
    CREATE LOGIN [{your ADF name}] FROM EXTERNAL PROVIDER
    ALTER SERVER ROLE [dbcreator] ADD MEMBER [{your ADF name}]
    ALTER SERVER ROLE [securityadmin] ADD MEMBER [{your ADF name}]
    ```
    
    명령이 성공적으로 완료되면 ADF에 대한 관리 ID에 데이터베이스(SSISDB)를 만들 수 있는 기능이 부여됩니다.

6.  If your SSISDB was created using SQL authentication and you want to switch to use Azure AD authentication for your Azure-SSIS IR to access it, right-click on **SSISDB** database and select **New query**.

7.  In the query window, enter the following T-SQL command, and select **Execute** on the toolbar.

    ```sql
    CREATE USER [{your ADF name}] FOR LOGIN [{your ADF name}] WITH DEFAULT_SCHEMA = dbo
    ALTER ROLE db_owner ADD MEMBER [{your ADF name}]
    ```

    명령이 성공적으로 완료되면 ADF에 대한 관리 ID에 SSISDB에 액세스할 있는 기능이 부여됩니다.

## <a name="provision-azure-ssis-ir-in-azure-portaladf-app"></a>Azure Portal/ADF 앱에서 Azure-SSIS IR 프로비전

Azure Portal/ADF 앱에서 Azure-SSIS IR을 프로비전하는 경우 **SQL 설정** 페이지에서 **ADF에 대한 관리 ID와 함께 AAD 인증 사용** 옵션을 선택합니다. 다음 스크린샷은 SSISDB를 호스트하는 Azure SQL Database 서버가 포함된 IR에 대한 설정을 표시합니다. SSISDB를 호스트하는 Managed Instance를 사용한 IR의 경우 다른 설정은 동일하나, **카탈로그 데이터베이스 서비스 계층** 및 **Azure 서비스 액세스 허용** 설정은 적용할 수 없습니다.

Azure-SSIS IR을 만드는 방법에 대한 자세한 정보는 [Azure Data Factory에서 Azure-SSIS 통합 런타임 만들기](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime)를 참조하세요.

![Azure-SSIS 통합 런타임에 대한 설정](media/enable-aad-authentication-azure-ssis-ir/enable-aad-authentication.png)

## <a name="provision-azure-ssis-ir-with-powershell"></a>PowerShell을 사용하여 Azure-SSIS IR 프로비전

PowerShell을 사용하여 Azure-SSIS IR을 프로비전하려면 다음 작업을 수행합니다.

1.  [Azure PowerShell](https://github.com/Azure/azure-powershell/releases/tag/v5.5.0-March2018)  모듈을 설치합니다.

2.  스크립트에서 `CatalogAdminCredential` 매개 변수를 설정하지 마세요. 다음은 그 예입니다.

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                               -DataFactoryName $DataFactoryName `
                                               -Name $AzureSSISName `
                                               -Description $AzureSSISDescription `
                                               -Type Managed `
                                               -Location $AzureSSISLocation `
                                               -NodeSize $AzureSSISNodeSize `
                                               -NodeCount $AzureSSISNodeNumber `
                                               -Edition $AzureSSISEdition `
                                               -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode `
                                               -CatalogServerEndpoint $SSISDBServerEndpoint `
                                               -CatalogPricingTier $SSISDBPricingTier

    Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                                 -DataFactoryName $DataFactoryName `
                                                 -Name $AzureSSISName
    ```

## <a name="run-ssis-packages-with-managed-identity-authentication"></a>Run SSIS Packages with Managed Identity Authentication

When you run SSIS packages on Azure-SSIS IR, you can use managed identity authentication to connect to various Azure resources. Currently we have already supported managed identity authentication in the following connection managers.

- [OLE DB Connection Manager](https://docs.microsoft.com/sql/integration-services/connection-manager/ole-db-connection-manager#managed-identities-for-azure-resources-authentication)

- [ADO.NET Connection Manager](https://docs.microsoft.com/sql/integration-services/connection-manager/ado-net-connection-manager#managed-identities-for-azure-resources-authentication)

- [Azure Storage Connection Manager](https://docs.microsoft.com/sql/integration-services/connection-manager/azure-storage-connection-manager#managed-identities-for-azure-resources-authentication)
