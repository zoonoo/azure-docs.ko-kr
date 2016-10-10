<properties
   pageTitle="Azure Resource Manager 템플릿의 가용성 및 크기 조정 | Microsoft Azure"
   description="Azure 가상 컴퓨터 DotNet Core 자습서"
   services="virtual-machines-linux"
   documentationCenter="virtual-machines"
   authors="neilpeterson"
   manager="timlt"
   editor="tysonn"
   tags="azure-service-management"/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure"
   ms.date="09/21/2016"
   ms.author="nepeters"/>

# Azure Resource Manager 템플릿의 가용성 및 크기 조정

가용성 및 크기 조정은 가동 시간 및 요구를 충족하는 능력을 나타냅니다. 응용 프로그램의 가동 시간이 99.9% 이상이어야 하면 여러 개의 동시 계산 리소스를 허용하는 아키텍처가 필요합니다. 예를 들어 단일 웹 사이트를 유지하지 않고, 가용성 수준이 좀 더 높은 구성에 동일한 사이트의 여러 인스턴스를 포함하고 부하 분산 기술을 사용합니다. 이 구성에서 응용 프로그램의 인스턴스 하나를 유지 관리에 사용하고 나머지는 계속 작동되도록 할 수 있습니다. 크기 조정은 요구를 처리하는 응용 프로그램의 능력을 나타냅니다. 부하 분산된 응용 프로그램을 사용하여 풀에서 인스턴스를 추가하거나 제거하면 수요에 맞게 크기를 응용 프로그램 규모를 조정할 수 있습니다.

이 문서에서는 가용성 및 크기 조정을 위해 Music Store 샘플 배포를 구성하는 방법을 자세히 설명합니다. 모든 종속성 및 고유한 구성이 강조 표시됩니다. 최상의 환경을 위해서는 솔루션 인스턴스를 Azure 구독에 미리 배포하고 Azure Resource Manager 템플릿을 따라 작업하는 것이 좋습니다. 전체 템플릿은 [Ubuntu의 Music Store 배포](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-linux)에서 확인할 수 있습니다.

## 가용성 집합

가용성 집합은 여러 물리적 호스트 및 기타 인프라 구성 요소(예: 전원 공급 장치 및 물리적 네트워킹 하드웨어)에 Azure 가상 컴퓨터를 논리적으로 분산합니다. 가용성 집합은 유지 관리, 장치 오류 또는 기타 가동 중단 시간 동안 모든 가상 컴퓨터가 영향을 받지는 않도록 합니다. Visual Studio 새 리소스 추가 마법사를 사용하거나 템플릿에 유효한 JSON을 삽입하여 Azure Resource Manager 템플릿에 가용성 집합을 추가할 수 있습니다.

[가용성 집합](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-linux/azuredeploy.json#L387) 링크를 따라 Resource Manager 템플릿 내의 JSON 샘플을 볼 수 있습니다.


```none
{
  "apiVersion": "2015-06-15",
  "type": "Microsoft.Compute/availabilitySets",
  "name": "[variables('availabilitySetName')]",
  "location": "[resourceGroup().location]",
  "dependsOn": [],
  "tags": {
    "displayName": "avalibility-set"
  },
  "properties": {
    "platformUpdateDomainCount": 5,
    "platformFaultDomainCount": 3
  }
},
```

가용성 집합은 가상 컴퓨터 리소스의 속성으로 선언됩니다.

[가상 컴퓨터와 가용성 집합 연결](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-linux/azuredeploy.json#L313) 링크를 따라 Resource Manager 템플릿 내의 JSON 샘플을 볼 수 있습니다.


```none
"properties": {
  "availabilitySet": {
    "id": "[resourceId('Microsoft.Compute/availabilitySets', variables('availabilitySetName'))]"
  },
```
Azure Portal의 가용성 집합. 각 가상 컴퓨터 및 구성 방법에 대한 세부 정보가 여기에 나와 있습니다.

![가용성 집합](./media/virtual-machines-linux-dotnet-core/aset.png)

가용성 집합에 대한 자세한 내용은 [가상 컴퓨터의 가용성 관리](./virtual-machines-linux-manage-availability.md)를 참조하세요.

## 네트워크 부하 분산 장치

가용성 집합은 응용 프로그램 내결함성을 제공하지만 부하 분산 장치는 단일 네트워크 주소에서 응용 프로그램의 여러 인스턴스를 사용할 수 있게 합니다. 응용 프로그램의 여러 인스턴스를 각각 하나의 부하 분산 장치에 연결된 여러 가상 컴퓨터에서 호스트할 수 있습니다. 응용 프로그램에 액세스하면 부하 분산 장치는 들어오는 요청을 연결된 멤버 간에 라우팅합니다. Visual Studio 새 리소스 추가 마법사를 사용하거나 적절한 형식의 JSON 리소스를 삽입하여 Azure Resource Manager 템플릿에 부하 분산 장치를 추가할 수 있습니다.

[네트워크 부하 분산 장치](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-linux/azuredeploy.json#L208) 링크를 따라 Resource Manager 템플릿 내의 JSON 샘플을 볼 수 있습니다.

```none
{
  "apiVersion": "2015-06-15",
  "type": "Microsoft.Network/loadBalancers",
  "name": "[variables('loadBalancerName')]",
  "location": "[resourceGroup().location]",
  "tags": {
    "displayName": "load-balancer-front"
  },
  ........<truncated>
}
```

샘플 응용 프로그램이 공용 IP 주소를 사용하여 인터넷에 노출되므로 이 주소는 부하 분산 장치에 연결됩니다.

[네트워크 부하 부산 장치와 공용 IP 주소 연결](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-linux/azuredeploy.json#L221) 링크를 따라 Resource Manager 템플릿 내의 JSON 샘플을 볼 수 있습니다.

```none
"frontendIPConfigurations": [
  {
    "properties": {
      "publicIPAddress": {
        "id": "[resourceId('Microsoft.Network/publicIPAddresses', variables('publicipaddressName'))]"
      }
    },
    "name": "LoadBalancerFrontend"
  }
],
```

Azure Portal의 네트워크 부하 분산 장치 개요에 공용 IP 주소와의 연결 관계가 나와 있습니다.

![네트워크 부하 분산 장치](./media/virtual-machines-linux-dotnet-core/nlb.png)

## 부하 분산 장치 규칙

부하 분산 장치를 사용하면 계획된 리소스 간에 트래픽이 분산되는 방식을 제어하는 규칙이 구성됩니다. 샘플 Music Store 응용 프로그램을 사용할 경우 트래픽은 공용 IP 주소의 포트 80에 도착하고 모든 가상 컴퓨터의 포트 80에 분산됩니다.

[부하 분산 장치 규칙](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-linux/azuredeploy.json#L270) 링크를 따라 Resource Manager 템플릿 내의 JSON 샘플을 볼 수 있습니다.


```none
"loadBalancingRules": [
  {
    "name": "[variables('loadBalencerRule')]",
    "properties": {
      "frontendIPConfiguration": {
        "id": "[concat(resourceId('Microsoft.Network/loadBalancers', variables('loadBalancerName')), '/frontendIPConfigurations/LoadBalancerFrontend')]"
      },
      "backendAddressPool": {
        "id": "[variables('lbPoolID')]"
      },
      "protocol": "Tcp",
      "frontendPort": 80,
      "backendPort": 80,
      "enableFloatingIP": false,
      "idleTimeoutInMinutes": 5,
      "probe": {
        "id": "[variables('lbProbeID')]"
      }
    }
  }
],
```

포털의 네트워크 부하 분산 장치 규칙 보기.

![네트워크 부하 분산 장치 규칙](./media/virtual-machines-linux-dotnet-core/lbrule.png)

## 부하 분산 장치 검색

부하 분산 장치는 요청이 실행 중인 시스템에만 제공되도록 각 가상 컴퓨터를 모니터링해야 합니다. 이 모니터링은 미리 정의된 포트를 지속적으로 검색하여 진행됩니다. Music Store 배포는 포함된 모든 가상 컴퓨터에서 포트 80을 검색하도록 구성됩니다.

[부하 분산 장치 검색](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-linux/azuredeploy.json#L257) 링크를 따라 Resource Manager 템플릿 내의 JSON 샘플을 볼 수 있습니다.


```none
"probes": [
  {
    "properties": {
      "protocol": "Tcp",
      "port": 80,
      "intervalInSeconds": 15,
      "numberOfProbes": 2
    },
    "name": "lbprobe"
  }
]
```

Azure Portal에서 살펴본 부하 분산 장치 검색.

![네트워크 부하 분산 장치 검색](./media/virtual-machines-linux-dotnet-core/lbprobe.png)

## 인바운드 NAT 규칙

부하 분산 장치를 사용할 때 각 가상 컴퓨터에 부하가 분산되지 않은 액세스를 제공하는 규칙을 적용해야 합니다. 예를 들어 각 가상 컴퓨터에 대한 SSH 연결을 만들 때 이 트래픽의 부하가 분산되지 않아야 하며, 미리 결정된 경로가 구성되어야 합니다. 미리 결정된 경로는 인바운드 NAT 규칙 리소스를 사용하여 구성됩니다. 이 리소스를 사용하면 인바운드 통신을 개별 가상 컴퓨터에 매핑할 수 있습니다.

Music Store 응용 프로그램을 사용할 경우 5000에서 시작하는 포트가 SSH 액세스를 위해 각 가상 컴퓨터의 포트 22에 매핑됩니다. `copyindex()` 함수는 두 번째 가상 컴퓨터가 들어오는 포트 5001을 수신하고, 세 번째가 5002를 수신하는 방식으로 계속되도록 들어오는 포트를 증가시키는 데 사용됩니다.

[인바운드 NAT 규칙](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-linux/azuredeploy.json#L270) 링크를 따라 Resource Manager 템플릿 내의 JSON 샘플을 볼 수 있습니다.

```none
{
  "apiVersion": "2015-06-15",
  "type": "Microsoft.Network/loadBalancers/inboundNatRules",
  "name": "[concat(variables('loadBalancerName'), '/', 'SSH-VM', copyIndex())]",
  "tags": {
    "displayName": "load-balancer-nat-rule"
  },
  "location": "[resourceGroup().location]",
  "copy": {
    "name": "lbNatLoop",
    "count": "[parameters('numberOfInstances')]"
  },
  "dependsOn": [
    "[concat('Microsoft.Network/loadBalancers/', variables('loadBalancerName'))]"
  ],
  "properties": {
    "frontendIPConfiguration": {
      "id": "[variables('ipConfigID')]"
    },
    "protocol": "tcp",
    "frontendPort": "[copyIndex(5000)]",
    "backendPort": 22,
    "enableFloatingIP": false
  }
},
```

Azure Portal의 예제 인바운드 NAT 규칙. 배포의 각 가상 컴퓨터에 대해 SSH NAT 규칙이 생성됩니다.

![인바운드 NAT 규칙](./media/virtual-machines-linux-dotnet-core/natrule.png)

Azure 네트워크 부하 분산 장치에 대한 자세한 내용은 [Azure 인프라 서비스를 위한 부하 분산](./virtual-machines-linux-load-balance.md)을 참조하세요.

## 여러 VM 배포

마지막으로 가용성 집합 또는 부하 분산 장치가 효과적으로 작동하려면 여러 개의 가상 컴퓨터가 필요합니다. Azure Resource Manager 템플릿 copy 함수를 사용하여 여러 VM을 배포할 수 있습니다. copy 함수를 사용할 때 정해진 수의 가상 컴퓨터를 정의할 필요는 없습니다. 배포 시에 이 값이 동적으로 제공될 수 있기 때문입니다. copy 함수는 만들 인스턴스 수를 사용하고, 적절한 수의 가상 컴퓨터 및 관련 리소스의 배포를 처리합니다.

Music Store 샘플 템플릿에서 인스턴스 수를 사용하는 매개 변수가 정의됩니다. 가상 컴퓨터 및 관련 리소스를 만들 때 템플릿 전체에서 이 수가 사용됩니다.

```none
"numberOfInstances": {
  "type": "int",
  "minValue": 1,
  "defaultValue": 1,
  "metadata": {
    "description": "Number of VM instances to be created behind load balancer."
  }
},
```

가상 컴퓨터 리소스에서 복사 루프에 이름이 지정되며, 결과 사본 수를 제어하는 데 인스턴스 수 매개 변수가 사용됩니다.

[가상 컴퓨터 Copy 함수](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-linux/azuredeploy.json#L300) 링크를 따라 Resource Manager 템플릿 내의 JSON 샘플을 볼 수 있습니다.


```none
"apiVersion": "2015-06-15",
"type": "Microsoft.Compute/virtualMachines",
"name": "[concat(variables('vmName'),copyindex())]",
"location": "[resourceGroup().location]",
"copy": {
  "name": "virtualMachineLoop",
  "count": "[parameters('numberOfInstances')]"
},
```

현재 반복되는 copy 함수 항목에는 `copyIndex()` 함수를 통해 액세스할 수 있습니다. copy index 함수 값은 가상 컴퓨터 및 기타 리소스를 명명하는 데 사용될 수 있습니다. 예를 들어 가상 컴퓨터의 두 인스턴스가 배포된 경우 둘 다 이름이 달라야 합니다. `copyIndex()` 함수는 고유한 이름을 만들기 위해 가상 컴퓨터 이름의 일부로 사용될 수 있습니다. 명명을 위해 사용되는 `copyindex()` 함수의 예제는 가상 컴퓨터 리소스에서 볼 수 있습니다. 여기서 컴퓨터 이름은 `vmName` 매개 변수와 `copyIndex()` 함수를 연결해서 만듭니다.

[Copy Index 함수](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-linux/azuredeploy.json#L319) 링크를 따라 Resource Manager 템플릿 내의 JSON 샘플을 볼 수 있습니다.


```none
"osProfile": {
  "computerName": "[concat(parameters('vmName'),copyindex())]",
  "adminUsername": "[parameters('adminUsername')]",
  "linuxConfiguration": {
    "disablePasswordAuthentication": "true",
    "ssh": {
      "publicKeys": [
        {
          "path": "[variables('sshKeyPath')]",
          "keyData": "[parameters('sshKeyData')]"
        }
      ]
    }
  }
},
```

`copyIndex` 함수는 Music Store 샘플 템플릿에서 여러 번 사용됩니다. `copyIndex`를 활용하는 리소스 및 함수에는 네트워크 인터페이스, 부하 분산 장치 규칙 등, 가상 컴퓨터의 단일 인스턴스에 국한되는 항목이 포함되며, 모두가 함수에 따라 좌우됩니다.

copy 함수에 대한 자세한 내용은 [Azure Resource Manager에서 리소스의 여러 인스턴스 만들기](../resource-group-create-multiple.md)를 참조하세요.

## 다음 단계

<hr>

[4단계 - Azure Resource Manager 템플릿을 사용한 응용 프로그램 배포](./virtual-machines-linux-dotnet-core-5-app-deployment.md)

<!---HONumber=AcomDC_0928_2016-->