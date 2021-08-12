---
title: Azure AD Domain Services에서 보안 LDAP 문제 해결 | Microsoft Docs
description: Azure Active Directory Domain Services 관리형 도메인에 대한 LDAPS(보안 LDAP) 문제를 해결하는 방법을 알아봅니다.
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: 445c60da-e115-447b-841d-96739975bdf6
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 07/09/2020
ms.author: justinha
ms.openlocfilehash: e21ec7d8fdf7a5c7a8ba7a9f22aa5709581cbae6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96618402"
---
# <a name="troubleshoot-secure-ldap-connectivity-issues-to-an-azure-active-directory-domain-services-managed-domain"></a>Azure Active Directory Domain Services 관리형 도메인에 대한 보안 LDAP 연결 문제 해결

LDAP(Lightweight Directory Access Protocol)를 사용하여 Azure AD DS(Azure Active Directory Domain Services)와 통신하는 애플리케이션 및 서비스는 [보안 LDAP를 사용하도록 구성](tutorial-configure-ldaps.md)할 수 있습니다. 보안 LDAP가 제대로 작동하려면 적절한 인증서 및 필수 네트워크 포트를 열어야 합니다.

이 문서는 Azure AD DS의 보안 LDAP 액세스와 관련된 문제를 해결하는 데 도움이 됩니다.

## <a name="common-connection-issues"></a>일반적인 연결 문제

보안 LDAP를 사용하여 Azure AD DS 관리형 도메인에 연결하는 데 문제가 있으면 다음 문제 해결 단계를 검토하세요. 각 문제 해결 단계 후에 관리형 도메인에 다시 연결해 봅니다.

* 보안 LDAP 인증서의 발급자 체인을 클라이언트에서 신뢰할 수 있어야 합니다. 트러스트를 설정하려면 클라이언트의 신뢰할 수 있는 루트 인증서 저장소에 루트 CA(인증 기관)를 추가할 수 있습니다.
    * [인증서를 내보내고 클라이언트 컴퓨터에 적용][client-cert]해야 합니다.
* 관리형 도메인의 보안 LDAP 인증서의 *주체* 또는 *주체 대체 이름* 특성에 DNS 이름이 있는지 확인합니다.
    * [보안 LDAP 인증서 요구 사항][certs-prereqs]을 검토하고 필요한 경우 대체 인증서를 만듭니다.
* LDAP 클라이언트(예: *ldp.exe*)가 IP 주소가 아닌 DNS 이름을 사용하여 보안 LDAP 엔드포인트에 연결되는지 확인합니다.
    * 관리형 도메인에 적용되는 인증서에는 서비스의 IP 주소는 포함되지 않으며 DNS 이름만 포함됩니다.
* LDAP 클라이언트가 연결할 DNS 이름을 확인합니다. 관리되는 도메인에서 보안 LDAP에 대한 공용 IP 주소를 확인해야 합니다.
    * DNS 이름이 내부 IP 주소로 확인되는 경우 외부 IP 주소로 확인할 DNS 레코드를 업데이트합니다.
* 외부 연결의 경우 네트워크 보안 그룹은 인터넷에서 TCP 포트 636로의 트래픽을 허용하는 규칙을 포함해야 합니다.
    * 외부 연결이 아닌 가상 네트워크에 직접 연결되어 있는 리소스에서 보안 LDAP를 사용하여 관리형 도메인에 연결할 수 있는 경우 [보안 LDAP 트래픽을 허용하는 네트워크 보안 그룹 규칙을 만들어야][ldaps-nsg] 합니다.

## <a name="next-steps"></a>다음 단계

문제가 여전히 발생하는 경우 추가 문제 해결 지원을 위해 [Azure 지원 요청을 엽니다][azure-support].

<!-- INTERNAL LINKS -->
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
[configure-ldaps]: tutorial-configure-ldaps.md
[certs-prereqs]: tutorial-configure-ldaps.md#create-a-certificate-for-secure-ldap
[client-cert]: tutorial-configure-ldaps.md#export-a-certificate-for-client-computers
[ldaps-nsg]: tutorial-configure-ldaps.md#lock-down-secure-ldap-access-over-the-internet
