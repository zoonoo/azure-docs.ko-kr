---
title: Azure AD Domain Services에 대 한 관리 개념 Microsoft Docs
description: Azure Active Directory Domain Services 관리 되는 도메인을 관리 하는 방법 및 사용자 계정 및 암호의 동작에 대해 알아봅니다.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 10/08/2019
ms.author: iainfou
ms.openlocfilehash: b82927efa9054e71379d01993d1669527bc71402
ms.sourcegitcommit: 961468fa0cfe650dc1bec87e032e648486f67651
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/10/2019
ms.locfileid: "72249426"
---
# <a name="management-concepts-for-user-accounts-passwords-and-administration-in-azure-active-directory-domain-services"></a>Azure Active Directory Domain Services의 사용자 계정, 암호 및 관리에 대 한 관리 개념

Azure Active Directory Domain Services (AD DS) 관리 되는 도메인을 만들고 실행할 때 기존 온-프레미스 AD DS 환경에 비해 동작이 약간 다릅니다. Azure AD DS에서 자체 관리 되는 도메인으로 동일한 관리 도구를 사용할 수 있지만 DC (도메인 컨트롤러)에 직접 액세스할 수는 없습니다. 또한 사용자 계정 생성의 원본에 따라 암호 정책 및 암호 해시에 대 한 동작의 차이가 있습니다.

이 개념 문서에서는 Azure AD DS 관리 되는 도메인을 관리 하는 방법과 사용자 계정이 생성 되는 방식에 따라 사용자 계정의 다른 동작에 대해 자세히 설명 합니다.

## <a name="domain-management"></a>도메인 관리

Azure AD DS에서 사용자, 그룹, 자격 증명 및 정책과 같은 모든 리소스를 포함 하는 Dc (도메인 컨트롤러)는 관리 서비스의 일부입니다. 중복성을 위해 두 Dc가 Azure AD DS 관리 되는 도메인의 일부로 생성 됩니다. 이러한 Dc에 로그인 하 여 관리 작업을 수행할 수는 없습니다. 대신 Azure AD DS 관리 되는 도메인에 가입 된 관리 VM을 만든 다음 일반 AD DS 관리 도구를 설치 합니다. 예를 들어 Active Directory 관리 센터 또는 그룹 정책 DNS (Microsoft Management Console) 스냅인과 같은 MMC (Microsoft Management Console) 스냅인을 사용할 수 있습니다.

## <a name="user-account-creation"></a>사용자 계정 만들기

여러 가지 방법으로 Azure AD DS에서 사용자 계정을 만들 수 있습니다. 대부분의 사용자 계정은 Azure AD에서 동기화 되며, 온-프레미스 AD DS 환경에서 동기화 된 사용자 계정을 포함할 수도 있습니다. 수동으로 계정을 Azure AD DS에서 직접 만들 수도 있습니다. 초기 암호 동기화 또는 암호 정책과 같은 일부 기능은 사용자 계정을 만드는 방법 및 위치에 따라 다르게 작동 합니다.

* 사용자 계정은 Azure AD에서 동기화 할 수 있습니다. 여기에는 Azure AD에서 직접 만든 클라우드 전용 사용자 계정과 Azure AD Connect를 사용 하 여 온-프레미스 AD DS 환경에서 동기화 된 하이브리드 사용자 계정이 포함 됩니다.
    * Azure AD DS의 대부분 사용자 계정은 Azure AD의 동기화 프로세스를 통해 생성 됩니다.
* 사용자 계정은 Azure AD DS 관리 되는 도메인에서 수동으로 만들 수 있으며 Azure AD에 존재 하지 않습니다.
    * Azure AD DS 에서만 실행 되는 응용 프로그램에 대 한 서비스 계정을 만들어야 하는 경우 관리 되는 도메인에서 수동으로 만들 수 있습니다. Azure AD에서 동기화가 단방향 이므로 azure AD DS에서 만든 사용자 계정은 Azure AD에 다시 동기화 되지 않습니다.

## <a name="password-policy"></a>암호 정책

Azure AD DS에는 계정 잠금, 최대 암호 사용 기간 및 암호 복잡성과 같은 항목에 대 한 설정을 정의 하는 기본 암호 정책이 포함 됩니다. 계정 잠금 정책과 같은 설정은 이전 섹션에 설명 된 대로 사용자를 만든 방법에 관계 없이 Azure AD DS의 모든 사용자에 게 적용 됩니다. 최소 암호 길이 및 암호 복잡성과 같은 몇 가지 설정은 Azure AD DS에서 직접 만든 사용자 에게만 적용 됩니다.

사용자 고유의 사용자 지정 암호 정책을 만들어 Azure AD DS의 기본 정책을 재정의할 수 있습니다. 그런 다음 필요에 따라 특정 사용자 그룹에 이러한 사용자 지정 정책을 적용할 수 있습니다.

사용자 생성의 원본에 따라 암호 정책을 적용 하는 방법의 차이점에 대 한 자세한 내용은 [관리 되는 도메인의 암호 및 계정 잠금 정책][password-policy]을 참조 하세요.

## <a name="password-hashes"></a>암호 해시

관리되는 도메인에서 사용자를 인증하려면 Azure AD DS에 NTLM(NT LAN Manager) 및 Kerberos 인증에 적합한 형식의 암호 해시가 필요합니다. Azure AD DS를 테넌트에서 사용하도록 설정할 때까지 Azure AD는 NTLM 또는 Kerberos 인증에 필요한 형식의 암호 해시를 생성하거나 저장하지 않습니다. 또한 보안상의 이유로 Azure AD는 암호 자격 증명을 일반 텍스트 형식으로 저장하지 않습니다. 따라서 Azure AD는 사용자의 기존 자격 증명에 따라 이러한 NTLM 또는 Kerberos 암호 해시를 자동으로 생성할 수 없습니다.

클라우드 전용 사용자 계정의 경우 Azure AD DS를 사용하려면 먼저 사용자가 암호를 변경해야 합니다. 이 암호 변경 프로세스로 인해 Kerberos 및 NTLM 인증용 암호 해시가 생성되어 Azure AD에 저장됩니다.

Azure AD Connect를 사용 하 여 온-프레미스 AD DS 환경에서 동기화 된 사용자의 경우 [암호 해시 동기화를 사용 하도록 설정][hybrid-phs]합니다.

> [!IMPORTANT]
> Azure AD Connect는 azure AD 테 넌 트에 대해 Azure AD DS를 사용 하도록 설정 하는 경우에만 레거시 암호 해시를 동기화 합니다. Azure AD Connect 사용 하 여 온-프레미스 AD DS 환경을 Azure AD와 동기화 하는 경우에는 레거시 암호 해시가 사용 되지 않습니다.
>
> 레거시 응용 프로그램이 NTLM 인증 또는 LDAP 단순 바인딩을 사용 하지 않는 경우 Azure AD DS에 대해 NTLM 암호 해시 동기화를 사용 하지 않도록 설정 하는 것이 좋습니다. 자세한 내용은 [약한 암호 그룹 및 NTLM 자격 증명 해시 동기화 사용 안 함][secure-domain]을 참조 하세요.

적절히 구성되면 사용 가능한 암호 해시가 Azure AD DS 관리형 도메인에 저장됩니다. Azure AD DS 관리형 도메인을 삭제하면 해당 지점에 저장된 암호 해시도 모두 삭제됩니다. 나중에 Azure AD DS 관리 되는 도메인을 만드는 경우 Azure AD의 동기화 된 자격 증명 정보를 다시 사용할 수 없습니다. 암호 해시를 다시 저장 하려면 암호 해시 동기화를 다시 구성 해야 합니다. 이전에는 도메인 조인 VM 또는 사용자가 즉시 인증할 수 없었으므로 Azure AD에서 암호 해시를 새 Azure AD DS 관리형 도메인에 생성하고 저장해야 합니다. 자세한 내용은 [Azure AD DS 및 Azure AD Connect에 대한 암호 해시 동기화 프로세스][azure-ad-password-sync]를 참조하세요.

## <a name="next-steps"></a>다음 단계

시작 하려면 [Azure AD DS 관리 되는 도메인을 만듭니다][create-instance].

<!-- INTERNAL LINKS -->
[password-policy]: password-policy.md
[hybrid-phs]: tutorial-configure-password-hash-sync.md#enable-synchronization-of-password-hashes
[secure-domain]: secure-your-domain.md
[azure-ad-password-sync]: ../active-directory/hybrid/how-to-connect-password-hash-synchronization.md#password-hash-sync-process-for-azure-ad-domain-services
[create-instance]: tutorial-create-instance.md
