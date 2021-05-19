---
title: Azure AD Domain Services의 관리 개념 | Microsoft Docs
description: Azure Active Directory Domain Services의 관리되는 도메인과 사용자 계정 및 암호의 동작을 관리하는 방법에 대해 알아봅니다.
services: active-directory-ds
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 03/10/2021
ms.author: justinha
ms.openlocfilehash: 5473ef46751d64fdbbf1d52f39c66f49d707e615
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102631390"
---
# <a name="management-concepts-for-user-accounts-passwords-and-administration-in-azure-active-directory-domain-services"></a>Azure Active Directory Domain Services에서 사용자 계정, 암호 및 관리에 대한 관리 개념

AD DS(Azure Active Directory Domain Services)의 관리되는 도메인을 만들고 실행할 때 기존 온-프레미스 AD DS 환경과 몇 가지 다른 동작이 있습니다. Azure AD DS에서 동일한 관리 도구를 자체 관리되는 도메인으로 사용할 수 있지만 DC(도메인 컨트롤러)에 직접 액세스할 수는 없습니다. 또한 사용자 계정이 생성된 출처에 따라 암호 정책 및 암호 해시의 동작에 몇 가지 차이점이 있습니다.

이 개념 문서에서는 관리되는 도메인을 관리하는 방법과 사용자 계정의 생성 방식에 따라 다른 동작에 대해 자세히 설명합니다.

## <a name="domain-management"></a>도메인 관리

관리되는 도메인은 DNS 네임스페이스와 일치하는 디렉터리입니다. 관리되는 도메인에서 사용자와 그룹, 자격 증명 및 정책과 같은 모든 리소스를 포함하는 DC(도메인 컨트롤러)는 관리되는 서비스의 일부입니다. 중복성을 위해 두 DC가 관리되는 도메인의 일부로 생성됩니다. 이러한 DC에 로그인하여 관리 태스크를 수행할 수는 없습니다. 대신, 관리되는 도메인에 가입된 관리 VM을 만든 다음, 일반 AD DS 관리 도구를 설치합니다. 예를 들어 Active Directory 관리 센터 또는 DNS나 그룹 정책 개체 등과 같은 MMC(Microsoft Management Console) 스냅인을 사용할 수 있습니다.

## <a name="user-account-creation"></a>사용자 계정 만들기

여러 가지 방법으로 관리되는 도메인에 사용자 계정을 만들 수 있습니다. 대부분의 사용자 계정은 Azure AD에서 동기화되며, 온-프레미스 AD DS 환경에서 동기화된 사용자 계정을 포함할 수도 있습니다. 관리되는 도메인에서 직접 계정을 수동으로 만들 수도 있습니다. 초기 암호 동기화 또는 암호 정책과 같은 일부 기능은 사용자 계정을 만드는 방법 및 위치에 따라 다르게 작동합니다.

* 사용자 계정은 Azure AD에서 동기화할 수 있습니다. 여기에는 Azure AD에서 직접 만든 클라우드 전용 사용자 계정과 Azure AD Connect를 사용하여 온-프레미스 AD DS 환경에서 동기화된 하이브리드 사용자 계정이 포함됩니다.
    * 관리되는 도메인에 있는 대부분의 사용자 계정은 Azure AD에서 동기화되는 프로세스를 통해 생성됩니다.
* 사용자 계정은 관리되는 도메인에서 수동으로 만들 수 있으며 Azure AD에 존재하지 않습니다.
    * 관리되는 도메인에서만 실행되는 애플리케이션에 대한 서비스 계정을 만들어야 하는 경우 관리되는 도메인에서 수동으로 만들 수 있습니다. Azure AD에서 단방향으로 동기화가 이루어지므로 관리되는 도메인에서 만든 사용자 계정은 Azure AD에 다시 동기화되지 않습니다.

## <a name="password-policy"></a>암호 정책

Azure AD DS에는 계정 잠금, 최대 암호 사용 기간 및 암호 복잡성과 같은 항목에 대한 설정을 정의하는 기본 암호 정책이 포함됩니다. 계정 잠금 정책과 같은 설정은 이전 섹션에서 설명한 것처럼, 사용자가 생성된 방법에 관계없이 관리되는 도메인의 모든 사용자에게 적용됩니다. 최소 암호 길이 및 암호 복잡성과 같은 몇 가지 설정은 관리되는 도메인에서 직접 만든 사용자에게만 적용됩니다.

관리되는 도메인의 기본 정책을 재정의하는 사용자 고유의 사용자 지정 암호 정책을 만들 수 있습니다. 그런 다음, 필요에 따라 특정 사용자 그룹에 이러한 사용자 지정 정책을 적용할 수 있습니다.

사용자가 생성된 출처에 따라 암호 정책을 다르게 적용하는 방법에 대한 자세한 내용은 [관리되는 도메인의 암호 및 계정 잠금 정책][password-policy]을 참조하세요.

## <a name="password-hashes"></a>암호 해시

관리되는 도메인에서 사용자를 인증하려면 Azure AD DS에 NTLM(NT LAN Manager) 및 Kerberos 인증에 적합한 형식의 암호 해시가 필요합니다. Azure AD DS를 테넌트에서 사용하도록 설정할 때까지 Azure AD는 NTLM 또는 Kerberos 인증에 필요한 형식의 암호 해시를 생성하거나 저장하지 않습니다. 또한 보안상의 이유로 Azure AD는 암호 자격 증명을 일반 텍스트 형식으로 저장하지 않습니다. 따라서 Azure AD는 사용자의 기존 자격 증명에 따라 이러한 NTLM 또는 Kerberos 암호 해시를 자동으로 생성할 수 없습니다.

클라우드 전용 사용자 계정의 경우 관리되는 도메인을 사용하려면 먼저 사용자가 암호를 변경해야 합니다. 이 암호 변경 프로세스로 인해 Kerberos 및 NTLM 인증용 암호 해시가 생성되어 Azure AD에 저장됩니다. 암호가 변경될 때까지 계정은 Azure AD에서 Azure AD DS로 동기화되지 않습니다.

Azure AD Connect를 사용하여 온-프레미스 AD DS 환경에서 동기화된 사용자의 경우 [암호 해시 동기화를 사용하도록 설정][hybrid-phs]합니다.

> [!IMPORTANT]
> Azure AD Connect는 Azure AD 테넌트에 Azure AD DS를 사용하도록 설정하는 경우에만 레거시 암호 해시를 동기화합니다. Azure AD Connect를 사용하여 온-프레미스 AD DS 환경을 Azure AD와 동기화하는 경우에만 레거시 암호 해시가 사용되지 않습니다.
>
> 레거시 애플리케이션이 NTLM 인증 또는 LDAP 단순 바인딩을 사용하지 않는 경우 Azure AD DS에 NTLM 암호 해시 동기화를 사용하지 않도록 설정하는 것이 좋습니다. 자세한 내용은 [취약한 암호 그룹 및 NTLM 자격 증명 해시 동기화를 사용하지 않도록 설정][secure-domain]을 참조하세요.

적절히 구성되면 사용 가능한 암호 해시가 관리되는 도메인에 저장됩니다. 관리되는 도메인을 삭제하면 해당 지점에 저장된 암호 해시도 모두 삭제됩니다. 나중에 또 다른 관리되는 도메인을 만드는 경우 Azure AD에서 동기화된 자격 증명 정보는 다시 사용할 수 없습니다. 암호 해시를 다시 저장하도록 암호 해시 동기화를 다시 구성해야 합니다. 이전에는 도메인 조인 VM 또는 사용자가 즉시 인증할 수 없었으므로 Azure AD에서 암호 해시를 새 관리되는 도메인에 생성하고 저장해야 합니다. 자세한 내용은 [Azure AD DS 및 Azure AD Connect에 대한 암호 해시 동기화 프로세스][azure-ad-password-sync]를 참조하세요.

> [!IMPORTANT]
> Azure AD Connect는 온-프레미스 AD DS 환경과의 동기화를 위해서만 설치되고 구성되어야 합니다. 개체를 Azure AD로 개체를 다시 동기화하기 위해 관리되는 도메인에 Azure AD Connect를 설치하는 것은 지원되지 않습니다.

## <a name="forests-and-trusts"></a>포리스트 및 트러스트

*포리스트* 는 AD DS(Active Directory Domain Services)에서 하나 이상의 *도메인* 을 그룹화하는 데 사용되는 논리적 구문입니다. 그런 다음, 도메인은 사용자 또는 그룹에 대한 개체를 저장하고 인증 서비스를 제공합니다.

Azure AD DS에서 포리스트는 도메인을 하나만 포함합니다. 온-프레미스 AD DS 포리스트에는 여러 도메인이 포함되는 경우가 많습니다. 대기업은 인수 및 합병 후에 각각에 여러 도메인이 포함되는 여러 온-프레미스 포리스트가 구현될 수 있습니다.

기본적으로 관리되는 도메인은 *사용자* 포리스트로 생성됩니다. 이 유형의 포리스트는 온-프레미스 AD DS 환경에서 만든 모든 사용자 계정을 포함하여 Azure AD의 모든 개체를 동기화합니다. 사용자 계정은 도메인에 가입된 VM에 로그인하는 등 관리되는 도메인에 대해 직접 인증을 받을 수 있습니다. 사용자 포리스트는 암호 해시를 동기화할 수 있고 사용자가 스마트 카드 인증과 같은 독점적인 로그인 방법을 사용하지 않을 때 작동합니다.

Azure AD DS *리소스* 포리스트에서 사용자는 단방향 포리스트 *트러스트* 를 통해 온-프레미스 AD DS에서 인증을 받습니다. 이 방법을 사용하면 사용자 개체 및 암호 해시가 Azure AD DS와 동기화되지 않습니다. 사용자 개체 및 자격 증명은 온-프레미스 AD DS에만 존재합니다. 이 접근 방식을 통해 엔터프라이즈는 Azure에서 LDAPS, Kerberos 또는 NTLM 등의 클래식 인증에 의존하는 리소스 및 애플리케이션 플랫폼을 호스트할 수 있지만 인증 문제나 우려는 해소됩니다.

Azure AD DS의 포리스트 유형에 대한 자세한 정보는 [리소스 포리스트란?][concepts-forest] 및 [Azure AD DS에서 포리스트 트러스트가 작동하는 방법][concepts-trust]을 참조하세요.

## <a name="azure-ad-ds-skus"></a>Azure AD DS SKU

Azure AD DS에서 사용 가능한 성능 및 기능은 SKU에 따라 다릅니다. 관리되는 도메인을 만들 때 SKU를 선택할 수 있으며, 관리되는 도메인이 배포된 후 비즈니스 요구 사항이 변경됨에 따라 SKU를 전환할 수 있습니다. 다음 표에서는 사용 가능한 SKU와 각각의 차이점을 간략하게 설명합니다.

| SKU name   | 최대 개체 수 | Backup 주기 | 최대 아웃바운드 포리스트 트러스트 수 |
|------------|----------------------|------------------|----|
| Standard   | 제한 없음            | 5일마다     | 0  |
| Enterprise | 제한 없음            | 3일마다     | 5  |
| 프리미엄    | 제한 없음            | 매일            | 10 |

이러한 Azure AD DS SKU 이전에는 관리되는 도메인의 개체 수(사용자 및 컴퓨터 계정)를 기준으로 하는 청구 모델이 사용되었습니다. 관리되는 도메인의 개체 수를 기준으로 하는 가변 가격 책정 방식은 더 이상 사용되지 않습니다.

자세한 내용은 [Azure AD DS 가격 책정 페이지][pricing]를 참조하세요.

### <a name="managed-domain-performance"></a>관리되는 도메인 성능

도메인 성능은 애플리케이션에 인증을 구현하는 방법에 따라 달라집니다. 추가 컴퓨팅 리소스는 쿼리 응답 시간을 개선하고 동기화 작업에 소요되는 시간을 줄이는 데 도움이 될 수 있습니다. SKU 수준이 늘어나면 관리되는 도메인에 사용할 수 있는 컴퓨팅 리소스가 늘어납니다. 애플리케이션의 성능을 모니터링하고 필요한 리소스를 계획하세요.

비즈니스 또는 애플리케이션 요구 사항이 변경되어 관리되는 도메인에 추가 컴퓨팅 성능이 필요한 경우 다른 SKU로 전환할 수 있습니다.

### <a name="backup-frequency"></a>Backup 주기

백업 빈도는 관리되는 도메인의 스냅샷이 생성되는 빈도를 결정합니다. 백업은 Azure 플랫폼에서 관리하는 자동 프로세스입니다. 관리되는 도메인에 문제가 발생할 경우 Azure 지원 팀이 백업으로 복원하는 데 도움을 줄 수 있습니다. 동기화는 Azure AD에서 *단방향* 으로만 이루어지므로 관리되는 도메인의 모든 문제는 Azure AD 또는 온-프레미스 AD DS 환경 및 기능에 영향을 주지 않습니다.

SKU 수준이 늘어나면 이러한 백업 스냅샷의 빈도가 증가합니다. 비즈니스 요구 사항 및 RPO(복구 지점 목표)를 검토하여 관리되는 도메인에 필요한 백업 빈도를 결정합니다. 비즈니스 또는 애플리케이션 요구 사항이 변경되어 더 자주 백업해야 하는 경우 다른 SKU로 전환할 수 있습니다.

### <a name="outbound-forest-trusts"></a>아웃바운드 포리스트 트러스트

이전 섹션에서는 관리되는 도메인에서 온-프레미스 AD DS 환경으로의 단방향 아웃바운드 포리스트 트러스트를 자세히 설명했습니다. SKU는 관리되는 도메인에 만들 수 있는 최대 포리스트 트러스트 수를 결정합니다. 비즈니스 및 애플리케이션 요구 사항을 검토하여 실제로 필요한 트러스트 수를 확인하고 적절한 Azure AD DS SKU를 선택합니다. 다시 말하지만, 비즈니스 요구 사항이 변경되어 추가 포리스트 트러스트를 만들어야 하는 경우에는 다른 SKU로 전환할 수 있습니다.

## <a name="next-steps"></a>다음 단계

시작하려면 [Azure AD DS의 관리되는 도메인을 만듭니다][create-instance].

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
