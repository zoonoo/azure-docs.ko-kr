---
title: Azure AD 도메인 서비스에 대한 관리 개념 | 마이크로 소프트 문서
description: Azure Active Directory 도메인 서비스 관리 도메인 관리 및 사용자 계정 및 암호의 동작을 관리하는 방법에 대해 알아보기
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 01/31/2020
ms.author: iainfou
ms.openlocfilehash: 682935fa2324b8de4992ab2f90c7f71e05c4f8ac
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79264233"
---
# <a name="management-concepts-for-user-accounts-passwords-and-administration-in-azure-active-directory-domain-services"></a>Azure Active Directory 도메인 서비스의 사용자 계정, 암호 및 관리에 대한 관리 개념

Azure Active Directory 도메인 서비스(AD DS) 관리 도메인을 만들고 실행하는 경우 기존의 온-프레미스 AD DS 환경에 비해 동작에 몇 가지 차이가 있습니다. Azure AD DS에서 동일한 관리 도구를 자체 관리 도메인으로 사용하지만 DC(도메인 컨트롤러)에 직접 액세스할 수는 없습니다. 또한 사용자 계정 생성의 소스에 따라 암호 정책과 암호 해시에 대한 동작에 몇 가지 차이점이 있습니다.

이 개념문서에서는 Azure AD DS 관리 도메인을 관리하는 방법과 생성 방식에 따라 사용자 계정의 다양한 동작을 자세히 설명합니다.

## <a name="domain-management"></a>도메인 관리

Azure AD DS에서 사용자 및 그룹, 자격 증명 및 정책과 같은 모든 리소스를 포함하는 도메인 컨트롤러(DC)는 관리되는 서비스의 일부입니다. 중복성의 경우 Azure AD DS 관리 도메인의 일부로 두 개의 DC가 만들어집니다. 관리 작업을 수행하기 위해 이러한 DC에 로그인할 수 없습니다. 대신 Azure AD DS 관리 도메인에 조인된 관리 VM을 만든 다음 일반 AD DS 관리 도구를 설치합니다. 예를 들어, DNS 또는 그룹 정책 개체와 같은 활성 디렉터리 관리 센터 또는 MMC(Microsoft 관리 콘솔) 스냅인을 사용할 수 있습니다.

## <a name="user-account-creation"></a>사용자 계정 생성

사용자 계정은 여러 가지 방법으로 Azure AD DS에서 만들 수 있습니다. 대부분의 사용자 계정은 Azure AD에서 동기화되며 온-프레미스 AD DS 환경에서 동기화된 사용자 계정도 포함될 수 있습니다. Azure AD DS에서 직접 계정을 수동으로 만들 수도 있습니다. 초기 암호 동기화 또는 암호 정책과 같은 일부 기능은 사용자 계정을 생성하는 방법과 위치에 따라 다르게 실행됩니다.

* 사용자 계정은 Azure AD에서 동기화할 수 있습니다. 여기에는 Azure AD에서 직접 만든 클라우드 전용 사용자 계정과 Azure AD Connect를 사용하여 온-프레미스 AD DS 환경에서 동기화된 하이브리드 사용자 계정이 포함됩니다.
    * Azure AD DS의 대부분의 사용자 계정은 Azure AD의 동기화 프로세스를 통해 만들어집니다.
* 사용자 계정은 Azure AD DS 관리 도메인에서 수동으로 만들 수 있으며 Azure AD에 존재하지 않습니다.
    * Azure AD DS에서만 실행되는 응용 프로그램에 대한 서비스 계정을 만들어야 하는 경우 관리되는 도메인에서 수동으로 만들 수 있습니다. 동기화는 Azure AD에서 한 가지 방법이기 때문에 Azure AD DS에서 만든 사용자 계정은 Azure AD와 다시 동기화되지 않습니다.

## <a name="password-policy"></a>암호 정책

Azure AD DS에는 계정 잠금, 최대 암호 수명 및 암호 복잡성과 같은 설정에 대한 설정을 정의하는 기본 암호 정책이 포함되어 있습니다. 계정 잠금 정책과 같은 설정은 이전 섹션에서 설명한 대로 사용자가 만들어진 방식에 관계없이 Azure AD DS의 모든 사용자에게 적용됩니다. 최소 암호 길이 및 암호 복잡성과 같은 몇 가지 설정은 Azure AD DS에서 직접 만든 사용자에게만 적용됩니다.

사용자 지정 암호 정책을 만들어 Azure AD DS의 기본 정책을 재정의할 수 있습니다. 그런 다음 필요에 따라 특정 사용자 그룹에 이러한 사용자 지정 정책을 적용할 수 있습니다.

사용자 생성 소스에 따라 암호 정책이 적용되는 방법의 차이점에 대한 자세한 내용은 [관리되는 도메인의 암호 및 계정 잠금 정책을][password-policy]참조하십시오.

## <a name="password-hashes"></a>암호 해시

관리되는 도메인에서 사용자를 인증하려면 Azure AD DS에 NTLM(NT LAN Manager) 및 Kerberos 인증에 적합한 형식의 암호 해시가 필요합니다. Azure AD DS를 테넌트에서 사용하도록 설정할 때까지 Azure AD는 NTLM 또는 Kerberos 인증에 필요한 형식의 암호 해시를 생성하거나 저장하지 않습니다. 또한 보안상의 이유로 Azure AD는 암호 자격 증명을 일반 텍스트 형식으로 저장하지 않습니다. 따라서 Azure AD는 사용자의 기존 자격 증명에 따라 이러한 NTLM 또는 Kerberos 암호 해시를 자동으로 생성할 수 없습니다.

클라우드 전용 사용자 계정의 경우 Azure AD DS를 사용하려면 먼저 사용자가 암호를 변경해야 합니다. 이 암호 변경 프로세스로 인해 Kerberos 및 NTLM 인증용 암호 해시가 생성되어 Azure AD에 저장됩니다.

Azure AD Connect를 사용하여 온-프레미스 AD DS 환경에서 동기화된 사용자의 경우 [암호 해시 동기화를 사용하도록 설정합니다.][hybrid-phs]

> [!IMPORTANT]
> Azure AD Connect는 Azure AD 테넌트에 대해 Azure AD DS를 사용하도록 설정할 때만 레거시 암호 해시를 동기화합니다. Azure AD Connect를 사용하여 온-프레미스 AD DS 환경을 Azure AD와 동기화하는 경우에만 레거시 암호 해시가 사용되지 않습니다.
>
> 레거시 응용 프로그램에서 NTLM 인증 또는 LDAP 단순 바인딩을 사용하지 않는 경우 Azure AD DS에 대한 NTLM 암호 해시 동기화를 사용하지 않도록 설정하는 것이 좋습니다. 자세한 내용은 [약한 암호 모음 및 NTLM 자격 증명 해시 동기화 를 참조하십시오.][secure-domain]

적절히 구성되면 사용 가능한 암호 해시가 Azure AD DS 관리형 도메인에 저장됩니다. Azure AD DS 관리형 도메인을 삭제하면 해당 지점에 저장된 암호 해시도 모두 삭제됩니다. 나중에 Azure AD DS 관리 도메인을 만드는 경우 Azure AD의 동기화된 자격 증명 정보를 다시 사용할 수 없습니다. 이전에는 도메인 조인 VM 또는 사용자가 즉시 인증할 수 없었으므로 Azure AD에서 암호 해시를 새 Azure AD DS 관리형 도메인에 생성하고 저장해야 합니다. 자세한 내용은 [Azure AD DS 및 Azure AD Connect에 대한 암호 해시 동기화 프로세스][azure-ad-password-sync]를 참조하세요.

> [!IMPORTANT]
> Azure AD Connect는 온-프레미스 AD DS 환경과의 동기화를 위해서만 설치되고 구성되어야 합니다. 개체를 Azure AD로 개체를 다시 동기화하기 위해 Azure AD DS 관리형 도메인에 Azure AD Connect를 설치하는 것은 지원되지 않습니다.

## <a name="forests-and-trusts"></a>포리스트 및 트러스트

*포리스트는* 하나 이상의 도메인을 그룹화하는 데 AD DS(Active Directory 도메인 서비스)에서 사용하는 논리적 *구문입니다.* 그런 다음 도메인은 사용자 또는 그룹에 대한 개체를 저장하고 인증 서비스를 제공합니다.

Azure AD DS에서 포리스트에는 하나의 도메인만 포함됩니다. 온-프레미스 AD DS 포리스트에는 종종 많은 도메인이 포함됩니다. 대규모 조직에서는 특히 인수 합병 후 각각 여러 도메인을 포함하는 여러 온-프레미스 포리스트가 생길 수 있습니다.

기본적으로 Azure AD DS 관리 도메인은 *사용자* 포리스트로 만들어집니다. 이 유형의 포리스트는 온-프레미스 AD DS 환경에서 만든 모든 사용자 계정을 포함하여 Azure AD의 모든 개체를 동기화합니다. 사용자 계정은 도메인 에 가입한 VM에 로그인하는 등 Azure AD DS 관리 도메인에 대해 직접 인증할 수 있습니다. 사용자 포리스트는 암호 해시를 동기화할 수 있고 사용자가 스마트 카드 인증과 같은 단독 로그인 방법을 사용하지 않는 경우에 작동합니다.

Azure AD DS *리소스* 포리스트에서 사용자는 온-프레미스 AD DS에서 단방향 포리스트 *트러스트를* 통해 인증합니다. 이 방법을 사용하면 사용자 개체 및 암호 해시가 Azure AD DS에 동기화되지 않습니다. 사용자 개체 및 자격 증명은 온-프레미스 AD DS에만 존재합니다. 이 방법을 사용하면 기업은 LDAPS, Kerberos 또는 NTLM과 같은 클래식 인증에 의존하는 Azure에서 리소스 및 응용 프로그램 플랫폼을 호스팅할 수 있지만 인증 문제 나 문제는 제거됩니다. Azure AD DS 리소스 포리스트는 현재 미리 보기 상태입니다.

Azure AD DS의 포리스트 형식에 대한 자세한 내용은 [리소스 포리스트란 무엇이며][concepts-forest] [Azure AD DS에서 포리스트 트러스트는 어떻게 작동합니까?][concepts-trust]

## <a name="azure-ad-ds-skus"></a>Azure AD DS SUS

Azure AD DS에서 사용 가능한 성능 및 기능은 SKU를 기반으로 합니다. 관리되는 도메인을 만들 때 SKU를 선택하고 관리되는 도메인을 배포한 후 비즈니스 요구 사항이 변경되면 SKU를 전환할 수 있습니다. 다음 표에서는 사용 가능한 SUS와 SUS 간의 차이점을 간략하게 설명합니다.

| SKU name   | 최대 개체 수 | Backup 주기 | 아웃바운드 포리스트 트러스트의 최대 수 |
|------------|----------------------|------------------|----|
| Standard   | 제한 없음            | 7일마다     | 0  |
| Enterprise | 제한 없음            | 3일마다     | 5  |
| Premium    | 제한 없음            | 매일            | 10 |

이러한 Azure AD DS SUS 이전에는 Azure AD DS 관리 도메인의 개체 수(사용자 및 컴퓨터 계정)를 기반으로 하는 청구 모델이 사용되었습니다. 관리되는 도메인의 개체 수에 따라 더 이상 가변 가격 책정이 없습니다.

자세한 내용은 Azure [AD DS 가격 페이지를][pricing]참조하십시오.

### <a name="managed-domain-performance"></a>관리되는 도메인 성능

도메인 성능은 응용 프로그램에 대한 인증을 구현하는 방법에 따라 다릅니다. 추가 계산 리소스는 쿼리 응답 시간을 개선하고 동기화 작업에 소요되는 시간을 줄이는 데 도움이 될 수 있습니다. SKU 수준이 증가하면 관리되는 도메인에서 사용할 수 있는 계산 리소스가 증가합니다. 응용 프로그램의 성능을 모니터링하고 필요한 리소스를 계획합니다.

비즈니스 또는 응용 프로그램이 변경해야 하고 Azure AD DS 관리 도메인에 대한 추가 계산 능력이 필요한 경우 다른 SKU로 전환할 수 있습니다.

### <a name="backup-frequency"></a>Backup 주기

백업 빈도는 관리되는 도메인의 스냅숏이 생성되는 빈도를 결정합니다. 백업은 Azure 플랫폼에서 관리하는 자동화된 프로세스입니다. 관리되는 도메인에 문제가 있는 경우 Azure 지원은 백업에서 복원하는 데 도움을 줄 수 있습니다. 동기화는 Azure *AD에서* 한 가지 방법으로만 발생하므로 Azure AD DS 관리 도메인의 모든 문제는 Azure AD 또는 온-프레미스 AD DS 환경 및 기능에 영향을 주지 않습니다.

SKU 수준이 증가하면 이러한 백업 스냅숏의 빈도가 증가합니다. 비즈니스 요구 사항 및 RPO(복구 지점 목표)를 검토하여 관리되는 도메인에 필요한 백업 빈도를 결정합니다. 비즈니스 또는 응용 프로그램 요구 사항이 변경되고 더 자주 백업해야 하는 경우 다른 SKU로 전환할 수 있습니다.

### <a name="outbound-forests"></a>아웃바운드 포리스트

이전 섹션에서는 Azure AD DS 관리 도메인에서 온-프레미스 AD DS 환경(현재 미리 보기)에 이르는 단방향 아웃바운드 포리스트 트러스트를 자세히 설명했습니다. SKU는 Azure AD DS 관리 도메인에 대해 만들 수 있는 최대 포리스트 트러스트 수를 결정합니다. 비즈니스 및 응용 프로그램 요구 사항을 검토하여 실제로 필요한 트러스트 수를 확인하고 적절한 Azure AD DS SKU를 선택합니다. 비즈니스 요구 사항이 변경되고 추가 포리스트 트러스트를 만들어야 하는 경우 다른 SKU로 전환할 수 있습니다.

## <a name="next-steps"></a>다음 단계

시작하려면 [Azure AD DS 관리 도메인을 만듭니다.][create-instance]

<!-- INTERNAL LINKS -->
[password-policy]: password-policy.md
[hybrid-phs]: tutorial-configure-password-hash-sync.md#enable-synchronization-of-password-hashes
[secure-domain]: secure-your-domain.md
[azure-ad-password-sync]: ../active-directory/hybrid/how-to-connect-password-hash-synchronization.md#password-hash-sync-process-for-azure-ad-domain-services
[create-instance]: tutorial-create-instance.md
[tutorial-create-instance-advanced]: tutorial-create-instance-advanced.md
[concepts-forest]: concepts-resource-forest.md
[concepts-trust]: concepts-forest-trust.md

<!-- EXTERNAL LINKS -->
[pricing]: https://azure.microsoft.com/pricing/details/active-directory-ds/
