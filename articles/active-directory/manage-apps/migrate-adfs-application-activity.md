---
title: 활동 보고서를 사용하여 AD FS 앱을 Azure Active Directory로 이동 | 마이크로소프트 문서'
description: AD FS(활성 디렉터리 페더레이션 서비스) 응용 프로그램 활동 보고서를 사용하면 응용 프로그램을 AD FS에서 Azure Active Directory(Azure AD)로 빠르게 마이그레이션할 수 있습니다. AD FS에 대한 이 마이그레이션 도구는 Azure AD와의 호환성을 식별하고 마이그레이션 지침을 제공합니다.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 01/14/2019
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: 333e440fdd5f5062dda45fb12a83543c63e66c04
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75978026"
---
# <a name="use-the-ad-fs-application-activity-report-preview-to-migrate-applications-to-azure-ad"></a>AD FS 응용 프로그램 활동 보고서(미리 보기)를 사용하여 응용 프로그램을 Azure AD로 마이그레이션합니다.

대부분의 조직에서 AD FS(Active Directory Federation Services)를 사용하여 클라우드 응용 프로그램에 대한 단일 사인온을 제공합니다. 특히 비용 관리, 위험 관리, 생산성, 규정 준수 및 거버넌스 측면에서 인증을 위해 AD FS 응용 프로그램을 Azure AD로 이동하면 상당한 이점이 있습니다. 그러나 Azure AD와 호환되는 응용 프로그램을 이해하고 특정 마이그레이션 단계를 식별하는 데 시간이 많이 걸릴 수 있습니다.

Azure 포털의 AD FS 응용 프로그램 활동 보고서(미리 보기)를 사용하면 Azure AD로 마이그레이션할 수 있는 응용 프로그램을 신속하게 식별할 수 있습니다. Azure AD와의 호환성을 위해 모든 AD FS 응용 프로그램을 평가하고, 모든 문제를 검사하며, 마이그레이션을 위한 개별 응용 프로그램을 준비하는 방법에 대한 지침을 제공합니다. AD FS 응용 프로그램 활동 보고서를 사용하면 다음을 수행할 수 있습니다.

* **AD FS 응용 프로그램을 검색하고 마이그레이션 범위를 제공합니다.** AD FS 응용 프로그램 활동 보고서는 조직의 모든 AD FS 응용 프로그램을 나열하고 Azure AD로 마이그레이션할 준비가 되었음을 나타냅니다.
* **마이그레이션을 위한 응용 프로그램의 우선 순위를 지정합니다.** 지난 1일, 7일 또는 30일 동안 응용 프로그램에 로그인한 고유 사용자 수를 확인하여 응용 프로그램 마이그레이션의 중요도 또는 위험을 파악합니다.
* **마이그레이션 테스트를 실행하고 문제를 해결합니다.** 보고 서비스는 테스트를 자동으로 실행하여 응용 프로그램을 마이그레이션할 준비가 되었는지 확인합니다. 결과는 AD FS 응용 프로그램 활동 보고서에 마이그레이션 상태로 표시됩니다. 잠재적인 마이그레이션 문제가 식별되면 문제를 해결하는 방법에 대한 구체적인 지침을 얻을 수 있습니다.

AD FS 응용 프로그램 활동 데이터는 글로벌 관리자, 보고서 판독기, 보안 판독기, 응용 프로그램 관리자 또는 클라우드 응용 프로그램 관리자와 같은 관리자 역할이 할당된 사용자가 사용할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

* 조직에서 응용 프로그램에 액세스하려면 현재 AD FS를 사용해야 합니다.
* Azure AD 연결 상태는 Azure AD 테넌트에서 활성화되어야 합니다.
   * [Azure AD 연결 상태 자세히 알아보기](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-health-adfs)
   * [Azure AD 연결 상태 설정 시작](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-health-agent-install)

## <a name="discover-ad-fs-applications-that-can-be-migrated"></a>마이그레이션할 수 있는 AD FS 응용 프로그램 검색 

AD FS 응용 프로그램 활동 보고서는 Azure AD **사용 & 인사이트** 보고 아래의 Azure 포털에서 사용할 수 있습니다. AD FS 응용 프로그램 활동 보고서는 각 AD FS 응용 프로그램을 분석하여 있는 것으로 마이그레이션할 수 있는지 또는 추가 검토가 필요한지 확인합니다. 

1. AD FS 응용 프로그램 활동 데이터(글로벌 관리자, 보고서 판독기, 보안 판독기, 응용 프로그램 관리자 또는 클라우드 응용 프로그램 관리자)에 액세스할 수 있는 관리자 역할을 사용하여 [Azure 포털에](https://portal.azure.com) 로그인합니다.

2. **Azure Active Directory를**선택한 다음 **엔터프라이즈 응용 프로그램을 선택합니다.**

3. **활동**에서 **사용 & 인사이트(미리 보기)를**선택한 다음 **AD FS 응용 프로그램 활동을** 선택하여 조직의 모든 AD FS 응용 프로그램 목록을 엽니다.

   ![AD FS 응용 프로그램 활동](media/migrate-adfs-application-activity/adfs-application-activity.png)

4. AD FS 응용 프로그램 활동 목록의 각 응용 프로그램에 대해 **마이그레이션 상태를**봅니다.

   * **마이그레이션 준비가 되었다는** 것은 AD FS 응용 프로그램 구성이 Azure AD에서 완전히 지원되며 있는 대로 마이그레이션할 수 있다는 것을 의미합니다.

   * **검토가 필요한다는** 것은 응용 프로그램의 일부 설정을 Azure AD로 마이그레이션할 수 있지만 있는 것으로 마이그레이션할 수 없는 설정을 검토해야 합니다.

   * **필요한 추가 단계는** Azure AD가 응용 프로그램의 일부 설정을 지원하지 않으므로 응용 프로그램을 현재 상태로 마이그레이션할 수 없음을 의미합니다.

## <a name="evaluate-the-readiness-of-an-application-for-migration"></a>마이그레이션을 위한 응용 프로그램의 준비 준비 여부를 평가합니다. 

1. AD FS 응용 프로그램 활동 목록에서 **마이그레이션 상태** 열의 상태를 클릭하여 마이그레이션 세부 정보를 엽니다. 잠재적인 마이그레이션 문제와 함께 통과된 구성 테스트에 대한 요약이 표시됩니다.

   ![마이그레이션 세부 정보](media/migrate-adfs-application-activity/migration-details.png)

2. 메시지를 클릭하여 추가 마이그레이션 규칙 세부 정보를 엽니다. 테스트된 속성의 전체 목록은 [아래AD FS 응용 프로그램 구성 테스트](#ad-fs-application-configuration-tests) 표를 참조하십시오.

   ![마이그레이션 규칙 세부 정보](media/migrate-adfs-application-activity/migration-rule-details.png)

### <a name="ad-fs-application-configuration-tests"></a>AD FS 애플리케이션 구성 테스트

다음 표에는 AD FS 응용 프로그램에서 수행되는 모든 구성 테스트가 나열되어 있습니다.

|결과  |통과/경고/실패  |설명  |
|---------|---------|---------|
|테스트-ADFSRP추가 인증 규칙 <br> 추가 인증에 대해 마이그레이션할 수 없는 규칙이 하나 이상 검색되었습니다.       | 패스/경고          | 신뢰할 수 있는 당사자에는 MFA(다단계 인증)를 묻는 규칙이 있습니다. Azure AD로 이동하려면 해당 규칙을 조건부 액세스 정책으로 변환합니다. 온-프레미스 MFA를 사용하는 경우 Azure MFA로 이동하는 것이 좋습니다. [조건부 액세스에 대해 자세히 알아보십시오.](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks)        |
|테스트-ADFSRP추가WSFedEndpoint <br> 신뢰할 수 있는 당사자에는 추가 WSFedEndpoint가 true로 설정되어 있습니다.       | 성공/실패          | AD FS의 의존 당사자는 여러 WS-Fed 어설션 끝점을 허용합니다.현재 Azure AD는 하나만 지원합니다.이 결과가 마이그레이션을 차단하는 시나리오가 있는 경우 [.](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/38695621-allow-multiple-ws-fed-assertion-endpoints)     |
|테스트-ADFSRP허용인증클래스참조 <br> 신뢰할 수 있는 당사자가 허용된 인증 클래스 참조를 설정했습니다.       | 성공/실패          | AD FS의 이 설정을 사용하면 응용 프로그램이 특정 인증 유형만 허용하도록 구성되었는지 여부를 지정할 수 있습니다. 이 기능을 얻으려면 조건부 액세스를 사용하는 것이 좋습니다. 이 결과가 마이그레이션을 차단하는 시나리오가 있는 경우 [.](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/38695672-allow-in-azure-ad-to-specify-certain-authentication)  [조건부 액세스에 대해 자세히 알아보십시오.](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks)          |
|테스트-ADFSRP항상 필요 인증 <br> 항상 필요 인증검사결과      | 성공/실패          | AD FS의 이 설정을 사용하면 응용 프로그램이 SSO 쿠키를 무시하도록 구성되었는지 여부를 지정하고 **항상 인증 에 대한 프롬프트를**지정할 수 있습니다. Azure AD에서 조건부 액세스 정책을 사용하여 인증 세션을 관리하여 유사한 동작을 수행할 수 있습니다. [조건부 액세스를 통해 인증 세션 관리 구성에 대해 자세히 알아봅니다.](https://docs.microsoft.com/azure/active-directory/conditional-access/howto-conditional-access-session-lifetime)          |
|테스트-ADFSRP자동 업데이트 사용 가능 <br> 의존 당사자가 자동 UpdateEnabled가 true로 설정되어 있습니다.       | 패스/경고          | AD FS의 이 설정을 사용하면 AD FS가 페더레이션 메타데이터 내의 변경 내용에 따라 응용 프로그램을 자동으로 업데이트하도록 구성되었는지 여부를 지정할 수 있습니다. Azure AD는 현재 이 것을 지원하지 않지만 응용 프로그램이 Azure AD로 마이그레이션되는 것을 차단해서는 안 됩니다.           |
|테스트-ADFSRP클레임공급자 이름 <br> 리클레임 당사자에는 여러 클레임공급자가 사용 설정되어 있습니다.       | 성공/실패          | AD FS의 이 설정은 신뢰할 수 있는 당사자가 클레임을 수락하는 ID 공급자를 호출합니다. Azure AD에서 Azure AD B2B를 사용하여 외부 공동 작업을 활성화할 수 있습니다. [Azure AD B2B에 대해 자세히 알아보십시오.](https://docs.microsoft.com/azure/active-directory/b2b/what-is-b2b)          |
|테스트-ADFSRP위임인증규칙      | 성공/실패          | 응용 프로그램에 사용자 지정 위임 권한 부여 규칙이 정의되어 있습니다. 이 개념은 Azure AD가 OpenID Connect 및 OAuth 2.0과 같은 최신 인증 프로토콜을 사용하여 지원하는 WS-Trust 개념입니다. [Microsoft ID 플랫폼에 대해 자세히 알아보십시오.](https://docs.microsoft.com/azure/active-directory/develop/v2-protocols-oidc)          |
|테스트-ADFSRP 사칭 권한 부여규칙       | 패스/경고          | 응용 프로그램에 사용자 지정 가장 권한 부여 규칙이 정의되어 있습니다.이 개념은 Azure AD가 OpenID Connect 및 OAuth 2.0과 같은 최신 인증 프로토콜을 사용하여 지원하는 WS-Trust 개념입니다. [Microsoft ID 플랫폼에 대해 자세히 알아보십시오.](https://docs.microsoft.com/azure/active-directory/develop/v2-protocols-oidc)          |
|테스트-ADFSRP발급규칙 <br> 발급 권한 부여에 대해 마이그레이션할 수 없는 규칙이 하나 이상 검색되었습니다.       | 패스/경고          | 응용 프로그램에는 AD FS에 정의된 사용자 지정 발급 권한 부여 규칙이 있습니다.Azure AD는 Azure AD 조건부 액세스를 통해 이 기능을 지원합니다. [조건부 액세스에 대해 자세히 알아보십시오.](https://docs.microsoft.com/azure/active-directory/conditional-access/overview) <br> 응용 프로그램에 할당된 사용자 또는 그룹별로 응용 프로그램에 대한 액세스를 제한할 수도 있습니다. [응용 프로그램에 액세스하기 위해 사용자 및 그룹을 할당하는 방법에 대해 자세히 알아봅니다.](https://docs.microsoft.com/azure/active-directory/manage-apps/methods-for-assigning-users-and-groups)            |
|테스트-ADFSRP교진변환규칙 <br> 발급 변환에 대해 마이그레이션할 수 없는 규칙이 하나 이상 검색되었습니다.       | 패스/경고          | 응용 프로그램에는 AD FS에 정의된 사용자 지정 발급 변환 규칙이 있습니다. Azure AD는 토큰에서 발급된 클레임을 사용자 지정하는 것을 지원합니다. 자세한 내용은 [엔터프라이즈 응용 프로그램에 대한 SAML 토큰에서 발급된 클레임 사용자 지정을](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization)참조하십시오.           |
|테스트-ADFSRP 모니터링 가능 <br> 신뢰할 수 있는 당사자가 모니터링사용이 true로 설정되어 있습니다.       | 패스/경고          | AD FS의 이 설정을 사용하면 AD FS가 페더레이션 메타데이터 내의 변경 내용에 따라 응용 프로그램을 자동으로 업데이트하도록 구성되었는지 여부를 지정할 수 있습니다. Azure AD는 현재 이 것을 지원하지 않지만 응용 프로그램이 Azure AD로 마이그레이션되는 것을 차단해서는 안 됩니다.           |
|테스트-ADFSRPNotBeforeSkew <br> NotBeforeSkew검사결과      | 패스/경고          | AD FS는 SAML 토큰의 NotBefore 및 NotOnOrAfter 시간을 기반으로 시간 기울이기를 허용합니다. Azure AD는 기본적으로 이 문제를 자동으로 처리합니다.          |
|테스트-ADFSRP요청MFA로부터클레임제공자 <br> 신뢰할 수 있는 당사자에는 RequestMFAFrom클레임공급자가 true로 설정되어 있습니다.       | 패스/경고          | AD FS의 이 설정은 사용자가 다른 클레임 공급자로부터 오는 경우 MFA에 대한 동작을 결정합니다. Azure AD에서 Azure AD B2B를 사용하여 외부 공동 작업을 활성화할 수 있습니다. 그런 다음 조건부 액세스 정책을 적용하여 게스트 액세스를 보호할 수 있습니다. [Azure AD B2B](https://docs.microsoft.com/azure/active-directory/b2b/what-is-b2b) 및 [조건부 액세스에](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)대해 자세히 알아보십시오.          |
|테스트-ADFSRP 서명된 삼l요청필수 <br> 신뢰할 수 있는 당사자가 서명한 SamlRequests필수 설정됨       | 성공/실패          | 응용 프로그램은 SAML 요청의 서명을 확인하기 위해 AD FS에서 구성됩니다. Azure AD는 서명된 SAML 요청을 수락합니다. 그러나 서명을 확인 하지 않습니다. Azure AD에는 악의적인 호출로부터 보호하기 위한 다양한 방법이 있습니다. 예를 들어 Azure AD는 SAML 요청의 유효성을 검사하기 위해 응용 프로그램에서 구성된 회신 URL을 사용합니다. Azure AD는 응용 프로그램에 대해 구성된 URL을 회신하기 위해 토큰만 보냅니다. 이 결과가 마이그레이션을 차단하는 시나리오가 있는 경우 [.](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/13394589-saml-signature)          |
|테스트-ADFSRP토큰 수명 <br> 토큰 수명 검사결과        | 패스/경고         | 응용 프로그램은 사용자 지정 토큰 수명 동안 구성됩니다. AD FS 기본값은 1시간입니다.Azure AD는 조건부 액세스를 사용하여 이 기능을 지원합니다. 자세한 내용은 [조건부 액세스를 사용하여 인증 세션 관리 구성을](https://docs.microsoft.com/azure/active-directory/conditional-access/howto-conditional-access-session-lifetime)참조하십시오.          |
|리빙 파티는 클레임을 암호화하도록 설정되어 있습니다. 이것은 Azure AD에서 지원됩니다.       | 합격          | Azure AD를 사용하면 응용 프로그램에 전송된 토큰을 암호화할 수 있습니다. 자세한 내용은 [Azure AD SAML 토큰 암호화 구성을](https://docs.microsoft.com/azure/active-directory/manage-apps/howto-saml-token-encryption)참조하십시오.          |
|암호화된 이름 IdRequired검사결과      | 성공/실패          | 응용 프로그램은 SAML 토큰에서 nameID 클레임을 암호화하도록 구성됩니다.Azure AD를 사용하면 응용 프로그램에 전송된 전체 토큰을 암호화할 수 있습니다.특정 클레임의 암호화는 아직 지원되지 않습니다. 자세한 내용은 [Azure AD SAML 토큰 암호화 구성을](https://docs.microsoft.com/azure/active-directory/manage-apps/howto-saml-token-encryption)참조하십시오.         |

## <a name="check-the-results-of-claim-rule-tests"></a>클레임 규칙 테스트 결과 확인

AD FS에서 응용 프로그램에 대한 클레임 규칙을 구성한 경우 이 환경에서는 모든 클레임 규칙에 대한 세부 분석을 제공합니다. Azure AD로 이동할 수 있는 클레임 규칙과 추가 검토가 필요한 클레임 규칙을 확인할 수 있습니다.

1. AD FS 응용 프로그램 활동 목록에서 **마이그레이션 상태** 열의 상태를 클릭하여 마이그레이션 세부 정보를 엽니다. 잠재적인 마이그레이션 문제와 함께 통과된 구성 테스트에 대한 요약이 표시됩니다.

2. 마이그레이션 **규칙 세부 정보** 페이지에서 결과를 확장하여 잠재적인 마이그레이션 문제에 대한 세부 정보를 표시하고 추가 지침을 얻습니다. 테스트된 모든 클레임 규칙에 대한 자세한 목록은 아래의 [클레임 규칙 테스트 결과 확인](#check-the-results-of-claim-rule-tests) 표를 참조하십시오.

   아래 예제에서는 발급 변환 규칙에 대 한 마이그레이션 규칙 세부 정보를 보여 드립니다. 응용 프로그램을 Azure AD로 마이그레이션하기 전에 검토하고 해결해야 하는 클레임의 특정 부분이 나열됩니다.

   ![마이그레이션 규칙 세부 정보 추가 지침](media/migrate-adfs-application-activity/migration-rule-details-guidance.png)

### <a name="claim-rule-tests"></a>클레임 규칙 테스트

다음 표에는 AD FS 응용 프로그램에서 수행되는 모든 클레임 규칙 테스트가 나열되어 있습니다.

|속성  |설명  |
|---------|---------|
|UNSUPPORTED_CONDITION_PARAMETER      | 조건 문은 정규 식을 사용하여 클레임이 특정 패턴과 일치하는지 평가합니다.Azure AD에서 유사한 기능을 얻으려면 IfEmpty(), StartWith(), contains() 등과 같은 미리 정의된 변환을 사용할 수 있습니다. 자세한 내용은 [엔터프라이즈 응용 프로그램에 대한 SAML 토큰에서 발급된 클레임 사용자 지정을](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization)참조하십시오.          |
|UNSUPPORTED_CONDITION_CLASS      | 조건 문에는 발급 문을 실행하기 전에 평가해야 하는 여러 조건이 있습니다.Azure AD는 여러 클레임 값을 평가할 수 있는 클레임의 변환 함수를 통해 이 기능을 지원할 수 있습니다.자세한 내용은 [엔터프라이즈 응용 프로그램에 대한 SAML 토큰에서 발급된 클레임 사용자 지정을](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization)참조하십시오.          |
|UNSUPPORTED_RULE_TYPE      | 클레임 규칙을 인식할 수 없습니다. Azure AD에서 클레임을 구성하는 방법에 대한 자세한 내용은 [엔터프라이즈 응용 프로그램에 대한 SAML 토큰에서 발급된 클레임 사용자 지정을](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization)참조하십시오.          |
|CONDITION_MATCHES_UNSUPPORTED_ISSUER      | 조건 문은 Azure AD에서 지원되지 않는 발급자를 사용합니다.현재 Azure AD는 Active Directory 또는 Azure AD와 다른 저장소의 클레임을 소스로 연결하지 않습니다. 이로 인해 응용 프로그램을 Azure AD로 마이그레이션할 수 없는 경우 [.](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/38695717-allow-to-source-user-attributes-from-external-dire)         |
|UNSUPPORTED_CONDITION_FUNCTION      | 조건 문은 일치 횟수에 관계없이 집계 함수를 사용하여 단일 클레임을 발행하거나 추가합니다.Azure AD에서 사용자의 특성을 평가하여 IfEmpty(), StartWith(), Contains() 등과 같은 함수를 사용하여 클레임에 사용할 값을 결정할 수 있습니다.자세한 내용은 [엔터프라이즈 응용 프로그램에 대한 SAML 토큰에서 발급된 클레임 사용자 지정을](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization)참조하십시오.          |
|RESTRICTED_CLAIM_ISSUED      | 조건 문은 Azure AD에서 제한된 클레임을 사용합니다. 제한된 클레임을 발행할 수 있지만 소스를 수정하거나 변환을 적용할 수는 없습니다. 자세한 내용은 [Azure AD의 특정 앱에 대해 토큰에서 내보낸 클레임 사용자 지정을](https://docs.microsoft.com/azure/active-directory/develop/active-directory-claims-mapping)참조하세요.          |
|EXTERNAL_ATTRIBUTE_STORE      | 발급 문은 Active Directory와 다른 특성 저장소를 사용합니다. 현재 Azure AD는 Active Directory 또는 Azure AD와 다른 저장소의 클레임을 소스로 연결하지 않습니다. 이 결과로 응용 프로그램을 Azure AD로 마이그레이션하는 것이 차단되면 [에 알려주세요.](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/38695717-allow-to-source-user-attributes-from-external-dire)          |
|UNSUPPORTED_ISSUANCE_CLASS      | 발급 문은 ADD를 사용하여 들어오는 클레임 집합에 클레임을 추가합니다. Azure AD에서 이 변환은 여러 클레임 변환으로 구성될 수 있습니다.자세한 내용은 [엔터프라이즈 응용 프로그램에 대한 SAML 토큰에서 발급된 클레임 사용자 지정을](https://docs.microsoft.com/azure/active-directory/develop/active-directory-claims-mapping)참조하십시오.         |
|UNSUPPORTED_ISSUANCE_TRANSFORMATION      | 발급 문은 정규 표현식을 사용하여 내보내질 클레임의 값을 변환합니다.Azure AD에서 유사한 기능을 얻으려면 추출(), Trim(), ToLower 와 같은 미리 정의된 변환을 사용할 수 있습니다. 자세한 내용은 [엔터프라이즈 응용 프로그램에 대한 SAML 토큰에서 발급된 클레임 사용자 지정을](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization)참조하십시오.          |


## <a name="next-steps"></a>다음 단계

- [비디오: AD FS 활동 보고서를 사용하여 응용 프로그램을 마이그레이션하는 방법](https://www.youtube.com/watch?v=OThlTA239lU)
- [Azure Active 디렉터리로 응용 프로그램 관리](what-is-application-management.md)
- [앱에 대한 액세스 관리](what-is-access-management.md)
- [Azure AD 연결 페더레이션](../hybrid/how-to-connect-fed-whatis.md)
