---
title: 가상 네트워크용 Azure Resource Manager 템플릿 샘플 | Microsoft Docs
description: Azure 가상 네트워크 배포에 사용할 수 있는 다양한 Azure Resource Manager 템플릿에 대해 알아봅니다.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: twooley
editor: ''
tags: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 04/22/2019
ms.author: kumud
ms.openlocfilehash: c553e3f2de963abb4a3c23a4114eecfa33d0aa61
ms.sourcegitcommit: 4cdd4b65ddbd3261967cdcd6bc4adf46b4b49b01
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/06/2019
ms.locfileid: "66735325"
---
# <a name="azure-resource-manager-template-samples-for-virtual-network"></a>가상 네트워크용 Azure Resource Manager 템플릿 샘플

다음 표에는 Azure Resource Manager 템플릿 샘플 링크가 포함되어 있습니다. Azure [Portal](../azure-resource-manager/resource-group-template-deploy-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json), Azure [CLI](../azure-resource-manager/resource-group-template-deploy-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 또는 Azure [PowerShell](../azure-resource-manager/resource-group-template-deploy.md?toc=%2fazure%2fvirtual-network%2ftoc.json)을 사용하여 템플릿을 배포할 수 있습니다. 템플릿을 직접 작성하는 방법을 알아보려면 [첫 번째 템플릿 만들기](../azure-resource-manager/resource-manager-create-first-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 및 [Azure Resource Manager 템플릿의 구조 및 구문 이해](../azure-resource-manager/resource-group-authoring-templates.md?toc=%2fazure%2fvirtual-network%2ftoc.json)를 참조하세요.

템플릿에서 사용할 JSON 구문 및 속성은 [Microsoft.Network 리소스 종류](/azure/templates/microsoft.network/allversions)를 참조하세요.


| Task | 설명 |
|----|----|
|[두 서브넷이 있는 가상 네트워크 만들기](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vnet-two-subnets)| 두 개의 서브넷이 있는 가상 네트워크를 만듭니다.|
|[네트워크 가상 어플라이언스를 통한 트래픽 라우팅](https://github.com/Azure/azure-quickstart-templates/tree/master/201-userdefined-routes-appliance)| 세 개의 서브넷이 있는 가상 네트워크를 만듭니다. 각 서브넷에 가상 머신을 배포합니다. 세 번째 서브넷의 가상 머신을 통해 한 서브넷에서 다른 서브넷으로 트래픽을 보내는 경로가 포함된 경로 테이블을 만듭니다. 경로 테이블을 서브넷 중 하나에 연결합니다.|
|[Azure Storage에 대한 가상 네트워크 서비스 엔드포인트 만들기](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vnet-2subnets-service-endpoints-storage-integration)|두 서브넷과 각 서브넷의 네트워크 인터페이스를 사용하여 새 가상 네트워크를 만듭니다. 서브넷 중 하나에 Azure Storage에 대한 서비스 엔드포인트를 사용하도록 설정하고 해당 서브넷에 새 스토리지 계정의 보안을 설정합니다.|
|[두 가상 네트워크 연결](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vnet-to-vnet-peering)| 두 가상 네트워크를 만들고 이들 간에 가상 네트워크 피어링을 만듭니다.|
|[여러 IP 주소로 가상 머신 만들기](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-multiple-ipconfig)| 여러 IP 주소로 Windows 또는 Linux VM을 만듭니다.|
|[IPv4 + IPv6 이중 스택 가상 네트워크 구성](https://github.com/Azure/azure-quickstart-templates/tree/master/ipv6-in-vnet)|IPv4 및 IPv6 공용 IP 주소가 있는 Azure Basic Load Balancer 및 두 개의 VM으로 이중 스택(IPv4+IPv6) 가상 네트워크를 배포합니다. |
