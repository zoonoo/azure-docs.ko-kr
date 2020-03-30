---
title: 계획 하이브리드 Azure 활성 디렉터리 조인 - Azure Active Directory
description: 하이브리드 Azure Active Directory 가입 디바이스를 구성하는 방법에 대해 알아봅니다.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 152ff52ce52b573d7f24cbb2fafc944b1794f6d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80129251"
---
# <a name="how-to-plan-your-hybrid-azure-active-directory-join-implementation"></a>방법: 하이브리드 Azure Active Directory 조인 구현 계획

사용자와 마찬가지로, 디바이스는 보호가 필요한 또 다른 핵심 ID이며, 언제 어디서나 디바이스를 사용하여 리소스를 보호해야 합니다. 다음 방법 중 하나를 사용하여 디바이스 ID를 Azure AD로 가져와서 관리하면 이 목표를 달성할 수 있습니다.

- Azure AD 조인
- 하이브리드 Azure AD 조인
- Azure AD 등록

Azure AD에 디바이스를 가져오면 클라우드와 온-프레미스 리소스에서 SSO(Single Sign-On)를 통해 사용자의 생산성을 극대화할 수 있습니다. 동시에 [조건부](../active-directory-conditional-access-azure-portal.md)액세스를 통해 클라우드 및 온-프레미스 리소스에 대한 액세스를 보호할 수 있습니다.

온-프레미스 AD(Active Directory) 환경이 있고 AD 도메인에 가입한 컴퓨터를 Azure AD에 조인하려는 경우 하이브리드 Azure AD 조인을 수행하여 이 작업을 수행할 수 있습니다. 이 문서에서는 사용자 환경에서 하이브리드 Azure AD 조인을 구현하는 데 관련된 단계를 제공합니다. 

## <a name="prerequisites"></a>사전 요구 사항

이 문서에서는 [Azure Active Directory의 장치 ID 관리 소개에](../device-management-introduction.md)대해 잘 알고 있다고 가정합니다.

> [!NOTE]
> Windows 10 하이브리드 Azure AD 조인에 필요한 최소 도메인 컨트롤러 버전은 Windows Server 2008 R2입니다.

## <a name="plan-your-implementation"></a>구현 계획

하이브리드 Azure AD 구현을 계획하려면 다음 사항을 숙지해야 합니다.

|   |   |
| --- | --- |
| ![확인][1] | 지원되는 디바이스 검토 |
| ![확인][1] | 알아야 할 사항 검토 |
| ![확인][1] | 하이브리드 Azure AD 조인의 제어된 유효성 검사 검토 |
| ![확인][1] | ID 인프라에 따라 시나리오 선택 |
| ![확인][1] | 하이브리드 Azure AD 조인에 대한 온-프레미스 AD UPN 지원 검토 |

## <a name="review-supported-devices"></a>지원되는 디바이스 검토

하이브리드 Azure AD 조인은 광범위한 Windows 디바이스를 지원합니다. 이전 버전의 Windows를 실행하는 디바이스에 대한 구성에는 추가 또는 다른 단계가 필요하므로 지원되는 디바이스는 두 가지 범주로 그룹화됩니다.

### <a name="windows-current-devices"></a>Windows 현재 디바이스

- 윈도우 10
- Windows Server 2016
  - **참고**: Azure National 클라우드 고객은 버전 1809가 필요합니다.
- Windows Server 2019

Windows 데스크톱 운영 체제를 실행하는 장치의 경우 지원되는 버전은 이 문서에 나열되어 있습니다 [Windows 10 릴리스 정보](/windows/release-information/). 모범 사례로, 마이크로소프트는 윈도 즈의 최신 버전으로 업그레이드 하는 것이 좋습니다 10.

### <a name="windows-down-level-devices"></a>Windows 하위 수준 디바이스

- Windows 8.1
- Windows 7 지원은 2020년 1월 14일에 종료되었습니다. 자세한 내용은 [Windows 7 지원이 종료되었음을](https://support.microsoft.com/en-us/help/4057281/windows-7-support-ended-on-january-14-2020)참조하십시오.
- Windows Server 2012 R2
- Windows Server 2012
- 윈도우 서버 2008 R2. Windows Server 2008 및 2008 R2에 대한 지원 정보는 [Windows Server 2008 지원 종료를](https://www.microsoft.com/cloud-platform/windows-server-2008)참조하십시오.

첫 번째 계획 단계에서는 사용자 환경을 검토하고 Windows 하위 수준 디바이스를 지원해야 하는지 여부를 결정해야 합니다.

## <a name="review-things-you-should-know"></a>알아야 할 사항 검토

### <a name="unsupported-scenarios"></a>지원되지 않는 시나리오
- 하이브리드 Azure AD 조인은 환경이 ID 데이터를 두 개 이상의 Azure AD 테넌트에 동기화하는 단일 AD 포리스트로 구성된 경우 현재 지원되지 않습니다.

- DC(도메인 컨트롤러) 역할을 실행하는 Windows 서버에서는 하이브리드 Azure AD 조인이 지원되지 않습니다.

- 자격 증명 로밍 또는 사용자 프로필 로밍 또는 필수 프로필을 사용할 때 하이브리드 Azure AD 조인은 Windows 하위 수준 장치에서 지원되지 않습니다.

- 서버 코어 OS는 모든 유형의 장치 등록을 지원하지 않습니다.

### <a name="os-imaging-considerations"></a>OS 이미징 고려 사항
- 시스템 준비 도구(Sysprep)에 의존하고 있고 설치를 위해 **Windows 10 1809 이전** 이미지를 사용하는 경우 해당 이미지가 하이브리드 Azure AD 조인으로 Azure AD에 이미 등록된 장치가 아닌지 확인합니다.

- 추가 VM을 만들기 위해 VM(가상 컴퓨터) 스냅숏에 의존하는 경우 스냅숏이 하이브리드 Azure AD 조인으로 Azure AD에 이미 등록된 VM이 아닌지 확인합니다.

- 통합 쓰기 [필터](/windows-hardware/customize/enterprise/unified-write-filter) 및 재부팅 시 디스크에 대한 변경 내용을 지우는 유사한 기술을 사용하는 경우 장치가 하이브리드 Azure AD조된 후에 적용되어야 합니다. 하이브리드 Azure AD 조인을 완료하기 전에 이러한 기술을 사용하도록 설정하면 모든 재부팅시 장치가 가입되지 않습니다.

### <a name="handling-devices-with-azure-ad-registered-state"></a>Azure AD 등록 된 상태로 장치 처리
Windows 10 도메인에 가입한 장치가 테넌트에 [등록된 Azure AD인](overview.md#getting-devices-in-azure-ad) 경우 하이브리드 Azure AD 조인 및 Azure AD 등록 장치의 이중 상태가 될 수 있습니다. 이 시나리오를 자동으로 해결하려면 Windows 10 1803(KB4489894 적용) 이상으로 업그레이드하는 것이 좋습니다. 1803 이전 릴리스에서는 하이브리드 Azure AD 조인을 활성화하기 전에 Azure AD 등록된 상태를 수동으로 제거해야 합니다. 1803년 이상 릴리스에서는 이 이중 상태를 피하기 위해 다음과 같은 변경 사항이 변경되었습니다.

- 장치에 하이브리드 Azure AD가 <i>조인되고 동일한 사용자가 로그인한 후</i>사용자에 대한 기존 Azure AD 등록 상태가 자동으로 제거됩니다. 예를 들어 사용자 A가 장치에 Azure AD 등록 상태가 있는 경우 사용자 A가 장치에 로그인할 때만 사용자 A의 이중 상태가 정리됩니다. 동일한 장치에 여러 사용자가 있는 경우 해당 사용자가 로그인할 때 이중 상태가 개별적으로 정리됩니다.
- 이 레지스트리 키를 추가하여 도메인에 가입한 장치가 Azure AD로 등록되지 않도록 할 수 있습니다 .
- Windows 10 1803에서 비즈니스용 Windows Hello가 구성된 경우 사용자는 이중 상태가 정리된 후 비즈니스용 Windows Hello를 다시 설정해야 합니다. 이 문제는 KB4512509로 해결되었습니다.

> [!NOTE]
> Intune에서 관리하는 경우 Azure AD 등록 장치가 자동으로 제거되지 않습니다.

### <a name="additional-considerations"></a>기타 고려 사항
- 환경에서 가상 데스크톱 인프라(VDI)를 사용하는 경우 [장치 ID 및 데스크톱 가상화를](/azure/active-directory/devices/howto-device-identity-virtual-desktop-infrastructure)참조하십시오.

- 하이브리드 Azure AD 조인은 FIPS 호환 TPM 2.0에 대해 지원되며 TPM 1.2에는 지원되지 않습니다. 장치에 FIPS 호환 TPM 1.2가 있는 경우 하이브리드 Azure AD 조인을 진행하기 전에 장치를 비활성화해야 합니다. Microsoft는 TPM 제조업체에 종속되어 있으므로 TPM에 대한 FIPS 모드를 사용하지 않도록 설정하는 도구를 제공하지 않습니다. 지원을 받으시면 하드웨어 OEM에 문의하십시오. 

- Windows 10 1903 릴리스에서 시작하여 TPM 1.2는 하이브리드 Azure AD 조인과 함께 사용되지 않으며 이러한 TPM이 있는 장치는 TPM이 없는 것처럼 간주됩니다.

## <a name="review-controlled-validation-of-hybrid-azure-ad-join"></a>하이브리드 Azure AD 조인의 제어된 유효성 검사 검토

모든 필수 구성 조건이 준비되면 Windows 장치는 Azure AD 테넌트의 장치로 자동으로 등록됩니다. Azure AD에서 이러한 장치 ID의 상태를 하이브리드 Azure AD 조인이라고 합니다. 이 문서에서 다루는 개념에 대한 자세한 내용은 [Azure Active Directory의 장치 ID 관리 소개](overview.md)문서에서 확인할 수 있습니다.

조직은 전체 조직에서 한 번에 활성화하기 전에 하이브리드 Azure AD 조인에 대한 제어된 유효성 검사를 수행할 수 있습니다. [하이브리드 Azure AD 조인의](hybrid-azuread-join-control.md) 아티클 제어 유효성 검사를 검토하여 이를 수행하는 방법을 이해합니다.

## <a name="select-your-scenario-based-on-your-identity-infrastructure"></a>ID 인프라에 따라 시나리오 선택

하이브리드 Azure AD 조인은 UPN이 라우팅 가능한지 여부와 관계없이 관리되는 환경과 페더레이션된 환경 모두에서 작동합니다. 지원되는 시나리오에 대한 테이블은 페이지 하단을 참조하십시오.  

### <a name="managed-environment"></a>관리 환경

관리형 환경은 [Seamless Single Sign On](/azure/active-directory/hybrid/how-to-connect-sso)을 사용하여 [PHS(암호 해시 동기화)](/azure/active-directory/hybrid/whatis-phs) 또는 [PTA(통과 인증)](/azure/active-directory/hybrid/how-to-connect-pta)를 통해 배포할 수 있습니다.

이러한 시나리오는 인증용 페더레이션 서버를 구성할 필요가 없습니다.

### <a name="federated-environment"></a>페더레이션 환경

페더레이션 환경은 다음 요구 사항을 지원하는 ID 공급자가 있어야 합니다. AD FS(Active Directory Federation Services)를 사용하는 페더레이션된 환경을 사용하는 경우에는 아래 요구 사항이 이미 지원됩니다.

- **WIAORMULTIAUTHN 클레임:** 이 클레임은 Windows 하위 수준 장치에 대해 하이브리드 Azure AD 조인을 수행하는 데 필요합니다.
- **WS-트러스트 프로토콜:** 이 프로토콜은 Azure AD를 사용하여 Windows 현재 하이브리드 Azure AD 조인 장치를 인증하는 데 필요합니다. AD FS를 사용하는 경우 다음 WS-Trust 엔드포인트를 사용하도록 설정해야 합니다. `/adfs/services/trust/2005/windowstransport`  
`/adfs/services/trust/13/windowstransport`  
  `/adfs/services/trust/2005/usernamemixed` 
  `/adfs/services/trust/13/usernamemixed`
  `/adfs/services/trust/2005/certificatemixed` 
  `/adfs/services/trust/13/certificatemixed` 

> [!WARNING] 
> **adfs/services/trust/2005/windowstransport** 또는 **adfs/services/trust/13/windowstransport**는 모두 인트라넷 연결 엔드포인트로만 사용하도록 설정해야 하며 웹 애플리케이션 프록시를 통해 엑스트라넷 연결 엔드포인트로 노출되어서는 안됩니다. WS-Trust Windows 끝점을 사용하지 않도록 설정하는 방법에 대한 자세한 내용은 [프록시에서 WS-Trust Windows 끝점 사용 안 함](/windows-server/identity/ad-fs/deployment/best-practices-securing-ad-fs#disable-ws-trust-windows-endpoints-on-the-proxy-ie-from-extranet)참조 **서비스** > 끝점에서 AD FS 관리 콘솔을 통해 활성화된**끝점을**확인할 수 있습니다.

> [!NOTE]
> Azure AD는 관리형 도메인에서 스마트 카드나 인증서를 지원하지 않습니다.

버전 1.1.819.0부터 Azure AD Connect는 하이브리드 Azure AD 조인을 구성하는 마법사를 제공합니다. 마법사를 사용하면 구성 프로세스를 크게 간소화할 수 있습니다. 필요한 버전의 Azure AD Connect를 설치할 수 없는 경우 [디바이스 등록을 수동으로 구성하는 방법](hybrid-azuread-join-manual.md)을 참조하세요. 

ID 인프라와 일치하는 시나리오를 기반으로 다음을 참조하십시오.

- [페더레이션 환경에 대한 하이브리드 Azure Active Directory 조인 구성](hybrid-azuread-join-federated-domains.md)
- [관리되는 환경에 대한 하이브리드 Azure Active Directory 조인 구성](hybrid-azuread-join-managed-domains.md)

## <a name="review-on-premises-ad-users-upn-support-for-hybrid-azure-ad-join"></a>하이브리드 Azure AD 조인에 대한 온-프레미스 AD 사용자 UPN 지원 검토

경우에 따라 온-프레미스 AD 사용자 UPN Azure AD UPN과 다를 수 있습니다. 그러한 경우 Windows 10 하이브리드 Azure AD 조인은 [인증 방법](/azure/security/fundamentals/choose-ad-authn), 도메인 유형 및 Windows 10 버전을 기반으로 온-프레미스 AD UPN에 대한 제한된 지원을 제공합니다. 사용자 환경에 있을 수 있는 온-프레미스 AD UPN에는 두 가지 유형이 있습니다.

- 라우팅 가능한 사용자 UPN: 라우팅 가능한 UPN에는 도메인 등록 기관에 등록된 유효한 인증된 도메인이 있습니다. 예를 들어 contoso.com이 Azure AD의 기본 도메인인 경우 contoso.org는 Contoso에서 소유하고 [Azure AD에서 확인](/azure/active-directory/fundamentals/add-custom-domain)된 기본 도메인입니다.
- 라우팅이 불가능한 사용자 UPN: 라우팅할 수 없는 UPN에는 확인된 도메인이 없습니다. 조직의 사설망 내에서만 적용됩니다. 예를 들어 contoso.com이 Azure AD의 기본 도메인인 경우 contoso.com은 온-프레미스 AD의 기본 도에미인이지만 인터넷에서 확인할 수 없는 도메인이며 Consoso의 네트워크 내에서만 사용됩니다.

> [!NOTE]
> 이 섹션의 정보는 온-프레미스 사용자 UPN에만 적용됩니다. 온-프레미스 컴퓨터 도메인 접미사(예: computer1.contoso.local)에는 적용되지 않습니다.

아래 표에서는 Windows 10 하이브리드 Azure AD 조인에서 이러한 온-프레미스 AD UPN에 대한 지원을 자세히 설명합니다.

| 온-프레미스 AD UPN 유형 | 도메인 유형 | Windows 10 버전 | 설명 |
| ----- | ----- | ----- | ----- |
| 라우팅 가능 | 페더레이션 | 1703 릴리스 | 일반 공급 |
| 라우팅 불가능 | 페더레이션 | 1803 릴리스 | 일반 공급 |
| 라우팅 가능 | 관리 | 1803 릴리스 | 일반적으로 사용 가능한 Windows 잠금 화면에서 Azure AD SSPR은 지원되지 않습니다. |
| 라우팅 불가능 | 관리 | 지원되지 않음 | |

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [페더레이션 된 환경에](hybrid-azuread-join-federated-domains.md)
> 대 한 하이브리드 Azure Active Directory 조인 구성[관리 되는 환경에 대 한 하이브리드 Azure Active Directory 조인 구성](hybrid-azuread-join-managed-domains.md)

<!--Image references-->
[1]: ./media/hybrid-azuread-join-plan/12.png
