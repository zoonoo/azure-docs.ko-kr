---
title: Azure의 Active Directory 기반 서비스 비교 | Microsoft Docs
description: 이 개요에서는 Active Directory Domain Services, Azure Active Directory 및 Azure Active Directory Domain Services의 다양한 ID 제품을 비교합니다.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: overview
ms.date: 10/30/2019
ms.author: iainfou
ms.openlocfilehash: 04a1f19ddf894467a9129e8a16c951298a6af529
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73474719"
---
# <a name="compare-self-managed-active-directory-domain-services-azure-active-directory-and-managed-azure-active-directory-domain-services"></a>자체 관리형 Active Directory Domain Services, Azure Active Directory 및 관리형 Azure Active Directory Domain Services 비교

중앙 ID에 대한 애플리케이션, 서비스 또는 디바이스 액세스를 제공하기 위해 Azure에서 Active Directory 기반 서비스를 사용하는 세 가지 일반적인 방법이 있습니다. 이러한 ID 솔루션을 선택하면 조직의 요구 사항에 가장 적합한 디렉터리를 유연하게 사용할 수 있습니다. 예를 들어 모바일 디바이스를 실행하는 클라우드 전용 사용자를 일반적으로 관리하는 경우 사용자 고유의 AD DS(Active Directory Domain Services) ID 솔루션을 구축하고 실행하는 것은 적합하지 않을 수 있습니다. 대신 Azure Active Directory만 사용할 수 있습니다.

세 가지 Active Directory 기반 ID 솔루션은 일반적인 이름과 기술을 공유하지만 서로 다른 고객의 요구 사항을 충족하는 서비스를 제공하도록 설계되었습니다. 이러한 ID 솔루션 및 기능 세트는 개략적으로 다음과 같습니다.

* **AD DS(Active Directory Domain Services)** - ID 및 인증, 컴퓨터 개체 관리, 그룹 정책 및 신뢰와 같은 주요 기능을 제공하는 엔터프라이즈 지원 LDAP(Lightweight Directory Access Protocol) 서버입니다.
    * AD DS는 온-프레미스 IT 환경을 갖춘 많은 조직의 중앙 구성 요소이며, 핵심 사용자 계정 인증 및 컴퓨터 관리 기능을 제공합니다.
* **Azure AD(Azure Active Directory)** - Office 365, Azure Portal 또는 SaaS 애플리케이션과 같은 리소스에 사용자 계정 및 인증 서비스를 제공하는 클라우드 기반 ID 및 모바일 디바이스 관리입니다.
    * Azure AD는 온-프레미스 AD DS 환경과 동기화하여 클라우드에서 기본적으로 작동하는 단일 ID를 사용자에게 제공할 수 있습니다.
* **Azure AD DS(Azure Active Directory Domain Services)** - 도메인 조인, 그룹 정책, LDAP 및 Kerberos/NTLM 인증과 같은 완전히 호환되는 기존 AD DS 기능의 하위 세트를 관리되는 도메인 서비스에 제공합니다.
    * Azure AD DS는 온-프레미스 AD DS 환경과 자체적으로 동기화할 수 있는 Azure AD와 통합되어 중앙 ID 사용 사례를 리프트 앤 시프트 전략의 일환으로 Azure에서 실행되는 기존 웹 애플리케이션으로 확장합니다.

이 개요 문서에서는 조직의 요구 사항에 따라 이러한 ID 솔루션이 함께 작동하거나 독립적으로 사용되는 방법을 비교 및 대조합니다.

## <a name="azure-ad-ds-and-self-managed-ad-ds"></a>Azure AD DS 및 자체 관리형 AD DS

Kerberos 또는 NTLM과 같은 기존 인증 메커니즘에 액세스해야 하는 애플리케이션과 서비스가 있는 경우 클라우드에서 Active Directory Domain Services를 제공하는 두 가지 방법이 있습니다.

* Azure AD DS(Azure Active Directory Domain Services)를 사용하여 만든 *관리형* 도메인입니다. Microsoft에서 필요한 리소스를 만들고 관리합니다.
* VM(가상 머신), Windows Server 게스트 OS 및 AD DS(Active Directory Domain Services)와 같은 기존 리소스를 사용하여 만들고 구성한 *자체 관리형* 도메인입니다. 그런 다음, 이러한 리소스를 계속 관리합니다.

Azure AD DS를 사용하면 Microsoft에서 핵심 서비스 구성 요소를 *관리형* 도메인 환경으로 배포하고 유지 관리합니다. VM, Windows Server OS 또는 DC(도메인 컨트롤러)와 같은 구성 요소에 대한 AD DS 인프라를 배포, 관리, 패치 및 보호하지 않습니다.

Azure AD DS는 기능의 더 작은 하위 세트를 기존의 자체 관리형 AD DS 환경에 제공하여 설계 및 관리 복잡성의 일부를 줄입니다. 예를 들어 설계하고 유지 관리할 AD 포리스트, 도메인, 사이트 및 복제 링크가 없습니다. 클라우드에서 실행되고 Kerberos 또는 NTLM과 같은 기존 인증 메커니즘에 액세스해야 하는 애플리케이션과 서비스의 경우 Azure AD DS는 관리 오버헤드가 최소화된 관리되는 도메인 환경을 제공합니다.

자체 관리형 AD DS 환경을 배포하고 실행하는 경우 연결된 모든 인프라 및 디렉터리 구성 요소를 유지 관리해야 합니다. 자체 관리형 AD DS 환경을 사용하면 유지 관리 오버헤드가 추가로 발생하지만, 스키마를 확장하거나 포리스트 트러스트를 만드는 것과 같은 추가 작업을 수행할 수 있습니다.

클라우드에서 ID를 애플리케이션과 서비스에 제공하는 자체 관리형 AD DS 환경에 대한 일반적인 배포 모델은 다음과 같습니다.

* **독립 실행형 클라우드 전용 AD DS** - Azure VM이 도메인 컨트롤러로 구성되고, 별도의 클라우드 전용 AD DS 환경이 만들어집니다. 이 AD DS 환경은 온-프레미스 AD DS 환경과 통합되지 않습니다. 다른 자격 증명 세트를 사용하여 클라우드에서 VM에 로그인하고 관리합니다.
* **리소스 포리스트 배포** - Azure VM이 도메인 컨트롤러로 구성되고, AD DS 도메인이 기존 포리스트의 일부로 만들어집니다. 그러면 트러스트 관계가 온-프레미스 AD DS 환경에 구성됩니다. 다른 Azure VM은 클라우드에서 이 리소스 포리스트의 도메인에 조인할 수 있습니다. 사용자 인증은 온-프레미스 AD DS 환경에 대한 VPN/ExpressRoute 연결을 통해 실행됩니다.
* **온-프레미스 도메인을 Azure로 확장** - Azure 가상 네트워크가 VPN/ExpressRoute 연결을 통해 온-프레미스 네트워크에 연결됩니다. Azure VM은 이 Azure 가상 네트워크에 연결되므로 온-프레미스 AD DS 환경의 도메인에 조인할 수 있습니다.
    * 다른 방법으로, Azure VM을 만들어 온-프레미스 AD DS 도메인에서 복제본 도메인 컨트롤러로 승격시키는 것입니다. 이러한 도메인 컨트롤러는 VPN/ExpressRoute 연결을 통해 온-프레미스 AD DS 환경에 복제됩니다. 온-프레미스 AD DS 도메인은 효과적으로 Azure로 확장됩니다.

다음 표에는 조직에 필요할 수 있는 몇 가지 기능과 관리형 Azure AD DS 도메인 또는 자체 관리형 AD DS 도메인 간의 차이점이 간략하게 나와 있습니다.

| **기능** | **Azure AD DS** | **자체 관리형 AD DS** |
| ----------- |:---------------:|:----------------------:|
| **관리형 서비스**                               | **&#x2713;** | **&#x2715;** |
| **보안 배포**                            | **&#x2713;** | 관리자가 배포를 보호함 |
| **DNS 서버**                                    | **&#x2713;** (관리되는 서비스) |**&#x2713;** |
| **도메인 또는 엔터프라이즈 관리자 권한** | **&#x2715;** | **&#x2713;** |
| **도메인 가입**                                   | **&#x2713;** | **&#x2713;** |
| **NTLM 및 Kerberos를 사용하여 도메인 인증** | **&#x2713;** | **&#x2713;** |
| **Kerberos 제한 위임**               | 리소스 기반 | 리소스 기반 및 계정 기반|
| **사용자 지정 OU 구조**                           | **&#x2713;** | **&#x2713;** |
| **그룹 정책**                                  | **&#x2713;** | **&#x2713;** |
| **스키마 확장**                             | **&#x2715;** | **&#x2713;** |
| **AD 도메인/포리스트 트러스트**                     | **&#x2715;** | **&#x2713;** |
| **LDAPS(보안 LDAP)**                           | **&#x2713;** | **&#x2713;** |
| **LDAP 읽기**                                     | **&#x2713;** | **&#x2713;** |
| **LDAP 쓰기**                                    | **&#x2713;** (관리되는 도메인 내) | **&#x2713;** |
| **지리적 분산 배포**                   | **&#x2715;** | **&#x2713;** |

## <a name="azure-ad-ds-and-azure-ad"></a>Azure AD DS 및 Azure AD

Azure AD를 사용하면 조직에서 사용하는 디바이스의 ID를 관리하고 이러한 디바이스에서 회사 리소스에 대한 액세스를 제어할 수 있습니다. 사용자는 또한 디바이스에 ID를 제공하는 Azure AD에 자신의 개인 디바이스(BYO(Bring-Your-Own), 모델)를 등록할 수 있습니다. 그러면 사용자가 Azure AD에 로그인하고 디바이스를 사용하여 보안 리소스에 액세스할 때 Azure AD에서 해당 디바이스를 인증합니다. 디바이스는 Microsoft Intune과 같은 MDM(모바일 디바이스 관리) 소프트웨어를 사용하여 관리할 수 있습니다. 이 관리 기능을 사용하면 중요한 리소스에 대한 액세스를 정책 준수 관리형 디바이스로 제한할 수 있습니다.

또한 기존 컴퓨터와 랩톱도 Azure AD에 조인할 수 있습니다. 이 메커니즘은 사용자가 회사 자격 증명을 사용하여 디바이스에 로그인할 수 있도록 하는 것과 같이 개인 디바이스를 Azure AD에 등록할 때와 동일한 이점을 누릴 수 있습니다.

Azure AD 가입 디바이스에는 다음과 같은 이점이 있습니다.

* Azure AD에서 보호하는 애플리케이션에 대한 SSO(Single Sign-On)
* 디바이스 전반에 걸친 사용자 설정의 엔터프라이즈 정책 준수 로밍.
* 회사 자격 증명을 사용하여 비즈니스용 Windows 스토어에 액세스
* 비즈니스용 Windows Hello
* 회사 정책을 준수하는 디바이스에서 앱 및 리소스에 대한 제한적 액세스.

온-프레미스 AD DS 환경이 포함된 하이브리드 배포의 사용 여부와 관계없이 디바이스는 Azure AD에 조인할 수 있습니다. 다음 표에는 일반적인 디바이스 소유권 모델과 이러한 모델이 일반적으로 도메인에 조인되는 방법이 간략하게 나와 있습니다.

| **디바이스의 유형**                                        | **디바이스 플랫폼**             | **메커니즘**          |
|:----------------------------------------------------------| -------------------------------- | ---------------------- |
| 개인 디바이스                                          | Windows 10, iOS, Android, Mac OS | Azure AD 등록    |
| 온-프레미스 AD DS에 조인되지 않은 조직 소유의 디바이스 | 윈도우 10                       | Azure AD 가입        |
| 온-프레미스 AD DS에 조인된 조직 소유의 디바이스  | 윈도우 10                       | 하이브리드 Azure AD 가입 |

Azure AD 조인 또는 등록 디바이스에서 사용자 인증은 최신 OAuth/OpenID Connect 기반 프로토콜을 사용하여 수행됩니다. 이러한 프로토콜은 인터넷을 통해 작동하도록 설계되었으므로 사용자가 어디서나 회사 리소스에 액세스하는 모바일 시나리오에 적합합니다.

Azure AD DS 조인 디바이스를 사용하면 애플리케이션에서 Kerberos 및 NTLM 프로토콜을 인증에 사용할 수 있으므로, 리프트 앤 시프트 전략의 일환으로 Azure VM에서 실행되도록 마이그레이션된 레거시 애플리케이션을 지원할 수 있습니다. 다음 표에는 디바이스가 표시되는 방법의 차이점이 간략하게 나와 있으며, 이를 통해 디렉터리에 대해 디바이스 자체를 인증할 수 있습니다.

| **측면**                      | **Azure AD 조인**                                 | **Azure AD DS 조인**                                                    |
|:--------------------------------| --------------------------------------------------- | ------------------------------------------------------------------------- |
| 다음에 의해 디바이스가 제어됨            | Azure AD                                            | Azure AD DS 관리형 도메인                                                |
| 디렉터리의 표현 | Azure AD 디렉터리의 디바이스 개체            | Azure AD DS 관리형 도메인의 컴퓨터 개체                        |
| 인증                  | OAuth/OpenID Connect 기반 프로토콜              | Kerberos 및 NTLM 프로토콜                                               |
| 관리                      | Intune과 같은 MDM(모바일 디바이스 관리) 소프트웨어 | 그룹 정책                                                              |
| 네트워킹                      | 인터넷을 통해 작동                             | 관리되는 도메인이 배포되는 가상 네트워크에 연결되거나 피어링되어 있어야 합니다. |
| 유용한 경우                    | 최종 사용자 모바일 또는 데스크톱 디바이스                  | Azure에 배포된 서버 VM                                              |

## <a name="next-steps"></a>다음 단계

Azure AD DS 사용을 시작하려면 [Azure Portal을 사용하여 Azure AD DS 관리형 도메인을 만듭니다][tutorial-create].

<!-- INTERNAL LINKS -->
[manage-dns]: manage-dns.md
[deploy-kcd]: deploy-kcd.md
[custom-ou]: create-ou.md
[manage-gpos]: manage-group-policy.md
[tutorial-ldaps]: tutorial-configure-ldaps.md
[tutorial-create]: tutorial-create-instance.md
