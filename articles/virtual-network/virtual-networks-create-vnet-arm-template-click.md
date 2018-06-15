---
title: 가상 네트워크 만들기 | Azure Resource Manager 템플릿 | Microsoft Docs
description: Azure Resource Manager 템플릿을 사용하여 가상 네트워크를 만드는 방법을 알아봅니다.
services: virtual-network
documentationcenter: ''
author: jimdial
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 69530861-2f97-4a6e-b336-a7baf2690044
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2016
ms.author: jdial
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: bb096f64a6bc41ad2e75c058c7a9f00bbe480207
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/13/2018
ms.locfileid: "29880473"
---
# <a name="create-a-virtual-network-using-an-azure-resource-manager-template"></a>Azure Resource Manager 템플릿을 사용하여 가상 네트워크 만들기

[!INCLUDE [virtual-networks-create-vnet-intro](../../includes/virtual-networks-create-vnet-intro-include.md)]

Azure에는 Azure Resource Manager 및 클래식이라는 두 가지 배포 모델이 있습니다. Resource Manager 배포 모델을 통해 리소스를 만드는 것이 좋습니다. 두 가지 모델의 차이점에 대해 자세히 알아보려면 [Azure 배포 모델 이해](../azure-resource-manager/resource-manager-deployment-model.md) 문서를 참조하세요.
 
이 문서는 Azure Resource Manager 템플릿을 사용하여 Resource Manager 배포 모델을 통해 VNet을 만드는 방법을 설명합니다. 다른 도구를 사용하여 Resource Manager를 통해 VNet을 만들거나 다음 목록에서 다른 옵션을 선택하여 클래식 배포 모델을 통해 VNet을 만들 수도 있습니다.

> [!div class="op_single_selector"]
- [포털](quick-create-portal.md)
- [PowerShell](quick-create-powershell.md)
- [CLI](quick-create-cli.md)
- [템플릿](virtual-networks-create-vnet-arm-template-click.md)
- [포털(클래식)](virtual-networks-create-vnet-classic-pportal.md)
- [PowerShell(클래식)](virtual-networks-create-vnet-classic-netcfg-ps.md)
- [CLI(클래식)](virtual-networks-create-vnet-classic-cli.md)

GitHub에서 기존 Azure Resource Manager 템플릿을 다운로드한 후 수정하고 GitHub, PowerShell 및 Azure CLI에서 템플릿을 배포하는 방법을 알아봅니다.

변경하지 않고 GitHub에서 바로 Azure Resource Manager 템플릿을 배포하는 경우 [GitHub에서 템플릿 배포](#deploy-the-arm-template-by-using-click-to-deploy)로 건너뜁니다.

[!INCLUDE [virtual-networks-create-vnet-scenario-include](../../includes/virtual-networks-create-vnet-scenario-include.md)]

## <a name="download-and-understand-the-azure-resource-manager-template"></a>Azure 리소스 관리자 템플릿 다운로드 및 이해
GitHub에서 VNet 및 두 개의 서브넷을 만들기 위한 기존 템플릿을 다운로드하고 원하는 대로 변경한 후 다시 사용할 수 있습니다. 이렇게 하려면 다음 단계를 완료합니다.

1. [샘플 템플릿 페이지](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vnet-two-subnets)로 이동합니다.
2. **azuredeploy.json**을 클릭하고 **RAW**를 클릭합니다.
3. 파일을 컴퓨터의 로컬 폴더에 저장합니다.
4. 템플릿에 익숙한 경우 7단계로 건너뜁니다.
5. 저장한 파일을 열고 줄 5에서 **parameters** 아래의 내용을 확인합니다. Azure 리소스 관리자 템플릿 매개 변수는 배포하는 동안 채울 수 있는 값에 대한 자리 표시자를 제공합니다.
   
   | 매개 변수 | 설명 |
   | --- | --- |
   | **vnetName** |새 VNet의 이름 |
   | **addressPrefix** |CIDR 형식의 VNet에 대한 주소 공간 |
   | **subnet1Name** |첫 번째 VNet의 이름 |
   | **subnet1Prefix** |첫 번째 서브넷의 CIDR 블록 |
   | **subnet2Name** |두 번째 VNet의 이름 |
   | **subnet2Prefix** |두 번째 서브넷의 CIDR 블록 |
   
   > [!IMPORTANT]
   > GitHub에서 유지 관리되는 Azure 리소스 관리자 템플릿은 시간이 지나면서 달라질 수 있습니다. 템플릿을 사용하기 전에 확인합니다.
   > 
   > 
6. **resources** 아래의 내용을 확인하고 다음을 검토합니다.
   
   * **type**. 템플릿에 의해 생성되는 리소스의 유형입니다. 이 경우 VNet를 나타내는 **Microsoft.Network/virtualNetworks**입니다.
   * **이름**: 리소스의 이름입니다. **[parameters(‘vnetName’)]** 이 사용됩니다. 이 경우 배포 중에 사용자 또는 매개 변수 파일에 의한 입력으로 이름이 제공됩니다.
   * **properties**. 리소스의 속성 목록입니다. 이 템플릿은 VNet 생성 동안 주소 공간 및 서브넷 속성을 사용합니다.
7. [샘플 템플릿 페이지](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vnet-two-subnets)로 다시 이동합니다.
8. **azuredeploy-paremeters.json**을 클릭하고 **RAW**를 클릭합니다.
9. 파일을 컴퓨터의 로컬 폴더에 저장합니다.
10. 방금 저장한 파일을 열고 매개 변수 값을 편집합니다. 다음 값을 사용하여 이 시나리오에 설명된 VNet을 배포합니다.

    ```json
        {
          "vnetName": {
              "value": "TestVNet"
          },
          "addressPrefix": {
              "value": "192.168.0.0/16"
          },
          "subnet1Name": {
              "value": "FrontEnd"
          },
          "subnet1Prefix": {
            "value": "192.168.1.0/24"
          },
          "subnet2Name": {
              "value": "BackEnd"
          },
          "subnet2Prefix": {
              "value": "192.168.2.0/24"
          }
        }
    ```

11. 파일을 저장합니다.


## <a name="deploy-the-template-using-powershell"></a>PowerShell을 사용하여 템플릿 배포

PowerShell을 사용하여 다운로드한 템플릿을 배포하려면 다음 단계를 완료합니다.

1. [Azure PowerShell 설치 및 구성](/powershell/azure/overview) 문서에 나오는 단계를 완료하여 Azure PowerShell을 설치 및 구성합니다.
2. 다음 명령을 실행하여 새 리소스 그룹을 만듭니다.

    ```powershell
    New-AzureRmResourceGroup -Name TestRG -Location centralus
    ```

    명령은 *미국 중부* Azure 지역에서 *TestRG*라는 리소스 그룹을 만듭니다. 리소스 그룹에 대한 자세한 내용은 [Azure 리소스 관리자 개요](../azure-resource-manager/resource-group-overview.md)를 참조하세요.

    예상 출력:

        ResourceGroupName : TestRG
        Location          : centralus
        ProvisioningState : Succeeded
        Tags              :
        Permissions       :
                            Actions  NotActions
                            =======  ==========
                            *
        ResourceId        : /subscriptions/[Id]/resourceGroups/TestRG

3. 위에서 다운로드하고 수정한 템플릿 및 매개 변수를 사용하여 새 VNet을 배포하기 위해 다음 명령을 실행합니다.

    ```powershell
    New-AzureRmResourceGroupDeployment -Name TestVNetDeployment -ResourceGroupName TestRG `
    -TemplateFile C:\ARM\azuredeploy.json -TemplateParameterFile C:\ARM\azuredeploy-parameters.json
    ```

    예상 출력:
   
        DeploymentName    : TestVNetDeployment
        ResourceGroupName : TestRG
        ProvisioningState : Succeeded
        Timestamp         : [Date and time]
        Mode              : Incremental
        TemplateLink      :
        Parameters        :
                            Name             Type                       Value
                            ===============  =========================  ==========
                            location         String                     Central US
                            vnetName         String                     TestVNet
                            addressPrefix    String                     192.168.0.0/16
                            subnet1Prefix    String                     192.168.1.0/24
                            subnet1Name      String                     FrontEnd
                            subnet2Prefix    String                     192.168.2.0/24
                            subnet2Name      String                     BackEnd
   
        Outputs           :
4. 새 VNet의 속성을 보려면 다음 명령을 실행합니다.

    ```powershell
    Get-AzureRmVirtualNetwork -ResourceGroupName TestRG -Name TestVNet
    ```

    예상 출력:

        Name              : TestVNet
        ResourceGroupName : TestRG
        Location          : centralus
        Id                : /subscriptions/[Id]/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet
        Etag              : W/"[Id]"
        ProvisioningState : Succeeded
        Tags              :
        AddressSpace      : {
                              "AddressPrefixes": [
                                "192.168.0.0/16"
                              ]
                            }
        DhcpOptions       : {
                              "DnsServers": null
                            }
        NetworkInterfaces : null
        Subnets           : [
                              {
                                "Name": "FrontEnd",
                                "Etag": "W/\"[Id]\"",
                                "Id": "/subscriptions/[Id]/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd",
                                "AddressPrefix": "192.168.1.0/24",
                                "IpConfigurations": [],
                                "NetworkSecurityGroup": null,
                                "RouteTable": null,
                                "ProvisioningState": "Succeeded"
                              },
                              {
                                "Name": "BackEnd",
                                "Etag": "W/\"[Id]\"",
                                "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/BackEnd",
                                "AddressPrefix": "192.168.2.0/24",
                                "IpConfigurations": [],
                                "NetworkSecurityGroup": null,
                                "RouteTable": null,
                                "ProvisioningState": "Succeeded"
                              }
                            ]

## <a name="deploy-the-template-using-click-to-deploy"></a>클릭하여 배포를 사용하여 템플릿 배포

Microsoft에서 유지 관리하는 GitHub 리포지토리에 대한 미리 정의된 Azure Resource Manager 템플릿 업로드를 다시 사용하고 해당 커뮤니티를 열 수 있습니다. 이러한 템플릿은 GitHub에서 바로 배포하거나 다운로드한 후 필요에 맞게 수정할 수 있습니다. 두 서브넷으로 VNet을 만드는 템플릿을 배포하려면 다음 단계를 완료합니다.

1. 브라우저에서 [https://github.com/Azure/azure-quickstart-templates](https://github.com/Azure/azure-quickstart-templates)으로 이동합니다.
2. 템플릿 목록 아래로 스크롤하여 **101-vnet-two-subnets**를 클릭합니다. 아래와 같이 **README.md** 파일을 선택합니다.

    ![Github의 READEME.md 파일](./media/virtual-networks-create-vnet-arm-template-click-include/figure1.png)

3. **Deploy to Azure**를 클릭합니다. 필요한 경우 Azure 로그인 자격 증명을 입력합니다. 
4. **매개 변수** 블레이드에서 새 VNet을 만드는 데 사용할 값을 입력하고 **확인**을 클릭합니다. 다음 그림에서는 이 시나리오에 대한 값을 보여 줍니다.
   
    ![ARM 템플릿 매개 변수](./media/virtual-networks-create-vnet-arm-template-click-include/figure2.png)

5. **리소스 그룹**을 클릭하고 VNet을 추가할 리소스 그룹을 클릭하거나 **새로 만들기**를 클릭하여 새 리소스 그룹에 VNet을 추가합니다. 다음 그림은 **TestRG**라는 새 리소스 그룹에 대한 리소스 그룹 설정을 보여 줍니다.

    ![리소스 그룹](./media/virtual-networks-create-vnet-arm-template-click-include/figure3.png)

6. 필요한 경우 VNet에 맞게 **구독** 및 **위치** 설정을 변경합니다.
7. VNet을 **시작 보드**에 타일로 표시하지 않으려면 **시작 보드에 고정**을 사용하지 않도록 설정합니다.
8. **약관**을 클릭하고 약관을 읽은 후 **구입**을 클릭하여 동의합니다. 
9. **만들기**를 클릭하여 VNet을 만듭니다.
   
    ![Preview 포털에서 배포 타일 제출](./media/virtual-networks-create-vnet-arm-template-click-include/figure4.png)

10. 배포가 완료되면 Azure Portal에서 **모든 서비스**를 클릭하고 표시되는 필터 상자에 *가상 네트워크*를 입력한 다음, 가상 네트워크를 클릭하여 가상 네트워크 블레이드를 표시합니다. 블레이드에서 *TestVNet*을 클릭합니다. *TestVNet* 블레이드에서 다음 그림과 같이 만들어진 서브넷을 보려면 **서브넷**을 클릭합니다.
    
     ![Preview 포털에서 VNet 만들기](./media/virtual-networks-create-vnet-arm-template-click-include/figure5.png)

## <a name="next-steps"></a>다음 단계

연결 방법 알아보기:

- 가상 머신(VM)에서 가상 네트워크 연결은 [Windows VM 만들기](../virtual-machines/virtual-machines-windows-hero-tutorial.md) 또는 [Linux VM 만들기](../virtual-machines/linux/quick-create-portal.md) 문서를 참조하세요. 해당 문서의 단계에서 VNet 및 서브넷을 만드는 대신 기존 VNet 및 서브넷을 VM에 연결하도록 선택할 수 있습니다.
- 가상 네트워크에서 다른 가상 네트워크 연결은 [VNet 연결](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md) 문서를 참조하세요.
- 가상 네트워크에서 온-프레미스 네트워크 연결은 사이트 간 VPN(가상 사설망) 또는 ExpressRoute 회로를 사용합니다. 자세한 내용은 [사이트 간 VPN을 사용하여 VNet을 온-프레미스 네트워크에 연결](../vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md) 및 [VNet을 ExpressRoute 회선에 연결](../expressroute/expressroute-howto-linkvnet-arm.md) 문서를 참조하세요.
