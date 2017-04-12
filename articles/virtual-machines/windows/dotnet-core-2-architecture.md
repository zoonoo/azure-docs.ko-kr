---
title: "Azure Resource Manager 템플릿을 사용하여 WIndows 계산 리소스 배포 | Microsoft Docs"
description: "Azure 가상 컴퓨터 DotNet Core 자습서"
services: virtual-machines-windows
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: b026fe81-1bc1-4899-ac32-886091671498
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 11/21/2016
ms.author: nepeters
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 197ebd6e37066cb4463d540284ec3f3b074d95e1
ms.openlocfilehash: 6f4b947d767deea3b1a7cb870f659d718fa8ab10
ms.lasthandoff: 03/31/2017


---
# <a name="application-architecture-with-azure-resource-manager-templates-for-windows-vms"></a>Windows VM용 Azure Resource Manager 템플릿을 사용하는 응용 프로그램 아키텍처

Azure Resource Manager 배포를 개발할 때 계산 요구 사항을 Azure 리소스 및 서비스에 매핑해야 합니다. 응용 프로그램이 몇 개의 http 끝점, 데이터베이스 및 데이터 캐싱 서비스로 구성되면 이러한 각 데이터는 구성 요소를 호스트하는 Azure 리소스를 합리적으로 할당해야 합니다. 예를 들어 샘플 Music Store 응용 프로그램에는 가상 컴퓨터에 호스트되는 웹 응용 프로그램과 Azure SQL Database에 호스트되는 SQL Database가 포함됩니다. 

이 문서에서는 샘플 Azure Resource Manager 템플릿에서 Music Store 계산 리소스를 구성하는 방법을 자세히 설명합니다. 모든 종속성 및 고유한 구성이 강조 표시됩니다. 최상의 환경을 위해서는 솔루션 인스턴스를 Azure 구독에 미리 배포하고 Azure Resource Manager 템플릿을 따라 작업하는 것이 좋습니다. 전체 템플릿은 [Windows의 Music Store 배포](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-windows)에서 확인할 수 있습니다.

## <a name="virtual-machine"></a>가상 컴퓨터
Music Store 응용 프로그램에는 고객이 음악을 찾아보고 구매할 수 있는 웹 응용 프로그램이 포함되어 있습니다. 웹 응용 프로그램을 호스트할 수 있는 여러 Azure 서비스가 있지만 이 예제에서는 가상 컴퓨터가 사용됩니다. 샘플 Music Store 템플릿을 사용하여 가상 컴퓨터가 배포되고, 웹 서버가 설치되며 Music Store 웹 사이트가 설치 및 구성됩니다. 이 문서에서는 목적에 맞게 가상 컴퓨터 배포만 자세히 설명합니다. 웹 서버와 응용 프로그램의 구성은 다음 문서에서 자세히 설명합니다.

Visual Studio 새 리소스 추가 마법사를 사용하거나 배포 템플릿에 유효한 JSON을 삽입하여 가상 컴퓨터를 템플릿에 추가할 수 있습니다. 가상 컴퓨터를 배포할 때는 몇 가지 관련 리소스도 필요합니다. Visual Studio를 사용하여 템플릿을 만들 경우 이러한 리소스가 자동으로 생성됩니다. 템플릿을 수동으로 생성하는 경우 이러한 리소스를 삽입하고 구성해야 합니다.

[가상 컴퓨터 JSON](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-windows/azuredeploy.json#L285)링크를 따라 Resource Manager 템플릿 내의 JSON 샘플을 볼 수 있습니다.

```json
{
  {
  "apiVersion": "2015-06-15",
  "type": "Microsoft.Compute/virtualMachines",
  "name": "[concat(variables('vmName'),copyindex())]",
  "location": "[resourceGroup().location]",
  "copy": {
    "name": "virtualMachineLoop",
    "count": "[parameters('numberOfInstances')]"
  },
  "tags": {
    "displayName": "virtual-machine"
  },
  "dependsOn": [
    "[concat('Microsoft.Storage/storageAccounts/', variables('vhdStorageName'))]",
    "[concat('Microsoft.Compute/availabilitySets/', variables('availabilitySetName'))]",
    "nicLoop"
  ],
  "properties": {
    "availabilitySet": {
      "id": "[resourceId('Microsoft.Compute/availabilitySets', variables('availabilitySetName'))]"
    },
  ........<truncated>  
}
```

일단 배포되면 Azure Portal에서 가상 컴퓨터 속성을 볼 수 있습니다.

![가상 컴퓨터](./media/dotnet-core-2-architecture/vm-win.png)

## <a name="storage-account"></a>저장소 계정
저장소 계정에는 많은 저장소 옵션과 기능이 있습니다. Azure 가상 컴퓨터의 컨텍스트에서 저장소 계정은 가상 컴퓨터의 가상 하드 드라이브와 추가 데이터 디스크를 보유합니다. Music Store 샘플에는 배포에 각 가상 컴퓨터의 가상 하드 드라이브를 보유하기 위한 단일 저장소 계정이 포함되어 있습니다. 

[저장소 계정](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-windows/azuredeploy.json#L98)링크를 따라 Resource Manager 템플릿 내의 JSON 샘플을 볼 수 있습니다.

```json
{
  "apiVersion": "2015-06-15",
  "type": "Microsoft.Storage/storageAccounts",
  "name": "[variables('vhdStorageName')]",
  "location": "[resourceGroup().location]",
  "tags": {
    "displayName": "storage-account"
  },
  "properties": {
    "accountType": "[variables('vhdStorageType')]"
  }
}
```

저장소 계정은 가상 컴퓨터의 Resource Manager 템플릿 선언 내에 있는 가상 컴퓨터와 연결됩니다. 

[가상 컴퓨터 및 저장소 계정 연결](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-windows/azuredeploy.json#L321)링크를 따라 Resource Manager 템플릿 내의 JSON 샘플을 볼 수 있습니다.

```json
"osDisk": {
  "name": "osdisk",
  "vhd": {
    "uri": "[concat(reference(concat('Microsoft.Storage/storageAccounts/',variables('vhdStorageName')), '2015-06-15').primaryEndpoints.blob,'vhds/osdisk', copyindex(), '.vhd')]"
  },
  "caching": "ReadWrite",
  "createOption": "FromImage"
}
```

배포 후에는 Azure Portal에서 해당 저장소 계정을 볼 수 있습니다.

![저장소 계정](./media/dotnet-core-2-architecture/storacct-win.png)

Storage 계정 blob 컨테이너를 클릭하면 템플릿을 통해 배포된 각 가상 컴퓨터의 가상 하드 드라이버 파일을 볼 수 있습니다.

![가상 하드 드라이브](./media/dotnet-core-2-architecture/vhd-win.png)

Azure 저장소에 대한 자세한 내용은 [저장소 설명서](https://azure.microsoft.com/documentation/services/storage/)를 참조하세요.

## <a name="virtual-network"></a>가상 네트워크
가상 컴퓨터에 다른 가상 컴퓨터 및 Azure 리소스와 통신하는 기능과 같은 내부 네트워킹이 필요한 경우 Azure 가상 네트워크가 필요합니다.  가상 네트워크가 형성되었다고 해서 인터넷을 통해 가상 컴퓨터에 액세스할 수 있는 것은 아닙니다. 공용 연결에는 공용 IP 주소가 필요합니다. 이 내용은 이 시리즈의 뒷부분에서 자세히 설명합니다.

[가상 네트워크 및 서브넷](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-windows/azuredeploy.json#L126)링크를 따라 Resource Manager 템플릿 내의 JSON 샘플을 볼 수 있습니다.

```json
{
  "apiVersion": "2015-06-15",
  "type": "Microsoft.Network/virtualNetworks",
  "name": "[variables('virtualNetworkName')]",
  "location": "[resourceGroup().location]",
  "dependsOn": [
    "[concat('Microsoft.Network/networkSecurityGroups/', variables('networkSecurityGroup'))]"
  ],
  "tags": {
    "displayName": "virtual-network"
  },
  "properties": {
    "addressSpace": {
      "addressPrefixes": [
        "10.0.0.0/24"
      ]
    },
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
    ]
  }
}
```

Azure Portal에서 가상 네트워크는 다음 이미지와 비슷합니다. 템플릿을 사용하여 배포한 모든 가상 컴퓨터가 가상 네트워크에 연결되어 있는지 확인합니다.

![가상 네트워크](./media/dotnet-core-2-architecture/vnet-win.png)

## <a name="network-interface"></a>네트워크 인터페이스
 네트워크 인터페이스는 가상 컴퓨터를 가상 네트워크에, 좀 더 구체적으로 말해서 가상 네트워크에 정의된 서브넷에 연결합니다. 

 [네트워크 인터페이스](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-windows/azuredeploy.json#L156)링크를 따라 Resource Manager 템플릿 내의 JSON 샘플을 볼 수 있습니다.

```json
{
  "apiVersion": "2015-06-15",
  "type": "Microsoft.Network/networkInterfaces",
  "name": "[concat(variables('networkInterfaceName'), copyindex())]",
  "location": "[resourceGroup().location]",
  "tags": {
    "displayName": "network-interface"
  },
  "copy": {
    "name": "nicLoop",
    "count": "[parameters('numberOfInstances')]"
  },
  "dependsOn": [
    "[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]",
    "[concat('Microsoft.Network/loadBalancers/', variables('loadBalancerName'))]",
    "[concat('Microsoft.Network/publicIPAddresses/', variables('publicIpAddressName'))]",
    "[concat('Microsoft.Network/loadBalancers/', variables('loadBalancerName'), '/inboundNatRules/', 'RDP-VM', copyIndex())]"
  ],
  "properties": {
    "ipConfigurations": [
      {
        "name": "ipconfig",
        "properties": {
          "privateIPAllocationMethod": "Dynamic",
          "subnet": {
            "id": "[variables('subnetRef')]"
          },
          "loadBalancerBackendAddressPools": [
            {
              "id": "[variables('lbPoolID')]"
            }
          ],
          "loadBalancerInboundNatRules": [
            {
              "id": "[concat(variables('lbID'),'/inboundNatRules/RDP-VM', copyIndex())]"
            }
          ]
        }
      }
    ]
  }
}
```

각 가상 컴퓨터 리소스에는 네트워크 프로필이 포함됩니다. 네트워크 인터페이스는 이 프로필에서 가상 컴퓨터와 연결됩니다.  

[가상 컴퓨터 네트워크 프로필](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-windows/azuredeploy.json#L330)링크를 따라 Resource Manager 템플릿 내의 JSON 샘플을 볼 수 있습니다.

```json
"networkProfile": {
  "networkInterfaces": [
    {
      "id": "[resourceId('Microsoft.Network/networkInterfaces', concat(variables('networkInterfaceName'), copyindex()))]"
    }
  ]
}
```

Azure Portal에서 네트워크 인터페이스는 다음 이미지와 비슷합니다. 내부 IP 주소와 가상 컴퓨터 연결은 네트워크 인터페이스 리소스에서 볼 수 있습니다.

![네트워크 인터페이스](./media/dotnet-core-2-architecture/nic-win.png)

Azure 가상 네트워크에 대한 자세한 내용은 [Azure 가상 네트워크 설명서](https://azure.microsoft.com/documentation/services/virtual-network/)를 참조하세요.

## <a name="azure-sql-database"></a>Azure SQL 데이터베이스
Music Store 웹 사이트를 호스트하는 가상 컴퓨터 외에도, Azure SQL Database가 Music Store 데이터베이스를 호스트하기 위해 배포됩니다. 여기서 Azure SQL Database를 사용하면 또 다른 가상 컴퓨터 집합을 사용하지 않아도 되며 크기 조정 및 가용성이 서비스에 기본적으로 제공된다는 장점이 있습니다.

Visual Studio 새 리소스 추가 마법사를 사용하거나 템플릿에 유효한 JSON을 삽입하여 Azure SQL Database를 추가할 수 있습니다. SQL Server 리소스에는 SQL 인스턴스에 대해 관리자 권한이 부여되는 사용자 이름 및 암호가 포함됩니다. 또한 SQL 방화벽 리소스도 추가됩니다. 기본적으로 Azure에서 호스트되는 응용 프로그램은 SQL 인스턴스에 연결할 수 있습니다. SQL Server Management Studio와 같은 외부 응용 프로그램이 SQL 인스턴스에 연결할 수 있게 하려면 방화벽을 구성해야 합니다. Music Store 데모에 맞게 기본 구성에는 문제가 없습니다. 

[Azure SQL DB](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-windows/azuredeploy.json#L379) 링크를 따라 Resource Manager 템플릿 내의 JSON 샘플을 볼 수 있습니다.

```json
{
  "apiVersion": "2014-04-01-preview",
  "type": "Microsoft.Sql/servers",
  "name": "[variables('musicstoresqlName')]",
  "location": "[resourceGroup().location]",
  "dependsOn": [],
  "tags": {
    "displayName": "sql-music-store"
  },
  "properties": {
    "administratorLogin": "[parameters('adminUsername')]",
    "administratorLoginPassword": "[parameters('adminPassword')]"
  },
  "resources": [
    {
      "apiVersion": "2014-04-01-preview",
      "type": "firewallrules",
      "name": "firewall-allow-azure",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[concat('Microsoft.Sql/servers/', variables('musicstoresqlName'))]"
      ],
      "properties": {
        "startIpAddress": "0.0.0.0",
        "endIpAddress": "0.0.0.0"
      }
    }
  ]
}
```

Azure Portal에 표시되는 SQL Server 및 MusicStore 데이터베이스 보기.

![SQL Server](./media/dotnet-core-2-architecture/sql-win.png)

Azure SQL Database 배포에 대한 자세한 내용은 [Azure SQL Database 설명서](https://azure.microsoft.com/documentation/services/sql-database/)를 참조하세요.

## <a name="next-step"></a>다음 단계
<hr>

[2단계 - Azure Resource Manager 템플릿의 액세스 및 보안](dotnet-core-3-access-security.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)


