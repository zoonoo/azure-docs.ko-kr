---
title: "작업 공간에서 Windows 10 장치 사용 | Microsoft Docs"
description: "사용자 및 IT를 위한 기능의 스냅숏을 제공하며 Windows 10을 사용하여 엔터프라이즈에서 장치를 프로비전 및 사용하는 다양한 방법을 비교합니다."
services: active-directory
documentationcenter: 
author: femila
manager: swadhwa
editor: 
tags: azure-classic-portal
ms.assetid: 94ccc8fd-b17b-4fda-8d56-9d87aa37a9f9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/17/2016
ms.author: femila
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 388ca47c7ec21723906125227ed8250e03606e17


---
# <a name="using-windows-10-devices-in-your-workplace"></a>작업 공간에서 Windows 10 장치 사용
적용 대상: Windows 10 PC

Windows 10은 조직을 위한 세 가지 모델을 제공하여 사용자가 안전하고 편리한 방식으로 작업 리소스에 액세스할 수 있도록 합니다.

* **Azure Active Directory 조인** (Azure AD 조인): 주로 클라우드에서 제공되는 Office 365와 같은 리소스에 액세스하는 작업자용. Azure AD 조인은 Windows 10의 새로운 환경을 프로비전하는 셀프 서비스 작업입니다.
* **도메인 가입**: 온-프레미스 앱 및 리소스에 투자하는 조직용. 도메인 가입은 Azure AD에 연결된 경우 Windows 10에서 향상된 환경을 제공합니다.
* **새롭게 단순화된 BYOD 환경**: Windows에 회사 또는 학교 계정을 추가하고 개인 장치에서 리소스에 쉽게 액세스하려는 사용자용.

다음 표는 사용자 및 IT 관리자가 얻는 기능의 스냅샷을 나타내며 Windows 10을 사용하여 엔터프라이즈에서 장치를 프로비전 및 사용하는 다양한 방법을 비교합니다.

|  | 도메인 가입 | Azure AD 조인 | 개인 장치 |
| --- | --- | --- | --- |
| 회사 또는 학교 계정을 위한 Windows 장치 로그인 |예 |예 |아니요 |
| Office 365 및 Azure AD 앱에 대한 사용자 SSO(Single-Sign-On). SSO는 조직 리소스에 액세스하기 위해 한 번만 로그인하는 기능입니다. |예 |예 |예 |
| Kerberos/NTLM 앱에 대한 사용자 SSO |예 |제한 |예, VPN을 통해 |
| Microsoft Passport 및 Windows Hello를 사용하는 회사 또는 학교 계정에 대한 강력한 권한 부여 및 손쉬운 로그인 |예 |예 |예 |
| 회사 또는 학교 계정을 사용한 엔터프라이즈 Windows 저장소에 액세스(Microsoft 계정 없음) |예 |예 |예 |
| 회사 또는 학교 계정을 사용하여 장치 간 사용자 설정을 로밍하는 엔터프라이즈 규정 준수 |예 |예 |예 |
| 조직 앱에서 조직 장치 정책을 준수하는 장치로 액세스를 제한하는 기능 |예 |예 |예 |
| "어디에서나 작업"에 대한 장치의 사용자 셀프 서비스 프로비전 |아니요 |예 |예 |
| 장치를 관리하는 기능 |예, GP/SCCM을 통해 |예 |예 |

## <a name="use-work-owned-devices-with-azure-ad-join-and-domain-join-in-windows-10"></a>Windows 10에서 Azure AD 조인 및 도메인 가입된 회사 소유의 장치 사용
Windows 10은 회사 리소스에 액세스하기 위해 회사 소유의 장치에 대한 두 가지 방법을 제공합니다.

* Azure AD 조인
* 도메인 가입
  
  모두 조직의 필요 및 요구 사항에 따라 유효한 옵션일 수 있습니다. 경우에 따라 조직이 배포하는 두 방법을 모두 설정하면 장점을 활용할 수 있습니다.

## <a name="when-to-use-azure-active-directory-join"></a>Azure Active Directory 조인을 사용하는 경우
Azure AD 조인은 Windows 10의 환경을 프로비전하는 새로운 셀프 서비스 작업입니다.  주로 클라우드에서 제공되는 Office 365와 같은 작업 리소스에 액세스하는 작업자를 대상으로 지정합니다. 엔터프라이즈를 위해 컴퓨터, 태블릿 및 휴대폰을 구성하는 간단한 방법입니다. 장치는 Windows 플랫폼 전반에 걸쳐 일관된 제어를 사용하여 모바일 장치 관리를 통해 관리됩니다.

**다음의 이유로 Azure AD 조인 사용**:

* 언제든 작업자에 장치의 셀프 서비스 프로비전을 사용하려고 합니다.
* 사용자에게 태블릿 및 휴대폰과 같은 회사 소유의 모바일 장치를 제공합니다.
* 비정규직 근로자, 계약자 또는 학생과 같은 사용자 집합을 Active Directory 대신 Azure AD에서 관리하려고 합니다.
* 제한된 온-프레미스 인프라를 사용하는 원격 지사에서 작업자에게 조인 기능을 제공하려고 합니다.
* 온-프레미스 Active Directory가 없습니다.

일부 조직에서는 특히 클라우드로 해당 리소스의 전체 또는 대부분을 마이그레이션할 때 Azure AD 조인을 회사 소유의 장치를 배포하는 기본 방법으로 사용합니다. 또한 Active Directory 및 Azure AD를 모두 사용하는 하이브리드 조직은 사용자 또는 부서에 따라 하나의 메서드를 배포하도록 선택할 수 있습니다.

예를 들어, 학군 및 대학교는 직원은 Active Directory에서, 학생은 Azure AD에서 관리할 수 있습니다. 일부 회사에서는 Azure AD에서 원격 사무실 또는 판매 부서를 관리하려 할 수 있습니다. Azure AD 조인 및 도메인 가입 메서드는 모두 하이브리드 조직 내에서 사용할 수 있습니다. Azure AD 조인은 작업 환경에서 장치를 배포하는 도메인 가입을 훌륭하게 보완할 수 있습니다.

**엔터프라이즈 리소스에 대한 액세스 대부분이 클라우드를 통하는 경우 조직은 다음과 같은 추가 혜택을 얻을 수 있습니다**.

* 온-프레미스 ID 인프라에 대한 종속성을 제거할 수 있습니다.
* 이미징 솔루션에서 벗어나 셀프 서비스를 구성하여 장치 배포 모델을 간소화할 수 있습니다.
* 모바일 장치 관리를 사용하여 여러 플랫폼에서 모든 장치를 관리할 수 있습니다.

Azure AD 조인에 대한 자세한 내용은 [Azure Active Directory 조인을 통해 클라우드 기능을 Windows 10 장치로 확장](active-directory-azureadjoin-overview.md)을 참조하세요.

## <a name="when-to-use-domain-join-or-keep-using-it"></a>도메인 가입을 사용하는 경우(또는 계속 사용)
지난 15년 동안 많은 조직에서는 작업 장치를 연결하는 데 도메인 가입을 사용했습니다. 사용자는 Active Directory 회사 또는 학교 계정으로 자신의 장치에 로그인할 수 있습니다. 도메인 가입을 통해도 IT로 이러한 장치를 완벽하게 중앙에서 관리할 수 있습니다. 조직에서는 일반적으로 장치를 프로비전하는 이미징 메서드에 의존하고 이를 관리하는 데 SCCM(System Center Configuration Manager) 또는 GP(그룹 정책)를 사용합니다.

**다음의 이유로 인해 엔터프라이즈에서 도메인 가입(또는 계속 사용)을 사용합니다**.

* NTLM/Kerberos를 사용하는 이러한 장치에 배포된 Win32 앱이 있습니다.
* 장치를 관리하는 데 GP 또는 SCCM/DCM이 필요합니다.
* 직원에 대해 장치를 구성하도록 이미징 솔루션을 계속 사용하려고 합니다.

**또한 Windows 10에서 도메인에 가입하면 Azure AD에 연결한 경우 다음과 같은 이점을 제공합니다.**

* Microsoft Passport 및 Windows Hello를 사용하는 회사 또는 학교 계정에 대한 강력한 장치 바인딩된 인증 및 손쉬운 로그인
* 회사 또는 학교 계정을 사용하는 장치에 대한 엔터프라이즈 Windows 스토어에 액세스(Microsoft 계정 필요 없음)
* 회사 또는 학교 계정을 사용하는 장치 간 사용자 설정의 엔터프라이즈 규격 로밍(Microsoft 계정 필요 없음)
* 조직 앱에서 조직 장치 정책을 준수하는 장치로 액세스를 제한하는 기능

Azure AD 조인에 대한 자세한 내용은 [Azure Active Directory 조인을 통해 클라우드 기능을 Windows 10 장치로 확장](active-directory-azureadjoin-overview.md)을 참조하세요.

## <a name="enable-joining-of-personally-owned-devices-for-work-or-school"></a>회사 또는 학교용으로 개인 소유한 장치의 가입 사용
엔터프라이즈에서 BYOD를 지원하려면 Windows 10은 사용자가 해당 컴퓨터, 태블릿 또는 휴대폰에 회사 또는 학교 계정을 추가하는 기능을 제공합니다. 사용자가 회사 또는 학교 계정을 추가하면 장치가 Azure AD에 등록되고 필요에 따라 조직이 구성한 모바일 장치 관리 시스템에 등록됩니다. 디렉터리는 이러한 장치를 '등록된' vs  ‘Azure AD 조인된’으로 표시합니다. IT 관리자는 이 정보를 기반으로 다른 정책을 적용할 수 있으며 원하는 경우 회사 소유의 장치 보다 개인 소유의 장치에 맑은 터치를 제공합니다.

사용자가 회사 또는 학교 계정을 개인 장치에 매우 간편하게 추가할 수 있습니다. 이 작업은 처음으로 작업 응용 프로그램에 액세스할 때 수행하거나 설정 메뉴를 통해 수동으로 할 수 있습니다. 이 계정은 조직의 리소스에 SSO를 제공합니다.

Azure AD 조인하는 방법에 대한 자세한 내용은 [Windows 10 환경에 대한 Azure AD에 도메인 가입된 장치 연결](active-directory-azureadjoin-devices-group-policy.md)을 참조하세요.

## <a name="enable-domain-join-or-azure-ad-join"></a>도메인 가입 또는 Azure AD 조인 사용
앞에서 설명한 모든 방법(도메인 가입, Azure AD 조인 및 회사 또는 학교 계정 추가)은 Windows 10 사용자 환경에 진입점이 있습니다. 그러나 모든 환경이 작동하기 위해서는 IT 관리자가 인프라에서 기능을 사용하도록 설정해야 합니다.

## <a name="requirements-for-deploying-azure-ad-join"></a>Azure AD 조인 배포를 위한 요구 사항
모든 사용자 집합에 대해 Azure AD 조인을 배포하려면 다음이 필요합니다.

* Azure AD 구독
* 더 많은 기능이 필요한 경우 모바일 장치 관리 자동 등록과 같은 Azure AD Premium 구독
* 모바일 장치 관리(예: Microsoft Intune 구독, Office 365용 모바일 장치 관리 또는 Azure AD와 통합되는 모든 파트너 모바일 장치 관리 공급업체) (자세한 내용은 이 문서의 끝부분에 있는 [FAQ 섹션](#frequently-asked-questions) 참조).

시설이 하이브리드인 경우 Azure AD에 온-프레미스 디렉터리를 확장하는 Azure AD Connect를 배포하는 것이 좋습니다.

## <a name="requirements-for-using-domain-join-with-azure-ad"></a>Azure AD에 도메인 가입을 사용하기 위한 요구 사항
도메인 가입은 항상 포함되기만 하면 계속 작동합니다. 하지만 Azure AD 이점을 가져오려면 다음이 필요합니다.

* Azure AD 구독
* Azure AD에 온-프레미스 디렉터리를 확장하는 Azure AD Connect의 배포
* 도메인 가입된 장치가 Azure AD에 대한 조건부 액세스를 포함하도록 허용하는 정책
* 일부 장치에 대한 액세스를 제한하려는 경우 "도메인 가입된" 장치에 대한 액세스를 허용하는 정책
* Technical Preview용 System Center Configuration Manager 버전 1509 - 호환 장치를 필요로 하는 규칙을 사용하도록 설정 (TechNet 설명서 및 블로그 게시물 참조).

Windows 10에서 도메인 가입에 대한 자세한 내용은 [Windows 10 환경용 Azure AD에 도메인 가입된 장치 연결](active-directory-azureadjoin-devices-group-policy.md)을 참조하세요.

## <a name="requirements-for-using-byod-and-add-a-work-or-school-account"></a>BYOD 및 "회사 또는 학교 계정 추가" 사용을 위한 요구 사항
회사 또는 학교 계정에 "BYOD(Bring Your Own Device)"를 사용하도록 설정하려면 다음이 필요합니다.

* Azure AD 구독
* 더 많은 기능이 필요한 경우 모바일 장치 관리 자동 등록과 같은 Azure AD Premium 구독

## <a name="requirements-for-using-microsoft-passport"></a>Microsoft Passport 사용을 위한 요구 사항
Microsoft Passport를 사용하려면 다음이 필요합니다.

* Microsoft Passport를 사용하는 인증서 기반 인증 지원을 위한 PKI(공개 키 인프라)
* Azure AD 조인 및 회사 또는 학교 계정에 Microsoft Passport를 사용하는 인증서 기반 인증 지원을 위한 Intune 구독
* 도메인 가입에 Microsoft Passport를 사용하는 인증서 기반 인증 지원을 위한 Technical Preview용 System Center Configuration Manager 버전 1509(TechNet 설명서 및 블로그 게시물 참조).
* 조직에서 Microsoft Passport를 사용하도록 설정하기 위한 정책

PKI를 사용하는 대안으로 다음을 수행하여 키 기반 Microsoft Passport를 사용할 수 있습니다.

* Windows Server 2016 "프로덕션 미리 보기 1" DC를 배포합니다(도메인 또는 포리스트 기능 수준이 필요하지 않습니다. 각 Active Directory 사이트를 제공하는 중복성에 대한 여러 DC).
* 정책을 설정하여 조직에서 Microsoft Passport를 사용합니다.

Windows 10에서 Microsoft Passport 및 Windows Hello에 대한 자세한 내용은 <link-to-MS-Passport-and-Windows-Hello-document>을(를) 참조하세요.

## <a name="frequently-asked-questions"></a>질문과 대답
### <a name="which-partner-mobile-device-management-products-integrate-with-azure-ad"></a>Azure AD와 통합되는 파트너 모바일 장치 관리 제품은 무엇인가요?
다음 공급 업체 제품은 통합된 등록 및 Windows 10에서 조건부 액세스의 경우 Azure AD와 통합됩니다.

* AirWatch by VMware
* Citrix Xenmobile
* Lightspeed Mobile Manager
* SOTI 온-프레미스 모바일 장치 관리
* MobileIron

### <a name="what-about-workplace-join-in-windows-10"></a>Windows 10에서 작업 공간 가입은 어떠나요?
Windows 8.1에서 작업 공간 가입은 BYOD를 활성화하는 데 사용되었습니다. Windows 10에서 BYOD는 이 문서의 앞부분에서 설명한 대로 "회사 또는 학교 계정 추가"를 통해 활성화됩니다. Azure AD와 해당 모바일 장치 관리를 통합하지 않는 조직의 경우 사용자는 **설정** > **계정** > **액세스 작업**을 통해 수동으로 관리에 장치를 등록할 수 있습니다.

### <a name="can-users-connect-their-microsoft-account-to-their-domain-account-in-windows-10"></a>사용자가 Windows 10의 해당 도메인 계정에 해당 Microsoft 계정을 연결할 수 있나요?
10 Windows에서는 안됩니다. Windows 8.1에서 도메인에 가입된 장치의 사용자는 해당 Microsoft 계정(예: Hotmail, Live, Outlook, Xbox 등)을 해당 도메인 계정에 "연결"하여 Live 서비스에 대한 SSO, Windows 스토어의 사용 및 장치 간에 사용자 설정 로밍과 같은 특정 환경을 사용하도록 설정합니다. Windows 10에서 Microsoft 계정 "연결" 기능은 사용이 중지되었습니다. 사용자는 하나 이상의 Microsoft 계정을 추가 계정으로 추가하여 SSO를 Windows 스토어와 같은 소비자 서비스에 사용하도록 설정합니다. **설정** > **계정** > **계정**에서 수행됩니다.

연결된 해당 Microsoft 계정을 보유했던 Windows 8.1 도메인 가입 장치에서 업그레이드 중인 사용자는 자동으로 해당 연결된 Microsoft 계정을 사용하는 추가 계정 목록에 추가합니다.

## <a name="additional-information"></a>추가 정보
* [엔터프라이즈를 위한 Windows 10: 작업에 장치를 사용하는 방법](active-directory-azureadjoin-windows10-devices-overview.md)
* [Azure Active Directory 조인을 통해 클라우드 기능을 Windows 10 장치로 확장](active-directory-azureadjoin-user-upgrade.md)
* [Azure AD 조인에 대한 사용 시나리오에 대해 알아보기](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Windows 10 환경용 Azure AD에 도메인 가입된 장치 연결](active-directory-azureadjoin-devices-group-policy.md)
* [Azure AD 조인 설정](active-directory-azureadjoin-setup.md)




<!--HONumber=Nov16_HO3-->


