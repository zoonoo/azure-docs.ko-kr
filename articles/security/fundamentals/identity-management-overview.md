---
title: ID 관리에 도움이 되는 Azure 보안 기능 | Microsoft Docs
description: Id 관리에 도움이 되는 핵심 Azure 보안 기능에 대해 알아봅니다. Single Sign-On 및 역방향 프록시와 같은 항목에 대 한 정보를 참조 하세요.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 5aa0a7ac-8f18-4ede-92a1-ae0dfe585e28
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/19/2018
ms.author: terrylan
Customer intent: As an IT Pro or decision maker I am trying to learn about identity management capabilities in Azure
ms.openlocfilehash: 54c14f1ef78694055c567ded0fdccede062605ba
ms.sourcegitcommit: 4313e0d13714559d67d51770b2b9b92e4b0cc629
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/27/2020
ms.locfileid: "91400318"
---
# <a name="azure-identity-management-security-overview"></a>Azure ID 관리 보안 개요

 ID 관리는 [보안 주체](/windows/security/identity-protection/access-control/security-principals)를 인증하고 권한을 부여하는 프로세스입니다. 해당 주체(ID)에 대한 정보를 제어하는 작업도 포함됩니다. 보안 주체 (id)에는 서비스, 응용 프로그램, 사용자, 그룹 등이 포함 될 수 있습니다. Microsoft id 및 액세스 관리 솔루션을 사용 하면 회사 데이터 센터와 클라우드 간에 응용 프로그램 및 리소스에 대 한 액세스를 보호할 수 있습니다. 이러한 보호를 통해 Multi-Factor Authentication 및 조건부 액세스 정책과 같은 추가 수준의 유효성 검사를 수행할 수 있습니다. 고급 보안 보고, 감사 및 경고를 통해 의심스러운 작업을 모니터링하여 잠재적인 보안 문제를 완화시킵니다. [Azure Active Directory Premium](/azure/active-directory/active-directory-editions) 은 수천 개의 클라우드 SaaS(software as a service) 앱에 SSO(Single Sign-On)을 제공하고 온-프레미스를 실행하는 웹앱에 액세스를 제공합니다.
 
Azure AD(Azure Active Directory)의 보안 이점을 활용하여 다음을 수행할 수 있습니다.

* 하이브리드 엔터프라이즈에서 사용자, 그룹 및 디바이스의 동기화가 유지되도록 각 사용자에 대해 단일 ID를 만들고 관리합니다. 
* 수천 개의 미리 통합된 SaaS 앱을 포함한 애플리케이션에 대한 SSO 액세스를 제공합니다.
* 온-프레미스 및 클라우드 애플리케이션 모두에 대해 규칙 기반 Multi-Factor Authentication을 적용하여 애플리케이션 액세스 보안을 사용하도록 설정
* Azure AD 애플리케이션 프록시를 통해 온-프레미스 웹 애플리케이션에 대한 보안 원격 액세스 프로비전

이 문서의 목표는 ID 관리에 도움이 되는 핵심 Azure 보안 기능의 개요를 제공하는 것입니다. 문서에 각 기능에 대한 세부 정보를 제공하는 링크를 제공하므로 자세히 알아볼 수 있습니다.  

문서는 다음과 같은 핵심 Azure ID 관리 기능에 중점을 둡니다.

* SSO(Single sign-on)
* 역방향 프록시
* Multi-Factor Authentication
* Azure RBAC(Azure 역할 기반 액세스 제어)
* 보안 모니터링, 경고 및 기계 학습 기반 보고서
* 소비자 ID 및 액세스 관리
* 디바이스 등록
* Privileged Identity Management
* ID 보호
* 하이브리드 ID 관리/Azure AD Connect
* Azure AD 액세스 검토

## <a name="single-sign-on"></a>SSO(Single sign-on)

SSO란 단일 사용자 계정을 사용하여 한 번만 로그인함으로써 비즈니스를 수행하는 데 필요한 모든 애플리케이션 및 리소스에 액세스할 수 있음을 의미합니다. 로그인하면 다시 인증(예: 암호 입력)을 수행하지 않아도 필요한 모든 애플리케이션에 액세스할 수 있습니다.

많은 조직에서는 사용자 생산성을 위해 Microsoft 365, Box 및 Salesforce와 같은 SaaS 응용 프로그램에 의존 합니다. 지금까지 IT 담당자는 각 SaaS 애플리케이션에서 사용자 계정을 개별적으로 만들고 업데이트해야 하며, 사용자는 각 SaaS 애플리케이션에 대한 암호를 기억해야 했습니다.

Azure AD는 온-프레미스 Active Directory 환경을 클라우드로 확장하여 사용자가 자신의 기본 조직 계정을 사용하여 해당 도메인에 가입된 디바이스 및 회사 리소스뿐만 아니라 작업에 필요한 모든 웹 및 SaaS 애플리케이션에 로그인할 수 있도록 합니다.

사용자는 여러 사용자 이름과 암호 집합을 관리할 필요가 없을 뿐만 아니라, 해당 조직 그룹 및 직원 상태에 따라 애플리케이션 액세스를 자동으로 프로비전하거나 프로비전을 해제할 수 있습니다. Azure AD는 SaaS 애플리케이션에 대한 사용자의 액세스를 중앙에서 관리할 수 있는 보안 및 액세스 관리 제어를 도입했습니다.

자세한 정보:

* [Single Sign-On 개요](https://azure.microsoft.com/documentation/videos/overview-of-single-sign-on/)
* [Azure Active Directory로 애플리케이션 액세스 및 Single Sign-On을 구현하는 방법](../../active-directory/manage-apps/what-is-single-sign-on.md)
* [앱 관리에 대 한 빠른 시작 시리즈](../../active-directory/manage-apps/view-applications-portal.md)

## <a name="reverse-proxy"></a>역방향 프록시

Azure AD 애플리케이션 프록시를 사용하면 [SharePoint](https://support.office.com/article/What-is-SharePoint-97b915e6-651b-43b2-827d-fb25777f446f?ui=en-US&rs=en-US&ad=US) 사이트, [Outlook 웹앱](https://technet.microsoft.com/library/jj657718.aspx), [IIS](https://www.iis.net/) 기반 앱과 같은 온-프레미스 애플리케이션을 프라이빗 네트워크 내에 게시할 수 있으며 네트워크 외부 사용자가 안전하게 액세스할 수 있게 합니다. 애플리케이션 프록시에서는 Azure AD가 지원하는 수천 개의 SaaS 애플리케이션과 함께, 많은 유형의 온-프레미스 웹 애플리케이션에 대한 원격 액세스 및 SSO를 제공합니다. 직원들은 자택에서 개인용 디바이스를 사용하여 앱에 로그인하고 이 클라우드 기반 프록시를 통해 인증할 수 있습니다.

자세한 정보:

* [Azure AD 애플리케이션 프록시 사용](/azure/active-directory/manage-apps/application-proxy-enable)
* [Azure AD 애플리케이션 프록시를 사용하여 애플리케이션 게시](/azure/active-directory/active-directory-application-proxy-publish)
* [애플리케이션 프록시를 사용하는 Single Sign-On](../../active-directory/manage-apps/application-proxy-configure-single-sign-on-with-kcd.md)
* [조건부 액세스 사용](../../active-directory/manage-apps/application-proxy-integrate-with-sharepoint-server.md)

## <a name="multi-factor-authentication"></a>Multi-Factor Authentication

Azure Multi-Factor Authentication은 1 초과 인증 방법을 사용해야 하고 사용자 로그인 및 트랜잭션에 중요한 제2의 보안 계층을 추가하는 인증 방법입니다. 간단한 로그인 프로세스에 대한 사용자 요구를 충족하는 동안 Multi-Factor Authentication을 사용하면 데이터와 애플리케이션에 대한 액세스를 보호합니다. 전화 통화, 문자 메시지 또는 모바일 앱 알림 또는 확인 코드 및 타사 OAuth 토큰과 같은 다양한 확인 옵션을 통해 강력한 인증을 전달합니다.

자세한 정보:

* [Multi-Factor Authentication](https://azure.microsoft.com/documentation/services/multi-factor-authentication/)
* [Azure Multi-Factor Authentication이란?](/azure/active-directory/authentication/multi-factor-authentication)
* [Azure Multi-Factor Authentication 작동 방법](../../active-directory/authentication/concept-mfa-howitworks.md)

## <a name="azure-rbac"></a>Azure RBAC

Azure RBAC는 Azure에서 리소스에 대 한 세분화 된 액세스 관리를 제공 하는 Azure Resource Manager을 기반으로 하는 권한 부여 시스템입니다. Azure RBAC를 사용 하면 사용자가 보유 한 액세스 수준을 세부적으로 제어할 수 있습니다. 예를 들어 어떤 사용자는 가상 네트워크만 관리하도록 제한하고 다른 사용자는 리소스 그룹의 모든 리소스를 관리하도록 제한할 수 있습니다. Azure에는 사용할 수 있는 기본 제공 역할이 여러 개 있습니다. 다음은 네 가지 기본 제공 역할입니다. 처음 세 개는 모든 리소스 종류에 적용됩니다.

- [소유자](/azure/role-based-access-control/built-in-roles#owner) - 액세스 권한을 다른 사용자에게 위임할 수 있는 권한을 포함하여 모든 리소스에 대한 전체 액세스 권한을 보유합니다. 
- [기여자](/azure/role-based-access-control/built-in-roles#contributor) - 모든 유형의 Azure 리소스를 만들고 관리할 수 있지만 다른 사용자에게 액세스 권한을 부여할 수 없습니다.
- [읽기 권한자](/azure/role-based-access-control/built-in-roles#reader) - 기존 Azure 리소스를 볼 수 있습니다.
- [사용자 액세스 관리자](/azure/role-based-access-control/built-in-roles#user-access-administrator) - Azure 리소스에 대한 사용자 액세스를 관리할 수 있습니다.

자세한 정보:

* [Azure RBAC(Azure 역할 기반 액세스 제어)란?](/azure/role-based-access-control/overview)
* [Azure 기본 제공 역할](/azure/role-based-access-control/built-in-roles)

## <a name="security-monitoring-alerts-and-machine-learning-based-reports"></a>보안 모니터링, 경고 및 기계 학습 기반 보고서

일치하지 않는 액세스 패턴을 식별하는 보안 모니터링, 경고 및 기계 학습 기반 보고서로 비즈니스를 보호할 수 있습니다. Azure AD 액세스 및 사용 보고서를 사용하여 조직 디렉터리의 무결성 및 보안을 볼 수 있습니다. 이 정보를 사용하면 디렉터리 관리자는 가능한 보안 위험이 발생할 수 있는 위치를 보다 잘 결정하여 이러한 위험을 적절하게 완화할 수 있습니다.

Azure Portal에서 보고서는 다음 범주에 속합니다.

* **비정상 보고서**: 비정상으로 확인된 로그인 이벤트를 포함합니다. 이러한 활동을 인식하고 이벤트가 의심스러운지 확인할 수 있게 해 줍니다.
* **통합 애플리케이션 보고서**: 클라우드 애플리케이션이 조직에서 사용되는 방식을 파악할 수 있게 해 줍니다. Azure AD는 수천 개의 클라우드 애플리케이션과 통합을 제공합니다.
* **오류 보고서**: 외부 애플리케이션에 계정을 프로비전할 때 발생할 수 있는 오류를 나타냅니다.
* **사용자별 보고서**: 특정 사용자에 대한 디바이스/로그인 활동 데이터를 표시합니다.
* **활동 로그**: 최근 24시간, 최근 7일 또는 최근 30일 이내에 감사된 모든 이벤트의 레코드와 그룹 활동 변경 사항, 암호 재설정 및 등록 활동이 포함됩니다.

자세한 정보:

* [액세스 및 사용 보고서 보기](/azure/active-directory/active-directory-view-access-usage-reports)
* [Azure Active Directory Reporting 시작하기](/azure/active-directory/active-directory-reporting-getting-started)
* [Azure Active Directory 보고 가이드](/azure/active-directory/active-directory-reporting-guide)

## <a name="consumer-identity-and-access-management"></a>소비자 ID 및 액세스 관리

Azure AD B2C는 수억 개의 ID로 확장하는 소비자 지향 애플리케이션에 항상 사용 가능한 전역적인 ID 관리 서비스입니다. 이 서비스는 모바일 및 웹 플랫폼에 통합될 수 있습니다. 고객은 사용자 지정 가능한 환경을 통해 기존 소셜 계정을 사용하거나 새 자격 증명을 만들어 모든 애플리케이션에 로그인할 수 있습니다.

이전에는 고객을 애플리케이션에 가입 및 로그인하게 하려고 했던 애플리케이션 개발자들이 자체 코드를 작성했습니다. 또한 온-프레미스 데이터베이스나 시스템을 사용하여 사용자 이름과 암호를 저장했습니다. Azure Active Directory B2C는 조직에게 안전한 표준 기반 플랫폼 및 확장할 수 있는 정책의 다양한 집합으로 소비자 ID 관리를 애플리케이션에 통합하는 더 나은 방법을 제공합니다.

Azure AD B2C를 사용하면 소비자는 기존 소셜 계정(Facebook, Google, Amazon, LinkedIn)을 사용하거나 새 자격 증명(이메일 주소 및 암호 또는 사용자 이름 및 암호)을 만들어서 애플리케이션을 등록할 수 있습니다.

자세한 정보:

* [Azure Active Directory B2C란?](https://azure.microsoft.com/services/active-directory-b2c/)
* [Azure Active Directory B2C 미리 보기: 애플리케이션에 소비자 등록 및 로그인](../../active-directory-b2c/overview.md)
* [Azure Active Directory B2C 미리 보기: 응용 프로그램 유형](../../active-directory-b2c/application-types.md)

## <a name="device-registration"></a>디바이스 등록

Azure AD device registration은 장치 기반 [조건부 액세스](/azure/active-directory/active-directory-conditional-access-device-registration-overview) 시나리오의 기초입니다. 디바이스가 등록되면 Azure AD 디바이스 등록은 사용자가 로그인할 때 디바이스를 인증하는 데 사용하는 ID와 함께 디바이스를 제공합니다. 그런 다음 인증 된 장치 및 장치 특성을 사용 하 여 클라우드 및 온-프레미스에서 호스트 되는 응용 프로그램에 대 한 조건부 액세스 정책을 적용할 수 있습니다.

Intune과 같은 모바일 디바이스 관리 솔루션과 함께 사용할 경우 Azure AD의 디바이스 특성이 디바이스에 대한 추가 정보로 업데이트됩니다. 그런 다음 보안 및 규정 준수에 대 한 표준을 충족 하도록 장치에서 액세스를 적용 하는 조건부 액세스 규칙을 만들 수 있습니다.

자세한 정보:

* [Azure AD 디바이스 등록 시작](/azure/active-directory/active-directory-conditional-access-device-registration-overview)
* [Windows 도메인에 가입된 디바이스의 Azure AD 자동 디바이스 등록](/azure/active-directory/active-directory-conditional-access-automatic-device-registration)
* [Windows 도메인 가입 디바이스에 대한 Azure AD 자동 등록 설정](/azure/active-directory/active-directory-conditional-access-automatic-device-registration-setup)

## <a name="privileged-identity-management"></a>Privileged Identity Management

Azure AD Privileged Identity Management를 사용 하 여 권한 있는 id를 관리, 제어 및 모니터링 하 고 Azure AD 및 기타 Microsoft 온라인 서비스 (예: Microsoft 365 및 Microsoft Intune)의 리소스에 액세스할 수 있습니다.

사용자가 Azure 또는 Microsoft 365 리소스 또는 다른 SaaS 앱에서 권한 있는 작업을 수행 해야 하는 경우가 있습니다. 이는 보통 조직이 사용자에게 Azure AD에서 영구 권한 있는 액세스를 제공해야 함을 의미합니다. 조직은 사용자가 관리자 권한으로 수행하는 작업을 충분히 모니터링할 수 없으므로 이러한 액세스는 클라우드에 호스트된 리소스의 보안 위험을 증가시킵니다. 또한 권한 있는 액세스가 있는 사용자 계정이 손상되면 이로 인해 조직의 전반적인 클라우드 보안에 영향을 줄 수 있습니다. Azure AD 권한 있는 ID 관리는 이 위험을 완화하는 데 도움이 됩니다.

Azure AD Privileged Identity Management로 다음을 수행할 수 있습니다.

* Azure AD 관리자인 사용자를 확인할 수 있습니다.
* Microsoft 365 및 Intune과 같은 Microsoft 서비스에 대 한 주문형 JIT (just-in-time) 관리 액세스를 사용 하도록 설정 합니다.
* 관리자 액세스 기록 및 관리자 할당 변경에 대한 보고서를 가져옵니다.
* 권한 있는 역할의 액세스에 대한 알림을 받을 수 있습니다.

자세한 정보:

* [Azure AD Privileged Identity Management란?](../../active-directory/privileged-identity-management/pim-configure.md)
* [PIM에서 Azure AD 디렉터리 역할 할당](../../active-directory/privileged-identity-management/pim-how-to-add-role-to-user.md)

## <a name="identity-protection"></a>ID 보호

Azure AD ID 보호은 조직의 id에 영향을 주는 위험 검색 및 잠재적 취약성에 대 한 통합 보기를 제공 하는 보안 서비스입니다. ID 보호는 Azure AD 비정상 작업 보고서를 통해 사용할 수 있는 기존 Azure AD 변칙 검색 기능을 활용합니다. Id 보호에는 실시간으로 변칙을 검색할 수 있는 새로운 위험 검색 유형도 도입 됩니다.

자세한 정보:

* [Azure AD ID 보호](/azure/active-directory/identity-protection/overview)
* [Channel 9: Azure AD 및 ID 표시: ID 보호 미리 보기](https://channel9.msdn.com/Series/Azure-AD-Identity/Azure-AD-and-Identity-Show-Identity-Protection-Preview)

## <a name="hybrid-identity-managementazure-ad-connect"></a>하이브리드 ID 관리/Azure AD Connect

Microsoft의 ID 솔루션은 온-프레미스 및 클라우드 기반 기능을 확장하며 이는 위치에 관계 없이 모든 리소스에 인증 및 권한 부여에 대한 단일 사용자 ID를 만듭니다. 하이브리드 ID라고 합니다. Azure AD Connect는 하이브리드 ID 목표를 충족하고 달성하도록 설계된 Microsoft 도구입니다. 이렇게 하면 Azure AD와 통합된 Microsoft 365, Azure 및 SaaS 애플리케이션 사용자를 위한 공통 ID를 제공할 수 있습니다. 다음과 같은 기능을 제공합니다.

* 동기화
* AD FS 및 페더레이션 통합
* 통과 인증
* 상태 모니터링

자세한 정보:

* [하이브리드 ID 백서](https://download.microsoft.com/download/D/B/A/DBA9E313-B833-48EE-998A-240AA799A8AB/Hybrid_Identity_White_Paper.pdf)
* [Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory/)
* [Azure AD 팀 블로그](https://blogs.technet.microsoft.com/ad/)

## <a name="azure-ad-access-reviews"></a>Azure AD 액세스 검토

Azure AD(Azure Active Directory) 액세스 검토를 사용하면 조직에서 그룹 멤버 자격을 효율적으로 관리하고 엔터프라이즈 애플리케이션에 액세스하고 권한 있는 역할을 할당할 수 있습니다.

자세한 정보:

* [Azure AD 액세스 검토](../../active-directory/governance/access-reviews-overview.md)
* [Azure AD 액세스 검토를 사용하여 사용자 액세스 관리](../../active-directory/governance/access-reviews-overview.md)
