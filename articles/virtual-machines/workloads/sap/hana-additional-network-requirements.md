---
title: Azure(대규모 인스턴스)의 SAP HANA에 대한 추가 네트워크 요구 사항 | Microsoft Docs
description: Azure(대규모 인스턴스)의 SAP HANA에 대한 추가 네트워크 요구 사항입니다.
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 2f4c74076f2763addc1f2b971d8ea0ca29ce6fd0
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60203978"
---
# <a name="additional-network-requirements-for-large-instances"></a>대규모 인스턴스의 추가 네트워크 요구 사항

Azure에서 대규모 SAP HANA 인스턴스 배포의 일부로 추가 네트워크 요구 사항이 있을 수 있습니다.

## <a name="add-more-ip-addresses-or-subnets"></a>더 많은 IP 주소 또는 서브넷 추가

IP 주소 또는 서브넷을 추가할 때 Azure Portal, PowerShell 또는 Azure CLI를 사용합니다.

새 집계 범위를 생성하는 대신 새 IP 주소 범위를 가상 네트워크 주소 공간에 새 범위로 추가합니다. 이 변경 사항을 Microsoft에 제출합니다. 이렇게 하면 해당 새 IP 주소 범위에서 클라이언트의 HANA 대규모 인스턴스 단위로 연결할 수 있습니다. Azure 지원 요청을 열고 새 가상 네트워크 주소 공간이 추가되도록 할 수 있습니다. 확인을 받은 후에 다음 단계를 수행합니다.

Azure Portal에서 추가 서브넷을 만들려면 [Azure Portal을 사용하여 가상 네트워크 만들기](../../../virtual-network/manage-virtual-network.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#create-a-virtual-network)를 참조하세요. PowerShell에서 서브넷을 하나 만들려면 [PowerShell을 사용하여 가상 네트워크 만들기](../../../virtual-network/manage-virtual-network.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#create-a-virtual-network)를 참조하세요.

## <a name="add-virtual-networks"></a>가상 네트워크 추가

Azure 가상 네트워크를 하나 이상 처음 연결한 후에 Azure의 SAP HANA(대규모 인스턴스)에 액세스하는 추가 항목을 연결할 수 있습니다. 먼저 Azure 지원 요청을 제출합니다. 해당 요청에 특정 Azure 배포를 식별하는 구체적인 정보를 포함합니다. 또한, Azure 가상 네트워크 주소 공간의 IP 주소 공간 범위도 포함합니다. 그러면 Microsoft Service Management의 SAP HANA에서 추가 가상 네트워크 및 ExpressRoute를 연결하는 데 필요한 정보를 제공합니다. 모든 가상 네트워크에 대해 ExpressRoute 회로를 HANA 대규모 인스턴스에 연결하는 데 고유한 권한 부여 키가 필요합니다.

## <a name="increase-expressroute-circuit-bandwidth"></a>ExpressRoute 회로 대역폭 증가

Microsoft Service Management의 SAP HANA에 문의하세요. Azure(대규모 인스턴스) ExpressRoute 회로의 SAP HANA 대역폭을 증가시켜야 하는 경우 Azure 지원 요청을 만듭니다. (최대 10GBPS의 단일 회로 대역폭으로 증가하도록 요청할 수 있습니다.) 그러면 작업이 완료된 후에 Azure에서 이보다 속도를 빠르게 하기 위해 아무 작업도 수행할 필요가 없다는 알림이 표시됩니다.

## <a name="add-an-additional-expressroute-circuit"></a>추가 ExpressRoute 회로 추가

Microsoft Service Management의 SAP HANA에 문의하세요. 추가 ExpressRoute 회선을 추가하도록 권장되는 경우 Azure 지원 요청(새 회로에 연결하기 위한 권한 부여 정보를 얻기 위한 요청 포함)을 만듭니다. 요청하기 전에 가상 네트워크에 사용되는 주소 공간을 정의해야 합니다. 그러면 Microsoft Service Management의 SAP HANA에서 권한을 제공할 수 있습니다.

새 회로가 만들어지고 Microsoft Service Management의 SAP HANA 구성이 완료되면 진행해야 하는 정보를 포함한 알림을 받게 됩니다. 동일한 Azure 지역에 있는 Azure(큰 인스턴스) ExpressRoute 회로 상의 또 다른 SAP HANA에 이미 연결된 경우 Azure Virtual Network를 이 추가 회로에 연결할 수 없습니다.

## <a name="delete-a-subnet"></a>서브넷 삭제

가상 네트워크 서브넷을 제거하려면 Azure Portal, PowerShell 또는 Azure CLI를 사용할 수 있습니다. Azure Virtual Network IP 주소 범위 또는 주소 공간이 집계 범위인 경우 Microsoft의 후속 조치가 없습니다. 그러나 가상 네트워크는 삭제된 서브넷을 포함하는 BGP 경로 주소 공간을 아직 전파하고 있습니다. Azure Virtual Network 주소 범위 또는 주소 공간을 삭제된 서브넷에 할당된 IP 주소 범위를 포함하는 여러 IP 주소 범위로 정의했을 수 있습니다. 이러한 할당된 IP 주소 범위를 가상 네트워크 주소 공간에서 삭제해야 합니다. 그런 다음, Microsoft Service Management의 SAP HANA에 Azure(대규모 인스턴스)의 SAP HANA가 통신하도록 허용되는 범위에서 해당 주소 공간을 삭제할 것을 알립니다.

자세한 내용은 [서브넷 삭제](../../../virtual-network/virtual-network-manage-subnet.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#delete-a-subnet)를 참조하세요.

## <a name="delete-a-virtual-network"></a>가상 네트워크 삭제

자세한 내용은 [가상 네트워크 삭제](../../../virtual-network/manage-virtual-network.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#delete-a-virtual-network)를 참조하세요.

Microsoft Service Management의 SAP HANA는 Azure(대규모 인스턴스) ExpressRoute 회로의 SAP HANA에 대한 기존 권한을 제거합니다. 또한, HANA 대규모 인스턴스와의 통신을 위해 Azure Virtual Network IP 주소 범위 또는 주소 공간도 제거합니다.

가상 네트워크를 제거한 후에는 Azure 지원 요청을 열어 제거할 IP 주소 공간 범위를 제공합니다.

모든 항목을 제거하려면 ExpressRoute 연결, 가상 네트워크 게이트웨이, 가상 네트워크 게이트웨이 공용 IP 및 가상 네트워크를 삭제합니다.

## <a name="delete-an-expressroute-circuit"></a>ExpressRoute 회로 삭제

Azure(대규모 인스턴스) ExpressRoute 회로에서 추가 SAP HANA를 제거하려면 Microsoft Service Management의 SAP HANA에 대한 Azure 지원 요청을 엽니다. 회로 삭제를 요청합니다. Azure 구독 내에서 필요에 따라 가상 네트워크를 삭제하거나 유지할 수 있습니다. 그러나 HANA 대규모 인스턴스 ExpressRoute 회로와 연결된 가상 네트워크 게이트웨이 간에 연결을 삭제해야 합니다.

## <a name="next-steps"></a>다음 단계

- [Azure에서 SAP HANA(큰 인스턴스)를 설치하고 구성하는 방법](hana-installation.md)
