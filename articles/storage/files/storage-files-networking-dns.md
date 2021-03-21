---
title: Azure Files에 대 한 DNS 전달 구성 Microsoft Docs
description: Azure Files에 대 한 DNS 전달을 구성 하는 방법에 대해 알아봅니다.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 3/19/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 9abe306668a4b20e42e45c498bf85b540dfaaee5
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "94630195"
---
# <a name="configuring-dns-forwarding-for-azure-files"></a>Azure Files에 대한 DNS 전달 구성
Azure Files를 사용 하 여 파일 공유를 포함 하는 저장소 계정에 대 한 개인 끝점을 만들 수 있습니다. 다양 한 응용 프로그램에 유용 하지만 개인 끝점은 개인 피어 링을 사용 하 여 VPN 또는 Express 경로 연결을 사용 하 여 온-프레미스 네트워크에서 Azure 파일 공유에 연결 하는 데 특히 유용 합니다. 

저장소 계정에 대 한 연결이 네트워크 터널을 통해 이동 하려면 저장소 계정의 정규화 된 도메인 이름 (FQDN)이 개인 끝점의 개인 IP 주소를 확인 해야 합니다. 이를 위해서는 `core.windows.net` 가상 네트워크 내에서 액세스할 수 있는 Azure 개인 DNS 서비스로 저장소 끝점 접미사 (공용 클라우드 지역)를 전달 해야 합니다. 이 가이드에서는 저장소 계정의 개인 끝점 IP 주소를 적절 하 게 확인 하기 위해 DNS 전달을 설정 및 구성 하는 방법을 보여 줍니다.

이 문서에 설명 된 단계를 완료 하기 전에 Azure Files 배포 및 [Azure Files 네트워킹 고려 사항](storage-files-networking-overview.md) [에 대 한 계획](storage-files-planning.md) 을 읽어 보는 것이 좋습니다.

## <a name="overview"></a>개요
Azure Files는 Azure 파일 공유에 액세스하기 위한 다음과 같은 두 가지 기본 유형의 엔드포인트를 제공합니다. 
- 퍼블릭 엔드포인트 - 공용 IP 주소를 사용하며 전 세계 어디서나 액세스할 수 있습니다.
- 프라이빗 엔드포인트 - 가상 네트워크 내에 존재하며 해당 가상 네트워크의 주소 공간 내에서 개인 IP 주소를 사용합니다.

퍼블릭 엔드포인트와 프라이빗 엔드포인트는 Azure 스토리지 계정에 있습니다. 스토리지 계정은 여러 파일 공유뿐만 아니라 다른 스토리지 리소스(예: Blob 컨테이너 또는 큐)도 배포할 수 있는 공유 스토리지 풀을 나타내는 관리 구조입니다.

모든 저장소 계정에는 FQDN (정규화 된 도메인 이름)이 있습니다. 공용 클라우드 지역의 경우이 FQDN은의 패턴을 따릅니다 `storageaccount.file.core.windows.net` `storageaccount` . 여기서은 저장소 계정의 이름입니다. SMB를 사용 하 여 워크스테이션에 공유를 탑재 하는 것과 같이이 이름에 대해 요청을 수행 하면 운영 체제에서 DNS 조회를 수행 하 여 정규화 된 도메인 이름을 SMB 요청을 보내는 데 사용할 수 있는 IP 주소로 확인 합니다.

기본적으로는 `storageaccount.file.core.windows.net` 공용 끝점의 IP 주소를 확인 합니다. 저장소 계정에 대 한 공용 끝점은 다른 많은 저장소 계정의 공용 끝점을 호스트 하는 Azure storage 클러스터에서 호스팅됩니다. 개인 끝점을 만들 때 개인 DNS 영역은 추가 된 가상 네트워크에 연결 되며, CNAME 레코드는 `storageaccount.file.core.windows.net` 저장소 계정의 개인 끝점의 개인 IP 주소에 대 한 a 레코드 항목에 매핑됩니다. 이렇게 하면 `storageaccount.file.core.windows.net` 가상 네트워크 내에서 FQDN을 사용 하 여 개인 끝점의 IP 주소를 확인할 수 있습니다.

최종 목표는 VPN 또는 Express 경로 연결과 같은 네트워크 터널을 사용 하 여 온-프레미스에서 저장소 계정 내에서 호스트 되는 Azure 파일 공유에 액세스 하는 것 이므로 온-프레미스 DNS 서버를 구성 하 여 Azure Files 서비스에 대 한 요청을 Azure 개인 DNS 서비스로 전달 해야 합니다. 이렇게 하려면  `*.core.windows.net` Azure 가상 네트워크 내에서 호스트 되는 DNS 서버에 대 한 조건부 전달 (또는 미국 정부, 독일 또는 중국 국가 클라우드의 적절 한 저장소 끝점 접미사)을 설정 해야 합니다. 그런 다음이 DNS 서버는 저장소 계정의 정규화 된 도메인 이름을 적절 한 개인 IP 주소로 확인 하는 Azure의 개인 DNS 서비스로 요청을 재귀적으로 전달 합니다.

Azure Files에 대 한 DNS 전달을 구성 하려면 요청을 전달 하기 위해 DNS 서버를 호스트 하는 가상 컴퓨터를 실행 해야 합니다. 그러나 가상 네트워크 내에서 호스트 되는 모든 Azure 파일 공유에 대해 한 번만 수행 하면 됩니다. 또한 Azure Files에 대 한 배타적 요구 사항은 아닙니다. 온-프레미스에서 액세스 하려는 개인 끝점을 지 원하는 Azure 서비스는이 가이드에서 구성할 DNS 전달 (Azure Blob storage, SQL Azure, Cosmos DB 등)을 사용할 수 있습니다. 

이 가이드는 Azure storage 끝점에 대 한 DNS 전달을 구성 하는 단계를 보여 줍니다. Azure Files 외에도 다른 모든 Azure 저장소 서비스 (Azure Blob storage, Azure Table storage, Azure Queue storage 등)에 대 한 DNS 이름 확인 요청이 Azure의 개인 DNS 서비스로 전달 됩니다. 원하는 경우 다른 Azure 서비스에 대 한 추가 끝점을 추가할 수도 있습니다. 또한 온-프레미스 DNS 서버로 다시 DNS를 전달 하 여 가상 네트워크 (예: DFS-N 서버) 내의 클라우드 리소스에서 온-프레미스 컴퓨터 이름을 확인할 수 있도록 구성 됩니다. 

## <a name="prerequisites"></a>필수 구성 요소
Azure Files에 대 한 DNS 전달을 설정 하려면 먼저 다음 단계를 완료 해야 합니다.

- 탑재 하려는 Azure 파일 공유가 포함 된 저장소 계정입니다. 저장소 계정 및 Azure 파일 공유를 만드는 방법을 알아보려면 [azure 파일 공유 만들기](storage-how-to-create-file-share.md)를 참조 하세요.
- 저장소 계정에 대 한 개인 끝점입니다. Azure Files에 대 한 개인 끝점을 만드는 방법을 알아보려면 [개인 끝점 만들기](storage-files-networking-endpoints.md#create-a-private-endpoint)를 참조 하세요.
- Azure PowerShell 모듈의 [최신 버전](/powershell/azure/install-az-ps) 입니다.

> [!Important]  
> 이 가이드에서는 온-프레미스 환경의 Windows Server 내에서 DNS 서버를 사용 하 고 있다고 가정 합니다. 이 가이드에서 설명 하는 모든 단계는 Windows DNS 서버 뿐 아니라 모든 DNS 서버에서 가능 합니다.

## <a name="manually-configuring-dns-forwarding"></a>수동으로 DNS 전달 구성
Azure 가상 네트워크 내에 DNS 서버가 이미 있는 경우 또는 조직에서 사용 하는 방법에 따라 자체 가상 컴퓨터를 DNS 서버로 배포 하려는 경우에는 기본 제공 DNS 서버 PowerShell cmdlet을 사용 하 여 DNS를 수동으로 구성할 수 있습니다.

온-프레미스 DNS 서버에서를 사용 하 여 조건부 전달자를 만듭니다 `Add-DnsServerConditionalForwarderZone` . Azure로의 트래픽을 올바르게 전달 하려면 모든 온-프레미스 DNS 서버에이 조건부 전달자를 배포 해야 합니다. 을 `<azure-dns-server-ip>` 사용자 환경에 적합 한 IP 주소로 바꾸어야 합니다.

```powershell
$vnetDnsServers = "<azure-dns-server-ip>", "<azure-dns-server-ip>"

$storageAccountEndpoint = Get-AzContext | `
    Select-Object -ExpandProperty Environment | `
    Select-Object -ExpandProperty StorageEndpointSuffix

Add-DnsServerConditionalForwarderZone `
        -Name $storageAccountEndpoint `
        -MasterServers $vnetDnsServers
```

또한 Azure 가상 네트워크 내의 DNS 서버에서 저장소 계정 DNS 영역에 대 한 요청이 예약 된 IP 주소에 의해 제어 되 되는 Azure 개인 DNS 서비스로 전달 되도록 전달자를 배치 해야 합니다 `168.63.129.16` . `$storageAccountEndpoint`다른 PowerShell 세션 내에서 명령을 실행 하는 경우에는 채워야 합니다.

```powershell
Add-DnsServerConditionalForwarderZone `
        -Name $storageAccountEndpoint `
        -MasterServers "168.63.129.16"
```

## <a name="using-the-azure-files-hybrid-module-to-configure-dns-forwarding"></a>Azure Files 하이브리드 모듈을 사용 하 여 DNS 전달 구성
DNS 전달을 최대한 쉽게 구성 하기 위해 Azure Files 하이브리드 모듈에서 자동화를 제공 했습니다. 이 모듈에서 DNS를 조작 하기 위해 제공 하는 cmdlet은 Azure 가상 네트워크에 DNS 서버를 배포 하 고 온-프레미스 DNS 서버를 업데이트 하 여 전달 하는 데 도움이 됩니다. 

Azure Files 하이브리드 모듈을 사용한 적이 없는 경우 먼저 워크스테이션에 설치 해야 합니다. Azure Files 하이브리드 PowerShell 모듈의 [최신 버전](https://github.com/Azure-Samples/azure-files-samples/releases) 을 다운로드 합니다.

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

DNS 전달 솔루션을 배포 하는 데는 두 단계가 있습니다. DNS 전달 규칙 집합은 요청을 전달할 Azure 서비스와 DNS 전달자의 실제 배포를 정의 합니다. 

다음 예에서는 저장소 계정에 요청을 전달 하 고, Azure Files, Azure Blob storage, Azure Table storage 및 Azure Queue storage에 대 한 포괄 요청을 전달 합니다. 원하는 경우 `-AzureEndpoints` cmdlet의 매개 변수를 통해 추가 Azure 서비스에 대 한 전달을 규칙에 추가할 수 있습니다 `New-AzDnsForwardingRuleSet` . `<virtual-network-resource-group>`, `<virtual-network-name>` 및 `<subnet-name>`을 사용자 환경에 적합한 값으로 바꿔야 합니다.

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

또한 다음과 같이 몇 가지 추가 매개 변수를 제공 하는 데 유용 하거나 필요 합니다.

| 매개 변수 이름 | Type | 설명 |
|----------------|------|-------------|
| `DnsServerResourceGroupName` | `string` | 기본적으로 DNS 서버는 가상 네트워크와 동일한 리소스 그룹에 배포 됩니다. 이를 원하지 않는 경우이 매개 변수를 사용 하 여 배포할 대체 리소스 그룹을 선택할 수 있습니다. |
| `DnsForwarderRootName` | `string` | 기본적으로 Azure에 배포 된 DNS 서버에는 이름이 있습니다 `DnsFwder-*` . 여기서 별표는 iterator로 채워집니다. 이 매개 변수는 해당 이름의 루트 (예:)를 변경 `DnsFwder` 합니다. |
| `VmTemporaryPassword` | `SecureString` | 기본적으로 VM이 도메인에 가입 되기 전의 임시 기본 계정에 대해 임의의 암호를 선택 합니다. 도메인에 가입 된 후에는 기본 계정이 사용 하지 않도록 설정 됩니다. |
| `DomainToJoin` | `string` | 가입할 DNS VM에 가입할 도메인입니다. 기본적으로이 도메인은 cmdlet을 실행 하는 컴퓨터의 도메인을 기반으로 선택 됩니다. |
| `DnsForwarderRedundancyCount` | `int` | 가상 네트워크에 대해 배포할 DNS Vm의 수입니다. 기본적으로는 `New-AzDnsForwarder` 중복성을 보장 하기 위해 Azure 가상 네트워크의 두 DNS 서버를 가용성 집합에 배포 합니다. 이 번호는 원하는 대로 수정할 수 있습니다. |
| `OnPremDnsHostNames` | `HashSet<string>` | 전달자를 만들 온-프레미스 DNS 호스트 이름에 대 한 수동으로 지정 된 목록입니다. 이 매개 변수는 수동으로 지정한 DNS 이름을 가진 클라이언트 범위가 있는 경우와 같이 모든 온-프레미스 DNS 서버에서 전달자를 적용 하지 않으려는 경우에 유용 합니다. |
| `Credential` | `PSCredential` | DNS 서버를 업데이트할 때 사용할 자격 증명입니다. 이는 로그인 한 사용자 계정에 DNS 설정을 수정할 수 있는 권한이 없는 경우에 유용 합니다. |
| `SkipParentDomain` | `SwitchParameter` | 기본적으로 DNS 전달자는 사용자 환경에 있는 최상위 수준 도메인에 적용 됩니다. 예를 들어 `northamerica.corp.contoso.com` 이의 자식 도메인 인 경우에 `corp.contoso.com` 연결 된 DNS 서버에 대 한 전달자를 만듭니다 `corp.contoso.com` . 이 매개 변수는에서 전달자를 만들도록 `northamerica.corp.contoso.com` 합니다. |

## <a name="confirm-dns-forwarders"></a>DNS 전달자 확인
DNS 전달 자가 성공적으로 적용 되었는지 테스트 하기 전에를 사용 하 여 로컬 워크스테이션에서 DNS 캐시를 지우는 것이 좋습니다 `Clear-DnsClientCache` . 를 테스트 하 여 저장소 계정의 정규화 된 도메인 이름을 성공적으로 확인할 수 있는지 확인 하려면 또는를 사용 `Resolve-DnsName` `nslookup` 합니다.

```powershell
# Replace storageaccount.file.core.windows.net with the appropriate FQDN for your storage account.
# Note the proper suffix (core.windows.net) depends on the cloud your deployed in.
Resolve-DnsName -Name storageaccount.file.core.windows.net
```

이름 확인에 성공 하면 확인 된 IP 주소가 저장소 계정의 IP 주소와 일치 하는 것을 확인 해야 합니다.

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

VPN 또는 Express 경로 연결을 이미 설정한 경우를 사용 `Test-NetConnection` 하 여 저장소 계정에 TCP 연결을 만들 수 있는지 확인할 수도 있습니다.

```PowerShell
Test-NetConnection -ComputerName storageaccount.file.core.windows.net -CommonTCPPort SMB
```

## <a name="see-also"></a>참고 항목
- [Azure Files 배포 계획](storage-files-planning.md)
- [Azure Files 네트워킹 고려 사항](storage-files-networking-overview.md)
- [Azure Files 네트워크 엔드포인트 구성](storage-files-networking-endpoints.md)