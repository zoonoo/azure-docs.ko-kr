---
title: Azure Resource Manager support for Load Balancer
description: In this article, use Azure PowerShell and templates with Azure Load Balancer
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/19/2019
ms.author: allensu
ms.openlocfilehash: 57a60a65dee995695224aa1b451e848ea8768ab1
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/20/2019
ms.locfileid: "74215397"
---
# <a name="azure-resource-manager-support-with-azure-load-balancer"></a>Azure Resource Manager support with Azure Load Balancer



Azure의 서비스용 관리 프레임워크로는 기본적으로 Azure Resource Manager가 사용됩니다. 이제 Azure Resource Manager 기반 API 및 도구를 사용하여 Azure Load Balancer를 관리할 수 있습니다.

## <a name="concepts"></a>개념

Resource Manager를 사용하는 경우 Azure Load Balancer에 다음과 같은 자식 리소스가 포함됩니다.

* Front-end IP configuration – a load balancer can include one or more frontend IP addresses, otherwise known as a virtual IPs (VIPs). 이러한 IP 주소는 트래픽에 대한 수신으로 사용됩니다.
* Back-end address pool – This pool is a collection of IP addresses associated with the virtual machine Network Interface Card (NIC) to which load is distributed.
* Load-balancing rules – a rule property maps a given frontend IP and port combination to a set of back-end IP addresses and port combination. A single load balancer can have multiple load-balancing rules. Each rule is a combination of a frontend IP and port and back-end IP and port associated with VMs.
* 검색 - 검색을 사용하여 VM 인스턴스의 상태를 추적할 수 있습니다. 상태 검색에 실패하면 해당 VM 인스턴스는 자동으로 회전에서 제외됩니다.
* Inbound NAT rules – NAT rules defining the inbound traffic flowing through the frontend IP and distributed to the back-end IP.

![](./media/load-balancer-arm/load-balancer-arm.png)

## <a name="quickstart-templates"></a>빠른 시작 템플릿

Azure Resource Manager에서 선언적 템플릿을 사용하여 애플리케이션을 프로비전할 수 있습니다. 단일 템플릿에서 여러 서비스를 종속 항목과 함께 배포할 수 있습니다. 동일한 템플릿을 사용하여 애플리케이션 수명 주기의 모든 단계에서 애플리케이션을 반복적으로 배포할 수 있습니다.

Templates may include definitions for:
* **가상 머신**
* **가상 네트워크**
* **가용성 집합**
* **Network interfaces (NICs)**
* **스토리지 계정**
* **부하 분산 장치**
* **네트워크 보안 그룹**
* **Public IPs.** 

템플릿을 사용하면 복잡한 애플리케이션에 필요한 모든 항목을 만들 수 있습니다. 버전 제어 및 협업을 위해 템플릿 파일을 콘텐츠 관리 시스템에 체크 인할 수 있습니다.

[템플릿에 대한 자세한 정보](../azure-resource-manager/resource-manager-template-walkthrough.md)

[네트워크 리소스에 대한 자세한 정보](../networking/networking-overview.md)

For Quickstart templates using Azure Load Balancer, see the [GitHub repository](https://github.com/Azure/azure-quickstart-templates) that hosts a set of community-generated templates.

템플릿의 예:

* [부하 분산 장치의 2개 VM 및 부하 분산 규칙](https://go.microsoft.com/fwlink/?LinkId=544799)
* [2 VMs in a VNET with an Internal Load Balancer and load balancer rules](https://go.microsoft.com/fwlink/?LinkId=544800)
* [2 VMs in a load balancer and configure NAT rules on the LB](https://go.microsoft.com/fwlink/?LinkId=544801)

## <a name="setting-up-azure-load-balancer-with-a-powershell-or-cli"></a>PowerShell 또는 CLI를 사용하여 Azure 부하 분산 장치 설정

Get started with Azure Resource Manager cmdlets, command-line tools, and REST APIs

* [Azure 네트워킹 Cmdlet](https://docs.microsoft.com/powershell/module/az.network#networking) 을 사용하여 부하 분산 장치를 만들 수 있습니다.
* [Azure Resource Manager를 사용하여 부하 분산 장치를 만드는 방법](load-balancer-get-started-ilb-arm-ps.md)
* [Azure 리소스 관리에서 Azure CLI 사용](../xplat-cli-azure-resource-manager.md)
* [부하 분산 장치 REST API](https://msdn.microsoft.com/library/azure/mt163651.aspx)

## <a name="next-steps"></a>다음 단계

[Get started creating an Internet facing load balancer](load-balancer-get-started-internet-arm-ps.md) and configure the type of [distribution mode](load-balancer-distribution-mode.md) for specific network traffic behavior.

[부하 분산 장치의 유휴 TCP 시간 제한 설정](load-balancer-tcp-idle-timeout.md)을 관리하는 방법을 파악합니다. These settings are important when your application needs to keep connections alive for servers behind a load balancer.
