---
title: Azure AD Domain Services에서 로그인 문제 해결 | Microsoft Docs
description: Azure Active Directory Domain Services에서 일반적인 사용자 로그인 문제 및 오류를 해결하는 방법을 알아봅니다.
services: active-directory-ds
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 07/06/2020
ms.author: justinha
ms.openlocfilehash: 85b261b8754f35c7705690d15671144b858c0a43
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96618572"
---
# <a name="troubleshoot-account-sign-in-problems-with-an-azure-active-directory-domain-services-managed-domain"></a>Azure Active Directory Domain Services 관리되는 도메인의 계정 로그인 문제 해결

Azure AD DS(Azure Active Directory Domain Services) 관리되는 도메인에 로그인할 수 없는 사용자 계정의 가장 일반적인 이유로는 다음 시나리오가 포함됩니다.

* [계정이 아직 Azure AD DS로 동기화되지 않았습니다.](#account-isnt-synchronized-into-azure-ad-ds-yet)
* [Azure AD DS에 계정이 로그인할 수 있는 암호 해시가 없습니다.](#azure-ad-ds-doesnt-have-the-password-hashes)
* [계정이 잠겼습니다.](#the-account-is-locked-out)

> [!TIP]
> Azure AD DS는 Azure AD 테넌트 외부에 있는 계정에 대한 자격 증명에서 동기화될 수 없습니다. 외부 사용자는 Azure AD DS 관리되는 도메인에 로그인할 수 없습니다.

## <a name="account-isnt-synchronized-into-azure-ad-ds-yet"></a>계정이 아직 Azure AD DS로 동기화되지 않음

디렉터리의 크기에 따라 사용자 계정 및 해시 자격 증명이 관리되는 도메인에서 사용 가능하도록 하는 데 시간이 걸릴 수 있습니다. 대규모 디렉터리의 경우 Azure AD에서의 이 초기 단방향 동기화는 몇 시간이 걸릴 수 있으며, 최대 하루 또는 이틀까지 걸릴 수 있습니다. 충분히 오래 기다렸다가 인증을 다시 시도해야 합니다.

사용자 Azure AD Connect가 온-프레미스 디렉터리 데이터를 Azure AD로 동기화하는 하이브리드 환경의 경우 최신 버전의 Azure AD Connect를 실행하고 [Azure AD DS를 사용하도록 설정한 후 전체 동기화를 수행하도록 Azure AD Connect를 구성][azure-ad-connect-phs]했는지 확인합니다. Azure AD DS을 사용하지 않도록 설정한 다음 다시 사용하도록 설정하는 경우 이러한 단계를 다시 수행해야 합니다.

Azure AD Connect를 통해 동기화되지 않는 계정에 계속 문제가 발생하는 경우 Azure AD Sync 서비스를 다시 시작합니다. Azure AD Connect가 설치된 컴퓨터에서 명령 프롬프트 창을 열고 다음 명령을 실행합니다.

```console
net stop 'Microsoft Azure AD Sync'
net start 'Microsoft Azure AD Sync'
```

## <a name="azure-ad-ds-doesnt-have-the-password-hashes"></a>Azure AD DS에 암호 해시 없음

Azure AD DS를 테넌트에서 사용하도록 설정할 때까지 Azure AD는 NTLM 또는 Kerberos 인증에 필요한 형식의 암호 해시를 생성하거나 저장하지 않습니다. 또한 보안상의 이유로 Azure AD는 암호 자격 증명을 일반 텍스트 형식으로 저장하지 않습니다. 따라서 Azure AD는 사용자의 기존 자격 증명에 따라 이러한 NTLM 또는 Kerberos 암호 해시를 자동으로 생성할 수 없습니다.

### <a name="hybrid-environments-with-on-premises-synchronization"></a>온-프레미스 동기화를 사용하는 하이브리드 환경

Azure AD Connect를 사용하여 온-프레미스 AD DS 환경에서 동기화하는 하이브리드 환경의 경우 필요한 NTLM 또는 Kerberos 암호 해시를 로컬에서 생성하고 Azure AD로 동기화할 수 있습니다. 관리되는 도메인을 만든 후 [Azure Active Directory Domain Services에 대한 암호 해시 동기화를 사용하도록 설정][azure-ad-connect-phs]합니다. 이 암호 해시 동기화 단계를 완료하지 않으면 관리되는 도메인을 사용하여 계정에 로그인할 수 없습니다. Azure AD DS을 사용하지 않도록 설정한 다음 다시 사용하도록 설정하는 경우 이러한 단계를 다시 수행해야 합니다.

자세한 내용은 [Azure AD DS에 대한 암호 해시 동기화 작동 방식][phs-process]을 참조하세요.

### <a name="cloud-only-environments-with-no-on-premises-synchronization"></a>온-프레미스 동기화가 없는 클라우드 전용 환경

온-프레미스 동기화가 없는 관리되는 도메인, Azure AD의 계정만 필요한 NTLM 또는 Kerberos 암호 해시를 생성해야 합니다. 클라우드 전용 계정이 로그인할 수 없는 경우 Azure AD DS를 사용하도록 설정한 후 계정에 대한 암호 변경 프로세스가 성공적으로 완료되었습니까?

* **아니요, 암호가 변경되지 않았습니다.**
    * [계정에 대한 암호를 변경][enable-user-accounts]하여 필요한 암호 해시를 생성한 다음, 15분 동안 기다린 후 다시 로그인을 시도합니다.
    * Azure AD DS을 사용하지 않도록 설정한 다음 다시 사용하도록 설정하면 각 계정에서 다시 단계에 따라 암호를 변경하고 필요한 암호 해시를 생성해야 합니다.
* **예, 암호가 변경되었습니다.**
    * `AADDSCONTOSO\deeriley`와 같은 *SAMAccountName* 형식 대신 `driley@aaddscontoso.com`과 같은 *UPN* 형식을 사용하여 로그인을 시도합니다.
    * UPN 접두사가 너무 길거나 관리되는 도메인의 다른 사용자와 UPN 접두사가 동일한 사용자에 대해 *SAMAccountName* 이 자동으로 생성될 수 있습니다. *UPN* 형식은 Azure AD 테넌트 내에서 고유하도록 보장됩니다.

## <a name="the-account-is-locked-out"></a>계정이 잠김

관리되는 도메인의 사용자 계정은 실패한 로그인 시도에 대해 정의된 임계점이 충족되었을 때 잠깁니다. 이 계정 잠금 동작은 자동화된 디지털 공격일 가능성이 있는 반복적인 무차별 로그인 시도로부터 사용자를 보호하도록 설계되었습니다.

기본적으로 2분 이내에 5번의 잘못된 암호 시도가 있는 경우 계정은 30분 동안 잠깁니다.

자세한 내용과 계정 잠금 문제를 해결하는 방법은 [Azure AD DS의 계정 잠금 문제 해결][troubleshoot-account-lockout]을 참조하세요.

## <a name="next-steps"></a>다음 단계

VM을 관리되는 도메인에 가입시키는 데 문제가 있는 경우 [도움말을 찾고 Azure Active Directory에 대한 지원 티켓을 엽니다][azure-ad-support].

<!-- INTERNAL LINKS -->
[troubleshoot-account-lockout]: troubleshoot-account-lockout.md
[azure-ad-connect-phs]: ./tutorial-configure-password-hash-sync.md
[enable-user-accounts]:  tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds
[phs-process]: ../active-directory/hybrid/how-to-connect-password-hash-synchronization.md#password-hash-sync-process-for-azure-ad-domain-services
[azure-ad-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md