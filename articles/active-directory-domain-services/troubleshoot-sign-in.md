---
title: Azure AD 도메인 서비스의 로그인 문제 해결 | 마이크로 소프트 문서
description: Azure Active Directory 도메인 서비스에서 일반적인 사용자 로그인 문제 및 오류 문제를 해결하는 방법을 알아봅니다.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 10/02/2019
ms.author: iainfou
ms.openlocfilehash: 0585ced3bc53f216ab203b4686b5800b5e14bbbd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77612738"
---
# <a name="troubleshoot-account-sign-in-problems-with-an-azure-ad-domain-services-managed-domain"></a>Azure AD 도메인 서비스 관리 도메인의 계정 로그인 문제 해결

Azure AD DS 관리 도메인에 로그인할 수 없는 사용자 계정의 가장 일반적인 이유는 다음과 같습니다.

* [계정이 아직 Azure AD DS에 동기화되지 않았습니다.](#account-isnt-synchronized-into-azure-ad-ds-yet)
* [Azure AD DS에는 계정이 로그인할 수 있도록 하는 암호 해시가 없습니다.](#azure-ad-ds-doesnt-have-the-password-hashes)
* [계정이 잠겼습니다.](#the-account-is-locked-out)

> [!TIP]
> Azure AD DS는 Azure AD 테넌트 외부에 있는 계정에 대한 자격 증명에서 동기화할 수 없습니다. 외부 사용자는 Azure AD DS 관리 도메인에 로그인할 수 없습니다.

## <a name="account-isnt-synchronized-into-azure-ad-ds-yet"></a>계정이 아직 Azure AD DS에 동기화되지 않았습니다.

디렉터리 크기에 따라 Azure AD DS에서 사용자 계정 및 자격 증명 해시를 사용할 수 있는 데 시간이 걸릴 수 있습니다. 대규모 디렉터리에서 Azure AD의 이 초기 단방향 동기화는 몇 시간 에서 최대 하루 또는 이틀까지 걸릴 수 있습니다. 인증을 다시 시도하기 전에 충분히 기다려야 합니다.

사용자 Azure AD Connect를 연결하여 온-프레미스 디렉터리 데이터를 Azure AD에 동기화하는 하이브리드 환경의 경우 Azure AD Connect의 최신 버전을 실행하고 [Azure AD DS를 사용하도록 설정한 후 전체 동기화를 수행하도록 Azure AD Connect를 구성했는지][azure-ad-connect-phs]확인합니다. Azure AD DS를 사용하지 않도록 설정한 다음 다시 활성화하는 경우 다음 단계를 다시 수행해야 합니다.

Azure AD Connect를 통해 동기화되지 않는 계정에 문제가 계속 발생하면 Azure AD 동기화 서비스를 다시 시작합니다. Azure AD Connect가 설치된 컴퓨터에서 명령 프롬프트 창을 열고 다음 명령을 실행합니다.

```console
net stop 'Microsoft Azure AD Sync'
net start 'Microsoft Azure AD Sync'
```

## <a name="azure-ad-ds-doesnt-have-the-password-hashes"></a>Azure AD DS에 암호 해시가 없습니다.

Azure AD DS를 테넌트에서 사용하도록 설정할 때까지 Azure AD는 NTLM 또는 Kerberos 인증에 필요한 형식의 암호 해시를 생성하거나 저장하지 않습니다. 또한 보안상의 이유로 Azure AD는 암호 자격 증명을 일반 텍스트 형식으로 저장하지 않습니다. 따라서 Azure AD는 사용자의 기존 자격 증명에 따라 이러한 NTLM 또는 Kerberos 암호 해시를 자동으로 생성할 수 없습니다.

### <a name="hybrid-environments-with-on-premises-synchronization"></a>온-프레미스 동기화가 있는 하이브리드 환경

Azure AD Connect를 사용하여 온-프레미스 AD DS 환경에서 동기화하는 하이브리드 환경의 경우 필요한 NTLM 또는 Kerberos 암호 해시를 Azure AD에 로컬로 생성하고 동기화할 수 있습니다. Azure AD DS 관리 도메인을 만든 후 [Azure Active Directory 도메인 서비스에 대한 암호 해시 동기화를 사용하도록 설정합니다.][azure-ad-connect-phs] 이 암호 해시 동기화 단계를 완료하지 않으면 Azure AD DS를 사용하여 계정에 로그인할 수 없습니다. Azure AD DS를 사용하지 않도록 설정한 다음 다시 활성화하는 경우 해당 단계를 다시 수행해야 합니다.

자세한 내용은 [Azure AD DS에 대한 암호 해시 동기화 작동 방식을][phs-process]참조하십시오.

### <a name="cloud-only-environments-with-no-on-premises-synchronization"></a>온-프레미스 동기화가 없는 클라우드 전용 환경

온-프레미스 동기화가 없는 Azure AD DS 관리 도메인, Azure AD의 계정만 필요한 NTLM 또는 Kerberos 암호 해시를 생성해야 합니다. 클라우드 전용 계정에서 로그인할 수 없는 경우 Azure AD DS를 사용하도록 설정한 후 계정에 대한 암호 변경 프로세스가 성공적으로 완료된 경우?

* **아니요, 암호가 변경되지 않았습니다.**
    * [계정에 대한 암호를 변경하여][enable-user-accounts] 필요한 암호 해시를 생성한 다음 다시 로그인하기 전에 15분 동안 기다립니다.
    * Azure AD DS를 사용하지 않도록 설정한 다음 다시 활성화하는 경우 각 계정은 단계를 다시 수행하여 암호를 변경하고 필요한 암호 해시를 생성해야 합니다.
* **예, 암호가 변경되었습니다.**
    * 과 같은 *SAMAccountName* 형식 대신 `driley@aaddscontoso.com`에 의한 *UPN* 형식을 `AADDSCONTOSO\deeriley`사용하여 로그인해 보십시오.
    * *SAMAccountName은* UPN 접두사가 지나치게 길거나 관리되는 도메인의 다른 사용자와 동일한 사용자에 대해 자동으로 생성될 수 있습니다. *UPN* 형식은 Azure AD 테넌트 내에서 고유하게 보장됩니다.

## <a name="the-account-is-locked-out"></a>계정이 잠겨 있습니다.

실패한 로그인 시도에 대한 정의된 임계값이 충족되면 Azure AD DS의 사용자 계정이 잠깁니다. 이 계정 잠금 동작은 자동화된 디지털 공격을 나타낼 수 있는 반복적인 무차별 암호 대입 로그인 시도로부터 사용자를 보호하기 위해 고안되었습니다.

기본적으로 2분 동안 5번의 잘못된 암호 시도가 있는 경우 계정은 30분 동안 잠깁니다.

자세한 정보 및 계정 잠금 문제를 해결하는 방법은 [Azure AD DS의 계정 잠금 문제 해결을][troubleshoot-account-lockout]참조하세요.

## <a name="next-steps"></a>다음 단계

VM을 Azure AD DS 관리 도메인에 가입하는 데 여전히 문제가 있는 경우 [도움말을 찾아 Azure Active Directory 에 대한 지원 티켓을 엽니다.][azure-ad-support]

<!-- INTERNAL LINKS -->
[troubleshoot-account-lockout]: troubleshoot-account-lockout.md
[azure-ad-connect-phs]: active-directory-ds-getting-started-password-sync-synced-tenant.md
[enable-user-accounts]:  tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds
[phs-process]: ../active-directory/hybrid/how-to-connect-password-hash-synchronization.md#password-hash-sync-process-for-azure-ad-domain-services
[azure-ad-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
