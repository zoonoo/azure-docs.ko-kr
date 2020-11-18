---
title: Azure Data Lake Storage Gen2에 대 한 Acl을 재귀적으로 설정 Microsoft Docs
description: 부모 디렉터리의 기존 자식 항목에 대 한 액세스 제어 목록을 재귀적으로 추가, 업데이트 및 제거할 수 있습니다.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: how-to
ms.date: 11/03/2020
ms.author: normesta
ms.reviewer: prishet
ms.custom: devx-track-csharp
ms.openlocfilehash: 2ab554f45de30bb676d2933a4a1268b6831ae4f5
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2020
ms.locfileid: "94659923"
---
# <a name="set-access-control-lists-acls-recursively-for-azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2에 대 한 Acl (액세스 제어 목록)을 재귀적으로 설정

부모 디렉터리 아래에 생성 된 새 자식 항목에는 ACL 상속이 이미 사용할 수 있습니다. 이제 각 자식 항목에 대해 개별적으로 변경할 필요 없이 부모 디렉터리의 기존 자식 항목에 대해 Acl을 재귀적으로 추가, 업데이트 및 제거할 수 있습니다.

[라이브러리](#libraries)  |  [샘플](#code-samples)  |  모범 [사례](#best-practice-guidelines)

## <a name="prerequisites"></a>사전 요구 사항

- Azure 구독 [Azure 평가판](https://azure.microsoft.com/pricing/free-trial/)을 참조하세요.

- HNS(계층 구조 네임스페이스)를 사용하도록 설정된 스토리지 계정입니다. 만들려면 [다음 지침](create-data-lake-storage-account.md)을 수행합니다.

- 재귀 ACL 프로세스를 실행할 수 있는 올바른 권한입니다. 올바른 사용 권한에는 다음 중 하나가 포함 됩니다. 

  - 대상 컨테이너, 부모 리소스 그룹 또는 구독의 범위에서 [저장소 Blob 데이터 소유자](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) 역할이 할당 된 프로 비전 된 AD (Azure Active Directory) [보안 주체](https://docs.microsoft.com/azure/role-based-access-control/overview#security-principal) 입니다.   

  - 재귀 ACL 프로세스를 적용 하려는 대상 컨테이너 또는 디렉터리를 소유 하는 사용자입니다. 여기에는 대상 컨테이너 또는 디렉터리의 모든 자식 항목이 포함 됩니다. 

- 디렉터리 및 파일에 Acl을 적용 하는 방법을 이해 합니다. [Azure Data Lake Storage Gen2의 액세스 제어를](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)참조 하세요. 

PowerShell, .NET SDK 및 Python SDK에 대 한 설치 지침을 보려면이 문서의 **프로젝트 설정** 섹션을 참조 하세요.

## <a name="set-up-your-project"></a>프로젝트 설정

필요한 라이브러리를 설치 합니다.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. 다음 명령을 사용 하 여 설치한 PowerShell 버전이 이상 인지 확인 `5.1` 합니다.    

   ```powershell
   echo $PSVersionTable.PSVersion.ToString() 
   ```
    
   PowerShell 버전을 업그레이드 하려면 [기존 Windows Powershell 업그레이드](https://docs.microsoft.com/powershell/scripting/install/installing-windows-powershell) 를 참조 하세요.
    
2. 설치 **Az. Storage** module.

   ```powershell
   Install-Module Az.Storage -Repository PSGallery -Force  
   ```

   PowerShell 모듈을 설치 하는 방법에 대 한 자세한 내용은 [Azure PowerShell 모듈 설치](https://docs.microsoft.com/powershell/azure/install-az-ps) 를 참조 하세요.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview)을 열거나 Azure CLI를 로컬로 [설치](https://docs.microsoft.com/cli/azure/install-azure-cli)한 경우 Windows PowerShell과 같은 명령 콘솔 애플리케이션을 엽니다.

2. 다음 명령을 사용하여 설치된 Azure CLI 버전이 `2.14.0` 이상인지 확인합니다.

   ```azurecli
    az --version
   ```
   Azure CLI 버전이 `2.14.0`보다 낮은 경우 이후 버전을 설치합니다. [Azure CLI 설치](https://docs.microsoft.com/cli/azure/install-azure-cli)를 참조하세요.

### <a name="net"></a>[.NET](#tab/dotnet)

1. 명령 창을 엽니다 (예: Windows PowerShell).

2. 프로젝트 디렉터리에서 명령을 사용 하 여 DataLake 미리 보기 패키지를 설치 합니다. `dotnet add package`

   ```console
   dotnet add package Azure.Storage.Files.DataLake -v 12.5.0-preview.1 -s https://pkgs.dev.azure.com/azure-sdk/public/_packaging/azure-sdk-for-net/nuget/v3/index.json
   ```

3. 이러한 using 문을 코드 파일의 맨 위에 추가 합니다.

   ```csharp
   using Azure;
   using Azure.Core;
   using Azure.Storage;
   using Azure.Storage.Files.DataLake;
   using Azure.Storage.Files.DataLake.Models;
   using System.Collections.Generic;
   using System.Threading.Tasks;
    ```

### <a name="java"></a>[Java](#tab/java)

시작 하려면 [이 페이지](https://search.maven.org/artifact/com.azure/azure-storage-file-datalake) 를 열고 최신 버전의 Java 라이브러리를 찾습니다. 그런 다음 텍스트 편집기에서 *pom.xml* 파일을 엽니다. 해당 버전을 참조 하는 종속성 요소를 추가 합니다.

AD (Azure Active Directory)를 사용 하 여 클라이언트 응용 프로그램을 인증 하려는 경우 Azure Secret Client 라이브러리에 종속성을 추가 합니다. [프로젝트에 비밀 클라이언트 라이브러리 패키지 추가를](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/identity/azure-identity#adding-the-package-to-your-project)참조 하세요.

그런 다음 이러한 imports 문을 코드 파일에 추가 합니다.

```java
import com.azure.identity.ClientSecretCredential;
import com.azure.identity.ClientSecretCredentialBuilder;
import com.azure.core.http.rest.PagedIterable;
import com.azure.core.http.rest.Response;
import com.azure.storage.common.StorageSharedKeyCredential;
import com.azure.storage.file.datalake.DataLakeDirectoryClient;
import com.azure.storage.file.datalake.DataLakeFileClient;
import com.azure.storage.file.datalake.DataLakeFileSystemClient;
import com.azure.storage.file.datalake.DataLakeServiceClient;
import com.azure.storage.file.datalake.DataLakeServiceClientBuilder;
import com.azure.storage.file.datalake.models.AccessControlChangeResult;
import com.azure.storage.file.datalake.models.AccessControlType;
import com.azure.storage.file.datalake.models.ListPathsOptions;
import com.azure.storage.file.datalake.models.PathAccessControl;
import com.azure.storage.file.datalake.models.PathAccessControlEntry;
import com.azure.storage.file.datalake.models.PathItem;
import com.azure.storage.file.datalake.models.PathPermissions;
import com.azure.storage.file.datalake.models.PathRemoveAccessControlEntry;
import com.azure.storage.file.datalake.models.RolePermissions;
import com.azure.storage.file.datalake.options.PathSetAccessControlRecursiveOptions;
```

### <a name="python"></a>[Python](#tab/python)

1. [Python 용 Azure Data Lake Storage 클라이언트 라이브러리](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2Fazure_storage_file_datalake-12.1.0b99-py2.py3-none-any.whl%3Fsv%3D2019-02-02%26st%3D2020-08-24T07%253A47%253A01Z%26se%3D2021-08-25T07%253A47%253A00Z%26sr%3Db%26sp%3Dr%26sig%3DH1XYw4FTLJse%252BYQ%252BfamVL21UPVIKRnnh2mfudA%252BfI0I%253D&data=02%7C01%7Cnormesta%40microsoft.com%7C95a5966d938a4902560e08d84912fe32%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637339693209725909&sdata=acv4KWZdzkITw1lP0%2FiA3lZuW7NF5JObjY26IXttfGI%3D&reserved=0)를 다운로드 합니다.

2. [Pip](https://pypi.org/project/pip/)를 사용 하 여 다운로드 한 라이브러리를 설치 합니다.

   ```
   pip install azure_storage_file_datalake-12.1.0b99-py2.py3-none-any.whl
   ```

3. 이러한 import 문을 코드 파일의 맨 위에 추가 합니다.

   ```python
   import os, uuid, sys
   from azure.storage.filedatalake import DataLakeServiceClient
   from azure.core._match_conditions import MatchConditions
   from azure.storage.filedatalake._models import ContentSettings
   ```

---

## <a name="connect-to-your-account"></a>계정에 연결

AD (Azure Active Directory)를 사용 하거나 계정 키를 사용 하 여 연결할 수 있습니다. 

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Windows PowerShell 명령 창을 열고 명령을 사용 하 여 Azure 구독에 로그인 하 `Connect-AzAccount` 고 화면의 지시를 따릅니다.

```powershell
Connect-AzAccount
```

Id가 둘 이상의 구독과 연결 된 경우 활성 구독을 디렉터리를 만들고 관리 하려는 저장소 계정의 구독으로 설정 합니다. 이 예에서는 `<subscription-id>` 자리 표시자 값을 구독의 ID로 바꿉니다.

```powershell
Select-AzSubscription -SubscriptionId <subscription-id>
```

그런 다음 명령을 통해 저장소 계정에 대 한 권한 부여를 가져오는 방법을 선택 합니다. 

### <a name="option-1-obtain-authorization-by-using-azure-active-directory-ad"></a>옵션 1: Azure Active Directory (AD)를 사용 하 여 권한 부여 가져오기

이 접근 방식을 사용 하는 경우 시스템은 사용자 계정에 적절 한 Azure RBAC (역할 기반 액세스 제어) 할당 및 ACL 권한이 있는지 확인 합니다. 

```powershell
$ctx = New-AzStorageContext -StorageAccountName '<storage-account-name>' -UseConnectedAccount
```

다음 표에서는 지원 되는 각 역할과 해당 ACL 설정 기능을 보여 줍니다.

|역할|ACL 설정 기능|
|--|--|
|[Storage Blob 데이터 소유자](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)|계정에 있는 모든 디렉터리 및 파일입니다.|
|[Storage Blob 데이터 기여자](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)|보안 주체가 소유 하는 디렉터리와 파일만|

### <a name="option-2-obtain-authorization-by-using-the-storage-account-key"></a>옵션 2: 저장소 계정 키를 사용 하 여 권한 부여 가져오기

이 방법을 사용 하면 시스템에서 Azure RBAC 또는 ACL 사용 권한을 확인 하지 않습니다.

```powershell
$storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -AccountName "<storage-account-name>"
$ctx = $storageAccount.Context
```

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. Azure CLI를 로컬로 사용하는 경우 로그인 명령을 실행합니다.

   ```azurecli
   az login
   ```

   CLI는 기본 브라우저를 열 수 있으면 기본 브라우저를 열고 Azure 로그인 페이지를 로드합니다.

   그렇지 않으면 [https://aka.ms/devicelogin](https://aka.ms/devicelogin)에서 브라우저 페이지를 열고 터미널에 표시된 권한 부여 코드를 입력합니다. 그런 다음 브라우저에서 계정 자격 증명으로 로그인합니다.

   다른 인증 방법에 대한 자세한 내용은 [Azure CLI를 사용하여 Blob 또는 큐 데이터에 대한 액세스 권한 부여](../common/authorize-data-operations-cli.md)를 참조하세요.

2. ID가 둘 이상의 구독과 연결된 경우 정적 웹 사이트를 호스트하는 스토리지 계정의 구독으로 활성 구독을 설정합니다.

   ```azurecli
   az account set --subscription <subscription-id>
   ```

   `<subscription-id>` 자리 표시자 값을 구독의 ID로 바꿉니다.

> [!NOTE]
> 이 문서에 제공된 예제는 Azure AD(Active Directory) 인증을 보여 줍니다. 권한 부여 방법에 대한 자세한 내용은 [Azure CLI를 사용하여 Blob 또는 큐 데이터에 대한 액세스 권한 부여I](../common/authorize-data-operations-cli.md)를 참조하세요.

### <a name="net"></a>[.NET](#tab/dotnet)

이 문서의 코드 조각을 사용 하려면 저장소 계정을 나타내는 [DataLakeServiceClient](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakeserviceclient) 인스턴스를 만들어야 합니다.

#### <a name="connect-by-using-azure-active-directory-ad"></a>Azure Active Directory (AD)를 사용 하 여 연결

[.Net 용 azure id 클라이언트 라이브러리](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/identity/Azure.Identity) 를 사용 하 여 azure AD로 응용 프로그램을 인증할 수 있습니다.

패키지를 설치한 후에는이 using 문을 코드 파일의 맨 위에 추가 합니다.

```csharp
using Azure.Identity;
```

클라이언트 ID, 클라이언트 암호 및 테 넌 트 ID를 가져옵니다. 이렇게 하려면 [클라이언트 응용 프로그램의 요청에 대 한 권한 부여를 위해 AZURE AD에서 토큰 가져오기](../common/storage-auth-aad-app.md)를 참조 하세요. 해당 프로세스의 일부로 다음 [azure 역할 기반 액세스 제어 (AZURE RBAC)](../../role-based-access-control/overview.md) 역할 중 하나를 보안 주체에 할당 해야 합니다. 

|역할|ACL 설정 기능|
|--|--|
|[Storage Blob 데이터 소유자](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)|계정에 있는 모든 디렉터리 및 파일입니다.|
|[Storage Blob 데이터 기여자](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)|보안 주체가 소유 하는 디렉터리와 파일만|

이 예에서는 클라이언트 ID, 클라이언트 암호 및 테 넌 트 ID를 사용 하 여 [DataLakeServiceClient](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakeserviceclient?) 인스턴스를 만듭니다.  

```cs
public void GetDataLakeServiceClient(ref DataLakeServiceClient dataLakeServiceClient, 
    String accountName, String clientID, string clientSecret, string tenantID)
{

    TokenCredential credential = new ClientSecretCredential(
        tenantID, clientID, clientSecret, new TokenCredentialOptions());

    string dfsUri = "https://" + accountName + ".dfs.core.windows.net";

    dataLakeServiceClient = new DataLakeServiceClient(new Uri(dfsUri), credential);
}

``` 

#### <a name="connect-by-using-an-account-key"></a>계정 키를 사용 하 여 연결

이 방법은 계정에 연결 하는 가장 쉬운 방법입니다. 

이 예에서는 계정 키를 사용 하 여 [DataLakeServiceClient](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakeserviceclient?) 인스턴스를 만듭니다.

```cs
public void GetDataLakeServiceClient(ref DataLakeServiceClient dataLakeServiceClient,
    string accountName, string accountKey)
{
    StorageSharedKeyCredential sharedKeyCredential =
        new StorageSharedKeyCredential(accountName, accountKey);

    string dfsUri = "https://" + accountName + ".dfs.core.windows.net";

    dataLakeServiceClient = new DataLakeServiceClient
        (new Uri(dfsUri), sharedKeyCredential);
}
```

> [!NOTE]
> 더 많은 예제는 [.net 용 Azure id 클라이언트 라이브러리](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/identity/Azure.Identity) 설명서를 참조 하세요.

### <a name="java"></a>[Java](#tab/java)

이 문서의 코드 조각을 사용 하려면 저장소 계정을 나타내는 **DataLakeServiceClient** 인스턴스를 만들어야 합니다. 

#### <a name="connect-by-using-an-account-key"></a>계정 키를 사용 하 여 연결

계정에 연결 하는 가장 쉬운 방법입니다. 

이 예에서는 계정 키를 사용 하 여 **DataLakeServiceClient** 인스턴스를 만듭니다.

```java

static public DataLakeServiceClient GetDataLakeServiceClient
(String accountName, String accountKey){

    StorageSharedKeyCredential sharedKeyCredential =
        new StorageSharedKeyCredential(accountName, accountKey);

    DataLakeServiceClientBuilder builder = new DataLakeServiceClientBuilder();

    builder.credential(sharedKeyCredential);
    builder.endpoint("https://" + accountName + ".dfs.core.windows.net");

    return builder.buildClient();
}      
```

#### <a name="connect-by-using-azure-active-directory-azure-ad"></a>Azure Active Directory를 사용 하 여 연결 (Azure AD)

[Java 용 azure id 클라이언트 라이브러리](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/identity/azure-identity) 를 사용 하 여 azure AD로 응용 프로그램을 인증할 수 있습니다.

이 예에서는 클라이언트 ID, 클라이언트 암호 및 테 넌 트 ID를 사용 하 여 **DataLakeServiceClient** 인스턴스를 만듭니다.  이러한 값을 얻으려면 [클라이언트 응용 프로그램의 요청에 대 한 권한 부여를 위해 AZURE AD에서 토큰 획득](../common/storage-auth-aad-app.md)을 참조 하세요.

```java
static public DataLakeServiceClient GetDataLakeServiceClient
    (String accountName, String clientId, String ClientSecret, String tenantID){

    String endpoint = "https://" + accountName + ".dfs.core.windows.net";
        
    ClientSecretCredential clientSecretCredential = new ClientSecretCredentialBuilder()
    .clientId(clientId)
    .clientSecret(ClientSecret)
    .tenantId(tenantID)
    .build();
           
    DataLakeServiceClientBuilder builder = new DataLakeServiceClientBuilder();
    return builder.credential(clientSecretCredential).endpoint(endpoint).buildClient();
 } 
```

> [!NOTE]
> 더 많은 예제는 [Java 용 Azure id 클라이언트 라이브러리](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/identity/azure-identity) 설명서를 참조 하세요.

### <a name="python"></a>[Python](#tab/python)

이 문서의 코드 조각을 사용 하려면 저장소 계정을 나타내는 **DataLakeServiceClient** 인스턴스를 만들어야 합니다. 

### <a name="connect-by-using-azure-active-directory-ad"></a>Azure Active Directory (AD)를 사용 하 여 연결

[Python 용 azure identity client 라이브러리](https://pypi.org/project/azure-identity/) 를 사용 하 여 azure AD로 응용 프로그램을 인증할 수 있습니다.

이 예에서는 클라이언트 ID, 클라이언트 암호 및 테 넌 트 ID를 사용 하 여 **DataLakeServiceClient** 인스턴스를 만듭니다.  이러한 값을 얻으려면 [클라이언트 응용 프로그램의 요청에 대 한 권한 부여를 위해 AZURE AD에서 토큰 획득](../common/storage-auth-aad-app.md)을 참조 하세요. 해당 프로세스의 일부로 다음 [azure 역할 기반 액세스 제어 (AZURE RBAC)](../../role-based-access-control/overview.md) 역할 중 하나를 보안 주체에 할당 해야 합니다. 

|역할|ACL 설정 기능|
|--|--|
|[Storage Blob 데이터 소유자](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)|계정에 있는 모든 디렉터리 및 파일입니다.|
|[Storage Blob 데이터 기여자](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)|보안 주체가 소유 하는 디렉터리와 파일만|

```python
def initialize_storage_account_ad(storage_account_name, client_id, client_secret, tenant_id):
    
    try:  
        global service_client

        credential = ClientSecretCredential(tenant_id, client_id, client_secret)

        service_client = DataLakeServiceClient(account_url="{}://{}.dfs.core.windows.net".format(
            "https", storage_account_name), credential=credential)
    
    except Exception as e:
        print(e)
```

> [!NOTE]
> 더 많은 예제는 [Python 용 Azure id 클라이언트 라이브러리](https://pypi.org/project/azure-identity/) 설명서를 참조 하세요.

### <a name="connect-by-using-an-account-key"></a>계정 키를 사용 하 여 연결

계정에 연결 하는 가장 쉬운 방법입니다. 

이 예에서는 계정 키를 사용 하 여 **DataLakeServiceClient** 인스턴스를 만듭니다.

```python
try:  
    global service_client
        
    service_client = DataLakeServiceClient(account_url="{}://{}.dfs.core.windows.net".format(
        "https", storage_account_name), credential=storage_account_key)
    
except Exception as e:
    print(e)
```
 
- `storage_account_name` 자리 표시자 값을 스토리지 계정 이름으로 바꿉니다.

- `storage_account_key`자리 표시자 값을 저장소 계정 액세스 키로 바꿉니다.

---

## <a name="set-an-acl-recursively"></a>재귀적으로 ACL 설정

ACL을 *설정* 하는 경우 모든 항목을 포함 하 여 전체 acl을 **바꿉니다** . 보안 주체의 권한 수준을 변경 하거나 다른 기존 항목에 영향을 주지 않고 ACL에 새 보안 주체를 추가 하려면 대신 ACL을 *업데이트* 해야 합니다. ACL을 대체 하는 대신 업데이트 하려면이 문서의 [acl을 재귀적으로 업데이트](#update-an-acl-recursively) 섹션을 참조 하세요.   

이 섹션에는 ACL을 설정 하는 방법에 대 한 예제가 포함 되어 있습니다. 

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

**AzDataLakeGen2AclRecursive** cmdlet을 사용 하 여 재귀적으로 ACL을 설정 합니다.

이 예에서는 라는 디렉터리의 ACL을 설정 합니다 `my-parent-directory` . 이러한 항목은 소유 하는 사용자에 게 읽기, 쓰기 및 실행 권한을 부여 하 고, 소유 그룹만 읽기 및 실행 권한을 부여 하 고, 다른 모든 사용자에 게 액세스 권한을 부여 하지는 않습니다. 이 예제의 마지막 ACL 항목은 개체 ID "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" 읽기 및 실행 권한이 있는 특정 사용자를 제공 합니다.

```powershell
$filesystemName = "my-container"
$dirname = "my-parent-directory/"
$userID = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx";

$acl = Set-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rwx 
$acl = Set-AzDataLakeGen2ItemAclObject -AccessControlType group -Permission r-x -InputObject $acl 
$acl = Set-AzDataLakeGen2ItemAclObject -AccessControlType other -Permission "---" -InputObject $acl
$acl = Set-AzDataLakeGen2ItemAclObject -AccessControlType user -EntityId $userID -Permission r-x -InputObject $acl 

Set-AzDataLakeGen2AclRecursive -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl

```

> [!NOTE]
> **기본** ACL 항목을 설정 하려면 **AzDataLakeGen2ItemAclObject** 명령을 실행할 때 **-defaultscope** 매개 변수를 사용 합니다. 예: `$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rwx -DefaultScope`.

일괄 처리 크기를 지정 하 여 일괄 처리에서 Acl을 재귀적으로 설정 하는 예제를 보려면 [AzDataLakeGen2AclRecursive](https://docs.microsoft.com/powershell/module/az.storage/set-azdatalakegen2aclrecursive) 참조 문서를 참조 하세요.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[Az storage fs access set-recursive](https://docs.microsoft.com/cli/azure/storage/fs/access#az_storage_fs_access_set_recursive) 명령을 사용 하 여 재귀적으로 ACL을 설정 합니다.

이 예에서는 라는 디렉터리의 ACL을 설정 합니다 `my-parent-directory` . 이러한 항목은 소유 하는 사용자에 게 읽기, 쓰기 및 실행 권한을 부여 하 고, 소유 그룹만 읽기 및 실행 권한을 부여 하 고, 다른 모든 사용자에 게 액세스 권한을 부여 하지는 않습니다. 이 예제의 마지막 ACL 항목은 개체 ID "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" 읽기 및 실행 권한이 있는 특정 사용자를 제공 합니다.

```azurecli
az storage fs access set-recursive --acl "user::rwx,group::r-x,other::---,user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx:r-x" -p my-parent-directory/ -f my-container --account-name mystorageaccount --auth-mode login
```

> [!NOTE]
> **기본** ACL 항목을 설정 하려면 `default:` 각 항목에 접두사를 추가 합니다. 예를 들어 `default:user::rwx` 또는 `default:user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx:r-x`입니다. 

### <a name="net"></a>[.NET](#tab/dotnet)

**DataLakeDirectoryClient SetAccessControlRecursiveAsync** 메서드를 호출 하 여 ACL을 재귀적으로 설정 합니다. 이 메서드를 [Pathaccesscontrolitem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem) [목록](/dotnet/api/system.collections.generic.list-1) 으로 전달 합니다. 각 [Pathaccesscontrolitem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem) 은 ACL 항목을 정의 합니다. 

**기본** ACL 항목을 설정 하려는 경우 [Pathaccesscontrolitem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem) 의 [defaultscope](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem.defaultscope#Azure_Storage_Files_DataLake_Models_PathAccessControlItem_DefaultScope) 속성을 **true** 로 설정할 수 있습니다. 

이 예에서는 라는 디렉터리의 ACL을 설정 합니다 `my-parent-directory` . 이 메서드는 `isDefaultScope` 기본 ACL을 설정할지 여부를 지정 하는 라는 부울 매개 변수를 허용 합니다. 이 매개 변수는 [Pathaccesscontrolitem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem)의 생성자에서 사용 됩니다. ACL의 항목은 소유 사용자에 대 한 읽기, 쓰기 및 실행 권한을 부여 하 고, 소유 그룹에 읽기 및 실행 권한만 제공 하 고, 다른 모든 사용자에 게 액세스 권한을 부여 하지는 않습니다. 이 예제의 마지막 ACL 항목은 개체 ID "" xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx "읽기 및 실행 권한이 있는 특정 사용자를 제공 합니다.

```cs
public async void SetACLRecursively(DataLakeServiceClient serviceClient, bool isDefaultScope)
{
    DataLakeDirectoryClient directoryClient =
        serviceClient.GetFileSystemClient("my-container").
            GetDirectoryClient("my-parent-directory");

    List<PathAccessControlItem> accessControlList = 
        new List<PathAccessControlItem>() 
    {
        new PathAccessControlItem(AccessControlType.User, 
            RolePermissions.Read | 
            RolePermissions.Write | 
            RolePermissions.Execute, isDefaultScope),
                    
        new PathAccessControlItem(AccessControlType.Group, 
            RolePermissions.Read | 
            RolePermissions.Execute, isDefaultScope),
                    
        new PathAccessControlItem(AccessControlType.Other, 
            RolePermissions.None, isDefaultScope),

        new PathAccessControlItem(AccessControlType.User, 
            RolePermissions.Read | 
            RolePermissions.Execute, isDefaultScope,
            entityId: "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"),
    };

    await directoryClient.SetAccessControlRecursiveAsync
        (accessControlList, null);
}

```

일괄 처리 크기를 지정 하 여 일괄 처리에서 Acl을 재귀적으로 설정 하는 예제를 보려면 .NET [샘플](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2FRecursive-Acl-Sample-Net.zip%3Fsv%3D2019-02-02%26st%3D2020-08-24T07%253A45%253A28Z%26se%3D2021-09-25T07%253A45%253A00Z%26sr%3Db%26sp%3Dr%26sig%3D2GI3f0KaKMZbTi89AgtyGg%252BJePgNSsHKCL68V6I5W3s%253D&data=02%7C01%7Cnormesta%40microsoft.com%7C6eae76c57d224fb6de8908d848525330%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637338865714571853&sdata=%2FWom8iI3DSDMSw%2FfYvAaQ69zbAoqXNTQ39Q9yVMnASA%3D&reserved=0)을 참조 하세요.

### <a name="java"></a>[Java](#tab/java)

**DataLakeDirectoryClient** 를 재귀적으로 호출 하 여 ACL을 설정 합니다. 이 메서드를 [Pathaccesscontrolentry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) 개체의 [목록](https://docs.oracle.com/javase/8/docs/api/java/util/List.html) 으로 전달 합니다. 각 [Pathaccesscontrolentry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) 는 ACL 항목을 정의 합니다. 

**기본** ACL 항목을 설정 하려면 [Pathaccesscontrolentry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) 의 **setdefaultscope** 메서드를 호출 하 고 값을 **true로** 전달 하면 됩니다. 

이 예에서는 라는 디렉터리의 ACL을 설정 합니다 `my-parent-directory` . 이 메서드는 `isDefaultScope` 기본 ACL을 설정할지 여부를 지정 하는 라는 부울 매개 변수를 허용 합니다. 이 매개 변수는 [Pathaccesscontrolentry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html)의 **setdefaultscope** 메서드를 호출할 때마다 사용 됩니다. ACL의 항목은 소유 사용자에 대 한 읽기, 쓰기 및 실행 권한을 부여 하 고, 소유 그룹에 읽기 및 실행 권한만 제공 하 고, 다른 모든 사용자에 게 액세스 권한을 부여 하지는 않습니다. 이 예제의 마지막 ACL 항목은 개체 ID "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" 읽기 및 실행 권한이 있는 특정 사용자를 제공 합니다.

```java
static public void SetACLRecursively(DataLakeFileSystemClient fileSystemClient, Boolean isDefaultScope){
    
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.getDirectoryClient("my-parent-directory");

    List<PathAccessControlEntry> pathAccessControlEntries = 
        new ArrayList<PathAccessControlEntry>();

    // Create owner entry.
    PathAccessControlEntry ownerEntry = new PathAccessControlEntry();

    RolePermissions ownerPermission = new RolePermissions();
    ownerPermission.setExecutePermission(true).setReadPermission(true).setWritePermission(true);

    ownerEntry.setDefaultScope(isDefaultScope);
    ownerEntry.setAccessControlType(AccessControlType.USER);
    ownerEntry.setPermissions(ownerPermission);

    pathAccessControlEntries.add(ownerEntry);

    // Create group entry.
    PathAccessControlEntry groupEntry = new PathAccessControlEntry();

    RolePermissions groupPermission = new RolePermissions();
    groupPermission.setExecutePermission(true).setReadPermission(true).setWritePermission(false);

    groupEntry.setDefaultScope(isDefaultScope);
    groupEntry.setAccessControlType(AccessControlType.GROUP);
    groupEntry.setPermissions(groupPermission);

    pathAccessControlEntries.add(groupEntry);

    // Create other entry.
    PathAccessControlEntry otherEntry = new PathAccessControlEntry();

    RolePermissions otherPermission = new RolePermissions();
    otherPermission.setExecutePermission(false).setReadPermission(false).setWritePermission(false);

    otherEntry.setDefaultScope(isDefaultScope);
    otherEntry.setAccessControlType(AccessControlType.OTHER);
    otherEntry.setPermissions(otherPermission);

    pathAccessControlEntries.add(otherEntry);

    // Create named user entry.
    PathAccessControlEntry userEntry = new PathAccessControlEntry();

    RolePermissions userPermission = new RolePermissions();
    userPermission.setExecutePermission(true).setReadPermission(true).setWritePermission(false);

    userEntry.setDefaultScope(isDefaultScope);
    userEntry.setAccessControlType(AccessControlType.USER);
    userEntry.setEntityId("xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx");
    userEntry.setPermissions(userPermission);    
    
    pathAccessControlEntries.add(userEntry);
    
    directoryClient.setAccessControlRecursive(pathAccessControlEntries);        

}
```

### <a name="python"></a>[Python](#tab/python)

**DataLakeDirectoryClient.set_access_control_recursive** 메서드를 호출 하 여 ACL을 재귀적으로 설정 합니다.

**기본** acl 항목을 설정 하려면 `default:` 각 acl 항목 문자열의 시작 부분에 문자열을 추가 합니다. 

이 예에서는 라는 디렉터리의 ACL을 설정 합니다 `my-parent-directory` . 

이 메서드는 `is_default_scope` 기본 ACL을 설정할지 여부를 지정 하는 라는 부울 매개 변수를 허용 합니다. 해당 매개 변수가 이면 `True` ACL 항목의 목록 앞에 문자열을 표시 합니다 `default:` . 

ACL의 항목은 소유 사용자에 대 한 읽기, 쓰기 및 실행 권한을 부여 하 고, 소유 그룹에 읽기 및 실행 권한만 제공 하 고, 다른 모든 사용자에 게 액세스 권한을 부여 하지는 않습니다. 이 예제의 마지막 ACL 항목은 개체 ID "" xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx "읽기 및 실행 권한이 있는 특정 사용자를 제공 합니다. 이러한 항목은 소유 하는 사용자에 게 읽기, 쓰기 및 실행 권한을 부여 하 고, 소유 그룹만 읽기 및 실행 권한을 부여 하 고, 다른 모든 사용자에 게 액세스 권한을 부여 하지는 않습니다. 이 예제의 마지막 ACL 항목은 개체 ID "" xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx "읽기 및 실행 권한이 있는 특정 사용자를 제공 합니다.

```python
def set_permission_recursively(is_default_scope):
    
    try:
        file_system_client = service_client.get_file_system_client(file_system="my-container")

        directory_client = file_system_client.get_directory_client("my-parent-directory")
              
        acl = 'user::rwx,group::rwx,other::rwx,user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx:r--'   

        if is_default_scope:
           acl = 'default:user::rwx,default:group::rwx,default:other::rwx,default:user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx:r--'

        directory_client.set_access_control_recursive(acl=acl)
        
    except Exception as e:
     print(e)
```

일괄 처리 크기를 지정 하 여 일괄 처리에서 Acl을 재귀적으로 처리 하는 예제를 보려면 python [샘플](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-datalake/samples/datalake_samples_access_control_recursive.py)을 참조 하세요.

---

## <a name="update-an-acl-recursively"></a>재귀적으로 ACL 업데이트

Acl을 *업데이트할* 때 acl을 대체 하는 대신 acl을 수정 합니다. 예를 들어 ACL에 나열 된 다른 보안 주체에 영향을 주지 않고 ACL에 새 보안 주체를 추가할 수 있습니다.  ACL을 업데이트 하지 않고 바꾸려면이 문서의 [acl을 재귀적으로 설정](#set-an-acl-recursively) 섹션을 참조 하세요. 

ACL을 업데이트 하려면 업데이트 하려는 ACL 항목을 사용 하 여 새 ACL 개체를 만든 다음 ACL 업데이트 작업에서 해당 개체를 사용 합니다. 기존 ACL을 가져오지 말고 업데이트할 ACL 항목만 제공 하면 됩니다.

이 섹션에는 ACL을 업데이트 하는 방법에 대 한 예제가 포함 되어 있습니다.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

**AzDataLakeGen2AclRecursive** cmdlet을 사용 하 여 ACL을 재귀적으로 업데이트 합니다. 

이 예에서는 write 권한이 있는 ACL 항목을 업데이트 합니다. 

```powershell
$filesystemName = "my-container"
$dirname = "my-parent-directory/"
$userID = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx";

$acl = Set-AzDataLakeGen2ItemAclObject -AccessControlType user -EntityId $userID -Permission rwx

Update-AzDataLakeGen2AclRecursive -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl

```

> [!NOTE]
> **기본** ACL 항목을 업데이트 하려면 **AzDataLakeGen2ItemAclObject** 명령을 실행할 때 **-defaultscope** 매개 변수를 사용 합니다. 예: `$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -EntityId $userID -Permission rwx -DefaultScope`.

일괄 처리 크기를 지정 하 여 일괄 처리에서 Acl을 재귀적으로 업데이트 하는 예제를 보려면 [AzDataLakeGen2AclRecursive](https://docs.microsoft.com/powershell/module/az.storage/update-azdatalakegen2aclrecursive) 참조 문서를 참조 하세요.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[Az storage fs access update-recursive](https://docs.microsoft.com/cli/azure/storage/fs/access#az_storage_fs_access_update_recursive) 명령을 사용 하 여 ACL을 재귀적으로 업데이트 합니다. 

이 예에서는 write 권한이 있는 ACL 항목을 업데이트 합니다. 

```azurecli
az storage fs access update-recursive --acl "user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx:rwx" -p my-parent-directory/ -f my-container --account-name mystorageaccount --auth-mode login
```

> [!NOTE]
> **기본** ACL 항목을 업데이트 하려면 `default:` 각 항목에 접두사를 추가 합니다. 예: `default:user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx:r-x`.

### <a name="net"></a>[.NET](#tab/dotnet)

**DataLakeDirectoryClient UpdateAccessControlRecursiveAsync** 메서드를 호출 하 여 ACL을 재귀적으로 업데이트 합니다.  이 메서드를 [Pathaccesscontrolitem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem) [목록](/dotnet/api/system.collections.generic.list-1) 으로 전달 합니다. 각 [Pathaccesscontrolitem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem) 은 ACL 항목을 정의 합니다. 

**기본** ACL 항목을 업데이트 하려는 경우 [Pathaccesscontrolitem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem) 의 [defaultscope](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem.defaultscope#Azure_Storage_Files_DataLake_Models_PathAccessControlItem_DefaultScope) 속성을 **true** 로 설정할 수 있습니다. 

이 예에서는 write 권한이 있는 ACL 항목을 업데이트 합니다. 이 메서드는 `isDefaultScope` 기본 ACL의 업데이트 여부를 지정 하는 라는 부울 매개 변수를 허용 합니다. 이 매개 변수는 [Pathaccesscontrolitem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem)의 생성자에서 사용 됩니다.

```cs
public async void UpdateACLsRecursively(DataLakeServiceClient serviceClient, bool isDefaultScope)
{
    DataLakeDirectoryClient directoryClient =
        serviceClient.GetFileSystemClient("my-container").
        GetDirectoryClient("my-parent-directory");

    List<PathAccessControlItem> accessControlListUpdate = 
        new List<PathAccessControlItem>()
    {
        new PathAccessControlItem(AccessControlType.User, 
            RolePermissions.Read |
            RolePermissions.Write | 
            RolePermissions.Execute, isDefaultScope, 
            entityId: "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"),
    };

    await directoryClient.UpdateAccessControlRecursiveAsync
        (accessControlListUpdate, null);

}
```

일괄 처리 크기를 지정 하 여 일괄 처리에서 Acl을 재귀적으로 업데이트 하는 예제를 보려면 .NET [샘플](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2FRecursive-Acl-Sample-Net.zip%3Fsv%3D2019-02-02%26st%3D2020-08-24T07%253A45%253A28Z%26se%3D2021-09-25T07%253A45%253A00Z%26sr%3Db%26sp%3Dr%26sig%3D2GI3f0KaKMZbTi89AgtyGg%252BJePgNSsHKCL68V6I5W3s%253D&data=02%7C01%7Cnormesta%40microsoft.com%7C6eae76c57d224fb6de8908d848525330%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637338865714571853&sdata=%2FWom8iI3DSDMSw%2FfYvAaQ69zbAoqXNTQ39Q9yVMnASA%3D&reserved=0)을 참조 하세요.

### <a name="java"></a>[Java](#tab/java)

**DataLakeDirectoryClient Accesscontrolrecursive** 메서드를 호출 하 여 ACL을 재귀적으로 업데이트 합니다.  이 메서드를 [Pathaccesscontrolentry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) 개체의 [목록](https://docs.oracle.com/javase/8/docs/api/java/util/List.html) 으로 전달 합니다. 각 [Pathaccesscontrolentry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) 는 ACL 항목을 정의 합니다. 

**기본** ACL 항목을 업데이트 하려면 [Pathaccesscontrolentry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) 의 **setdefaultscope** 메서드를 선택 하 고 값을 **true로** 전달 하면 됩니다. 

이 예에서는 write 권한이 있는 ACL 항목을 업데이트 합니다. 이 메서드는 `isDefaultScope` 기본 ACL의 업데이트 여부를 지정 하는 라는 부울 매개 변수를 허용 합니다. 이 매개 변수는 [Pathaccesscontrolentry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html)의 **setdefaultscope** 메서드 호출에 사용 됩니다. 

```java
static public void UpdateACLRecursively(DataLakeFileSystemClient fileSystemClient, Boolean isDefaultScope){

    DataLakeDirectoryClient directoryClient =
    fileSystemClient.getDirectoryClient("my-parent-directory");

    List<PathAccessControlEntry> pathAccessControlEntries = 
        new ArrayList<PathAccessControlEntry>();

    // Create named user entry.
    PathAccessControlEntry userEntry = new PathAccessControlEntry();

    RolePermissions userPermission = new RolePermissions();
    userPermission.setExecutePermission(true).setReadPermission(true).setWritePermission(true);

    userEntry.setDefaultScope(isDefaultScope);
    userEntry.setAccessControlType(AccessControlType.USER);
    userEntry.setEntityId("xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx");
    userEntry.setPermissions(userPermission);    
    
    pathAccessControlEntries.add(userEntry);
    
    directoryClient.updateAccessControlRecursive(pathAccessControlEntries);          
}
```

### <a name="python"></a>[Python](#tab/python)

**DataLakeDirectoryClient.update_access_control_recursive** 메서드를 호출 하 여 ACL을 재귀적으로 업데이트 합니다. **기본** acl 항목을 업데이트 하려면 `default:` 각 acl 항목 문자열의 시작 부분에 문자열을 추가 합니다. 

이 예에서는 write 권한이 있는 ACL 항목을 업데이트 합니다.

이 예에서는 라는 디렉터리의 ACL을 설정 합니다 `my-parent-directory` . 이 메서드는 `is_default_scope` 기본 ACL의 업데이트 여부를 지정 하는 라는 부울 매개 변수를 허용 합니다. 해당 매개 변수가 이면 `True` 업데이트 된 ACL 항목 앞에 문자열을 입력 합니다 `default:` .  

```python
def update_permission_recursively(is_default_scope):
    
    try:
        file_system_client = service_client.get_file_system_client(file_system="my-container")

        directory_client = file_system_client.get_directory_client("my-parent-directory")

        acl = 'user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx:rwx'   

        if is_default_scope:
           acl = 'default:user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx:rwx'

        directory_client.update_access_control_recursive(acl=acl)

        acl_props = directory_client.get_access_control()
        
        print(acl_props['permissions'])

    except Exception as e:
     print(e)
```

일괄 처리 크기를 지정 하 여 일괄 처리에서 Acl을 재귀적으로 처리 하는 예제를 보려면 python [샘플](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-datalake/samples/datalake_samples_access_control_recursive.py)을 참조 하세요.

---

## <a name="remove-acl-entries-recursively"></a>ACL 항목을 재귀적으로 제거

하나 이상의 ACL 항목을 재귀적으로 제거할 수 있습니다. ACL 항목을 제거 하려면 acl 항목을 제거할 새 ACL 개체를 만든 다음 ACL 제거 작업에서 해당 개체를 사용 합니다. 기존 ACL을 가져오지 말고 제거할 ACL 항목만 제공 하면 됩니다. 

이 섹션에는 ACL을 제거 하는 방법에 대 한 예제가 포함 되어 있습니다. 

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

**AzDataLakeGen2AclRecursive** cmdlet을 사용 하 여 ACL 항목을 제거 합니다. 

이 예에서는 컨테이너의 루트 디렉터리에서 ACL 항목을 제거 합니다.  

```powershell
$filesystemName = "my-container"
$userID = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"

$acl = Set-AzDataLakeGen2ItemAclObject -AccessControlType user -EntityId $userID -Permission "---" 

Remove-AzDataLakeGen2AclRecursive -Context $ctx -FileSystem $filesystemName  -Acl $acl
```

> [!NOTE]
> **기본** ACL 항목을 제거 하려면 **AzDataLakeGen2ItemAclObject** 명령을 실행할 때 **-defaultscope** 매개 변수를 사용 합니다. 예: `$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -EntityId $userID -Permission "---" -DefaultScope`.

일괄 처리 크기를 지정 하 여 일괄 처리에서 Acl을 재귀적으로 제거 하는 예제를 보려면 [AzDataLakeGen2AclRecursive](https://docs.microsoft.com/powershell/module/az.storage/remove-azdatalakegen2aclrecursive) 참조 문서를 참조 하세요.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[Az storage fs access remove-recursive](https://docs.microsoft.com/cli/azure/storage/fs/access#az_storage_fs_access_remove_recursive) 명령을 사용 하 여 ACL 항목을 제거 합니다. 

이 예에서는 컨테이너의 루트 디렉터리에서 ACL 항목을 제거 합니다.  

```azurecli
az storage fs access remove-recursive --acl "user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" -p my-parent-directory/ -f my-container --account-name mystorageaccount --auth-mode login
```

> [!NOTE]
> **기본** ACL 항목을 제거 하려면 `default:` 각 항목에 접두사를 추가 합니다. 예: `default:user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`.

### <a name="net"></a>[.NET](#tab/dotnet)

**DataLakeDirectoryClient RemoveAccessControlRecursiveAsync** 메서드를 호출 하 여 ACL 항목을 제거 합니다. 이 메서드를 [Pathaccesscontrolitem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem) [목록](/dotnet/api/system.collections.generic.list-1) 으로 전달 합니다. 각 [Pathaccesscontrolitem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem) 은 ACL 항목을 정의 합니다. 

**기본** ACL 항목을 제거 하려는 경우 [Pathaccesscontrolitem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem) 의 [defaultscope](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem.defaultscope#Azure_Storage_Files_DataLake_Models_PathAccessControlItem_DefaultScope) 속성을 **true** 로 설정할 수 있습니다. 

이 예에서는 라는 디렉터리의 ACL에서 ACL 항목을 제거 `my-parent-directory` 합니다. 이 메서드는 `isDefaultScope` 기본 ACL에서 항목을 제거할지 여부를 지정 하는 라는 부울 매개 변수를 허용 합니다. 이 매개 변수는 [Pathaccesscontrolitem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem)의 생성자에서 사용 됩니다.

```cs
public async void RemoveACLsRecursively(DataLakeServiceClient serviceClient, isDefaultScope)
{
    DataLakeDirectoryClient directoryClient =
        serviceClient.GetFileSystemClient("my-container").
            GetDirectoryClient("my-parent-directory");

    List<RemovePathAccessControlItem> accessControlListForRemoval = 
        new List<RemovePathAccessControlItem>()
        {
            new RemovePathAccessControlItem(AccessControlType.User, isDefaultScope,
            entityId: "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"),
        };

    await directoryClient.RemoveAccessControlRecursiveAsync
        (accessControlListForRemoval, null);

}
```

일괄 처리 크기를 지정 하 여 일괄 처리에서 Acl을 재귀적으로 제거 하는 예제를 보려면 .NET [샘플](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2FRecursive-Acl-Sample-Net.zip%3Fsv%3D2019-02-02%26st%3D2020-08-24T07%253A45%253A28Z%26se%3D2021-09-25T07%253A45%253A00Z%26sr%3Db%26sp%3Dr%26sig%3D2GI3f0KaKMZbTi89AgtyGg%252BJePgNSsHKCL68V6I5W3s%253D&data=02%7C01%7Cnormesta%40microsoft.com%7C6eae76c57d224fb6de8908d848525330%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637338865714571853&sdata=%2FWom8iI3DSDMSw%2FfYvAaQ69zbAoqXNTQ39Q9yVMnASA%3D&reserved=0)을 참조 하세요.

### <a name="java"></a>[Java](#tab/java)

**DataLakeDirectoryClient Accesscontrolrecursive** 메서드를 호출 하 여 ACL 항목을 제거 합니다. 이 메서드를 [Pathaccesscontrolentry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) 개체의 [목록](https://docs.oracle.com/javase/8/docs/api/java/util/List.html) 으로 전달 합니다. 각 [Pathaccesscontrolentry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) 는 ACL 항목을 정의 합니다. 

**기본** ACL 항목을 제거 하려는 경우 [Pathaccesscontrolentry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) 의 **setdefaultscope** 메서드를 사용할 수 있으며 값을 **true로** 전달할 수 있습니다.  

이 예에서는 라는 디렉터리의 ACL에서 ACL 항목을 제거 `my-parent-directory` 합니다. 이 메서드는 `isDefaultScope` 기본 ACL에서 항목을 제거할지 여부를 지정 하는 라는 부울 매개 변수를 허용 합니다. 이 매개 변수는 [Pathaccesscontrolentry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html)의 **setdefaultscope** 메서드 호출에 사용 됩니다.


```java
static public void RemoveACLRecursively(DataLakeFileSystemClient fileSystemClient, Boolean isDefaultScope){

    DataLakeDirectoryClient directoryClient =
    fileSystemClient.getDirectoryClient("my-parent-directory");

    List<PathRemoveAccessControlEntry> pathRemoveAccessControlEntries = 
        new ArrayList<PathRemoveAccessControlEntry>();

    // Create named user entry.
    PathRemoveAccessControlEntry userEntry = new PathRemoveAccessControlEntry();

    RolePermissions userPermission = new RolePermissions();
    userPermission.setExecutePermission(true).setReadPermission(true).setWritePermission(true);

    userEntry.setDefaultScope(isDefaultScope);
    userEntry.setAccessControlType(AccessControlType.USER);
    userEntry.setEntityId("xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"); 
    
    pathRemoveAccessControlEntries.add(userEntry);
    
    directoryClient.removeAccessControlRecursive(pathRemoveAccessControlEntries);      

}
```

### <a name="python"></a>[Python](#tab/python)

**DataLakeDirectoryClient.remove_access_control_recursive** 메서드를 호출 하 여 ACL 항목을 제거 합니다. **기본** acl 항목을 제거 하려면 `default:` acl 항목 문자열의 시작 부분에 문자열을 추가 합니다. 

이 예에서는 라는 디렉터리의 ACL에서 ACL 항목을 제거 `my-parent-directory` 합니다. 이 메서드는 `is_default_scope` 기본 ACL에서 항목을 제거할지 여부를 지정 하는 라는 부울 매개 변수를 허용 합니다. 해당 매개 변수가 이면 `True` 업데이트 된 ACL 항목 앞에 문자열을 입력 합니다 `default:` . 

```python
def remove_permission_recursively(is_default_scope):
    
    try:
        file_system_client = service_client.get_file_system_client(file_system="my-container")

        directory_client = file_system_client.get_directory_client("my-parent-directory")
              
        acl = 'user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'

        if is_default_scope:
           acl = 'default:user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'

        directory_client.remove_access_control_recursive(acl=acl)

    except Exception as e:
     print(e)
```

일괄 처리 크기를 지정 하 여 일괄 처리에서 Acl을 재귀적으로 처리 하는 예제를 보려면 python [샘플](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-datalake/samples/datalake_samples_access_control_recursive.py)을 참조 하세요.

---

## <a name="recover-from-failures"></a>오류에서 복구

런타임 또는 사용 권한 오류가 발생할 수 있습니다. 런타임 오류의 경우 처음부터 프로세스를 다시 시작 합니다. 보안 주체에 수정 되는 디렉터리 계층 구조에 있는 디렉터리 또는 파일의 ACL을 수정할 수 있는 권한이 없으면 권한 오류가 발생할 수 있습니다. 권한 문제를 해결 한 다음 연속 토큰을 사용 하 여 오류 지점에서 프로세스를 다시 시작 하거나 프로세스를 처음부터 다시 시작 하도록 선택 합니다. 처음부터 다시 시작 하는 것을 선호 하는 경우 연속 토큰을 사용할 필요가 없습니다. 부정적인 영향 없이 ACL 항목을 다시 적용할 수 있습니다.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

이 예에서는 결과를 변수로 반환 하 고 실패 한 항목을 형식이 지정 된 테이블로 파이프 합니다.

```powershell
$result = Set-AzDataLakeGen2AclRecursive -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl
$result
$result.FailedEntries | ft 
```

테이블의 출력에 따라 모든 사용 권한 오류를 수정한 다음 연속 토큰을 사용 하 여 실행을 다시 시작할 수 있습니다.

```powershell
$result = Set-AzDataLakeGen2AclRecursive -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl -ContinuationToken $result.ContinuationToken
$result

```

일괄 처리 크기를 지정 하 여 일괄 처리에서 Acl을 재귀적으로 설정 하는 예제를 보려면 [AzDataLakeGen2AclRecursive](https://docs.microsoft.com/powershell/module/az.storage/set-azdatalakegen2aclrecursive) 참조 문서를 참조 하세요.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

오류가 발생 하는 경우 매개 변수를로 설정 하 여 연속 토큰을 반환할 수 있습니다 `--continue-on-failure` `false` . 오류를 해결 한 후 명령을 다시 실행 하 고 `--continuation` 매개 변수를 연속 토큰으로 설정 하 여 오류 발생 지점에서 프로세스를 다시 시작할 수 있습니다. 

```azurecli
az storage fs access set-recursive --acl "user::rw-,group::r-x,other::---" --continue-on-failure false --continuation xxxxxxx -p my-parent-directory/ -f my-container --account-name mystorageaccount --auth-mode login  
```

## <a name="net"></a>[.NET](#tab/dotnet)

이 예에서는 오류가 발생 한 경우 연속 토큰을 반환 합니다. 응용 프로그램은 오류가 해결 된 후에이 예제 메서드를 다시 호출 하 고 연속 토큰을 전달할 수 있습니다. 이 예제 메서드를 처음 호출 하는 경우 응용 프로그램은 `null` 연속 토큰 매개 변수에 대 한 값을 전달할 수 있습니다. 

```cs
public async Task<string> ResumeAsync(DataLakeServiceClient serviceClient,
    DataLakeDirectoryClient directoryClient,
    List<PathAccessControlItem> accessControlList, 
    string continuationToken)
{
    try
    {
        var accessControlChangeResult =
            await directoryClient.SetAccessControlRecursiveAsync(
                accessControlList, continuationToken: continuationToken, null);

        if (accessControlChangeResult.Value.Counters.FailedChangesCount > 0)
        {
            continuationToken =
                accessControlChangeResult.Value.ContinuationToken;
        }

        return continuationToken;
    }
    catch (Exception ex)
    {
        Console.WriteLine(ex.ToString());
        return continuationToken;
    }

}
```

일괄 처리 크기를 지정 하 여 일괄 처리에서 Acl을 재귀적으로 설정 하는 예제를 보려면 .NET [샘플](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2FRecursive-Acl-Sample-Net.zip%3Fsv%3D2019-02-02%26st%3D2020-08-24T07%253A45%253A28Z%26se%3D2021-09-25T07%253A45%253A00Z%26sr%3Db%26sp%3Dr%26sig%3D2GI3f0KaKMZbTi89AgtyGg%252BJePgNSsHKCL68V6I5W3s%253D&data=02%7C01%7Cnormesta%40microsoft.com%7C6eae76c57d224fb6de8908d848525330%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637338865714571853&sdata=%2FWom8iI3DSDMSw%2FfYvAaQ69zbAoqXNTQ39Q9yVMnASA%3D&reserved=0)을 참조 하세요.

### <a name="java"></a>[Java](#tab/java)

이 예에서는 오류가 발생 한 경우 연속 토큰을 반환 합니다. 응용 프로그램은 오류가 해결 된 후에이 예제 메서드를 다시 호출 하 고 연속 토큰을 전달할 수 있습니다. 이 예제 메서드를 처음 호출 하는 경우 응용 프로그램은 `null` 연속 토큰 매개 변수에 대 한 값을 전달할 수 있습니다. 

```java
static public String ResumeSetACLRecursively(DataLakeFileSystemClient fileSystemClient,
DataLakeDirectoryClient directoryClient,
List<PathAccessControlEntry> accessControlList, 
String continuationToken){

    try{
        PathSetAccessControlRecursiveOptions options = new PathSetAccessControlRecursiveOptions(accessControlList);
        
        options.setContinuationToken(continuationToken);
    
        Response<AccessControlChangeResult> accessControlChangeResult =  
            directoryClient.setAccessControlRecursiveWithResponse(options, null, null);

        if (accessControlChangeResult.getValue().getCounters().getFailedChangesCount() > 0)
        {
            continuationToken =
                accessControlChangeResult.getValue().getContinuationToken();
        }
    
        return continuationToken;

    }
    catch(Exception ex){
    
        System.out.println(ex.toString());
        return continuationToken;
    }
}
```

### <a name="python"></a>[Python](#tab/python)

이 예에서는 오류가 발생 한 경우 연속 토큰을 반환 합니다. 응용 프로그램은 오류가 해결 된 후에이 예제 메서드를 다시 호출 하 고 연속 토큰을 전달할 수 있습니다. 이 예제 메서드를 처음 호출 하는 경우 응용 프로그램은 ``None`` 연속 토큰 매개 변수에 대 한 값을 전달할 수 있습니다. 

```python
def resume_set_acl_recursive(continuation_token):
    
    try:
        file_system_client = service_client.get_file_system_client(file_system="my-container")

        directory_client = file_system_client.get_directory_client("my-parent-directory")
              
        acl = 'user::rwx,group::rwx,other::rwx,user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx:r-x'

        acl_change_result = directory_client.set_access_control_recursive(acl=acl, continuation=continuation_token)

        continuation_token = acl_change_result.continuation

        return continuation_token
        
    except Exception as e:
     print(e) 
     return continuation_token
```

일괄 처리 크기를 지정 하 여 일괄 처리에서 Acl을 재귀적으로 처리 하는 예제를 보려면 python [샘플](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-datalake/samples/datalake_samples_access_control_recursive.py)을 참조 하세요.

---

권한 오류가 발생 해도 프로세스가 중단 되지 않도록 하려면이를 지정할 수 있습니다.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

이 예제에서는 매개 변수를 사용 하 여 `ContinueOnFailure` 작업에 사용 권한 오류가 발생 해도 계속 실행 되도록 합니다. 

```powershell
$result = Set-AzDataLakeGen2AclRecursive -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl -ContinueOnFailure

echo "[Result Summary]"
echo "TotalDirectoriesSuccessfulCount: `t$($result.TotalFilesSuccessfulCount)"
echo "TotalFilesSuccessfulCount: `t`t`t$($result.TotalDirectoriesSuccessfulCount)"
echo "TotalFailureCount: `t`t`t`t`t$($result.TotalFailureCount)"
echo "FailedEntries:"$($result.FailedEntries | ft) 
```

일괄 처리 크기를 지정 하 여 일괄 처리에서 Acl을 재귀적으로 설정 하는 예제를 보려면 [AzDataLakeGen2AclRecursive](https://docs.microsoft.com/powershell/module/az.storage/set-azdatalakegen2aclrecursive) 참조 문서를 참조 하세요.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

프로세스가 중단 없이 완료 되 게 하려면 `--continue-on-failure` 매개 변수를로 설정 `true` 합니다. 

```azurecli
az storage fs access set-recursive --acl "user::rw-,group::r-x,other::---" --continue-on-failure true --continuation xxxxxxx -p my-parent-directory/ -f my-container --account-name mystorageaccount --auth-mode login  
```

### <a name="net"></a>[.NET](#tab/dotnet)

프로세스가 중단 없이 완료 되도록 하려면 **AccessControlChangedOptions** 개체를 전달 하 고 해당 개체의 **ContinueOnFailure** 속성을로 설정 ``true`` 합니다.

이 예에서는 ACL 항목을 재귀적으로 설정 합니다. 이 코드에 사용 권한 오류가 발생 하면 해당 오류를 기록 하 고 실행을 계속 합니다. 이 예에서는 실패 횟수를 콘솔에 출력 합니다. 

```cs
public async Task ContinueOnFailureAsync(DataLakeServiceClient serviceClient,
    DataLakeDirectoryClient directoryClient, 
    List<PathAccessControlItem> accessControlList)
{
    var accessControlChangeResult = 
        await directoryClient.SetAccessControlRecursiveAsync(
            accessControlList, null, new AccessControlChangeOptions() 
            { ContinueOnFailure = true });

    var counters = accessControlChangeResult.Value.Counters;

    Console.WriteLine("Number of directories changed: " +
        counters.ChangedDirectoriesCount.ToString());

    Console.WriteLine("Number of files changed: " +
        counters.ChangedFilesCount.ToString());

    Console.WriteLine("Number of failures: " +
        counters.FailedChangesCount.ToString());
}
```

일괄 처리 크기를 지정 하 여 일괄 처리에서 Acl을 재귀적으로 설정 하는 예제를 보려면 .NET [샘플](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2FRecursive-Acl-Sample-Net.zip%3Fsv%3D2019-02-02%26st%3D2020-08-24T07%253A45%253A28Z%26se%3D2021-09-25T07%253A45%253A00Z%26sr%3Db%26sp%3Dr%26sig%3D2GI3f0KaKMZbTi89AgtyGg%252BJePgNSsHKCL68V6I5W3s%253D&data=02%7C01%7Cnormesta%40microsoft.com%7C6eae76c57d224fb6de8908d848525330%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637338865714571853&sdata=%2FWom8iI3DSDMSw%2FfYvAaQ69zbAoqXNTQ39Q9yVMnASA%3D&reserved=0)을 참조 하세요.

### <a name="java"></a>[Java](#tab/java)

프로세스가 중단 없이 완료 되도록 하려면 [PathSetAccessControlRecursiveOptions](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) 개체의 **setContinueOnFailure** 메서드를 호출 하 고 **true** 값을 전달 합니다.

이 예에서는 ACL 항목을 재귀적으로 설정 합니다. 이 코드에 사용 권한 오류가 발생 하면 해당 오류를 기록 하 고 실행을 계속 합니다. 이 예에서는 실패 횟수를 콘솔에 출력 합니다. 

```java
static public void ContinueOnFailure(DataLakeFileSystemClient fileSystemClient,
DataLakeDirectoryClient directoryClient,
List<PathAccessControlEntry> accessControlList){
    
    PathSetAccessControlRecursiveOptions options = 
        new PathSetAccessControlRecursiveOptions(accessControlList);
        
    options.setContinueOnFailure(true);
    
    Response<AccessControlChangeResult> accessControlChangeResult =  
        directoryClient.setAccessControlRecursiveWithResponse(options, null, null);

    AccessControlChangeCounters counters = accessControlChangeResult.getValue().getCounters();

    System.out.println("Number of directories changes: " + 
        counters.getChangedDirectoriesCount());

    System.out.println("Number of files changed: " + 
        counters.getChangedDirectoriesCount());

    System.out.println("Number of failures: " + 
        counters.getChangedDirectoriesCount());
}
```

### <a name="python"></a>[Python](#tab/python)

프로세스가 중단 없이 완료 되도록 하려면 연속 토큰을 **DataLakeDirectoryClient.set_access_control_recursive** 메서드에 전달 하지 마십시오.

이 예에서는 ACL 항목을 재귀적으로 설정 합니다. 이 코드에 사용 권한 오류가 발생 하면 해당 오류를 기록 하 고 실행을 계속 합니다. 이 예에서는 실패 횟수를 콘솔에 출력 합니다. 

```python
def continue_on_failure():
    
    try:
        file_system_client = service_client.get_file_system_client(file_system="my-container")

        directory_client = file_system_client.get_directory_client("my-parent-directory")
              
        acl = 'user::rwx,group::rwx,other::rwx,user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx:r--'

        acl_change_result = directory_client.set_access_control_recursive(acl=acl)

        print("Summary: {} directories and {} files were updated successfully, {} failures were counted."
          .format(acl_change_result.counters.directories_successful, acl_change_result.counters.files_successful,
                  acl_change_result.counters.failure_count))
        
    except Exception as e:
     print(e)
```

일괄 처리 크기를 지정 하 여 일괄 처리에서 Acl을 재귀적으로 처리 하는 예제를 보려면 python [샘플](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-datalake/samples/datalake_samples_access_control_recursive.py)을 참조 하세요.

---

## <a name="resources"></a>리소스

이 섹션에는 라이브러리 및 코드 샘플에 대 한 링크가 포함 되어 있습니다.

#### <a name="libraries"></a>라이브러리

- [PowerShell](https://www.powershellgallery.com/packages/Az.Storage/3.0.0)
- [Azure CLI](https://docs.microsoft.com/cli/azure/storage/fs/access)
- [.NET](https://pkgs.dev.azure.com/azure-sdk/public/_packaging/azure-sdk-for-net/nuget/v3/index.json)
- [Java](/java/api/overview/azure/storage-file-datalake-readme)
- [Python](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2Fazure_storage_file_datalake-12.1.0b99-py2.py3-none-any.whl%3Fsv%3D2019-02-02%26st%3D2020-08-24T07%253A47%253A01Z%26se%3D2021-08-25T07%253A47%253A00Z%26sr%3Db%26sp%3Dr%26sig%3DH1XYw4FTLJse%252BYQ%252BfamVL21UPVIKRnnh2mfudA%252BfI0I%253D&data=02%7C01%7Cnormesta%40microsoft.com%7C95a5966d938a4902560e08d84912fe32%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637339693209725909&sdata=acv4KWZdzkITw1lP0%2FiA3lZuW7NF5JObjY26IXttfGI%3D&reserved=0)
- [REST (영문)](https://docs.microsoft.com/rest/api/storageservices/datalakestoragegen2/path/update)

#### <a name="code-samples"></a>코드 샘플

- PowerShell: [추가 정보](https://recursiveaclpr.blob.core.windows.net/privatedrop/README.txt?sv=2019-02-02&st=2020-08-24T17%3A03%3A18Z&se=2021-08-25T17%3A03%3A00Z&sr=b&sp=r&sig=sPdKiCSXWExV62sByeOYqBTqpGmV2h9o8BLij3iPkNQ%3D)  |  [샘플](https://recursiveaclpr.blob.core.windows.net/privatedrop/samplePS.ps1?sv=2019-02-02&st=2020-08-24T17%3A04%3A44Z&se=2021-08-25T17%3A04%3A00Z&sr=b&sp=r&sig=dNNKS%2BZcp%2F1gl6yOx6QLZ6OpmXkN88ZjBeBtym1Mejo%3D)

- Azure CLI: [샘플](https://github.com/Azure/azure-cli/blob/2a55a5350696a3a93a13f364f2104ec8bc82cdd3/src/azure-cli/azure/cli/command_modules/storage/docs/ADLS%20Gen2.md)

- NET: [추가 정보](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2FREADME%2520for%2520net%3Fsv%3D2019-02-02%26st%3D2020-08-25T23%253A20%253A42Z%26se%3D2021-08-26T23%253A20%253A00Z%26sr%3Db%26sp%3Dr%26sig%3DKrnHvasHoSoVeUyr2g%252FSc2aDVW3De4A%252Fvx0lFWZs494%253D&data=02%7C01%7Cnormesta%40microsoft.com%7Cda902e4fe6c24e6a07d908d8494fd4bd%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637339954503767961&sdata=gd%2B2LphTtDFVb7pZko9rkGO9OG%2FVvmeXprHB9IOEYXE%3D&reserved=0)  |  [샘플](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2FRecursive-Acl-Sample-Net.zip%3Fsv%3D2019-02-02%26st%3D2020-08-24T07%253A45%253A28Z%26se%3D2021-09-25T07%253A45%253A00Z%26sr%3Db%26sp%3Dr%26sig%3D2GI3f0KaKMZbTi89AgtyGg%252BJePgNSsHKCL68V6I5W3s%253D&data=02%7C01%7Cnormesta%40microsoft.com%7C6eae76c57d224fb6de8908d848525330%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637338865714571853&sdata=%2FWom8iI3DSDMSw%2FfYvAaQ69zbAoqXNTQ39Q9yVMnASA%3D&reserved=0)

- Python: [추가 정보](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2FREADME%2520for%2520python%3Fsv%3D2019-02-02%26st%3D2020-08-25T23%253A21%253A47Z%26se%3D2021-08-26T23%253A21%253A00Z%26sr%3Db%26sp%3Dr%26sig%3DRq6Bl5lXrtYk79thy8wX7UTbjyd2f%252B6xzVBFFVYbdYg%253D&data=02%7C01%7Cnormesta%40microsoft.com%7Cda902e4fe6c24e6a07d908d8494fd4bd%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637339954503777915&sdata=3e46Lp2miOHj755Gh0odH3M0%2BdTF3loGCCBENrulVTM%3D&reserved=0)  |  [샘플](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-datalake/samples/datalake_samples_access_control_recursive.py)

## <a name="best-practice-guidelines"></a>모범 사례 지침

이 섹션에서는 Acl을 재귀적으로 설정 하는 몇 가지 모범 사례 지침을 제공 합니다. 

#### <a name="handling-runtime-errors"></a>런타임 오류 처리

런타임 오류는 여러 가지 이유로 발생할 수 있습니다 (예: 중단 또는 클라이언트 연결 문제). 런타임 오류가 발생 하는 경우 재귀 ACL 프로세스를 다시 시작 합니다. Acl은 부정적인 영향을 일으키지 않고 항목에 다시 적용할 수 있습니다. 

#### <a name="handling-permission-errors-403"></a>권한 오류 처리 (403)

재귀 ACL 프로세스를 실행 하는 동안 액세스 제어 예외가 발생 하는 경우 AD [보안 주체](https://docs.microsoft.com/azure/role-based-access-control/overview#security-principal) 에 게 디렉터리 계층 구조에 있는 하나 이상의 자식 항목에 ACL을 적용 하는 데 충분 한 권한이 없을 수 있습니다. 권한 오류가 발생 하면 프로세스가 중지 되 고 연속 토큰이 제공 됩니다. 권한 문제를 해결 한 다음 연속 토큰을 사용 하 여 나머지 데이터 집합을 처리 합니다. 이미 성공적으로 처리 된 디렉터리와 파일은 다시 처리 하지 않아도 됩니다. 재귀 ACL 프로세스를 다시 시작 하도록 선택할 수도 있습니다. Acl은 부정적인 영향을 일으키지 않고 항목에 다시 적용할 수 있습니다. 

#### <a name="credentials"></a>자격 증명 

대상 저장소 계정 또는 컨테이너의 범위에서 [저장소 Blob 데이터 소유자](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) 역할이 할당 된 Azure AD 보안 주체를 프로 비전 하는 것이 좋습니다. 

#### <a name="performance"></a>성능 

대기 시간을 줄이려면 storage 계정과 동일한 지역에 있는 Azure VM (가상 머신)에서 재귀 ACL 프로세스를 실행 하는 것이 좋습니다. 

#### <a name="acl-limits"></a>ACL 제한

디렉터리 또는 파일에 적용할 수 있는 최대 Acl 수는 32 액세스 Acl 및 32 기본 Acl입니다. 자세한 내용은 [Azure Data Lake Storage Gen2의 액세스 제어](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)를 참조하세요.

## <a name="see-also"></a>참조

- [Azure Data Lake Storage Gen2의 액세스 제어](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)
- [알려진 문제](data-lake-storage-known-issues.md)


