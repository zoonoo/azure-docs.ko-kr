---
title: "Linux VM용 Azure 템플릿의 액세스 및 보안 | Microsoft Docs"
description: "Azure 가상 컴퓨터 DotNet Core 자습서"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: 07e47189-680e-4102-a8d4-5a8eb9c00213
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/21/2016
ms.author: nepeters
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 2fd9fd50be79e8af239101147e5ae0a39eb2dc07
ms.openlocfilehash: 7cef940b26003578a3b08c453ed4f91d3e617b2e
ms.lasthandoff: 03/01/2017

---
# <a name="access-and-security-in-azure-resource-manager-templates-for-linux-vms"></a>Linux VM용 Azure Resource Manager 템플릿의 액세스 및 보안

Azure에 호스트되는 응용 프로그램은 인터넷 또는 Azure와의 VPN/Express 경로 연결을 통해 액세스되어야 할 수 있습니다. Music Store 응용 프로그램 샘플을 사용할 경우 공용 IP 주소를 통해 인터넷에서 해당 웹 사이트를 사용할 수 있습니다. 액세스가 설정되면 응용 프로그램에 대한 연결 및 가상 컴퓨터 리소스에 대한 액세스가 자체적으로 보호되어야 합니다. 이 액세스 보안은 네트워크 보안 그룹을 통해 제공됩니다. 

이 문서에서는 샘플 Azure Resource Manager 템플릿에서 Music Store 응용 프로그램의 보안을 유지하는 방법을 자세히 설명합니다. 모든 종속성 및 고유한 구성이 강조 표시됩니다. 최상의 환경을 위해서는 솔루션 인스턴스를 Azure 구독에 미리 배포하고 Azure Resource Manager 템플릿을 따라 작업하는 것이 좋습니다. 전체 템플릿은 [Ubuntu의 Music Store 배포](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-linux)에서 확인할 수 있습니다. 

## <a name="public-ip-address"></a>공용 IP 주소
Azure 리소스에 대한 공용 액세스를 제공하려면 공용 IP 주소 리소스를 사용할 수 있습니다. 정적 또는 동적 IP 주소로 공용 IP 주소를 구성할 수 있습니다. 동적 주소가 사용되고 가상 컴퓨터를 중지된 후 할당 취소되면 해당 주소가 제거됩니다. 컴퓨터 다시 시작되면 다른 공용 IP 주소를 할당할 수 있습니다. IP 주소가 변경되지 않게 하려면 예약된 IP 주소를 사용할 수 있습니다. 

Visual Studio 새 리소스 추가 마법사를 사용하거나 템플릿에 유효한 JSON을 삽입하여 Azure Resource Manager 템플릿에 공용 IP 주소를 추가할 수 있습니다. 

[공용 IP 주소](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-linux/azuredeploy.json#L121)링크를 따라 Resource Manager 템플릿 내의 JSON 샘플을 볼 수 있습니다.

```json
{
  "apiVersion": "2015-06-15",
  "type": "Microsoft.Network/publicIPAddresses",
  "name": "[variables('publicipaddressName')]",
  "location": "[resourceGroup().location]",
  "tags": {
    "displayName": "public-ip-front"
  },
  "properties": {
    "publicIPAllocationMethod": "Dynamic",
    "dnsSettings": {
      "domainNameLabel": "[parameters('publicipaddressDnsName')]"
    }
  }
}
```

공용 IP 주소를 가상 네트워크 어댑터 또는 부하 분산 장치에 연결할 수 있습니다. 이 예제에서는 Music Store 웹 사이트 부하가 여러 가상 컴퓨터에서 분산되기 때문에 공용 IP 주소가 부하 분산 장치에 연결됩니다.

[공용 IP 주소와 부하 부산 장치 연결](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-linux/azuredeploy.json#L208)링크를 따라 Resource Manager 템플릿 내의 JSON 샘플을 볼 수 있습니다.

```json
"frontendIPConfigurations": [
  {
    "properties": {
      "publicIPAddress": {
        "id": "[resourceId('Microsoft.Network/publicIPAddresses', variables('publicipaddressName'))]"
      }
    },
    "name": "LoadBalancerFrontend"
  }
]
```

Azure Portal에 표시된 공용 IP 주소. 공용 IP 주소가 가상 컴퓨터가 아닌 부하 분산 장치에 연결되어 있는지 확인합니다. 네트워크 부하 분산 장치는 이 시리즈의 다음 문서에 자세히 나와 있습니다.

![공용 IP 주소](./media/virtual-machines-linux-dotnet-core/pubip.png)

Azure 공용 IP 주소에 대한 자세한 내용은 [Azure의 IP 주소](../virtual-network/virtual-network-ip-addresses-overview-arm.md)를 참조하세요.

## <a name="network-security-group"></a>네트워크 보안 그룹
Azure 리소스에 대한 액세스가 설정되면 이 액세스를 제한해야 합니다. Azure 가상 컴퓨터의 경우 네트워크 보안 그룹을 사용하여 보안 액세스가 설정됩니다. Music Store 응용 프로그램 샘플을 사용하면 가상 컴퓨터에 대한 모든 액세스가 제한됩니다. 단 http 액세스를 위한 포트 80과 SSH 액세스를 위한 포트 22는 예외입니다. Visual Studio 새 리소스 추가 마법사를 사용하거나 템플릿에 유효한 JSON을 삽입하여 Azure Resource Manager 템플릿에 네트워크 보안 그룹을 추가할 수 있습니다.

[네트워크 보안 그룹](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-linux/azuredeploy.json#L68)링크를 따라 Resource Manager 템플릿 내의 JSON 샘플을 볼 수 있습니다.

```json
{
  "apiVersion": "2015-05-01-preview",
  "type": "Microsoft.Network/networkSecurityGroups",
  "name": "[variables('nsgfront')]",
  "location": "[resourceGroup().location]",
  "tags": {
    "displayName": "nsg-front"
  },
  "properties": {
    "securityRules": [
      {
        "name": "http",
        "properties": {
          "description": "http endpoint",
          "protocol": "Tcp",
          "sourcePortRange": "*",
          "destinationPortRange": "80",
          "sourceAddressPrefix": "*",
          "destinationAddressPrefix": "*",
          "access": "Allow",
          "priority": 100,
          "direction": "Inbound"
        }
      },
      ........<truncated> 
    ]
  }
}
```

이 예제에서 네트워크 보안 그룹은 가상 네트워크 리소스에 선언된 서브넷 개체와 연결됩니다. 

[네트워크 보안 그룹과 가상 네트워크의 연결](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-linux/azuredeploy.json#L158)링크를 따라 Resource Manager 템플릿 내의 JSON 샘플을 볼 수 있습니다.

```json
"subnets": [
  {
    "name": "[variables('subnetName')]",
    "properties": {
      "addressPrefix": "10.0.0.0/24",
      "networkSecurityGroup": {
        "id": "[resourceId('Microsoft.Network/networkSecurityGroups', variables('networkSecurityGroup'))]"
      }
    }
  }
```

Azure Portal의 네트워크 보안 그룹은 다음과 같습니다. NSG는 서브넷 및/또는 네트워크 인터페이스와 연결될 수 있습니다. 이 경우 NSG는 서브넷에 연결됩니다. 이 구성에서 인바운드 규칙이 서브넷에 연결된 모든 가상 컴퓨터에 적용됩니다.

![네트워크 보안 그룹](./media/virtual-machines-linux-dotnet-core/nsg.png)

네트워크 보안 그룹에 대한 자세한 내용은 [네트워크 보안 그룹이란?](https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/)을 참조하세요.

## <a name="next-step"></a>다음 단계
<hr>

[3단계 - Azure Resource Manager 템플릿의 가용성 및 크기 조정](virtual-machines-linux-dotnet-core-4-availability-scale.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)


