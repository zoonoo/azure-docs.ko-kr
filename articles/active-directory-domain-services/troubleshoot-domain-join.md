---
title: Azure AD 도메인 서비스로 도메인 가입 문제 해결 | 마이크로 소프트 문서
description: VM을 도메인에 가입하거나 응용 프로그램을 Azure Active Directory 도메인 서비스에 연결하려고 할 때 일반적인 문제를 해결하는 방법과 관리되는 도메인에 연결하거나 인증할 수 없습니다.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 10/02/2019
ms.author: iainfou
ms.openlocfilehash: f187dba4eace61695a72e4b7b08731e65ff0d7f9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78299111"
---
# <a name="troubleshoot-domain-join-problems-with-an-azure-ad-domain-services-managed-domain"></a>Azure AD 도메인 서비스 관리 도메인에서 도메인 가입 문제 해결

가상 시스템(VM)에 가입하거나 응용 프로그램을 AD DS(관리 되는 도메인)에 연결하려고 하면 연결할 수 없는 오류가 발생할 수 있습니다. 도메인 가입 문제를 해결하려면 다음 사항 중 문제가 있는 사항을 검토하십시오.

* 인증 프롬프트를 받지 못하면 VM 또는 응용 프로그램이 Azure AD DS 관리 도메인에 연결할 수 없습니다.
    * [도메인 가입에 대한 연결 문제](#connectivity-issues-for-domain-join)해결을 시작합니다.
* 인증 중에 오류가 발생하면 Azure AD DS 관리 도메인에 대한 연결이 성공합니다.
    * 도메인 가입 [중에 자격 증명 관련 문제를](#credentials-related-issues-during-domain-join)해결하기 시작합니다.

## <a name="connectivity-issues-for-domain-join"></a>도메인 가입을 위한 연결 문제

VM에서 Azure AD DS 관리 도메인을 찾을 수 없는 경우 일반적으로 네트워크 연결 또는 구성 문제가 있습니다. 다음 문제 해결 단계를 검토하여 문제를 찾고 해결합니다.

1. VM이 Azure AD DS에 대해 활성화된 동일한 피어있는 가상 네트워크에 연결되어 있는지 확인합니다. 그렇지 않은 경우 VM은 가입하기 위해 도메인을 찾고 연결할 수 없습니다.
    * VM이 동일한 가상 네트워크에 연결되어 있지 않은 경우 가상 네트워킹 피어링 또는 VPN 연결이 *활성* 또는 *연결되어* 트래픽이 올바르게 흐르도록 하는지 확인합니다.
1. 와 같은 `ping aaddscontoso.com`Azure AD DS 관리 도메인의 도메인 이름을 사용하여 도메인을 ping해 보십시오.
    * ping 응답이 실패하면 Azure AD DS 관리 도메인의 경우 포털의 개요 페이지에 표시되는 도메인의 IP `ping 10.0.0.4`주소를 ping해 보십시오.
    * 도메인이 아닌 IP 주소를 ping할 수 있는 경우 DNS가 잘못 구성될 수 있습니다. 가상 네트워크에 대해 Azure AD DS 관리 도메인 DNS 서버를 구성했는지 확인합니다.
1. 와 같은 `ipconfig /flushdns`가상 컴퓨터에서 DNS 확인자 캐시를 플러시해 보십시오.

### <a name="network-security-group-nsg-configuration"></a>NSG(네트워크 보안 그룹) 구성

Azure AD DS 관리 도메인을 만들 때 성공적인 도메인 작업에 대 한 적절 한 규칙으로 네트워크 보안 그룹도 만들어집니다. 추가 네트워크 보안 그룹 규칙을 편집하거나 만드는 경우 Azure AD DS가 연결 및 인증 서비스를 제공하는 데 필요한 포트를 실수로 차단할 수 있습니다. 이러한 네트워크 보안 그룹 규칙은 암호 동기화가 완료되지 않거나 사용자가 로그인할 수 없거나 도메인 가입 문제와 같은 문제를 일으킬 수 있습니다.

연결 문제가 계속 발생하면 다음 문제 해결 단계를 검토하십시오.

1. Azure 포털에서 Azure AD DS 관리 도메인의 상태를 확인합니다. *AADDS001에*대한 경고가 있는 경우 네트워크 보안 그룹 규칙이 액세스를 차단하고 있습니다.
1. 필요한 [포트 및 네트워크 보안 그룹 규칙을][network-ports]검토합니다. 이러한 네트워크 포트를 차단하여 연결하는 VM 또는 가상 네트워크에 적용되는 네트워크 보안 그룹 규칙이 없는지 확인합니다.
1. 네트워크 보안 그룹 구성 문제가 해결되면 *AADDS001* 경고는 약 2시간 후에 상태 페이지에서 사라집니다. 이제 네트워크 연결을 사용할 수 있게 되면 VM에 다시 도메인 가입을 시도하십시오.

## <a name="credentials-related-issues-during-domain-join"></a>도메인 가입 중 자격 증명 관련 문제

Azure AD DS 관리 도메인에 가입하도록 자격 증명을 요청하는 대화 상자가 있으면 VM은 Azure 가상 네트워크를 사용하여 도메인에 연결할 수 있습니다. 도메인 조인 프로세스는 도메인 인증 또는 권한 부여를 통해 제공하는 자격 증명을 사용하여 도메인 가입 프로세스를 완료하지 못합니다.

자격 증명 관련 문제를 해결하려면 다음 문제 해결 단계를 검토하십시오.

1. UPN 형식을 사용하여 자격 증명(예: `dee@aaddscontoso.onmicrosoft.com`)을 지정합니다. 이 UPN이 Azure AD에서 올바르게 구성되었는지 확인합니다.
    * 테넌트에 동일한 UPN 접두사를 가진 사용자가 여러 명이 있거나 UPN 접두사가 지나치게 긴 경우 계정에 대한 *SAMAccountName이* 자동으로 생성될 수 있습니다. 따라서 계정의 *SAMAccountName* 형식은 온-프레미스 도메인에서 예상하거나 사용하는 것과 다를 수 있습니다.
1. Azure AD DS 관리 도메인의 일부인 사용자 계정에 대한 자격 증명을 사용하여 VM을 관리되는 도메인에 조인합니다.
1. [암호 동기화를 사용하도록 설정하고][enable-password-sync] 초기 암호 동기화가 완료될 때까지 충분히 기다렸는지 확인합니다.

## <a name="next-steps"></a>다음 단계

도메인 조인 작업의 일부로 Active Directory 프로세스에 대한 자세한 내용은 [Join 및 인증 문제를][join-authentication-issues]참조하십시오.

VM을 Azure AD DS 관리 도메인에 가입하는 데 여전히 문제가 있는 경우 [도움말을 찾아 Azure Active Directory 에 대한 지원 티켓을 엽니다.][azure-ad-support]

<!-- INTERNAL LINKS -->
[enable-password-sync]: tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds
[network-ports]: network-considerations.md#network-security-groups-and-required-ports
[azure-ad-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md

<!-- EXTERNAL LINKS -->
[join-authentication-issues]: /previous-versions/windows/it-pro/windows-2000-server/cc961817(v=technet.10)
