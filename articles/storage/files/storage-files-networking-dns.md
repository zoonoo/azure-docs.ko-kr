---
title: Azure Files에 대한 DNS 전달 구성 | Microsoft Docs
description: Azure Files에 대 한 DNS 전달을 구성 하는 방법에 대해 알아봅니다.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 3/19/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 4b5ce48cf15c890fbdb3dfd90d9d0ab922a2f4b2
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91320271"
---
# <a name="configuring-dns-forwarding-for-azure-files"></a>Azure Files에 대한 DNS 전달 구성
Azure Files를 사용하면 파일 공유를 포함하는 스토리지 계정의 프라이빗 엔드포인트를 만들 수 있습니다. 프라이빗 엔드포인트는 다양한 애플리케이션에 유용하지만, 온-프레미스 네트워크에서 프라이빗 피어링을 사용하는 VPN 또는 ExpressRoute 연결을 통해 Azure 파일 공유에 연결할 때 특히 유용합니다. 

스토리지 계정에 대한 연결이 네트워크 터널을 통해 이동하려면 스토리지 계정의 FQDN(정규화된 도메인 이름)이 프라이빗 엔드포인트의 개인 IP 주소를 확인해야 합니다. 이를 위해서는 스토리지 엔드포인트 접미사(퍼블릭 클라우드 지역은 `core.windows.net`)를 가상 네트워크 내에서 액세스할 수 있는 Azure 프라이빗 DNS 서비스에 전달해야 합니다. 이 가이드에서는 스토리지 계정의 프라이빗 엔드포인트 IP 주소를 적절하게 확인하도록 DNS 전달을 설정하고 구성하는 방법을 보여줍니다.

이 문서에 설명된 단계를 완료하기 전에 [Azure Files 배포에 대한 계획](storage-files-planning.md) 및 [Azure Files 네트워킹 고려 사항](storage-files-networking-overview.md)을 읽어보는 것이 좋습니다.

## <a name="overview"></a>개요
Azure Files는 Azure 파일 공유에 액세스하기 위한 다음과 같은 두 가지 기본 유형의 엔드포인트를 제공합니다. 
- 퍼블릭 엔드포인트 - 공용 IP 주소를 사용하며 전 세계 어디서나 액세스할 수 있습니다.
- 프라이빗 엔드포인트 - 가상 네트워크 내에 존재하며 해당 가상 네트워크의 주소 공간 내에서 개인 IP 주소를 사용합니다.

퍼블릭 엔드포인트와 프라이빗 엔드포인트는 Azure 스토리지 계정에 있습니다. 스토리지 계정은 여러 파일 공유뿐만 아니라 다른 스토리지 리소스(예: Blob 컨테이너 또는 큐)도 배포할 수 있는 공유 스토리지 풀을 나타내는 관리 구조입니다.

모든 스토리지 계정에는 FQDN(정규화된 도메인 이름)이 있습니다. 퍼블릭 클라우드 지역의 경우 이 FQDN은 `storageaccount.file.core.windows.net` 패턴을 따르며, 여기서 `storageaccount`는 스토리지 계정의 이름입니다. SMB를 사용하여 워크스테이션에 공유를 탑재하는 경우처럼 이 이름을 요청하면 운영 체제에서는 DNS 조회를 수행하여 정규화된 도메인 이름을 SMB 요청을 보내는 데 사용할 수 있는 IP 주소로 확인합니다.

기본적으로 `storageaccount.file.core.windows.net`은 퍼블릭 엔드포인트의 IP 주소를 확인합니다. 스토리지 계정의 퍼블릭 엔드포인트는 다른 여러 스토리지 계정의 퍼블릭 엔드포인트를 호스팅하는 Azure 스토리지 클러스터에 호스팅됩니다. 프라이빗 엔드포인트를 만들 때 프라이빗 DNS 영역은 추가된 가상 네트워크에 연결되며, CNAME 레코드는 스토리지 계정 프라이빗 엔드포인트의 개인 IP 주소에 대한 A 레코드 항목에 `storageaccount.file.core.windows.net`을 매핑합니다. 이를 통해 가상 네트워크 내에서 `storageaccount.file.core.windows.net` FQDN을 사용하여 프라이빗 엔드포인트의 IP 주소를 확인할 수 있습니다.

우리의 최종 목표는 온-프레미스에서 VPN 또는 ExpressRoute 연결과 같은 네트워크 터널을 사용하여 스토리지 계정 내에 호스팅되는 Azure 파일 공유에 액세스하는 것이므로, Azure Files 서비스에 대한 요청을 Azure 프라이빗 DNS 서비스에 전달하도록 온-프레미스 DNS 서버를 구성해야 합니다. 이렇게 하려면 `*.core.windows.net`(또는 US Government, 독일 또는 중국 지역 클라우드의 적절한 스토리지 엔드포인트 접미사)을 Azure 가상 네트워크 내에 호스팅되는 DNS 서버에 *조건부로 전달*하도록 설정해야 합니다 그러면 이 DNS 서버는 요청을 Azure의 프라이빗 DNS 서비스에 재귀적으로 전달하고, 서비스에서는 스토리지 계정의 정규화된 도메인 이름을 적절한 개인 IP 주소로 확인합니다.

Azure Files에 대한 DNS 전달을 구성하려면 요청을 전달할 DNS 서버를 호스팅하는 가상 머신을 실행해야 하는데, 이 작업은 가상 네트워크 내부에 호스팅되는 모든 Azure 파일 공유에 대해 한 번만 수행하면 됩니다. 이것은 Azure Files에만 적용되는 요구 사항이 아닙니다. 온-프레미스에서 액세스하려는 프라이빗 엔드포인트를 지원하는 모든 Azure 서비스는 이 가이드에서 구성하는 DNS 전달을 사용할 수 있습니다. 여기에는 Azure Blob 스토리지, SQL Azure, Cosmos DB 등이 포함됩니다. 

이 가이드에서는 Azure 스토리지 엔드포인트에 대한 DNS 전달을 구성하는 단계를 보여줍니다. 따라서 Azure Files 외에도 다른 모든 Azure 스토리지 서비스(Azure Blob storage, Azure Table storage, Azure Queue storage 등)에 대한 DNS 이름 확인 요청이 Azure의 프라이빗 DNS 서비스로 전달됩니다. 원하는 경우 다른 Azure 서비스에 대한 엔드포인트를 더 추가할 수 있습니다. 또한 온-프레미스 DNS 서버로 다시 DNS를 전달하도록 구성되며, 가상 네트워크(예: DFS-N 서버) 내의 클라우드 리소스가 온-프레미스 머신 이름을 확인할 수 있습니다. 

## <a name="prerequisites"></a>사전 요구 사항
Azure Files로 DNS 전달을 설정하려면 먼저 다음 단계를 완료해야 합니다.

- 탑재하려는 Azure 파일 공유가 포함된 스토리지 계정. 스토리지 계정 및 Azure 파일 공유를 만드는 방법을 알아보려면 [Azure 파일 공유 만들기](storage-how-to-create-file-share.md)를 참조하세요.
- 스토리지 계정의 프라이빗 엔드포인트. Azure Files의 프라이빗 엔드포인트를 만드는 방법을 알아보려면 [프라이빗 엔드포인트 만들기](storage-files-networking-endpoints.md#create-a-private-endpoint)를 참조하세요.
- [최신 버전](https://docs.microsoft.com/powershell/azure/install-az-ps)의 Azure PowerShell 모듈

> [!Important]  
> 이 가이드에서는 여러분이 온-프레미스 환경의 Windows Server 내에서 DNS 서버를 사용 중이라고 가정합니다. 이 가이드에서 설명하는 모든 단계는 Windows DNS 서버뿐 아니라 모든 DNS 서버에서 수행할 수 있습니다.

## <a name="manually-configuring-dns-forwarding"></a>수동으로 DNS 전달 구성
Azure 가상 네트워크 내에 DNS 서버가 이미 있는 경우 또는 조직에서 사용하는 방법에 따라 자체 가상 머신을 DNS 서버로 배포하려는 경우에는 기본 제공 DNS 서버 PowerShell cmdlet을 사용하여 수동으로 DNS를 구성하면 됩니다.

온-프레미스 DNS 서버에서 `Add-DnsServerConditionalForwarderZone`을 사용하여 조건부 전달자를 만듭니다. 이 조건부 전달자가 트래픽을 Azure에 적절하게 전달하려면 모든 온-프레미스 DNS 서버에 이 조건부 전달자를 배포해야 합니다. `<azure-dns-server-ip>`를 환경에 맞는 적절한 IP 주소로 바꿔야 합니다.

```powershell
$vnetDnsServers = "<azure-dns-server-ip>", "<azure-dns-server-ip>"

$storageAccountEndpoint = Get-AzContext | `
    Select-Object -ExpandProperty Environment | `
    Select-Object -ExpandProperty StorageEndpointSuffix

Add-DnsServerConditionalForwarderZone `
        -Name $storageAccountEndpoint `
        -MasterServers $vnetDnsServers
```

또한 스토리지 계정 DNS 영역에 대한 요청이 (예약된 IP 주소 `168.63.129.16`이 앞에 붙는) Azure 프라이빗 DNS 서비스에 전달되도록 Azure 가상 네트워크 내의 DNS 서버에 전달자를 배치해야 합니다. (다른 PowerShell 세션 내에서 명령을 실행하는 경우 `$storageAccountEndpoint`를 채워야 합니다.)

```powershell
Add-DnsServerConditionalForwarderZone `
        -Name $storageAccountEndpoint `
        -MasterServers "168.63.129.16"
```

## <a name="using-the-azure-files-hybrid-module-to-configure-dns-forwarding"></a>Azure Files 하이브리드 모듈을 사용하여 DNS 전달 구성
DNS 전달을 최대한 쉽게 구성할 수 있도록 Azure Files 하이브리드 모듈이 자동화되었습니다. 이 모듈에서 DNS를 조작하기 위해 제공되는 cmdlet을 사용하면 간편하게 Azure 가상 네트워크에 DNS 서버를 배포하고 이러한 서버에 전달하도록 온-프레미스 DNS 서버를 업데이트할 수 있습니다. 

Azure Files 하이브리드 모듈을 사용한 적이 없는 경우 먼저 워크스테이션에 이 모듈을 설치해야 합니다. 다음과 같이 [최신 버전](https://github.com/Azure-Samples/azure-files-samples/releases)의 Azure Files 하이브리드 PowerShell 모듈을 다운로드합니다.

```PowerShell
# Unzip the downloaded file
Expand-Archive -Path AzFilesHybrid.zip

# Change the execution policy to unblock importing AzFilesHybrid.psm1 module
Set-ExecutionPolicy -ExecutionPolicy Unrestricted

# Navigate to where AzFilesHybrid is unzipped and stored and run to copy the files into your path
.\AzFilesHybrid\CopyToPSPath.ps1 

# Import AzFilesHybrid module
Import-Module -Name AzFilesHybrid
```

DNS 전달 솔루션을 배포하려면 요청을 전달할 Azure 서비스와 DNS 전달자의 실제 배포를 정의하는 두 가지 단계를 수행해야 합니다. 

다음 예제에서는 Azure Files, Azure Blob storage, Azure Table storage 및 Azure Queue storage에 대한 요청을 포함하여 모든 요청을 스토리지 계정으로 전달합니다. 원하는 경우 `New-AzDnsForwardingRuleSet` cmdlet의 `-AzureEndpoints` 매개 변수를 통해 추가 Azure 서비스에 대한 전달을 규칙에 추가할 수 있습니다. `<virtual-network-resource-group>`, `<virtual-network-name>` 및 `<subnet-name>`을 사용자 환경에 적합한 값으로 바꿔야 합니다.

```PowerShell
# Create a rule set, which defines the forwarding rules
$ruleSet = New-AzDnsForwardingRuleSet -AzureEndpoints StorageAccountEndpoint

# Deploy and configure DNS forwarders
New-AzDnsForwarder `
        -DnsForwardingRuleSet $ruleSet `
        -VirtualNetworkResourceGroupName "<virtual-network-resource-group>" `
        -VirtualNetworkName "<virtual-network-name>" `
        -VirtualNetworkSubnetName "<subnet-name>"
```

또한 다음과 같은 여러 추가 매개 변수를 제공하는 것이 더 좋을 수도/꼭 제공해야 할 수도 있습니다.

| 매개 변수 이름 | Type | Description |
|----------------|------|-------------|
| `DnsServerResourceGroupName` | `string` | 기본적으로 DNS 서버는 가상 네트워크와 동일한 리소스 그룹에 배포됩니다. 이를 원하지 않는 경우 이 매개 변수를 사용하여 DNS 서버를 배포할 대체 리소스 그룹을 선택할 수 있습니다. |
| `DnsForwarderRootName` | `string` | 기본적으로 Azure에 배포된 DNS 서버의 이름은 `DnsFwder-*` 형식이며, 여기서 별표는 열거자로 채워집니다. 이 매개 변수는 해당 이름의 루트(예: `DnsFwder`)를 변경합니다. |
| `VmTemporaryPassword` | `SecureString` | 기본적으로 VM이 도메인에 가입되기 전에는 임시 기본 계정의 암호가 임의로 선택됩니다. 도메인에 가입된 후에는 기본 계정이 사용되지 않습니다. |
| `DomainToJoin` | `string` | DNS VM을 가입할 도메인입니다. 기본적으로 이 도메인은 cmdlet을 실행하는 컴퓨터의 도메인에 따라 선택됩니다. |
| `DnsForwarderRedundancyCount` | `int` | 가상 네트워크에 배포할 DNS VM의 수입니다. 기본적으로 `New-AzDnsForwarder`는 중복성을 보장하기 위해 Azure 가상 네트워크와 가용성 집합에 두 개의 DNS 서버를 배포합니다. 이 숫자는 원하는 대로 수정할 수 있습니다. |
| `OnPremDnsHostNames` | `HashSet<string>` | 전달자를 만들기 위해 수동으로 지정한 온-프레미스 DNS 호스트 이름의 목록입니다. 이 매개 변수는 DNS 이름을 수동으로 지정한 클라이언트 범위가 있는 경우처럼 일부 온-프레미스 DNS 서버에만 전달자를 적용하려는 경우에 유용합니다. |
| `Credential` | `PSCredential` | DNS 서버를 업데이트할 때 사용할 자격 증명입니다. 로그인에 사용한 사용자 계정에 DNS 설정을 수정할 권한이 없는 경우에 유용합니다. |
| `SkipParentDomain` | `SwitchParameter` | 기본적으로 DNS 전달자는 환경의 최상위 수준 도메인에 적용됩니다. 예를 들어 `northamerica.corp.contoso.com`이 `corp.contoso.com`의 자식 도메인인 경우 `corp.contoso.com`과 연결된 DNS 서버에 대한 전달자가 만들어집니다. 이 매개 변수를 사용하면 `northamerica.corp.contoso.com`에 전달자가 만들어집니다. |

## <a name="confirm-dns-forwarders"></a>DNS 전달자 확인
DNS 전달자가 성공적으로 적용되었는지 테스트하기 전에, `Clear-DnsClientCache`를 사용하여 로컬 워크스테이션에서 DNS 캐시를 지우는 것이 좋습니다. 스토리지 계정의 정규화된 도메인 이름을 성공적으로 확인할 수 있는지 테스트하려면 `Resolve-DnsName` 또는 `nslookup`을 사용합니다.

```powershell
# Replace storageaccount.file.core.windows.net with the appropriate FQDN for your storage account.
# Note the proper suffix (core.windows.net) depends on the cloud your deployed in.
Resolve-DnsName -Name storageaccount.file.core.windows.net
```

확인된 IP 주소가 스토리지 계정의 IP 주소와 일치하면 이름 확인이 성공한 것입니다.

```Output
Name                              Type   TTL   Section    NameHost
----                              ----   ---   -------    --------
storageaccount.file.core.windows. CNAME  29    Answer     csostoracct.privatelink.file.core.windows.net
net

Name       : storageaccount.privatelink.file.core.windows.net
QueryType  : A
TTL        : 1769
Section    : Answer
IP4Address : 192.168.0.4
```

또한 VPN 또는 ExpressRoute 연결을 이미 설정한 경우 `Test-NetConnection`을 사용하여 스토리지 계정에 TCP를 연결할 수 있는지 확인할 수 있습니다.

```PowerShell
Test-NetConnection -ComputerName storageaccount.file.core.windows.net -CommonTCPPort SMB
```

## <a name="see-also"></a>참고 항목
- [Azure 파일 배포에 대한 계획](storage-files-planning.md)
- [Azure Files 네트워킹 고려 사항](storage-files-networking-overview.md)
- [Azure Files 네트워크 엔드포인트 구성](storage-files-networking-endpoints.md)