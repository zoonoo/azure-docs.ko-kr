---
title: 클라우드간편으로 Azure VMware 솔루션 - Azure AD를 사설 클라우드에서 ID 원본으로 사용
description: Azure Simple 프라이빗 클라우드에서 Azure AD를 ID 공급자로 추가하여 Azure에서 CloudSimple에 액세스하는 사용자를 인증하는 방법을 설명합니다.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 674ca8bea110d60557d1e50e7b68c9c3f7a92bf2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77564587"
---
# <a name="use-azure-ad-as-an-identity-provider-for-vcenter-on-cloudsimple-private-cloud"></a>Azure AD를 클라우드단순 프라이빗 클라우드의 vCenter용 ID 공급자로 사용

클라우드심플 프라이빗 클라우드 vCenter를 설정하여 VMware 관리자가 vCenter에 액세스하도록 Azure Active Directory(Azure AD)로 인증할 수 있습니다. 단일 사인온 ID 원본이 설정되면 **클라우드 소유자** 사용자는 ID 원본에서 vCenter에 사용자를 추가할 수 있습니다.  

다음과 같은 방법으로 Active Directory 도메인 및 도메인 컨트롤러를 설정할 수 있습니다.

* 온-프레미스에서 실행되는 Active Directory 도메인 및 도메인 컨트롤러
* Azure 구독에서 가상 컴퓨터로 실행되는 Active Directory 도메인 및 도메인 컨트롤러
* CloudSimple 프라이빗 클라우드에서 실행되는 새로운 Active Directory 도메인 및 도메인 컨트롤러
* Azure Active Directory 서비스

이 가이드는 Azure AD를 ID 원본으로 설정하는 데 필요한 작업에 대해 설명합니다.  Azure에서 실행 중인 온-프레미스 Active Directory 또는 Active Directory 사용에 대 한 자세한 내용은 id 원본을 설정 하는 자세한 지침에 [대 한 Active Directory를 사용 하 여 vCenter ID 원본 설정](set-vcenter-identity.md) 참조 하십시오.

## <a name="about-azure-ad"></a>Azure AD 정보

Azure AD는 Microsoft 다중 테넌트, 클라우드 기반 디렉터리 및 ID 관리 서비스입니다.  Azure AD는 사용자가 Azure에서 서로 다른 서비스를 인증하고 액세스할 수 있도록 확장 가능하고 일관되며 신뢰할 수 있는 인증 메커니즘을 제공합니다.  또한 Azure AD를 인증/ID 원본으로 사용하는 타사 서비스에 대한 보안 LDAP 서비스를 제공합니다.  Azure AD는 프라이빗 클라우드를 관리하는 사용자를 위해 프라이빗 클라우드에 대한 액세스를 제공하는 데 사용할 수 있는 핵심 디렉터리 서비스, 고급 ID 거버넌스 및 응용 프로그램 액세스 관리를 결합합니다.

Azure AD를 vCenter가 있는 ID 원본으로 사용하려면 Azure AD 및 Azure AD 도메인 서비스를 설정해야 합니다. 다음 지침을 따릅니다.

1. [Azure AD 및 Azure AD 도메인 서비스를 설정하는 방법](#set-up-azure-ad-and-azure-ad-domain-services)
2. [프라이빗 클라우드 vCenter에서 ID 소스를 설정하는 방법](#set-up-an-identity-source-on-your-private-cloud-vcenter)

## <a name="set-up-azure-ad-and-azure-ad-domain-services"></a>Azure AD 및 Azure AD 도메인 서비스 설정

시작하기 전에 전역 관리자 권한이 있는 Azure 구독에 액세스해야 합니다.  다음 단계는 일반적인 지침을 제공합니다. 자세한 내용은 Azure 설명서에 포함되어 있습니다.

### <a name="azure-ad"></a>Azure AD

> [!NOTE]
> Azure AD가 이미 있는 경우 이 섹션을 건너뛸 수 있습니다.

1. [Azure AD 설명서에](../active-directory/fundamentals/get-started-azure-ad.md)설명된 대로 구독에서 Azure AD를 설정합니다.
2. Azure Active Directory Premium에 대한 [등록에](../active-directory/fundamentals/active-directory-get-started-premium.md)설명된 대로 구독에서 Azure Active Directory Premium을 사용하도록 설정합니다.
3. 사용자 지정 도메인 이름을 설정하고 [Azure Active Directory에 사용자](../active-directory/fundamentals/add-custom-domain.md)지정 도메인 이름 추가에 설명된 대로 사용자 지정 도메인 이름을 확인합니다.
    1. Azure에 제공된 정보를 통해 도메인 등록 기관에 DNS 레코드를 설정합니다.
    2. 사용자 지정 도메인 이름을 기본 도메인으로 설정합니다.

다른 Azure AD 기능을 선택적으로 구성할 수 있습니다.  Azure AD를 사용 하 고 vCenter 인증을 사용 하기 위해 필요 하지 않습니다.

### <a name="azure-ad-domain-services"></a>Azure AD 도메인 서비스

> [!NOTE]
> 이는 Azure AD를 vCenter의 ID 원본으로 사용하도록 설정하는 중요한 단계입니다.  문제를 방지하려면 모든 단계가 올바르게 수행되었는지 확인하십시오.

1. [Azure 포털을 사용하여 Azure Active Directory 도메인 서비스 활성화에](../active-directory-domain-services/active-directory-ds-getting-started.md)설명된 대로 Azure AD 도메인 서비스를 활성화합니다.
2. Azure 포털을 사용하여 Azure [Active Directory 도메인 서비스 활성화에](../active-directory-domain-services/active-directory-ds-getting-started-network.md)설명된 대로 Azure AD 도메인 서비스에서 사용할 네트워크를 설정합니다.
3. [Azure 포털을 사용하여 Azure Active Directory 도메인 서비스 활성화에](../active-directory-domain-services/active-directory-ds-getting-started-admingroup.md)설명된 대로 Azure AD 도메인 서비스를 관리하기 위한 관리자 그룹 구성.
4. Azure [Active Directory 도메인](../active-directory-domain-services/active-directory-ds-getting-started-dns.md)서비스 활성화에 설명된 대로 Azure AD 도메인 서비스에 대한 DNS 설정을 업데이트합니다.  인터넷을 통해 AD에 연결하려면 Azure AD 도메인 서비스의 공용 IP 주소에 대한 DNS 레코드를 도메인 이름으로 설정합니다.
5. 사용자에 대한 암호 해시 동기화를 사용하도록 설정합니다.  이 단계를 사용하면 NT LAN 관리자(NTLM) 및 Kerberos 인증에 필요한 암호 해시를 Azure AD 도메인 서비스에 동기화할 수 있습니다. 암호 해시 동기화를 설정하면 사용자는 회사 자격 증명을 사용하여 관리되는 도메인에 로그인할 수 있습니다. [Azure Active Directory 도메인 서비스에 대한 암호 해시 동기화 활성화를](../active-directory-domain-services/active-directory-ds-getting-started-password-sync.md)참조하십시오.
    1. 클라우드 전용 사용자가 있는 경우 <a href="http://myapps.microsoft.com/" target="_blank">Azure AD 액세스 패널을</a> 사용하여 암호를 변경하여 암호 해시가 NTLM 또는 Kerberos에서 요구하는 형식으로 저장되도록 해야 합니다.  [클라우드 전용 사용자 계정에 대해 관리되는 도메인에 암호 해시 동기화 사용의 지침을 따릅니다.](../active-directory-domain-services/tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds)  이 단계는 Azure 포털 또는 Azure AD PowerShell cmdlet을 사용하여 Azure AD 디렉터리에서 만들어진 개별 사용자 및 새 사용자에 대해 수행해야 합니다. Azure AD 도메인 서비스에 액세스해야 하는 사용자는 <a href="http://myapps.microsoft.com/" target="_blank">Azure AD 액세스 패널을</a> 사용하고 프로필에 액세스하여 암호를 변경해야 합니다.

        > [!NOTE]
        > 조직에 클라우드 전용 사용자 계정이 있는 경우 Azure Active Directory Domain Services를 사용해야 하는 사용자는 모두 암호를 변경해야 합니다. 클라우드 전용 사용자 계정은 Azure Portal 또는 Azure AD PowerShell cmdlet을 사용하여 Azure AD 디렉터리에 만든 계정입니다. 이러한 사용자 계정은 온-프레미스 디렉터리에서 동기화되지 않습니다.

    2. 온-프레미스 Active 디렉터리에서 암호를 동기화하는 경우 [Active Directory 설명서의](../active-directory-domain-services/active-directory-ds-getting-started-password-sync-synced-tenant.md)단계를 따릅니다.

6.  Azure AD 도메인 서비스 관리 도메인에 [대한 LDAP(보안 LDAPS 구성)에](../active-directory-domain-services/tutorial-configure-ldaps.md)설명된 대로 Azure Active Directory 도메인 서비스에서 보안 LDAP를 구성합니다.
    1. Azure 항목에 설명된 대로 보안 LDAP에서 사용할 [인증서를 업로드하면 보안 LDAP에 대한 인증서를 얻을 수](../active-directory-domain-services/tutorial-configure-ldaps.md#create-a-certificate-for-secure-ldap)있습니다.  CloudSimple은 vCenter가 인증서를 신뢰할 수 있도록 인증 기관에서 발급한 서명된 인증서를 사용하는 것이 좋습니다.
    2. 설명된 대로 보안 LDAP를 사용하도록 [설정하여 Azure AD 도메인 서비스 관리 도메인에 대해 LDAP(보안 LDAPS)를 사용하도록 설정합니다.](../active-directory-domain-services/tutorial-configure-ldaps.md)
    3. ID 원본을 구성하는 동안 vCenter와 함께 사용할 수 있는 .cer 형식으로 인증서의 공용 부분(개인 키 제외)을 저장합니다.
    4. Azure AD 도메인 서비스에 대한 인터넷 액세스가 필요한 경우 '인터넷을 통해 LDAP에 대한 보안 액세스 허용' 옵션을 사용하도록 설정합니다.
    5. TCP 포트 636에 대한 Azure AD 도메인 서비스 NSG에 대한 인바운드 보안 규칙을 추가합니다.

## <a name="set-up-an-identity-source-on-your-private-cloud-vcenter"></a>프라이빗 클라우드 vCenter에 ID 소스 설정

1. 프라이빗 클라우드 vCenter에 대한 [권한을 에스컬레이션합니다.](escalate-private-cloud-privileges.md)
2. ID 원본 을 설정하는 데 필요한 구성 매개 변수를 수집합니다.

    | **옵션** | **설명** |
    |------------|-----------------|
    | **이름** | ID 원본의 이름입니다. |
    | **사용자를 위한 기본 DN** | 사용자에 대 한 기본 고유 한 이름입니다.  Azure AD의 경우 `OU=AADDC Users,DC=<domain>,DC=<domain suffix>` 다음 `OU=AADDC Users,DC=cloudsimplecustomer,DC=com`을 사용합니다.|
    | **도메인 이름** | 도메인의 FQDN은 예를 들어 example.com. 이 텍스트 상자에 IP 주소를 제공하지 마십시오. |
    | **도메인 별칭** | *(선택 사항)* 도메인 NetBIOS 이름입니다. SSPI 인증을 사용하는 경우 Active Directory 도메인의 NetBIOS 이름을 ID 원본의 별칭으로 추가합니다. |
    | **그룹에 대한 기본 DN** | 그룹의 기준 고유 이름입니다. Azure AD의 경우 `OU=AADDC Users,DC=<domain>,DC=<domain suffix>` 다음 예제를 사용합니다.`OU=AADDC Users,DC=cloudsimplecustomer,DC=com`|
    | **기본 서버 URL** | 도메인에 대한 기본 도메인 컨트롤러 LDAP 서버입니다.<br><br>형식을 `ldaps://hostname:port`사용합니다. 포트는 일반적으로 LDAPS 연결에 대해 636입니다. <br><br>기본 또는 보조 LDAP URL에서 사용할 `ldaps://` 때 Active Directory 서버의 LDAPS 끝점에 대한 신뢰를 설정하는 인증서가 필요합니다. |
    | **보조 서버 URL** | 장애 조치(failover)에 사용되는 보조 도메인 컨트롤러 LDAP 서버의 주소입니다. |
    | **인증서 선택** | ACTIVE Directory LDAP Server 또는 OpenLDAP Server ID 원본과 함께 LDAPS를 사용하려면 `ldaps://` URL 텍스트 상자를 입력한 후 인증서 선택 버튼이 나타납니다. 보조 URL은 필요하지 않습니다. |
    | **사용자** | 사용자 및 그룹에 대한 기본 DN에 대한 읽기 전용 액세스 권한이 있는 도메인에 있는 사용자의 ID입니다. |
    | **암호** | 사용자 이름으로 지정된 사용자의 암호입니다. |

3. 권한이 에스컬레이션된 후 프라이빗 클라우드 vCenter에 로그인합니다.
4. 이전 단계의 값을 사용하여 [vCenter의 ID 원본 추가의 지침에](set-vcenter-identity.md#add-an-identity-source-on-vcenter) 따라 Azure Active Directory를 ID 원본으로 설정합니다.
5. VMware [항목에](https://docs.vmware.com/en/VMware-vSphere/5.5/com.vmware.vsphere.security.doc/GUID-CDEA6F32-7581-4615-8572-E0B44C11D80D.html)설명된 대로 Azure AD의 사용자/그룹을 vCenter 그룹에 추가합니다.

> [!CAUTION]
> 새 사용자는 *클라우드-소유자 그룹,* *클라우드-글로벌-클러스터-관리자-그룹,* *클라우드-글로벌-스토리지-관리자-그룹,* *클라우드-글로벌-네트워크-관리자 그룹* 또는, *클라우드-글로벌-VM-관리자 그룹.*  *관리자* 그룹에 추가된 사용자는 자동으로 제거됩니다.  서비스 계정만 *Administrators* 그룹에 추가해야 합니다.

## <a name="next-steps"></a>다음 단계

* [프라이빗 클라우드 권한 모델에 대해 알아보기](learn-private-cloud-permissions.md)
