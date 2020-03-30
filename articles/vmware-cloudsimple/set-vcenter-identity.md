---
title: 클라우드간이별 Azure VMware 솔루션 - 프라이빗 클라우드에서 vCenter ID 원본 설정
description: VMware 관리자가 vCenter에 액세스하도록 Active Directory를 사용하여 인증하도록 프라이빗 클라우드 vCenter를 설정하는 방법에 대해 설명합니다.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 5355e43ca6ac075e76a76ceb51be135cf4b62b0a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77564026"
---
# <a name="set-up-vcenter-identity-sources-to-use-active-directory"></a>활성 디렉터리를 사용하도록 vCenter ID 원본 설정

## <a name="about-vmware-vcenter-identity-sources"></a>VMware vCenter ID 원본 정보

VMware vCenter는 vCenter에 액세스하는 사용자의 인증을 위해 다양한 ID 원본을 지원합니다.  CloudSimple 프라이빗 클라우드 vCenter는 VMware 관리자가 vCenter에 액세스할 수 있도록 Active Directory를 사용하여 인증하도록 설정할 수 있습니다. 설정이 완료되면 **클라우드 소유자** 사용자는 ID 원본에서 vCenter에 사용자를 추가할 수 있습니다.  

다음과 같은 방법으로 Active Directory 도메인 및 도메인 컨트롤러를 설정할 수 있습니다.

* 온-프레미스에서 실행되는 Active Directory 도메인 및 도메인 컨트롤러
* Azure 구독에서 가상 컴퓨터로 실행되는 Active Directory 도메인 및 도메인 컨트롤러
* 프라이빗 클라우드에서 실행되는 새로운 Active Directory 도메인 및 도메인 컨트롤러
* Azure Active Directory 서비스

이 가이드에서는 온-프레미스 또는 구독에서 가상 컴퓨터로 실행되는 Active Directory 도메인 및 도메인 컨트롤러를 설정하는 작업에 대해 설명합니다.  Azure AD를 ID 원본으로 사용하려면 Id 원본 설정에 대한 자세한 지침은 [CloudSimple 프라이빗 클라우드의 vCenter용 ID 공급자로 Azure AD 사용을](azure-ad.md) 참조하십시오.

[ID 원본을 추가하기](#add-an-identity-source-on-vcenter)전에 vCenter 권한을 일시적으로 [에스컬레이션합니다.](escalate-private-cloud-privileges.md)

> [!CAUTION]
> 새 사용자는 *클라우드-소유자 그룹,* *클라우드-글로벌-클러스터-관리자-그룹,* *클라우드-글로벌-스토리지-관리자-그룹,* *클라우드-글로벌-네트워크-관리자 그룹* 또는, *클라우드-글로벌-VM-관리자 그룹.*  *관리자* 그룹에 추가된 사용자는 자동으로 제거됩니다.  서비스 계정만 *Administrators* 그룹에 추가해야 하며 서비스 계정은 vSphere 웹 UI에 로그인하는 데 사용할 수 없습니다.   


## <a name="identity-source-options"></a>ID 원본 옵션

* [온-프레미스 Active Directory를 단일 사인온 ID 원본으로 추가](#add-on-premises-active-directory-as-a-single-sign-on-identity-source)
* [프라이빗 클라우드에 새 Active Directory 설정](#set-up-new-active-directory-on-a-private-cloud)
* [Azure에서 활성 디렉터리 설정](#set-up-active-directory-on-azure)

## <a name="add-on-premises-active-directory-as-a-single-sign-on-identity-source"></a>온-프레미스 활성 디렉터리를 단일 사인온 ID 원본으로 추가

온-프레미스 Active Directory를 단일 사인온 ID 원본으로 설정하려면 다음을 수행해야 합니다.

* 온-프레미스 데이터 센터에서 프라이빗 클라우드로 [사이트 간 VPN 연결.](vpn-gateway.md#set-up-a-site-to-site-vpn-gateway)
* 온-프레미스 DNS 서버 IP는 vCenter 및 플랫폼 서비스 컨트롤러(PSC)에 추가되었습니다.

Active Directory 도메인을 설정할 때 다음 표의 정보를 사용합니다.

| **옵션** | **설명** |
|------------|-----------------|
| **이름** | ID 원본의 이름입니다. |
| **사용자를 위한 기본 DN** | 사용자에 대 한 기본 고유 한 이름입니다. |
| **도메인 이름** | 도메인의 FQDN은 예를 들어 example.com. 이 텍스트 상자에 IP 주소를 제공하지 마십시오. |
| **도메인 별칭** | 도메인 NetBIOS 이름입니다. SSPI 인증을 사용하는 경우 Active Directory 도메인의 NetBIOS 이름을 ID 원본의 별칭으로 추가합니다. |
| **그룹에 대한 기본 DN** | 그룹의 기준 고유 이름입니다. |
| **기본 서버 URL** | 도메인에 대한 기본 도메인 컨트롤러 LDAP 서버입니다.<br><br>형식 `ldap://hostname:port` 또는 `ldaps://hostname:port`을 사용합니다. 포트는 일반적으로 LDAP 연결의 경우 389이고 LDAPS 연결의 경우 636입니다. Active Directory 다중 도메인 컨트롤러 배포의 경우 포트는 일반적으로 LDAP의 경우 3268이고 LDAPS의 경우 3269입니다.<br><br>기본 또는 보조 LDAP URL에서 사용할 `ldaps://` 때 Active Directory 서버의 LDAPS 끝점에 대한 신뢰를 설정하는 인증서가 필요합니다. |
| **보조 서버 URL** | 장애 조치(failover)에 사용되는 보조 도메인 컨트롤러 LDAP 서버의 주소입니다. |
| **인증서 선택** | ACTIVE Directory LDAP Server 또는 OpenLDAP Server ID 원본과 함께 LDAPS를 사용하려면 `ldaps://` URL 텍스트 상자를 입력한 후 인증서 선택 버튼이 나타납니다. 보조 URL은 필요하지 않습니다. |
| **사용자** | 사용자 및 그룹에 대한 기본 DN에 대한 읽기 전용 액세스 권한이 있는 도메인에 있는 사용자의 ID입니다. |
| **암호** | 사용자 이름으로 지정된 사용자의 암호입니다. |

이전 테이블에 정보가 있는 경우 vCenter에서 온-프레미스 Active Directory를 단일 사인온 ID 원본으로 추가할 수 있습니다.

> [!TIP]
> 단일 사인온 ID 원본에 대한 자세한 내용은 [VMware 설명서 페이지에서](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.psc.doc/GUID-B23B1360-8838-4FF2-B074-71643C4CB040.html)확인할 수 있습니다.

## <a name="set-up-new-active-directory-on-a-private-cloud"></a>프라이빗 클라우드에 새 Active Directory 설정

프라이빗 클라우드에서 새 Active Directory 도메인을 설정하고 단일 사인온의 ID 원본으로 사용할 수 있습니다.  Active Directory 도메인은 기존 Active Directory 포리스트의 일부이거나 독립 포리스트로 설정할 수 있습니다.

### <a name="new-active-directory-forest-and-domain"></a>새 활성 디렉터리 포리스트 및 도메인

새 Active Directory 포리스트 및 도메인을 설정하려면 다음이 필요합니다.

* 새 Active Directory 포리스트 및 도메인의 도메인 컨트롤러로 사용할 Microsoft Windows 서버를 실행하는 하나 이상의 가상 시스템입니다.
* 이름 확인을 위해 DNS 서비스를 실행하는 하나 이상의 가상 시스템입니다.

자세한 [단계는 새 Windows 서버 2012 활성 디렉터리 포리스트 설치를](https://docs.microsoft.com/windows-server/identity/ad-ds/deploy/install-a-new-windows-server-2012-active-directory-forest--level-200-) 참조하십시오.

> [!TIP]
> 고가용성 서비스를 위해 여러 도메인 컨트롤러와 DNS 서버를 설정하는 것이 좋습니다.

Active Directory 포리스트 및 도메인을 설정한 후 새 Active Directory에 대 [한 vCenter에 ID 원본을 추가할](#add-an-identity-source-on-vcenter) 수 있습니다.

### <a name="new-active-directory-domain-in-an-existing-active-directory-forest"></a>기존 Active Directory 포리스트의 새 Active Directory 도메인

기존 Active Directory 포리스트에 새 Active Directory 도메인을 설정하려면 다음이 필요합니다.

* Active Directory 포리스트 위치에 대한 사이트 간 VPN 연결.
* DNS 서버는 기존 Active Directory 포리스트의 이름을 확인합니다.

자세한 내용은 [새 Windows Server 2012 Active Directory 자식 또는 트리 도메인 설치를](https://docs.microsoft.com/windows-server/identity/ad-ds/deploy/install-a-new-windows-server-2012-active-directory-child-or-tree-domain--level-200-) 참조하십시오.

Active Directory 도메인을 설정한 후 새 Active Directory에 대한 [vCenter에 ID 원본을 추가할](#add-an-identity-source-on-vcenter) 수 있습니다.

## <a name="set-up-active-directory-on-azure"></a>Azure에서 활성 디렉터리 설정

Azure에서 실행되는 Active Directory는 온-프레미스에서 실행되는 Active Directory와 유사합니다.  azure에서 실행되는 Active Directory를 vCenter의 단일 사인온 ID 원본으로 설정하려면 vCenter 서버와 PSC가 Active Directory 서비스가 실행 중인 Azure 가상 네트워크에 대한 네트워크 연결이 있어야 합니다.  Active Directory 서비스가 CloudSimple 프라이빗 클라우드로 실행되는 Azure 가상 네트워크의 [ExpressRoute를 사용하여](azure-expressroute-connection.md) Azure 가상 네트워크 연결을 사용하여 이 연결을 설정할 수 있습니다.

네트워크 연결이 설정된 후 [온-프레미스 활성 디렉터리 에서 단일 사인온 ID 원본으로 추가하는](#add-on-premises-active-directory-as-a-single-sign-on-identity-source) 단계를 수행하여 ID 원본으로 추가합니다.  

## <a name="add-an-identity-source-on-vcenter"></a>vCenter에서 ID 원본 추가

1. 프라이빗 클라우드에서 [권한을 에스컬레이션합니다.](escalate-private-cloud-privileges.md)

2. 프라이빗 클라우드의 경우 vCenter에 로그인합니다.

3. **홈 > 관리를**선택합니다.

    ![관리](media/OnPremAD01.png)

4. **> 구성에서 단일 사인을**선택합니다.

    ![Single Sign On](media/OnPremAD02.png)

5. ID **원본** 탭을 **+** 열고 클릭하여 새 ID 원본을 추가합니다.

    ![ID 소스](media/OnPremAD03.png)

6. **활성 디렉터리를 LDAP 서버로** 선택하고 다음 을 **클릭합니다.**

    ![Active Directory](media/OnPremAD04.png)

7. 환경에 대한 ID 원본 매개 변수를 지정하고 다음 을 **클릭합니다.**

    ![Active Directory](media/OnPremAD05.png)

8. 설정을 검토하고 **완료를**클릭합니다.
