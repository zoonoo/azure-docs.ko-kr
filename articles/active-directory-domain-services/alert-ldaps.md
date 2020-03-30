---
title: Azure AD 도메인 서비스에서 안전한 LDAP 경고 해결 | 마이크로 소프트 문서
description: Azure Active Directory 도메인 서비스에 대한 보안 LDAP를 사용하여 일반적인 경고를 해결하고 해결하는 방법을 알아봅니다.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 81208c0b-8d41-4f65-be15-42119b1b5957
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 09/18/2019
ms.author: iainfou
ms.openlocfilehash: 06b0fa1979f18981ec5cf78dc9a9dbad8b196394
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "71258041"
---
# <a name="known-issues-secure-ldap-alerts-in-azure-active-directory-domain-services"></a>알려진 문제: Azure Active Directory 도메인 서비스에서 LDAP 경고 보안

LDAP(라이트급 디렉터리 액세스 프로토콜)를 사용하여 Azure Active Directory 도메인 서비스(Azure AD DS)와 통신하는 응용 프로그램 및 서비스는 [보안 LDAP를 사용하도록 구성할](tutorial-configure-ldaps.md)수 있습니다. 안전한 LDAP가 올바르게 작동하려면 적절한 인증서와 필요한 네트워크 포트를 열어야 합니다.

이 문서는 Azure AD DS에서 안전한 LDAP 액세스를 통해 일반적인 경고를 이해하고 해결하는 데 도움이 됩니다.

## <a name="aadds101-secure-ldap-network-configuration"></a>AADDS101: 안전한 LDAP 네트워크 구성

### <a name="alert-message"></a>경고 메시지

*인터넷을 통해 안전한 LDAP는 관리되는 도메인에 대해 활성화됩니다. 그러나 포트 636에 대한 액세스는 네트워크 보안 그룹을 사용하여 잠기지 않습니다. 관리되는 도메인의 사용자 계정이 암호 무차별 암호 대입 공격에 노출될 수 있습니다.*

### <a name="resolution"></a>해결 방법

보안 LDAP를 사용하도록 설정하는 경우 인바운드 LDAPS 액세스를 특정 IP 주소로 제한하는 추가 규칙을 만드는 것이 좋습니다. 이러한 규칙은 Azure AD DS 관리 도메인을 무차별 암호 대입 공격으로부터 보호합니다. 네트워크 보안 그룹을 업데이트하여 보안 LDAP에 대한 TCP 포트 636 액세스를 제한하려면 다음 단계를 완료하십시오.

1. Azure 포털에서 네트워크 보안 **그룹을**검색하고 선택합니다.
1. *AADDS-contoso.com-NSG와*같이 관리되는 도메인과 연결된 네트워크 보안 그룹을 선택한 다음 **인바운드 보안 규칙을** 선택합니다.
1. **+** TCP 포트 636에 대한 규칙을 추가합니다. 필요한 경우 창에서 **고급을** 선택하여 규칙을 만듭니다.
1. **소스의**경우 드롭다운 메뉴에서 *IP 주소를 선택합니다.* 안전한 LDAP 트래픽에 대한 액세스 권한을 부여하려는 원본 IP 주소를 입력합니다.
1. **목적지로** *Any를* 선택한 다음 대상 포트 범위에 대해 *636을* **입력합니다.**
1. **프로토콜을** *TCP로* 설정하고 허용하기 **위한 작업을** *설정합니다.*
1. 규칙의 우선 순위를 지정한 다음 *제한LDAPS*와 같은 이름을 입력합니다.
1. 준비되면 **추가를** 선택하여 규칙을 만듭니다.

Azure AD DS 관리 도메인의 상태는 2시간 이내에 자동으로 업데이트되고 경고를 제거합니다.

> [!TIP]
> TCP 포트 636은 Azure AD DS가 원활하게 실행되는 데 필요한 유일한 규칙은 아닙니다. 자세한 내용은 Azure [AD DS 네트워크 보안 그룹 및 필수 포트를](network-considerations.md#network-security-groups-and-required-ports)참조하십시오.

## <a name="aadds502-secure-ldap-certificate-expiring"></a>AADDS502: 보안 LDAP 인증서 만료

### <a name="alert-message"></a>경고 메시지

*관리되는 도메인에 대한 보안 LDAP 인증서는 [date]]에 만료됩니다.*

### <a name="resolution"></a>해결 방법

보안 LDAP에 대한 인증서를 만드는 단계에 따라 대체 보안 [LDAP 인증서를 만듭니다.](tutorial-configure-ldaps.md#create-a-certificate-for-secure-ldap) 대체 인증서를 Azure AD DS에 적용하고 보안 LDAP를 사용하여 연결하는 모든 클라이언트에 인증서를 배포합니다.

## <a name="next-steps"></a>다음 단계

그래도 문제가 있는 경우 추가 문제 해결 지원을 위해 [Azure 지원 요청을 엽니다.][azure-support]

<!-- INTERNAL LINKS -->
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
