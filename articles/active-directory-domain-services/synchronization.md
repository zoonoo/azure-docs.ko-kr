---
title: Azure AD 도메인 서비스에서 동기화가 작동하는 방법 | 마이크로 소프트 문서
description: 동기화 프로세스가 Azure AD 테넌트 또는 온-프레미스 Active Directory 도메인 서비스 환경에서 Azure Active Directory 도메인 서비스 관리 도메인에 대한 개체 및 자격 증명에 대해 작동하는 방법을 알아봅니다.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 57cbf436-fc1d-4bab-b991-7d25b6e987ef
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 02/10/2020
ms.author: iainfou
ms.openlocfilehash: 7e0e904b182a57a51b5d76f0acebc13bce5902b2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78944427"
---
# <a name="how-objects-and-credentials-are-synchronized-in-an-azure-ad-domain-services-managed-domain"></a>Azure AD 도메인 서비스 관리 도메인에서 개체 및 자격 증명이 동기화되는 방법

Azure Active Directory 도메인 서비스(AD DS) 관리 도메인의 개체 및 자격 증명은 도메인 내에서 로컬로 만들거나 Azure Active Directory(Azure AD) 테넌트에서 동기화할 수 있습니다. Azure AD DS를 처음 배포할 때 자동 단방향 동기화가 구성되고 Azure AD에서 개체를 복제하기 시작됩니다. 이 단방향 동기화는 Azure AD DS 관리 도메인을 Azure AD의 변경 내용과 함께 최신 상태로 유지하기 위해 백그라운드에서 계속 실행됩니다. Azure AD DS에서 Azure AD로 다시 동기화가 발생하지 않습니다.

하이브리드 환경에서온-프레미스 AD DS 도메인의 개체 및 자격 증명은 Azure AD Connect를 사용하여 Azure AD에 동기화할 수 있습니다. 이러한 개체가 Azure AD에 성공적으로 동기화되면 자동 백그라운드 동기화를 통해 Azure AD DS 관리 도메인을 사용하는 응용 프로그램에서 해당 개체 및 자격 증명을 사용할 수 있습니다.

다음 다이어그램에서는 Azure AD DS, Azure AD 및 선택적 온-프레미스 AD DS 환경 간의 동기화 가 작동하는 방법을 보여 줍니다.

![Azure AD 도메인 서비스 관리 도메인에 대한 동기화 개요](./media/active-directory-domain-services-design-guide/sync-topology.png)

## <a name="synchronization-from-azure-ad-to-azure-ad-ds"></a>Azure AD에서 Azure AD DS로의 동기화

사용자 계정, 그룹 멤버 자격 및 자격 증명 해시는 Azure AD에서 Azure AD DS로 한 가지 방법으로 동기화됩니다. 이 동기화 프로세스는 자동입니다. 이 동기화 프로세스를 구성, 모니터링 또는 관리할 필요가 없습니다. Azure AD 디렉터리내 개체 수에 따라 초기 동기화는 몇 시간에서 며칠까지 걸릴 수 있습니다. 초기 동기화가 완료되면 암호 또는 특성 변경과 같은 Azure AD에서 변경된 내용이 Azure AD DS에 자동으로 동기화됩니다.

동기화 프로세스는 설계에 의해 단방향 / 단방향입니다. Azure AD DS에서 Azure AD로 변경 내용을 역방향 동기화할 필요가 없습니다. Azure AD DS 관리 되는 도메인은 주로 만들 수 있는 사용자 지정 O를 제외 하 고 읽기 전용입니다. Azure AD DS 관리 도메인 내의 사용자 특성, 사용자 암호 또는 그룹 구성원 자격은 변경할 수 없습니다.

## <a name="attribute-synchronization-and-mapping-to-azure-ad-ds"></a>Azure AD DS에 대한 특성 동기화 및 매핑

다음 표에는 몇 가지 일반적인 특성과 이러한 특성이 Azure AD DS와 동기화되는 방식이 나열되어 있습니다.

| Azure AD DS의 특성 | 원본 | 메모 |
|:--- |:--- |:--- |
| UPN | Azure AD 테넌트에서 사용자의 *UPN* 특성 | Azure AD 테넌트의 UPN 특성은 Azure AD DS에 있는 것처럼 동기화됩니다. Azure AD DS 관리 도메인에 로그인하는 가장 신뢰할 수 있는 방법은 UPN을 사용하는 것입니다. |
| SAMAccountName | Azure AD 테넌트또는 자동 생성된 사용자의 *mailNickname* 특성 | *SAMAccountName* 특성은 Azure AD 테넌트의 *mailNickname* 특성에서 파생됩니다. 여러 사용자 계정에 동일한 *mail@Mail@adaily.kr]* *SAMAccountName* 사용자의 *mail@.co.kr* *UPN* *SAMAccountName* *SAMAccountName* |
| 암호 | Azure AD 테넌트의 사용자 암호 | NTLM 또는 Kerberos 인증에 필요한 레거시 암호 해시는 Azure AD 테넌트에서 동기화됩니다. Azure AD 테넌트가 Azure AD Connect를 사용하여 하이브리드 동기화를 위해 구성된 경우 이러한 암호 해시는 온-프레미스 AD DS 환경에서 공급됩니다. |
| 기본 사용자/그룹 SID | 자동 생성 | 사용자/그룹 계정의 기본 SID는 Azure AD DS에서 자동으로 생성됩니다. 이 특성은 온-프레미스 AD DS 환경에서 개체의 기본 사용자/그룹 SID와 일치하지 않습니다. 이러한 불일치는 Azure AD DS 관리 도메인이 온-프레미스 AD DS 도메인과 다른 SID 네임스페이스를 가지고 있기 때문입니다. |
| 사용자 및 그룹에 대한 SID 기록 | 온-프레미스 기본 사용자 및 그룹 SID | Azure AD DS의 사용자 및 그룹에 대한 *SidHistory* 특성은 온-프레미스 AD DS 환경에서 해당 기본 사용자 또는 그룹 SID와 일치하도록 설정됩니다. 이 기능을 사용하면 ACL 리소스를 다시 사용할 필요가 없으므로 온-프레미스 응용 프로그램을 Azure AD DS로 더 쉽게 이동할 수 있습니다. |

> [!TIP]
> **UPN 형식을 사용하여 관리되는 도메인에 로그인** 과 같은 `AADDSCONTOSO\driley` *SAMAccountName* 특성은 Azure AD DS 관리 도메인의 일부 사용자 계정에 대해 자동으로 생성될 수 있습니다. 사용자의 자동 생성 *된 SAMAccountNameUPN* 접두사와 다를 수 있으므로 항상 신뢰할 수 있는 로그인 방법은 아닙니다.
>
> 예를 들어 여러 사용자가 동일한 *mailNickname* 특성을 가지고 있거나 사용자가 UPN 접두사를 지나치게 긴 경우 이러한 사용자의 *SAMAccountName이* 자동으로 생성될 수 있습니다. 와 같은 `driley@aaddscontoso.com`UPN 형식을 사용하여 Azure AD DS 관리 도메인에 안정적으로 로그인합니다.

### <a name="attribute-mapping-for-user-accounts"></a>사용자 계정에 대한 특성 매핑

다음 표에서는 Azure AD의 사용자 개체에 대한 특정 특성이 Azure AD DS의 해당 특성과 동기화되는 방법을 보여 줍니다.

| Azure AD의 사용자 특성 | Azure AD DS의 사용자 특성 |
|:--- |:--- |
| accountEnabled |userAccountControl(ACCOUNT_DISABLED 비트를 설정하거나 지움) |
| city |l |
| country |co |
| department |department |
| displayName |displayName |
| facsimileTelephoneNumber |facsimileTelephoneNumber |
| givenName |givenName |
| jobTitle |title |
| mail |mail |
| mailNickname |msDS-AzureADMailNickname |
| mailNickname |SAMAccountName (때로는 자동 생성 될 수 있음) |
| mobile |mobile |
| objectid |msDS-AzureADObjectId |
| onPremiseSecurityIdentifier |sidHistory |
| passwordPolicies |userAccountControl(DONT_EXPIRE_PASSWORD 비트를 설정하거나 지움) |
| physicalDeliveryOfficeName |physicalDeliveryOfficeName |
| postalCode |postalCode |
| preferredLanguage |preferredLanguage |
| state |st |
| streetAddress |streetAddress |
| surname |sn |
| telephoneNumber |telephoneNumber |
| userPrincipalName |userPrincipalName |

### <a name="attribute-mapping-for-groups"></a>그룹에 대한 특성 매핑

다음 표에서는 Azure AD의 그룹 개체에 대한 특정 특성이 Azure AD DS의 해당 특성과 동기화되는 방법을 보여 줍니다.

| Azure AD의 특성 그룹 화 | Azure AD DS의 특성 그룹 화 |
|:--- |:--- |
| displayName |displayName |
| displayName |SAMAccountName (때로는 자동 생성 될 수 있음) |
| mail |mail |
| mailNickname |msDS-AzureADMailNickname |
| objectid |msDS-AzureADObjectId |
| onPremiseSecurityIdentifier |sidHistory |
| securityEnabled |groupType |

## <a name="synchronization-from-on-premises-ad-ds-to-azure-ad-and-azure-ad-ds"></a>온-프레미스 AD DS에서 Azure AD 및 Azure AD DS로 동기화

Azure AD Connect는 온-프레미스 AD DS 환경에서 Azure AD로 사용자 계정, 그룹 구성원 및 자격 증명 해시를 동기화하는 데 사용됩니다. UPN 및 온-프레미스 보안 식별자(SID)와 같은 사용자 계정의 특성이 동기화됩니다. Azure AD DS를 사용하여 로그인하려면 NTLM 및 Kerberos 인증에 필요한 레거시 암호 해시도 Azure AD에 동기화됩니다.

> [!IMPORTANT]
> Azure AD Connect는 온-프레미스 AD DS 환경과의 동기화를 위해서만 설치되고 구성되어야 합니다. 개체를 Azure AD로 개체를 다시 동기화하기 위해 Azure AD DS 관리형 도메인에 Azure AD Connect를 설치하는 것은 지원되지 않습니다.

쓰기 백을 구성하면 Azure AD의 변경 내용이 온-프레미스 AD DS 환경과 다시 동기화됩니다. 예를 들어 사용자가 Azure AD 셀프 서비스 암호 관리를 사용하여 암호를 변경하는 경우 온-프레미스 AD DS 환경에서 암호가 다시 업데이트됩니다.

> [!NOTE]
> 모든 알려진 버그에 대한 수정 프로그램을 유지할 수 있도록 항상 최신 버전의 Azure AD Connect를 사용합니다.

### <a name="synchronization-from-a-multi-forest-on-premises-environment"></a>다중 포리스트 온-프레미스 환경에서의 동기화

대부분의 조직에는 여러 포리스트가 포함된 상당히 복잡한 온-프레미스 AD DS 환경이 있습니다. Azure AD Connect는 다중 포리스트 환경에서 Azure AD로 사용자, 그룹 및 자격 증명 해시 동기화를 지원합니다.

Azure AD에는 훨씬 간단하고 평평한 네임스페이스가 있습니다. 사용자가 Azure AD에서 보호되는 애플리케이션에 안정적으로 액세스할 수 있도록 하려면 서로 다른 포리스트의 사용자 계정 간에 발생하는 UPN 충돌을 해결해야 합니다. Azure AD DS 관리 도메인은 Azure AD와 유사한 플랫 OU 구조를 사용합니다. 온-프레미스에서 계층적 OU 구조를 구성한 경우에도 모든 사용자 계정 및 그룹은 서로 다른 온-프레미스 도메인 또는 포리스트에서 동기화되더라도 *AADDC Users* 컨테이너에 저장됩니다. Azure AD DS 관리 도메인은 계층적 OU 구조를 평평하게 합니다.

이전에 자세히 설명했듯이 Azure AD DS에서 Azure AD로 다시 동기화된 동기화가 없습니다. Azure AD DS에서 [사용자 지정 조직 단위(OU)를 만든](create-ou.md) 다음 해당 사용자 지정 OU 내에서 사용자, 그룹 또는 서비스 계정을 만들 수 있습니다. 사용자 지정 OUs에서 만든 개체는 Azure AD에 다시 동기화되지 않습니다. 이러한 개체는 Azure AD DS 관리 도메인 내에서만 사용할 수 있으며 Azure AD PowerShell cmdlets, Microsoft 그래프 API 또는 Azure AD 관리 UI를 사용하여 표시되지 않습니다.

## <a name="what-isnt-synchronized-to-azure-ad-ds"></a>Azure AD DS에 동기화되지 않은 것

다음 개체 또는 특성은 온-프레미스 AD DS 환경에서 Azure AD 또는 Azure AD DS로 동기화되지 않습니다.

* **제외된 특성:** Azure AD Connect를 사용하여 온-프레미스 AD DS 환경에서 Azure AD에 동기화에서 특정 특성을 제외하도록 선택할 수 있습니다. 이러한 제외된 특성은 Azure AD DS에서 사용할 수 없습니다.
* **그룹 정책:** 온-프레미스 AD DS 환경에서 구성된 그룹 정책은 Azure AD DS와 동기화되지 않습니다.
* **Sysvol 폴더:** 온-프레미스 AD DS 환경에서 *Sysvol* 폴더의 내용은 Azure AD DS에 동기화 되지 않습니다.
* **컴퓨터 개체:** 온-프레미스 AD DS 환경에 조인된 컴퓨터의 컴퓨터 개체는 Azure AD DS와 동기화되지 않습니다. 이러한 컴퓨터는 Azure AD DS 관리 도메인과 트러스트 관계를 맺지 않으며 온-프레미스 AD DS 환경에만 속합니다. Azure AD DS에서는 관리되는 도메인에 명시적으로 도메인으로 조인한 컴퓨터의 컴퓨터 개체만 표시됩니다.
* **사용자 및 그룹에 대한 SidHistory 특성:** 온-프레미스 AD DS 환경의 기본 사용자 및 기본 그룹 SID는 Azure AD DS에 동기화됩니다. 그러나 사용자 및 그룹에 대한 기존 *SidHistory* 특성은 온-프레미스 AD DS 환경에서 Azure AD DS로 동기화되지 않습니다.
* **조직 단위(OU) 구조:** 온-프레미스 AD DS 환경에 정의된 조직 단위는 Azure AD DS와 동기화되지 않습니다. Azure AD DS에는 사용자를 위한 두 개의 기본 제공 O와 컴퓨터용 이 두 개의 기본 제공 O가 있습니다. Azure AD DS 관리 도메인에는 플랫 OU 구조가 있습니다. [관리되는 도메인에서 사용자 지정 OU를 만들도록](create-ou.md)선택할 수 있습니다.

## <a name="password-hash-synchronization-and-security-considerations"></a>암호 해시 동기화 및 보안 고려 사항

Azure AD DS를 사용하도록 설정하면 NTLM + Kerberos 인증을 위한 레거시 암호 해시가 필요합니다. Azure AD는 일반 텍스트 암호를 저장하지 않으므로 기존 사용자 계정에 대해 이러한 해시를 자동으로 생성할 수 없습니다. 생성 및 저장되면 NTLM 및 Kerberos 호환 암호 해시는 항상 Azure AD에서 암호화된 방식으로 저장됩니다.

암호화 키는 각 Azure AD 테넌트에 고유합니다. 이러한 해시는 Azure AD DS만 암호 해독 키에 액세스할 수 있도록 암호화됩니다. Azure AD의 다른 서비스나 구성 요소는 암호 해독 키에 액세스할 수 없습니다.

그런 다음 레거시 암호 해시가 Azure AD에서 Azure AD DS 관리 도메인의 도메인 컨트롤러로 동기화됩니다. Azure AD DS에서 이러한 관리되는 도메인 컨트롤러의 디스크는 미사용 으로 암호화됩니다. 이러한 암호 해시는 온-프레미스 AD DS 환경에서 암호를 저장하고 보안하는 방법과 유사한 도메인 컨트롤러에 저장되고 보호됩니다.

클라우드 전용 Azure AD 환경의 경우 필요한 암호 해시를 생성하고 Azure AD에 저장하려면 [암호를 재설정/변경해야 합니다.](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds) Azure AD Domain Services를 사용하도록 설정한 후에 Azure AD에서 만든 클라우드 사용자 계정의 경우 암호 해시는 NTLM 및 Kerberos 호환 형식으로 생성되고 저장됩니다. 이러한 새 계정은 암호를 재설정하거나 변경할 필요가 없으므로 레거시 암호 해시가 생성됩니다.

Azure AD Connect를 사용하여 온-프레미스 AD DS 환경에서 동기화된 하이브리드 사용자 계정의 경우 [NTLM 및 Kerberos 호환 형식의 암호 해시를 동기화하도록 Azure AD Connect를 구성해야 합니다.](tutorial-configure-password-hash-sync.md)

## <a name="next-steps"></a>다음 단계

암호 동기화의 세부 사항에 대한 자세한 내용은 [Azure AD Connect에서 암호 해시 동기화가 작동하는 방식을](../active-directory/hybrid/how-to-connect-password-hash-synchronization.md?context=/azure/active-directory-domain-services/context/azure-ad-ds-context)참조하십시오.

Azure AD DS를 시작하려면 [관리되는 도메인을 만듭니다.](tutorial-create-instance.md)
