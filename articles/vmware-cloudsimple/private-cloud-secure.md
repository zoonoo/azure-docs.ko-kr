---
title: 클라우드에 의한 Azure VMware 솔루션 단순 - 보안 프라이빗 클라우드
description: 클라우드단순 프라이빗 클라우드로 Azure VMware 솔루션을 보호하는 방법에 대해 설명합니다.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 4541874a9e8fc4111e5c65d02f07535c4d14f9f1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77565981"
---
# <a name="how-to-secure-your-private-cloud-environment"></a>프라이빗 클라우드 환경을 보호하는 방법

Azure에서 CloudSimple 서비스, CloudSimple 포털 및 사설 클라우드에 대한 역할 기반 액세스 제어(RBAC)를 정의합니다.  VMware SSO를 사용하여 vCenter 오브 프라이빗 클라우드에 액세스하기 위한 사용자, 그룹 및 역할이 지정됩니다.  

## <a name="rbac-for-cloudsimple-service"></a>클라우드 간편 서비스를 위한 RBAC

CloudSimple 서비스를 만들려면 Azure 구독에서 **소유자** 또는 **기여자** 역할이 필요합니다.  기본적으로 모든 소유자와 기여자는 CloudSimple 서비스를 만들고 사설 클라우드를 만들고 관리하기 위한 CloudSimple 포털에 액세스할 수 있습니다.  리전당 하나의 CloudSimple 서비스만 만들 수 있습니다.  특정 관리자에 대한 액세스를 제한하려면 아래 절차를 따르십시오.

1. Azure 포털의 새 **리소스 그룹에** CloudSimple 서비스 만들기
2. 리소스 그룹에 대한 RBAC를 지정합니다.
3. 노드를 구입하고 CloudSimple 서비스와 동일한 리소스 그룹을 사용합니다.

리소스 그룹에 **소유자** 또는 **기여자** 권한이 있는 사용자만 CloudSimple 서비스를 보고 CloudSimple 포털을 시작합니다.

RBAC에 대한 자세한 내용은 [Azure 리소스에 대한 역할 기반 액세스 제어(RBAC)를](../role-based-access-control/overview.md)참조하세요.

## <a name="rbac-for-private-cloud-vcenter"></a>프라이빗 클라우드용 RBAC vCenter

기본 사용자는 `CloudOwner@cloudsimple.local` 프라이빗 클라우드를 만들 때 vCenter SSO 도메인에서 만들어집니다.  클라우드소유자 사용자에게는 vCenter를 관리하는 권한이 있습니다. 다른 사용자에 대한 액세스 권한을 부여하기 위해 vCenter SSO에 추가 ID 원본이 추가됩니다.  미리 정의된 역할 및 그룹은 추가 사용자를 추가하는 데 사용할 수 있는 vCenter에 설정됩니다.

### <a name="add-new-users-to-vcenter"></a>vCenter에 새 사용자 추가

1. 프라이빗 클라우드에서 **CloudOwner\@cloudsimple.local** 사용자에 대한 [권한을 에스컬레이션합니다.](escalate-private-cloud-privileges.md)
2. **클라우드 소유자\@클라우드simple.local를** 사용 하 여 vCenter에 로그인
3. [vCenter 단일 사인온 사용자 추가](https://docs.vmware.com/en/VMware-vSphere/5.5/com.vmware.vsphere.security.doc/GUID-72BFF98C-C530-4C50-BF31-B5779D2A4BBB.html).
4. [vCenter 단일 사인온 그룹에](https://docs.vmware.com/en/VMware-vSphere/5.5/com.vmware.vsphere.security.doc/GUID-CDEA6F32-7581-4615-8572-E0B44C11D80D.html)사용자를 추가합니다.

미리 정의된 역할 및 그룹에 대한 자세한 내용은 [VMware vCenter 문서의 CloudSimple 프라이빗 클라우드 사용 권한 모델을](learn-private-cloud-permissions.md) 참조하십시오.

### <a name="add-new-identity-sources"></a>새 ID 원본 추가

프라이빗 클라우드의 vCenter SSO 도메인에 대한 ID 공급자를 추가할 수 있습니다.  ID 공급자는 인증을 제공하고 vCenter SSO 그룹은 사용자에 대한 권한 부여를 제공합니다.

* 프라이빗 클라우드 vCenter에서 [Active Directory를 ID 공급자로 사용합니다.](set-vcenter-identity.md)
* 프라이빗 클라우드 vCenter에서 [Azure AD를 ID 공급자로 사용](azure-ad.md)

1. 프라이빗 클라우드에서 **CloudOwner\@cloudsimple.local** 사용자에 대한 [권한을 에스컬레이션합니다.](escalate-private-cloud-privileges.md)
2. **클라우드 소유자\@클라우드simple.local를** 사용 하 여 vCenter에 로그인
3. id 공급자의 사용자를 [vCenter 단일 사인온 그룹에](https://docs.vmware.com/en/VMware-vSphere/5.5/com.vmware.vsphere.security.doc/GUID-CDEA6F32-7581-4615-8572-E0B44C11D80D.html)추가합니다.

## <a name="secure-network-on-your-private-cloud-environment"></a>프라이빗 클라우드 환경에서 안전한 네트워크

프라이빗 클라우드 환경의 네트워크 보안은 네트워크 액세스를 보호하고 리소스 간의 네트워크 트래픽을 제어하여 제어됩니다.

### <a name="access-to-private-cloud-resources"></a>프라이빗 클라우드 리소스에 대한 액세스

프라이빗 클라우드 vCenter 및 리소스 액세스는 보안 네트워크 연결을 통해 제공됩니다.

* **[익스프레스루트 연결.](on-premises-connection.md)** ExpressRoute는 온-프레미스 환경에서 안전한 고대역폭, 낮은 대기 시간 연결을 제공합니다.  연결을 사용하면 온-프레미스 서비스, 네트워크 및 사용자가 프라이빗 클라우드 vCenter에 액세스할 수 있습니다.
* **[사이트 간 VPN 게이트웨이.](vpn-gateway.md)** 사이트 간 VPN을 통해 온-프레미스에서 보안 터널을 통해 프라이빗 클라우드 리소스에 액세스할 수 있습니다.  온-프레미스 네트워크가 프라이빗 클라우드로 네트워크 트래픽을 보내고 받을 수 있도록 지정합니다.
* **[지점 간 VPN 게이트웨이.](vpn-gateway.md#set-up-a-site-to-site-vpn-gateway)** 프라이빗 클라우드 vCenter에 대한 빠른 원격 액세스를 위해 지점 간 VPN 연결을 사용합니다.

### <a name="control-network-traffic-in-private-cloud"></a>프라이빗 클라우드에서 네트워크 트래픽 제어

방화벽 테이블과 규칙은 프라이빗 클라우드의 네트워크 트래픽을 제어합니다.  방화벽 테이블을 사용하면 테이블에 정의된 규칙의 조합에 따라 원본 네트워크 또는 IP 주소와 대상 네트워크 또는 IP 주소 간의 네트워크 트래픽을 제어할 수 있습니다.

1. 방화벽 [테이블을](firewall.md#add-a-new-firewall-table)만듭니다.
2. 방화벽 테이블에 [규칙을 추가합니다.](firewall.md#create-a-firewall-rule)
3. [VLAN/서브넷에 방화벽 테이블을 연결합니다.](firewall.md#attach-vlans-subnet)
