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
ms.openlocfilehash: 8ee89553846309bd96f3cad3648e337488cf07d3
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/07/2018
ms.locfileid: "44167631"
---
# <a name="additional-network-requirements"></a>추가 네트워크 요구 사항

경우에 따라 HANA 대규모 인스턴스 배포의 일부로 추가 네트워크 요구 사항이 있을 수 있습니다. 이 문서에서는 다음 네트워크 요구 사항에 대해 설명합니다.
- [더 많은 IP 주소 또는 서브넷 추가](#adding-more-ip-addresses-or-subnets)
- [가상 네트워크 추가](#adding-vnets)
- [ExpressRoute 회로 대역폭 증가](#increasing-expressroute-circuit-bandwidth)
- [추가적인 ExpressRoute 회로 추가](#adding-an-additional-expressroute-circuit)
- [서브넷 삭제](#deleting-a-subnet)
- [가상 네트워크 삭제](#deleting-a-vnet)
- [ExpressRouter 회로 삭제](#deleting-an-expressroute-circuit)


## <a name="adding-more-ip-addresses-or-subnets"></a>더 많은 IP 주소 또는 서브넷 추가

IP 주소 또는 서브넷을 추가하는 경우 Azure Portal, PowerShell 또는 CLI 중 하나를 사용합니다.

이 경우 새 집계 범위를 생성하는 대신 새 IP 주소 범위를 새 범위로 VNet 주소 공간에 추가하는 것이 좋습니다. 두 경우 모두 클라이언트의 HANA 큰 인스턴스 단위에 새 IP 주소 범위 밖의 연결을 허용하려면 이 변경 내용을 Microsoft에 제출해야 합니다. Azure 지원 요청을 열고 새 VNet 주소 공간이 추가되게 할 수 있습니다. 확인을 받은 후에 다음 단계를 수행합니다.

Azure Portal에서 추가 서브넷을 만들려면 [Azure Portal을 사용하여 가상 네트워크 만들기](../../../virtual-network/manage-virtual-network.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#create-a-virtual-network)를 참조하고 PowerShell에서 추가 서브넷을 만들려면 [PowerShell을 사용하여 가상 네트워크 만들기](../../../virtual-network/manage-virtual-network.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#create-a-virtual-network)를 참조하세요.

## <a name="adding-vnets"></a>VNet 추가

하나 이상의 Azure VNet을 처음 연결한 후에 Azure(큰 인스턴스)의 SAP HANA에 액세스하는 추가 항목을 추가할 수 있습니다. 먼저 Azure 지원 요청을 제출합니다. 이 요청에 특정 Azure 배포를 식별하는 특정 정보 및 Azure VNet 주소 공간의 IP 주소 공간 범위를 모두 포함합니다. Azure의 SAP HANA Service Management에서는 추가 VNet 및 ExpressRoute를 연결해야 하는 데 필요한 정보를 제공합니다. 모든 VNet에 대해 ExpressRoute 회로를 HANA 큰 인스턴스에 연결하는 데 고유한 권한 부여 키가 필요합니다.

새 Azure VNet을 추가하는 단계:

1. 온보딩 프로세스의 첫 번째 단계를 완성하고 _Azure VNet 만들기_ 섹션을 참조하세요.
2. 온보딩 프로세스의 두 번째 단계를 완성하고 _게이트웨이 서브넷 만들기_ 섹션을 참조하세요.
3. 추가 VNet을 HANA 큰 인스턴스 ExpressRoute 회로에 연결하려면, 새 VNet에 대한 정보를 사용하여 Azure 지원 요청을 열고 새 권한 부여 키를 요청합니다.
4. 인증이 완료되었다고 알림을 받으면 Microsoft에서 제공되는 권한 부여 정보를 사용하여 온보딩 프로세스에서 세 번째 단계를 완료합니다. _VNet 연결_ 섹션을 참조하세요.

## <a name="increasing-expressroute-circuit-bandwidth"></a>ExpressRoute 회로 대역폭 증가

Azure의 SAP HANA Service Management에 문의하세요. Azure(큰 인스턴스)의 SAP HANA ExpressRoute 회로 대역폭을 증가시켜야 하는 경우 Azure 지원 요청을 만듭니다. (최대 10GBPS의 단일 회로 대역폭으로 증가하도록 요청할 수 있습니다.) 그러면 작업이 완료된 후에 Azure에서 이보다 속도를 빠르게 하기 위해 필요한 추가 작업 없음이라는 알림이 표시됩니다.

## <a name="adding-an-additional-expressroute-circuit"></a>추가 ExpressRoute 회로 추가

Azure의 SAP HANA Service Management를 참조하여 추가 ExpressRoute 회로가 필요하다는 조언을 받은 경우 새 ExpressRoute 회로를 만들도록 Azure 지원에 요청하고 연결하는 인증 정보를 가져옵니다. VNet에 사용될 주소 공간은 Azure의 SAP HANA Service Management가 권한 부여를 제공하기 위해 요청하기 전에 정의되어야 합니다.

새 회로가 만들어지고 Azure의 SAP HANA Service Management 구성이 완료되면 진행해야 하는 정보를 포함한 알림을 받게 됩니다. 새 VNet을 만들고 이 추가 회로에 연결하기 위해 위에 제공된 단계를 수행합니다. 동일한 Azure 지역에 있는 Azure(큰 인스턴스) ExpressRoute 회로 상의 또 다른 SAP HANA에 이미 연결된 경우 Azure VNet을 이 추가 회로에 연결할 수 없습니다.

## <a name="deleting-a-subnet"></a>서브넷 삭제

VNet 서브넷을 제거하기 위해 Azure Portal, PowerShell 또는 CLI 중 하나를 사용할 수 있습니다. Azure VNet IP 주소 범위/Azure VNet 주소 공간이 집계 범위인 경우 Microsoft의 후속 조치가 없습니다. 단, 삭제된 서브넷을 포함하는 BGP 경로 주소 공간을 VNet이 여전히 전파하고 있습니다. Azure VNet IP 주소 범위/Azure VNet 주소 공간을 삭제된 서브넷에 할당된 여러 IP 주소 범위로 정의한 경우, VNet 주소 공간에서 해당 주소를 삭제하고 Azure의 SAP HANA Service Management에 알려서 Azure(큰 인스턴스)의 SAP HANA 통신 허용 범위에서 제거하도록 합니다.

서브넷을 삭제하려면 서브넷 삭제에 대한 자세한 내용을 제공하는 [서브넷 삭제](../../../virtual-network/virtual-network-manage-subnet.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#delete-a-subnet)를 참조하세요.

## <a name="deleting-a-vnet"></a>VNet 삭제

가상 네트워크를 삭제하려면 [가상 네트워크 삭제](../../../virtual-network/manage-virtual-network.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#delete-a-virtual-network)를 참조하세요. Azure의 SAP HANA Service Management는 Azure(큰 인스턴스)의 SAP HANA ExpressRoute 회로에 대한 기존 권한 부여를 제거하고 HANA 큰 인스턴스와의 통신에 대한 Azure VNet IP 주소 범위/Azure VNet 주소 공간을 제거합니다.

VNet을 제거하면 제거될 IP 주소 공간 범위를 제공하는 Azure 지원 요청이 열립니다.

모든 항목이 제거되었는지 확인하려면 다음 항목을 삭제합니다.

- ExpressRoute 연결, VNet 게이트웨이, VNet 게이트웨이 공용 IP 및 VNet

## <a name="deleting-an-expressroute-circuit"></a>ExpressRoute 회로 삭제

추가 Azure(큰 인스턴스)의 SAP HANA ExpressRoute 회로를 제거하려면 Azure의 SAP HANA Service Management를 통해 Azure 지원 요청을 열고 회로를 삭제하도록 요청합니다. Azure 구독 내에서 필요에 따라 VNet을 삭제하거나 유지할 수 있습니다. 그러나 HANA 큰 인스턴스 ExpressRoute 회로와 연결된 VNet 게이트웨이 간에 연결을 삭제해야 합니다.

VNet도 제거하려면 위의 섹션에서 VNet 삭제에 대한 지침을 따릅니다.

**다음 단계**

- [Azure에서 SAP HANA(대규모 인스턴스)를 설치하고 구성하는 방법](hana-installation.md)을 참조하세요.
