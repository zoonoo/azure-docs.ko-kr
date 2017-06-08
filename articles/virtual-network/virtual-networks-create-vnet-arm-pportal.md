---
title: "Azure Virtual Network 만들기 | Microsoft Docs"
description: "여러 서브넷이 있는 가상 네트워크를 만드는 방법에 대해 알아봅니다."
services: virtual-network
documentationcenter: 
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 4ad679a4-a959-4e48-a317-d9f5655a442b
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/12/2017
ms.author: jdial
ms.custom: 
ms.translationtype: Human Translation
ms.sourcegitcommit: e7da3c6d4cfad588e8cc6850143112989ff3e481
ms.openlocfilehash: 19857ad1e970ad32359708ded320c53a4778ef8c
ms.contentlocale: ko-kr
ms.lasthandoff: 05/16/2017


---
# <a name="create-a-virtual-network-with-multiple-subnets"></a>여러 서브넷이 있는 가상 네트워크 만들기

이 자습서에서는 별도의 공용 및 개인 서브넷이 있는 기본 Azure VNet(가상 네트워크)을 만드는 방법에 대해 알아봅니다. VM(Virtual Machines), App Service 환경, Virtual Machine Scale Sets, HDInsight 및 Cloud Services와 같은 Azure 리소스를 서브넷에 연결할 수 있습니다. VNet에 연결된 리소스는 개인 Azure 네트워크를 통해 서로 통신할 수 있습니다.

다음 섹션에서는 Azure [Portal](#portal), Azure [CLI](#cli)(명령줄 인터페이스), Azure [PowerShell](#powershell) 및 Azure Resource Manager [템플릿](#template)을 사용하여 VNet을 배포하는 단계가 포함되어 있습니다. VNet을 배포하는 데 어떤 도구를 선택하든지 간에 결과는 동일합니다. 도구에 대한 링크를 클릭하면 문서의 해당 섹션으로 바로 이동합니다. 모든 VNet 및 서브넷 설정에 대한 자세한 내용은 [VNet 관리](virtual-network-manage-network.md) 및 [서브넷 관리](virtual-network-manage-subnet.md) 문서를 참조하세요.

## <a name="portal"></a>Azure Portal

1. 인터넷 브라우저에서 Azure [Portal](https://portal.azure.com)을 열고 Azure [계정](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account)으로 로그인합니다. 아직 계정이 없는 경우 [평가판](https://azure.microsoft.com/offers/ms-azr-0044p)을 등록할 수 있습니다.
2. 포털에서 **+ 새로 만들기** > **네트워킹** > **가상 네트워크**를 차례로 클릭합니다.
3. 표시되는 **가상 네트워크** 블레이드의 **배포 모델 선택** 아래에서 *Resource Manager*를 선택한 채로 두고 **만들기**를 클릭합니다.
4. 표시되는 **가상 네트워크 만들기** 블레이드에서 다음 값을 입력한 다음 **만들기** 단추를 클릭합니다.

    |설정|값|
    |---|---|
    |이름|*MyVnet*|
    |주소 공간|*10.0.0.0/16*|
    |서브넷 이름|공용|
    |서브넷 주소 범위|*10.0.0.0/24*|
    |리소스 그룹|**새로 만들기**를 선택한 채로 두고 *MyResourceGroup*을 입력합니다.|
    |구독 및 위치|구독 및 위치를 선택합니다.

    Azure를 처음 사용하는 경우 [리소스 그룹](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group), [구독](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) 및 [위치](https://azure.microsoft.com/regions)(지역이라고도 함)에 대해 자세히 알아봅니다.
6. 포털에서는 VNet을 만들 때 서브넷을 하나만 만들 수 있습니다. 이 자습서에서는 VNet을 만든 후 두 번째 서브넷을 만듭니다. 나중에 인터넷에서 액세스할 수 있는 리소스를 *공용* 서브넷에 연결하고, 인터넷에서 액세스할 수 없는 리소스를 개인 서브넷에 연결할 수 있습니다. 두 번째 서브넷을 만들려면 포털 위쪽의 *리소스 검색* 상자에서 *MyVnet*을 입력합니다. 검색 결과에서 표시되는 **MyVnet**을 클릭합니다. 동일한 이름의 구독에 여러 개의 VNet이 있으면 각 VNet 아래에 동일한 이름의 리소스 그룹 이름이 표시됩니다. *MyResourceGroup*이 있는 MyVnet 결과를 클릭했는지 확인합니다.
7. 표시되는 **MyVnet** 블레이드의 **설정** 아래에서 **서브넷**을 클릭합니다.
8. **MyVnet - 서브넷** 블레이드에서 **+ 서브넷**을 클릭합니다.
9. **서브넷 추가** 블레이드에서 **이름**에 대해 *개인*, **주소 범위**에 대해 *10.0.1.0/24*를 입력한 다음 **확인**을 클릭합니다.
10. **MyVnet - 서브넷** 블레이드에서 서브넷을 검토합니다. 만든 **공용** 및 **개인** 서브넷이 표시됩니다.
11. **선택 사항:** 이 자습서에서 만든 리소스를 삭제하려면 이 문서의 [리소스 삭제](#delete-portal) 섹션에서 설명하는 단계를 완료합니다.

## <a name="cli"></a>CLI
Windows, Linux 또는 macOS 중 어느 운영 체제에서 명령을 실행하든지 간에 CLI 명령은 동일하지만 운영 체제 셸 간에 스크립팅 차이점이 있습니다. 다음 지침은 CLI 명령을 포함한 Bash 스크립트를 실행하기 위한 것입니다.

1. 인터넷 브라우저에서 Azure [Portal](https://portal.azure.com)을 열고 Azure [계정](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account)으로 로그인합니다. 아직 계정이 없는 경우 [평가판](https://azure.microsoft.com/offers/ms-azr-0044p)을 등록할 수 있습니다.
2. 포털 위쪽의 *리소스 검색* 상자 오른쪽에 있는 **>_** 아이콘을 클릭하여 Bash Azure Cloud Shell(미리 보기)을 시작합니다. 클라우드 셸 창이 포털의 아래쪽에 나타나고, 몇 초 후에 **username@Azure:~$** 프롬프트가 표시됩니다. 클라우드 셸은 포털에 인증된 자격 증명을 사용하여 Azure에 자동으로 로그인합니다.
3. 브라우저에서 다음 스크립트를 복사합니다.
    ```azurecli
    #!/bin/bash
    
    # Create a resource group.
    az group create \
      --name MyResourceGroup \
      --location eastus
    
    # Create a virtual network with one subnet.
    az network vnet create \
      --name MyVnet \
      --resource-group MyResourceGroup \
      --subnet-name Public
    
    # Create an additional subnet within the VNet.
    az network vnet subnet create \
      --name Private \
      --address-prefix 10.0.1.0/24 \
      --vnet-name MyVnet \
      --resource-group MyResourceGroup
    ```
4. 스크립트 파일을 만들고 저장합니다. 클라우드 셸 프롬프트에서 `nano myscript.sh --nonewlines`를 입력합니다. 이 명령은 빈 myscript.sh 파일로 GNU nano 편집기를 시작합니다. 편집기 창 안에 마우스를 놓고 마우스 오른쪽 단추를 클릭한 다음 **붙여넣기**를 클릭합니다. 클라우드 셸 저장소는 세션 전체에서 유지되지 않습니다. 클라우드 셸 세션 전체에서 스크립트를 유지하려면 클라우드 셸에 대한 [영구 저장소](../cloud-shell/persisting-shell-storage.md?toc=%2fazure%2fvirtual-network%2ftoc.json)를 설정합니다. 
5. 키보드에서 **Ctrl+X** 키를 누른 채 **Y**를 입력한 다음 **Enter** 키를 눌러 파일을 myscript.sh로 저장합니다.
6. 클라우드 셸 프롬프트에서 `chmod +x myscript.sh` 명령을 사용하여 실행 파일로 표시합니다.
7. `./myscript.sh`를 입력하여 스크립트를 실행합니다.
8. 스크립트가 완료되면 Bash 클라우드 셸에서 다음 명령을 복사하고 붙여넣어 VNet에 대한 서브넷을 검토합니다.
    ```azurecli
    az network vnet subnet list --resource-group MyResourceGroup --vnet-name MyVnet --output table
    ```
9. **선택 사항:** 이 자습서에서 만든 리소스를 삭제하려면 이 문서의 [리소스 삭제](#delete-cli) 섹션에서 설명하는 단계를 완료합니다.

## <a name="powershell"></a>PowerShell
1. 최신 버전의 Azure PowerShell [AzureRm](https://www.powershellgallery.com/packages/AzureRM/) 모듈을 설치합니다. Azure PowerShell을 처음 사용하는 경우 [Azure PowerShell 개요](/powershell/azure/overview?toc=%2fazure%2fvirtual-network%2ftoc.json) 문서를 참조하세요.
2. [Windows 시작] 단추를 클릭하고 **Powershell**을 입력한 다음 검색 결과에서 **PowerShell**을 클릭하여 PowerShell 세션을 시작합니다.
3. PowerShell 창에서 `login-azurermaccount` 명령을 입력하여 Azure [계정](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account)으로 로그인합니다. 아직 계정이 없는 경우 [평가판](https://azure.microsoft.com/offers/ms-azr-0044p)을 등록할 수 있습니다.
4. 브라우저에서 다음 스크립트를 복사합니다.
    ```powershell
    # Create a resource group
    New-AzureRmResourceGroup `
      -Name MyResourceGroup `
      -Location eastus
    
    # Create two subnets
    $Subnet1 = New-AzureRmVirtualNetworkSubnetConfig `
      -Name Public `
      -AddressPrefix 10.0.0.0/24
    $Subnet2 = New-AzureRmVirtualNetworkSubnetConfig `
      -Name Private `
      -AddressPrefix 10.0.1.0/24
    
    # Create a virtual network
    $Vnet=New-AzureRmVirtualNetwork `
      -ResourceGroupName MyResourceGroup `
      -Location eastus `
      -Name MyVnet `
      -AddressPrefix 10.0.0.0/16 `
      -Subnet $Subnet1,$Subnet2
    #
    ```
5. 스크립트를 실행하려면 PowerShell 창에서 마우스 오른쪽 단추로 클릭합니다.
6. 다음 명령을 복사하고 명령을 PowerShell 창에 붙여넣어 VNet에 대한 서브넷을 검토합니다.
    ```powershell
    $Vnet = $Vnet.subnets | Format-Table Name, AddressPrefix
    ```
7. **선택 사항:** 이 자습서에서 만든 리소스를 삭제하려면 이 문서의 [리소스 삭제](#delete-powershell) 섹션에서 설명하는 단계를 완료합니다.

## <a name="template"></a>템플릿

Azure Resource Manager 템플릿을 사용하여 VNet을 배포할 수 있습니다. 템플릿에 대한 자세한 내용은 [Resource Manager란?](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#template-deployment) 문서를 참조하세요. 템플릿에 액세스하여 해당 매개 변수에 대해 알아보려면 [두 서브넷이 있는 VNet 만들기 템플릿](https://azure.microsoft.com/resources/templates/101-vnet-two-subnets/) 웹 페이지를 참조하세요. [포털](#template-portal), [CLI](#template-cli) 또는 [PowerShell](#template-powershell)을 사용하여 템플릿을 배포할 수 있습니다.

**선택 사항:** 이 자습서에서 만든 리소스를 삭제하려면 이 문서의 [리소스 삭제](#delete) 섹션에서 설명하는 하위 섹션 단계를 완료합니다.

### <a name="template-portal"></a>포털

1. 브라우저에서 템플릿 [웹 페이지](https://azure.microsoft.com/resources/templates/101-vnet-two-subnets)를 엽니다.
2. **Azure에 배포** 단추를 클릭합니다. 그러면 Azure Portal 로그인 페이지가 열립니다.
3. Azure [계정](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account)을 사용하여 포털에 로그인합니다. 아직 계정이 없는 경우 [평가판](https://azure.microsoft.com/offers/ms-azr-0044p)을 등록할 수 있습니다.
4. 매개 변수에 대해 다음 값을 입력합니다.

    |매개 변수를 포함해야 합니다.|값|
    |---|---|
    |구독|사용 중인 구독을 선택합니다.|
    |리소스 그룹|MyResourceGroup|
    |위치|위치를 선택합니다.|
    |VNet 이름|MyVnet|
    |VNet 주소 접두사|10.0.0.0/16|
    |Subnet1Prefix|10.0.0.0/24|
    |Subnet1Name|공용|
    |Subnet2Prefix|10.0.1.0/24|
    |Subnet2Name|개인|

5. 사용 약관에 동의한 다음 **구매**를 클릭하여 VNet을 배포합니다.

### <a name="template-cli"></a>CLI

1. 인터넷 브라우저에서 Azure [Portal](https://portal.azure.com)을 열고 Azure [계정](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account)으로 로그인합니다. 아직 계정이 없는 경우 [평가판](https://azure.microsoft.com/offers/ms-azr-0044p)을 등록할 수 있습니다.
2. 포털 위쪽의 *리소스 검색* 상자 오른쪽에 있는 **>_** 아이콘을 클릭하여 Bash Azure Cloud Shell(미리 보기)을 시작합니다. 클라우드 셸 창이 포털의 아래쪽에 나타나고, 몇 초 후에 **username@Azure:~$** 프롬프트가 표시됩니다. 클라우드 셸은 포털에 인증된 자격 증명을 사용하여 Azure에 자동으로 로그인합니다.
3. 다음 명령을 입력하여 VNet에 대한 리소스 그룹을 만듭니다.  `az group create --name MyResourceGroup --location eastus`
4. 다음을 사용하여 템플릿을 배포할 수 있습니다.
    - **기본 매개 변수 값:** 다음 명령을 입력합니다.   `az group deployment create --resource-group MyResourceGroup --name VnetTutorial --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vnet-two-subnets/azuredeploy.json`
    - **사용자 지정 매개 변수 값:** 템플릿을 다운로드하여 수정한 후에 배포하거나, 명령줄에서 매개 변수를 사용하여 템플릿을 배포하거나, 별도의 매개 변수 파일을 사용하여 템플릿을 배포합니다. [두 서브넷이 있는 VNet 만들기 템플릿](https://azure.microsoft.com/resources/templates/101-vnet-two-subnets/) 웹 페이지에서 **GitHub에서 찾아보기** 단추를 클릭하여 템플릿 및 매개 변수 파일을 다운로드할 수 있습니다. GitHub에서 **azuredeploy.parameters.json** 또는 **azuredeploy.json** 파일을 클릭한 다음 해당 파일에 대한 **Raw**(원시 파일) 단추를 클릭합니다. 브라우저에서 내용을 복사하고 컴퓨터의 파일에 저장합니다. 템플릿에서 매개 변수 값을 수정하거나 별도의 매개 변수 파일을 통해 템플릿을 배포합니다.  

    이러한 방법을 사용하여 템플릿을 배포하는 방법에 대해 자세히 알아보려면 `az group deployment create --help`를 입력합니다.

### <a name="template-powershell"></a>PowerShell

1. 최신 버전의 Azure PowerShell [AzureRm](https://www.powershellgallery.com/packages/AzureRM/) 모듈을 설치합니다. Azure PowerShell을 처음 사용하는 경우 [Azure PowerShell 개요](/azure/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 문서를 참조하세요.
2. [Windows 시작] 단추를 클릭하고 **powershell**을 입력한 다음 검색 결과에서 **PowerShell**을 클릭하여 PowerShell 세션을 시작합니다.
3. PowerShell 창에서 `login-azurermaccount` 명령을 입력하여 Azure [계정](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account)으로 로그인합니다. 아직 계정이 없는 경우 [평가판](https://azure.microsoft.com/offers/ms-azr-0044p)을 등록할 수 있습니다.
4. 다음 명령을 입력하여 VNet에 대한 리소스 그룹을 만듭니다.  `New-AzureRmResourceGroup -Name MyResourceGroup -Location eastus`
5. 다음을 사용하여 템플릿을 배포할 수 있습니다.
    - **기본 매개 변수 값:** 이렇게 하려면 다음 명령을 입력합니다.   `New-AzureRmResourceGroupDeployment -Name VnetTutorial -ResourceGroupName MyResourceGroup -TemplateUri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vnet-two-subnets/azuredeploy.json`        
    - **사용자 지정 매개 변수 값:** 이렇게 하려면 템플릿을 다운로드하여 수정한 후에 배포하거나, 명령줄에서 매개 변수를 사용하여 템플릿을 배포하거나, 별도의 매개 변수 파일을 사용하여 템플릿을 배포하면 됩니다. [두 서브넷이 있는 VNet 만들기 템플릿](https://azure.microsoft.com/resources/templates/101-vnet-two-subnets/) 웹 페이지에서 **GitHub에서 찾아보기** 단추를 클릭하여 템플릿 및 매개 변수 파일을 다운로드할 수 있습니다. GitHub에서 **azuredeploy.parameters.json** 또는 **azuredeploy.json** 파일을 클릭한 다음 해당 파일에 대한 **Raw**(원시 파일) 단추를 클릭합니다. 브라우저에서 내용을 복사하고 컴퓨터의 파일에 저장합니다. 템플릿에서 매개 변수 값을 수정하거나 별도의 매개 변수 파일을 통해 템플릿을 배포합니다.  

    이러한 방법을 사용하여 템플릿을 배포하는 방법에 대해 자세히 알아보려면 `Get-Help New-AzureRmResourceGroupDeployment`를 입력합니다. 

## <a name="delete"></a>리소스 삭제
이 자습서를 완료한 후에는 사용 요금이 발생하지 않도록 리소스를 삭제할 수 있습니다. 리소스 그룹을 삭제하면 해당 리소스 그룹 내의 모든 리소스도 삭제됩니다.

### <a name="delete-portal"></a>포털

1. 포털에서 위쪽에 있는 *리소스 검색* 상자에서 *MyResourceGroup*을 입력합니다. 검색 결과에서 표시되는 **MyResourceGroup**을 클릭합니다.
2. 표시되는 MyResourceGroup 블레이드의 위쪽 근처에 있는 [삭제] 아이콘을 클릭합니다.
3. 삭제를 확인하려면 **리소스 그룹 이름 입력:** 상자에서 *MyResourceGroup*을 입력하고 **삭제**를 클릭합니다.

### <a name="delete-cli"></a>CLI

클라우드 셸 프롬프트에서 `az group delete --name MyResourceGroup --yes` 명령을 입력합니다.

### <a name="delete-powershell"></a>PowerShell

PowerShell 프롬프트에서 `Remove-AzureRmResourceGroup -Name MyResourceGroup` 명령을 입력합니다.

## <a name="next-steps"></a>다음 단계

- 모든 VNet 및 서브넷 설정에 대해 이해하려면 [VNet 관리](virtual-network-manage-network.md#view-vnet) 및 [서브넷 관리](virtual-network-manage-subnet.md#create-subnet) 문서를 참조하세요. 서로 다른 요구 사항을 충족하도록 프로덕션 VNet 및 서브넷을 만들 수 있는 다양한 옵션이 있습니다.
- [NSG(네트워크 보안 그룹)](virtual-networks-nsg.md)를 만들고 서브넷에 적용하여 인바운드 및 아웃바운드 서브넷 트래픽을 필터링합니다.
- [Windows](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 또는 [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) VM을 만들고 VNet에 연결합니다.
- [VNet 피어링](virtual-network-peering-overview.md)을 사용하여 VNet을 동일한 위치에 있는 다른 VNet에 연결합니다.
- [사이트 간 VPN(가상 사설망)](../vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 또는 [ExpressRoute](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 회로를 사용하여 VNet을 온-프레미스 네트워크에 연결합니다.

