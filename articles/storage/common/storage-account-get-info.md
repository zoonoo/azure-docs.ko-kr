---
title: .NET을 통해 저장소 계정 유형 및 SKU 이름 받기
titleSuffix: Azure Storage
description: .NET 클라이언트 라이브러리를 사용하여 Azure Storage 계정 유형 및 SKU 이름을 얻는 방법을 알아봅니다.
services: storage
author: mhopkins-msft
ms.author: mhopkins
ms.date: 08/06/2019
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.openlocfilehash: 1495ed55c24a0f94b911d60d1db0f32940ea134a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79137061"
---
# <a name="get-storage-account-type-and-sku-name-with-net"></a>.NET을 통해 저장소 계정 유형 및 SKU 이름 받기

이 문서에서는 .NET 에 대한 Azure Storage [클라이언트 라이브러리를](/dotnet/api/overview/azure/storage?view=azure-dotnet)사용하여 Blob에 대한 Azure Storage 계정 유형 및 SKU 이름을 얻는 방법을 보여 주며 있습니다.

계정 정보는 버전 2018-03-28부터 서비스 버전에서 사용할 수 있습니다.

## <a name="about-account-type-and-sku-name"></a>계정 유형 및 SKU 이름 에 대해

**계정 유형**: 유효한 `BlobStorage` `BlockBlobStorage`계정 `FileStorage` `Storage`유형에는 에 대한 이 의 가 포함됩니다. `StorageV2` [Azure 저장소 계정 개요에는](storage-account-overview.md) 다양한 저장소 계정에 대한 설명을 비롯한 추가 정보가 있습니다.

**SKU 이름**: 유효한 `Premium_LRS`SKU 이름에는 `Standard_RAGRS` `Standard_RAGZRS`" `Premium_ZRS` `Standard_GRS` `Standard_ZRS`, " `Standard_GZRS` `Standard_LRS`및 . SKU 이름은 대/소문자를 구분하며 [SkuName 클래스의](/dotnet/api/microsoft.azure.management.storage.models.skuname?view=azure-dotnet)문자열 필드입니다.

## <a name="retrieve-account-information"></a>계정 정보 검색

저장소 계정 유형 및 Blob과 연결된 SKU 이름을 얻으려면 [GetAccountProperties](/dotnet/api/microsoft.azure.storage.blob.cloudblob.getaccountproperties?view=azure-dotnet) 또는 [GetAccountPropertiesAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.getaccountpropertiesasync?view=azure-dotnet) 메서드를 호출합니다.

다음 코드 예제는 읽기 전용 계정 속성을 검색하고 표시합니다.

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

[!INCLUDE [storage-blob-dotnet-resources-include](../../../includes/storage-blob-dotnet-resources-include.md)]

## <a name="next-steps"></a>다음 단계

[Azure 포털](https://portal.azure.com) 및 Azure REST API를 통해 저장소 계정에서 수행할 수 있는 다른 작업에 대해 알아봅니다.

- [계정 정보 작업 받기(REST)](/rest/api/storageservices/get-account-information)
