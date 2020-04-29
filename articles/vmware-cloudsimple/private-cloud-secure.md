---
title: CloudSimple-Secure 사설 클라우드의 Azure VMware 솔루션
description: CloudSimple 사설 클라우드를 통해 Azure VMware 솔루션을 보호 하는 방법을 설명 합니다.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 4541874a9e8fc4111e5c65d02f07535c4d14f9f1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "77565981"
---
# <a name="how-to-secure-your-private-cloud-environment"></a>사설 클라우드 환경을 보호 하는 방법

Azure에서 CloudSimple 서비스, CloudSimple 포털 및 사설 클라우드에 대 한 RBAC (역할 기반 액세스 제어)를 정의 합니다.  사설 클라우드의 vCenter에 액세스 하기 위한 사용자, 그룹 및 역할은 VMware SSO를 사용 하 여 지정 됩니다.  

## <a name="rbac-for-cloudsimple-service"></a>CloudSimple service에 대 한 RBAC

CloudSimple 서비스를 만들려면 Azure 구독에 대 한 **소유자** 또는 **참가자** 역할이 필요 합니다.  기본적으로 모든 소유자와 참가자는 CloudSimple 서비스를 만들고 사설 클라우드를 만들고 관리 하기 위한 CloudSimple 포털에 액세스할 수 있습니다.  지역 마다 CloudSimple 서비스를 하나만 만들 수 있습니다.  특정 관리자에 대 한 액세스를 제한 하려면 다음 절차를 따르세요.

1. Azure Portal의 새 **리소스 그룹** 에 Cloudsimple 서비스 만들기
2. 리소스 그룹에 대 한 RBAC를 지정 합니다.
3. 노드를 구매 하 고 CloudSimple service와 동일한 리소스 그룹 사용

리소스 그룹에 대 한 **소유자** 또는 **참가자** 권한이 있는 사용자만 CloudSimple 서비스를 보고 cloudsimple 포털을 시작 합니다.

RBAC에 대 한 자세한 내용은 [Azure 리소스에 대 한 rbac (역할 기반 액세스 제어) 란?](../role-based-access-control/overview.md)을 참조 하세요.

## <a name="rbac-for-private-cloud-vcenter"></a>사설 클라우드 vCenter 용 RBAC

사설 클라우드를 `CloudOwner@cloudsimple.local` 만들 때 vCenter SSO 도메인에 기본 사용자가 만들어집니다.  CloudOwner 사용자에 게 vCenter를 관리할 권한이 있습니다. 다른 사용자에 게 액세스를 제공 하기 위해 추가 id 소스가 vCenter SSO에 추가 됩니다.  미리 정의 된 역할 및 그룹은 사용자를 추가 하는 데 사용할 수 있는 vCenter에 설정 됩니다.

### <a name="add-new-users-to-vcenter"></a>VCenter에 새 사용자 추가

1. 사설 클라우드의 **CloudOwner\@cloudsimple. 로컬** 사용자에 대 한 [권한을 에스컬레이션](escalate-private-cloud-privileges.md) 합니다.
2. **CloudOwner\@cloudsimple. local** 을 사용 하 여 vCenter에 로그인 합니다.
3. [VCenter Single Sign-on 사용자를 추가](https://docs.vmware.com/en/VMware-vSphere/5.5/com.vmware.vsphere.security.doc/GUID-72BFF98C-C530-4C50-BF31-B5779D2A4BBB.html)합니다.
4. [VCenter Single Sign-On 그룹](https://docs.vmware.com/en/VMware-vSphere/5.5/com.vmware.vsphere.security.doc/GUID-CDEA6F32-7581-4615-8572-E0B44C11D80D.html)에 사용자를 추가 합니다.

미리 정의 된 역할 및 그룹에 대 한 자세한 내용은 [VMware vCenter의 Cloudsimple 사설 클라우드 권한 모델](learn-private-cloud-permissions.md) 문서를 참조 하세요.

### <a name="add-new-identity-sources"></a>새 id 소스 추가

사설 클라우드의 vCenter SSO 도메인에 대해 다른 id 공급자를 추가할 수 있습니다.  Id 공급자는 인증을 제공 하 고 vCenter SSO 그룹은 사용자에 대 한 권한 부여를 제공 합니다.

* 사설 클라우드 vCenter에서 [id 공급자로 Active Directory를 사용](set-vcenter-identity.md) 합니다.
* 사설 클라우드 vCenter에서 [AZURE AD를 id 공급자로 사용](azure-ad.md)

1. 사설 클라우드의 **CloudOwner\@cloudsimple. 로컬** 사용자에 대 한 [권한을 에스컬레이션](escalate-private-cloud-privileges.md) 합니다.
2. **CloudOwner\@cloudsimple. local** 을 사용 하 여 vCenter에 로그인 합니다.
3. Id 공급자의 사용자를 [vCenter Single Sign-On 그룹](https://docs.vmware.com/en/VMware-vSphere/5.5/com.vmware.vsphere.security.doc/GUID-CDEA6F32-7581-4615-8572-E0B44C11D80D.html)에 추가 합니다.

## <a name="secure-network-on-your-private-cloud-environment"></a>사설 클라우드 환경의 보안 네트워크

사설 클라우드 환경의 네트워크 보안은 네트워크 액세스를 보호 하 고 리소스 간의 네트워크 트래픽을 제어 하 여 제어 됩니다.

### <a name="access-to-private-cloud-resources"></a>사설 클라우드 리소스에 대 한 액세스

사설 클라우드 vCenter 및 리소스 액세스는 보안 네트워크 연결을 통해 수행 됩니다.

* **[Express 경로 연결](on-premises-connection.md)** 입니다. Express 경로는 온-프레미스 환경에서의 안전 하 고 대역폭이 낮은 대기 시간 연결을 제공 합니다.  연결을 사용 하면 온-프레미스 서비스, 네트워크 및 사용자가 사설 클라우드 vCenter에 액세스할 수 있습니다.
* **[사이트 간 VPN gateway](vpn-gateway.md)**. 사이트 간 VPN은 보안 터널을 통해 온-프레미스에서 사설 클라우드 리소스에 대 한 액세스를 제공 합니다.  사설 클라우드로 네트워크 트래픽을 보내고 받을 수 있는 온-프레미스 네트워크를 지정 합니다.
* **[지점 및 사이트 간 VPN gateway](vpn-gateway.md#set-up-a-site-to-site-vpn-gateway)**. 지점 및 사이트 간 VPN 연결을 사용 하 여 사설 클라우드 vCenter에 대 한 빠른 원격 액세스를 사용 합니다.

### <a name="control-network-traffic-in-private-cloud"></a>사설 클라우드에서 네트워크 트래픽 제어

방화벽 테이블 및 규칙은 사설 클라우드의 네트워크 트래픽을 제어 합니다.  방화벽 테이블을 사용 하면 테이블에 정의 된 규칙의 조합에 따라 원본 네트워크 또는 IP 주소와 대상 네트워크 또는 IP 주소 간의 네트워크 트래픽을 제어할 수 있습니다.

1. [방화벽 테이블](firewall.md#add-a-new-firewall-table)을 만듭니다.
2. 방화벽 테이블에 [규칙을 추가](firewall.md#create-a-firewall-rule) 합니다.
3. [VLAN/서브넷에 방화벽 테이블을 연결](firewall.md#attach-vlans-subnet)합니다.
