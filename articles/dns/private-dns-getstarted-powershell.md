---
title: Azure PowerShell을 사용하여 Azure DNS 프라이빗 영역 만들기
description: 이 문서 작성 및 Azure DNS에 사설 DNS 영역 및 레코드를 테스트 합니다. Azure PowerShell을 사용하여 첫 번째 프라이빗 DNS 영역 및 레코드를 만들고 관리하는 단계별 가이드입니다.
services: dns
author: vhorne
ms.service: dns
ms.topic: article
ms.date: 06/14/2019
ms.author: victorh
ms.openlocfilehash: 9d79ed28bd331b723755e1c17233aa82421ad1d7
ms.sourcegitcommit: 72f1d1210980d2f75e490f879521bc73d76a17e1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/14/2019
ms.locfileid: "67147886"
---
# <a name="create-an-azure-dns-private-zone-using-azure-powershell"></a>Azure PowerShell을 사용하여 Azure DNS 프라이빗 영역 만들기

[!INCLUDE [private-dns-public-preview-notice](../../includes/private-dns-public-preview-notice.md)]

이 문서에서는 Azure PowerShell을 사용하여 첫 번째 프라이빗 DNS 영역 및 레코드를 만드는 단계를 안내합니다.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

DNS 영역은 특정 도메인에 대한 DNS 레코드를 호스트하는 데 사용됩니다. Azure DNS에서 도메인 호스팅을 시작하려면 해당 도메인 이름의 DNS 영역을 만들어야 합니다. 그러면 이 DNS 영역 안에 도메인의 각 DNS 레코드가 생성됩니다. 가상 네트워크에 프라이빗 DNS 영역에 게시하려면 영역 내에서 레코드를 확인하도록 허용된 가상 네트워크 목록을 지정합니다.  이것을 *연결된* 가상 네트워크라고 합니다. 자동 등록이 활성화된 경우 Azure DNS는 가상 머신이 생성되고, 해당 IP 주소를 변경하거나 삭제될 때마다 영역 레코드를 업데이트합니다.

이 문서에서는 다음 방법을 설명합니다.

> [!div class="checklist"]
> * DNS 프라이빗 영역 만들기
> * 테스트 가상 머신 만들기
> * 추가 DNS 레코드 만들기
> * 프라이빗 영역 테스트

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Azure 구독이 아직 없는 경우 시작하기 전에 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 을 만듭니다.

원하는 경우 [Azure CLI](private-dns-getstarted-cli.md)를 사용하여 이 절차를 완료할 수 있습니다.

## <a name="create-the-resource-group"></a>리소스 그룹 만들기

먼저 DNS 영역을 포함할 리소스 그룹을 만듭니다. 

```azurepowershell
New-AzResourceGroup -name MyAzureResourceGroup -location "eastus"
```

## <a name="create-a-dns-private-zone"></a>DNS 프라이빗 영역 만들기

DNS 영역은 `New-AzPrivateDnsZone` cmdlet을 사용하여 생성됩니다.

다음 예제에서는 **myAzureVNet**이라는 가상 네트워크를 만듭니다. 그런 다음, **MyAzureResourceGroup** 리소스 그룹에 **private.contoso.com**이라는 DNS 영역을 만들고, DNS 영역을 **MyAzureVnet** 가상 네트워크에 연결하고, 자동 등록을 활성화합니다.

```azurepowershell
$backendSubnet = New-AzVirtualNetworkSubnetConfig -Name backendSubnet -AddressPrefix "10.2.0.0/24"
$vnet = New-AzVirtualNetwork `
  -ResourceGroupName MyAzureResourceGroup `
  -Location eastus `
  -Name myAzureVNet `
  -AddressPrefix 10.2.0.0/16 `
  -Subnet $backendSubnet

$zone = New-AzPrivateDnsZone -Name private.contoso.com -ResourceGroupName MyAzureResourceGroup

$link = New-AzPrivateDnsVirtualNetworkLink -ZoneName private.contoso.com `
  -ResourceGroupName MyAzureResourceGroup -Name "mylink" `
  -VirtualNetworkId $vnet.id -EnableRegistration
```

이름 확인 (자동 호스트 이름 등록 제외)에 대 한 영역을 만들려는 경우 생략할 수 있습니다는 `-EnableRegistration` 매개 변수입니다.

### <a name="list-dns-private-zones"></a>DNS 프라이빗 영역 나열

`Get-AzPrivateDnsZone`에서 영역 이름을 생략하면, 리소스 그룹의 모든 영역을 열거할 수 있습니다. 이 작업은 영역 개체의 배열을 반환합니다.

```azurepowershell
$zones = Get-AzPrivateDnsZone -ResourceGroupName MyAzureResourceGroup
$zones
```

`Get-AzPrivateDnsZone`에서 영역 이름 및 리소스 그룹 이름을 모두 생략하여 Azure 구독의 모든 영역을 열거할 수 있습니다.

```azurepowershell
$zones = Get-AzPrivateDnsZone
$zones
```

## <a name="create-the-test-virtual-machines"></a>테스트 가상 머신 만들기

이제 프라이빗 DNS 영역을 테스트할 수 있도록 두 대의 가상 머신을 만듭니다.

```azurepowershell
New-AzVm `
    -ResourceGroupName "myAzureResourceGroup" `
    -Name "myVM01" `
    -Location "East US" `
    -subnetname backendSubnet `
    -VirtualNetworkName "myAzureVnet" `
    -addressprefix 10.2.0.0/24 `
    -OpenPorts 3389

New-AzVm `
    -ResourceGroupName "myAzureResourceGroup" `
    -Name "myVM02" `
    -Location "East US" `
    -subnetname backendSubnet `
    -VirtualNetworkName "myAzureVnet" `
    -addressprefix 10.2.0.0/24 `
    -OpenPorts 3389
```

이 설정은 완료하는 데 몇 분 정도 걸립니다.

## <a name="create-an-additional-dns-record"></a>추가 DNS 레코드 만들기

`New-AzPrivateDnsRecordSet` cmdlet을 사용하여 레코드 집합을 만듭니다. 다음 예제에서는 리소스 그룹 **MyAzureResourceGroup**의 DNS 영역 **private.contoso.com**에 상대적 이름 **db**가 포함된 레코드를 만듭니다. 레코드 집합의 정규화된 이름은 **db.private.contoso.com**입니다. 레코드 형식은 "A"이고, IP 주소는 "10.2.0.4"이며, TTL은 3600초입니다.

```azurepowershell
New-AzPrivateDnsRecordSet -Name db -RecordType A -ZoneName private.contoso.com `
   -ResourceGroupName MyAzureResourceGroup -Ttl 3600 `
   -PrivateDnsRecords (New-AzPrivateDnsRecordConfig -IPv4Address "10.2.0.4")
```

### <a name="view-dns-records"></a>DNS 레코드 보기

사용자 영역에 DNS 레코드를 나열하려면 다음을 실행하세요.

```azurepowershell
Get-AzPrivateDnsRecordSet -ZoneName private.contoso.com -ResourceGroupName MyAzureResourceGroup
```

## <a name="test-the-private-zone"></a>프라이빗 영역 테스트

이제 **private.contoso.com** 프라이빗 영역에 대한 이름 확인을 테스트할 수 있습니다.

### <a name="configure-vms-to-allow-inbound-icmp"></a>인바운드 ICMP를 허용하도록 VM 구성

이름 확인을 테스트하는 ping 명령을 사용할 수 있습니다. 따라서 인바운드 ICMP 패킷을 허용하도록 두 대의 가상 머신에서 방화벽을 구성합니다.

1. myVM01에 연결하고, 관리자 권한으로 Windows PowerShell 창을 엽니다.
2. 다음 명령 실행:

   ```powershell
   New-NetFirewallRule –DisplayName “Allow ICMPv4-In” –Protocol ICMPv4
   ```

myVM02에서 반복

### <a name="ping-the-vms-by-name"></a>이름별로 VM ping

1. myVM02 Windows PowerShell 명령 프롬프트에서 자동으로 등록된 호스트 이름을 사용하여 myVM01을 ping합니다.

   ```
   ping myVM01.private.contoso.com
   ```

   다음과 유사한 결과가 표시됩니다.

   ```
   PS C:\> ping myvm01.private.contoso.com

   Pinging myvm01.private.contoso.com [10.2.0.4] with 32 bytes of data:
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time=1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128

   Ping statistics for 10.2.0.4:
       Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
   Approximate round trip times in milli-seconds:
       Minimum = 0ms, Maximum = 1ms, Average = 0ms
   PS C:\>
   ```

2. 이제 이전에 만든 **db** 이름을 ping합니다.

   ```
   ping db.private.contoso.com
   ```

   다음과 유사한 결과가 표시됩니다.

   ```
   PS C:\> ping db.private.contoso.com

   Pinging db.private.contoso.com [10.2.0.4] with 32 bytes of data:
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128

   Ping statistics for 10.2.0.4:
       Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
   Approximate round trip times in milliseconds:
       Minimum = 0ms, Maximum = 0ms, Average = 0ms
   PS C:\>
   ```

## <a name="delete-all-resources"></a>모든 리소스 삭제

더 이상 필요한 경우 삭제 합니다 **MyAzureResourceGroup** 이 문서에서 만든 리소스를 삭제 하려면 리소스 그룹입니다.

```azurepowershell
Remove-AzResourceGroup -Name MyAzureResourceGroup
```

## <a name="next-steps"></a>다음 단계

이 문서에서는 DNS 레코드를 만든 사설 DNS 영역을 배포 하 고 영역을 테스트 합니다.
다음으로, 프라이빗 DNS 영역에 대해 자세히 알아봅니다.

* [프라이빗 도메인에 Azure DNS 사용](private-dns-overview.md)
