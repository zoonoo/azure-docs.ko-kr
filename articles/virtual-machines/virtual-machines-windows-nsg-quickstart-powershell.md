---
title: PowerShell을 사용하여 VM으로 포트 열기 | Microsoft Docs
description: Azure Resource Manager 배포 모델 및 Azure PowerShell을 사용하여 Windows VM에 대한 포트를 열고 끝점을 만드는 방법 알아보기
services: virtual-machines-windows
documentationcenter: ''
author: iainfoulds
manager: timlt
editor: ''

ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 08/08/2016
ms.author: iainfou

---
# PowerShell을 사용하여 Azure에서 VM으로 포트 열기
[!INCLUDE [virtual-machines-common-nsg-quickstart](../../includes/virtual-machines-common-nsg-quickstart.md)]

## 빠른 명령
네트워크 보안 그룹 및 ACL 규칙을 만들려면 [최신 버전의 Azure PowerShell을 설치](../powershell-install-configure.md)해야 합니다. [Azure 포털을 사용하여 수행할 수도 있습니다](virtual-machines-windows-nsg-quickstart-portal.md).

먼저 고유한 이름 및 설명을 입력하여 TCP 포트 80에서 HTTP 트래픽을 허용하는 규칙을 만들어야 합니다.

```
$httprule = New-AzureRmNetworkSecurityRuleConfig -Name http-rule -Description "Allow HTTP" `
    -Access Allow -Protocol Tcp -Direction Inbound -Priority 100 `
    -SourceAddressPrefix Internet -SourcePortRange * `
    -DestinationAddressPrefix * -DestinationPortRange 80
```

그런 후 다음과 같이 고유한 리소스 그룹 이름 및 위치를 입력하여 네트워크 보안 그룹을 만들고 방금 만든 HTTP 규칙을 할당합니다.

```
$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName TestRG -Location westus `
    -Name "TestNSG" -SecurityRules $httprule
```

이제 서브넷에 네트워크 보안 그룹을 할당합니다. 먼저 만든 가상 네트워크를 선택합니다.

```
$vnet = Get-AzureRmVirtualNetwork -ResourceGroupName TestRG -Name TestVNet
```

네트워크 보안 그룹을 서브넷에 연결합니다.

```
Set-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name TestSubnet `
    -NetworkSecurityGroup $nsg
```

마지막으로, 변경 내용이 적용되도록 가상 네트워크를 업데이트합니다.

```
Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
```


## 네트워크 보안 그룹에 대한 자세한 정보
여기서 빠른 명령을 사용하면 VM으로 트래픽이 이동되도록 할 수 있습니다. 네트워크 보안 그룹은 리소스에 대한 액세스를 제어하는 많은 기능과 세분성을 제공합니다. [여기서 네트워크 보안 그룹 및 ACL 규칙 만들기](../virtual-network/virtual-networks-create-nsg-arm-ps.md)에 대해 자세히 읽어보세요.

네트워크 보안 그룹 및 ACL 규칙을 Azure Resource Manager 템플릿의 일부로 정의할 수도 있습니다. [템플릿을 사용하여 네트워크 보안 그룹 만들기](../virtual-network/virtual-networks-create-nsg-arm-template.md)에 대해 자세히 읽어보세요.

포트 전달을 사용하여 고유한 외부 포트를 VM의 내부 포트에 매핑해야 하는 경우 부하 분산 장치 및 NAT(네트워크 주소 변환) 규칙을 사용합니다. 예를 들어 TCP 포트 8080을 외부에 노출하고 트래픽이 VM의 TCP 포트 80으로 전달되도록 할 수 있습니다. [인터넷 연결 부하 분산 장치 만들기](../load-balancer/load-balancer-get-started-internet-arm-ps.md)에 대해 자세히 알아볼 수 있습니다.

## 다음 단계
이 예제에서는 HTTP 트래픽을 허용하는 간단한 규칙을 만들었습니다. 다음 문서에서 보다 자세한 환경을 만들기 위한 정보를 찾을 수 있습니다.

* [Azure Resource Manager 개요](../resource-group-overview.md)
* [NSG(네트워크 보안 그룹)란?](../virtual-network/virtual-networks-nsg.md)
* [부하 분산 장치에 대한 Azure Resource Manager 개요](../load-balancer/load-balancer-arm.md)

<!---HONumber=AcomDC_0907_2016-->