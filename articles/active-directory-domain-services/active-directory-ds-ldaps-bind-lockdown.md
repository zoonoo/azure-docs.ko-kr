---
title: Azure AD Domain Services 관리되는 도메인에 대해 보안 LDAP(LDAPS) 바인딩 | Microsoft Docs
description: 보안 LDAP(LDAPS)를 사용하여 Azure AD Domain Services 관리되는 도메인에 바인딩
services: active-directory-ds
documentationcenter: ''
author: MikeStephens-MS
manager: daveba
editor: curtand
ms.assetid: 6871374a-0300-4275-9a45-a39a52c65ae4
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: mstephen
ms.openlocfilehash: 47c9553b4191fe6dbae8d92d75dfae83f191a063
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/27/2019
ms.locfileid: "66234865"
---
# <a name="bind-to-an-azure-ad-domain-services-managed-domain-using-secure-ldap-ldaps"></a>보안 LDAP(LDAPS)를 사용하여 Azure AD Domain Services 관리되는 도메인에 바인딩

## <a name="before-you-begin"></a>시작하기 전에
[작업 4: 인터넷에서 관리되는 도메인에 액세스하도록 DNS 구성](active-directory-ds-ldaps-configure-dns.md)을 완료합니다.


## <a name="task-5-bind-to-the-managed-domain-over-ldap-using-ldpexe"></a>작업 5: LDP.exe를 사용하여 LDAP를 통해 관리되는 도메인에 바인딩
원격 서버 관리 도구 패키지에 포함된 LDP.exe 도구를 사용하여 LDAP를 통해 바인딩하고 검색할 수 있습니다.

먼저 LDP를 열고 관리되는 도메인에 연결합니다. **연결**을 클릭하고 메뉴에서 **연결...** 을 클릭합니다. 관리되는 도메인의 DNS 도메인 이름을 지정합니다. 연결에 사용할 포트를 지정합니다. LDAP 연결의 경우, 포트 389를 사용합니다. LDAPS 연결의 경우, 포트 636을 사용합니다. **확인** 단추를 클릭하여 관리되는 도메인에 연결합니다.

다음으로 관리되는 도메인에 바인딩합니다. **연결**을 클릭하고 메뉴에서 **바인딩...** 을 클릭합니다. ‘AAD DC 관리자’ 그룹에 속한 사용자 계정의 자격 증명을 제공합니다.

**보기**를 선택한 다음, 메뉴에서 **트리**를 선택합니다. 기본 DN 필드를 비워 두고 [확인]을 클릭합니다. 검색할 컨테이너로 이동하고, 컨테이너를 마우스 오른쪽 단추로 클릭하고, [검색]을 선택합니다.

> [!TIP]
> - Azure AD에서 동기화된 사용자 및 그룹은 **AADDC 사용자** 조직 구성 단위에 저장됩니다. 이 조직 구성 단의 검색 경로는 ```OU=AADDC Users,DC=CONTOSO100,DC=COM```과 같습니다.
> - 관리되는 도메인에 조인된 컴퓨터의 컴퓨터 계정은 **AADDC 컴퓨터** 조직 구성 단위에 저장됩니다. 이 조직 구성 단의 검색 경로는 ```OU=AADDC Computers,DC=CONTOSO100,DC=COM```과 같습니다.
>
>

자세한 정보 - [LDAP query basics](https://technet.microsoft.com/library/aa996205.aspx)(LDAP 쿼리 기본 사항)


## <a name="task-6-lock-down-secure-ldap-access-to-your-managed-domain-over-the-internet"></a>작업 6: 인터넷을 통해 관리되는 도메인에 대한 보안 LDAP 액세스 잠금
> [!NOTE]
> 인터넷을 통해 관리되는 도메인에 대한 LDAPS 액세스를 사용하지 않도록 설정한 경우 이 구성 작업을 건너뜁니다.
>
>

이 태스크를 시작하기 전에 [작업 3](active-directory-ds-admin-guide-configure-secure-ldap-enable-ldaps.md)에 나와 있는 단계를 완료합니다.

인터넷을 통해 관리되는 도메인에 대한 LDAPS 액세스를 사용하도록 설정하는 경우 보안 위협을 만듭니다. 관리되는 도메인은 보안 LDAP에 사용되는 포트(즉, 포트 636)에서 인터넷을 통해 접속할 수 있습니다. 관리되는 도메인에 대한 액세스를 알려진 특정 IP 주소로 제한할 수 있습니다. NSG(네트워크 보안 그룹)를 만들고 Azure AD Domain Services를 사용하도록 설정된 서브넷과 연결합니다.

다음 테이블에서 샘플 NSG는 인터넷을 통한 보안 LDAP 액세스를 잠급니다. NSG 규칙은 지정된 IP 주소 집합에서만 TCP 포트 636을 통해 인바운드 보안 LDAP 액세스를 허용합니다. 기본 'DenyAll' 규칙은 인터넷의 다른 모든 인바운드 트래픽에 적용됩니다. 지정된 IP 주소에서 인터넷을 통해 LDAPS 액세스를 허용하는 NSG 규칙은 DenyAll NSG 규칙보다 우선 순위가 높습니다.

![인터넷을 통해 LDAPS 액세스를 보안하는 예제 NSG](./media/active-directory-domain-services-admin-guide/secure-ldap-sample-nsg.png)

**자세한 정보** - [네트워크 보안 그룹](../virtual-network/security-overview.md).


## <a name="related-content"></a>관련 콘텐츠
* [Azure AD Domain Services - 시작 가이드](create-instance.md)
* [Azure AD Domain Services 도메인을 관리 합니다.](manage-domain.md)
* [LDAP query basics](https://technet.microsoft.com/library/aa996205.aspx)(LDAP 쿼리 기본 사항)
* [Azure AD Domain Services에 대 한 그룹 정책 관리](manage-group-policy.md)
* [네트워크 보안 그룹](../virtual-network/security-overview.md)
* [네트워크 보안 그룹 만들기](../virtual-network/tutorial-filter-network-traffic.md)


## <a name="next-step"></a>다음 단계
[관리되는 도메인에서 보안 LDAP 문제 해결](tshoot-ldaps.md)
