---
title: Azure PowerShell를 사용 하 여 Azure 개인 끝점 만들기 Microsoft Docs
description: Azure 개인 링크에 대 한 자세한 정보
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: how-to
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: 0c6fc36be101679cea3a770f311005f63c3f0d66
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84737379"
---
# <a name="create-a-private-endpoint-using-azure-powershell"></a>Azure PowerShell를 사용 하 여 개인 끝점 만들기
프라이빗 엔드포인트는 Azure에서 프라이빗 링크를 만드는 데 사용되는 기본 구성 요소입니다. 프라이빗 엔드포인트는 VM(Virtual Machines) 같은 Azure 리소스가 프라이빗 링크 리소스와 비공개로 통신할 수 있게 해줍니다. 

이 빠른 시작에서는 Azure PowerShell를 사용 하 여 Azure 개인 끝점이 있는 논리 SQL 서버인 Azure Virtual Network에서 VM을 만드는 방법에 대해 알아봅니다. 그러면 VM에서 SQL Database에 안전하게 액세스할 수 있습니다.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

리소스를 만들기 전에 Virtual Network 및 개인 끝점 ( [AzResourceGroup)](/powershell/module/az.resources/new-azresourcegroup)을 호스팅하는 리소스 그룹을 만들어야 합니다. 다음 예제에서는 *WestUS* 위치에 *myresourcegroup* 이라는 리소스 그룹을 만듭니다.

```azurepowershell

New-AzResourceGroup `
  -ResourceGroupName myResourceGroup `
  -Location westcentralus
```

## <a name="create-a-virtual-network"></a>Virtual Network 만들기
이 섹션에서는 가상 네트워크 및 서브넷을 만듭니다. 그런 다음 서브넷을 Virtual Network 연결 합니다.

### <a name="create-a-virtual-network"></a>Virtual Network 만들기

[AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork)를 사용 하 여 개인 끝점에 대 한 가상 네트워크를 만듭니다. 다음 예에서는 *MyVirtualNetwork*라는 Virtual Network를 만듭니다.
 
```azurepowershell

$virtualNetwork = New-AzVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location westcentralus `
  -Name myVirtualNetwork `
  -AddressPrefix 10.0.0.0/16
```

### <a name="add-a-subnet"></a>서브넷 추가

Azure는 Virtual Network 내의 서브넷에 리소스를 배포 하므로 서브넷을 만들어야 합니다. [AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig)를 사용 하 여 *mysubnet* 이라는 서브넷 구성을 만듭니다. 다음 예제에서는 개인 끝점 네트워크 정책 플래그가 **사용 안 함으로**설정 된 *mysubnet* 이라는 서브넷을 만듭니다.

```azurepowershell
$subnetConfig = Add-AzVirtualNetworkSubnetConfig `
  -Name mySubnet `
  -AddressPrefix 10.0.0.0/24 `
  -PrivateEndpointNetworkPoliciesFlag "Disabled" `
  -VirtualNetwork $virtualNetwork
```

> [!CAUTION]
> `PrivateEndpointNetworkPoliciesFlag`매개 변수를 사용 가능한 다른 플래그와 혼동 하기 쉽습니다 `PrivateLinkServiceNetworkPoliciesFlag` . 이러한 플래그는 긴 단어이 고 모양은 동일 하기 때문입니다.  올바른를 사용 하 고 있는지 확인 `PrivateEndpointNetworkPoliciesFlag` 합니다.

### <a name="associate-the-subnet-to-the-virtual-network"></a>서브넷을 Virtual Network 연결

[AzVirtualNetwork](/powershell/module/az.network/Set-azVirtualNetwork)를 사용 하 여 서브넷 구성을 Virtual Network에 쓸 수 있습니다. 다음 명령은 서브넷을 만듭니다.

```azurepowershell
$virtualNetwork | Set-AzVirtualNetwork
```

## <a name="create-a-virtual-machine"></a>Virtual Machine 만들기

[New-azvm](/powershell/module/az.compute/new-azvm)를 사용 하 여 VIRTUAL NETWORK에서 VM을 만듭니다. 다음 명령을 실행하면 자격 증명을 묻는 메시지가 표시됩니다. VM의 사용자 이름 및 암호를 입력합니다.

```azurepowershell-interactive
New-AzVm `
    -ResourceGroupName "myResourceGroup" `
    -Name "myVm" `
    -Location "westcentralus" `
    -VirtualNetworkName "MyVirtualNetwork" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress" `
    -OpenPorts 80,3389 `
    -AsJob  
```

`-AsJob` 옵션은 백그라운드에서 VM을 만듭니다. 다음 단계를 계속 진행할 수 있습니다.

Azure가 백그라운드에서 VM을 만들기 시작하면 다음과 같은 메시지가 표시됩니다.

```powershell
Id     Name            PSJobTypeName   State         HasMoreData     Location             Command
--     ----            -------------   -----         -----------     --------             -------
1      Long Running... AzureLongRun... Running       True            localhost            New-AzVM
```

## <a name="create-a-logical-sql-server"></a>논리 SQL 서버 만들기 

AzSqlServer 명령을 사용 하 여 논리 SQL server를 만듭니다. 서버의 이름은 Azure 전체에서 고유해야 하므로 괄호 안의 자리 표시자 값을 사용자 고유의 값으로 바꿉니다.

```azurepowershell-interactive
$adminSqlLogin = "SqlAdmin"
$password = "ChangeYourAdminPassword1"

$server = New-AzSqlServer -ResourceGroupName "myResourceGroup" `
    -ServerName "myserver" `
    -Location "WestCentralUS" `
    -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $adminSqlLogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))

New-AzSqlDatabase  -ResourceGroupName "myResourceGroup" `
    -ServerName "myserver"`
    -DatabaseName "myda"`
    -RequestedServiceObjectiveName "S0" `
    -SampleName "AdventureWorksLT"
```

## <a name="create-a-private-endpoint"></a>Private Endpoint 만들기

[AzPrivateLinkServiceConnection](/powershell/module/az.network/New-AzPrivateLinkServiceConnection)를 사용 하는 Virtual Network의 서버에 대 한 개인 끝점: 

```azurepowershell

$privateEndpointConnection = New-AzPrivateLinkServiceConnection -Name "myConnection" `
  -PrivateLinkServiceId $server.ResourceId `
  -GroupId "sqlServer" 
 
$virtualNetwork = Get-AzVirtualNetwork -ResourceGroupName  "myResourceGroup" -Name "MyVirtualNetwork"  
 
$subnet = $virtualNetwork `
  | Select -ExpandProperty subnets `
  | Where-Object  {$_.Name -eq 'mysubnet'}  
 
$privateEndpoint = New-AzPrivateEndpoint -ResourceGroupName "myResourceGroup" `
  -Name "myPrivateEndpoint" `
  -Location "westcentralus" `
  -Subnet  $subnet `
  -PrivateLinkServiceConnection $privateEndpointConnection
``` 

## <a name="configure-the-private-dns-zone"></a>프라이빗 DNS 영역 구성 
SQL Database 도메인에 대한 프라이빗 DNS 영역을 만들고, Virtual Network와 연결 링크를 만들고, DNS 영역 그룹을 만들어 프라이빗 엔드포인트를 프라이빗 DNS 영역과 연결합니다.

```azurepowershell

$zone = New-AzPrivateDnsZone -ResourceGroupName "myResourceGroup" `
  -Name "privatelink.database.windows.net" 
 
$link  = New-AzPrivateDnsVirtualNetworkLink -ResourceGroupName "myResourceGroup" `
  -ZoneName "privatelink.database.windows.net"`
  -Name "mylink" `
  -VirtualNetworkId $virtualNetwork.Id  

$config = New-AzPrivateDnsZoneConfig -Name "privatelink.database.windows.net" -PrivateDnsZoneId $zone.ResourceId

$privateDnsZoneGroup = New-AzPrivateDnsZoneGroup -ResourceGroupName "myResourceGroup" `
 -PrivateEndpointName "myPrivateEndpoint" -name "MyZoneGroup" -PrivateDnsZoneConfig $config
``` 
  
## <a name="connect-to-a-vm-from-the-internet"></a>인터넷에서 VM에 연결

[Get-AzPublicIpAddress](/powershell/module/az.network/Get-AzPublicIpAddress)를 사용하여 VM의 공용 IP 주소를 반환합니다. 이 예제에서는 *myvm* vm의 공용 IP 주소를 반환 합니다.

```azurepowershell
Get-AzPublicIpAddress `
  -Name myPublicIpAddress `
  -ResourceGroupName myResourceGroup `
  | Select IpAddress 
```  
로컬 컴퓨터에서 명령 프롬프트를 엽니다. Mstsc 명령을 실행 합니다. <publicIpAddress>를 마지막 단계에서 반환된 공용 IP 주소로 바꿉니다. 


> [!NOTE]
> 로컬 컴퓨터의 PowerShell 프롬프트에서 이러한 명령을 실행했으며 Az PowerShell 모듈 버전 1.0 이상을 사용 중이면 해당 인터페이스에서 계속 진행할 수 있습니다.
```
mstsc /v:<publicIpAddress>
```

1. 메시지가 표시되면 **연결**을 선택합니다. 
2. VM을 만들 때 지정한 사용자 이름과 암호를 입력합니다.
  > [!NOTE]
  > VM을 만들 때 입력 한 자격 증명을 지정 하려면 다른 계정을 사용 하 > 다른 옵션을 선택 해야 할 수도 있습니다. 
  
3. **확인**을 선택합니다. 
4. 인증서 경고가 표시될 수도 있습니다. 표시되는 경우 **예** 또는 **계속**을 선택합니다. 

## <a name="access-sql-database-privately-from-the-vm"></a>VM에서 비공개로 SQL Database에 액세스

1. myVM의 원격 데스크톱에서 PowerShell을 엽니다.
2. `nslookup myserver.database.windows.net`를 입력합니다. 을 `myserver` SQL server 이름으로 바꾸어야 합니다.

    다음과 유사한 메시지가 표시됩니다.
    
    ```azurepowershell
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    myserver.privatelink.database.windows.net
    Address:  10.0.0.5
    Aliases:   myserver.database.windows.net
    ```
    
3. [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-ver15)를 설치합니다.
4. **서버에 연결**에서 다음 정보를 입력하거나 선택합니다.

    | 설정 | 값 |
    | --- | --- |
    | 서버 유형 | 데이터베이스 엔진 |
    | 서버 이름 | myserver.database.windows.net |
    | 사용자 이름 | 만드는 동안 제공 된 사용자 이름을 입력 합니다. |
    | 암호 | 만드는 동안 제공 된 암호 입력 |
    | 암호 기억을 | 예 |
    
5. **연결**을 선택합니다.
6. 왼쪽 메뉴에서 **데이터베이스** 를 검색 합니다. 
7. (선택 사항) Mydatabase에서 정보를 만들거나 쿼리합니다.
8. *Myvm*에 대 한 원격 데스크톱 연결을 닫습니다. 

## <a name="clean-up-resources"></a>리소스 정리 
개인 끝점, SQL Database 및 VM을 사용 하 여 완료 한 경우 [AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) 를 사용 하 여 리소스 그룹 및 해당 그룹에 포함 된 모든 리소스를 제거 합니다.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>다음 단계
- [Azure Private Link](private-link-overview.md)에 대해 자세히 알아봅니다.
