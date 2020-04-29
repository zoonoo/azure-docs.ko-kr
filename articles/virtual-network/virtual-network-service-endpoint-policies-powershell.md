---
title: Azure Storage에 대 한 데이터 반출 제한 Azure PowerShell
description: 이 문서에서는 Azure PowerShell를 사용 하 여 가상 네트워크 서비스 끝점 정책으로 리소스를 Azure Storage 하는 가상 네트워크 데이터를 제한 하 고 제한 하는 방법을 알아봅니다.
services: virtual-network
documentationcenter: virtual-network
author: RDhillon
manager: narayan
editor: ''
tags: azure-resource-manager
Customer intent: I want only resources in a virtual network subnet to access an Azure PaaS resource, such as an Azure Storage account.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: ''
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: rdhillon
ms.custom: ''
ms.openlocfilehash: 673431e2ddfc9a641bb1c640891daac79350cb3a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "78253027"
---
# <a name="manage-data-exfiltration-to-azure-storage-accounts-with-virtual-network-service-endpoint-policies-using-azure-powershell"></a>Azure PowerShell를 사용 하 여 가상 네트워크 서비스 끝점 정책을 사용 하 여 계정을 Azure Storage 하는 데이터 반출 관리

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

가상 네트워크 서비스 끝점 정책을 사용 하면 서비스 끝점을 통해 가상 네트워크 내에서 Azure Storage 계정에 대 한 액세스 제어를 적용할 수 있습니다. 이는 작업을 보호 하 고, 허용 되는 저장소 계정 및 데이터 반출이 허용 되는 위치를 관리 하는 데 중요 합니다.
이 문서에서는 다음 방법을 설명합니다.

* 가상 네트워크를 만듭니다.
* 서브넷을 추가 하 고 Azure Storage에 대해 서비스 끝점을 사용 하도록 설정 합니다.
* 두 개의 Azure Storage 계정을 만들고 위에서 만든 서브넷에서 네트워크 액세스를 허용 합니다.
* 저장소 계정 중 하나에만 액세스를 허용 하는 서비스 끝점 정책을 만듭니다.
* 서브넷에 VM (가상 머신)을 배포 합니다.
* 서브넷에서 허용 된 저장소 계정에 대 한 액세스를 확인 합니다.
* 서브넷에서 허용 되지 않는 저장소 계정에 대 한 액세스가 거부 되었는지 확인 합니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

PowerShell을 로컬로 설치하고 사용하도록 선택하는 경우, 이 문서에는 Azure PowerShell 모듈 버전 1.0.0 이상이 필요합니다. 설치되어 있는 버전을 확인하려면 `Get-Module -ListAvailable Az`을 실행합니다. 업그레이드해야 하는 경우 [Azure PowerShell 모듈 설치](/powershell/azure/install-az-ps)를 참조하세요. 또한 PowerShell을 로컬로 실행하는 경우 `Connect-AzAccount`를 실행하여 Azure와 연결해야 합니다.

## <a name="create-a-virtual-network"></a>가상 네트워크 만들기

가상 네트워크를 만들기 전에 가상 네트워크에 대한 리소스 그룹과 이 아티클에서 만든 다른 모든 리소스를 만들어야 합니다. [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)을 사용하여 리소스 그룹을 만듭니다. 다음 예제에서는 *myResourceGroup*이라는 리소스 그룹을 만듭니다. 

```azurepowershell-interactive
New-AzResourceGroup `
  -ResourceGroupName myResourceGroup `
  -Location EastUS
```

[New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork)를 사용하여 가상 네트워크를 만듭니다. 다음 예제는 주소 접두사 *10.0.0.0/16*을 사용하는 *myVirtualNetwork*라는 가상 네트워크를 만듭니다.

```azurepowershell-interactive
$virtualNetwork = New-AzVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myVirtualNetwork `
  -AddressPrefix 10.0.0.0/16
```

## <a name="enable-a-service-endpoint"></a>서비스 엔드포인트 사용

가상 네트워크에 서브넷을 만듭니다. 이 예제에서는 *Private*라는 서브넷이 *Microsoft.Storage*에 대한 서비스 엔드포인트로 생성됩니다. 

```azurepowershell-interactive
$subnetConfigPrivate = Add-AzVirtualNetworkSubnetConfig `
  -Name Private `
  -AddressPrefix 10.0.0.0/24 `
  -VirtualNetwork $virtualNetwork `
  -ServiceEndpoint Microsoft.Storage

$virtualNetwork | Set-AzVirtualNetwork
```

## <a name="restrict-network-access-for-the-subnet"></a>서브넷에 대 한 네트워크 액세스 제한

[AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig)를 사용 하 여 네트워크 보안 그룹 보안 규칙을 만듭니다. 다음 규칙을 사용하면 Azure Storage 서비스에 지정된 공용 IP 주소에 대한 아웃바운드 액세스가 허용됩니다. 

```azurepowershell-interactive
$rule1 = New-AzNetworkSecurityRuleConfig `
  -Name Allow-Storage-All `
  -Access Allow `
  -DestinationAddressPrefix Storage `
  -DestinationPortRange * `
  -Direction Outbound `
  -Priority 100 -Protocol * `
  -SourceAddressPrefix VirtualNetwork `
  -SourcePortRange *
```

다음 규칙은 모든 공용 IP 주소에 대한 액세스를 거부합니다. 우선 순위가 더 높은 이전 규칙이 이 규칙을 재정의하여 Azure Storage의 공용 IP 주소에 대한 액세스를 허용합니다.

```azurepowershell-interactive
$rule2 = New-AzNetworkSecurityRuleConfig `
  -Name Deny-Internet-All `
  -Access Deny `
  -DestinationAddressPrefix Internet `
  -DestinationPortRange * `
  -Direction Outbound `
  -Priority 110 -Protocol * `
  -SourceAddressPrefix VirtualNetwork `
  -SourcePortRange *
```

다음 규칙을 사용하면 어디에서나 서브넷에 대한 RDP(원격 데스크톱 프로토콜) 트래픽 인바운드가 허용됩니다. 서브넷에 대한 원격 데스크톱 연결이 허용되므로 이후 단계에서 리소스에 대한 네트워크 액세스를 확인할 수 있습니다.

```azurepowershell-interactive
$rule3 = New-AzNetworkSecurityRuleConfig `
  -Name Allow-RDP-All `
  -Access Allow `
  -DestinationAddressPrefix VirtualNetwork `
  -DestinationPortRange 3389 `
  -Direction Inbound `
  -Priority 120 `
  -Protocol * `
  -SourceAddressPrefix * `
  -SourcePortRange *
```

[New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup)을 사용하여 네트워크 보안 그룹을 만듭니다. 다음 예제에서는 *myNsgPrivate*라는 네트워크 보안 그룹을 만듭니다.

```azurepowershell-interactive
$nsg = New-AzNetworkSecurityGroup `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myNsgPrivate `
  -SecurityRules $rule1,$rule2,$rule3
```

[AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) 를 사용 하 여 *개인* 서브넷에 네트워크 보안 그룹을 연결 하 고 가상 네트워크에 서브넷 구성을 씁니다. 다음 예제에서는 *myNsgPrivate* 네트워크 보안 그룹을 *프라이빗* 서브넷에 연결합니다.

```azurepowershell-interactive
Set-AzVirtualNetworkSubnetConfig `
  -VirtualNetwork $VirtualNetwork `
  -Name Private `
  -AddressPrefix 10.0.0.0/24 `
  -ServiceEndpoint Microsoft.Storage `
  -NetworkSecurityGroup $nsg

$virtualNetwork | Set-AzVirtualNetwork
```

## <a name="restrict-network-access-to-azure-storage-accounts"></a>Azure Storage 계정에 대 한 네트워크 액세스 제한

서비스 엔드포인트에 사용할 수 있는 Azure 서비스를 통해 만든 리소스에 대한 네트워크 액세스를 제한하는 데 필요한 단계는 서비스에 따라 다릅니다. 각 서비스에 대한 특정 단계는 개별 서비스의 설명서를 참조하세요. 이 문서의 나머지 부분에는 Azure Storage 계정에 대한 네트워크 액세스를 제한하는 단계가 예제로 포함되어 있습니다.

### <a name="create-two-storage-accounts"></a>두 개의 저장소 계정 만들기

[AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount)를 사용 하 여 Azure storage 계정을 만듭니다.

```azurepowershell-interactive
$storageAcctName1 = 'allowedaccount'

New-AzStorageAccount `
  -Location EastUS `
  -Name $storageAcctName1 `
  -ResourceGroupName myResourceGroup `
  -SkuName Standard_LRS `
  -Kind StorageV2
```

저장소 계정을 만든 후 [AzStorageAccountKey](/powershell/module/az.storage/get-azstorageaccountkey)를 사용 하 여 저장소 계정에 대 한 키를 변수로 검색 합니다.

```azurepowershell-interactive
$storageAcctKey1 = (Get-AzStorageAccountKey -ResourceGroupName myResourceGroup -AccountName $storageAcctName1).Value[0]
```

이 키는 이후 단계에서 파일 공유를 만드는 데 사용됩니다. `$storageAcctKey`를 입력하고 값을 적어 둡니다. 이후 단계에서 파일 공유를 VM의 드라이브에 매핑할 때도 이 값을 수동으로 입력해야 합니다.

이제 위의 단계를 반복 하 여 두 번째 저장소 계정을 만듭니다.

```azurepowershell-interactive
$storageAcctName2 = 'notallowedaccount'

New-AzStorageAccount `
  -Location EastUS `
  -Name $storageAcctName2 `
  -ResourceGroupName myResourceGroup `
  -SkuName Standard_LRS `
  -Kind StorageV2
```

또한 나중에를 사용 하 여 파일 공유를 만들 수 있도록이 계정에서 저장소 계정 키를 검색 합니다.

```azurepowershell-interactive
$storageAcctKey2 = (Get-AzStorageAccountKey -ResourceGroupName myResourceGroup -AccountName $storageAcctName2).Value[0]
```

### <a name="create-a-file-share-in-each-of-the-storage-account"></a>각 저장소 계정에서 파일 공유를 만듭니다.

[AzStorageContext](/powershell/module/az.storage/new-AzStoragecontext)를 사용 하 여 저장소 계정 및 키에 대 한 컨텍스트를 만듭니다. 이 컨텍스트는 스토리지 계정 이름 및 계정 키를 캡슐화합니다.

```azurepowershell-interactive
$storageContext1 = New-AzStorageContext $storageAcctName1 $storageAcctKey1

$storageContext2 = New-AzStorageContext $storageAcctName2 $storageAcctKey2
```

[AzStorageShare](/powershell/module/az.storage/new-azstorageshare)를 사용 하 여 파일 공유를 만듭니다.

```azurepowershell-interactive
$share1 = New-AzStorageShare my-file-share -Context $storageContext1

$share2 = New-AzStorageShare my-file-share -Context $storageContext2
```

### <a name="deny-all-network-access-to-a-storage-accounts"></a>저장소 계정에 대 한 모든 네트워크 액세스 거부

기본적으로 스토리지 계정은 네트워크에 있는 클라이언트의 네트워크 연결을 허용합니다. 선택한 네트워크에 대 한 액세스를 제한 하려면 기본 작업을 [AzStorageAccountNetworkRuleSet](/powershell/module/az.storage/update-azstorageaccountnetworkruleset)로 *거부* 로 변경 합니다. 네트워크 액세스가 거부되면 네트워크에서 스토리지 계정에 액세스할 수 없습니다.

```azurepowershell-interactive
Update-AzStorageAccountNetworkRuleSet `
  -ResourceGroupName myresourcegroup `
  -Name $storageAcctName1 `
  -DefaultAction Deny

Update-AzStorageAccountNetworkRuleSet  `
  -ResourceGroupName myresourcegroup `
  -Name $storageAcctName2 `
  -DefaultAction Deny
```

### <a name="enable-network-access-only-from-the-vnet-subnet"></a>VNet 서브넷 에서만 네트워크 액세스 사용

[AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) 를 사용 하 여 만든 가상 네트워크를 검색 한 다음 [AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig)를 사용 하 여 전용 서브넷 개체를 변수로 검색 합니다.

```azurepowershell-interactive
$privateSubnet = Get-AzVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Name myVirtualNetwork `
  | Get-AzVirtualNetworkSubnetConfig -Name Private
```

[AzStorageAccountNetworkRule](/powershell/module/az.network/add-aznetworksecurityruleconfig)를 사용 하 여 *개인* 서브넷에서 저장소 계정에 대 한 네트워크 액세스를 허용 합니다.

```azurepowershell-interactive
Add-AzStorageAccountNetworkRule `
  -ResourceGroupName myresourcegroup `
  -Name $storageAcctName1 `
  -VirtualNetworkResourceId $privateSubnet.Id

Add-AzStorageAccountNetworkRule `
  -ResourceGroupName myresourcegroup `
  -Name $storageAcctName2 `
  -VirtualNetworkResourceId $privateSubnet.Id
```

## <a name="apply-policy-to-allow-access-to-valid-storage-account"></a>올바른 저장소 계정에 대 한 액세스를 허용 하는 정책 적용

가상 네트워크의 사용자가 안전 하 고 허용 되는 Azure Storage 계정에만 액세스할 수 있도록 하려면 정의에서 허용 된 저장소 계정 목록으로 서비스 끝점 정책을 만들 수 있습니다. 그런 다음 서비스 끝점을 통해 저장소에 연결 된 가상 네트워크 서브넷에이 정책이 적용 됩니다.

### <a name="create-a-service-endpoint-policy"></a>서비스 엔드포인트 정책 만들기

이 섹션에서는 서비스 끝점을 통해 액세스할 수 있는 리소스 목록을 사용 하 여 정책 정의를 만듭니다.

첫 번째 (허용 된) 저장소 계정에 대 한 리소스 ID를 검색 합니다. 

```azurepowershell-interactive
$resourceId = (Get-AzStorageAccount -ResourceGroupName myresourcegroup -Name $storageAcctName1).id
```

위의 리소스를 허용 하는 정책 정의 만들기

```azurepowershell-interactive
$policyDefinition = New-AzServiceEndpointPolicyDefinition -Name mypolicydefinition `
  -Description "Service Endpoint Policy Definition" `
  -Service "Microsoft.Storage" `
  -ServiceResource $resourceId
```

위에서 만든 정책 정의를 사용 하 여 서비스 끝점 정책 만들기

```azurepowershell-interactive
$sepolicy = New-AzServiceEndpointPolicy -ResourceGroupName myresourcegroup `
  -Name mysepolicy -Location EastUS
  -ServiceEndpointPolicyDefinition $policyDefinition
```

### <a name="associate-the-service-endpoint-policy-to-the-virtual-network-subnet"></a>가상 네트워크 서브넷에 서비스 끝점 정책 연결

서비스 끝점 정책을 만든 후에는 Azure Storage에 대 한 서비스 끝점 구성을 사용 하 여 대상 서브넷과 연결 합니다.

```azurepowershell-interactive
Set-AzVirtualNetworkSubnetConfig -VirtualNetwork $VirtualNetwork `
  -Name Private `
  -AddressPrefix 10.0.0.0/24 `
  -NetworkSecurityGroup $nsg `
  -ServiceEndpoint Microsoft.Storage `
  -ServiceEndpointPolicy $sepolicy

$virtualNetwork | Set-AzVirtualNetwork
```
## <a name="validate-access-restriction-to-azure-storage-accounts"></a>Azure Storage 계정에 대 한 액세스 제한 확인

### <a name="deploy-the-virtual-machine"></a>가상 머신 배포

저장소 계정에 대 한 네트워크 액세스를 테스트 하려면 서브넷에 VM을 배포 합니다.

[New-azvm](/powershell/module/az.compute/new-azvm)를 사용 하 여 *개인* 서브넷에서 가상 머신을 만듭니다. 다음 명령을 실행하면 자격 증명을 묻는 메시지가 표시됩니다. 입력하는 값은 VM에 대한 사용자 이름과 암호로 구성됩니다. `-AsJob` 옵션은 백그라운드에서 VM을 만들므로 다음 단계를 계속 진행할 수 있습니다.

```azurepowershell-interactive
New-AzVm -ResourceGroupName myresourcegroup `
  -Location "East US" `
  -VirtualNetworkName myVirtualNetwork `
  -SubnetName Private `
  -Name "myVMPrivate" -AsJob
```

다음 예제 출력과 유사한 출력이 반환됩니다.

```powershell
Id     Name            PSJobTypeName   State         HasMoreData     Location             Command
--     ----            -------------   -----         -----------     --------             -------
1      Long Running... AzureLongRun... Running       True            localhost            New-AzVM
```

### <a name="confirm-access-to-the-allowed-storage-account"></a>*허용 되* 는 저장소 계정에 대 한 액세스 확인

[Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress)를 사용하여 VM의 공용 IP 주소를 반환합니다. 다음 예제에서는 *myVmPrivate* VM의 공용 IP 주소를 반환합니다.

```azurepowershell-interactive
Get-AzPublicIpAddress `
  -Name myVmPrivate `
  -ResourceGroupName myResourceGroup `
  | Select IpAddress
```

다음 명령에서 `<publicIpAddress>`을 이전 명령에서 반환된 공용 IP 주소로 바꾸고 다음 명령을 입력합니다.

```powershell
mstsc /v:<publicIpAddress>
```

원격 데스크톱 프로토콜(.rdp) 파일이 만들어지고 컴퓨터에 다운로드됩니다. 다운로드한 rdp 파일을 엽니다. 메시지가 표시되면 **연결**을 선택합니다. VM을 만들 때 지정한 사용자 이름과 암호를 입력합니다. **추가 선택 사항**, **다른 계정 사용**을 차례로 선택하여 VM을 만들 때 입력한 자격 증명을 지정해야 할 수도 있습니다. **확인**을 선택합니다. 로그인 프로세스 중에 인증서 경고가 나타날 수 있습니다. 경고 메시지가 표시되면 **예** 또는 **계속**을 선택하여 연결을 계속합니다.

*Myvmprivate* VM에서 PowerShell을 사용 하 여 허용 된 저장소 계정의 Azure 파일 공유를 Z 드라이브에 매핑합니다. 

```powershell
$acctKey = ConvertTo-SecureString -String $storageAcctKey1 -AsPlainText -Force
$credential = New-Object System.Management.Automation.PSCredential -ArgumentList ("Azure\allowedaccount"), $acctKey
New-PSDrive -Name Z -PSProvider FileSystem -Root "\\allowedaccount.file.core.windows.net\my-file-share" -Credential $credential
```

PowerShell에서 다음 예제 출력과 비슷한 출력을 반환합니다.

```powershell
Name           Used (GB)     Free (GB) Provider      Root
----           ---------     --------- --------      ----
Z                                      FileSystem    \\allowedaccount.file.core.windows.net\my-f...
```

Azure 파일 공유가 Z 드라이브에 매핑되었습니다.

*myVmPrivate* VM에 대한 원격 데스크톱 세션을 닫습니다.

### <a name="confirm-access-is-denied-to-non-allowed-storage-account"></a>*허용 되지 않는* 저장소 계정에 대 한 액세스가 거부 되었는지 확인

동일한 *Myvmprivate* VM에서 Azure 파일 공유를 드라이브 X에 매핑하려고 시도 합니다. 

```powershell
$acctKey = ConvertTo-SecureString -String $storageAcctKey1 -AsPlainText -Force
$credential = New-Object System.Management.Automation.PSCredential -ArgumentList "Azure\notallowedaccount", $acctKey
New-PSDrive -Name X -PSProvider FileSystem -Root "\\notallowedaccount.file.core.windows.net\my-file-share" -Credential $credential
```

공유에 대한 액세스가 거부되고 `New-PSDrive : Access is denied` 오류가 수신됩니다. 저장소 계정 *notallowedaccount* 가 서비스 끝점 정책의 허용 된 리소스 목록에 없으므로 액세스가 거부 되었습니다. 

*myVmPublic* VM에 대한 원격 데스크톱 세션을 닫습니다.

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않은 경우 [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup)을 사용하여 리소스 그룹 및 해당 그룹에 포함된 모든 리소스를 제거할 수 있습니다.

```azurepowershell-interactive 
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>다음 단계

이 문서에서는 Azure 가상 네트워크 서비스 끝점에 대 한 서비스 끝점 정책을 Azure Storage에 적용 했습니다. 가상 네트워크 서브넷에서 특정 저장소 계정 (즉, 다른 사용자가 거부 함)에 대 한 제한 된 네트워크 액세스와 Azure Storage 계정을 만들었습니다. 서비스 끝점 정책에 대해 자세히 알아보려면 [서비스 끝점 정책 개요](virtual-network-service-endpoint-policies-overview.md)를 참조 하세요.
