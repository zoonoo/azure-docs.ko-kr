---
title: "VPN Gateway 구성: 클래식 포털: Azure | Microsoft Docs"
description: "이 문서에서는 가상 네트워크 VPN 게이트웨이 구성하고 게이트웨이 VPN 라우팅 형식을 변경하는 방법을 안내합니다. 이러한 단계는 클래식 배포 모델 및 클래식 포털에 적용됩니다."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: carmonm
editor: 
tags: azure-service-management
ms.assetid: fbe59ba8-b11f-4d21-9bb1-225ec6c6d351
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/09/2017
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: b6f001345daf411497295357ab43d01635ae743e
ms.lasthandoff: 03/25/2017


---
# <a name="configure-a-vpn-gateway-in-the-classic-portal"></a>클래식 포털에서 VPN Gateway 구성 
Azure 및 온-프레미스 위치 간에 보안 프레미스 간 연결을 만들려면 가상 네트워크 게이트웨이를 만들어야 합니다. VPN Gateway는 특정 유형의 가상 네트워크 게이트웨이입니다. 클래식 배포 모델에서 VPN Gateway는 두 VPN 라우팅 형식인 정적 또는 동적 중 하나일 수 있습니다. 선택하는 VPN 형식은 네트워크 디자인 계획과 사용하려는 온-프레미스 VPN 장치 둘 다에 따라 좌우됩니다. VPN 장치에 대한 자세한 내용은 [VPN 장치 정보](vpn-gateway-about-vpn-devices.md)를 참조하세요.

**Azure 배포 모델 정보**

[!INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## <a name="configuration-overview"></a>구성 개요
다음 단계에서는 클래식 포털에서 VPN Gateway를 구성하는 방법을 안내합니다. 이러한 단계는 클래식 배포 모델을 사용하여 만든 가상 네트워크에 대한 게이트웨이에 적용됩니다. 현재 게이트웨이에 대한 구성 설정의 일부만 Azure 포털에서 사용할 수 있습니다. 사용 가능한 경우 Azure 포털에 적용되는 새 명령 집합이 생성됩니다.

### <a name="before-you-begin"></a>시작하기 전에
게이트웨이를 구성하기 전에 먼저 가상 네트워크를 만들어야 합니다. 프레미스 간 연결을 위해 가상 네트워크를 만드는 단계는 [사이트 간 VPN 연결을 사용하여 가상 네트워크 구성](vpn-gateway-site-to-site-create.md) 또는 [지점 및 사이트 간 VPN 연결을 사용하여 가상 네트워크 구성](vpn-gateway-point-to-site-create.md)을 참조하세요. 그런 후 다음 단계에 따라 VPN 게이트웨이 구성하고 VPN 장치를 구성하는 데 필요한 정보를 수집합니다. 

VPN 게이트웨이가 이미 있는 경우 VPN 라우팅 형식을 변경하려면 [게이트웨이의 VPN 라우팅 형식을 변경하는 방법](#how-to-change-the-vpn-routing-type-for-your-gateway)을 참조하세요.

## <a name="create-a-vpn-gateway"></a>VPN 게이트웨이 만들기
1. [Azure 클래식 포털](https://manage.windowsazure.com)의 **Networks** 페이지에서, 가상 네트워크의 상태 열이 **생성됨**인지 확인합니다.
2. **이름** 열에서 가상 네트워크의 이름을 클릭합니다.
3. **대시보드** 페이지에서 이 VNet에 게이트웨이가 아직 구성되지 않은 것을 볼 수 있습니다. 게이트웨이 구성 단계를 진행할 때 이 상태가 표시됩니다.

![게이트웨이가 만들어지지 않음](./media/vpn-gateway-configure-vpn-gateway-mp/IC717025.png)

그런 다음 페이지 아래쪽에서 **게이트웨이 만들기**를 클릭합니다. *고정 라우팅* 또는 *동적 라우팅*을 선택할 수 있습니다. VPN 라우팅 형식은 몇 가지 요인에 따라 선택합니다. 예를 들어 VPN 장치가 지원하는 항목과 지점 및 사이트 간 연결을 지원해야 하는지 여부 등에 따라 달라집니다. 필요한 VPN 라우팅 형식은 [가상 네트워크 연결을 위한 VPN 장치 정보](vpn-gateway-about-vpn-devices.md) 를 확인하세요. 게이트웨이를 만든 후 게이트웨이 VPN 라우팅 유형 간에 변경하려면 게이트웨이를 삭제하고 다시 만들어야 합니다. 시스템에서 게이트웨이 만들 것인지 확인하는 메시지를 표시하면 **예**를 클릭합니다.

![게이트웨이 VPN 라우팅 유형](./media/vpn-gateway-configure-vpn-gateway-mp/IC717026.png)

게이트웨이를 만들 때 페이지의 게이트웨이 그래픽이 노란색으로 변경되고 *게이트웨이를 만드는 중*이 표시됩니다. 게이트웨이를 만드는 데에는 최대 45분이 걸릴 수 있습니다. 게이트웨이가 완료될 때까지 대기한 다음 다른 구성 설정을 계속합니다.

![게이트웨이 만들기](./media/vpn-gateway-configure-vpn-gateway-mp/IC717027.png)

게이트웨이가 *연결 중*으로 변경되면 VPN 장치에 필요한 정보를 수집할 수 있습니다.

![게이트웨이 연결](./media/vpn-gateway-configure-vpn-gateway-mp/IC717028.png)

## <a name="site-to-site-connections"></a>사이트 간 연결

### <a name="step-1-gather-information-for-your-vpn-device-configuration"></a>1단계. VPN 장치 구성에 대한 정보 수집
사이트 간 연결을 만든 경우 게이트웨이를 만든 후에 VPN 장치 구성에 대한 정보를 수집합니다. 이 정보는 가상 네트워크에 대한 **대시보드** 페이지에 있습니다.

1. **게이트웨이 IP 주소 -** IP 주소는 **대시보드** 페이지에서 찾을 수 있습니다. IP 주소는 게이트웨이 만들기를 완료한 이후에 표시됩니다.
2. **공유 키 -** 화면 아래쪽에 있는 **키 관리**를 클릭합니다. 키 옆에 있는 아이콘을 클릭하여 키를 클립보드에 복사한 다음 붙여넣고 저장합니다. 이 단추는 S2S VPN 터널이 하나인 경우에만 작동합니다. S2S VPN 터널이 여러 개 있는 경우 *가상 네트워크 게이트웨이 공유 키 가져오기* API 또는 PowerShell cmdlet을 사용하세요.

![키 관리](./media/vpn-gateway-configure-vpn-gateway-mp/IC717029.png)

### <a name="step-2--configure-your-vpn-device"></a>2단계.  VPN 장치 구성
사이트 간 연결의 경우 이전 단계를 완료한 후 사용자 또는 네트워크 관리자는 연결을 설정하기 위해 VPN 장치를 구성해야 합니다. VPN 장치에 대한 자세한 내용은 [가상 네트워크 연결을 위한 VPN 장치 정보](vpn-gateway-about-vpn-devices.md) 를 참조하세요.

VPN 장치를 구성한 후 VNet에 대한 대시보드 페이지에서 업데이트된 연결 정보를 볼 수 있습니다.

또한 다음 명령 중 하나를 실행하여 연결을 테스트할 수 있습니다.

|  | Cisco ASA | Cisco ISR/ASR | Juniper SSG/ISG | Juniper SRX/J |
| --- | --- | --- | --- | --- |
| **Check main mode SAs** |show crypto isakmp sa |show crypto isakmp sa |get ike cookie |show security ike security-association |
| **Check quick mode SAs** |show crypto ipsec sa |show crypto ipsec sa |get sa |show security ipsec security-association |

### <a name="step-3-verify-your-local-network-ranges-and-vpn-gateway-ip-address"></a>3단계. 로컬 네트워크 범위 및 VPN 게이트웨이 IP 주소 확인
#### <a name="verify-your-vpn-gateway-ip-address"></a>VPN 게이트웨이 IP 주소 확인
게이트웨이를 올바르게 연결하려면 프레미스 간 구성에 대해 지정한 로컬 네트워크에 대해 VPN 장치의 IP 주소를 올바르게 구성해야 합니다. 일반적으로 이 구성은 사이트 간 구성 프로세스 중에 설정합니다. 그러나 이전에 다른 장치에서 이 로컬 네트워크를 사용했거나 이 로컬 네트워크에 대한 IP 주소가 변경된 경우 설정을 편집하여 올바른 게이트웨이 IP 주소를 지정할 수 있습니다.

1. 게이트웨이 IP 주소를 확인하려면 왼쪽 포털 창에서 **Networks**를 클릭한 다음 페이지 위쪽에 있는 **로컬 네트워크**를 선택합니다. 사용자가 만든 각 로컬 네트워크에 대한 VPN 게이트웨이 주소가 표시됩니다. IP 주소를 편집하려면 VNet을 선택하고 페이지 아래쪽에 있는 **편집** 을 클릭합니다.
2. **로컬 네트워크 정보 지정** 페이지에서 IP 주소를 편집한 후 페이지의 아래쪽에 있는 다음 화살표를 클릭합니다.
3. **주소 공간 지정** 페이지에서 오른쪽 아래의 확인 표시를 클릭하여 설정을 저장합니다.

#### <a name="verify-the-address-ranges-for-your-local-networks"></a>로컬 네트워크의 주소 범위 확인
게이트웨이를 통해 온-프레미스 위치로 올바른 트래픽이 이동되려면 각 IP 주소 범위가 지정되어 있는지 확인해야 합니다. 각 범위가 Azure **로컬 네트워크** 구성에 나열되어야 합니다. 온-프레미스 위치의 네트워크 구성에 따라 이 작업은 다소 커질 수 있습니다. 나열된 범위 내에 포함된 IP 주소에 대해 바인딩되는 트래픽은 가상 네트워크 VPN 게이트웨이를 통해 전송됩니다. 온-프레미스 구성이 인바운드 트래픽을 받을 수 있는지 확인하려는 경우에도 나열하는 범위가 전용 범위일 필요는 없습니다.

로컬 네트워크에 대한 범위를 추가하거나 편집하려면 다음 단계를 사용하세요.

1. 로컬 네트워크에 대한 IP 주소 범위를 편집하려면 왼쪽 포털 창에서 **Networks**를 클릭한 다음 페이지 위쪽에 있는 **로컬 네트워크**를 선택합니다. 포털에서 나열된 범위를 확인하는 가장 쉬운 방법은 **편집** 페이지를 사용하는 것입니다. 범위를 확인하려면 VNet을 선택하고 페이지 아래쪽에 있는 **편집** 을 클릭합니다.
2. **로컬 네트워크 정보 지정** 페이지에서 아무것도 변경하지 않습니다. 페이지 맨 아래에 있는 다음 화살표를 클릭합니다.
3. **주소 공간 지정** 페이지에서 네트워크 주소 공간을 변경합니다. 그런 다음 확인 표시를 클릭하여 구성을 저장합니다.

## <a name="how-to-view-gateway-traffic"></a>게이트웨이 트래픽을 확인하는 방법
가상 네트워크 **대시보드** 에서 게이트웨이 및 게이트웨이 트래픽을 확인할 수 있습니다.

**대시보드** 페이지에서 다음을 확인할 수 있습니다.

* 게이트웨이를 통해 전달되는 데이터의 양(입력 데이터와 출력 데이터 모두)
* 가상 네트워크에 대해 지정된 DNS 서버의 이름
* 게이트웨이 및 VPN 장치 간 연결
* VPN 장치에 대한 게이트웨이 연결을 구성하는 데 사용되는 공유 키

## <a name="how-to-change-the-vpn-routing-type-for-your-gateway"></a>게이트웨이의 VPN 라우팅 유형을 변경하는 방법
일부 연결 구성은 특정 게이트웨이 라우팅 유형에만 사용할 수 있으므로 기존 VPN 게이트웨이의 게이트웨이 VPN 라우팅 유형을 변경해야 할 수도 있습니다. 예를 들어 고정 게이트웨이를 사용하는 기존 사이트 간 연결에 지점 및 사이트 간 연결을 추가할 수 있습니다. 지점 및 사이트 간 연결에는 동적 게이트웨이가 필요합니다. 즉, P2S 연결을 구성하려면 게이트웨이 VPN 라우팅 형식을 정적에서 동적으로 변경해야 합니다.

게이트웨이 VPN 라우팅 형식을 변경해야 하는 경우 기존 게이트웨이를 삭제한 다음 새 라우팅 형식으로 새 게이트웨이를 만듭니다. 게이트웨이 라우팅 형식을 변경하기 위해 전체 가상 네트워크를 삭제할 필요는 없습니다.

게이트웨이 VPN 라우팅 형식을 변경하기 전에 VPN 장치에서 사용하려는 라우팅 형식을 지원하는지 확인해야 합니다. 새 라우팅 구성 샘플을 다운로드하고 VPN 장치 요구 사항을 확인하려면 [가상 네트워크 연결을 위한 VPN 장치 정보](vpn-gateway-about-vpn-devices.md)를 참조하세요.

> [!IMPORTANT]
> 가상 네트워크 VPN 게이트웨이를 삭제하면 게이트웨이에 할당된 VIP가 해제됩니다. 게이트웨이를 다시 만들면 새 VIP가 게이트웨이에 할당됩니다.
> 
> 

1. **기존 VPN 게이트웨이 삭제**
   
    가상 네트워크의 **대시보드** 페이지의 맨 아래쪽으로 이동하여 **게이트웨이 삭제**를 클릭합니다. 게이트웨이가 삭제되었다는 알림이 표시될 때까지 기다립니다. 게이트웨이가 삭제되었다는 알림이 화면에 표시된 이후에 새 게이트웨이를 만들 수 있습니다.
2. **새 VPN 게이트웨이 만들기**
   
    페이지 맨 위의 절차를 사용하여 새 게이트웨이를 만듭니다. [VPN 게이트웨이 만들기](#create-a-vpn-gateway).

## <a name="next-steps"></a>다음 단계
가상 네트워크에 가상 컴퓨터를 추가할 수 있습니다. [사용자 지정 가상 컴퓨터를 만드는 방법](../virtual-machines/windows/classic/createportal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)을 참조하세요.

지점 및 사이트 간 VPN 연결을 구성하려면 [지점 및 사이트 간 VPN 연결 구성](vpn-gateway-point-to-site-create.md)을 참조하세요.


