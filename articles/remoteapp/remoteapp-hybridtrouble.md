---
title: "RemoteApp 하이브리드 컬렉션 만들기 문제 해결 | Microsoft Docs"
description: "RemoteApp 하이브리드 컬렉션 만들기 오류 문제를 해결하는 방법을 알아봅니다."
services: remoteapp
documentationcenter: 
author: vkbucha
manager: mbaldwin
ms.assetid: b32033ee-8d52-4e74-bb78-86ca873c34e2
ms.service: remoteapp
ms.workload: compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/23/2016
ms.author: mbaldwin
translationtype: Human Translation
ms.sourcegitcommit: 5cce99eff6ed75636399153a846654f56fb64a68
ms.openlocfilehash: a486dcb3f994cd78311ee86521a6792a4d57438e
ms.lasthandoff: 03/31/2017


---
# <a name="troubleshoot-creating-azure-remoteapp-hybrid-collections"></a>Azure RemoteApp 하이브리드 컬렉션 만들기 문제 해결
> [!IMPORTANT]
> Azure RemoteApp은 2017년 8월 31일에 중단되었습니다. 자세한 내용은 [알림](https://go.microsoft.com/fwlink/?linkid=821148) 을 읽어보세요.
> 
> 

하이브리드 컬렉션은 Azure 클라우드에 호스트되고 데이터를 저장하지만 사용자가 로컬 네트워크에 저장된 데이터 및 리소스에 액세스할 수 있게 합니다. 사용자는 Azure Active Directory와 동기화 또는 페더레이션된 회사 자격 증명으로 로그인하여 앱에 액세스할 수 있습니다. 기존 Azure 가상 네트워크를 사용하는 하이브리드 컬렉션을 배포하거나 새 가상 네트워크를 만들 수 있습니다. Azure RemoteApp이 향후 증가할 것을 예상하여 CIDR 범위가 충분히 큰 가상 네트워크 서브넷을 만들거나 사용하는 것이 좋습니다.

컬렉션을 아직 만들지 않았나요? [하이브리드 컬렉션을 만들기](remoteapp-create-hybrid-deployment.md) 의 단계를 참조하세요.

컬렉션을 만드는 데 문제가 있거나 컬렉션이 생각대로 작동하지 않으면 다음 정보를 확인하세요.

## <a name="your-image-is-invalid"></a>이미지가 올바르지 않음
Azure의 컬렉션 프로비전을 대기하는 중에 "GoldImageInvalid" 등의 메시지가 나타나면 템플릿 이미지가 [정의된 이미지 요구 사항](remoteapp-imagereqs.md)에 부합하지 않음을 나타냅니다. 따라서 해당 [요구 사항](remoteapp-imagereqs.md)을 읽고 이미지를 수정한 다음 다시 컬렉션을 만듭니다.

## <a name="does-your-vnet-have-network-security-groups-defined"></a>VNET에 네트워크 보안 그룹이 정의되어 있나요?
컬렉션에 사용하고 있는 서브넷에 네트워크 보안 그룹이 정의되어 있는 경우 해당 서브넷 내에서 이러한 [URL 및 포트](remoteapp-ports.md) 에 액세스할 수 있는지 확인합니다.

더 엄격한 제어를 위해 서브넷에 배포한 VM에 네트워크 보안 그룹을 더 추가할 수 있습니다.

## <a name="are-you-using-your-own-dns-servers-and-are-they-accessible-from-your-vnet-subnet"></a>자체 DNS 서버를 사용하고 있나요? VNET 서브넷에서 이러한 DNS 서버에 액세스할 수 있나요?
> [!NOTE]
> VNET의 DNS 서버가 항상 작동해야 하며 VNET에 호스팅된 가상 컴퓨터를 항상 확인할 수 있어야 합니다. 이 작업에 Google DNS를 사용하지 마십시오.
> 
> 

하이브리드 컬렉션의 경우 자체 DNS 서버를 사용합니다. 가상 네트워크를 만드는 경우 관리 포털을 통해 지정하거나 네트워크 구성 스키마에 지정합니다. DNS 서버는 라운드 로빈과 반대인 장애 조치(Failover) 방식으로 지정된 순서대로 사용됩니다.  
[VM 및 역할 인스턴스에 대한 이름 확인](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md) 을 참조하여 DNS 서버가 올바르게 구성되었는지 확인합니다.

컬렉션의 DNS 서버에 액세스할 수 있으며 이 컬렉션에 대해 지정한 VNET 서브넷에서 이러한 DNS 서버를 사용할 수 있는지 확인합니다.

예:

    <VirtualNetworkConfiguration>
    <Dns>
      <DnsServers>
        <DnsServer name="" IPAddress=""/>
      </DnsServers>
    </Dns>
    </VirtualNetworkConfiguration>

![DNS 정의](./media/remoteapp-hybridtrouble/dnsvpn.png)

## <a name="are-you-using-an-active-directory-domain-controller-in-your-collection"></a>컬렉션에서 Active Directory 도메인 컨트롤러를 사용하고 있나요?
현재는 하나의 Active Directory 도메인만 Azure RemoteApp과 연결될 수 있습니다. 하이브리드 컬렉션은 Windows Server Active Directory 배포에서 DirSync 도구를 사용하여 동기화된, 특히 암호 동기화 옵션을 사용하여 동기화되거나 AD FS(Active Directory Federation Services) 페더레이션을 구성하여 동기화된 Azure Active Directory 계정만 지원합니다. 온-프레미스 도메인의 UPN 도메인 접미사와 일치하는 사용자 지정 도메인을 만들고 디렉터리 통합을 설정해야 합니다.

자세한 내용은 [Azure RemoteApp에 대해 Active Directory 구성](remoteapp-ad.md) 을 참조하세요.

제공된 도메인 세부 정보가 유효하며 Azure RemoteApp에 사용되는 서브넷에 만들어진 VM에서 도메인 컨트롤러에 연결할 수 있는지 확인합니다. 또한 제공된 도메인에 컴퓨터를 추가할 수 있는 권한이 제공된 서비스 계정 자격 증명에 있고 VNET에 제공된 DNS에서 제공된 AD 이름을 확인할 수 있는지 확인합니다.

## <a name="what-domain-name-did-you-specify-when-you-created-your-collection"></a>컬렉션을 만들 때 어떤 도메인 이름을 지정했나요?
만들거나 추가한 도메인 이름은 내부 도메인 이름(Azure AD 도메인 이름이 아님)이어야 하며 확인할 수 있는 DNS 형식(contoso.local)이어야 합니다. 예를 들어 Active Directory 내부 이름(contoso.local) 및 Active Directory UPN(contoso.com)이 있는 경우 컬렉션을 만들 때 내부 이름을 사용해야 합니다.


