---
title: Azure ID 및 액세스 관리의 기본 사항 - Azure Active Directory | Microsoft Docs
description: Azure Active Directory Premium Edition에서 사용 가능한 고급 보호 기능 및 추가 도구에 대해 알아봅니다.
services: active-directory
author: eross-msft
manager: mtillman
ms.author: lizross
ms.service: active-directory
ms.component: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 09/13/2018
ms.reviewer: jsnow
ms.custom: it-pro
ms.openlocfilehash: f7baa29c77ae4af9813bfc755a39cc07288a3ad2
ms.sourcegitcommit: 1b561b77aa080416b094b6f41fce5b6a4721e7d5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/17/2018
ms.locfileid: "45734678"
---
# <a name="what-are-the-fundamentals-of-azure-identity-and-access-management"></a>Azure ID 및 액세스 관리의 기본 사항
Azure AD Premium은 고급 보호 기능이 포함된 클라우드 기반 ID 및 액세스 관리 솔루션입니다. 이러한 고급 기능을 사용하면 모든 앱에 보안 ID, ID 보호 기능([Microsoft intelligence Security Graph](https://www.microsoft.com/security/intelligence)를 통해 향상됨) 및 [PIM(Privileged Identity Management)](../privileged-identity-management/pim-configure.md)을 제공할 수 있습니다. Azure AD에서는 실시간으로 사용자 ID를 보호할 수 있으므로 조직 데이터와 관련된 위험 기반/적응형 액세스 정책을 만들 수 있습니다.

다음 짧은 비디오에서 Azure AD ID 관리 및 보호에 대한 간략한 개요를 살펴보세요.
>[!VIDEO https://www.youtube.com/embed/9LGIJ2-FKIM]

또한 Azure AD에서는 암호 재설정, 사용자/그룹 관리, 앱 요청 등 환경을 보호하고 자동화하고 관리하는 데 사용할 수 있는 도구 집합도 제공됩니다. 그뿐 아니라 사용자 소유 장치도 관리할 수 있으며, SaaS(Software as a Service) 앱의 액세스 및 제어도 가능합니다.

Azure Active Directory Premium Edition 및 관련 도구의 비용에 대한 자세한 내용은 [Azure Active Directory 가격](https://azure.microsoft.com/pricing/details/active-directory/)을 참조하세요.

## <a name="connect-on-premises-active-directory-with-azure-ad-and-office-365"></a>Azure AD 및 Office 365를 사용하여 온-프레미스 Active Directory 연결
[하이브리드 ID 관리](https://aka.ms/aadframework)를 통해 온-프레미스 디렉터리를 Azure AD와 통합하면 온-프레미스 Active Directory 구현을 클라우드로 확장할 수 있습니다. [Azure AD Connect](../connect/active-directory-aadconnect.md)에서 이러한 통합 기능이 제공되므로 사용자는 온-프레미스 리소스와 Office 365 등의 클라우드 서비스에 ID 하나를 사용하여 SSO(Single Sign-On) 방식으로 액세스할 수 있습니다.

DirSync, Azure AD Sync와 같은 이전 버전 ID 통합 도구 대신 Azure AD Connect를 사용하면 온-프레미스와 Azure AD 간의 ID 동기화 요구를 지원할 수 있습니다. 다음을 통해 Azure AD Connect 동기화를 사용하도록 설정합니다.

- **동기화.** 사용자, 그룹 및 기타 개체를 생성합니다. 또한 온-프레미스 사용자의 ID 정보가 Azure AD의 정보와 일치하도록 설정합니다. 암호 쓰기 저장을 설정하면 사용자가 Azure AD에서 암호를 업데이트할 때 온-프레미스 디렉터리를 동기화된 상태로 유지할 수 있습니다.

- 인증. Azure AD 하이브리드 ID 솔루션을 설정할 때는 올바른 인증 방법을 선택해야 합니다. 조직에서 사용할 인증 방법을 클라우드 인증(암호 해시 동기화/통과 인증) 또는 페더레이션 인증(AD FS) 중에서 선택할 수 있습니다. 사용 가능한 옵션에 대한 자세한 내용은 [Azure Active Directory 하이브리드 ID 솔루션용으로 올바른 인증 방법 선택](https://aka.ms/auth-options)을 참조하세요.

- **상태 모니터링.** Azure AD Connect Health는 모니터링 기능 및 모니터링 활동 확인을 위한 Azure Portal의 중앙 위치를 제공합니다. 자세한 내용은 [온-프레미스 ID 인프라 및 클라우드 동기화 서비스 모니터링](../connect-health/active-directory-aadconnect-health.md)을 참고하세요.

## <a name="increase-productivity-and-reduce-helpdesk-costs-with-self-service-and-single-sign-on-experiences"></a>셀프 서비스 및 Single Sign-On 환경으로 생산성 향상 및 헬프데스크 비용 절감
사용자는 모든 장치에서 일관되게 제공되는 환경에서 사용자 이름과 암호 하나를 사용하는 경우 시간을 절약할 수 있습니다. 또한 지원 센터의 지원을 기다리지 않고 [잊어버린 암호를 재설정](../user-help/active-directory-passwords-update-your-own-password.md)하거나 응용 프로그램 액세스를 요청하는 등의 셀프 서비스 작업을 수행하여 시간을 절약할 수도 있습니다.

SSO 및 일관된 환경 사용 범위를 확장하는 Azure AD는 [온-프레미스 Active Directory를 클라우드로 확장](../connect/active-directory-aadconnect.md)함으로써 사용자가 기본 조직 계정을 도메인 가입 장치, 회사 리소스 및 작업을 완료하기 위해 사용해야 하는 모든 웹 및 SaaS 응용 프로그램에 사용할 수 있도록 합니다. 

그리고 그룹 등록 및 사용자의 직원 상태에 따라 응용 프로그램 액세스 권한을 자동으로 프로비전하거나 프로비전 해제할 수 있습니다. 따라서 갤러리 앱이나 자체 온-프레미스 앱(직접 개발하여 [Azure AD Application Proxy](../manage-apps/application-proxy.md)를 통해 게시한 앱)에 대한 액세스를 제어할 수 있습니다.

## <a name="manage-and-control-access-to-your-organizational-resources"></a>조직 리소스 액세스 관리 및 제어
Microsoft ID 및 액세스 관리 솔루션을 사용하면 조직 데이터 센터 전반의 앱/리소스 액세스 및 클라우드 액세스를 보호할 수 있습니다. 이러한 액세스 관리 기능을 통해 [Multi-Factor Authentication](../authentication/concept-mfa-howitworks.md) 및 [조건부 액세스 정책](../conditional-access/overview.md)과 같은 추가 유효성 검사 수준을 제공할 수 있습니다. 또한 고급 보안 보고, 감사 및 경고를 통해 의심스러운 작업을 모니터링하여 보안 문제 발생 가능성을 완화할 수도 있습니다.

Azure AD Premium에서 조건부 액세스 정책을 사용하는 경우 SaaS 앱, 클라우드나 온-프레미스에서 실행되는 사용자 지정 앱, 웹앱 등 Azure AD에 연결된 모든 앱용으로 정책 기반 액세스 규칙을 만들 수 있습니다. Azure AD는 규칙을 실시간으로 평가하고 사용자가 앱 액세스를 시도할 때마다 규칙을 적용합니다. Azure ID 보호 정책을 사용하면 의심스러운 활동 발견 시 액세스를 차단하거나 Multi-Factor Authentication을 적용하거나 사용자 암호를 재설정하는 등의 방식으로 필요한 조치를 자동으로 취할 수 있습니다.

## <a name="azure-active-directory-privileged-identity-management"></a>Azure Active Directory Privileged Identity Management
Azure Active Directory Premium P2 버전에 포함된 [PIM(Privileged Identity Management)](../privileged-identity-management/pim-getting-started.md)을 사용하면 Azure Active Directory 및 기타 Microsoft 온라인 서비스에서 관리 계정과 이러한 계정의 리소스 액세스를 검색, 제한 및 모니터링할 수 있습니다. 또한 PIM 사용 시에는 정확히 필요한 기간에 대해 주문형 관리 액세스를 관리할 수 있습니다. 즉, 관리자가 미리 구성된 기간 동안 Multi-Factor Authentication을 통해 일시적으로 권한 상승을 요청하도록 허용할 수 있습니다. 이 기간이 지나면 계정은 일반 사용자 상태로 돌아갑니다.

## <a name="next-steps"></a>다음 단계
Azure AD 아키텍처에 대한 자세한 내용은 [Azure AD 아키텍처란?](active-directory-architecture.md)을 참조하세요
