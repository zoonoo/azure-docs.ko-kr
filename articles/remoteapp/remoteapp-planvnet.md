---
title: "Azure RemoteApp 컬렉션에 대해 가상 네트워크를 계획하는 방법 | Microsoft 문서"
description: "Azure RemoteApp 컬렉션에 대한 가상 네트워크를 계획하는 방법에 대해 알아봅니다."
services: remoteapp
documentationcenter: 
author: mghosh1616
manager: mbaldwin
ms.assetid: ad9aff0e-f374-49c0-951d-4a7be1c36de0
ms.service: remoteapp
ms.workload: compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/23/2016
ms.author: mbaldwin
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 71537845edcca85a7d0722dd32264da9f6b579e9


---
# <a name="how-to-plan-your-virtual-network-for-azure-remoteapp"></a>Azure RemoteApp에 대한 가상 네트워크를 계획하는 방법
> [!IMPORTANT]
> Azure RemoteApp은 중단되었습니다. 자세한 내용은 [알림](https://go.microsoft.com/fwlink/?linkid=821148) 을 읽어보세요.
> 
> 

이 문서에서는 Azure RemoteApp에 대한 Azure 가상 네트워크(VNET) 및 서브넷을 설정하는 방법을 설명합니다. Azure 가상 네트워크에 익숙하지 않은 경우 클라우드로 네트워크 인프라를 가상화하고 Azure 및 온-프레미스 리소스로 하이브리드 솔루션을 만들 수 있도록 하는 기능입니다. 해당 서비스에 대한 자세한 내용은 [여기](../virtual-network/virtual-networks-overview.md)에서 확인할 수 있습니다.

Azure RemoteApp을 배포하고 있는 가상 네트워크의 트래픽(수신 및 송신)에 대한 보안 정책을 정의하려는 경우 Azure 가상 네트워크의 배포의 나머지 부분에서 Azure RemoteApp에 대한 별도 서브넷을 만드는 것이 좋습니다. Azure 가상 네트워크 서브넷에 대한 보안 정책을 정의하는 방법에 대한 자세한 내용은 [NSG(네트워크 보안 그룹)란?](../virtual-network/virtual-networks-nsg.md)을 읽어 보세요.

## <a name="types-of-azure-remoteapp-collections-with-azure-virtual-networks"></a>Azure 가상 네트워크를 사용한 Azure RemoteApp 컬렉션 형식
다음 그래픽은 가상 네트워크를 사용하려는 경우 두 가지 다른 컬렉션 옵션을 표시합니다.

### <a name="azure-remoteapp-cloud-collection-with-vnet"></a>VNET을 통한 Azure RemoteApp 클라우드 컬렉션
 ![VNET을 통한 Azure RemoteApp - 클라우드 컬렉션](./media/remoteapp-planvpn/ra-cloudvpn.png)

Azure RemoteApp 컬렉션을 나타내며 여기에서 액세스가 필요한 RemoteApp 세션이 호스팅하는 모든 리소스는 Azure에서 배포됩니다. Azure에 있는 RemoteApp VNET 또는 다른 VNET과 같이 동일한 VNET에 있을 수 있습니다.

### <a name="azure-remoteapp-hybrid-collection-with-vnet"></a>VNET을 통한 Azure RemoteApp 하이브리드 컬렉션
![VNET을 통한 Azure RemoteApp - 하이브리드 컬렉션](./media/remoteapp-planvpn/ra-hybridvpn.png)

Azure RemoteApp 컬렉션을 나타내며 여기에서 액세스가 필요한 RemoteApp 세션이 호스팅하는 일부 리소스는 온-프레미스에서 배포됩니다. RemoteApp VNET은 사이트 간 VPN 또는 Express Route와 같은 Azure 하이브리드 기술을 사용하여 온-프레미스 네트워크에 연결됩니다.

## <a name="how-the-system-works"></a>시스템 작동 방식
실제로 Azure RemoteApp은 Azure 가상 컴퓨터(업로드된 이미지)를 프로비전하는 동안 선택한 가상 네트워크 서브넷에 배포합니다. 하이브리드 컬렉션을 선택한 경우 가상 네트워크에 제공된 DNS 서버를 통해 프로비전 워크플로에 입력한 도메인 컨트롤러의 FQDN을 해결하기 위해 노력합니다.  
기존 가상 네트워크에 연결하고 있는 경우 Azure RemoteApp 서브넷에서 네트워크 보안 그룹의 필요한 포트를 노출해야 합니다. 

[Azure RemoteApp에 대해 충분히 큰 서브넷](remoteapp-vnetsizing.md)을 사용하는 것이 좋습니다. Azure 가상 네트워크에서 가장 많이 지원되는 것은 /8(CIDR 서브넷 정의 사용)입니다. 사용자의 서브넷은 더 많은 사용자가 응용 프로그램에 액세스할 때 확장하는 동안 모든 Azure RemoteApp VM을 수용하기에 충분해야 합니다. 

다음은 가상 네트워크 서브넷에서 사용하도록 설정해야 하는 항목입니다. 

1. 서브넷 아웃바운드 트래픽은 내부 Azure RemoteApp 서비스 중 하나와 통신하도록 포트 범위 10101-10175에서 허용되어야 합니다.
2. 아웃바운드 트래픽은 포트 443의 Azure 저장소에 연결하도록 서브넷에서 허용되어야 합니다.
3. Azure에서 호스팅되는 Active Directory가 있는 경우 Azure RemoteApp에 대한 가상 네트워크 서브넷 내의 모든 VM은 해당 도메인 컨트롤러에 연결할 수 있는지 확인합니다. 가상 네트워크의 DNS는 해당 도메인 컨트롤러의 FQDN을 확인할 수 있어야 합니다.

## <a name="virtual-network-with-forced-tunneling"></a>강제 터널링을 통한 가상 네트워크
[강제 터널링](../vpn-gateway/vpn-gateway-about-forced-tunneling.md) 은 이제 모든 새 Azure RemoteApp 컬렉션에 지원됩니다. 현재 강제 터널링을 지원하기 위해 기존 컬렉션의 마이그레이션이 지원되지 않습니다.  Azure RemoteApp에 연결 중인 VNET을 사용하여 모든 기존 컬렉션을 삭제하고 컬렉션에서 활성화된 강제 터널링을 가져오도록 새 것을 만들어야 합니다. 




<!--HONumber=Nov16_HO3-->


