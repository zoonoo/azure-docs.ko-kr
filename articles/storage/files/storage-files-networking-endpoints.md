---
title: Azure Files 네트워크 엔드포인트 구성 | Microsoft Docs
description: Azure Files의 네트워킹 옵션에 대한 개요입니다.
author: roygara
ms.service: storage
ms.topic: overview
ms.date: 3/19/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: e58a84d8c001ad273cfa14ffb871aaea71bc468b
ms.sourcegitcommit: 813f7126ed140a0dff7658553a80b266249d302f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/06/2020
ms.locfileid: "84465029"
---
# <a name="configuring-azure-files-network-endpoints"></a>Azure Files 네트워크 엔드포인트 구성
Azure Files는 Azure 파일 공유에 액세스하기 위한 다음과 같은 두 가지 기본 유형의 엔드포인트를 제공합니다. 
- 퍼블릭 엔드포인트 - 공용 IP 주소를 사용하며 전 세계 어디서나 액세스할 수 있습니다.
- 프라이빗 엔드포인트 - 가상 네트워크 내에 존재하며 해당 가상 네트워크의 주소 공간 내에서 개인 IP 주소를 사용합니다.

퍼블릭 엔드포인트와 프라이빗 엔드포인트는 Azure 스토리지 계정에 있습니다. 스토리지 계정은 여러 파일 공유뿐만 아니라 다른 스토리지 리소스(예: Blob 컨테이너 또는 큐)도 배포할 수 있는 공유 스토리지 풀을 나타내는 관리 구조입니다.

이 문서에서는 Azure 파일 공유에 직접 액세스하기 위한 스토리지 계정의 엔드포인트를 구성하는 방법을 중점적으로 설명합니다. 이 문서에 제공된 세부 정보 중 대부분은 Azure 파일 동기화가 스토리지 계정의 퍼블릭 및 프라이빗 엔드포인트와 상호 작용하는 방식에도 적용됩니다. 그러나 Azure 파일 동기화 배포 시 네트워킹 고려 사항에 대한 자세한 내용은 [Azure 파일 동기화 프록시 및 방화벽 설정 구성](storage-sync-files-firewall-and-proxy.md)을 참조하세요.

이 방법 가이드를 읽기 전에 [Azure Files 네트워킹 고려 사항](storage-files-networking-overview.md)을 읽어보는 것이 좋습니다.

## <a name="prerequisites"></a>필수 구성 요소
- 이 문서에서는 독자들이 이미 Azure 구독을 만들었다고 가정합니다. Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.
- 이 문서에서는 독자들이 온-프레미스에서 연결하려는 스토리지 계정에 Azure 파일 공유를 이미 만들었다고 가정합니다. Azure 파일 공유를 만드는 방법을 알아보려면 [Azure 파일 공유 만들기](storage-how-to-create-file-share.md)를 참조하세요.
- Azure PowerShell을 사용하려면 [최신 버전을 설치](https://docs.microsoft.com/powershell/azure/install-az-ps)하세요.
- Azure CLI를 사용하려면 [최신 버전을 설치](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)하세요.

## <a name="create-a-private-endpoint"></a>프라이빗 엔드포인트 만들기
스토리지 계정의 프라이빗 엔드포인트를 만들면 다음 Azure 리소스가 배포됩니다.

- **프라이빗 엔드포인트**: 스토리지 계정의 프라이빗 엔드포인트를 나타내는 Azure 리소스입니다. 스토리지 계정과 네트워크 인터페이스를 연결하는 리소스라고 생각하시면 됩니다.
- **NIC(네트워크 인터페이스)** : 지정된 가상 네트워크/서브넷 내에서 개인 IP 주소를 유지 관리하는 네트워크 인터페이스입니다. 가상 머신을 배포할 때 배포되는 것과 정확히 동일한 리소스이지만, VM에 할당되는 것이 아니라 프라이빗 엔드포인트의 소유입니다.
- **프라이빗 DNS 영역**: 이전에 이 가상 네트워크에 대한 프라이빗 엔드포인트를 한 번도 배포하지 않은 경우 가상 네트워크에 대한 새 프라이빗 DNS 영역이 배포됩니다. 이 DNS 영역의 스토리지 계정에 대한 DNS A 레코드도 생성됩니다. 이 가상 네트워크에 프라이빗 엔드포인트를 이미 배포한 경우 스토리지 계정에 대한 새 A 레코드가 기존 DNS 영역에 추가됩니다. DNS 영역을 배포하는 것은 선택 사항이지만 배포할 것을 적극 권장하며, AD 서비스 주체 또는 FileREST API를 사용하여 Azure 파일 공유를 탑재하는 경우에는 필수입니다.

> [!Note]  
> 이 문서에서는 `core.windows.net` Azure 퍼블릭 지역에 대한 스토리지 계정 DNS 접미사를 사용합니다. 이는 Azure US Government 클라우드 및 Azure 중국 클라우드와 같은 Azure 소버린 클라우드에도 적용되며, 사용자 환경에 적합한 접미사로 바꾸기만 하면 됩니다. 

# <a name="portal"></a>[포털](#tab/azure-portal)
[!INCLUDE [storage-files-networking-endpoints-private-portal](../../../includes/storage-files-networking-endpoints-private-portal.md)]

가상 네트워크 내에 가상 머신이 있거나 [Azure 파일용 DNS 전달 구성](storage-files-networking-dns.md)에 설명된 대로 DNS 전달을 구성한 경우, PowerShell, 명령줄 또는 터미널에서 다음 명령을 실행하여 프라이빗 엔드포인트가 올바르게 설정되었는지 테스트할 수 있습니다(Windows, Linux 또는 macOS에서 작동). 다음과 같이 `<storage-account-name>`을 적절한 스토리지 계정 이름으로 바꿔야 합니다.

```
nslookup <storage-account-name>.file.core.windows.net
```

모든 것이 정상적으로 작동하는 경우 다음과 같은 출력이 표시됩니다. 여기서 `192.168.0.5`는 가상 네트워크의 프라이빗 엔드포인트 개인 IP 주소입니다(Windows에 표시되는 출력).

```Output
Server:  UnKnown
Address:  10.2.4.4

Non-authoritative answer:
Name:    storageaccount.privatelink.file.core.windows.net
Address:  192.168.0.5
Aliases:  storageaccount.file.core.windows.net
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
[!INCLUDE [storage-files-networking-endpoints-private-powershell](../../../includes/storage-files-networking-endpoints-private-powershell.md)]

가상 네트워크 내에 가상 머신이 있거나 [Azure 파일용 전달 DNS 구성](storage-files-networking-dns.md)에 설명된 대로 DNS 전달을 구성한 경우, 다음 명령을 사용하여 프라이빗 엔드포인트가 올바르게 설정되었는지 테스트할 수 있습니다.

```PowerShell
$storageAccountHostName = [System.Uri]::new($storageAccount.PrimaryEndpoints.file) | `
    Select-Object -ExpandProperty Host

Resolve-DnsName -Name $storageAccountHostName
```

모든 것이 정상적으로 작동하는 경우 다음과 같은 출력이 표시됩니다. 여기서 `192.168.0.5`는 가상 네트워크의 프라이빗 엔드포인트 개인 IP 주소입니다.

```Output
Name                             Type   TTL   Section    NameHost
----                             ----   ---   -------    --------
storageaccount.file.core.windows CNAME  60    Answer     storageaccount.privatelink.file.core.windows.net
.net

Name       : storageaccount.privatelink.file.core.windows.net
QueryType  : A
TTL        : 600
Section    : Answer
IP4Address : 192.168.0.5
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
[!INCLUDE [storage-files-networking-endpoints-private-cli](../../../includes/storage-files-networking-endpoints-private-cli.md)]

가상 네트워크 내에 가상 머신이 있거나 [Azure 파일용 전달 DNS 구성](storage-files-networking-dns.md)에 설명된 대로 DNS 전달을 구성한 경우, 다음 명령을 사용하여 프라이빗 엔드포인트가 올바르게 설정되었는지 테스트할 수 있습니다.

```bash
httpEndpoint=$(az storage account show \
        --resource-group $storageAccountResourceGroupName \
        --name $storageAccountName \
        --query "primaryEndpoints.file" | \
    tr -d '"')

hostName=$(echo $httpEndpoint | cut -c7-$(expr length $httpEndpoint) | tr -d "/")
nslookup $hostName
```

모든 것이 정상적으로 작동하는 경우 다음과 같은 출력이 표시됩니다. 여기서 `192.168.0.5`는 가상 네트워크의 프라이빗 엔드포인트 개인 IP 주소입니다. `privatelink` 경로 대신 파일 공유를 탑재하려면 storageaccount.file.core.windows.net을 계속 사용해야 합니다.

```Output
Server:         127.0.0.53
Address:        127.0.0.53#53

Non-authoritative answer:
storageaccount.file.core.windows.net      canonical name = storageaccount.privatelink.file.core.windows.net.
Name:   storageaccount.privatelink.file.core.windows.net
Address: 192.168.0.5
```

---

## <a name="restrict-access-to-the-public-endpoint"></a>퍼블릭 엔드포인트에 대한 액세스 제한
스토리지 계정 방화벽 설정을 사용하여 퍼블릭 엔드포인트에 대한 액세스를 제한할 수 있습니다. 일반적으로 스토리지 계정에 대한 대부분의 방화벽 정책은 하나 이상의 가상 네트워크에 대한 네트워킹 액세스를 제한합니다. 스토리지 계정에 대한 액세스를 가상 네트워크로 제한하는 두 가지 방법은 다음과 같습니다.

- [스토리지 계정에 대한 하나 이상의 프라이빗 엔드포인트를 만들고](#create-a-private-endpoint), 모든 액세스를 퍼블릭 엔드포인트로 제한합니다. 이렇게 하면 원하는 가상 네트워크 내에서 시작된 트래픽만 스토리지 계정 내의 Azure 파일 공유에 액세스할 수 있습니다.
- 퍼블릭 엔드포인트를 하나 이상의 가상 네트워크로 제한합니다. 이는 *서비스 엔드포인트*라는 가상 네트워크의 기능을 사용하여 작동합니다. 서비스 엔드포인트를 통해 스토리지 계정으로의 트래픽을 제한하는 경우에도 공용 IP 주소를 통해 스토리지 계정에 액세스하게 됩니다.

### <a name="disable-access-to-the-public-endpoint"></a>퍼블릭 엔드포인트에 대한 액세스 제한
퍼블릭 엔드포인트에 대한 모든 액세스가 제한된 경우에도 프라이빗 엔드포인트를 통해 스토리지 계정에 액세스할 수 있습니다. 그렇지 않으면 스토리지 계정의 퍼블릭 엔드포인트에 대한 유효한 요청이 거부됩니다. 

# <a name="portal"></a>[포털](#tab/azure-portal)
[!INCLUDE [storage-files-networking-endpoints-public-disable-portal](../../../includes/storage-files-networking-endpoints-public-disable-portal.md)]

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
[!INCLUDE [storage-files-networking-endpoints-public-disable-powershell](../../../includes/storage-files-networking-endpoints-public-disable-powershell.md)]

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
[!INCLUDE [storage-files-networking-endpoints-public-disable-cli](../../../includes/storage-files-networking-endpoints-public-disable-cli.md)]

---

### <a name="restrict-access-to-the-public-endpoint-to-specific-virtual-networks"></a>퍼블릭 엔드포인트에 대한 액세스를 특정 가상 네트워크로 제한
스토리지 계정을 특정 가상 네트워크로 제한하면 지정된 가상 네트워크 내에서 퍼블릭 엔드포인트에 요청하는 것을 허용하는 것입니다. 이는 *서비스 엔드포인트*라는 가상 네트워크의 기능을 사용하여 작동합니다. 이 방법에 프라이빗 엔드포인트를 사용해도 되고 사용하지 않아도 됩니다.

# <a name="portal"></a>[포털](#tab/azure-portal)
[!INCLUDE [storage-files-networking-endpoints-public-restrict-portal](../../../includes/storage-files-networking-endpoints-public-restrict-portal.md)]

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
[!INCLUDE [storage-files-networking-endpoints-public-restrict-powershell](../../../includes/storage-files-networking-endpoints-public-restrict-powershell.md)]

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
[!INCLUDE [storage-files-networking-endpoints-public-restrict-cli](../../../includes/storage-files-networking-endpoints-public-restrict-cli.md)]

---

## <a name="see-also"></a>참고 항목
- [Azure Files 네트워킹 고려 사항](storage-files-networking-overview.md)
- [Azure Files에 대한 DNS 전달 구성](storage-files-networking-dns.md)
- [Azure Files에 대한 S2S VPN 구성](storage-files-configure-s2s-vpn.md)