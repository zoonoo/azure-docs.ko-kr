---
title: Azure AD(Azure Active Directory)에서 하이브리드 Azure Active Directory 조인 구현을 계획하는 방법 | Microsoft Docs
description: 하이브리드 Azure Active Directory 가입 디바이스를 구성하는 방법에 대해 알아봅니다.
services: active-directory
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: daveba
editor: ''
ms.assetid: 54e1b01b-03ee-4c46-bcf0-e01affc0419d
ms.service: active-directory
ms.subservice: devices
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/10/2019
ms.author: joflore
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 64dd8067654246f7c9a077d027c068df820f439d
ms.sourcegitcommit: 6932af4f4222786476fdf62e1e0bf09295d723a1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/05/2019
ms.locfileid: "66688707"
---
# <a name="how-to-plan-your-hybrid-azure-active-directory-join-implementation"></a>방법: 하이브리드 Azure Active Directory 조인 구현 계획

사용자에 게 비슷한 방법으로 장치를 다른 핵심 id 보호를 모든 위치와 언제 든 지 리소스를 보호 하는 데 사용 하려는 경우 이 목표를 가져오고 다음 방법 중 하나를 사용 하 여 Azure AD에서 장치 id를 관리 하 여 수행할 수 있습니다.

- Azure AD 조인
- 하이브리드 Azure AD 조인
- Azure AD 등록

Azure AD에 디바이스를 가져오면 클라우드와 온-프레미스 리소스에서 SSO(Single Sign-On)를 통해 사용자의 생산성을 극대화할 수 있습니다. 이와 동시에 [조건부 액세스](../active-directory-conditional-access-azure-portal.md)를 사용하여 클라우드 및 온-프레미스 리소스에 대한 액세스를 보호할 수 있습니다.

온-프레미스 Active Directory (AD) 환경이 있고 AD 도메인에 가입 된 컴퓨터를 Azure AD에 조인 하려는 경우 하이브리드 Azure AD 조인을 수행 하 여이 수행할 수 있습니다. 이 문서에서는 사용자 환경에서 하이브리드 Azure AD 조인을 구현하는 데 관련된 단계를 제공합니다. 

## <a name="prerequisites"></a>필수 조건

이 문서에 잘 알고 있다고 가정 합니다 [Azure Active Directory에서 장치 id 관리 소개](../device-management-introduction.md)합니다.

> [!NOTE]
> 최소는 하이브리드 Azure AD 가입 Windows 10은 Windows Server 2008 R2에 대 한 기능 도메인 및 포리스트 기능 수준이 필요 합니다.

## <a name="plan-your-implementation"></a>구현 계획

하이브리드 Azure AD 구현을 계획하려면 다음 사항을 숙지해야 합니다.

|   |   |
| --- | --- |
| ![확인][1] | 지원되는 디바이스 검토 |
| ![확인][1] | 알아야 할 사항 검토 |
| ![확인][1] | 하이브리드 Azure AD join의 제어 된 유효성 검사를 검토 합니다. |
| ![확인][1] | Id 인프라가 기반 시나리오를 선택 합니다. |
| ![확인][1] | 검토 온-프레미스 AD UPN 지원 하이브리드 Azure AD 조인 |

## <a name="review-supported-devices"></a>지원되는 디바이스 검토

하이브리드 Azure AD 조인은 광범위한 Windows 디바이스를 지원합니다. 이전 버전의 Windows를 실행하는 디바이스에 대한 구성에는 추가 또는 다른 단계가 필요하므로 지원되는 디바이스는 두 가지 범주로 그룹화됩니다.

### <a name="windows-current-devices"></a>Windows 현재 디바이스

- 윈도우 10
- Windows Server 2016
- Windows Server 2019

이 문서에 나열 된 지원 되는 버전의 Windows 데스크톱 운영 체제를 실행 하는 장치에 대 한 [Windows 10 릴리스 정보](https://docs.microsoft.com/windows/release-information/)합니다. 모범 사례로, Windows 10의 최신 버전으로 업그레이드 하는 것이 좋습니다.

### <a name="windows-down-level-devices"></a>Windows 하위 수준 디바이스

- Windows 8.1
- Windows 7. 지원에 대 한 내용은 Windows 7이이 문서를 검토 하십시오 [종료 되는 Windows 7에 대 한 지원](https://www.microsoft.com/en-us/windowsforbusiness/end-of-windows-7-support)
- Windows Server 2012 R2
- Windows Server 2012
- Windows Server 2008 R2

첫 번째 계획 단계에서는 사용자 환경을 검토하고 Windows 하위 수준 디바이스를 지원해야 하는지 여부를 결정해야 합니다.

## <a name="review-things-you-should-know"></a>알아야 할 사항 검토

하이브리드 Azure AD 조인 현재 지원 되지 않습니다 둘 이상의 Azure AD 테 넌 트 id 데이터를 동기화 하는 단일 AD 포리스트와 환경의 구성 된 경우.

하이브리드 Azure AD 조인 현재 지원 되지 않습니다 VDI (가상 데스크톱 인프라)를 사용 하는 경우.

하이브리드 Azure AD는 FIPS 규격 Tpm에 대 한 지원 되지 않습니다. FIPS 규격 장치의 경우, 하이브리드 Azure AD 조인을 사용 하 여 계속 진행 하기 전에 해제 해야 합니다. Microsoft은 TPM 제조업체에 따라 달라 집니다 이므로 Tpm에 대 한 FIPS 모드를 해제 하는 것에 대 한 모든 도구를 제공 하지 않습니다. OEM의 하드웨어가 지원에 문의 하세요.

도메인 컨트롤러 (DC) 역할을 실행 하는 Windows 서버에 대 한 하이브리드 Azure AD 조인은 지원 되지 않습니다.

자격 증명 로밍 사용자 프로필 로밍 또는 사용 하는 경우 하이브리드 Azure AD 가입 Windows 하위 수준 장치에서 지원 되지 않습니다.

시스템 준비 도구 (Sysprep)에 의존 하는 경우와 사용 중인 경우는 **pre-Windows 10 1809** 설치에 대 한 이미지, 하이브리드 Azure AD 조인으로 Azure AD를 사용 하 여 이미 등록 된 장치에서 해당 이미지 아닌지 확인 하십시오.

추가 Vm을 만드는 스냅숏으로 가상 머신 (VM)에 의존 하는 경우 하이브리드 Azure AD 조인으로 Azure AD를 사용 하 여 이미 등록 되어 있는 VM에서 해당 스냅숏 되지 있는지 확인 합니다.

Windows 10 도메인 조인 디바이스에서 이미 [Azure AD를 테넌트에 등록](https://docs.microsoft.com/azure/active-directory/devices/overview#azure-ad-registered-devices)한 경우 먼저 해당 상태를 제거한 후에 하이브리드 Azure AD 조인을 사용하도록 설정할 것을 적극 권장합니다. Windows 10 1809 릴리스에서 이 이중 상태를 방지하기 위해 다음과 같이 변경되었습니다.

- 디바이스가 하이브리드 Azure AD에 조인되면 기존의 Azure AD 등록됨 상태가 자동으로 제거됩니다.
- 이 레지스트리 키-HKLM\SOFTWARE\Policies\Microsoft\Windows\WorkplaceJoin, "BlockAADWorkplaceJoin"를 추가 하 여 등록 하는 Azure AD에서 도메인에 가입 된 장치를 방지할 수 있습니다 = dword: 00000001 합니다.
- 이 변경은 KB4489894 적용을 사용 하 여 Windows 10 1803 릴리스에 대 한 출시 되었습니다. 그러나 경우 Windows Hello for Business 구성 사용자는 다시 설정 Windows Hello 비즈니스에 대 한 이중 상태 후 정리 해야 합니다.


## <a name="review-controlled-validation-of-hybrid-azure-ad-join"></a>하이브리드 Azure AD join의 제어 된 유효성 검사를 검토 합니다.

사전 요구 사항이 적으면 서 모두 준비에서 되 면 Windows 장치를 Azure AD 테 넌 트에서 장치로 자동으로 등록 됩니다. Azure AD에서 이러한 장치 id의 상태를 하이브리드 Azure AD 조인 이라고 합니다. 이 문서에서 설명 된 개념에 대 한 자세한 문서에서 찾을 수 있습니다 [Azure Active Directory에서 장치 id 관리 소개](overview.md) 고 [에 하이브리드 Azure Active Directory 조인 계획 구현](hybrid-azuread-join-plan.md)합니다.

조직이 제어 유효성 검사를 하이브리드 Azure AD 조인 중 한 번에 전체 조직에서 사용 하기 전에 할 수 있습니다. 문서를 검토 [하이브리드 Azure AD 조인 유효성 검사 제어](hybrid-azuread-join-control.md) 작업을 수행 하는 방법을 알아야 합니다.


## <a name="select-your-scenario-based-on-your-identity-infrastructure"></a>Id 인프라가 기반 시나리오를 선택 합니다.

하이브리드 Azure AD 조인 환경 관리 및 페더레이션 모두 작동합니다.  

### <a name="managed-environment"></a>관리 환경

관리 되는 환경 수를 통해 배포할 [암호 해시 동기화 (PHS)](https://docs.microsoft.com/azure/active-directory/hybrid/whatis-phs) 또는 [전달를 통해 인증 PTA ()](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-pta) 사용 하 여 [Seamless Single Sign On](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sso)합니다.

이러한 시나리오는 인증용 페더레이션 서버를 구성할 필요가 없습니다.

### <a name="federated-environment"></a>페더레이션 환경

페더레이션된 환경에는 다음 요구 사항을 지 원하는 id 공급자로 있어야 합니다.

- **Ws-trust 프로토콜:** 이 프로토콜은 Windows를 인증 하는 데 필요한 현재 하이브리드 Azure AD는 Azure AD 사용 하 여 장치를 가입 합니다.
- **WIAORMULTIAUTHN 클레임:** 이 클레임은 Windows 하위 수준 장치에 대 한 하이브리드 Azure AD 조인을 수행 해야 합니다.

Active Directory Federation Services (AD FS)를 사용 하 여 페더레이션된 환경에 있는 경우 위의 요구 사항은 이미 지원 됩니다.

> [!NOTE]
> Azure AD는 관리형 도메인에서 스마트 카드나 인증서를 지원하지 않습니다.

버전 1.1.819.0부터 Azure AD Connect는 하이브리드 Azure AD 조인을 구성하는 마법사를 제공합니다. 마법사를 사용하면 구성 프로세스를 크게 간소화할 수 있습니다. 필요한 버전의 Azure AD Connect를 설치할 수 없는 경우 [디바이스 등록을 수동으로 구성하는 방법](https://docs.microsoft.com/azure/active-directory/devices/hybrid-azuread-join-manual)을 참조하세요. 

Id 인프라가 일치 하는 시나리오에 따른, 참조:

- [페더레이션된 환경에 대 한 하이브리드 Azure Active Directory 연결 구성](hybrid-azuread-join-federated-domains.md)
- [관리 되는 환경에 대 한 하이브리드 Azure Active Directory 조인 구성](hybrid-azuread-join-managed-domains.md)



## <a name="review-on-premises-ad-upn-support-for-hybrid-azure-ad-join"></a>검토 온-프레미스 하이브리드 Azure AD 조인에 대 한 AD UPN 지원

경우에 따라 온-프레미스 AD UPN이 Azure AD UPN과 다를 수 있습니다. 그러한 경우 Windows 10 하이브리드 Azure AD 조인은 [인증 방법](https://docs.microsoft.com/azure/security/azure-ad-choose-authn), 도메인 유형 및 Windows 10 버전을 기반으로 온-프레미스 AD UPN에 대한 제한된 지원을 제공합니다. 사용자 환경에 있을 수 있는 온-프레미스 AD UPN에는 두 가지 유형이 있습니다.

- 라우팅 가능 UPN: 라우팅 가능 UPN에는 도메인 등록 기관에 등록되어 유효한 것으로 확인된 도메인이 있습니다. 예를 들어 contoso.com이 Azure AD의 기본 도메인인 경우 contoso.org는 Contoso에서 소유하고 [Azure AD에서 확인](https://docs.microsoft.com/azure/active-directory/fundamentals/add-custom-domain)된 기본 도메인입니다.
- 라우팅 불가능 UPN: 라우팅 불가능 UPN에는 확인된 도메인이 없습니다. 조직의 사설망 내에서만 적용됩니다. 예를 들어 contoso.com이 Azure AD의 기본 도메인인 경우 contoso.com은 온-프레미스 AD의 기본 도에미인이지만 인터넷에서 확인할 수 없는 도메인이며 Consoso의 네트워크 내에서만 사용됩니다.

아래 표에서는 Windows 10 하이브리드 Azure AD 조인에서 이러한 온-프레미스 AD UPN에 대한 지원을 자세히 설명합니다.

| 온-프레미스 AD UPN 유형 | 도메인 유형 | Windows 10 버전 | 설명 |
| ----- | ----- | ----- | ----- |
| 라우팅 가능 | 페더레이션 | 1703 릴리스 | 일반 공급 |
| 라우팅 불가능 | 페더레이션 | 1803 릴리스 | 일반 공급 |
| 라우팅 가능 | 관리 | 지원되지 않음 | |
| 라우팅 불가능 | 관리 | 지원되지 않음 | |

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [페더레이션된 환경에 대 한 구성 하이브리드 Azure Active Directory 조인](hybrid-azuread-join-federated-domains.md)
> [관리 되는 환경에 대 한 하이브리드 Azure Active Directory 연결 구성](hybrid-azuread-join-managed-domains.md)

<!--Image references-->
[1]: ./media/hybrid-azuread-join-plan/12.png
