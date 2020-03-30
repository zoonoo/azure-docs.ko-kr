---
title: Azure AD 도메인 서비스에서 안전한 LDAP 문제 해결 | 마이크로 소프트 문서
description: Azure Active Directory 도메인 관리 도메인에 대한 LDAP(보안 LDAPS) 문제를 해결하는 방법 알아보기
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 445c60da-e115-447b-841d-96739975bdf6
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 02/10/2020
ms.author: iainfou
ms.openlocfilehash: 22d1b6e2344256b52cfdbc48720a680a770a4216
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77132160"
---
# <a name="troubleshoot-secure-ldap-connectivity-issues-to-an-azure-active-directory-domain-services-managed-domain"></a>Azure Active Directory 도메인 관리 도메인에 대한 안전한 LDAP 연결 문제 해결

LDAP(라이트급 디렉터리 액세스 프로토콜)를 사용하여 Azure Active Directory 도메인 서비스(Azure AD DS)와 통신하는 응용 프로그램 및 서비스는 [보안 LDAP를 사용하도록 구성할](tutorial-configure-ldaps.md)수 있습니다. 안전한 LDAP가 올바르게 작동하려면 적절한 인증서와 필요한 네트워크 포트를 열어야 합니다.

이 문서에서는 Azure AD DS에서 안전한 LDAP 액세스 문제를 해결하는 데 도움이 됩니다.

## <a name="common-connection-issues"></a>일반적인 연결 문제

보안 LDAP를 사용하여 Azure AD DS 관리 도메인에 연결하는 데 문제가 있는 경우 다음 문제 해결 단계를 검토합니다. 각 문제 해결 단계 후 Azure AD DS 관리 도메인에 다시 연결해 보십시오.

* 보안 LDAP 인증서의 발급자 체인을 클라이언트에서 신뢰할 수 있어야 합니다. 클라이언트의 신뢰할 수 있는 루트 인증서 저장소에 루트 인증 기관(CA)을 추가하여 트러스트를 설정할 수 있습니다.
    * [클라이언트 컴퓨터에 인증서를 내보내고 적용해야][client-cert]합니다.
* 관리 되는 도메인에 대 한 보안 LDAP 인증서 *제목* 또는 *주체 대체 이름* 특성에 DNS 이름이 있는지 확인 합니다.
    * 안전한 [LDAP 인증서 요구 사항을][certs-prereqs] 검토하고 필요한 경우 대체 인증서를 만듭니다.
* *ldp.exe와* 같은 LDAP 클라이언트가 IP 주소가 아닌 DNS 이름을 사용하여 안전한 LDAP 끝점에 연결되는지 확인합니다.
    * Azure AD DS 관리 도메인에 적용되는 인증서에는 서비스의 IP 주소가 포함되지 않으며 DNS 이름만 포함됩니다.
* LDAP 클라이언트가 연결할 DNS 이름을 확인합니다. Azure AD DS 관리 도메인에서 안전한 LDAP를 위해 공용 IP 주소로 해결해야 합니다.
    * DNS 이름이 내부 IP 주소로 확인되면 DNS 레코드를 업데이트하여 외부 IP 주소로 확인합니다.
* 외부 연결의 경우 네트워크 보안 그룹에는 인터넷에서 TCP 포트 636으로의 트래픽을 허용하는 규칙이 포함되어야 합니다.
    * 외부 연결이 아닌 가상 네트워크에 직접 연결되지만 외부 연결이 아닌 리소스에서 안전한 LDAP를 사용하여 Azure AD DS 관리 도메인에 연결할 수 있는 경우 [안전한 LDAP 트래픽을 허용하는 네트워크 보안 그룹 규칙을 만들어야][ldaps-nsg]합니다.

## <a name="next-steps"></a>다음 단계

그래도 문제가 있는 경우 추가 문제 해결 지원을 위해 [Azure 지원 요청을 엽니다.][azure-support]

<!-- INTERNAL LINKS -->
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
[configure-ldaps]: tutorial-configure-ldaps.md
[certs-prereqs]: tutorial-configure-ldaps.md#create-a-certificate-for-secure-ldap
[client-cert]: tutorial-configure-ldaps.md#export-a-certificate-for-client-computers
[ldaps-nsg]: tutorial-configure-ldaps.md#lock-down-secure-ldap-access-over-the-internet
