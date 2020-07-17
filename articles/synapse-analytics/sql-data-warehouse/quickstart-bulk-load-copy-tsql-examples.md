---
title: COPY 문을 사용한 인증 메커니즘
description: 데이터 대량 로드를 위한 인증 메커니즘 개요를 표시합니다.
services: synapse-analytics
author: kevinvngo
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql-dw
ms.date: 05/06/2020
ms.author: kevin
ms.reviewer: jrasnick
ms.openlocfilehash: f5f6c6970ad8bb697ceb118b6725b37e93ca80b5
ms.sourcegitcommit: 6fd28c1e5cf6872fb28691c7dd307a5e4bc71228
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/23/2020
ms.locfileid: "85213060"
---
# <a name="securely-load-data-using-synapse-sql"></a>Synapse SQL을 사용하여 안전하게 데이터 로드

이 문서에서는 [COPY 문](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest)의 보안 인증 메커니즘을 설명하고 예시를 보여줍니다. COPY 문은 Synapse SQL에서 데이터를 대량 로드하기 위한 가장 유연하고 안전한 방법입니다.
## <a name="supported-authentication-mechanisms"></a>지원되는 인증 메커니즘

다음 표에서는 각 파일 유형 및 스토리지 계정에 대해 지원되는 인증 방법을 설명합니다. 원본 스토리지 위치 및 오류 파일 위치에 적용됩니다.

|                      |                CSV                |              Parquet              |                ORC                |
| :------------------: | :-------------------------------: | :-------------------------------: | :-------------------------------: |
|  Azure BLOB 스토리지  | SAS/MSI/SERVICE PRINCIPAL/KEY/AAD |              SAS/KEY              |              SAS/KEY              |
| Azure Data Lake Gen2 | SAS/MSI/SERVICE PRINCIPAL/KEY/AAD | SAS/MSI/SERVICE PRINCIPAL/KEY/AAD | SAS/MSI/SERVICE PRINCIPAL/KEY/AAD |

## <a name="a-storage-account-key-with-lf-as-the-row-terminator-unix-style-new-line"></a>A. 행 종결자로 LF가 사용되는 스토리지 계정 키(Unix 스타일의 줄 바꿈)


```sql
--Note when specifying the column list, input field numbers start from 1
COPY INTO target_table (Col_one default 'myStringDefault' 1, Col_two default 1 3)
FROM 'https://adlsgen2account.dfs.core.windows.net/myblobcontainer/folder1/'
WITH (
    FILE_TYPE = 'CSV'
    ,CREDENTIAL=(IDENTITY= 'Storage Account Key', SECRET='<Your_Account_Key>')
    --CREDENTIAL should look something like this:
    --CREDENTIAL=(IDENTITY= 'Storage Account Key', SECRET='x6RWv4It5F2msnjelv3H4DA80n0QW0daPdw43jM0nyetx4c6CpDkdj3986DX5AHFMIf/YN4y6kkCnU8lb+Wx0Pj+6MDw=='),
    ,ROWTERMINATOR='0x0A' --0x0A specifies to use the Line Feed character (Unix based systems)
)
```
> [!IMPORTANT]
>
> - 16진수 값(0x0A)을 사용하여 줄 바꿈 문자를 지정합니다. COPY 문은 '\n' 문자열을 '\r\n'(캐리지 리턴 줄 바꿈)으로 해석합니다.

## <a name="b-shared-access-signatures-sas-with-crlf-as-the-row-terminator-windows-style-new-line"></a>B. 행 종결자로 CRLF를 사용하는 SAS(공유 액세스 서명)(Windows 스타일 줄 바꿈)
```sql
COPY INTO target_table
FROM 'https://adlsgen2account.dfs.core.windows.net/myblobcontainer/folder1/'
WITH (
    FILE_TYPE = 'CSV'
    ,CREDENTIAL=(IDENTITY= 'Shared Access Signature', SECRET='<Your_SAS_Token>')
    --CREDENTIAL should look something like this:
    --CREDENTIAL=(IDENTITY= 'Shared Access Signature', SECRET='?sv=2018-03-28&ss=bfqt&srt=sco&sp=rl&st=2016-10-17T20%3A14%3A55Z&se=2021-10-18T20%3A19%3A00Z&sig=IEoOdmeYnE9%2FKiJDSFSYsz4AkNa%2F%2BTx61FuQ%2FfKHefqoBE%3D'),
    ,ROWTERMINATOR='\n'-- COPY command automatically prefixes the \r character when \n (newline) is specified. This results in carriage return newline (\r\n) for Windows based systems.
)
```

> [!IMPORTANT]
>
> - ROWTERMINATOR를 '\r\r\n'으로 해석되고 구문 분석 문제를 일으킬 수 있는 '\r\n'으로 지정하지 마십시오.

## <a name="c-managed-identity"></a>C. 관리 ID

스토리지 계정이 VNet에 연결되었을 때는 관리 ID 인증이 필요합니다. 

### <a name="prerequisites"></a>사전 요구 사항

1. [이 가이드](/powershell/azure/install-az-ps?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)를 사용하여 Azure PowerShell을 설치합니다.
2. 범용 v1 또는 Blob Storage 계정이 있는 경우 먼저 이 [가이드](../../storage/common/storage-account-upgrade.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)를 사용하여 범용 v2로 업그레이드해야 합니다.
3. Azure Storage 계정 **방화벽 및 가상 네트워크** 설정 메뉴에서 **신뢰할 수 있는 Microsoft 서비스가 이 스토리지 계정에 액세스하도록 허용합니다.** 를 설정해야 합니다. 자세한 내용은 이 [가이드](../../storage/common/storage-network-security.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#exceptions)를 참조하세요.
#### <a name="steps"></a>단계

1. PowerShell에서 AAD(Azure Active Directory)에 **SQL Server를 등록**합니다.

   ```powershell
   Connect-AzAccount
   Select-AzSubscription -SubscriptionId your-subscriptionId
   Set-AzSqlServer -ResourceGroupName your-database-server-resourceGroup -ServerName your-database-servername -AssignIdentity
   ```

2. 이 [가이드](../../storage/common/storage-account-create.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)를 사용하여 **범용 v2 스토리지 계정**을 만듭니다.

   > [!NOTE]
   > 범용 v1 또는 Blob Storage 계정이 있는 경우 먼저 이 [가이드](../../storage/common/storage-account-upgrade.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)를 사용하여 **v2로 업그레이드**해야 합니다.

3. 스토리지 계정 아래의 **액세스 제어(IAM)** 로 이동하고 **역할 할당 추가**를 선택합니다. **Storage Blob 데이터 소유자, 기여자 또는 읽기 권한자** RBAC 역할을 SQL Server에 할당합니다.

   > [!NOTE]
   > 소유자 권한이 있는 멤버만 이 단계를 수행할 수 있습니다. Azure 리소스에 대한 다양한 기본 제공 역할을 보려면 이 [가이드](../../role-based-access-control/built-in-roles.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)를 참조하세요.
   
4. 이제 "관리 ID"를 사용하여 COPY 문을 실행할 수 있습니다.

    ```sql
    COPY INTO dbo.target_table
    FROM 'https://myaccount.blob.core.windows.net/myblobcontainer/folder1/*.txt'
    WITH (
        FILE_TYPE = 'CSV',
        CREDENTIAL = (IDENTITY = 'Managed Identity'),
    )
    ```

> [!IMPORTANT]
>
> - **Storage** **Blob 데이터** 소유자, 기여자 또는 읽기 권한자 RBAC 역할을 지정합니다. 이러한 역할은 소유자, 기여자 및 읽기 권한자로 구성되는 Azure 기본 제공 역할과 다릅니다. 

## <a name="d-azure-active-directory-authentication-aad"></a>D. AAD(Azure Active Directory 인증)
#### <a name="steps"></a>단계

1. 스토리지 계정 아래의 **액세스 제어(IAM)** 로 이동하고 **역할 할당 추가**를 선택합니다. **Storage Blob 데이터 소유자, 기여자 또는 읽기 권한자** RBAC 역할을 AAD 사용자에게 할당합니다. 

2. 다음 [문서](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure?tabs=azure-powershell#create-an-azure-ad-administrator-for-azure-sql-server)에 따라 Azure AD 인증을 구성합니다. 

3. Active Directory를 사용하여 SQL 풀에 연결합니다. 이제 자격 증명을 지정하지 않고 COPY 문을 실행할 수 있습니다.

    ```sql
    COPY INTO dbo.target_table
    FROM 'https://myaccount.blob.core.windows.net/myblobcontainer/folder1/*.txt'
    WITH (
        FILE_TYPE = 'CSV'
    )
    ```

> [!IMPORTANT]
>
> - **Storage** **Blob 데이터** 소유자, 기여자 또는 읽기 권한자 RBAC 역할을 지정합니다. 이러한 역할은 소유자, 기여자 및 읽기 권한자로 구성되는 Azure 기본 제공 역할과 다릅니다. 

## <a name="e-service-principal-authentication"></a>E. 서비스 주체 인증
#### <a name="steps"></a>단계

1. [AAD(Azure Active Directory) 애플리케이션 만들기](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#create-an-azure-active-directory-application)
2. [애플리케이션 ID 가져오기](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in)
3. [인증 키 가져오기](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#create-a-new-application-secret)
4. [V1 OAuth 2.0 토큰 엔드포인트 가져오기](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-service-to-service-authenticate-using-active-directory?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#step-4-get-the-oauth-20-token-endpoint-only-for-java-based-applications)
5. 스토리지 계정에서 [AAD 애플리케이션에 읽기, 쓰기 및 실행 권한 할당](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-service-to-service-authenticate-using-active-directory?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#step-3-assign-the-azure-ad-application-to-the-azure-data-lake-storage-gen1-account-file-or-folder)
6. 이제 COPY 문을 실행할 수 있습니다.

    ```sql
    COPY INTO dbo.target_table
    FROM 'https://myaccount.blob.core.windows.net/myblobcontainer/folder0/*.txt'
    WITH (
        FILE_TYPE = 'CSV'
        ,CREDENTIAL=(IDENTITY= '<application_ID>@<OAuth_2.0_Token_EndPoint>' , SECRET= '<authentication_key>')
        --CREDENTIAL should look something like this:
        --,CREDENTIAL=(IDENTITY= '92761aac-12a9-4ec3-89b8-7149aef4c35b@https://login.microsoftonline.com/72f714bf-86f1-41af-91ab-2d7cd011db47/oauth2/token', SECRET='juXi12sZ6gse]woKQNgqwSywYv]7A.M')
    )
    ```

> [!IMPORTANT]
>
> - OAuth 2.0 토큰 엔드포인트의 **V1** 버전 사용

## <a name="next-steps"></a>다음 단계

- 자세한 구문은 [COPY 문 문서](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest#syntax)를 확인하세요.
- 모범 사례를 로드하려면 [데이터 로드 개요](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/design-elt-data-loading#what-is-elt) 문서를 확인하세요.
