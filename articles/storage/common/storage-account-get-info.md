---
title: .NET을 사용하여 스토리지 계정 유형 및 SKU 이름 가져오기
titleSuffix: Azure Storage
description: .NET 클라이언트 라이브러리를 사용하여 Azure Storage 계정 유형 및 SKU 이름을 가져오는 방법을 알아봅니다.
services: storage
author: twooley
ms.author: twooley
ms.date: 11/12/2020
ms.service: storage
ms.subservice: common
ms.topic: how-to
ms.custom: devx-track-csharp
ms.openlocfilehash: 96c77291a1c540e2ff95fb911b2321e78f20e604
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/26/2021
ms.locfileid: "110478847"
---
# <a name="get-storage-account-type-and-sku-name-with-net"></a>.NET을 사용하여 스토리지 계정 유형 및 SKU 이름 가져오기

이 문서에서는 [.NET용 Azure Storage 클라이언트 라이브러리](/dotnet/api/overview/azure/storage)를 사용하여 Blob에 대한 Azure Storage 계정 유형 및 SKU 이름을 가져오는 방법을 보여줍니다.

## <a name="about-account-type-and-sku-name"></a>계정 유형 및 SKU 이름 정보

**계정 유형**: 올바른 계정 유형에는 `BlobStorage`, `BlockBlobStorage`, `FileStorage`, `Storage` 및 `StorageV2`가 포함됩니다. [Azure 스토리지 계정 개요](storage-account-overview.md)에는 다양한 스토리지 계정에 대한 설명을 포함하여 자세한 정보가 있습니다.

**SKU 이름**: 유효한 SKU 이름에는 `Premium_LRS`, `Premium_ZRS`, `Standard_GRS`, `Standard_GZRS`, `Standard_LRS`, `Standard_RAGRS`, `Standard_RAGZRS` 및 `Standard_ZRS`가 포함됩니다. SKU 이름은 대/소문자를 구분하며, [SkuName 클래스](/dotnet/api/microsoft.azure.management.storage.models.skuname)의 문자열 필드입니다.

## <a name="retrieve-account-information"></a>계정 정보 검색

다음 코드 예제에서는 읽기 전용 계정 속성을 검색하고 표시합니다.

# <a name="net-v12-sdk"></a>[.NET v12 SDK](#tab/dotnet)

Blob과 연결된 스토리지 계정 유형 및 SKU 이름을 가져오려면 [GetAccountInfo](/dotnet/api/azure.storage.blobs.blobserviceclient.getaccountinfo) 또는 [GetAccountInfoAsync](/dotnet/api/azure.storage.blobs.blobserviceclient.getaccountinfoasync) 메서드를 호출합니다.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Account.cs" id="Snippet_GetAccountInfo":::

# <a name="net-v11-sdk"></a>[.NET v11 SDK](#tab/dotnet11)

Blob와 연결된 스토리지 계정 유형 및 SKU 이름을 가져오려면 [GetAccountProperties](/dotnet/api/microsoft.azure.storage.blob.cloudblob.getaccountproperties) 또는 [GetAccountPropertiesAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.getaccountpropertiesasync) 메서드를 호출합니다.

```csharp
private static async Task GetAccountInfoAsync(CloudBlob blob)
{
    try
    {
        // Get the blob's storage account properties.
        AccountProperties acctProps = await blob.GetAccountPropertiesAsync();

        // Display the properties.
        Console.WriteLine("Account properties");
        Console.WriteLine("  AccountKind: {0}", acctProps.AccountKind);
        Console.WriteLine("      SkuName: {0}", acctProps.SkuName);
    }
    catch (StorageException e)
    {
        Console.WriteLine("HTTP error code {0}: {1}",
                            e.RequestInformation.HttpStatusCode,
                            e.RequestInformation.ErrorCode);
        Console.WriteLine(e.Message);
        Console.ReadLine();
    }
}
```

---

[!INCLUDE [storage-blob-dotnet-resources-include](../../../includes/storage-blob-dotnet-resources-include.md)]

## <a name="next-steps"></a>다음 단계

[Azure Portal](https://portal.azure.com) 및 Azure REST API를 통해 스토리지 계정에서 수행할 수 있는 다른 작업에 대해 알아봅니다.

- [계정 정보 가져오기 작업(REST)](/rest/api/storageservices/get-account-information)
