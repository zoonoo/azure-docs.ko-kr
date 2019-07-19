---
title: Azure 리소스에 대 한 Azure Active Directory 및 관리 id를 사용 하 여 blob 및 큐에 대 한 액세스 권한 부여-Azure Storage
description: Azure Blob 및 Queue storage는 Azure 리소스에 대 한 Azure Active Directory 및 관리 id를 사용 하 여 리소스에 대 한 액세스 권한을 부여 합니다. Azure 리소스에 관리 되는 id를 사용 하 여 Azure virtual machines, 함수 앱, 가상 머신 확장 집합 등에서 실행 되는 응용 프로그램의 blob 및 큐에 대 한 액세스 권한을 부여할 수 있습니다.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 07/15/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 469790660e843816cc431420e7e1407c90a7de05
ms.sourcegitcommit: a6873b710ca07eb956d45596d4ec2c1d5dc57353
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/16/2019
ms.locfileid: "68249908"
---
# <a name="authorize-access-to-blobs-and-queues-with-azure-active-directory-and-managed-identities-for-azure-resources"></a>Azure 리소스에 대 한 Azure Active Directory 및 관리 id를 사용 하 여 blob 및 큐에 대 한 액세스 권한 부여

Azure Blob 및 Queue Storage는 [Azure 리소스에 대한 관리 ID를 사용](../../active-directory/managed-identities-azure-resources/overview.md)하는 Azure Active Directory(Azure AD) 인증을 지원합니다. Azure 리소스에 대 한 관리 되는 id는 azure Vm (가상 머신), 함수 앱, 가상 머신 확장 집합 및 기타 서비스에서 실행 되는 응용 프로그램의 Azure AD 자격 증명을 사용 하 여 blob 및 큐 데이터 액세스 권한을 부여할 수 있습니다. Azure 리소스에 대 한 관리 되는 id를 Azure AD 인증과 함께 사용 하 여 클라우드에서 실행 되는 응용 프로그램에 자격 증명을 저장 하지 않을 수 있습니다.  

이 문서에서는 Azure VM에서 관리 id를 사용 하 여 blob 또는 큐 데이터에 대 한 액세스 권한을 부여 하는 방법을 보여 줍니다.

## <a name="enable-managed-identities-on-a-vm"></a>VM에서 관리 ID 사용

Azure 리소스에 관리 되는 id를 사용 하 여 VM에서 blob 및 큐에 대 한 액세스 권한을 부여 하려면 먼저 VM에서 Azure 리소스에 대 한 관리 되는 id를 사용 하도록 설정 해야 합니다. Azure 리소스의 관리 ID를 사용하도록 설정하는 방법을 알아보려면 다음 문서 중 하나를 참조하세요.

- [Azure Portal](https://docs.microsoft.com/azure/active-directory/managed-service-identity/qs-configure-portal-windows-vm)
- [Azure PowerShell](../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [Azure CLI](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Azure Resource Manager 템플릿](../../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Azure Resource Manager 클라이언트 라이브러리](../../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)

## <a name="grant-permissions-to-an-azure-ad-managed-identity"></a>Azure AD 관리 id에 사용 권한 부여

Blob에 대 한 요청이 나 Azure Storage 응용 프로그램의 관리 되는 id에서 큐 서비스 권한을 부여 하려면 먼저 해당 관리 되는 id에 대 한 RBAC (역할 기반 액세스 제어) 설정을 구성 합니다. Azure Storage는 blob 및 큐 데이터에 대 한 사용 권한을 포함 하는 RBAC 역할을 정의 합니다. RBAC 역할이 관리 id에 할당 되 면 관리 되는 id에 해당 범위의 blob 또는 큐 데이터에 대 한 해당 권한이 부여 됩니다.

RBAC 역할을 할당 하는 방법에 대 한 자세한 내용은 다음 문서 중 하나를 참조 하세요.

- [Azure Portal에서 RBAC를 사용 하 여 Azure blob 및 큐 데이터에 대 한 액세스 권한 부여](storage-auth-aad-rbac-portal.md)
- [Azure CLI를 사용 하 여 Azure blob에 대 한 액세스 권한 부여 및 RBAC를 사용 하 여 데이터 큐](storage-auth-aad-rbac-cli.md)
- [PowerShell을 사용 하 여 Azure blob에 대 한 액세스 권한 부여 및 RBAC를 사용 하 여 데이터 큐](storage-auth-aad-rbac-powershell.md)

## <a name="azure-storage-resource-id"></a>Azure Storage 리소스 ID

[!INCLUDE [storage-resource-id-include](../../../includes/storage-resource-id-include.md)]

## <a name="net-code-example-create-a-block-blob"></a>.NET 코드 예제: 블록 Blob 만들기

이 코드 예제에서는 Azure AD에서 OAuth 2.0 토큰을 가져온 후이를 사용 하 여 블록 blob 만들기 요청에 권한을 부여 하는 방법을 보여 줍니다. 이 예제가 작동 하려면 먼저 앞의 섹션에 설명 된 단계를 따르세요.

.NET 용 [Microsoft Azure 앱 인증](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) 클라이언트 라이브러리 (미리 보기)는 코드에서 토큰을 획득 하 고 갱신 하는 프로세스를 간소화 합니다. 앱 인증 클라이언트 라이브러리는 인증을 자동으로 관리 합니다. 라이브러리는 개발자의 자격 증명을 사용 하 여 로컬 개발 중에 인증 합니다. 로컬 개발 동안 개발자 자격 증명을 사용하는 것이 Azure AD 자격 증명을 만들거나 개발자 간에 자격 증명을 공유할 필요가 없으므로 더 안전합니다. 솔루션을 나중에 Azure에 배포 하는 경우 라이브러리에서 자동으로 응용 프로그램 자격 증명을 사용 하도록 전환 합니다.

### <a name="install-packages"></a>패키지 설치

Azure Storage 응용 프로그램에서 앱 인증 라이브러리를 사용 하려면 [Nuget](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication)에서 최신 미리 보기 패키지를 설치 하 고 최신 버전 [Azure Storage의 .net 용 공용 클라이언트 라이브러리](https://www.nuget.org/packages/Microsoft.Azure.Storage.Common/) 및 [Azure Blob 저장소 클라이언트 라이브러리를 설치 합니다. .NET의 경우](https://www.nuget.org/packages/Microsoft.Azure.Storage.Blob/). 다음 **using** 문을 코드에 추가 합니다.

```csharp
using Microsoft.Azure.Services.AppAuthentication;
using Microsoft.Azure.Storage.Auth;
using Microsoft.Azure.Storage.Blob;
```

### <a name="add-the-callback-method"></a>콜백 메서드 추가

콜백 메서드는 토큰의 만료 시간을 확인 하 고 필요에 따라 갱신 합니다.

```csharp
private static async Task<NewTokenAndFrequency> TokenRenewerAsync(Object state, CancellationToken cancellationToken)
{
    // Specify the resource ID for requesting Azure AD tokens for Azure Storage.
    // Note that you can also specify the root URI for your storage account as the resource ID.
    const string StorageResource = "https://storage.azure.com/";  

    // Use the same token provider to request a new token.
    var authResult = await ((AzureServiceTokenProvider)state).GetAuthenticationResultAsync(StorageResource);

    // Renew the token 5 minutes before it expires.
    var next = (authResult.ExpiresOn - DateTimeOffset.UtcNow) - TimeSpan.FromMinutes(5);
    if (next.Ticks < 0)
    {
        next = default(TimeSpan);
        Console.WriteLine("Renewing token...");
    }

    // Return the new token and the next refresh time.
    return new NewTokenAndFrequency(authResult.AccessToken, next);
}
```

### <a name="get-a-token-and-create-a-block-blob"></a>토큰 가져오기 및 블록 blob 만들기

앱 인증 라이브러리는 **AzureServiceTokenProvider** 클래스를 제공 합니다. 이 클래스의 인스턴스는 토큰을 가져오는 콜백에 전달 된 다음 만료 되기 전에 토큰을 갱신할 수 있습니다.

다음 예제에서는 토큰을 가져와서이를 사용 하 여 새 blob을 만든 다음 동일한 토큰을 사용 하 여 blob을 읽습니다.

```csharp
const string blobName = "https://storagesamples.blob.core.windows.net/sample-container/blob1.txt";

// Get the initial access token and the interval at which to refresh it.
AzureServiceTokenProvider azureServiceTokenProvider = new AzureServiceTokenProvider();
var tokenAndFrequency = TokenRenewerAsync(azureServiceTokenProvider,
                                            CancellationToken.None).GetAwaiter().GetResult();

// Create storage credentials using the initial token, and connect the callback function
// to renew the token just before it expires
TokenCredential tokenCredential = new TokenCredential(tokenAndFrequency.Token,
                                                        TokenRenewerAsync,
                                                        azureServiceTokenProvider,
                                                        tokenAndFrequency.Frequency.Value);

StorageCredentials storageCredentials = new StorageCredentials(tokenCredential);

// Create a blob using the storage credentials.
CloudBlockBlob blob = new CloudBlockBlob(new Uri(blobName),
                                            storageCredentials);

// Upload text to the blob.
blob.UploadTextAsync(string.Format("This is a blob named {0}", blob.Name));

// Continue to make requests against Azure Storage.
// The token is automatically refreshed as needed in the background.
do
{
    // Read blob contents
    Console.WriteLine("Time accessed: {0} Blob Content: {1}",
                        DateTimeOffset.UtcNow,
                        blob.DownloadTextAsync().Result);

    // Sleep for ten seconds, then read the contents of the blob again.
    Thread.Sleep(TimeSpan.FromSeconds(10));
} while (true);
```

앱 인증 라이브러리에 대 한 자세한 내용은 [.net을 사용 하 여 Azure Key Vault에 대 한 서비스 간 인증을](../../key-vault/service-to-service-authentication.md)참조 하세요.

액세스 토큰을 획득 하는 방법에 대 한 자세한 내용은 [AZURE VM에서 azure 리소스에 대해 관리 되는 id를 사용 하 여 액세스 토큰을 가져오는 방법](../../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md)을 참조 하세요.

> [!NOTE]
> Azure AD를 사용 하 여 blob 또는 큐 데이터에 대 한 요청에 권한을 부여 하려면 해당 요청에 대해 HTTPS를 사용 해야 합니다.

## <a name="next-steps"></a>다음 단계

- Azure storage에 대 한 RBAC 역할에 대해 자세히 알아보려면 [rbac를 사용 하 여 저장소 데이터에 대 한 액세스 권한 관리](storage-auth-aad-rbac.md)를 참조 하세요.
- 저장소 애플리케이션 내에서 컨테이너와 큐에 대한 액세스 권한을 부여하는 방법을 알아보려면 [저장소 애플리케이션에서 Azure AD 사용](storage-auth-aad-app.md)을 참조하세요.
- Azure AD 자격 증명을 사용 하 여 Azure CLI 및 PowerShell 명령을 실행 하는 방법을 알아보려면 [AZURE ad 자격 증명을 사용 하 여 Azure CLI 또는 powershell 명령을 실행 하 여 blob 또는 큐 데이터에 액세스](storage-auth-aad-script.md)를 참조 하세요.
