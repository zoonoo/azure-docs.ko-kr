---
title: Azure Storage 클라이언트에 대한 보안 TLS 사용 | Microsoft Docs
description: Azure Storage의 클라이언트에서 TLS 1.2를 사용하는 방법을 알아봅니다.
services: storage
author: fhryo-msft
ms.service: storage
ms.topic: article
ms.date: 06/25/2018
ms.author: fryu
ms.component: common
ms.openlocfilehash: 47cc38226799c5fd3afa2e13e7731a7683b736ba
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/06/2018
ms.locfileid: "39527520"
---
# <a name="enable-secure-tls-for-azure-storage-client"></a>Azure Storage 클라이언트에 대한 보안 TLS 사용

TLS(전송 계층 보안) 및 SSL(Secure Sockets Layer)은 컴퓨터 네트워크를 통해 통신 보안을 제공하는 암호화 프로토콜입니다. SSL 1.0, 2.0 및 3.0은 취약하므로, RFC에 의해 금지되었습니다. 안전하지 않은 블록 암호(DES CBC 및 RC2 CBC) 및 스트림 암호(RC4)를 사용하는 경우 TLS 1.0은 안전하지 않습니다. PCI Council은 최신 TLS 버전으로 마이그레이션하도록 제안했습니다. 자세한 내용은 [TLS(전송 계층 보안)](https://en.wikipedia.org/wiki/Transport_Layer_Security#SSL_1.0.2C_2.0_and_3.0)을 참조하세요.

Azure Storage는 2015년 이후 SSL 3.0 사용을 중지하고 공용 HTTP 엔드포인트에서 TLS 1.2를 사용하지만 TLS 1.0 및 TLS 1.1도 이전 버전과의 호환성을 위해 지원됩니다.

Azure Storage에 대한 보안 및 준수 연결을 보장하기 위해 Azure Storage 서비스에 요청을 보내기 전에 클라이언트 쪽에서 TLS 1.2 또는 최신 버전을 사용하도록 설정해야 합니다.

## <a name="enable-tls-12-in-net-client"></a>.NET 클라이언트에서 TLS 1.2 사용

TLS 1.2를 협상하는 클라이언트의 경우 OS 및 .NET Framework 버전은 모두 TLS 1.2를 지원해야 합니다. [TLS 1.2에 대한 지원](https://docs.microsoft.com/en-us/dotnet/framework/network-programming/tls#support-for-tls-12)에서 자세한 내용을 참조하세요.

다음 샘플에서는 .NET 클라이언트에서 TLS 1.2를 사용하는 방법을 보여줍니다.

```csharp

    static void EnableTls12()
    {
        // Enable TLS 1.2 before connecting to Azure Storage
        System.Net.ServicePointManager.SecurityProtocol = System.Net.SecurityProtocolType.Tls12;

        // Connect to Azure Storage
        CloudStorageAccount storageAccount = CloudStorageAccount.Parse("DefaultEndpointsProtocol=https;AccountName={yourstorageaccount};AccountKey={yourstorageaccountkey};EndpointSuffix=core.windows.net");
        CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

        CloudBlobContainer container = blobClient.GetContainerReference("foo");
        container.CreateIfNotExists();
    }

```

## <a name="enable-tls-12-in-powershell-client"></a>PowerShell 클라이언트에서 TLS 1.2 사용

다음 샘플에서는 PowerShell 클라이언트에서 TLS 1.2를 사용하는 방법을 보여줍니다.

```powershell

# Enable TLS 1.2 before connecting to Azure Storage
[System.Net.ServicePointManager]::SecurityProtocol = [System.Net.SecurityProtocolType]::Tls12;

$resourceGroup = "{YourResourceGropuName}"
$storageAccountName = "{YourStorageAccountNme}"
$prefix = "foo"

# Connect to Azure Storage
$storageAccount = Get-AzureRmStorageAccount -ResourceGroupName $resourceGroup -Name $storageAccountName
$ctx = $storageAccount.Context
$listOfContainers = Get-AzureStorageContainer -Context $ctx -Prefix $prefix
$listOfContainers

```

## <a name="verify-tls-12-connection"></a>TLS 1.2 연결 확인

Fiddler를 사용하여 TLS 1.2를 실제로 사용하는지 확인할 수 있습니다. Fiddler를 열고 클라이언트 네트워크 트래픽을 캡쳐하기 시작한 다음, 위의 샘플을 실행합니다. 그러면 샘플이 만든 연결에서 TLS 버전을 찾을 수 있습니다.

다음 스크린샷은 확인을 위한 샘플입니다.

![Fiddler에서 TLS 버전을 확인하는 스크린샷](./media/storage-security-tls/storage-security-tls-verify-in-fiddler.png)

## <a name="see-also"></a>참고 항목

* [TLS(전송 계층 보안)](https://en.wikipedia.org/wiki/Transport_Layer_Security#SSL_1.0.2C_2.0_and_3.0)
* [TLS의 PCI 규정 준수](https://blog.pcisecuritystandards.org/migrating-from-ssl-and-early-tls)
* [Java 클라이언트에서 TLS 사용](https://www.java.com/en/configure_crypto.html)
