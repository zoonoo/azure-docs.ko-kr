---
title: Azure 포털을 사용하여 VM으로 포트 열기 | Microsoft Docs
description: Azure 포털의 Resource Manager 배포 모델을 사용하여 Windows VM에 대한 포트를 열고 끝점을 만드는 방법 알아보기
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
# Azure 포털을 사용하여 Azure에서 VM으로 포트 열기
[!INCLUDE [virtual-machines-common-nsg-quickstart](../../includes/virtual-machines-common-nsg-quickstart.md)]

## 빠른 명령
[Azure PowerShell을 사용하여 수행할 수도 있습니다](virtual-machines-windows-nsg-quickstart-powershell.md).

먼저 네트워크 보안 그룹을 만듭니다. 포털에서 리소스 그룹을 선택하고 '추가'를 클릭한 후 '네트워크 보안 그룹' 선택을 검색합니다.

![네트워크 보안 그룹 추가](./media/virtual-machines-windows-nsg-quickstart-portal/add-nsg.png)

네트워크 보안 그룹의 이름을 입력하고 위치를 선택합니다.

![네트워크 보안 그룹 만들기](./media/virtual-machines-windows-nsg-quickstart-portal/create-nsg.png)

새 네트워크 보안 그룹을 선택합니다. 이제 인바운드 규칙을 만듭니다.

![인바운드 규칙 추가](./media/virtual-machines-windows-nsg-quickstart-portal/add-inbound-rule.png)

새 규칙의 이름을 제공합니다. 기본적으로 포트 80은 이미 입력되어 있습니다. 네트워크 보안 그룹에 다른 규칙을 추가하는 경우 이 블레이드에서 소스, 프로토콜 및 대상을 변경합니다.

![인바운드 규칙 만들기](./media/virtual-machines-windows-nsg-quickstart-portal/create-inbound-rule.png)

마지막 단계는 네트워크 보안 그룹을 서브넷 또는 특정 네트워크 인터페이스에 연결하는 것입니다. 서브넷에 네트워크 보안 그룹을 연결합니다.

![서브넷에 네트워크 보안 그룹 연결](./media/virtual-machines-windows-nsg-quickstart-portal/associate-subnet.png)

가상 네트워크를 선택하고 적절한 서브넷을 선택합니다.

![가상 네트워킹에 네트워크 보안 그룹 연결](./media/virtual-machines-windows-nsg-quickstart-portal/select-vnet-subnet.png)

이제 네트워크 보안 그룹을 만들고, 포트 80에서 트래픽을 허용하는 인바운드 규칙을 만들었으며 해당 규칙을 서브넷과 연결했습니다. 해당 서브넷에 연결하는 모든 VM은 포트 80에 연결할 수 있게 됩니다.

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