---
title: 클라이언트 애플리케이션에 대한 TLS(전송 계층 보안) 구성
titleSuffix: Azure Storage
description: 최소 버전의 TLS(전송 계층 보안)를 사용하여 Azure Storage와 통신하도록 클라이언트 애플리케이션을 구성합니다.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 07/08/2020
ms.author: tamram
ms.reviewer: fryu
ms.subservice: common
ms.custom: devx-track-csharp
ms.openlocfilehash: 37b8c79df5b208feea185292fa09c323b64fa27d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "89001810"
---
# <a name="configure-transport-layer-security-tls-for-a-client-application"></a>클라이언트 애플리케이션에 대한 TLS(전송 계층 보안) 구성

보안을 위해 Azure Storage 계정에서는 클라이언트가 요청을 보낼 때 최소 버전의 TLS(전송 계층 보안)를 사용할 것을 요구할 수 있습니다. 클라이언트에서 최소 필수 버전보다 낮은 버전의 TLS를 사용하는 경우 Azure Storage에 대한 호출이 실패합니다. 예를 들어 스토리지 계정에 TLS 1.2가 필요한 경우 TLS 1.1을 사용하는 클라이언트에서 보낸 요청은 실패합니다.

이 문서에서는 특정 버전의 TLS를 사용하도록 클라이언트 애플리케이션을 구성하는 방법을 설명합니다. Azure Storage 계정에 대해 최소 필수 버전의 TLS를 구성하는 방법에 대한 자세한 내용은 [스토리지 계정에 대한 TLS(전송 계층 보안)의 최소 필수 버전 구성](transport-layer-security-configure-minimum-version.md)을 참조하세요.

## <a name="configure-the-client-tls-version"></a>클라이언트 TLS 버전 구성

클라이언트에서 특정 버전의 TLS를 사용하여 요청을 보내려면 운영 체제에서 해당 버전을 지원해야 합니다.

다음 예제에서는 PowerShell 또는 .NET에서 클라이언트의 TLS 버전을 1.2로 설정하는 방법을 보여 줍니다. 클라이언트에서 사용하는 .NET Framework는 TLS 1.2를 지원해야 합니다. 자세한 내용은 [TLS 1.2에 대한 지원](/dotnet/framework/network-programming/tls#support-for-tls-12)을 참조하세요.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

다음 샘플에서는 PowerShell 클라이언트에서 TLS 1.2를 사용하는 방법을 보여줍니다.

```powershell
# Set the TLS version used by the PowerShell client to TLS 1.2.
[System.Net.ServicePointManager]::SecurityProtocol = [System.Net.SecurityProtocolType]::Tls12;

# Create a new container.
$storageAccount = Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName
$ctx = $storageAccount.Context
New-AzStorageContainer -Name "sample-container" -Context $ctx
```

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

다음 샘플에서는 Azure Storage 클라이언트 라이브러리 버전 12를 사용하여 .NET 클라이언트에서 TLS 1.2를 사용하도록 설정하는 방법을 보여 줍니다.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Networking.cs" id="Snippet_ConfigureTls12":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

다음 샘플에서는 Azure Storage 클라이언트 라이브러리 버전 11을 사용하여 .NET 클라이언트에서 TLS 1.2를 사용하도록 설정하는 방법을 보여 줍니다.

```csharp
static void EnableTls12()
{
    // Enable TLS 1.2 before connecting to Azure Storage
    System.Net.ServicePointManager.SecurityProtocol = System.Net.SecurityProtocolType.Tls12;

    // Add your connection string here.
    string connectionString = "";

    // Connect to Azure Storage and create a new container.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(connectionString);
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    CloudBlobContainer container = blobClient.GetContainerReference("sample-container");
    container.CreateIfNotExists();
}
```

---

## <a name="verify-the-tls-version-used-by-a-client"></a>클라이언트에서 사용하는 TLS 버전 확인

지정된 버전의 TLS가 클라이언트에서 요청을 전송하는 데 사용되었는지 확인할 때 [Fiddler](https://www.telerik.com/fiddler) 또는 유사한 도구를 사용할 수 있습니다. Fiddler를 열어 클라이언트 네트워크 트래픽 캡처를 시작한 다음, 이전 섹션의 예제 중 하나를 실행합니다. 다음 그림에서처럼 Fiddler 추적을 확인하여 올바른 버전의 TLS가 요청을 보내는 데 사용되었는지 확인합니다.

:::image type="content" source="media/transport-layer-security-configure-client-version/fiddler-trace-tls-version.png" alt-text="요청에 사용되는 TLS 버전을 나타내는 Fiddler 추적을 보여 주는 스크린샷":::

## <a name="next-steps"></a>다음 단계

- [스토리지 계정에 대한 TLS(전송 계층 보안)의 최소 필수 버전 구성](transport-layer-security-configure-minimum-version.md)
- [Blob Storage에 대한 보안 권장 사항](../blobs/security-recommendations.md)
