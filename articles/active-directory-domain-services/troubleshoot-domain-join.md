---
title: Azure AD Domain Services에서 도메인 조인 문제 해결 | Microsoft Docs
description: VM을 도메인 조인하거나 애플리케이션을 Azure Active Directory Domain Services에 연결하려고 할 때 일반적인 문제 및 관리되는 도메인에 연결하거나 인증할 수 없는 문제를 해결하는 방법에 대해 알아봅니다.
services: active-directory-ds
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 07/06/2020
ms.author: justinha
ms.openlocfilehash: 86d6ca79a12e4706f558e92c3c83c5bddaa99b3c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96618606"
---
# <a name="troubleshoot-domain-join-problems-with-an-azure-active-directory-domain-services-managed-domain"></a>Azure Active Directory Domain Services 관리되는 도메인에서 도메인 조인 문제 해결

VM(가상 머신)에 조인하거나 애플리케이션을 Azure AD DS(Azure Active Directory Domain Services) 관리되는 도메인에 연결하려고 할 때 오류가 발생할 수 있습니다. 도메인 조인 문제를 해결하려면 다음 중 문제가 발생한 지점을 검토합니다.

* 인증 프롬프트가 수신되지 않으면 VM 또는 애플리케이션이 Azure AD DS 관리되는 도메인에 연결할 수 없습니다.
    * [도메인 조인에 대한 연결 문제](#connectivity-issues-for-domain-join) 해결을 시작합니다.
* 인증하는 동안 오류가 발생하는 경우 관리되는 도메인에 대한 연결에 성공합니다.
    * [도메인 조인 중에 자격 증명 관련 문제](#credentials-related-issues-during-domain-join) 해결을 시작합니다.

## <a name="connectivity-issues-for-domain-join"></a>도메인 조인에 대한 연결 문제

VM이 관리되는 도메인을 찾을 수 없는 경우 일반적으로 네트워크 연결 또는 구성 문제가 있습니다. 다음 문제 해결 단계를 검토하여 문제를 찾아 해결합니다.

1. VM이 관리되는 도메인과 동일한 또는 피어링된 가상 네트워크에 연결되어 있는지 확인합니다. 그렇지 않으면 VM에서 조인하기 위해 도메인을 찾아서 연결할 수 없습니다.
    * VM이 동일한 가상 네트워크에 연결되어 있지 않은 경우에는 트래픽이 올바르게 흐를 수 있도록 가상 네트워크 피어링 또는 VPN 연결이 *활성* 또는 *연결됨* 상태인지 확인합니다.
1. 관리되는 도메인의 도메인 이름(예: `ping aaddscontoso.com`)을 사용하여 도메인에 ping을 시도합니다.
    * ping 응답에 실패하는 경우, `ping 10.0.0.4`와 같이 관리되는 도메인에 대한 포털의 개요 페이지에 표시되는 도메인의 IP 주소에 ping을 시도합니다.
    * IP 주소는 ping할 수 있지만 도메인은 ping할 수 없는 경우 DNS가 잘못 구성되었을 수 있습니다. [가상 네트워크에 대해 관리되는 도메인 DNS 서버를 구성][configure-dns]했는지 확인합니다.
1. `ipconfig /flushdns`와 같이 가상 머신에 DNS 확인자 캐시 플러싱을 시도합니다.

### <a name="network-security-group-nsg-configuration"></a>NSG(네트워크 보안 그룹) 구성

관리되는 도메인을 만들 때 성공적인 도메인 작업에 적절한 규칙을 사용하여 네트워크 보안 그룹도 만들어집니다. 추가적인 네트워크 보안 그룹 규칙을 편집하거나 만드는 경우 Azure AD DS에 필요한 포트를 블록하여 연결 및 인증 서비스를 제공할 수 있습니다. 이러한 네트워크 보안 그룹 규칙은 암호 동기화가 완료되지 않는 문제, 사용자가 로그인을 할 수 없는 문제 또는 도메인 조인 문제 등의 원인이 될 수 있습니다.

연결 문제가 계속되면 다음 문제 해결 단계를 검토합니다.

1. Azure Portal에서 관리되는 도메인의 상태 페이지를 확인합니다. *AADDS001* 에 대한 경고를 받은 경우 네트워크 보안 그룹 규칙은 액세스를 차단합니다.
1. [필요한 포트 및 네트워크 보안 그룹 규칙][network-ports]을 검토합니다. 연결 중인 VM 또는 가상 네트워크에 적용되는 네트워크 보안 그룹 규칙이 이러한 네트워크 포트를 차단하지 않는지 확인합니다.
1. 네트워크 보안 그룹 구성 문제가 해결되면 약 2시간 내에 상태 페이지에서 *AADDS001* 경고가 사라집니다. 이제 네트워크 연결을 사용할 수 있습니다. VM에 도메인 조인을 다시 시도합니다.

## <a name="credentials-related-issues-during-domain-join"></a>도메인 조인 중 자격 증명 관련 문제

관리되는 도메인에 조인하기 위한 자격 증명을 요청하는 대화 상자가 표시되면 VM은 Azure 가상 네트워크를 사용하여 도메인에 연결할 수 있습니다. 도메인 조인 프로세스가 자격 증명을 사용하여 도메인 조인 프로세스를 완료하기 위해 도메인 또는 권한 부여에 인증할 때 실패합니다.

자격 증명 관련 문제를 해결하려면 다음 문제 해결 단계를 검토합니다.

1. UPN 형식을 사용하여 자격 증명(예: `dee@contoso.onmicrosoft.com`)을 지정합니다. 이 UPN이 Azure AD에서 올바르게 구성되어 있는지 확인합니다.
    * 테넌트에서 동일한 UPN 접두사를 사용하는 사용자가 여럿이거나 UPN 접두사가 지나치게 긴 경우 사용자 계정에 대한 *SAMAccountName* 이 자동으로 생성될 수 있습니다. 따라서 사용자 계정에 대한 *SAMAccountName* 형식이 온-프레미스 도메인에서 예상하거나 사용한 것과 다를 수 있습니다.
1. 관리되는 도메인에 속한 사용자 계정의 자격 증명을 사용하여 VM을 관리되는 도메인에 조인해 보세요.
1. [암호 동기화를 사용하도록 설정][enable-password-sync]하고 초기 암호 동기화가 완료될 때까지 충분히 대기해야 합니다.

## <a name="next-steps"></a>다음 단계

도메인 조인 작업의 일부로 Active Directory 프로세스를 보다 자세히 이해하려면 [조인 및 인증 문제][join-authentication-issues]를 참조하세요.

VM을 관리되는 도메인에 조인하는 데 문제가 있는 경우 [도움말을 찾고 Azure Active Directory에 대한 지원 티켓을 엽니다][azure-ad-support].

<!-- INTERNAL LINKS -->
[enable-password-sync]: tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds
[network-ports]: network-considerations.md#network-security-groups-and-required-ports
[azure-ad-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
[configure-dns]: tutorial-create-instance.md#update-dns-settings-for-the-azure-virtual-network

<!-- EXTERNAL LINKS -->
[join-authentication-issues]: /previous-versions/windows/it-pro/windows-2000-server/cc961817(v=technet.10)
