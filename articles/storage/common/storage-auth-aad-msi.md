---
title: 관리되는 ID로 데이터에 대한 액세스 권한 부여
titleSuffix: Azure Storage
description: Azure 리소스에 관리되는 ID를 사용하여 Azure 가상 컴퓨터, 함수 앱, 가상 시스템 크기 집합 등에서 실행되는 응용 프로그램의 Blob 및 큐 데이터에 대한 액세스를 승인하는 방법을 알아봅니다.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/04/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: f3bac0d47a53da1ec4d1fa08b5f0933f5f65dc56
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79255341"
---
# <a name="authorize-access-to-blob-and-queue-data-with-managed-identities-for-azure-resources"></a>Azure 리소스에 대한 관리 ID로 Blob 및 큐 데이터에 대한 액세스 권한 부여

Azure Blob 및 Queue Storage는 [Azure 리소스에 대한 관리 ID를 사용](../../active-directory/managed-identities-azure-resources/overview.md)하는 Azure Active Directory(Azure AD) 인증을 지원합니다. Azure 리소스에 대한 관리되는 ID는 Azure 가상 시스템(VM), 기능 앱, 가상 시스템 크기 집합 및 기타 서비스에서 실행되는 응용 프로그램에서 Azure AD 자격 증명을 사용하여 Blob 및 큐 데이터에 대한 액세스를 승인할 수 있습니다. Azure AD 인증과 함께 Azure 리소스에 대해 관리되는 ID를 사용하면 클라우드에서 실행되는 응용 프로그램과 함께 자격 증명을 저장하지 않도록 할 수 있습니다.  

이 문서에서는 Azure Resources에 대한 관리되는 ID를 사용하여 Azure VM에서 Blob 또는 큐 데이터에 대한 액세스를 승인하는 방법을 보여 주었습니다. 또한 개발 환경에서 코드를 테스트하는 방법에 대해서도 설명합니다.

## <a name="enable-managed-identities-on-a-vm"></a>VM에서 관리 ID 사용

Azure Resources에 대해 관리되는 ID를 사용하여 VM에서 Blob 및 큐에 대한 액세스를 승인하려면 먼저 VM에서 Azure Resources에 대해 관리되는 ID를 사용하도록 설정해야 합니다. Azure 리소스의 관리 ID를 사용하도록 설정하는 방법을 알아보려면 다음 문서 중 하나를 참조하세요.

- [Azure 포털](https://docs.microsoft.com/azure/active-directory/managed-service-identity/qs-configure-portal-windows-vm)
- [Azure 파워쉘](../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [Azure CLI](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Azure 리소스 관리자 템플릿](../../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Azure 리소스 관리자 클라이언트 라이브러리](../../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)

관리되는 ID에 대한 자세한 내용은 [Azure 리소스에 대한 관리ID를](../../active-directory/managed-identities-azure-resources/overview.md)참조하십시오.

## <a name="authenticate-with-the-azure-identity-library"></a>Azure ID 라이브러리로 인증

Azure Id 클라이언트 라이브러리는 [Azure SDK](https://github.com/Azure/azure-sdk)에 대한 Azure AD 토큰 인증 지원을 제공합니다. .NET, Java, Python 및 JavaScript용 Azure Storage 클라이언트 라이브러리의 최신 버전은 Azure Id 라이브러리와 통합되어 Azure Storage 요청 승인을 위해 OAuth 2.0 토큰을 획득하는 간단하고 안전한 방법을 제공합니다.

Azure Identity 클라이언트 라이브러리의 장점은 동일한 코드를 사용하여 응용 프로그램이 개발 환경에서 실행중인지 Azure에서 실행 중인지 여부를 인증할 수 있다는 것입니다. .NET에 대한 Azure ID 클라이언트 라이브러리는 보안 보안 주체를 인증합니다. 코드가 Azure에서 실행 중일 때 보안 주체는 Azure 리소스에 대해 관리되는 ID입니다. 개발 환경에서는 관리되는 ID가 없으므로 클라이언트 라이브러리는 테스트 목적으로 사용자 또는 서비스 주체를 인증합니다.

인증 후 Azure Identity 클라이언트 라이브러리는 토큰 자격 증명을 가져옵니다. 그런 다음 이 토큰 자격 증명은 Azure Storage에 대한 작업을 수행하기 위해 만드는 서비스 클라이언트 개체에 캡슐화됩니다. 라이브러리는 적절한 토큰 자격 증명을 얻어서 이를 원활하게 처리합니다.

.NET에 대한 Azure Id 클라이언트 라이브러리에 대한 자세한 내용은 .NET 에 [대한 Azure Id 클라이언트 라이브러리를](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/identity/Azure.Identity)참조하십시오. Azure Id 클라이언트 라이브러리에 대한 참조 설명서는 [Azure.Identity 네임스페이스](/dotnet/api/azure.identity)를 참조하십시오.

### <a name="assign-role-based-access-control-rbac-roles-for-access-to-data"></a>데이터에 대한 액세스를 위해 역할 기반 액세스 제어(RBAC) 역할 할당

Azure AD 보안 주체가 Blob 또는 큐 데이터에 액세스하려고 하면 해당 보안 주체가 리소스에 대한 사용 권한을 가져야 합니다. 보안 주체가 Azure에서 관리되는 ID이든 개발 환경에서 코드를 실행하는 Azure AD 사용자 계정이든 관계없이 보안 주체는 Azure Storage의 Blob 또는 큐 데이터에 대한 액세스를 부여하는 RBAC 역할을 할당받아야 합니다. RBAC를 통한 사용 권한 할당에 대한 자세한 내용은 [Azure Active Directory를 사용하여 Azure Blob 및 큐에 대한 액세스 권한 권한 권한에](../common/storage-auth-aad.md#assign-rbac-roles-for-access-rights) **대한 RBAC 역할 할당이라는** 섹션을 참조하십시오.

### <a name="authenticate-the-user-in-the-development-environment"></a>개발 환경에서 사용자 인증

개발 환경에서 코드가 실행중일 때 인증이 자동으로 처리되거나 사용 중인 도구에 따라 브라우저 로그인이 필요할 수 있습니다. 예를 들어 Microsoft Visual Studio는 단일 사인온(SSO)을 지원하므로 활성 Azure AD 사용자 계정이 인증에 자동으로 사용됩니다. SSO에 대한 자세한 내용은 [응용 프로그램에 대한 단일 사인온을](../../active-directory/manage-apps/what-is-single-sign-on.md)참조하십시오.

다른 개발 도구는 웹 브라우저를 통해 로그인하라는 메시지를 표시할 수 있습니다.

### <a name="authenticate-a-service-principal-in-the-development-environment"></a>개발 환경에서 서비스 주체 인증

개발 환경에서 웹 브라우저를 통한 단일 사인온 또는 로그인을 지원하지 않는 경우 서비스 주체를 사용하여 개발 환경에서 인증할 수 있습니다.

#### <a name="create-the-service-principal"></a>서비스 주체 만들기

Azure CLI를 사용하여 서비스 주체를 만들고 RBAC 역할을 할당하려면 [az 광고 sp create for-rbac](/cli/azure/ad/sp#az-ad-sp-create-for-rbac) 명령을 호출합니다. 새 서비스 주체에 할당할 Azure Storage 데이터 액세스 역할을 제공합니다. 또한 역할 할당에 대 한 범위를 제공 합니다. Azure 저장소에 제공된 기본 제공 역할에 대한 자세한 내용은 [Azure 리소스에 대한 기본 제공 역할을](../../role-based-access-control/built-in-roles.md)참조하십시오.

서비스 주체에 역할을 할당할 수 있는 충분한 권한이 없는 경우 계정 소유자 또는 관리자에게 역할 할당을 수행하도록 요청해야 할 수 있습니다.

다음 예제에서는 Azure CLI를 사용하여 새 서비스 주체를 만들고 계정 범위로 **저장소 Blob 데이터 판독기** 역할을 할당합니다.

```azurecli-interactive
az ad sp create-for-rbac \
    --name <service-principal> \
    --role "Storage Blob Data Reader" \
    --scopes /subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>
```

명령은 `az ad sp create-for-rbac` JSON 형식으로 서비스 주 속성 목록을 반환합니다. 다음 단계에서 필요한 환경 변수를 만드는 데 사용할 수 있도록 이러한 값을 복사합니다.

```json
{
    "appId": "generated-app-ID",
    "displayName": "service-principal-name",
    "name": "http://service-principal-uri",
    "password": "generated-password",
    "tenant": "tenant-ID"
}
```

> [!IMPORTANT]
> RBAC 역할 할당이 전파되는 데 몇 분 정도 걸릴 수 있습니다.

#### <a name="set-environment-variables"></a>환경 변수 설정

Azure Identity 클라이언트 라이브러리는 런타임시 세 가지 환경 변수에서 값을 읽고 서비스 주체를 인증합니다. 다음 표는 각 환경 변수에 대해 설정할 값을 설명합니다.

|환경 변수|값
|-|-
|`AZURE_CLIENT_ID`|서비스 주체의 앱 ID
|`AZURE_TENANT_ID`|서비스 주체의 Azure AD 테넌트 ID
|`AZURE_CLIENT_SECRET`|서비스 주체에 대해 생성된 암호

> [!IMPORTANT]
> 환경 변수를 설정한 후 콘솔 창을 닫고 다시 엽니다. Visual Studio 또는 다른 개발 환경을 사용하는 경우 새 환경 변수를 등록하려면 개발 환경을 다시 시작해야 할 수 있습니다.

자세한 내용은 [포털에서 Azure 앱에 대한 ID 만들기를](../../active-directory/develop/howto-create-service-principal-portal.md)참조하십시오.

[!INCLUDE [storage-install-packages-blob-and-identity-include](../../../includes/storage-install-packages-blob-and-identity-include.md)]

## <a name="net-code-example-create-a-block-blob"></a>.NET 코드 예제: 블록 Blob 만들기

Azure ID `using` 및 Azure Storage 클라이언트 라이브러리를 사용 하려면 코드에 다음 지시문을 추가 합니다.

```csharp
using Azure;
using Azure.Identity;
using Azure.Storage.Blobs;
using System;
using System.IO;
using System.Text;
using System.Threading.Tasks;
```

코드에서 Azure Storage에 대한 요청을 승인하는 데 사용할 수 있는 토큰 자격 증명을 얻으려면 [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) 클래스의 인스턴스를 만듭니다. 다음 코드 예제에서는 인증된 토큰 자격 증명을 얻고 이를 사용하여 서비스 클라이언트 개체를 만든 다음 서비스 클라이언트를 사용하여 새 Blob을 업로드하는 방법을 보여 줍니다.

```csharp
async static Task CreateBlockBlobAsync(string accountName, string containerName, string blobName)
{
    // Construct the blob container endpoint from the arguments.
    string containerEndpoint = string.Format("https://{0}.blob.core.windows.net/{1}",
                                                accountName,
                                                containerName);

    // Get a credential and create a client object for the blob container.
    BlobContainerClient containerClient = new BlobContainerClient(new Uri(containerEndpoint),
                                                                    new DefaultAzureCredential());

    try
    {
        // Create the container if it does not exist.
        await containerClient.CreateIfNotExistsAsync();

        // Upload text to a new block blob.
        string blobContents = "This is a block blob.";
        byte[] byteArray = Encoding.ASCII.GetBytes(blobContents);

        using (MemoryStream stream = new MemoryStream(byteArray))
        {
            await containerClient.UploadBlobAsync(blobName, stream);
        }
    }
    catch (RequestFailedException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
}
```

> [!NOTE]
> Azure AD를 사용하여 Blob 또는 큐 데이터에 대한 요청을 승인하려면 해당 요청에 HTTPS를 사용해야 합니다.

## <a name="next-steps"></a>다음 단계

- [RBAC를 사용하여 저장소 데이터에 대한 액세스 권한을 관리합니다.](storage-auth-aad-rbac.md)
- [저장소 응용 프로그램과 Azure AD를 사용합니다.](storage-auth-aad-app.md)
- [Azure AD 자격 증명을 사용하여 Azure CLI 또는 PowerShell 명령을 실행하여 Blob 또는 큐 데이터에 액세스합니다.](authorize-active-directory-powershell.md)
