---
title: Azure Active Directory를 사용 하 여 강력한 id 기반의 네 단계
description: 이 항목에서는 하이브리드 identity 고객 강력한 id 기반 구축 하기 위해 수행할 수는 4 단계를 설명 합니다.
services: active-directory
author: martincoetzer
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/20/2019
ms.subservice: hybrid
ms.author: martinco
ms.collection: M365-identity-device-management
ms.openlocfilehash: a20a1a1009949aa2e6de8586040e918ae15c8d39
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/08/2019
ms.locfileid: "67655937"
---
# <a name="four-steps-to-a-strong-identity-foundation-with-azure-active-directory"></a>Azure Active Directory를 사용 하 여 강력한 id 기반의 네 단계

앱 및 데이터에 대 한 액세스 관리 수 신속 하 게 클라우드로 앱 이동으로 인해 경계 네트워크 및 방화벽과 같은 전통적인 네트워크 보안 경계 전략에 의존 더 이상. 이제 조직 및 조직의 앱 및 데이터에 대 한 액세스를 권한이 무엇 제어할를 해당 id 솔루션을 신뢰 해야 합니다. 조직이 직원이 자신의 장치를 작업 하 여 장치를 사용 하 여 인터넷에 연결할 수 어디에서 나 수를 허용 합니다. 이러한 장치는 준수 및 보안을 보장 구현 하도록 선택 하는 조직 id 솔루션에서 중요 하 게 되었습니다. 오늘날의 디지털 작업 영역에서 [id가 주 제어 평면](https://www.microsoft.com/security/technology/identity-access-management?rtc=1) 모든 조직의 클라우드로 이동 합니다.

Azure Active Directory (Azure AD)는 하이브리드 id 솔루션을 채택 하에서 조직에는 자동화, 위임, 셀프 서비스 및 single sign-on 기능을 통해 생산성을 실현 하는 프리미엄 기능 액세스할을 수 있습니다. 작업 자가 회사 IT 팀이 오른쪽에 게 생산성을 설정 하려면 적절 한 리소스에 대 한 올바른 액세스 권한이 함으로써 해당 액세스 제어를 허용 하는 동안 수행에 필요한 모든 곳에서 회사 리소스에 액세스할 수 있습니다.

학습에 따라 모범 사례의이 검사 목록에서는 빌드에 권장 되는 작업을 신속 하 게 배포 된 *강력한* identity foundation 조직에서:

* 앱에 쉽게 연결
* 모든 사용자에 대 한 하나의 id를 자동으로 설정
* 안전 하 게 사용자 환경 향상
* 통찰력을 운영 화

## <a name="step-1---connect-to-apps-easily"></a>1 단계-앱에 쉽게 연결

Azure AD를 사용 하 여 앱에 연결 하 여에서 single sign-on (SSO)를 사용 하도록 설정 하 여 최종 사용자 생산성과 보안을 향상 시킬 수 있으며 사용자 프로 비전을 수행할 수 있습니다. 단일 위치에서 Azure AD에서 앱을 관리 하 여 관리 오버 헤드를 최소화할 수 있으며 보안 및 규정 준수 정책에 대 한 단일 제어 지점의 얻을 수 있습니다.

이 섹션에서는 내부 앱 및 Azure AD에 앱을 마이그레이션할 경우의 이점에 대 한 보안 원격 액세스를 사용 하도록 설정 하면 앱에 대 한 사용자 액세스를 관리 하기 위한 옵션에 설명 합니다.

### <a name="make-apps-available-to-your-users-seamlessly"></a>사용자에 게 앱에 원활 하 게

Azure AD 관리자를 사용 하면 [응용 프로그램을 추가할](https://docs.microsoft.com/azure/active-directory/manage-apps/add-application-portal) 엔터프라이즈 응용 프로그램 갤러리에 [Azure portal](https://portal.azure.com/)합니다. 엔터프라이즈 응용 프로그램 갤러리에 응용 프로그램 추가 쉽게 Azure AD를 id 공급자로 사용 하도록 응용 프로그램을 구성할 수 있습니다. 또한 조건부 액세스 정책 사용 하 여 응용 프로그램에 대 한 사용자 액세스를 관리 하 고 사용자가 반복적으로 자신의 암호를 입력 하 고 온-프레미스에 자동으로 로그인 됩니다 있도록 응용 프로그램에서 single sign-on (SSO) 구성 수 있습니다 및 클라우드 기반 응용 프로그램입니다.

응용 프로그램은 Azure AD 갤러리에 추가 되 면 사용자 지정 하 여 검색 하 고 필요에 따라 다른 앱을 요청 하는 앱을 볼 수 있습니다. Azure AD에서 제공 [여러 가지 방법을](https://docs.microsoft.com/azure/active-directory/manage-apps/end-user-experiences) 해당 앱에 액세스 하는 사용자에 대 한 합니다.

* 액세스 패널 / My Apps
* Office 365 앱 시작 관리자
* 페더레이션된 앱에 직접 로그온
* 직접 로그온 링크

앱에 대 한 사용자 액세스에 대 한 자세한 내용은 참조 하세요 **3 단계-사용자 역량을 강화** 이 문서의.

### <a name="migrate-apps-from-active-directory-federation-services-to-azure-ad"></a>Active Directory Federation Services에서 Azure AD에 앱을 마이그레이션

마이그레이션 single sign-on 구성에서 ADFS Active Directory Federation Services ()를 Azure AD에서 보안, 더 일관 된 관리 효율성 및 공동 작업 추가 기능을 수 있습니다. 최상의 결과 Azure AD에 앱 AD FS에서 마이그레이션하는 것이 좋습니다. Azure ad에 응용 프로그램 인증 및 권한 부여를 가져오는 다음과 같은 이점을 제공 합니다.

* 비용 관리
* 위험 관리
* 생산성 향상
* 준수 및 거 버 넌 스를 주소 지정

자세한 내용은 참조는 [Azure Active Directory에 응용 프로그램 마이그레이션](https://aka.ms/migrateapps/whitepaper) 백서입니다.

### <a name="enable-secure-remote-access-to-apps"></a>앱에 대 한 보안 원격 액세스를 사용 하도록 설정

[Azure AD Application Proxy](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-application-proxy) 내부 앱에 대 한 액세스를 안전 하 게에서 해야 하는 원격 사용자에 대 한 클라우드로 온-프레미스 앱을 게시 하는 조직 위한 간단한 솔루션을 제공 합니다. 한 번 로그인에 Azure AD에, 후 사용자는 외부 Url 또는 내부 응용 프로그램 포털을 통해 클라우드 및 온-프레미스 응용 프로그램을 액세스할 수 있습니다.

Azure AD 응용 프로그램 프록시는 다음과 같은 이점을 제공합니다.

* Azure AD를 온-프레미스 리소스로 확장
  * 클라우드 규모 보안 및 보호
  * 와 같은 조건부 액세스 및 Multi-factor Authentication을 사용 하도록 설정 하기 쉬운 기능
* VPN 및 기존의 역방향 프록시 솔루션 등의 경계 네트워크에 구성 요소 없음
* 필요한 인바운드 연결 없음
* single sign-on (SSO) 장치, 리소스 및 클라우드 및 온-프레미스에서 앱에서
* 최종 사용자 생산성을 높일 언제 든 지 어디서 나

### <a name="discover-shadow-it-with-microsoft-cloud-app-security"></a>Microsoft Cloud App Security로 섀도 IT 검색

오늘날 기업에서 IT 부서는 사용자가 해당 작업을 수행 하는 데 사용 되는 모든 클라우드 응용 프로그램 인식 하지 않은 경우가 많습니다. 얼마나 많은 클라우드 IT 관리자는 메시지가 직원이 생각 하는 앱 사용할 평균적으로 예를 들어 30 또는 40입니다. 실제로 평균은 1,000 개 별도 앱을 조직의 직원이 사용 하는 중입니다. 직원의 80%를 사용 하 여 비 사용 권한 앱을 아무도 검토에 보안 및 규정 준수 정책과 호환 되지 않을 수 있습니다.

[Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security) (MCAS) 도움이 사용자를 사용 하 여 인기 있는 유용한 앱을 식별 한다는 사용 권한을 부여 하 고 SSO 및 조건부 액세스와 같은 기능에서 혜택을 받을 사용자 있도록 엔터프라이즈 응용 프로그램 갤러리를 추가할 수 있습니다.

*"* * Cloud App Security** 사용자 올바르게 사용 하는 클라우드 및 SaaS 응용 프로그램에는 Accenture를 보호 하는 기본 보안 정책을 지 원하는 방식 확인 도움이 됩니다." *- [John Blasi, Accenture Director, 정보 보안 관리](https://customers.microsoft.com/story/accenture-professional-services-cloud-app-security)

MCAS 섀도 IT를 검색 하는 것 외에도 위험 수준은 확인할 수도 있습니다는 앱에서 회사 데이터, 가능한 데이터 유출 및 응용 프로그램의 기타 보안 위험과 무단된 액세스를 방지 합니다.

## <a name="step-2---establish-one-identity-for-every-user-automatically"></a>2 단계-모든 사용자에 대 한 하나의 id를 자동으로 설정

Azure AD 하이브리드 id 솔루션에서 온-프레미스 및 클라우드 기반 디렉터리를 함께 가져오는 하면 클라우드에서 기존 id를 프로 비전 하 여 기존 온-프레미스 Active Directory 투자를 재사용할 수 있습니다. 솔루션 동기화 온-프레미스 id IT 유지 하는 동안 Azure AD와 온-프레미스 Active Directory id에 대 한 정보의 기본 원본으로 기존 거 버 넌 스 솔루션을 사용 하 여 실행 합니다. Microsoft의 Azure AD 하이브리드 id 솔루션에 걸쳐 온-프레미스 및 클라우드 기반 기능을 인증 및 위치에 관계 없이 모든 리소스에 대 한 권한 부여에 대 한 일반적인 사용자 id를 만듭니다.

Azure AD와 온-프레미스 디렉터리 통합 사용자 생산성을 높일 수 있습니다 및 못하도록 사용자 모두 cloud에 액세스 하기 위한 공통 id를 제공 하 여 앱 및 서비스에서 여러 계정을 사용 하 고 온-프레미스 리소스입니다. 여러 계정을 사용 하는 최종 사용자에 대 한 불만 지점 및 IT 유사 합니다. 최종 사용자 관점에서 있으면 여러 계정 암호를 여러 개 기억할 필요가 있습니다. 이 방지 하려면 많은 사용자가 보안의 관점에서 잘못 되었습니다. 각 계정에 대해 동일한 암호를 다시 사용 합니다. IT 관점에서 다시 사용 하면 종종 자세한 암호 재설정 및 최종 사용자 불만이 함께 헬프데스크 비용입니다.

Azure AD Connect는 클라우드 응용 프로그램에 액세스 한 다음 사용할 수 있는 Azure AD에 온-프레미스 id 동기화에 사용 되는 도구입니다. Azure AD에서 id 되 면 Concur 또는 Salesforce와 같은 SaaS 응용 프로그램에 프로 비전 할 수 있습니다.

이 섹션에서는 고가용성, 클라우드에 대 한 최신 인증을 제공 하 고 온-프레미스에 줄이는 대 한 권장 사항을 나열 합니다.

> [!NOTE]
> Azure AD Connect에 자세히 알아보려면 원한다 면 참조 [Azure AD Connect 동기화 란?](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-whatis)

### <a name="set-up-a-staging-server-for-azure-ad-connect-and-keep-it-up-to-date"></a>Azure AD Connect에 대 한 스테이징 서버를 설정 하 고 최신 상태로 유지

Azure AD Connect 프로 비전 프로세스에서 키 역할을 합니다. 어떤 이유로 든 동기화 서버가 오프 라인 상태로 전환, 변경 내용을 온-프레미스로 클라우드에서 업데이트 되지 않습니다 및 사용자에 게 액세스 문제를 일으킬. 관리자가 신속 하 게 동기화 서버가 오프 라인이 되 면 동기화를 다시 시작할 수 있도록 장애 조치 전략을 정의 하는 것이 반드시 합니다.

고가용성을 제공할 이벤트의 기본 Azure AD Connect 서버가 오프 라인 상태로 전환한, 별도 배포 하는 것이 좋습니다 [스테이징 서버](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-staging-server) Azure AD Connect에 대 한 합니다. 서버 배포 관리자가 프로덕션 준비 서버를 간단한 구성 스위치를 하 여 "승격" 수 있습니다. 대기 서버를 준비 모드로 구성 된 것 할 수도 있습니다 및 테스트 하 고, 새 구성 변경 내용을 배포 하 고, 기존 서비스를 해제 하는 경우 새 서버를 도입 합니다.

> [!TIP]
> Azure AD Connect은 정기적으로 업데이트 됩니다. 따라서 유지 하는 스테이징 서버 성능 향상, 버그 수정 및 새 버전 마다 제공 하는 새 기능을 활용 하기 위해 현재 것이 좋습니다.

### <a name="enable-cloud-authentication"></a>클라우드 인증을 사용 하도록 설정

온-프레미스 Active Directory를 사용 하 여 조직 디렉터리의 Azure AD Connect를 사용 하 여 Azure AD로 확장 하 고 적절 한 인증 방법을 구성 해야 합니다. [올바른 인증 방법을 선택](https://docs.microsoft.com/azure/security/azure-ad-choose-authn) 조직의 클라우드 앱을 이동 하는 과정의 첫 번째 단계에 대 한 합니다. 구성 요소인 중요 한 모든 클라우드 데이터 및 리소스에 대 한 액세스를 제어 하기 때문입니다.

Azure AD에서 온-프레미스 디렉터리 개체에 대 한 클라우드 인증을 사용 하도록 설정 하는 것에 대 한 간단 하 고 권장 되는 메서드를 사용 하도록 설정 하는 것 [암호 해시 동기화](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-password-hash-synchronization) (PHS). 또는 사용 하도록 설정 하면 일부 조직에서는 고려할 수 [통과 인증](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-pta-quick-start) (PTA).

PHS 또는 PTA을 선택 하면 여부를 사용 하도록 설정 하려면 잊지 [Seamless Single Sign on](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sso) 지속적으로 입력 하지 않고 자신의 사용자 이름과 암호를 앱에서 회사에서 Windows 7 및 8 장치를 사용 하는 경우 클라우드 앱에 액세스할 수 있도록 하려면 네트워크입니다. Single sign-on 없이 사용자가 응용 프로그램별 암호 및 로그인 각 응용 프로그램에 기억해 야 합니다. 마찬가지로, IT 직원에 게 Office 365, Box 및 Salesforce와 같은 각 응용 프로그램에 대 한 사용자 계정을 만들고 업데이트 해야 합니다. 사용자가 자신의 암호를 기억해 및 각 응용 프로그램에 로그인 하는 시간을 투자 해야 합니다. 메커니즘을 제공 하는 표준화 된 단일 로그온 전체 기업 하는 것은 최상의 사용자 환경을, 위험, 보고를 하는 기능 및 거 버 넌 스의 감소 중요 합니다.

이미 AD FS 또는 다른 온-프레미스 인증 공급자를 사용 하 여 조직에 대 한 id 공급자로 Azure AD로 이동 수 복잡성을 낮추고 가용성을 향상 시킵니다. 페더레이션을 사용 하 여에 대 한 특정 사용 사례를 설정한 경우가 아니면로 마이그레이션 페더레이션된 인증에서 중 PHS 및 Seamless SSO 또는 PTA 및 Seamless SSO 사용 공간을 축소 된 온-프레미스 및 클라우드를 사용 하 여는 유연성의 이점을 활용 하는 것이 좋습니다. 향상 된 사용자 경험 합니다. 자세한 내용은 [페더레이션에서 Azure Active Directory에 대 한 암호 해시 동기화로 마이그레이션](https://docs.microsoft.com/azure/active-directory/hybrid/plan-migrate-adfs-password-hash-sync)합니다.

### <a name="enable-automatic-deprovisioning-of-accounts"></a>자동 계정 프로 비전 해제 사용

자동화 된 프로 비전 및 응용 프로그램에 프로 비전 해제를 사용 하도록 설정 하는 것은 여러 시스템에서 id 수명 주기 관리에 대 한 가장 좋은 전략입니다. Azure AD에서 지 원하는 [자동화 된, 정책 기반 프로비저닝 및 프로비저닝 해제](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-automatic-user-provisioning-portal) 다양 한 인기 있는 SaaS 응용 프로그램 ServiceNow 및 Salesforce 등과 같은 구현 하는 사용자 계정의 [SCIM 2.0 프로토콜](https://docs.microsoft.com/azure/active-directory/manage-apps/use-scim-to-provision-users-and-groups)합니다. 사용자 지정 코드 또는 CSV 파일의 수동 업로드 필요로 기존 프로 비전 솔루션과 달리 프로 비전 서비스는 클라우드에서 호스트 되 및 사전 통합 된 커넥터를 설정할 수 있습니다 및 Azure portal을 사용 하 여 관리를 제공 합니다. 자동 프로 비전 해제 하는 주요 이점은 점입니다 즉시 조직을 떠날 때 주요 SaaS 앱에서 사용자의 id를 제거 하 여 조직을 보호 합니다.

자동 사용자 계정 프로비전 및 작동 방식에 대한 자세한 내용은 [Azure Active Directory를 사용하여 SaaS 애플리케이션에 사용자 프로비전 및 프로비전 해제 자동화](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)를 참조하세요.

## <a name="step-3---empower-your-users-securely"></a>3 단계-사용자를 안전 하 게 지원

가 오늘 디지털 작업 영역에서 생산성을 사용 하 여 보안 분산 해야 합니다. 그러나 최종 사용자가 자주 다시 푸시하 클라우드 앱에 대 한 액세스와 생산성을 저하 하는 보안 조치입니다. 이 문제를 해결 하려면 Azure AD는 관리 오버 헤드를 최소화 하면서 생산성을 유지 하는 데 사용할 수 있는 셀프 서비스 기능을 제공 합니다.

이 섹션에는 유의 하면서 사용자에 게 제공 하 여 조직에서 마찰을 제거 하기 위한 권장 사항을 나열 합니다.

### <a name="enable-self-service-password-reset-for-all-users"></a>모든 사용자에 대 한 셀프 서비스 암호 재설정을 사용 하도록 설정

Azure의 [셀프 서비스 암호 재설정](https://docs.microsoft.com/azure/active-directory/authentication/quickstart-sspr) (SSPR) 사용자가 다시 설정 하 고 해당 암호 또는 관리자의 개입 없이 계정 잠금을 해제할 수 있도록 IT 관리자는 간단한 방법을 제공 합니다. 이 시스템에는 오용 또는 남용에 대해 경고하는 알림과 함께 사용자가 언제 시스템에 액세스하는지 추적하는 구체적인 보고서가 포함되어 있습니다.

기본적으로 Azure AD는 암호 재설정을 수행할 때 계정을 잠금 해제합니다. 그러나 사용 하도록 설정한 경우 Azure AD Connect [통합 온-프레미스](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-howitworks#on-premises-integration), 사용자가 암호를 재설정 하지 않고 계정의 잠금을 해제할 수 있게 하는 이러한 두 작업을 분리할 수 있습니다.

### <a name="ensure-all-users-are-registered-for-mfa-and-sspr"></a>모든 사용자에 게 MFA 및 SSPR에 등록 되었는지 확인 합니다.

Azure는 MFA 및 SSPR에 대 한 사용자가 등록 되어 있는지 확인 하 고 조직에서 사용할 수 있는 보고서를 제공 합니다. 사용자가 등록 하지 않은 프로세스에 사용자를 교육 해야 합니다.

MFA [로그인 보고서](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-reporting) MFA 사용에 대 한 정보를 포함 하 고 조직에 MFA가 작동 하는 방법에 대 한 정보를 제공 합니다. 로그인에 액세스할 수 활동 (감사 및 위험 이벤트 포함)를 Azure AD 문제 해결, 사용 현황 분석 및 법정 조사에 매우 중요 합니다.

마찬가지로 합니다 [셀프 서비스 암호 관리 보고서](https://docs.microsoft.com/azure/active-directory/authentication/howto-sspr-reporting) 권한이 있는 사용자를 확인 하기 위해 사용할 수 있습니다 (또는 되지 않은) SSPR에 등록 합니다.

### <a name="self-service-app-management"></a>셀프 서비스 앱 관리

사용 하도록 설정 해야 사용자가 액세스 패널에서 응용 프로그램을 셀프 검색할 수, 전에 [셀프 서비스 응용 프로그램 액세스](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-manage-self-service-access) 사용자가 셀프 검색 하 고 요청 하도록 허용 하려는 모든 응용 프로그램에 액세스 합니다. 셀프 서비스 응용 프로그램 액세스에는 자체 응용 프로그램을 검색 하 고 필요에 따라 비즈니스 그룹이 해당 응용 프로그램에 대 한 액세스를 승인 하도록 허용 하는 사용자를 허용 하는 좋은 방법입니다. 비즈니스 그룹이 해당 사용자에 게 할당 하는 자격 증명을 관리 하도록 할 수 있습니다 [응용 프로그램에서 Single Sign 암호](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-problem-password-sso-gallery#configure-password-sso-for-an-azure-ad-gallery-app) 자신의 액세스 패널에서 바로.

### <a name="self-service-group-management"></a>셀프 서비스 그룹 관리

응용 프로그램에 사용자 지정 가장 잘 매핑됩니다. 그룹을 사용 하 여 유연 하 게 대규모로 관리할 수 있도록 하므로:

* 특성 기반 동적 그룹 멤버 자격을 사용 하 여
* 앱 소유자에 게 위임

Azure AD는 보안 그룹 및 Office 365 그룹을 사용하여 리소스에 대한 액세스를 관리하는 기능을 제공합니다. 승인 또는 멤버 자격 요청을 거부 하 고 그룹 구성원의 제어 권한을 위임할 수 있는 그룹 소유자가 이러한 그룹을 관리할 수 있습니다. 이라고 [셀프 서비스 그룹 관리](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-self-service-management),이 기능 그룹 소유자를 해당 요청을 처리 하는 관리자에 의존 하지 않고도 그룹을 만들고 관리 역할을 할당 하지 함으로써 시간을 저장 합니다.

## <a name="step-4---operationalize-your-insights"></a>4 단계-통찰력을 운영 화

감사 및 보안 관련 이벤트 및 관련 된 경고에 대해 로깅을 효율적인 사용자 생산성을 유지 하 고 조직의 보안 전략의 필수 구성 요소는입니다. 보안 로그 및 보고서 도움이 질문 응답 같은:

* 에 대 한 지불 하는 사용 중 입니까?
* 아무 것도 내 테 넌 트에서 발생 악성 또는 의심 스러운 있나요?
* 사용자가 보안 문제가 발생할 때 영향을 받나요?

보안 로그 및 보고서는 의심스러운 활동에 대한 전자 기록을 제공하며, 네트워크의 외부 침투와 내부 공격의 시도 또는 성공을 나타낼 수 있는 패턴을 검색할 수 있도록 도움을 줍니다. 사용자 활동을 문서 규정 준수를 모니터링, 법정 분석 등을 수행 하려면 감사를 사용할 수 있습니다. 경고는 보안 이벤트의 알림을 제공합니다.

### <a name="assign-least-privileged-admin-roles-for-operations"></a>작업에 대 한 최소 권한이 가진된 관리자 역할 할당

작업에 접근 하는 방법에 대 한 생각을 고려해 야 할 관리의 두 가지 수준이 있습니다. 첫 번째 수준에서 전역 관리자의 관리 부담을 배치합니다. 전역 관리자 역할을 항상 사용 하는 것은 소규모 기업의 적절할 수 있습니다. 지원 센터 담당자 및 특정 작업을 담당 하는 관리자를 사용 하 여 대규모 조직의 경우 전역 관리자의 역할 할당을 보안상 위험할 수 있습니다 이러한 개인 맺음말 수 있는 작업을 관리 하는 기능을 사용 하 여 제공 하므로 하지만 어떤 것 수행할 수 있는 합니다.

이 경우에 다음 수준의 관리를 고려해 야 합니다. Azure AD를 사용 하 여, 최종 사용자에 게 권한이 적은 역할의 작업을 관리할 수 있는 "제한 된 관리자"를 지정할 수 있습니다. 예를 들어, 할당할 수 있습니다 프로그램 도움말 지원 센터 담당자는 [보안 읽기 권한자](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#security-reader) 역할을 읽기 전용 액세스를 사용 하 여 보안 관련 기능을 관리 하는 기능을 제공 합니다. 아마도 편이 할당할 또는 합니다 [인증 관리자](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#authentication-administrator) 비 암호 자격 증명 다시 설정 또는 읽기 및 Azure Service Health를 구성 하는 기능을 부여할 수 있는 개인에 게는 역할입니다.

자세한 내용은 참조 하세요 [Azure Active Directory에서 관리자 역할 권한](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)합니다.

### <a name="monitor-hybrid-components-azure-ad-connect-sync-ad-fs-using-azure-ad-connect-health"></a>하이브리드 구성 요소 (Azure AD Connect 동기화에 AD FS)를 모니터링할 Azure AD Connect Health 사용

Azure AD Connect 및 AD FS는 잠재적으로 수명 주기 관리 및 인증을 중단 하 고 궁극적으로 중단을 초래할 수 있는 중요 한 구성 요소입니다. 따라서 모니터링 및 보고 구성 요소에 대 한 Azure AD Connect Health를 배포 해야 합니다.

자세한 내용은 읽기 이동 [Azure AD Connect Health를 사용 하 여 모니터 AD FS](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-health-adfs)합니다.

### <a name="use-azure-monitor-to-collect-data-logs-for-analytics"></a>Azure Monitor를 사용 하 여 분석을 위해 데이터 로그를 수집 하려면

[Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview) 은 고급 분석 및 스마트 기계 학습, 깊이 있는 통찰력을 제공 하는 모든 Azure AD 로그에 대 한 통합된 모니터링 포털입니다. Azure Monitor를 사용 하 여 메트릭 및 로그 포털 내에서 및 자세한 리소스의 성능과 상태를 파악 하는 Api를 통해 사용할 수 있습니다. 다양 한 범위의 기존 타사 SIEM 시스템을 지 원하는 Api 및 데이터 내보내기 옵션을 통해 제품 통합을 사용 하도록 설정 하는 동안 포털 내에서 유리 환경 위한 단일 창이 있습니다. 또한 azure Monitor는 알림 받기 또는 리소스에 영향을 주는 문제에 자동화 된 작업을 수행 하는 경고 규칙을 구성 하는 기능을 제공 합니다.

![Azure Monitor](./media/four-steps/image1.png)

### <a name="create-custom-dashboards-for-your-leadership-and-your-day-to-day"></a>일을 사용 하 여 일상의 리더십에 사용자 지정 대시보드 만들기

SIEM 솔루션 없는 조직은 다운로드할 수는 [Power BI 콘텐츠 팩](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-power-bi-content-pack) Azure AD에 대 한 합니다. Power BI 콘텐츠 팩은 포함 하는 데 미리 작성된 된 보고서에는 사용자 채택 하 고 디렉터리 내의 모든 활동에 대 한 통찰력을 얻을 수 있는 Azure AD 기능을 사용 하는 방법을 이해 합니다. 만들 수도 있습니다 고유한 [사용자 지정 대시보드](https://docs.microsoft.com/power-bi/service-dashboards) 일상 작업에 대해 보고 하려면 리더십 팀과 공유 합니다. 대시보드는 비즈니스를 모니터링 하 고 가장 중요 한 메트릭을 한 눈에 모든 참조는 훌륭한 방법입니다. 대시보드에서 시각화 및 기본 보고서를 하나 또는 여러 개의 기본 데이터 집합 또는 여러 열을에서 가져올 수 있습니다. 대시보드 결합 온-프레미스와 클라우드 데이터, 데이터의 현재 위치에 관계 없이 통합 된 보기를 제공 합니다.

![Power BI 사용자 지정 대시보드](./media/four-steps/image2.png)

### <a name="understand-your-support-call-drivers"></a>지원 호출 드라이버를 이해 합니다.

이 문서에 설명 된 대로 하이브리드 id 솔루션을 구현 하는 경우 지원 통화 중에 감소 궁극적으로 유의 해야 합니다. 일반적인 문제 잊어버린된 암호 및 계정 잠금 Azure의 셀프 서비스 암호 재설정을 구현 하 여 완화 됩니다와 같은 셀프 서비스 응용 프로그램 액세스를 사용 하도록 설정 하는 동안 사용 하면 자체 검색 하 고 사용 하지 않고 응용 프로그램에 대 한 액세스를 요청 IT 직원에 게에서.

지원 요청 감소를 관찰 하지 않습니다, 경우 것이 좋습니다 경우 SSPR 또는 셀프 서비스 응용 프로그램 액세스가 올바르게 구성 되었는지 확인 하기 위해 지원 호출 드라이버를 분석 하는 체계적으로 될 수 있는 다른 새 문제가 있는 경우 또는 해결 합니다.

*"이 디지털 변환 과정에서 했던는 신뢰할 수 있는 id 및 액세스 관리 공급자, 파트너 및 효과적인 에코 시스템이 없거나;에 대 한 클라우드 서비스 공급자 간의 원활한 이면 서도 안전한 통합을 용이 하 게 Azure AD가가 필요한 기능 및 감지 하 고 위험에 대응 하는 표시 우리 제품 가장 적합 한 옵션입니다. "* --- [Yazan Almasri, 글로벌 정보 보안 책임자 Aramex](https://customers.microsoft.com/story/aramex-azure-active-directory-travel-transportation-united-arab-emirates-en)

### <a name="monitor-your-usage-of-apps-to-drive-insights"></a>통찰력을 끌어내는 앱의 사용 현황을 모니터링 합니다.

섀도 IT를 사용 하 여 조직에서 앱 사용 모니터링을 검색 하는 것 외에도 [Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security) 클라우드 응용 프로그램에 대 한 약속을 완전히 활용 하려면 이동 하면 조직을 보호할 수 있는 합니다. 활동에 대 한 향상 된 가시성을 통해 자산 제어를 유지 하 고 클라우드 응용 프로그램에서 중요 한 데이터의 보호를 강화 것이 도움이 됩니다. MCAS를 사용 하 여 조직에서 앱 사용 모니터링 다음 질문에 답할 수 있습니다.

* 비 사용 권한 앱 직원 사용 하는 데이터를 저장 하 시겠습니까?
* 위치 및 시기는 중요 한 데이터가 저장 되는 클라우드에서?
* 클라우드에서 중요 한 데이터에 액세스 하는?

*"Cloud App Security를 사용 하 여에서는 신속 하 게 잘못 된 부분을 파악 조치를 취할 수 있습니다."* --- [Eric LePenske 선임 관리자, 정보 보안 Accenture](https://customers.microsoft.com/story/accenture-professional-services-cloud-app-security)

## <a name="summary"></a>요약

하이브리드 Id 솔루션을 구현 하는 데에 많은 측면이 있지만이 4 단계의 검사 목록은 생산성과 보안 할 수 있도록 하는 id 인프라를 신속 하 게 수행 하는 데 도움이 됩니다.

* 앱에 쉽게 연결
* 모든 사용자에 대 한 하나의 id를 자동으로 설정
* 안전 하 게 사용자 환경 향상
* 통찰력을 운영 화

이 문서는 조직에 대 한 강력한 id 기반을 설정 하기 위한 유용한 로드맵 하시기 바랍니다.

## <a name="identity-checklist"></a>Identity 검사 목록

조직에서 경험을 더 견고한 identity foundation을 시작할 때 참조에 대 한 다음 검사 목록을 인쇄 하는 것이 좋습니다.

### <a name="today"></a>오늘

|등록했나요?|항목|
|:-|:-|
||파일럿 자체-서비스 암호 재설정 (SSPR)을 그룹에 대 한|
||Azure AD Connect Health를 사용 하 여 하이브리드 구성 요소 모니터링|
||작업에 대 한 최소 권한이 가진된 관리자 역할 할당|
||Microsoft Cloud App Security로 섀도 IT 검색|
||Azure Monitor를 사용 하 여 분석을 위해 데이터 로그를 수집 하려면|

### <a name="next-two-weeks"></a>앞으로 2 주

|등록했나요?|항목|
|:-|:-|
||사용자에 게는 앱 사용 가능|
||선택한 SaaS 앱에 대 한 Azure AD 프로 비전을 파일럿 실행|
||Azure AD Connect에 대 한 준비 서버를 설치 하 고 최신 상태로 유지|
||ADFS에서 Azure AD에 앱을 마이그레이션 시작|
||일을 사용 하 여 일상의 리더십에 사용자 지정 대시보드 만들기|

### <a name="next-month"></a>다음 달

|등록했나요?|항목|
|:-|:-|
||통찰력을 끌어내는 앱의 사용 현황을 모니터링 합니다.|
||앱에 안전한 원격 액세스를 파일럿|
||모든 사용자에 게 MFA 및 SSPR에 등록 되었는지 확인 합니다.|
||클라우드 인증을 사용 하도록 설정|

### <a name="next-three-months"></a>다음 3 개월

|등록했나요?|항목|
|:-|:-|
||셀프 서비스 앱 관리를 사용 하도록 설정|
||셀프 서비스 그룹 관리를 사용 하도록 설정|
||통찰력을 끌어내는 앱의 사용 현황을 모니터링 합니다.|
||지원 호출 드라이버를 이해 합니다.|

## <a name="next-steps"></a>다음 단계

기능을 사용 하 여 보안 태세를 강화 하는 방법에 대해 알아봅니다. Azure Active Directory 및이 다섯 단계의 검사 목록 [보안 id 인프라에 다섯 단계](https://aka.ms/securitysteps)합니다.

도움이 되는지를 알아봅니다 Azure AD에서 id 기능 수 조직에서 신속 하 게 채택 하 고 해당 id 관리의 더 전통적인에서 이동할 수 있도록 기능과 솔루션을 제공 하 여 클라우드 관리 관리로의 전환을 가속화 하세요. 온-프레미스 시스템을 Azure AD [Azure AD 제공 클라우드 관리 관리 온-프레미스 워크 로드에 대 한](https://aka.ms/cloudgoverned)합니다.
