---
title: "Azure Active Directory Domain Services: 네트워크 보안 그룹 구성 문제 해결 | Microsoft Docs"
description: "Azure AD Domain Services에 대한 NSG 구성 문제 해결"
services: active-directory-ds
documentationcenter: 
author: eringreenlee
manager: 
editor: 
ms.assetid: 95f970a7-5867-4108-a87e-471fa0910b8c
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/09/2018
ms.author: ergreenl
ms.openlocfilehash: 447f9119ea379e278be77d8699c7dcb751ea3085
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/18/2018
---
# <a name="azure-ad-domain-services---troubleshooting-network-security-group-configuration"></a>Azure AD Domain Services - 네트워크 보안 그룹 구성 문제 해결



## <a name="aadds104-network-error"></a>AADDS104: 네트워크 오류

**경고 메시지:** *Microsoft는 이 관리되는 도메인에 대한 도메인 컨트롤러에 연결할 수 없습니다. 가상 네트워크에 구성된 NSG(네트워크 보안 그룹)가 관리되는 도메인에 대한 액세스를 차단하려고 할 때 이 문제가 발생할 수 있습니다. 다른 가능한 이유는 인터넷에서 들어오는 트래픽을 차단하는 사용자 정의 경로가 있는 경우입니다.*

Azure AD Domain Services에 대한 네트워크 오류의 가장 일반적인 원인은 잘못된 NSG 구성 때문일 수 있습니다. Microsoft에서 관리되는 도메인을 제공하고 유지 관리할 수 있도록 하려면 NSG(네트워크 보안 그룹)를 사용하여 서브넷 내의 [특정 포트](active-directory-ds-networking.md#ports-required-for-azure-ad-domain-services)에 대한 액세스를 허용해야 합니다. 이러한 포트가 차단되는 경우 Microsoft는 필요한 리소스에 액세스할 수 없으며 이로 인해 서비스에 좋지 않은 영향을 줄 수 있습니다. NSG를 만드는 동안 이러한 포트를 열어 두어, 서비스가 중단되지 않도록 합니다.

## <a name="sample-nsg"></a>샘플 NSG
다음 표에서는 Microsoft에서 정보를 모니터링, 관리 및 업데이트하도록 하면서, 관리되는 도메인을 안전하게 유지하는 샘플 NSG를 보여 줍니다.

![샘플 NSG](.\media\active-directory-domain-services-alerts\default-nsg.png)

>[!NOTE]
> Azure AD Domain Services에는 무제한 아웃바운드 액세스가 필요합니다. NSG에 대한 추가 아웃바운드 규칙을 만드는 것은 바람직하지 않습니다.

## <a name="adding-a-rule-to-a-network-security-group-using-the-azure-portal"></a>Azure Portal을 사용하여 네트워크 보안 그룹에 규칙 추가
PowerShell을 사용하지 않으려는 경우 Azure Portal을 사용하여 NSG에 단일 규칙을 수동으로 추가할 수 있습니다. 네트워크 보안 그룹에 규칙을 만들려면 다음 단계를 따릅니다.

1. Azure Portal에서 [네트워크 보안 그룹](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.Network%2FNetworkSecurityGroups) 페이지로 이동합니다.
2. 테이블에서 도메인에 연결된 NSG를 선택합니다.
3. 왼쪽 탐색 창의 설정에서 **인바운드 보안 규칙** 또는 **아웃바운드 보안 규칙**을 클릭합니다.
4. **추가**를 클릭하고 정보를 입력하여 규칙을 만듭니다. **확인**을 클릭합니다.
5. 규칙 테이블에 규칙이 있으면 제대로 만들어진 것입니다.


## <a name="create-a-default-nsg-using-powershell"></a>PowerShell을 사용하여 기본 NSG 만들기

다음은 원치 않는 다른 액세스를 거부하면서, PowerShell을 사용하여 Azure AD Domain Services를 실행하는 데 필요한 모든 포트를 열어 두는 새로운 NSG를 만드는 데 사용할 수 있는 단계입니다.


이 해결 방법에서는 [Azure AD Powershell](https://docs.microsoft.com/en-us/powershell/azure/active-directory/install-adv2?toc=%2Fazure%2Factive-directory-domain-services%2Ftoc.json&view=azureadps-2.0)을 설치하고 실행해야 합니다.

1. Azure AD 디렉터리에 연결합니다.

  ```PowerShell
  # Connect to your Azure AD directory.
  Connect-AzureAD
  ```
2. Azure 구독에 로그인합니다.

  ```PowerShell
  # Log in to your Azure subscription.
  Login-AzureRmAccount
  ```

3. 세 가지 규칙으로 NSG를 만듭니다. 다음 스크립트는 Azure AD Domain Services를 실행하는 데 필요한 포트에 대한 액세스를 허용하는 NSG에 대한 세 가지 규칙을 정의합니다. 그런 다음, 이 스크립트는 해당 규칙을 포함하는 새 NSG를 만듭니다. 동일한 형식을 사용하여 적합한 추가 규칙을 추가하는 것이 좋습니다.

  ```PowerShell
  # Create the rules needed
  $rule1 = New-AzureRmNetworkSecurityRuleConfig -Name https-rule -Description "Allow HTTP" `
  -Access Allow -Protocol Tcp -Direction Inbound -Priority 101 `
  -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
  -DestinationPortRange 443
  $rule2 = New-AzureRmNetworkSecurityRuleConfig -Name manage-3389 -Description "Manage domain through port 3389" `
  -Access Allow -Protocol Tcp -Direction Inbound -Priority 102 `
  -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
  -DestinationPortRange 3389
  $rule3 = New-AzureRmNetworkSecurityRuleConfig -Name manage-5986 -Description "Manage domain through port 5986" `
  -Access Allow -Protocol Tcp -Direction Inbound -Priority 103 `
  -SourceAddressPrefix $serviceIPs -SourcePortRange * -DestinationAddressPrefix * `
  -DestinationPortRange 5986
  # Create the NSG with the 3 rules above
  $nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $resourceGroup -Location westus `
  -Name "AADDS-NSG" -SecurityRules $rule1,$rule2,$rule3
  ```

4. 마지막으로, 이 스크립트는 vNet과 선택한 서브넷에 NSG를 연결합니다.

  ```PowerShell
  # Find vnet and subnet
  $vnet = Get-AzureRmVirtualNetwork -ResourceGroupName $resourceGroup -Name $vnetName
  $subnet = Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name $subnetName
  # Set the nsg to the subnet and save the changes
  $subnet.NetworkSecurityGroup = $nsg
  Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
  ```

### <a name="full-script"></a>전체 스크립트

```PowerShell
#Change the following values to match your deployment
$resourceGroup = "ResourceGroupName"
$vnetName = "exampleVnet"
$subnetName = "exampleSubnet"

$serviceIPs = "52.180.183.8, 23.101.0.70, 52.225.184.198, 52.179.126.223, 13.74.249.156, 52.187.117.83, 52.161.13.95, 104.40.156.18, 104.40.87.209, 52.180.179.108, 52.175.18.134, 52.138.68.41, 104.41.159.212, 52.169.218.0, 52.187.120.237, 52.161.110.169, 52.174.189.149, 13.64.151.161"

# Create the rules needed
$rule1 = New-AzureRmNetworkSecurityRuleConfig -Name https-rule -Description "Allow HTTP" `
-Access Allow -Protocol Tcp -Direction Inbound -Priority 101 `
-SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
-DestinationPortRange 443

$rule2 = New-AzureRmNetworkSecurityRuleConfig -Name manage-3389 -Description "Manage domain through port 3389" `
-Access Allow -Protocol Tcp -Direction Inbound -Priority 102 `
-SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
-DestinationPortRange 3389

$rule3 = New-AzureRmNetworkSecurityRuleConfig -Name manage-5986 -Description "Manage domain through port 5986" `
-Access Allow -Protocol Tcp -Direction Inbound -Priority 103 `
-SourceAddressPrefix $serviceIPs -SourcePortRange * -DestinationAddressPrefix * `
-DestinationPortRange 3389


# Connect to your Azure AD directory.
Connect-AzureAD

# Log in to your Azure subscription.
Login-AzureRmAccount

# Create the NSG with the 3 rules above
$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $resourceGroup -Location westus `
-Name "NSG-Default" -SecurityRules $rule1,$rule2,$rule3

# Find vnet and subnet
$vnet = Get-AzureRmVirtualNetwork -ResourceGroupName $resourceGroup -Name $vnetName
$subnet = Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name $subnetName

# Set the nsg to the subnet and save the changes
$subnet.NetworkSecurityGroup = $nsg
Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
```

> [!NOTE]
>이 기본 NSG는 보안 LDAP에 사용되는 포트에 대한 액세스를 잠그지 않습니다. 이 포트에 대한 규칙을 만드는 방법을 알아보려면 [이 문서](active-directory-ds-troubleshoot-ldaps.md)를 참조하세요.
>

## <a name="contact-us"></a>문의처
[지원이 필요하거나 피드백을 공유하려면](active-directory-ds-contact-us.md)Azure Active Directory Domain Services 제품 팀에 문의하세요.
