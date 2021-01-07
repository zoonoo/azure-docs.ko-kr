---
title: .NET을 사용 하 여 컨테이너 또는 blob에 대 한 사용자 위임 SAS 만들기
titleSuffix: Azure Storage
description: Azure Storage .NET 클라이언트 라이브러리를 사용 하 여 Azure Active Directory 자격 증명으로 사용자 위임 SAS를 만드는 방법에 대해 알아봅니다.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/11/2020
ms.author: tamram
ms.reviewer: dineshm
ms.subservice: blobs
ms.openlocfilehash: 8667fc3c5224b1f75a9beeb95b6e1261c768c14d
ms.sourcegitcommit: fa807e40d729bf066b9b81c76a0e8c5b1c03b536
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/11/2020
ms.locfileid: "97347917"
---
# <a name="create-a-user-delegation-sas-for-a-container-or-blob-with-net"></a>.NET을 사용 하 여 컨테이너 또는 blob에 대 한 사용자 위임 SAS 만들기

[!INCLUDE [storage-auth-sas-intro-include](../../../includes/storage-auth-sas-intro-include.md)]

이 문서에서는 Azure AD (Azure Active Directory) 자격 증명을 사용 하 여 .NET 용 Azure Storage 클라이언트 라이브러리 버전 12를 사용 하는 컨테이너 또는 blob에 대 한 사용자 위임 SAS를 만드는 방법을 보여 줍니다.

[!INCLUDE [storage-auth-user-delegation-include](../../../includes/storage-auth-user-delegation-include.md)]

## <a name="assign-azure-roles-for-access-to-data"></a>데이터 액세스를 위한 Azure 역할 할당

Azure AD 보안 주체가 blob 데이터에 액세스 하려고 할 때 해당 보안 주체에 게 리소스에 대 한 권한이 있어야 합니다. 보안 주체가 Azure에서 관리 되는 id이 든, 개발 환경에서 코드를 실행 하는 Azure AD 사용자 계정 인지에 관계 없이 보안 주체는 Azure Storage의 blob 데이터에 대 한 액세스 권한을 부여 하는 Azure 역할을 할당 받아야 합니다. Azure RBAC를 통해 사용 권한을 할당 하는 방법에 대 한 자세한 내용은 [Azure Active Directory 사용 하 여 azure blob 및 큐에](../common/storage-auth-aad.md#assign-azure-roles-for-access-rights)대 한 액세스 권한 부여에서 **액세스 권한에 대 한 azure 역할 할당** 섹션을 참조 하세요

[!INCLUDE [storage-install-packages-blob-and-identity-include](../../../includes/storage-install-packages-blob-and-identity-include.md)]

Azure Storage에서 Azure Id 클라이언트 라이브러리를 사용 하 여 인증 하는 방법에 대 한 자세한 내용은 azure [리소스에 대 한 Azure Active Directory 및 관리 id를 사용 하 여 blob 및 큐에 대 한 액세스 권한 부여](../common/storage-auth-aad-msi.md?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json#authenticate-with-the-azure-identity-library)에서 **azure id 라이브러리를 사용** 하 여 인증 섹션을 참조 하세요.

## <a name="get-an-authenticated-token-credential"></a>인증 된 토큰 자격 증명 가져오기

코드에서 Azure Storage 요청에 권한을 부여 하는 데 사용할 수 있는 토큰 자격 증명을 가져오려면 [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) 클래스의 인스턴스를 만듭니다.

다음 코드 조각에서는 인증 된 토큰 자격 증명을 가져와서이를 사용 하 여 Blob 저장소에 대 한 서비스 클라이언트를 만드는 방법을 보여 줍니다.

```csharp
// Construct the blob endpoint from the account name.
string blobEndpoint = string.Format("https://{0}.blob.core.windows.net", accountName);

// Create a new Blob service client with Azure AD credentials.
BlobServiceClient blobClient = new BlobServiceClient(new Uri(blobEndpoint),
                                                     new DefaultAzureCredential());
```

## <a name="get-the-user-delegation-key"></a>사용자 위임 키 가져오기

모든 SAS는 키로 서명 됩니다. 사용자 위임 SAS를 만들려면 먼저 사용자 위임 키를 요청 해야 합니다. 그런 다음 SAS에 서명 하는 데 사용 됩니다. 사용자 위임 키는 Azure AD 자격 증명을 사용 한다는 점을 제외 하 고 서비스 SAS 또는 계정 SAS에 서명 하는 데 사용 되는 계정 키와 유사 합니다. 클라이언트에서 OAuth 2.0 토큰을 사용 하 여 사용자 위임 키를 요청 하면 Azure Storage 사용자를 대신 하 여 사용자 위임 키를 반환 합니다.

사용자 위임 키가 있으면 키의 수명 동안 해당 키를 사용 하 여 사용자 위임 공유 액세스 서명을 원하는 수 만큼 만들 수 있습니다. 사용자 위임 키는 해당 키를 획득 하는 데 사용 되는 OAuth 2.0 토큰과는 독립적 이므로 키가 여전히 유효한 경우에는 토큰을 갱신할 필요가 없습니다. 키가 최대 7 일 동안 유효 하도록 지정할 수 있습니다.

다음 방법 중 하나를 사용 하 여 사용자 위임 키를 요청 합니다.

- [GetUserDelegationKey](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.getuserdelegationkey)
- [GetUserDelegationKeyAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.getuserdelegationkeyasync)

다음 코드 조각은 사용자 위임 키를 가져오고 해당 속성을 작성 합니다.

```csharp
// Get a user delegation key for the Blob service that's valid for seven days.
// You can use the key to generate any number of shared access signatures over the lifetime of the key.
UserDelegationKey key = await blobClient.GetUserDelegationKeyAsync(DateTimeOffset.UtcNow,
                                                                   DateTimeOffset.UtcNow.AddDays(7));

// Read the key's properties.
Console.WriteLine("User delegation key properties:");
Console.WriteLine("Key signed start: {0}", key.SignedStartsOn);
Console.WriteLine("Key signed expiry: {0}", key.SignedExpiresOn);
Console.WriteLine("Key signed object ID: {0}", key.SignedObjectId);
Console.WriteLine("Key signed tenant ID: {0}", key.SignedTenantId);
Console.WriteLine("Key signed service: {0}", key.SignedService);
Console.WriteLine("Key signed version: {0}", key.SignedVersion);
```

## <a name="get-a-user-delegation-sas-for-a-blob"></a>Blob에 대 한 사용자 위임 SAS 가져오기

다음 코드 예제에서는 보안 주체를 인증 하 고 blob에 대 한 사용자 위임 SAS를 만들기 위한 전체 코드를 보여 줍니다.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Sas.cs" id="Snippet_GetUserDelegationSasBlob":::

다음 예제에서는 시뮬레이트된 클라이언트 응용 프로그램에서 이전 예제에서 만든 사용자 위임 SAS를 테스트 합니다. SAS가 유효한 경우 클라이언트 응용 프로그램은 blob의 내용을 읽을 수 있습니다. SAS가 유효 하지 않은 경우 (예: 만료 된 경우) Azure Storage 오류 코드 403 (사용할 수 없음)을 반환 합니다.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Sas.cs" id="Snippet_ReadBlobWithSasAsync":::

## <a name="get-a-user-delegation-sas-for-a-container"></a>컨테이너에 대 한 사용자 위임 SAS 가져오기

다음 코드 예제에서는 컨테이너에 대 한 사용자 위임 SAS를 생성 하는 방법을 보여 줍니다.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Sas.cs" id="Snippet_GetUserDelegationSasContainer":::

다음 예제에서는 시뮬레이트된 클라이언트 응용 프로그램에서 이전 예제에서 만든 사용자 위임 SAS를 테스트 합니다. SAS가 유효한 경우 클라이언트 응용 프로그램은 blob의 내용을 읽을 수 있습니다. SAS가 유효 하지 않은 경우 (예: 만료 된 경우) Azure Storage 오류 코드 403 (사용할 수 없음)을 반환 합니다.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Sas.cs" id="Snippet_ListBlobsWithSasAsync":::

[!INCLUDE [storage-blob-dotnet-resources-include](../../../includes/storage-blob-dotnet-resources-include.md)]

## <a name="see-also"></a>참고 항목

- [SAS (공유 액세스 서명)를 사용 하 여 Azure Storage 리소스에 대 한 제한 된 액세스 권한 부여](../common/storage-sas-overview.md)
- [사용자 위임 키 가져오기 작업](/rest/api/storageservices/get-user-delegation-key)
- [사용자 위임 SAS (REST API) 만들기](/rest/api/storageservices/create-user-delegation-sas)
